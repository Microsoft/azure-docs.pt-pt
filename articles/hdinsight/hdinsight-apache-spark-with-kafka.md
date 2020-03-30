---
title: Apache Spark streaming com Apache Kafka - Azure HDInsight
description: Aprenda a usar apache Spark para transmitir dados para dentro ou para fora de Apache Kafka usando DStreams. Neste exemplo, vai transmitir dados através de um bloco de notas Jupyter do Spark no HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/21/2019
ms.openlocfilehash: d868cdd346c79cf77d4f8c1ea6e4b20adcd99b6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74327402"
---
# <a name="apache-spark-streaming-dstream-example-with-apache-kafka-on-hdinsight"></a>Exemplo de streaming apache Spark (DStream) com Apache Kafka no HDInsight

Aprenda a usar [apache Spark](https://spark.apache.org/) para transmitir dados para dentro ou para fora de [Apache Kafka](https://kafka.apache.org/) no HDInsight usando [DStreams](https://spark.apache.org/docs/latest/api/java/org/apache/spark/streaming/dstream/DStream.html). Este exemplo usa um [Caderno Jupyter](https://jupyter.org/) que funciona no cluster Spark.

> [!NOTE]  
> Os passos neste documento criam um grupo de recursos do Azure que contém um cluster do Spark no HDInsight e um cluster do Kafka no HDInsight. Estes dois clusters estão localizados numa Rede Virtual do Azure, o que permite que o cluster do Spark comunique diretamente com o cluster do Kafka.
>
> Quando tiver concluído os passos neste documento, elimine os clusters para evitar encargos em excesso.

> [!IMPORTANT]  
> Este exemplo utiliza DStreams, que é uma tecnologia de streaming spark mais antiga. Para um exemplo que utiliza funcionalidades de streaming mais recentes spark, consulte o [Spark Structured Streaming com](hdinsight-apache-kafka-spark-structured-streaming.md) o documento Apache Kafka.

## <a name="create-the-clusters"></a>Criar os clusters

Apache Kafka no HDInsight não dá acesso aos corretores Kafka através da internet pública. Tudo o que fala com Kafka deve estar na mesma rede virtual Azure que os nós do aglomerado de Kafka. Para este exemplo, tanto os clusters Kafka como spark estão localizados numa rede virtual Azure. O diagrama que se segue mostra como a comunicação flui entre os clusters:

![Diagrama de clusters do Spark e Kafka numa rede virtual do Azure](./media/hdinsight-apache-spark-with-kafka/apache-spark-kafka-vnet.png)

> [!NOTE]  
> Embora a própria Kafka se limite à comunicação dentro da rede virtual, outros serviços no cluster, como sSH e Ambari, podem ser acedidos através da internet. Para obter mais informações sobre as portas públicas disponíveis com o HDInsight, veja [Portas e URIs utilizados pelo HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Enquanto você pode criar uma rede virtual Azure, Kafka, e clusters Spark manualmente, é mais fácil usar um modelo de Gestor de Recursos Azure. Utilize os seguintes passos para implantar uma rede virtual Azure, kafka e clusters Spark para a sua subscrição Azure.

1. Utilize o botão seguinte para iniciar sessão no Azure e abrir o modelo no Portal do Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-spark-cluster-in-vnet-v4.1.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

    O modelo do Gestor de **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-spark-cluster-in-vnet-v4.1.json**Recursos Azure está localizado em .

    > [!WARNING]  
    > Para garantir a disponibilidade do Kafka no HDInsight, o cluster tem de conter pelo menos três nós de trabalho. Este modelo cria um cluster do Kafka que contém três nós de trabalho.

    Este modelo cria um cluster HDInsight 3.6 tanto para Kafka como para Spark.

1. Utilize as seguintes informações para preencher as entradas na secção de **implementação personalizada:**

    |Propriedade |Valor |
    |---|---|
    |Grupo de recursos|Crie um grupo ou selecione um existente.|
    |Localização|Selecione um local geograficamente próximo de si.|
    |Nome do cluster de base|Este valor é usado como o nome base para os aglomerados Spark e Kafka. Por exemplo, a entrada em **hdistreaming** cria um aglomerado de faíscas chamado __spark-hdistreaming__ e um aglomerado kafka chamado **kafka-hdistreaming**.|
    |Nome de Utilizador de Início de Sessão do Cluster|O nome de utilizador administrativo dos aglomerados Spark e Kafka.|
    |Palavra-passe de Início de Sessão do Cluster|A senha de utilizador administrador dos clusters Spark e Kafka.|
    |Nome de Utilizador SSH|O utilizador sSH para criar para os clusters Spark e Kafka.|
    |Palavra-passe do SSH|A palavra-passe para o utilizador SSH para os clusters Spark e Kafka.|

    ![Parâmetros de implementação personalizados HDInsight](./media/hdinsight-apache-spark-with-kafka/hdinsight-parameters.png)

1. Leia os **Termos e Condições**, e depois selecione **concordo com os termos e condições acima indicados**.

1. Finalmente, selecione **Comprar**. Leva cerca de 20 minutos para criar os aglomerados.

Uma vez criados os recursos, aparece uma página sumária.

![Resumo do grupo de recursos para o vnet e clusters](./media/hdinsight-apache-spark-with-kafka/hdinsight-group-blade.png)

> [!IMPORTANT]  
> Note que os nomes dos clusters HDInsight são **spark-BASENAME** e **kafka-BASENAME,** onde BASENAME é o nome que forneceu ao modelo. Usa estes nomes em passos posteriores quando se conecta aos clusters.

## <a name="use-the-notebooks"></a>Use os cadernos

O código do exemplo descrito neste [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka)documento está disponível em .

## <a name="delete-the-cluster"></a>Eliminar o cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Uma vez que os passos deste documento criam ambos os clusters no mesmo grupo de recursos Azure, pode eliminar o grupo de recursos no portal Azure. A eliminação do grupo remove todos os recursos criados seguindo este documento, a Rede Virtual Azure e a conta de armazenamento utilizada pelos clusters.

## <a name="next-steps"></a>Passos seguintes

Neste exemplo, aprendeu a usar Spark para ler e escrever a Kafka. Use os seguintes links para descobrir outras formas de trabalhar com Kafka:

* [Começa com Apache Kafka no HDInsight](kafka/apache-kafka-get-started.md)
* [Use mirrormaker para criar uma réplica de Apache Kafka no HDInsight](kafka/apache-kafka-mirroring.md)
* [Use Apache Storm com Apache Kafka no HDInsight](hdinsight-apache-storm-with-kafka.md)
