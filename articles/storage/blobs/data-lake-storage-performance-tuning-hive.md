---
title: Azure Data Lake Storage Gen2 diretrizes de ajuste de desempenho do hive | Microsoft Docs
description: Diretrizes de ajuste de desempenho do Azure Data Lake Storage Gen2 Hive
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 1290174fb87306b34be81ed7fa4fb5de3bfba43c
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68847119"
---
# <a name="performance-tuning-guidance-for-hive-on-hdinsight-and-azure-data-lake-storage-gen2"></a>Diretrizes de ajuste de desempenho para o hive no HDInsight e Azure Data Lake Storage Gen2

As configurações padrão foram definidas para fornecer um bom desempenho em vários casos de uso diferentes.  Para consultas com uso intensivo de e/s, o hive pode ser ajustado para obter melhor desempenho com Azure Data Lake Storage Gen2.  

## <a name="prerequisites"></a>Pré-requisitos

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta de data Lake Storage Gen2**. Para obter instruções sobre como criar uma, consulte [início rápido: Criar uma conta de armazenamento Azure Data Lake Storage Gen2](data-lake-storage-quickstart-create-account.md)
* **Cluster HDInsight do Azure** com acesso a uma conta de data Lake Storage Gen2. Confira [usar o Azure data Lake Storage Gen2 com clusters do Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2)
* **Executando o hive no HDInsight**.  Para saber mais sobre a execução de trabalhos do hive no HDInsight, consulte [usar o hive no hdinsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-hive)
* **Diretrizes de ajuste de desempenho em data Lake Storage Gen2**.  Para obter conceitos gerais de desempenho, consulte [diretrizes de ajuste de desempenho data Lake Storage Gen2](data-lake-storage-performance-tuning-guidance.md)

## <a name="parameters"></a>Parâmetros

Aqui estão as configurações mais importantes a serem ajustadas para melhorar o desempenho de Data Lake Storage Gen2:

* **Hive. tez. Container. Size** – a quantidade de memória usada por cada tarefa

* **tez. GROUPING. min-size** – tamanho mínimo de cada mapeador

* **tez. GROUPING. Max-size** – tamanho máximo de cada mapeador

* **Hive. exec. redutor. bytes. per. redutor** – tamanho de cada redutor

**Hive. tez. Container. Size** -o tamanho do contêiner determina a quantidade de memória disponível para cada tarefa.  Essa é a entrada principal para controlar a simultaneidade no hive.  

**tez. GROUPING. min-size** – esse parâmetro permite que você defina o tamanho mínimo de cada mapeador.  Se o número de Mapeadores que o tez escolher for menor que o valor desse parâmetro, tez usará o valor definido aqui.

**tez. GROUPING. Max-size** – o parâmetro permite que você defina o tamanho máximo de cada mapeador.  Se o número de Mapeadores que o tez escolher for maior que o valor desse parâmetro, tez usará o valor definido aqui.

**Hive. exec. redutor. bytes. per. redutor** – esse parâmetro define o tamanho de cada redutor.  Por padrão, cada redutor é 256 MB.  

## <a name="guidance"></a>Orientação

**Definir Hive. exec. redutor. bytes. per. redutor** – o valor padrão funciona bem quando os dados são descompactados.  Para dados compactados, você deve reduzir o tamanho do redutor.  

**Definir Hive. tez. Container. Size** – em cada nó, a memória é especificada por yarn. NodeManager. Resource. Memory-MB e deve ser definida corretamente no cluster HDI por padrão.  Para obter informações adicionais sobre como definir a memória apropriada no YARN, [](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-hive-out-of-memory-error-oom)Confira esta postagem.

As cargas de trabalho com uso intensivo de e/s podem se beneficiar de mais paralelismo, diminuindo o tamanho do contêiner tez. Isso dá ao usuário mais contêineres que aumenta a simultaneidade.  No entanto, algumas consultas do hive exigem uma quantidade significativa de memória (por exemplo, MapJoin).  Se a tarefa não tiver memória suficiente, você receberá uma exceção de pouca memória durante o tempo de execução.  Se você receber exceções de memória insuficiente, deverá aumentar a memória.   

O número simultâneo de tarefas em execução ou paralelismo será limitado pelo total de memória YARN.  O número de contêineres YARN determinará quantas tarefas simultâneas podem ser executadas.  Para localizar a memória YARN por nó, você pode ir para Ambari.  Navegue até YARN e exiba a guia Configurações.  A memória YARN é exibida nesta janela.  

        Total YARN memory = nodes * YARN memory per node
        # of YARN containers = Total YARN memory / Tez container size
A chave para melhorar o desempenho usando o Data Lake Storage Gen2 é aumentar a simultaneidade o máximo possível.  O tez calcula automaticamente o número de tarefas que devem ser criadas para que você não precise defini-las.   

## <a name="example-calculation"></a>Exemplo de cálculo

Digamos que você tenha um cluster D14 de 8 nós.  

    Total YARN memory = nodes * YARN memory per node
    Total YARN memory = 8 nodes * 96GB = 768GB
    # of YARN containers = 768GB / 3072MB = 256

## <a name="further-information-on-hive-tuning"></a>Mais informações sobre o ajuste do hive

Aqui estão alguns Blogs que ajudarão a ajustar suas consultas de Hive:
* [Otimizar consultas do hive para Hadoop no HDInsight](https://azure.microsoft.com/documentation/articles/hdinsight-hadoop-optimize-hive-query/)
* [Solucionando problemas de desempenho de consulta do hive](https://blogs.msdn.microsoft.com/bigdatasupport/2015/08/13/troubleshooting-hive-query-performance-in-hdinsight-hadoop-cluster/)
* [Ignite Talk on Optimize Hive no HDInsight](https://channel9.msdn.com/events/Machine-Learning-and-Data-Sciences-Conference/Data-Science-Summit-2016/MSDSS25)
