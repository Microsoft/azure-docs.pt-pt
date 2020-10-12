---
title: 'Desempenho de sintonia: Hive, HDInsight & Azure Data Lake Storage Gen2 [ Microsoft Docs'
description: Compreenda as diretrizes de afinação para consultas intensivas de I/O usando Hive, HDInsight e Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: fb908fe94f940073753ea8e1cde3da2b2a0c4b6b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88034775"
---
# <a name="tune-performance-hive-hdinsight--azure-data-lake-storage-gen2"></a>Desempenho de sintonização: Hive, HDInsight & Azure Data Lake Storage Gen2

As definições predefinição foram definidas para proporcionar um bom desempenho em muitos casos de utilização diferentes.  Para consultas intensivas de I/O, a Hive pode ser sintonizada para obter um melhor desempenho com a Azure Data Lake Storage Gen2.  

## <a name="prerequisites"></a>Pré-requisitos

* **Uma assinatura Azure**. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta Gen2 de armazenamento de data lake.** Para obter instruções sobre como criar um, consulte [Quickstart: Crie uma conta de armazenamento Azure Data Lake Storage Gen2](data-lake-storage-quickstart-create-account.md)
* **Cluster Azure HDInsight** com acesso a uma conta Gen2 de armazenamento de data lake. Ver [Utilizar Azure Data Lake Storage Gen2 com clusters Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2)
* **A executar colmeia em HDInsight**.  Para aprender sobre como gerir os trabalhos de Colmeia em HDInsight, consulte [Use Hive on HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-hive)
* **Diretrizes de afinação de desempenho sobre data lake storage gen2**.  Para conceitos de desempenho geral, consulte [data lake storage Gen2 Performance Afinação de Afinação](data-lake-storage-performance-tuning-guidance.md)

## <a name="parameters"></a>Parâmetros

Aqui estão as configurações mais importantes para sintonizar para melhorar o desempenho da Data Lake Storage Gen2:

* **hive.tez.container.size** – a quantidade de memória utilizada por cada tarefa

* **tez.grouping.min-size** – tamanho mínimo de cada mapper

* **tez.grouping.max-size** – tamanho máximo de cada mapper

* **hive.exec.reducer.bytes.per.reducer** – tamanho de cada redutor

**hive.tez.container.size** - O tamanho do recipiente determina a quantidade de memória disponível para cada tarefa.  Esta é a principal entrada para controlar a concordância na Colmeia.  

**tez.grouping.min-size** – Este parâmetro permite definir o tamanho mínimo de cada mapper.  Se o número de mappers que tez escolhe for menor do que o valor deste parâmetro, então tez usará o valor definido aqui.

**tez.grouping.max-size** – O parâmetro permite definir o tamanho máximo de cada mapper.  Se o número de mappers que tez escolhe for maior do que o valor deste parâmetro, então tez usará o valor definido aqui.

**hive.exec.reducer.bytes.per.redutor** – Este parâmetro define o tamanho de cada redutor.  Por padrão, cada redutor é de 256MB.  

## <a name="guidance"></a>Orientação

**Desaprova hive.exec.reducer.bytes.per.reducer** – O valor predefinido funciona bem quando os dados não são reprimidos.  Para os dados que são comprimidos, deve reduzir o tamanho do redutor.  

**Definir hive.tez.container.size** - Em cada nó, a memória é especificada por yarn.nodemanager.resource.memory-mb e deve ser corretamente definida no cluster HDI por predefinição.  Para obter informações adicionais sobre a definição da memória apropriada em YARN, consulte este [post](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-hive-out-of-memory-error-oom).

As cargas de trabalho intensivas de I/O podem beneficiar de um maior paralelismo diminuindo o tamanho do recipiente Tez. Isto dá ao utilizador mais recipientes que aumentam a concordância.  No entanto, algumas consultas de Colmeia requerem uma quantidade significativa de memória (por exemplo, MapJoin).  Se a tarefa não tiver memória suficiente, obterá uma exceção da memória durante o tempo de funcionação.  Se receber exceções de memória, então deve aumentar a memória.   

O número simultâneo de tarefas em execução ou paralelismo será limitado pela memória total do YARN.  O número de contentores DE FIOS ditará quantas tarefas simultâneas podem ser executadas.  Para encontrar a memória yarn por nó, você pode ir a Ambari.  Navegue até YARN e veja o separador Configs.  A memória YARN é apresentada nesta janela.  

- Total da memória YARN = nós * memória YARN por nó
- \# de recipientes YARN = Total memória YARN / Tamanho do recipiente Tez

A chave para melhorar o desempenho usando data lake storage gen2 é aumentar a conúnquidade o mais possível.  O Tez calcula automaticamente o número de tarefas que devem ser criadas para que não seja necessário defini-lo.   

## <a name="example-calculation"></a>Cálculo de exemplo

Digamos que tem um aglomerado de 8 nó D14.  

- Total da memória YARN = nós * memória YARN por nó
- Total de memória YARN = 8 nóns * 96GB = 768GB
- \# de contentores YARN = 768GB / 3072MB = 256

## <a name="further-information-on-hive-tuning"></a>Mais informações sobre a afinação da Colmeia

Aqui estão alguns blogs que ajudarão a afinar as suas consultas de Hive:
* [Otimizar consultas de Colmeia para Hadoop em HDInsight](https://azure.microsoft.com/documentation/articles/hdinsight-hadoop-optimize-hive-query/)
* [Optimize Apache Hive queries in Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-optimize-hive-query) (Otimizar as consultas do Apache Hive no Azure HDInsight)
* [Ignite talk on otimize Hive on HDInsight](https://channel9.msdn.com/events/Machine-Learning-and-Data-Sciences-Conference/Data-Science-Summit-2016/MSDSS25)
