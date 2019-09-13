---
title: Apache Spark streaming com Apache Kafka-Azure HDInsight
description: Saiba como usar Apache Spark para transmitir dados para dentro ou para fora de Apache Kafka usando o DStreams. Neste exemplo, vai transmitir dados através de um bloco de notas Jupyter do Spark no HDInsight.
keywords: Kafka exemplo, Kafka Zookeeper, Spark streaming Kafka, exemplo de Kafka de streaming do Spark
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: b4d50c39bf670cb8c14d9dbe4feb2fc18c89bf0a
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70917206"
---
# <a name="apache-spark-streaming-dstream-example-with-apache-kafka-on-hdinsight"></a>Exemplo de streaming de Apache Spark (DStream) com Apache Kafka no HDInsight

Saiba como usar [Apache Spark](https://spark.apache.org/) para transmitir dados para dentro ou para fora do [Apache Kafka](https://kafka.apache.org/) no HDInsight usando o [DStreams](https://spark.apache.org/docs/latest/api/java/org/apache/spark/streaming/dstream/DStream.html). Este exemplo usa um [Jupyter Notebook](https://jupyter.org/) que é executado no cluster do Spark.

> [!NOTE]  
> Os passos neste documento criam um grupo de recursos do Azure que contém um cluster do Spark no HDInsight e um cluster do Kafka no HDInsight. Estes dois clusters estão localizados numa Rede Virtual do Azure, o que permite que o cluster do Spark comunique diretamente com o cluster do Kafka.
>
> Quando tiver concluído os passos neste documento, elimine os clusters para evitar encargos em excesso.

> [!IMPORTANT]  
> Este exemplo usa DStreams, que é uma tecnologia mais antiga de streaming do Spark. Para obter um exemplo que usa recursos mais recentes de streaming do Spark, consulte o documento [streaming estruturado do Spark com Apache Kafka](hdinsight-apache-kafka-spark-structured-streaming.md) .

## <a name="create-the-clusters"></a>Criar os clusters

O Apache Kafka no HDInsight não fornece acesso aos mediadores Kafka através da Internet pública. Tudo que se comunica com Kafka deve estar na mesma rede virtual do Azure que os nós no cluster Kafka. Para este exemplo, os clusters Kafka e Spark estão localizados em uma rede virtual do Azure. O diagrama a seguir mostra como a comunicação flui entre os clusters:

![Diagrama de clusters do Spark e Kafka numa rede virtual do Azure](./media/hdinsight-apache-spark-with-kafka/apache-spark-kafka-vnet.png)

> [!NOTE]  
> Embora o Kafka em si seja limitado à comunicação na rede virtual, outros serviços no cluster, como SSH e Ambari, podem ser acessados pela Internet. Para obter mais informações sobre as portas públicas disponíveis com o HDInsight, veja [Portas e URIs utilizados pelo HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Embora você possa criar uma rede virtual do Azure, os clusters Kafka e Spark manualmente, é mais fácil usar um modelo de Azure Resource Manager. Use as etapas a seguir para implantar uma rede virtual do Azure, Kafka e clusters Spark em sua assinatura do Azure.

1. Utilize o botão seguinte para iniciar sessão no Azure e abrir o modelo no Portal do Azure.
    
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-spark-cluster-in-vnet-v4.1.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/hdi-deploy-to-azure1.png" alt="Deploy to Azure"></a>
    
    O modelo do Azure Resource Manager está localizado em **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-spark-cluster-in-vnet-v4.1.json** .

    > [!WARNING]  
    > Para garantir a disponibilidade do Kafka no HDInsight, o cluster tem de conter pelo menos três nós de trabalho. Este modelo cria um cluster do Kafka que contém três nós de trabalho.

    Este modelo cria um cluster HDInsight 3,6 para Kafka e Spark.

2. Use as informações a seguir para preencher as entradas na seção **implantação personalizada** :
   
    ![Implantação personalizada do HDInsight](./media/hdinsight-apache-spark-with-kafka/hdinsight-parameters.png)
   
    * **Grupo de recursos**: Crie um grupo ou selecione um existente. Esse grupo contém o cluster HDInsight.

    * **Local**: Selecione um local geograficamente perto de você.

    * **Nome do cluster de base**: Esse valor é usado como o nome de base para os clusters Spark e Kafka. Por exemplo, a inserção de **hdistreaming** cria um cluster Spark chamado __Spark-hdistreaming__ e um cluster Kafka chamado **Kafka-hdistreaming**.

    * **Nome de usuário de logon do cluster**: O nome de usuário do administrador para os clusters Spark e Kafka.

    * **Senha de logon do cluster**: A senha de usuário do administrador para os clusters Spark e Kafka.

    * **Nome de usuário SSH**: O usuário SSH a ser criado para os clusters Spark e Kafka.

    * **Senha ssh**: A senha para o usuário SSH para os clusters Spark e Kafka.

3. Leia os **Termos e Condições** e selecione **Aceito os temos e as condições apresentados acima**.

4. Por fim, selecione **comprar**. Leva cerca de 20 minutos para criar os clusters.

Depois que os recursos tiverem sido criados, uma página de resumo será exibida.

![Resumo do grupo de recursos para vnet e clusters](./media/hdinsight-apache-spark-with-kafka/hdinsight-group-blade.png)

> [!IMPORTANT]  
> Observe que os nomes dos clusters HDInsight são **Spark-BASENAME** e **Kafka-BASENAME**, em que BASENAME é o nome que você forneceu para o modelo. Você usará esses nomes em etapas posteriores ao conectar-se aos clusters.

## <a name="use-the-notebooks"></a>Usar os blocos de anotações

O código de exemplo descrito neste documento está disponível em [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka).

Para concluir este exemplo, siga as etapas no `README.md`.

## <a name="delete-the-cluster"></a>Eliminar o cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Como as etapas neste documento criam ambos os clusters no mesmo grupo de recursos do Azure, você pode excluir o grupo de recursos no portal do Azure. A exclusão do grupo remove todos os recursos criados seguindo este documento, a rede virtual do Azure e a conta de armazenamento usada pelos clusters.

## <a name="next-steps"></a>Passos Seguintes

Neste exemplo, você aprendeu a usar o Spark para ler e gravar em Kafka. Use os links a seguir para descobrir outras maneiras de trabalhar com o Kafka:

* [Introdução ao Apache Kafka no HDInsight](kafka/apache-kafka-get-started.md)
* [Use MirrorMaker para criar uma réplica de Apache Kafka no HDInsight](kafka/apache-kafka-mirroring.md)
* [Usar Apache Storm com Apache Kafka no HDInsight](hdinsight-apache-storm-with-kafka.md)

