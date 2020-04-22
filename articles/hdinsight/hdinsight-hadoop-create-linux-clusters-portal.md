---
title: Crie clusters Apache Hadoop usando o navegador web, Azure HDInsight
description: Aprenda a criar aglomerados Apache Hadoop, Apache HBase, Apache Storm ou Apache Spark no HDInsight. Utilize o navegador web e o portal Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/13/2020
ms.openlocfilehash: 88392e4e61c28d4fe821c8176242246d10e52b23
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770901"
---
# <a name="create-linux-based-clusters-in-hdinsight-by-using-the-azure-portal"></a>Crie clusters baseados em Linux no HDInsight utilizando o portal Azure

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

O portal Azure é uma ferramenta de gestão baseada na web para serviços e recursos hospedados na nuvem do Microsoft Azure. Neste artigo, aprende-se a criar clusters Azure HDInsight baseados em Linux utilizando o portal. Detalhes adicionais estão disponíveis a partir de [clusters Create HDInsight](./hdinsight-hadoop-provision-linux-clusters.md).

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

O portal Azure expõe a maioria das propriedades do cluster. Ao utilizar modelos de Gestor de Recursos Azure, pode ocultar muitos detalhes. Para obter mais informações, consulte [Create Apache Hadoop clusters in HDInsight utilizando modelos](hdinsight-hadoop-create-linux-clusters-arm-templates.md)de Gestor de Recursos .

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-clusters"></a>Criar clusters

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. A partir do menu superior, selecione **+ Criar um recurso**.

    ![Criar um novo cluster no portal Azure](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-create-resource.png "Criação de um novo cluster no portal Azure")

1. Selecione **Analytics** > **Azure HDInsight** para ir à página de **cluster Create HDInsight.**

## <a name="basics"></a>Noções básicas

![HDInsight criar básicos de cluster](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-basics.png "Criação de um novo cluster no portal Azure")

A partir do separador **Basics,** forneça as seguintes informações:

|Propriedade |Descrição |
|---|---|
|Subscrição|A partir da lista de lançamentos, selecione a subscrição Azure que é usada para o cluster.|
|Grupo de recursos|A partir da lista de drop-down, selecione o seu grupo de recursos existente, ou selecione **Criar novo**.|
|Nome do cluster|Introduza um nome globalmente exclusivo.|
|Região|A partir da lista de abandono, selecione uma região onde o cluster é criado.|
|Tipo de cluster|Clique em **Selecionar o tipo de cluster** para abrir uma lista. A partir da lista, selecione o tipo de cluster procurado. Os clusters HDInsight vêm em diferentes tipos. Correspondem à carga de trabalho ou à tecnologia para a que o cluster está sintonizado. Não há nenhum método suportado para criar um cluster que combine vários tipos.|
|Versão|A partir da lista de lançamentos, selecione uma **versão**. Utilize a versão predefinida se não souber o que escolher. Para obter mais informações, veja [HDInsight cluster versions (Versões de clusters HDInsight)](hdinsight-component-versioning.md).|
|Nome de utilizador de início de sessão do cluster|Forneça o nome de utilizador, o predefinido é **o administrador**.|
|Palavra-passe de início de sessão do cluster|Forneça a senha.|
|Confirmar senha de login do cluster|Reintroduza a palavra-passe|
|Nome de utilizador de Secure Shell (SSH)|Forneça o nome de utilizador, o padrão é **sshuser**|
|Utilize senha de login de cluster para SSH|Se pretender a mesma senha SSH que a palavra-passe de administração especificada anteriormente, selecione a palavra-passe de login do cluster Use para a caixa de verificação **SSH.** Caso contrário, forneça uma **senha** ou **uma CHAVE PÚBLICA** para autenticar o utilizador SSH. Uma chave pública é a abordagem que recomendamos. Escolha **Selecione** na parte inferior para guardar a configuração das credenciais.  Para mais informações, consulte [Connect to HDInsight (Apache Hadoop) utilizando o SSH](hdinsight-hadoop-linux-use-ssh-unix.md).|

