---
title: Apache Spark de transmissão em fluxo com o Apache Kafka - Azure HDInsight
description: Saiba como utilizar o Apache Spark para transmitir dados para dentro ou fora do Apache Kafka com DStreams. Neste exemplo, vai transmitir dados através de um bloco de notas Jupyter do Spark no HDInsight.
keywords: exemplo de kafka, zookeeper do kafka, kafka, spark, o exemplo de kafka de transmissão em fluxo de transmissão em fluxo do spark
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: e0c39ae5f5c23ae0715ef1eee38b6dd34704538a
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097155"
---
# <a name="apache-spark-streaming-dstream-example-with-apache-kafka-on-hdinsight"></a>Apache Spark (DStream) de exemplo com o Apache Kafka no HDInsight de transmissão em fluxo

Aprenda a usar [Apache Spark](https://spark.apache.org/) para transmitir dados para dentro ou fora de [Apache Kafka](https://kafka.apache.org/) sobre como utilizar o HDInsight [DStreams](https://spark.apache.org/docs/latest/api/java/org/apache/spark/streaming/dstream/DStream.html). Este exemplo utiliza um [bloco de notas do Jupyter](https://jupyter.org/) que é executado no cluster do Spark.

> [!NOTE]  
> Os passos neste documento criam um grupo de recursos do Azure que contém um cluster do Spark no HDInsight e um cluster do Kafka no HDInsight. Estes dois clusters estão localizados numa Rede Virtual do Azure, o que permite que o cluster do Spark comunique diretamente com o cluster do Kafka.
>
> Quando tiver concluído os passos neste documento, elimine os clusters para evitar encargos em excesso.

> [!IMPORTANT]  
> Este exemplo utiliza DStreams, que é uma tecnologia de transmissão em fluxo do Spark mais antiga. Para obter um exemplo que utiliza o Spark mais recente funcionalidades de transmissão em fluxo, consulte a [Spark transmissão em fluxo estruturada com o Apache Kafka](hdinsight-apache-kafka-spark-structured-streaming.md) documento.

## <a name="create-the-clusters"></a>Criar os clusters

O Apache Kafka no HDInsight não fornece acesso aos mediadores Kafka através da Internet pública. Tudo o que se comunica com o Kafka tem de estar na mesma rede virtual do Azure que os nós do cluster do Kafka. Neste exemplo, os clusters de Kafka e Spark estão localizados numa rede virtual do Azure. O diagrama seguinte mostra como a comunicação flui entre os clusters:

![Diagrama de clusters do Spark e Kafka numa rede virtual do Azure](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]  
> Embora o Kafka em si está limitado à comunicação na rede virtual, outros serviços em cluster como SSH e Ambari podem ser acedidos através da internet. Para obter mais informações sobre as portas públicas disponíveis com o HDInsight, veja [Portas e URIs utilizados pelo HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Embora pode criar uma rede virtual do Azure, Kafka, e clusters do Spark manualmente, é mais fácil de usar um modelo Azure Resource Manager. Utilize os seguintes passos para implementar uma rede virtual do Azure, Kafka e do Spark clusters para a sua subscrição do Azure.

1. Utilize o botão seguinte para iniciar sessão no Azure e abrir o modelo no Portal do Azure.
    
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-spark-cluster-in-vnet-v4.1.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    O modelo do Azure Resource Manager está localizado em **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-spark-cluster-in-vnet-v4.1.json**.

    > [!WARNING]  
    > Para garantir a disponibilidade do Kafka no HDInsight, o cluster tem de conter pelo menos três nós de trabalho. Este modelo cria um cluster do Kafka que contém três nós de trabalho.

    Este modelo cria um cluster do HDInsight 3.6 para Kafka e Spark.

2. Utilize as seguintes informações para preencher as entradas a **implementação personalizada** secção:
   
    ![Implantação personalizada do HDInsight](./media/hdinsight-apache-spark-with-kafka/parameters.png)
   
    * **Grupo de recursos**: Crie um grupo ou selecione um existente. Este grupo contém o cluster do HDInsight.

    * **Localização**: Selecione um local geograficamente próximo de.

    * **Nome do Cluster de base**: Este valor é utilizado como o nome de base para os clusters do Spark e Kafka. Por exemplo, introduzir **hdistreaming** cria um cluster do Spark com o nome __spark hdistreaming__ e um cluster de Kafka com o nome **kafka hdistreaming**.

    * **Nome de utilizador de início de sessão do cluster**: O nome de utilizador de administrador para os clusters do Spark e Kafka.

    * **Palavra-passe de início de sessão do cluster**: A senha do usuário de administrador para os clusters do Spark e Kafka.

    * **Nome de utilizador SSH**: O utilizador SSH para criar para os clusters do Spark e Kafka.

    * **SSH palavra-passe**: A palavra-passe do utilizador SSH para os clusters do Spark e Kafka.

3. Leia os **Termos e Condições** e selecione **Aceito os temos e as condições apresentados acima**.

4. Por fim, selecione **Compra**. Demora cerca de 20 minutos para criar os clusters.

Assim que os recursos foram criados, é apresentada uma página de resumo.

![Resumo para a vnet e a clusters do grupo de recursos](./media/hdinsight-apache-spark-with-kafka/groupblade.png)

> [!IMPORTANT]  
> Tenha em atenção que os nomes dos HDInsight clusters estão **spark NOME_DE_BASE** e **kafka NOME_DE_BASE**, onde NOME_DE_BASE é o nome que indicou para o modelo. Utilize esses nomes em passos posteriores ao estabelecer ligação com os clusters.

## <a name="use-the-notebooks"></a>Utilize os blocos de notas

O código de exemplo descrito neste documento está disponível em [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka).

Para concluir este exemplo, siga os passos a `README.md`.

## <a name="delete-the-cluster"></a>Eliminar o cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Uma vez que os passos neste documento criam ambos os clusters no mesmo grupo de recursos do Azure, pode eliminar o grupo de recursos no portal do Azure. A eliminação do grupo remove todos os recursos criados através dos seguintes deste documento, a rede Virtual do Azure e conta de armazenamento utilizada pelos clusters.

## <a name="next-steps"></a>Passos Seguintes

Neste exemplo, aprendeu a utilizar o Spark para ler e escrever para o Kafka. Utilize as seguintes ligações para descobrir outras maneiras de trabalhar com o Kafka:

* [Introdução ao Apache Kafka no HDInsight](kafka/apache-kafka-get-started.md)
* [Utilize o MirrorMaker para criar uma réplica do Apache Kafka no HDInsight](kafka/apache-kafka-mirroring.md)
* [Utilizar o Apache Storm com o Apache Kafka no HDInsight](hdinsight-apache-storm-with-kafka.md)

