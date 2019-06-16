---
title: Criar clusters do Apache Hadoop com um navegador da web, Azure HDInsight
description: Saiba como criar clusters do Apache Hadoop, Apache HBase, Apache Storm ou do Apache Spark no Linux para o HDInsight com um navegador da web e o portal de pré-visualização do Azure.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: hrasheed
ms.openlocfilehash: cf1a6ffa61b41af5abd20dac13b85024001d2ed2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66394001"
---
# <a name="create-linux-based-clusters-in-hdinsight-by-using-the-azure-portal"></a>Criar clusters baseados em Linux no HDInsight com o portal do Azure
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

O portal do Azure é uma ferramenta de gestão baseado na web para serviços e recursos alojados na cloud do Microsoft Azure. Neste artigo, saiba como criar clusters do HDInsight do Azure baseado em Linux através do portal.

## <a name="prerequisites"></a>Pré-requisitos
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **Uma subscrição do Azure**. Ver [como obter a versão de avaliação gratuita do Azure para testar o Hadoop no HDInsight](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Um navegador da web modernos**. O portal do Azure utiliza o HTML5 e JavaScript. Não poderá funcionar corretamente em navegadores mais antigos.

## <a name="create-clusters"></a>Criar clusters
O portal do Azure expõe a maior parte das propriedades do cluster. Ao utilizar modelos Azure Resource Manager, pode ocultar muitos detalhes. Para obter mais informações, consulte [Apache Hadoop criar clusters no HDInsight utilizando modelos do Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]


1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. No menu à esquerda, selecione **+ criar um recurso**.

1.  Sob **do Azure Marketplace**, selecione **Analytics**.

1.  Sob **em destaque**, selecione **HDInsight**.
   
    ![Criar um novo cluster no portal do Azure](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster.png "criar um novo cluster no portal do Azure")

1. Sobre o **HDInsight** página, selecione **personalizado (tamanho, definições, aplicações)** .

1. Selecione **Noções básicas de 1**. Em seguida, introduza as seguintes informações.

    ![Configurar as definições básicas](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-basics.png "criar um novo cluster no portal do Azure")

    * Introduza o **nome do Cluster**. Este nome tem de ser globalmente exclusivo.

    * Partir do **subscrição** pendente, selecione a subscrição do Azure que é utilizada para o cluster.

    * Selecione **tipo de Cluster**. Em seguida, selecione o tipo de cluster que pretende criar. Os exemplos são o Hadoop e Apache Spark. O **sistema operativo** será **Linux**. Em seguida, selecione uma versão de tipo de cluster. Utilize a versão predefinida se não souber o que escolher. Para obter mais informações, veja [HDInsight cluster versions (Versões de clusters HDInsight)](hdinsight-component-versioning.md).
     
        > [!IMPORTANT]  
        > HDInsight clusters são fornecidos numa variedade de tipos. Eles correspondem à carga de trabalho ou tecnologia concebido para o cluster. Não existe nenhum método suportado para criar um cluster que combina vários tipos. Os exemplos são o Storm e HBase num cluster.
        
    * Para **nome de utilizador de início de sessão de Cluster** e **palavra-passe de início de sessão do Cluster**, forneça o nome de utilizador e palavra-passe para o utilizador administrador.

    * Introduza um **nome de utilizador SSH**. Se pretender que a mesma palavra-passe SSH como a palavra-passe de administrador que especificou anteriormente, selecione o **utilizar a mesma palavra-passe como início de sessão do cluster** caixa de verificação. Se não estiver, fornecer uma **palavra-passe** ou **chave pública** para autenticar o utilizador SSH. Uma chave pública é a abordagem que recomendamos. Escolher **selecione** na parte inferior para guardar a configuração de credenciais.
   
        Para obter mais informações, consulte [ligar ao HDInsight (Apache Hadoop) através de SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

    * Em **Grupo de recursos**, especifique se pretende criar um novo grupo de recursos ou utilizar um existente.

    * Especifique um datacenter **localização** onde o cluster ser criado.

    * Selecione **seguinte** para mover para a página seguinte.

4. Partir **segurança 2 + rede**, pode ligar o seu cluster a uma rede virtual, utilizando o menu pendente fornecido. Selecione uma rede virtual do Azure e a sub-rede, se quiser colocar o cluster numa rede virtual. Para obter informações sobre como utilizar o HDInsight com uma rede virtual, consulte [capacidades de estender o HDInsight, com uma rede Virtual do Azure](hdinsight-extend-hadoop-virtual-network.md). O artigo inclui os requisitos de configuração específicos para a rede virtual. 

    Se pretender utilizar o **Enterprise Security Package**, siga estas instruções: [Configurar um cluster do HDInsight com o Enterprise Security Package com o Azure Active Directory Domain Services](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds).

    Selecione **seguinte** para mover para a página seguinte.


5. Partir **armazenamento 3**, especifique se pretende o armazenamento do Azure ou o armazenamento do Azure Data Lake como o armazenamento predefinido. Para obter mais informações, consulte a tabela seguinte.

     ![Configurar definições de armazenamento](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-storage.png "criar um novo cluster no portal do Azure")

     | Armazenamento                                      | Descrição |
     |----------------------------------------------|-------------|
     | **Blobs de armazenamento do Azure como armazenamento predefinido**   | <ul><li>Para **tipo de armazenamento primário**, selecione **armazenamento do Azure**. Para **método de seleção**, escolha **minhas assinaturas** se pretender especificar uma conta de armazenamento que faz parte da sua subscrição do Azure. Em seguida, selecione a conta de armazenamento. Caso contrário, selecione **chave de acesso**. Em seguida, forneça as informações para a conta de armazenamento que pretende que escolha a partir de fora da sua subscrição do Azure.</li><li>Para **contentor predefinido**, escolha o nome de contentor predefinido sugerido pelo portal ou especificar a sua própria.</li><li>Se o armazenamento de Blobs do Azure é o armazenamento predefinido, também pode selecionar **contas de armazenamento adicionais** especificar contas de armazenamento adicional para associar o cluster. Para **chaves de armazenamento do Azure**, selecione **adicionar uma chave de armazenamento**. Em seguida, pode fornecer uma conta de armazenamento das suas subscrições do Azure ou de outras subscrições. Forneça a chave de acesso de conta de armazenamento.</li><li>Se o armazenamento de BLOBs é o armazenamento predefinido, também pode selecionar **acesso de armazenamento do Data Lake** para especificar o armazenamento do Azure Data Lake como armazenamento adicional. Para obter mais informações, consulte [início rápido: Configurar clusters no HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).</li></ul> |
     | **Armazenamento do Azure Data Lake como armazenamento predefinido** | Para **tipo de armazenamento primário**, selecione **Gen1 de armazenamento do Azure Data Lake** ou **Gen2 de armazenamento do Azure Data Lake**. Em seguida, veja o artigo [início rápido: Configurar clusters no HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md) para obter instruções. |
     | **Metastores externas**                      | Como opção, especifique uma base de dados SQL para guardar os metadados do Apache Hive e Apache Oozie associado ao cluster. Para **Selecione uma base de dados do SQL para Hive**, selecione uma base de dados SQL. Em seguida, forneça o nome de utilizador e palavra-passe para a base de dados. Repita estes passos para os metadados do Oozie.<br><br>Seguem-se algumas considerações sobre como utilizar a base de dados SQL do Azure para metastores: <ul><li>A base de dados SQL do Azure que é utilizado para o metastore têm de permitir conectividade a outros serviços do Azure, incluindo o Azure HDInsight. No lado direito do dashboard de base de dados SQL do Azure, selecione o nome do servidor. Este servidor é o que a instância de base de dados SQL é executado. Depois que estiver na vista de servidores, selecione **configurar**. Em seguida, para **dos serviços do Azure**, selecione **Sim**. Em seguida, selecione **Guardar**.</li><li>Quando cria um metastore, não atribua um nome base de dados com barras ou hífenes. Estes carateres podem fazer com que o processo de criação de cluster efetuar a ativação.</li></ul> |

     > [!WARNING]  
     > Não não suportada através de uma conta de armazenamento adicional numa localização diferente do que o cluster do HDInsight.

     Selecione **seguinte** para mover para a página seguinte.


6. Partir **4 aplicações (opcional)** , selecione todas as aplicações que pretende. Microsoft, fornecedores independentes de software (ISVs) ou pode desenvolver esses aplicativos. Para obter mais informações, consulte [instalar aplicações durante a criação do cluster](hdinsight-apps-install-applications.md#install-applications-during-cluster-creation).

    Selecione **seguinte** para mover para a página seguinte.


6. **Tamanho do cluster 5** apresenta informações sobre os nós que são utilizados para este cluster. Defina o número de nós de trabalho que precisa para o cluster. Também é mostrado o custo estimado de execução do cluster.
   
    ![Escalões de preço de nó de especifique](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-nodes.png "especificar o número de nós de cluster")
   
   > [!IMPORTANT]  
   > Se pretender mais do que 32 nós de trabalho, selecione um tamanho de nó principal com, pelo menos, oito núcleos e 14 GB de RAM. Planeie os nós na criação do cluster ou ao dimensionar o cluster após a criação. 
   > 
   > Para obter mais informações sobre tamanhos de nó e os custos associados, consulte [preços do Azure HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).
   
    Selecione **seguinte** para mover para a página seguinte.

8. Partir **6 ações de Script**, pode personalizar um cluster para instalar componentes personalizados. Esta opção funciona se pretender utilizar um script personalizado para personalizar um cluster, como o cluster está a ser criado. Para obter mais informações sobre as ações de script, consulte [clusters do HDInsight baseado em Linux personalizar utilizando as ações de script](hdinsight-hadoop-customize-cluster-linux.md).

   Selecione **seguinte** para mover para a página seguinte.

9. Partir **resumo 7**, verifique as informações que introduziu anteriormente. Em seguida, selecione **Criar**.

     ![Confirmar configurações](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-summary.png "especificar o número de nós de cluster")
    
    > [!NOTE]  
    > A criação do cluster demora algum tempo; aproximadamente 20 minutos. Monitor **notificações** para verificar o processo de aprovisionamento.

10. Depois de concluir o processo de criação, selecione **Ir para recurso** partir do **implementação concluída com êxito** notificação. A janela do cluster fornece as seguintes informações.
    
    ![Interface de clusters](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-completed.png "propriedades do Cluster")
    
    Os ícones na janela são explicados da seguinte forma:
    
    * O **descrição geral** separador fornece todas as informações essenciais sobre o cluster. Os exemplos são o nome, o grupo de recursos a que pertence, a localização, o sistema operativo e o URL para o dashboard do cluster.
    * **Dashboard** direciona-o para o portal de Ambari associado ao cluster.
    * **Secure Shell** fornece informações necessárias para aceder ao cluster utilizando SSH.
    * Usando **Dimensionar cluster**, pode aumentar o número de nós de trabalho associada ao cluster.
    * **Eliminar** elimina o cluster do HDInsight.
    

## <a name="customize-clusters"></a>Personalizar clusters
* [Personalizar clusters do HDInsight através da utilização de arranque](hdinsight-hadoop-customize-cluster-bootstrap.md)
* [Personalizar clusters do HDInsight baseado em Linux utilizando as ações de script](hdinsight-hadoop-customize-cluster-linux.md)

## <a name="delete-the-cluster"></a>Eliminar o cluster
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Resolução de problemas

Caso se depare com problemas com a criação de clusters do HDInsight, veja [aceder aos requisitos de controlo](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="next-steps"></a>Passos Seguintes
Criou um cluster do HDInsight com êxito. Agora, saiba como trabalhar com o seu cluster.

### <a name="apache-hadoop-clusters"></a>Clusters do Apache Hadoop
* [Utilizar o Apache Hive com o HDInsight](hadoop/hdinsight-use-hive.md)
* [Utilizar o Apache Pig com o HDInsight](hadoop/hdinsight-use-pig.md)
* [Utilizar o MapReduce com o HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Clusters do Apache HBase
* [Introdução ao Apache HBase no HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Desenvolver aplicações de Java para o Apache HBase no HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Clusters do Apache Storm
* [Desenvolver topologias de Java para Apache Storm no HDInsight](storm/apache-storm-develop-java-topology.md)
* [Utilizar componentes de Python no Apache Storm no HDInsight](storm/apache-storm-develop-python-topology.md)
* [Implementar e monitorizar topologias Apache Storm no HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)

### <a name="apache-spark-clusters"></a>Clusters do Apache Spark
* [Criar uma aplicação autónoma com o Scala](spark/apache-spark-create-standalone-application.md)
* [Executar tarefas remotamente num cluster do Apache Spark com o Apache Livy](spark/apache-spark-livy-rest-interface.md)
* [Apache Spark com BI: Efetuar análise de dados interativa com o Spark no HDInsight com ferramentas de BI](spark/apache-spark-use-bi-tools.md)
* [Apache Spark com Machine Learning: Utilizar o Spark no HDInsight para prever resultados de inspeções alimentares](spark/apache-spark-machine-learning-mllib-ipython.md)

