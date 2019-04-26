---
title: Guia para utilizar o Powershell com a geração 1 de armazenamento do Azure Data Lake de sintonização de desempenho | Documentos da Microsoft
description: Dicas sobre como melhorar o desempenho ao utilizar o Azure PowerShell com a geração 1 de armazenamento do Azure Data Lake
services: data-lake-store
documentationcenter: ''
author: stewu
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 01/09/2018
ms.author: stewu
ms.openlocfilehash: 1c554b0eee844a632e6412b6f8a285c7a2573326
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60195862"
---
# <a name="performance-tuning-guidance-for-using-powershell-with-azure-data-lake-storage-gen1"></a>Guia para utilizar o PowerShell com a geração 1 de armazenamento do Azure Data Lake de sintonização de desempenho

Este artigo lista as propriedades que podem ser otimizadas para obter um melhor desempenho ao utilizar o PowerShell para trabalhar com a geração 1 de armazenamento do Azure Data Lake:

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="performance-related-properties"></a>Propriedades relacionadas de desempenho

| Propriedade            | Predefinição | Descrição |
|---------------------|---------|-------------|
| PerFileThreadCount  | 10      | Este parâmetro permite-lhe escolher o número de threads paralelos para carregar ou transferir cada ficheiro. Este número representa o máximo de threads que pode ser alocado por ficheiro, mas poderá obter menos threads dependendo do seu cenário (por exemplo, se estiver a carregar um ficheiro de 1 KB, obterá um thread mesmo que solicite 20 threads).  |
| ConcurrentFileCount | 10      | Este parâmetro destina-se especificamente ao carregamento ou transferência de pastas. Este parâmetro determina o número de ficheiros simultâneos que podem ser carregados ou transferidos. Este número representa o número máximo de ficheiros simultâneos que podem ser carregados ou transferidos de uma só vez, mas poderá obter menos simultaneidade dependendo do seu cenário (por exemplo, se estiver a carregar dois ficheiros, obter dois carregamentos de ficheiros simultâneos, mesmo se perguntar para 15). |

**Exemplo**

Este comando transfere ficheiros de geração 1 de armazenamento do Data Lake para unidade local do usuário com 20 threads por ficheiro e 100 ficheiros simultâneos.

    Export-AzDataLakeStoreItem -AccountName <Data Lake Storage Gen1 account name> -PerFileThreadCount 20-ConcurrentFileCount 100 -Path /Powershell/100GB/ -Destination C:\Performance\ -Force -Recurse

## <a name="how-do-i-determine-the-value-for-these-properties"></a>Como posso determinar o valor para estas propriedades?

A próxima pergunta que pode ter é como determinar a que valor para fornecer para as propriedades relacionados ao desempenho. Eis algumas orientações que poderá utilizar.

* **Passo 1: Determinar a contagem total de threads** -deve começar por calcular a contagem total de threads a utilizar. Como orientação geral, deve usar seis threads para cada núcleo físico.

        Total thread count = total physical cores * 6

    **Exemplo**

    Partindo do princípio de que está a executar os comandos do PowerShell a partir de uma VM D14 que tem 16 núcleos

        Total thread count = 16 cores * 6 = 96 threads


* **Passo 2: Calcular a PerFileThreadCount** -Calculamos a nossa PerFileThreadCount com base no tamanho dos ficheiros. Para ficheiros inferior a 2,5 GB, não é necessário para alterar este parâmetro, uma vez que a predefinição de 10 é suficiente. Para ficheiros com mais de 2,5 GB, deve utilizar 10 threads como base para os primeiros 2,5 GB e adicionar 1 thread por cada aumento de 256 MB adicional no tamanho do ficheiro. Se estiver a copiar uma pasta com uma grande variedade de tamanhos de ficheiros, considere agrupá-los em tamanhos de ficheiro semelhantes. Ter tamanhos de ficheiro diferentes pode causar um desempenho não ideal. Se não for possível agrupar tamanhos de ficheiros semelhantes, deve definir PerFileThreadCount com base no tamanho de ficheiro maior.

        PerFileThreadCount = 10 threads for the first 2.5 GB + 1 thread for each additional 256 MB increase in file size

    **Exemplo**

    Partindo do princípio de que tem 100 ficheiros variam entre 1 GB e 10 GB, utilizamos os 10 GB como o maior tamanho de ficheiro para a equação, qual seria a seguinte formulação.

        PerFileThreadCount = 10 + ((10 GB - 2.5 GB) / 256 MB) = 40 threads

* **Passo 3: Calcular a ConcurrentFilecount** – utilize a contagem total de threads e a PerFileThreadCount para calcular a ConcurrentFileCount com base na seguinte equação:

        Total thread count = PerFileThreadCount * ConcurrentFileCount

    **Exemplo**

    Com base nos valores de exemplo que temos estado a utilizar

        96 = 40 * ConcurrentFileCount

    Por isso, **ConcurrentFileCount** é **2,4**, que podemos arredondar para **2**.

## <a name="further-tuning"></a>Otimização adicional

Poderá necessitar de otimização adicional porque existe uma grande variedade de tamanhos de ficheiro com os quais trabalhar. O cálculo anterior funciona bem se a todas ou a maioria dos ficheiros é maiores e mais de perto para o intervalo de 10 GB. Se, em vez disso, existirem vários tamanhos de ficheiro diferentes com muitos ficheiros mais pequenos, poderia reduzir a PerFileThreadCount. Ao reduzir a PerFileThreadCount, podemos aumentar a ConcurrentFileCount. Portanto, se partirmos do princípio que a maioria dos ficheiros é mais pequenas no intervalo de 5 GB, pode refazer o nosso cálculo:

    PerFileThreadCount = 10 + ((5 GB - 2.5 GB) / 256 MB) = 20

Por isso, **ConcurrentFileCount** torna-se 96/20, que corresponde a 4,8, arredondado para **4**.

Pode continuar a otimizar estas definições, alterando a **PerFileThreadCount** para cima e para baixo consoante a distribuição dos tamanhos de ficheiro.

### <a name="limitation"></a>Limitação

* **Número de ficheiros é menor que ConcurrentFileCount**: Se o número de ficheiros que está a carregar for menor que o **ConcurrentFileCount** que tenha de calcular, em seguida, deve reduzir **ConcurrentFileCount** seja igual ao número de ficheiros. Pode utilizar quaisquer threads restantes para aumentar a **PerFileThreadCount**.

* **Muitos threads**: Se aumentar a contagem de threads demais sem aumentar o tamanho do cluster, corre o risco de degradar o desempenho. Podem existir problemas de contenção ao alternar de contexto na CPU.

* **Simultaneidade insuficiente**: Se a simultaneidade não for suficiente, em seguida, o cluster pode ser demasiado pequeno. Pode aumentar o número de nós no seu cluster, o que lhe dá mais simultaneidade.

* **Erros de limitação**: Poderá ver erros de limitação, se a simultaneidade for demasiado elevada. Se vir erros de limitação, deverá reduzir a simultaneidade ou contactar a Microsoft.

## <a name="next-steps"></a>Passos Seguintes
* [Utilize a geração 1 de armazenamento do Azure Data Lake para requisitos de grandes volumes de dados](data-lake-store-data-scenarios.md) 
* [Proteger dados no Armazenamento do Data Lake Ger1](data-lake-store-secure-data.md)
* [Utilizar o Azure Data Lake Analytics com o Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Utilizar o Azure HDInsight com Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)

