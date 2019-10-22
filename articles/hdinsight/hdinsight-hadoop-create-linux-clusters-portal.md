---
title: Criar clusters Apache Hadoop usando um navegador da Web, Azure HDInsight
description: Saiba como criar clusters Apache Hadoop, Apache HBase, Apache Storm ou Apache Spark no Linux para HDInsight usando um navegador da Web e o portal do Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/28/2019
ms.openlocfilehash: 687fde2e203ed471e2f0164f1f4a670de4afc74e
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2019
ms.locfileid: "71677091"
---
# <a name="create-linux-based-clusters-in-hdinsight-by-using-the-azure-portal"></a>Criar clusters baseados em Linux no HDInsight usando o portal do Azure

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

O portal do Azure é uma ferramenta de gerenciamento baseada na Web para serviços e recursos hospedados na Microsoft Azure Cloud. Neste artigo, você aprenderá a criar clusters do Azure HDInsight baseados em Linux usando o Portal.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **Uma subscrição do Azure**. Veja [como obter a avaliação gratuita do Azure para testar o Hadoop no HDInsight](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Um navegador da Web moderno**. O portal do Azure usa HTML5 e JavaScript. Ele pode não funcionar corretamente em navegadores da Web mais antigos.

## <a name="create-clusters"></a>Criar clusters

O portal do Azure expõe a maioria das propriedades do cluster. Usando modelos de Azure Resource Manager, você pode ocultar muitos detalhes. Para obter mais informações, consulte [criar Apache Hadoop clusters no HDInsight usando modelos do Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. No menu à esquerda, navegue até **+ criar um recurso**  >  **Analytics**  > **HDInsight**.

    ![Criar um novo cluster no portal do Azure](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster.png "Criando um novo cluster no portal do Azure")

1. Na página **criar cluster HDInsight** , selecione **ir para a experiência de criação clássica**.

    ![Ir para a experiência de criação clássica](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-create-classic.png)

1. Na página **HDInsight** , selecione **personalizado (tamanho, configurações, aplicativos)** .

1. Selecione **1 noções básicas**. Em seguida, insira as informações a seguir.

    ![Conceitos básicos de criação de cluster do HDInsight](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-basics.png "Criando um novo cluster no portal do Azure")

    * Insira o **nome do cluster**. Este nome tem de ser globalmente exclusivo.

    * Na lista suspensa **assinatura** , selecione a assinatura do Azure que é usada para o cluster.

    * Selecione o **tipo de cluster**. Em seguida, selecione o tipo de cluster que você deseja criar. Os exemplos são Hadoop e Apache Spark. O **sistema operacional** será o **Linux**. Em seguida, selecione uma versão de tipo de cluster. Use a versão padrão se você não souber o que escolher. Para obter mais informações, veja [HDInsight cluster versions (Versões de clusters HDInsight)](hdinsight-component-versioning.md).
     
        > [!IMPORTANT]  
        > Os clusters HDInsight vêm em uma variedade de tipos. Eles correspondem à carga de trabalho ou à tecnologia para a qual o cluster é ajustado. Não há nenhum método com suporte para criar um cluster que combina vários tipos. Os exemplos são Storm e HBase em um cluster.
        
    * Para **nome** de usuário de logon do cluster e **senha de logon do cluster**, forneça o nome de usuário e a senha para os usuários administradores.

    * Insira um **nome de usuário SSH**. Se você quiser a mesma senha SSH que a senha de administrador especificada anteriormente, marque a caixa de seleção **usar a mesma senha como logon de cluster** . Caso contrário, forneça uma **senha** ou **chave pública** para autenticar o usuário SSH. Uma chave pública é a abordagem que recomendamos. Escolha **selecionar** na parte inferior para salvar a configuração de credenciais.
   
        Para obter mais informações, consulte [conectar-se ao HDInsight (Apache Hadoop) usando SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

    * Em **Grupo de recursos**, especifique se pretende criar um novo grupo de recursos ou utilizar um existente.

    * Especifique um **local** de datacenter em que o cluster é criado.

    * Selecione **Avançar** para ir para a próxima página.

1. De **2 segurança + rede**, você pode conectar o cluster a uma rede virtual usando o menu suspenso fornecido. Selecione uma rede virtual do Azure e a sub-rede se você quiser posicionar o cluster em uma rede virtual. Para obter informações sobre como usar o HDInsight com uma rede virtual, consulte [planejar uma implantação de rede virtual para clusters do Azure HDInsight](hdinsight-plan-virtual-network-deployment.md). O artigo inclui requisitos de configuração específicos para a rede virtual.

    Se você quiser usar o **Enterprise Security Package**, siga estas instruções: [configurar um cluster HDInsight com Enterprise Security Package usando Azure Active Directory Domain Services](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds).

    Selecione **Avançar** para ir para a próxima página.

1. De **3 armazenamento**, especifique se você deseja o armazenamento do Azure ou Azure data Lake Storage como seu armazenamento padrão. Para obter mais informações, consulte a tabela a seguir.

     ![HDInsight criar armazenamento de cluster](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-storage.png "Criando um novo cluster no portal do Azure")

     | Armazenamento                                      | Descrição |
     |----------------------------------------------|-------------|
     | **Blobs de armazenamento do Azure como o armazenamento padrão**   | <ul><li>Para **tipo de armazenamento primário**, selecione **armazenamento do Azure**. Para o **método de seleção**, escolha **minhas assinaturas** se desejar especificar uma conta de armazenamento que faça parte de sua assinatura do Azure. Em seguida, selecione a conta de armazenamento. Caso contrário, selecione **chave de acesso**. Em seguida, forneça as informações para a conta de armazenamento que você deseja escolher fora de sua assinatura do Azure.</li><li>Para o **contêiner padrão**, escolha o nome de contêiner padrão sugerido pelo portal ou especifique seu próprio.</li><li>Se o armazenamento de BLOBs do Azure for seu armazenamento padrão, você também poderá selecionar **contas de armazenamento adicionais** para especificar contas de armazenamento adicionais a serem associadas ao cluster. Para **chaves de armazenamento do Azure**, selecione **Adicionar uma chave de armazenamento**. Em seguida, você pode fornecer uma conta de armazenamento de suas assinaturas do Azure ou de outras assinaturas. Forneça a chave de acesso da conta de armazenamento.</li><li>Se o armazenamento de BLOBs for seu armazenamento padrão, você também poderá selecionar **Data Lake Storage acesso** para especificar Azure data Lake Storage como armazenamento adicional. Para obter mais informações, consulte [início rápido: configurar clusters no HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).</li></ul> |
     | **Azure Data Lake Storage como o armazenamento padrão** | Para **tipo de armazenamento primário**, selecione **Azure data Lake Storage Gen1** ou **Azure data Lake Storage Gen2**. Em seguida, consulte o artigo [início rápido: configurar clusters no HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md) para obter instruções. |
     | **Metastores externos**                      | Como opção, especifique um banco de dados SQL para salvar Apache Hive e os metadados do Apache Oozie associados ao cluster. Para **selecionar um banco de dados SQL para Hive**, selecione um banco de dados SQL. Em seguida, forneça o nome de usuário e a senha para o banco de dados. Repita essas etapas para os metadados do Oozie.<br><br>Algumas considerações sobre como usar o banco de dados SQL do Azure para metastores são as seguintes: <ul><li>O banco de dados SQL do Azure que é usado para o metastore deve permitir a conectividade com outros serviços do Azure, incluindo o Azure HDInsight. No lado direito do painel do banco de dados SQL do Azure, selecione o nome do servidor. Esse servidor é aquele em que a instância do banco de dados SQL é executada. Depois de estar na exibição do servidor, selecione **Configurar**. Em seguida, para os **Serviços do Azure**, selecione **Sim**. Em seguida, selecione **Guardar**.</li><li>Quando você cria um metastore, não nomeie um banco de dados com traços ou hifens. Esses caracteres podem causar falha no processo de criação do cluster.</li></ul> |

     > [!WARNING]  
     > Não há suporte para o uso de uma conta de armazenamento adicional em um local diferente do cluster HDInsight.

     Selecione **Avançar** para ir para a próxima página.

1. Em **4 aplicativos (opcional)** , selecione os aplicativos desejados. Microsoft, ISVs (fornecedores independentes de software) ou você pode desenvolver esses aplicativos. Para obter mais informações, consulte [instalar aplicativos durante a criação do cluster](hdinsight-apps-install-applications.md#install-applications-during-cluster-creation).

    Selecione **Avançar** para ir para a próxima página.

1. **5 o tamanho do cluster** exibe informações sobre os nós usados para esse cluster. Defina o número de nós de trabalho que você precisa para o cluster. O custo estimado da execução do cluster também é mostrado.

    ![Criar nós de cluster do HDInsight](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-nodes.png "Especificar o número de nós de cluster")

   > [!IMPORTANT]  
   > Se você planeja mais de 32 nós de trabalho, selecione um tamanho de nó de cabeçalho com pelo menos oito núcleos e 14 GB de RAM. Planeje os nós na criação do cluster ou dimensionando o cluster após a criação.
   >
   > Para obter mais informações sobre tamanhos de nós e custos associados, consulte [preços do Azure HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

    Selecione **Avançar** para ir para a próxima página.

1. Em **6 ações de script**, você pode personalizar um cluster para instalar componentes personalizados. Essa opção funcionará se você quiser usar um script personalizado para personalizar um cluster, pois o cluster está sendo criado. Para obter mais informações sobre as ações de script, consulte [Personalizar clusters HDInsight baseados em Linux usando ações de script](hdinsight-hadoop-customize-cluster-linux.md).

   Selecione **Avançar** para ir para a próxima página.

1. No **Resumo de 7**, verifique as informações que você inseriu anteriormente. Em seguida, selecione **Criar**.

     ![Resumo de criação de cluster do HDInsight](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-summary.png "Especificar o número de nós de cluster")
    
    > [!NOTE]  
    > A criação do cluster demora algum tempo; aproximadamente 20 minutos. Monitorar **notificações** para verificar o processo de provisionamento.

1. Após a conclusão do processo de criação, selecione **ir para o recurso** da notificação **implantação bem-sucedida** . A janela de cluster fornece as informações a seguir.

    ![Visão geral do cluster de portal do Azure de HDI](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-completed.png "Propriedades do cluster")

    Os ícones na janela são explicados da seguinte maneira:

    * A guia **visão geral** fornece todas as informações essenciais sobre o cluster. Os exemplos são o nome, o grupo de recursos ao qual ele pertence, o local, o sistema operacional e a URL para o painel do cluster.
    * O **painel** direciona você para o portal do Ambari associado ao cluster.
    * **Secure Shell** fornece as informações necessárias para acessar o cluster usando o SSH.
    * Usando **dimensionar cluster**, você pode aumentar o número de nós de trabalho associados ao cluster.
    * **Excluir** exclui o cluster HDInsight.

## <a name="customize-clusters"></a>Personalizar clusters
* [Personalizar os clusters HDInsight usando a inicialização](hdinsight-hadoop-customize-cluster-bootstrap.md)
* [Personalizar clusters HDInsight baseados em Linux usando ações de script](hdinsight-hadoop-customize-cluster-linux.md)

## <a name="delete-the-cluster"></a>Eliminar o cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Resolução de problemas

Caso se depare com problemas com a criação de clusters do HDInsight, veja [aceder aos requisitos de controlo](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="next-steps"></a>Passos seguintes

Você criou um cluster HDInsight com êxito. Agora, saiba como trabalhar com o cluster.

### <a name="apache-hadoop-clusters"></a>Clusters Apache Hadoop

* [Usar o Apache Hive com o HDInsight](hadoop/hdinsight-use-hive.md)
* [Usar o Apache Pig com o HDInsight](hadoop/hdinsight-use-pig.md)
* [Usar o MapReduce com o HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Clusters do Apache HBase

* [Introdução ao Apache HBase no HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Desenvolver aplicativos Java para o Apache HBase no HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Clusters Apache Storm

* [Desenvolver topologias Java para Apache Storm no HDInsight](storm/apache-storm-develop-java-topology.md)
* [Usar componentes do Python no Apache Storm no HDInsight](storm/apache-storm-develop-python-topology.md)
* [Implantar e monitorar topologias com Apache Storm no HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)

### <a name="apache-spark-clusters"></a>Clusters Apache Spark

* [Criar um aplicativo autônomo usando escalabilidade](spark/apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um cluster Apache Spark usando o Apache Livy](spark/apache-spark-livy-rest-interface.md)
* [Apache Spark com BI: executar análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](spark/apache-spark-use-bi-tools.md)
* [Apache Spark com Machine Learning: Use o Spark no HDInsight para prever os resultados da inspeção de alimentos](spark/apache-spark-machine-learning-mllib-ipython.md)
