---
title: Ajuste de desempenho Azure Data Lake Storage Gen1-PowerShell
description: Dicas sobre como melhorar o desempenho ao usar Azure PowerShell com Azure Data Lake Storage Gen1.
author: stewu
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: stewu
ms.openlocfilehash: c975af1799d427651b76bb9fde5ff765afed3f86
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904566"
---
# <a name="performance-tuning-guidance-for-using-powershell-with-azure-data-lake-storage-gen1"></a>Diretrizes de ajuste de desempenho para usar o PowerShell com Azure Data Lake Storage Gen1

Este artigo descreve as propriedades que você pode ajustar para obter melhor desempenho ao usar o PowerShell para trabalhar com Data Lake Storage Gen1.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="performance-related-properties"></a>Propriedades relacionadas ao desempenho

| Propriedade            | Predefinição | Descrição |
|---------------------|---------|-------------|
| PerFileThreadCount  | 10      | Este parâmetro permite-lhe escolher o número de threads paralelos para carregar ou transferir cada ficheiro. Esse número representa o máximo de threads que podem ser alocados por arquivo, mas você pode obter menos threads dependendo do seu cenário (por exemplo, se você estiver carregando um arquivo de 1 KB, você obterá um thread mesmo se solicitar 20 threads).  |
| ConcurrentFileCount | 10      | Este parâmetro destina-se especificamente ao carregamento ou transferência de pastas. Este parâmetro determina o número de ficheiros simultâneos que podem ser carregados ou transferidos. Esse número representa o número máximo de arquivos simultâneos que podem ser carregados ou baixados ao mesmo tempo, mas você pode obter menos simultaneidade dependendo do seu cenário (por exemplo, se você estiver carregando dois arquivos, obterá dois carregamentos de arquivos simultâneos mesmo se você perguntar para 15). |

**Exemplo:**

Esse comando baixa arquivos de Data Lake Storage Gen1 para a unidade local do usuário usando 20 threads por arquivo e 100 arquivos simultâneos.

```PowerShell
Export-AzDataLakeStoreItem -AccountName "Data Lake Storage Gen1 account name" `
    -PerFileThreadCount 20 `
    -ConcurrentFileCount 100 `
    -Path /Powershell/100GB `
    -Destination C:\Performance\ `
    -Force `
    -Recurse
```

## <a name="how-to-determine-property-values"></a>Como determinar valores de propriedade

A próxima pergunta que você pode ter é como determinar qual valor fornecer para as propriedades relacionadas ao desempenho. Eis algumas orientações que poderá utilizar.

* **Etapa 1: determinar a contagem total de threads** -início calculando a contagem total de threads a ser usada. Como uma diretriz geral, você deve usar seis threads para cada núcleo físico.

    `Total thread count = total physical cores * 6`

    **Exemplo:**

    Partindo do princípio de que está a executar os comandos do PowerShell a partir de uma VM D14 que tem 16 núcleos

    `Total thread count = 16 cores * 6 = 96 threads`

* **Etapa 2: calcular PerFileThreadCount** – calculamos nosso PerFileThreadCount com base no tamanho dos arquivos. Para arquivos menores que 2,5 GB, não é necessário alterar esse parâmetro porque o padrão de 10 é suficiente. Para arquivos com mais de 2,5 GB, você deve usar 10 threads como base para os primeiros 2,5 GB e adicionar 1 thread para cada aumento de 256 MB adicional no tamanho do arquivo. Se estiver a copiar uma pasta com uma grande variedade de tamanhos de ficheiros, considere agrupá-los em tamanhos de ficheiro semelhantes. Ter tamanhos de ficheiro diferentes pode causar um desempenho não ideal. Se não for possível agrupar tamanhos de ficheiros semelhantes, deve definir PerFileThreadCount com base no tamanho de ficheiro maior.

    `PerFileThreadCount = 10 threads for the first 2.5 GB + 1 thread for each additional 256 MB increase in file size`

    **Exemplo:**

    Supondo que você tenha 100 arquivos variando de 1 GB a 10 GB, usamos 10 GB como o maior tamanho de arquivo para a equação, que seria semelhante ao seguinte.

    `PerFileThreadCount = 10 + ((10 GB - 2.5 GB) / 256 MB) = 40 threads`

* **Etapa 3: calcular ConcurrentFilecount** -use a contagem total de threads e o PerFileThreadCount para calcular ConcurrentFilecount com base na seguinte equação:

    `Total thread count = PerFileThreadCount * ConcurrentFileCount`

    **Exemplo:**

    Com base nos valores de exemplo que temos estado a utilizar

    `96 = 40 * ConcurrentFileCount`

    Por isso, **ConcurrentFileCount** é **2,4**, que podemos arredondar para **2**.

## <a name="further-tuning"></a>Otimização adicional

Poderá necessitar de otimização adicional porque existe uma grande variedade de tamanhos de ficheiro com os quais trabalhar. O cálculo anterior funcionará bem se todos ou a maioria dos arquivos forem maiores e mais próximos do intervalo de 10 GB. Se, em vez disso, existirem vários tamanhos de ficheiro diferentes com muitos ficheiros mais pequenos, poderia reduzir a PerFileThreadCount. Ao reduzir a PerFileThreadCount, podemos aumentar a ConcurrentFileCount. Portanto, se presumirmos que a maioria de nossos arquivos seja menor no intervalo de 5 GB, podemos refazer nosso cálculo:

`PerFileThreadCount = 10 + ((5 GB - 2.5 GB) / 256 MB) = 20`

Portanto, **ConcurrentFileCount** se torna 96/20, que é 4,8, arredondado para **4**.

Pode continuar a otimizar estas definições, alterando a **PerFileThreadCount** para cima e para baixo consoante a distribuição dos tamanhos de ficheiro.

### <a name="limitation"></a>Limitação

* **O número de ficheiros é menor que ConcurrentFileCount**: se o número de ficheiros que está a carregar for menor que a **ConcurrentFileCount** que calculou, deve reduzir a **ConcurrentFileCount** para ser igual ao número de ficheiros. Pode utilizar quaisquer threads restantes para aumentar a **PerFileThreadCount**.

* **Existem demasiados threads**: se aumentar demasiado a contagem de threads sem aumentar o tamanho do cluster, corre o risco de degradar o desempenho. Podem existir problemas de contenção ao alternar de contexto na CPU.

* **Simultaneidade insuficiente**: se a simultaneidade não for suficiente, significa que o cluster pode ser demasiado pequeno. Você pode aumentar o número de nós no cluster, o que proporciona mais simultaneidade.

* **Erros de limitação**: poderá ver erros de limitação se a simultaneidade for demasiado elevada. Se vir erros de limitação, deverá reduzir a simultaneidade ou contactar a Microsoft.

## <a name="next-steps"></a>Passos seguintes

* [Usar Azure Data Lake Storage Gen1 para requisitos de Big Data](data-lake-store-data-scenarios.md) 
* [Proteger dados no Armazenamento do Data Lake Ger1](data-lake-store-secure-data.md)
* [Usar Azure Data Lake Analytics com Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Usar o Azure HDInsight com o Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)

