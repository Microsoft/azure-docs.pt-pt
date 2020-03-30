---
title: 'Performance de melodia: Hive, HDInsight & Azure Data Lake Storage Gen2 [ Microsoft Docs'
description: Diretrizes de afinação de desempenho da Colmeia de Azure Data Lake Gen2 Hive.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 66042568cede364c16302fbd85751de4113bbe0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74327592"
---
# <a name="tune-performance-hive-hdinsight--azure-data-lake-storage-gen2"></a>Performance de sintonização: Hive, HDInsight & Azure Data Lake Storage Gen2

As definições predefinidas foram definidas para proporcionar um bom desempenho em muitos casos de utilização diferentes.  Para consultas intensivas de I/O, a Hive pode ser sintonizada para obter um melhor desempenho com Azure Data Lake Storage Gen2.  

## <a name="prerequisites"></a>Pré-requisitos

* **Uma subscrição Azure.** Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta Gen2 de Armazenamento de Lago de Dados.** Para obter instruções sobre como criar uma, consulte [Quickstart: Crie uma conta](data-lake-storage-quickstart-create-account.md) de armazenamento de armazenamento de lago de dados Azure Gen2
* **Cluster Azure HDInsight** com acesso a uma conta Gen2 de Armazenamento de Data Lake. Ver [Utilização Azure Data Lake Storage Gen2 com clusters Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2)
* **A correr a Colmeia no HDInsight.**  Para aprender sobre executar trabalhos de Colmeia no HDInsight, consulte [Use Hive no HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-hive)
* **Diretrizes de afinação de desempenho sobre data lake storage Gen2**.  Para conceitos gerais de desempenho, consulte [Data Lake Storage Gen2 Performance Tuning Guidance](data-lake-storage-performance-tuning-guidance.md)

## <a name="parameters"></a>Parâmetros

Aqui estão as configurações mais importantes para sintonizar para um melhor desempenho do Data Lake Storage Gen2:

* **hive.tez.container.size** – a quantidade de memória utilizada por cada tarefa

* **tez.grouping.min-size** – tamanho mínimo de cada mapper

* **tez.grouping.max-size** – tamanho máximo de cada mapeador

* **hive.exec.reducer.bytes.per.reducer** – tamanho de cada redutor

**hive.tez.container.size** - O tamanho do recipiente determina a quantidade de memória disponível para cada tarefa.  Esta é a principal entrada para controlar a conmoeda na Colmeia.  

**tez.grouping.min-size** – Este parâmetro permite-lhe definir o tamanho mínimo de cada mapeador.  Se o número de mappers que tez escolhe é menor do que o valor deste parâmetro, então Tez usará o valor estabelecido aqui.

**tez.grouping.max-size** – O parâmetro permite-lhe definir o tamanho máximo de cada mapeador.  Se o número de mappers que tez escolhe é maior do que o valor deste parâmetro, então Tez usará o valor estabelecido aqui.

**hive.exec.reducer.bytes.per.reducer** – Este parâmetro define o tamanho de cada redutor.  Por predefinição, cada redutor é de 256MB.  

## <a name="guidance"></a>Orientação

**Definir hive.exec.reducer.bytes.per.reducer** – O valor predefinido funciona bem quando os dados não são comprimidos.  Para dados comprimidos, deve reduzir o tamanho do redutor.  

**Definir hive.tez.container.size** – Em cada nó, a memória é especificada por fio.nodemanager.resource.memory-mb e deve ser corretamente definida no cluster HDI por padrão.  Para obter informações adicionais sobre a definição da memória adequada no ARN, consulte este [post](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-hive-out-of-memory-error-oom).

As cargas de trabalho intensivas de I/O podem beneficiar de mais paralelismo diminuindo o tamanho do contentor Tez. Isto dá ao utilizador mais contentores que aumentam a conmoeda.  No entanto, algumas consultas da Hive requerem uma quantidade significativa de memória (por exemplo, MapJoin).  Se a tarefa não tiver memória suficiente, obterá uma exceção de memória durante o tempo de funcionação.  Se receber sem exceções de memória, então deve aumentar a memória.   

O número simultâneo de tarefas em execução ou paralelismo será delimitada pela memória total do ARN.  O número de contentores de ARN ditará quantas tarefas simultâneas podem executar.  Para encontrar a memória yARN por nó, pode ir a Ambari.  Navegue para o YARN e veja o separador Configs.  A memória yARN é exibida nesta janela.  

        Total YARN memory = nodes * YARN memory per node
        # of YARN containers = Total YARN memory / Tez container size
A chave para melhorar o desempenho usando data lake storage Gen2 é aumentar a conmoeda o máximo possível.  A Tez calcula automaticamente o número de tarefas que devem ser criadas para que não precise de defini-la.   

## <a name="example-calculation"></a>Cálculo de exemplo

Digamos que tem um aglomerado de 8 nós D14.  

    Total YARN memory = nodes * YARN memory per node
    Total YARN memory = 8 nodes * 96GB = 768GB
    # of YARN containers = 768GB / 3072MB = 256

## <a name="further-information-on-hive-tuning"></a>Mais informações sobre a finação da Colmeia

Aqui estão alguns blogs que ajudarão a afinar as suas consultas da Hive:
* [Otimizar consultas de Colmeia para Hadoop no HDInsight](https://azure.microsoft.com/documentation/articles/hdinsight-hadoop-optimize-hive-query/)
* [Desempenho da consulta da Colmeia de resolução de problemas](https://blogs.msdn.microsoft.com/bigdatasupport/2015/08/13/troubleshooting-hive-query-performance-in-hdinsight-hadoop-cluster/)
* [Ignite talk on otimize Hive on HDInsight](https://channel9.msdn.com/events/Machine-Learning-and-Data-Sciences-Conference/Data-Science-Summit-2016/MSDSS25)
