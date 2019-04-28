---
title: Azure Data Lake Gen1 Hive o desempenho do armazenamento diretrizes de ajuste | Documentos da Microsoft
description: Azure Data Lake Gen1 Hive o desempenho do armazenamento diretrizes de ajuste
services: data-lake-store
documentationcenter: ''
author: stewu
manager: amitkul
editor: stewu
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 12/19/2016
ms.author: stewu
ms.openlocfilehash: 433c6b7d70cea9406b67d65e23cc357939cb5aa0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61437281"
---
# <a name="performance-tuning-guidance-for-hive-on-hdinsight-and-azure-data-lake-storage-gen1"></a>Guia para o Hive no HDInsight e Azure Data Lake Storage Gen1 de sintonização de desempenho

As predefinições foram definidas para proporcionar um bom desempenho em muitos casos de utilização diferentes.  Para consultas intensivas de e/s, o Hive pode ser ajustado para obter um melhor desempenho com Gen1 de armazenamento do Azure Data Lake.  

## <a name="prerequisites"></a>Pré-requisitos

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta do Data Lake Storage Gen1**. Para obter instruções sobre como criar um, consulte [introdução ao Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* **Cluster de HDInsight do Azure** com acesso a uma conta de geração 1 de armazenamento do Data Lake. Ver [criar um cluster do HDInsight com Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Certifique-se de que ativar o ambiente de trabalho remoto para o cluster.
* **Em execução do Hive no HDInsight**.  Para saber mais sobre a execução de tarefas do Hive no HDInsight, veja [utilizar o Hive no HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-hive)
* **Diretrizes sobre a geração 1 de armazenamento do Data Lake de ajuste de desempenho**.  Para os conceitos gerais de desempenho, consulte [Data Lake Storage Gen1 ajuste de orientação de desempenho](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-performance-tuning-guidance)

## <a name="parameters"></a>Parâmetros

Seguem-se as definições mais importantes para otimizar o desempenho melhorado de geração 1 de armazenamento do Data Lake:

* **Hive.tez.Container.size** – a quantidade de memória utilizada pelas tarefas de cada

* **tamanho de tez.grouping.min** – mínimo tamanho de cada mapeador de pontos

* **tamanho de tez.grouping.Max** – máximo tamanho de cada mapeador de pontos

* **Hive.exec.reducer.bytes.Per.reducer** – tamanho de cada reducer

**Hive.tez.Container.size** -o tamanho do contentor determina a quantidade de memória está disponível para cada tarefa.  Esta é a entrada principal para controlar a simultaneidade no Hive.  

**tamanho de tez.grouping.min** – este parâmetro permite-lhe definir o tamanho mínimo de cada mapeador de pontos.  Se o número de mapeadores que escolhe Tez for menor que o valor deste parâmetro, em seguida, Tez utilizará o valor definido aqui.

**tamanho de tez.grouping.Max** – o parâmetro permite-lhe definir o tamanho máximo de cada mapeador de pontos.  Se o número de mapeadores que escolhe Tez é maior do que o valor deste parâmetro, em seguida, Tez utilizará o valor definido aqui.

**Hive.exec.reducer.bytes.Per.reducer** – este parâmetro define o tamanho de cada reducer.  Por predefinição, cada reducer é de 256MB.  

## <a name="guidance"></a>Orientação

**Definir hive.exec.reducer.bytes.per.reducer** – o valor predefinido funciona bem quando os dados são descomprimidos.  Para os dados que são comprimidos, deve reduzir o tamanho do reducer.  

**Definir hive.tez.container.size** – em cada nó, a memória é especificada por yarn.nodemanager.resource.memory mb e devem ser definida corretamente no cluster do HDI por predefinição.  Para obter mais informações sobre como definir a memória apropriada no YARN, consulte [publicar](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-hive-out-of-memory-error-oom).

Cargas de trabalho intensivas de e/s podem se beneficiar mais paralelismo, diminuindo o tamanho de contentor do Tez. Isso dá ao usuário mais contentores que aumenta a simultaneidade.  No entanto, algumas consultas do Hive exigem uma quantidade significativa de memória (por exemplo, MapJoin).  Se a tarefa não tem memória suficiente, obterá um fora de exceção de memória durante o tempo de execução.  Se receber exceções de memória insuficiente, em seguida, deve aumentar a memória.   

O número em simultâneo de tarefas em execução ou de paralelismo irá ser vinculado de acordo com a memória YARN total.  O número de contentores YARN ditará quantas tarefas simultâneas podem ser executadas.  Para localizar a memória YARN por nó, pode aceder à Ambari.  Navegue para o YARN e ver a guia configurações.  A memória YARN é apresentada nesta janela.  

        Total YARN memory = nodes * YARN memory per node
        # of YARN containers = Total YARN memory / Tez container size
A chave para melhorar o desempenho com a geração 1 de armazenamento do Data Lake é aumentar a simultaneidade tanto quanto possível.  Tez calcula automaticamente o número de tarefas que devem ser criados para que não é necessário configurá-lo.   

## <a name="example-calculation"></a>Cálculo de exemplo

Digamos que tem um cluster de D14 de 8 nós.  

    Total YARN memory = nodes * YARN memory per node
    Total YARN memory = 8 nodes * 96GB = 768GB
    # of YARN containers = 768GB / 3072MB = 256

## <a name="limitations"></a>Limitações

**Limitação do Data Lake Storage Gen1** 

Se atingir os limites de largura de banda fornecido pela geração 1 de armazenamento do Data Lake, poderia começar a ver falhas de tarefas. Isso poderia ser identificado por erros de limitação a observar nos registos de tarefas.  Pode diminuir o paralelismo aumente o tamanho do contentor Tez.  Se precisar de mais simultaneidade para a sua tarefa, entre em contato conosco.

Para verificar se obter limitados, tem de ativar a depuração de registo no lado do cliente. Eis como pode fazer isso:

1. Coloque a seguinte propriedade nas propriedades do log4j na configuração do Hive. Isso pode ser feito da vista do Ambari: log4j.logger.com.microsoft.azure.datalake.store=DEBUG reiniciar todos os o nós/serviço para a configuração entrem em vigor.

2. Se estiver obtendo otimizado, verá o código de erro HTTP 429 no ficheiro de registo do hive. O ficheiro de registo do hive é nos /tmp/&lt;utilizador&gt;/hive.log

## <a name="further-information-on-hive-tuning"></a>Obter mais informações sobre a otimização do Hive

Aqui estão alguns blogs que o ajudará a otimização das suas consultas do Hive:
* [Otimizar as consultas do Hive do Hadoop no HDInsight](https://azure.microsoft.com/documentation/articles/hdinsight-hadoop-optimize-hive-query/)
* [Resolução de problemas de desempenho das consultas do Hive](https://blogs.msdn.microsoft.com/bigdatasupport/2015/08/13/troubleshooting-hive-query-performance-in-hdinsight-hadoop-cluster/)
* [Conversa do ignite otimizar no Hive no HDInsight](https://channel9.msdn.com/events/Machine-Learning-and-Data-Sciences-Conference/Data-Science-Summit-2016/MSDSS25)
