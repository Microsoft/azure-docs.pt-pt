---
title: Apache Spark streaming com Apache Kafka - Azure HDInsight
description: Aprenda a usar o Apache Spark para transmitir dados para dentro ou para fora de Apache Kafka usando DStreams. Neste exemplo, transmite dados usando um Caderno Jupyter de Spark on HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/21/2019
ms.openlocfilehash: 6ef11e9c7907f57b3b8de0a042e1035bce638cf4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104863280"
---
# <a name="apache-spark-streaming-dstream-example-with-apache-kafka-on-hdinsight"></a>Apache Spark streaming exemplo (DStream) com Apache Kafka em HDInsight

Aprenda a usar [o Apache Spark](https://spark.apache.org/) para transmitir dados para dentro ou para fora de Apache [Kafka](https://kafka.apache.org/) em HDInsight usando [DStreams](https://spark.apache.org/docs/latest/api/java/org/apache/spark/streaming/dstream/DStream.html). Este exemplo usa um [Caderno Jupyter](https://jupyter.org/) que funciona no cluster Spark.

> [!NOTE]  
> Os passos neste documento criam um grupo de recursos do Azure que contém um cluster do Spark no HDInsight e um cluster do Kafka no HDInsight. Estes dois clusters estão localizados numa Rede Virtual do Azure, o que permite que o cluster do Spark comunique diretamente com o cluster do Kafka.
>
> Quando tiver concluído os passos neste documento, elimine os clusters para evitar encargos em excesso.

> [!IMPORTANT]  
> Este exemplo utiliza o DStreams, que é uma tecnologia de streaming Spark mais antiga. Para um exemplo que utiliza funcionalidades de streaming spark mais recentes, consulte o Spark Structured Streaming com o documento [Apache Kafka.](hdinsight-apache-kafka-spark-structured-streaming.md)

## <a name="create-the-clusters"></a>Criar os clusters

Apache Kafka na HDInsight não dá acesso aos corretores Kafka através da internet pública. Qualquer coisa que fale com Kafka deve estar na mesma rede virtual Azure que os nós no cluster kafka. Para este exemplo, tanto os clusters Kafka como Spark estão localizados numa rede virtual Azure. O seguinte diagrama mostra como a comunicação flui entre os clusters:

:::image type="content" source="./media/hdinsight-apache-spark-with-kafka/apache-spark-kafka-vnet.png" alt-text="Diagrama de clusters do Spark e Kafka numa rede virtual do Azure" border="false":::

> [!NOTE]  
> Embora a própria Kafka esteja limitada à comunicação dentro da rede virtual, outros serviços no cluster como O SSH e Ambari podem ser acedidos através da internet. Para obter mais informações sobre as portas públicas disponíveis com o HDInsight, veja [Portas e URIs utilizados pelo HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Embora possa criar uma rede virtual Azure, Kafka e Spark agrupamentos manualmente, é mais fácil usar um modelo de Gestor de Recursos Azure. Use os seguintes passos para implementar uma rede virtual Azure, Kafka e Spark clusters para a sua subscrição Azure.

1. Utilize o botão seguinte para iniciar sessão no Azure e abrir o modelo no Portal do Azure.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-spark-cluster-in-vnet-v4.1.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

   O modelo do Gestor de Recursos Azure está localizado em **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-spark-cluster-in-vnet-v4.1.json** .

   > [!WARNING]
   > Para garantir a disponibilidade do Kafka no HDInsight, o cluster tem de conter pelo menos três nós de trabalho. Este modelo cria um cluster do Kafka que contém três nós de trabalho.

   Este modelo cria um cluster HDInsight 3.6 para Kafka e Spark.

1. Utilize as seguintes informações para preencher as entradas na secção **de implantação personalizada:**

   |Propriedade |Valor |
   |---|---|
   |Grupo de recursos|Crie um grupo ou selecione um existente.|
   |Localização|Selecione uma localização geograficamente próxima de si.|
   |Nome do cluster base|Este valor é usado como o nome base para os clusters Spark e Kafka. Por exemplo, a entrada no **hdistreaming** cria um cluster spark chamado __spark-hdistreaming__ e um cluster Kafka chamado **kafka-hdistreaming**.|
   |Nome de Utilizador de Início de Sessão do Cluster|O nome de utilizador administrativo para os clusters Spark e Kafka.|
   |Palavra-passe de Início de Sessão do Cluster|A palavra-passe do utilizador administrativo para os clusters Spark e Kafka.|
   |Nome de Utilizador SSH|O utilizador SSH para criar para os clusters Spark e Kafka.|
   |Palavra-passe do SSH|A palavra-passe para o utilizador SSH para os clusters Spark e Kafka.|

   :::image type="content" source="./media/hdinsight-apache-spark-with-kafka/hdinsight-parameters.png" alt-text="Parâmetros de implementação personalizados HDInsight":::

1. Leia os **Termos e Condições** e, em seguida, selecione **Concordo com os termos e condições acima indicados**.

1. Finalmente, selecione **Comprar**. Leva cerca de 20 minutos para criar os aglomerados.

Uma vez criados os recursos, aparece uma página de resumo.

:::image type="content" source="./media/hdinsight-apache-spark-with-kafka/hdinsight-group-blade.png" alt-text="Resumo do grupo de recursos para o vnet e clusters":::

> [!IMPORTANT]  
> Note que os nomes dos clusters HDInsight são **spark-BASENAME** e **kafka-BASENAME,** onde o NOME BASENAME é o nome que forneceu ao modelo. Utilize estes nomes em etapas posteriores quando se ligar aos clusters.

## <a name="use-the-notebooks"></a>Use os cadernos

O código para o exemplo descrito neste documento está disponível em [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka) .

## <a name="delete-the-cluster"></a>Eliminar o cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Uma vez que os passos deste documento criam ambos os clusters no mesmo grupo de recursos Azure, pode eliminar o grupo de recursos no portal Azure. A eliminação do grupo remove todos os recursos criados seguindo este documento, a Rede Virtual Azure e a conta de armazenamento utilizada pelos clusters.

## <a name="next-steps"></a>Passos seguintes

Neste exemplo, aprendeu a usar Spark para ler e escrever para Kafka. Use os seguintes links para descobrir outras formas de trabalhar com Kafka:

* [Começa com o Apache Kafka na HDInsight](kafka/apache-kafka-get-started.md)
* [Use o MirrorMaker para criar uma réplica de Apache Kafka em HDInsight](kafka/apache-kafka-mirroring.md)
* [Use a Tempestade Apache com Apache Kafka em HDInsight](hdinsight-apache-storm-with-kafka.md)