Selecione **Seguinte: Armazenamento >>** para avançar para o separador seguinte.

## <a name="storage"></a>Armazenamento

![HDInsight cria armazenamento de cluster](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-storage.png "Criação de um novo cluster no portal Azure - armazenamento")

### <a name="primary-storage"></a>Armazenamento primário

A partir da lista de lançamentos para **o tipo de armazenamento primário,** selecione o seu tipo de armazenamento predefinido. Os campos posteriores a completar variarão em função da sua seleção. Para **armazenamento azure:**

1. Para **o método de seleção,** escolha **selecione na lista,** ou **utilize a tecla de acesso**.
    * Para **selecionar a partir da lista,** então selecione a sua conta de armazenamento **Primária** na lista de drop-down, ou selecione **Criar nova**.
    * Para **utilizar a tecla de acesso,** introduza o nome da sua conta de **armazenamento**. Em seguida, forneça a **tecla Acesso**.

1. Para **o Recipiente,** aceite o valor predefinido ou introduza um novo.

### <a name="additional-azure-storage"></a>Armazenamento Adicional azure

Opcional: **Selecione Adicionar armazenamento Azure** para armazenamento adicional de cluster. A utilização de uma conta de armazenamento adicional numa região diferente do cluster HDInsight não é suportada.

### <a name="metastore-settings"></a>Definições de metaloja

Opcional: Especifique uma base de dados SQL existente para salvar apache hive, Apache Oozie, e, ou metadados Apache Ambari fora do cluster. A Base de Dados Azure SQL que é utilizada para a metaloja deve permitir a conectividade com outros serviços Azure, incluindo o Azure HDInsight. Quando criar uma metaloja, não nomeie uma base de dados com traços ou hífenes. Estes caracteres podem fazer com que o processo de criação do cluster falhe.

Selecione **Seguinte: Segurança + rede >>** para avançar para o separador seguinte.

## <a name="security--networking"></a>Segurança + networking

![HDInsight cria networking de segurança de cluster](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-security-networking.png "HDInsight cria networking de segurança de cluster")

A partir do separador **De Segurança + networking,** forneça as seguintes informações:

|Propriedade |Descrição |
|---|---|
|Pacote de segurança empresarial|Opcional: Selecione a caixa de verificação para utilizar o Pacote de **Segurança Empresarial**. Para mais informações, consulte [configurar um cluster HDInsight com pacote de segurança empresarial utilizando os Serviços de Domínio de Diretório Ativo Azure](./domain-joined/apache-domain-joined-configure-using-azure-adds.md).|
|TLS|Opcional: Selecione uma versão TLS da lista de lançamentos. Para mais informações, consulte [Transport Layer Security](./transport-layer-security.md).|
|Rede virtual|Opcional: Selecione uma rede virtual existente e sub-rede da lista de lançamentos. Para obter informações, consulte [Plan uma implementação de rede virtual para clusters Azure HDInsight](hdinsight-plan-virtual-network-deployment.md). O artigo inclui requisitos específicos de configuração para a rede virtual.|
|Definições de encriptação do disco|Opcional: Selecione a caixa de verificação para utilizar encriptação. Para obter mais informações, consulte a [encriptação](./disk-encryption.md)do disco de chave gerida pelo Cliente .|
|Proxy REST do Kafka|Esta definição só está disponível para o tipo de cluster Kafka. Para mais informações, consulte [Utilizar um proxy REST](./kafka/rest-proxy.md).|
|Identidade|Opcional: Selecione uma identidade de serviço atribuída ao utilizador existente na lista de lançamentos. Para mais informações, consulte [identidades geridas no Azure HDInsight](./hdinsight-managed-identities.md).|

Selecione **Seguinte: Configuração + >>de preços** para avançar para o separador seguinte.

## <a name="configuration--pricing"></a>Configuração + preços

