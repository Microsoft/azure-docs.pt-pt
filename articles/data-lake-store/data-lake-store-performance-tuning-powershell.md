---
title: Afinação de desempenho da Gen1 de armazenamento de lagos Azure Data - PowerShell
description: Dicas sobre como melhorar o desempenho ao utilizar o Azure PowerShell com o Azure Data Lake Storage Gen1.
author: stewu
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: stewu
ms.openlocfilehash: c975af1799d427651b76bb9fde5ff765afed3f86
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73904566"
---
# <a name="performance-tuning-guidance-for-using-powershell-with-azure-data-lake-storage-gen1"></a>Orientação de afinação de desempenho para a utilização da PowerShell com o Azure Data Lake Storage Gen1

Este artigo descreve as propriedades que pode sintonizar para obter um melhor desempenho enquanto utiliza a PowerShell para trabalhar com data Lake Storage Gen1.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="performance-related-properties"></a>Propriedades relacionadas com o desempenho

| Propriedade            | Predefinição | Descrição |
|---------------------|---------|-------------|
| PerFileThreadCount  | 10      | Este parâmetro permite-lhe escolher o número de threads paralelos para carregar ou transferir cada ficheiro. Este número representa os fios máximos que podem ser atribuídos por ficheiro, mas pode obter menos fios dependendo do seu cenário (por exemplo, se estiver a carregar um ficheiro de 1 KB, obtém um fio mesmo que peça 20 fios).  |
| ConcurrentFileCount | 10      | Este parâmetro destina-se especificamente ao carregamento ou transferência de pastas. Este parâmetro determina o número de ficheiros simultâneos que podem ser carregados ou transferidos. Este número representa o número máximo de ficheiros simultâneos que podem ser carregados ou descarregados de uma só vez, mas pode obter menos condivisões dependendo do seu cenário (por exemplo, se estiver a carregar dois ficheiros, obtém dois uploads de ficheiros simultâneos mesmo que pergunte para 15). |

**Exemplo:**

Este comando descarrega ficheiros do Data Lake Storage Gen1 para a unidade local do utilizador utilizando 20 fios por ficheiro e 100 ficheiros simultâneos.

```PowerShell
Export-AzDataLakeStoreItem -AccountName "Data Lake Storage Gen1 account name" `
    -PerFileThreadCount 20 `
    -ConcurrentFileCount 100 `
    -Path /Powershell/100GB `
    -Destination C:\Performance\ `
    -Force `
    -Recurse
```

## <a name="how-to-determine-property-values"></a>Como determinar os valores da propriedade

A próxima questão que você pode ter é como determinar qual o valor a fornecer para as propriedades relacionadas com o desempenho. Eis algumas orientações que poderá utilizar.

* **Passo 1: Determinar a contagem total de fios** - Comece calculando a contagem total de fios a utilizar. Como orientação geral, deve utilizar seis fios para cada núcleo físico.

    `Total thread count = total physical cores * 6`

    **Exemplo:**

    Partindo do princípio de que está a executar os comandos do PowerShell a partir de uma VM D14 que tem 16 núcleos

    `Total thread count = 16 cores * 6 = 96 threads`

* **Passo 2: Calcular PerFileThreadCount** - Calculamos o nosso PerFileThreadCount com base no tamanho dos ficheiros. Para ficheiros inferiores a 2,5 GB, não há necessidade de alterar este parâmetro porque o padrão de 10 é suficiente. Para ficheiros superiores a 2,5 GB, deverá utilizar 10 fios como base para os primeiros 2,5 GB e adicionar 1 fio para cada aumento adicional de 256 MB no tamanho do ficheiro. Se estiver a copiar uma pasta com uma grande variedade de tamanhos de ficheiros, considere agrupá-los em tamanhos de ficheiro semelhantes. Ter tamanhos de ficheiro diferentes pode causar um desempenho não ideal. Se não for possível agrupar tamanhos de ficheiros semelhantes, deve definir PerFileThreadCount com base no tamanho de ficheiro maior.

    `PerFileThreadCount = 10 threads for the first 2.5 GB + 1 thread for each additional 256 MB increase in file size`

    **Exemplo:**

    Assumindo que tem 100 ficheiros que variam entre 1 GB e 10 GB, usamos os 10 GB como o maior tamanho de ficheiro para equação, que seria lido como o seguinte.

    `PerFileThreadCount = 10 + ((10 GB - 2.5 GB) / 256 MB) = 40 threads`

* Passo 3: Calcular a contagem de **ficheiros concurrent** - Utilize a contagem total de fios e o Conde PerFileThread para calcular o ConcurrentFileCount com base na seguinte equação:

    `Total thread count = PerFileThreadCount * ConcurrentFileCount`

    **Exemplo:**

    Com base nos valores de exemplo que temos estado a utilizar

    `96 = 40 * ConcurrentFileCount`

    Por isso, **ConcurrentFileCount** é **2,4**, que podemos arredondar para **2**.

## <a name="further-tuning"></a>Otimização adicional

Poderá necessitar de otimização adicional porque existe uma grande variedade de tamanhos de ficheiro com os quais trabalhar. O cálculo anterior funciona bem se todos ou a maioria dos ficheiros forem maiores e mais próximos da gama de 10 GB. Se, em vez disso, existirem vários tamanhos de ficheiro diferentes com muitos ficheiros mais pequenos, poderia reduzir a PerFileThreadCount. Ao reduzir a PerFileThreadCount, podemos aumentar a ConcurrentFileCount. Então, se assumirmos que a maioria dos nossos ficheiros são menores na gama de 5 GB, podemos refazer o nosso cálculo:

`PerFileThreadCount = 10 + ((5 GB - 2.5 GB) / 256 MB) = 20`

Assim, **o ConcurrentFileCount** torna-se 96/20, que é 4.8, arredondado para **4**.

Pode continuar a otimizar estas definições, alterando a **PerFileThreadCount** para cima e para baixo consoante a distribuição dos tamanhos de ficheiro.

### <a name="limitation"></a>Limitação

* **O número de ficheiros é menor que ConcurrentFileCount**: se o número de ficheiros que está a carregar for menor que a **ConcurrentFileCount** que calculou, deve reduzir a **ConcurrentFileCount** para ser igual ao número de ficheiros. Pode utilizar quaisquer threads restantes para aumentar a **PerFileThreadCount**.

* **Existem demasiados threads**: se aumentar demasiado a contagem de threads sem aumentar o tamanho do cluster, corre o risco de degradar o desempenho. Podem existir problemas de contenção ao alternar de contexto na CPU.

* **Simultaneidade insuficiente**: se a simultaneidade não for suficiente, significa que o cluster pode ser demasiado pequeno. Pode aumentar o número de nós no seu cluster, o que lhe dá mais condivisição.

* **Erros de limitação**: poderá ver erros de limitação se a simultaneidade for demasiado elevada. Se vir erros de limitação, deverá reduzir a simultaneidade ou contactar a Microsoft.

## <a name="next-steps"></a>Passos seguintes

* [Use o Azure Data Lake Storage Gen1 para grandes necessidades de dados](data-lake-store-data-scenarios.md) 
* [Proteger dados no Armazenamento do Data Lake Ger1](data-lake-store-secure-data.md)
* [Use Azure Data Lake Analytics com Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Use Azure HDInsight com Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)

