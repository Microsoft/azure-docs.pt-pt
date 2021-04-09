---
title: Azure Data Lake Storage Gen1 afinação de desempenho - PowerShell
description: Dicas sobre como melhorar o desempenho ao utilizar o Azure PowerShell com a Azure Data Lake Storage Gen1.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 01/09/2018
ms.author: twooley
ms.openlocfilehash: 4ac2bbb21fd1a987d544a536d0f52628824e0bf4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97723834"
---
# <a name="performance-tuning-guidance-for-using-powershell-with-azure-data-lake-storage-gen1"></a>Orientação de afinação de desempenho para usar PowerShell com Azure Data Lake Storage Gen1

Este artigo descreve as propriedades que você pode sintonizar para obter um melhor desempenho enquanto usa PowerShell para trabalhar com Data Lake Storage Gen1.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="performance-related-properties"></a>Propriedades relacionadas com o desempenho

| Propriedade            | Predefinição | Description |
|---------------------|---------|-------------|
| PerFileThreadCount  | 10      | Este parâmetro permite-lhe escolher o número de threads paralelos para carregar ou transferir cada ficheiro. Este número representa as linhas máximas que podem ser atribuídas por ficheiro, mas pode obter menos fios dependendo do seu cenário (por exemplo, se estiver a carregar um ficheiro de 1 KB, obtém-se um fio mesmo que peça 20 threads).  |
| ConcurrentFileCount | 10      | Este parâmetro destina-se especificamente ao carregamento ou transferência de pastas. Este parâmetro determina o número de ficheiros simultâneos que podem ser carregados ou transferidos. Este número representa o número máximo de ficheiros simultâneos que podem ser carregados ou descarregados de uma só vez, mas pode obter menos concordância dependendo do seu cenário (por exemplo, se estiver a carregar dois ficheiros, obtém dois ficheiros simultâneos mesmo que peça 15). |

**Exemplo:**

Este comando descarrega ficheiros da Data Lake Storage Gen1 para a unidade local do utilizador utilizando 20 fios por ficheiro e 100 ficheiros simultâneos.

```PowerShell
Export-AzDataLakeStoreItem -AccountName "Data Lake Storage Gen1 account name" `
    -PerFileThreadCount 20 `
    -ConcurrentFileCount 100 `
    -Path /Powershell/100GB `
    -Destination C:\Performance\ `
    -Force `
    -Recurse
```

## <a name="how-to-determine-property-values"></a>Como determinar os valores patrimoniais

A próxima pergunta que pode ter é como determinar que valor fornecer para as propriedades relacionadas com o desempenho. Eis algumas orientações que poderá utilizar.

* **Passo 1: Determine a contagem total de roscas** - Comece por calcular a contagem total de roscas a utilizar. Como orientação geral, deve utilizar seis fios para cada núcleo físico.

    `Total thread count = total physical cores * 6`

    **Exemplo:**

    Partindo do princípio de que está a executar os comandos do PowerShell a partir de uma VM D14 que tem 16 núcleos

    `Total thread count = 16 cores * 6 = 96 threads`

* **Passo 2: Calcular PerFileThreadCount** - Calculamos o nosso PerFileThreadCount com base no tamanho dos ficheiros. Para ficheiros inferiores a 2,5 GB, não há necessidade de alterar este parâmetro porque o padrão de 10 é suficiente. Para ficheiros maiores do que 2,5 GB, deve utilizar 10 fios como base para os primeiros 2,5 GB e adicionar 1 fio para cada aumento adicional de 256-MB no tamanho do ficheiro. Se estiver a copiar uma pasta com uma grande variedade de tamanhos de ficheiros, considere agrupá-los em tamanhos de ficheiro semelhantes. Ter tamanhos de ficheiro diferentes pode causar um desempenho não ideal. Se não for possível agrupar tamanhos de ficheiros semelhantes, deve definir PerFileThreadCount com base no tamanho de ficheiro maior.

    `PerFileThreadCount = 10 threads for the first 2.5 GB + 1 thread for each additional 256 MB increase in file size`

    **Exemplo:**

    Assumindo que tem 100 ficheiros que variam entre 1 GB e 10 GB, usamos o 10 GB como o maior tamanho de ficheiro para equação, que seria como se fosse o seguinte.

    `PerFileThreadCount = 10 + ((10 GB - 2.5 GB) / 256 MB) = 40 threads`

* **Passo 3: Calcular ConcurrentFilecount** - Use a contagem total de fios e PerFileThreadCount para calcular o ConcurrentFileCount com base na seguinte equação:

    `Total thread count = PerFileThreadCount * ConcurrentFileCount`

    **Exemplo:**

    Com base nos valores de exemplo que temos estado a utilizar

    `96 = 40 * ConcurrentFileCount`

    Por isso, **ConcurrentFileCount** é **2,4**, que podemos arredondar para **2**.

## <a name="further-tuning"></a>Otimização adicional

Poderá necessitar de otimização adicional porque existe uma grande variedade de tamanhos de ficheiro com os quais trabalhar. O cálculo anterior funciona bem se a totalidade ou a maioria dos ficheiros forem maiores e mais próximos da gama de 10 GB. Se, em vez disso, existirem vários tamanhos de ficheiro diferentes com muitos ficheiros mais pequenos, poderia reduzir a PerFileThreadCount. Ao reduzir a PerFileThreadCount, podemos aumentar a ConcurrentFileCount. Então, se assumirmos que a maioria dos nossos ficheiros são menores na gama de 5 GB, podemos refazer o nosso cálculo:

`PerFileThreadCount = 10 + ((5 GB - 2.5 GB) / 256 MB) = 20`

Assim, **o ConcurrentFileCount** torna-se 96/20, que é 4.8, arredondado para **4**.

Pode continuar a otimizar estas definições, alterando a **PerFileThreadCount** para cima e para baixo consoante a distribuição dos tamanhos de ficheiro.

### <a name="limitation"></a>Limitação

* **O número de ficheiros é menor que ConcurrentFileCount**: se o número de ficheiros que está a carregar for menor que a **ConcurrentFileCount** que calculou, deve reduzir a **ConcurrentFileCount** para ser igual ao número de ficheiros. Pode utilizar quaisquer threads restantes para aumentar a **PerFileThreadCount**.

* **Existem demasiados threads**: se aumentar demasiado a contagem de threads sem aumentar o tamanho do cluster, corre o risco de degradar o desempenho. Podem existir problemas de contenção ao alternar de contexto na CPU.

* **Simultaneidade insuficiente**: se a simultaneidade não for suficiente, significa que o cluster pode ser demasiado pequeno. Pode aumentar o número de nós no seu agrupamento, o que lhe dá mais concordância.

* **Erros de limitação**: poderá ver erros de limitação se a simultaneidade for demasiado elevada. Se vir erros de limitação, deverá reduzir a simultaneidade ou contactar a Microsoft.

## <a name="next-steps"></a>Passos seguintes

* [Use Azure Data Lake Storage Gen1 para grandes requisitos de dados](data-lake-store-data-scenarios.md) 
* [Proteger dados no Armazenamento do Data Lake Ger1](data-lake-store-secure-data.md)
* [Use Azure Data Lake Analytics com data lake storage gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Use Azure HDInsight com data lake storage gen1](data-lake-store-hdinsight-hadoop-use-portal.md)