![HDInsight criar configuração de cluster](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-configuration.png "Separador de configuração e preços")

A partir do separador **de preços Configuração +,** forneça as seguintes informações:

|Propriedade |Descrição |
|---|---|
|+ Adicionar aplicação|Opcional: Selecione quaisquer aplicações que pretenda. Microsoft, fornecedores de software independentes (ISVs), ou pode desenvolver estas aplicações. Para mais informações, consulte [Instalar aplicações durante](hdinsight-apps-install-applications.md#install-applications-during-cluster-creation)a criação do cluster .|
|Tamanho do nó|Opcional: Selecione um nó de tamanho diferente.|
|Número de nós|Opcional: Insira o número de nós para o tipo de nó especificado. Se planeia mais de 32 nós de trabalhador, selecione um tamanho de nó de cabeça com pelo menos oito núcleos e RAM de 14 GB. Planeie os nós na criação de clusterou, quer escalando o cluster após a criação.|
|Ativar a escala automática|Opcional: Selecione a caixa de verificação para ativar a funcionalidade. Para mais informações, consulte automaticamente a escala dos [clusters Azure HDInsight](./hdinsight-autoscale-clusters.md).|
|+ Adicionar ação de script|Opcional: Esta opção funciona se quiser usar um script personalizado para personalizar um cluster, uma vez que o cluster está a ser criado. Para obter mais informações sobre as ações do script, consulte os [clusters HDInsight baseados em Linux utilizando ações](hdinsight-hadoop-customize-cluster-linux.md)de script .|

Selecione **Review + crie >>** para validar a configuração do cluster e avançar para o separador final.

## <a name="review--create"></a>Rever + criar

![HDInsight cria resumo de cluster](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-review-create-hadoop.png "Especificar número de nós de cluster")

Reveja as definições. Selecione **Criar** para criar o cluster.

A criação do cluster demora algum tempo; aproximadamente 20 minutos. Monitorize **notificações** para verificar o processo de provisionamento.

## <a name="post-creation"></a>Pós-criação

Após o processo de criação terminar, selecione **Go to Resource** from the Deployment **conseguiu** a notificação. A janela do cluster fornece as seguintes informações.

![Visão geral do cluster do portal HDI Azure](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-completed.png "Propriedades de cluster")

Alguns dos ícones da janela são explicados da seguinte forma:

|Propriedade | Descrição |
|---|---|
|Descrição geral|Fornece todas as informações essenciais sobre o cluster. Exemplos são o nome, o grupo de recursos a que pertence, a localização, o sistema operativo e o URL para o painel de cluster.|
|Dashboards de cluster|Direciona-o para o portal Ambari associado ao cluster.|
|SSH + Login cluster|Fornece informações necessárias para aceder ao cluster utilizando o SSH.|
|Eliminar|Elimina o cluster HDInsight.|

## <a name="customize-clusters"></a>Personalizar clusters

* [Personalize os clusters HDInsight usando bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md)
* [Personalize os clusters HDInsight baseados em Linux usando ações de script](hdinsight-hadoop-customize-cluster-linux.md)

## <a name="delete-the-cluster"></a>Eliminar o cluster

Consulte [eliminar um cluster HDInsight utilizando o seu navegador, PowerShell ou o Azure CLI](./hdinsight-delete-cluster.md).

## <a name="troubleshoot"></a>Resolução de problemas

Caso se depare com problemas com a criação de clusters do HDInsight, veja [aceder aos requisitos de controlo](./hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="next-steps"></a>Passos seguintes

Criou com sucesso um cluster HDInsight. Agora aprende a trabalhar com o teu agrupamento.

* [Use a Colmeia Apache com HDInsight](hadoop/hdinsight-use-hive.md)
* [Começa com Apache HBase no HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Desenvolva topoologias java para tempestade Apache no HDInsight](storm/apache-storm-develop-java-topology.md)
* [Criar uma aplicação autónoma usando o Scala](spark/apache-spark-create-standalone-application.md)
