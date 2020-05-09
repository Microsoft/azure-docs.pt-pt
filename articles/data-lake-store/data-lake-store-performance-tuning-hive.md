---
title: Afinação de desempenho - Colmeia no Armazenamento de Lagos De Dados Azure Gen1
description: Diretrizes de afinação de desempenho para a Hive no HdInsight e Azure Data Lake Storage Gen1.
author: stewu
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 12/19/2016
ms.author: stewu
ms.openlocfilehash: 2e44332ddab9387c05a45d15101ccd2bdec3ada4
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82690527"
---
# <a name="performance-tuning-guidance-for-hive-on-hdinsight-and-azure-data-lake-storage-gen1"></a>Orientação de afinação de desempenho para hive em HDInsight e Azure Data Lake Storage Gen1

As definições predefinidas foram definidas para proporcionar um bom desempenho em muitos casos de utilização diferentes.  Para consultas intensivas de I/O, a Hive pode ser sintonizada para obter um melhor desempenho com Azure Data Lake Storage Gen1.  

## <a name="prerequisites"></a>Pré-requisitos

* **Uma subscrição Azure.** Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta Gen1 de Armazenamento de Lago**de Dados. Para obter instruções sobre como criar um, consulte [Começar com Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* **Cluster Azure HDInsight** com acesso a uma conta Gen1 de Armazenamento de Data Lake. Consulte [Criar um cluster HDInsight com Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Certifique-se de que ativa o Ambiente de Trabalho Remoto para o cluster.
* **A correr a Colmeia no HDInsight.**  Para aprender sobre executar trabalhos de Colmeia no HDInsight, consulte [Use Hive no HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-hive)
* **Diretrizes de afinação de desempenho sobre data lake storage Gen1**.  Para conceitos gerais de desempenho, consulte [Data Lake Storage Gen1 Performance Tuning Guidance](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-performance-tuning-guidance)

## <a name="parameters"></a>Parâmetros

Aqui estão as configurações mais importantes para sintonizar para um melhor desempenho do Data Lake Storage Gen1:

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
A chave para melhorar o desempenho usando data lake storage Gen1 é aumentar a conmoeda o máximo possível.  A Tez calcula automaticamente o número de tarefas que devem ser criadas para que não precise de defini-la.   

## <a name="example-calculation"></a>Cálculo de exemplo

Digamos que tem um aglomerado de 8 nós D14.  

    Total YARN memory = nodes * YARN memory per node
    Total YARN memory = 8 nodes * 96GB = 768GB
    # of YARN containers = 768GB / 3072MB = 256

## <a name="limitations"></a>Limitações

**Estrangulamento de Armazenamento de Lago de Dados Gen1** 

Se atingir os limites de largura de banda fornecidos pela Data Lake Storage Gen1, começará a ver falhas de tarefa. Isto poderia ser identificado observando erros de estrangulamento nos registos de tarefas.  Pode diminuir o paralelismo aumentando o tamanho do contentor Tez.  Se precisar de mais moeda para o seu trabalho, por favor contacte-nos.

Para verificar se está a ser estrangulado, tem de ativar o depurador de registo sinuoso do lado do cliente. Eis como podes fazer isso:

1. Coloque a seguinte propriedade nas propriedades log4j na Hive config. Isto pode ser feito a partir da vista ambari: log4j.logger.com.microsoft.azure.datalake.store=DEBUG Reiniciar todos os nós/serviço para que a config faça efeito.

2. Se estiver a ser estrangulado, verá o código de erro HTTP 429 no ficheiro de registo da colmeia. O ficheiro de registo da colmeia&lt;&gt;está em /tmp/ utilizador /hive.log

## <a name="further-information-on-hive-tuning"></a>Mais informações sobre a finação da Colmeia

Aqui estão alguns blogs que ajudarão a afinar as suas consultas da Hive:
* [Otimizar consultas de Colmeia para Hadoop no HDInsight](https://azure.microsoft.com/documentation/articles/hdinsight-hadoop-optimize-hive-query/)
* [Desempenho da consulta da Colmeia de resolução de problemas](https://blogs.msdn.microsoft.com/bigdatasupport/2015/08/13/troubleshooting-hive-query-performance-in-hdinsight-hadoop-cluster/)
* [Ignite talk on otimize Hive on HDInsight](https://channel9.msdn.com/events/Machine-Learning-and-Data-Sciences-Conference/Data-Science-Summit-2016/MSDSS25)
