---
title: Criar clusters Apache Hadoop usando o navegador Web, Azure HDInsight
description: Aprenda a criar aglomerados Apache Hadoop, Apache HBase, Apache Storm ou Apache Spark em HDInsight. Use o navegador web e o portal Azure.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 08/06/2020
ms.openlocfilehash: 98d18044693bc6f4d23e04e9403e71686df39451
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104872205"
---
# <a name="create-linux-based-clusters-in-hdinsight-by-using-the-azure-portal"></a>Criar clusters baseados em Linux em HDInsight utilizando o portal Azure

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

O portal Azure é uma ferramenta de gestão baseada na web para serviços e recursos hospedados na nuvem Microsoft Azure. Neste artigo, aprende-se a criar clusters Azure HDInsight baseados em Linux utilizando o portal. Detalhes adicionais estão disponíveis a partir de [clusters Create HDInsight](./hdinsight-hadoop-provision-linux-clusters.md).

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

O portal Azure expõe a maioria das propriedades do cluster. Ao utilizar os modelos do Gestor de Recursos Azure, pode ocultar muitos detalhes. Para obter mais informações, consulte [crie clusters Apache Hadoop em HDInsight utilizando modelos de Gestor de Recursos](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-clusters"></a>Criar clusters

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. A partir do menu superior, selecione **+ Crie um recurso.**

    :::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-create-resource.png" alt-text="Criar um novo cluster no portal Azure":::

1. Selecione **Analytics**  >  **Azure HDInsight** para ir à página de **cluster Create HDInsight.**

## <a name="basics"></a>Noções básicas

:::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-basics.png" alt-text="HDInsight criar básicos de cluster":::

A partir do separador **Básicos,** forneça as seguintes informações:

|Propriedade |Descrição |
|---|---|
|Subscrição|A partir da lista de drop-down, selecione a subscrição Azure que é usada para o cluster.|
|Grupo de recursos|A partir da lista de drop-down, selecione o seu grupo de recursos existente ou selecione **Criar novo**.|
|Nome do cluster|Introduza um nome globalmente exclusivo.|
|Region|A partir da lista de drop-down, selecione uma região onde o cluster é criado.|
|Tipo de cluster|Clique **em Selecionar o tipo de cluster** para abrir uma lista. Na lista, selecione o tipo de cluster procurado. Os clusters HDInsight vêm em diferentes tipos. Correspondem à carga de trabalho ou à tecnologia para a qual o cluster está sintonizado. Não há um método suportado para criar um cluster que combine vários tipos.|
|Versão|A partir da lista de drop-down, selecione uma **versão**. Utilize a versão padrão se não souber o que escolher. Para obter mais informações, veja [HDInsight cluster versions (Versões de clusters HDInsight)](hdinsight-component-versioning.md).|
|Nome de utilizador de início de sessão do cluster|Forneça o nome de utilizador, o padrão é **administrador**.|
|Palavra-passe de início de sessão do cluster|Forneça a senha.|
|Confirme a senha de login do cluster|Reentre na senha|
|Nome de utilizador de Secure Shell (SSH)|Forneça o nome de utilizador, o padrão é **sshuser**|
|Use a palavra-passe de login do cluster para SSH|Se pretender a mesma palavra-passe SSH que a palavra-passe de administração especificada anteriormente, selecione a palavra-passe de login do cluster Utilizar para a caixa de verificação **SSH.** Caso contrário, forneça uma **PALAVRA-passe** ou **UMA CHAVE PÚBLICA** para autenticar o utilizador SSH. Uma chave pública é a abordagem que recomendamos. Escolha **Selecione** na parte inferior para guardar a configuração das credenciais.  Para obter mais informações, consulte [Connect to HDInsight (Apache Hadoop) utilizando SSH](hdinsight-hadoop-linux-use-ssh-unix.md).|

Selecione **Seguinte: >>de armazenamento** para avançar para o separador seguinte.

## <a name="storage"></a>Armazenamento

> [!WARNING] 
> A partir de 15 de junho de 2020 os clientes não poderão criar um novo diretor de serviços utilizando o HDInsight. Consulte [Criar O Título de Serviço e Certificados](../active-directory/develop/howto-create-service-principal-portal.md) utilizando o Diretório Ativo Azure.

:::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-storage.png" alt-text="HDInsight criar armazenamento de cluster":::

### <a name="primary-storage"></a>Armazenamento primário

A partir da lista de drop-down para **o tipo de armazenamento primário,** selecione o seu tipo de armazenamento predefinido. Os campos posteriores a completar variarão em função da sua seleção. Para **armazenamento Azure:**

1. Para **o método seleção**, escolha **a lista** ou use **a tecla de acesso**.
    * Para **Selecionar a partir da lista,** em seguida, selecione a sua conta de **armazenamento primário** na lista de drop-down ou selecione Criar **novo**.
    * Para **utilizar a tecla de acesso,** insira o nome da sua **conta de Armazenamento**. Em seguida, forneça a **chave de acesso**.

1. Para **o Contentor,** aceite o valor predefinido ou introduza um novo.

### <a name="additional-azure-storage"></a>Armazenamento adicional de Azure

Opcional: **Selecione Adicionar armazenamento Azure** para armazenamento adicional de cluster. A utilização de uma conta de armazenamento adicional numa região diferente do cluster HDInsight não é suportada.

### <a name="metastore-settings"></a>Configurações de Metasteiros

Opcional: Especifique uma base de dados SQL existente para salvar a Colmeia Apache, Apache Oozie e, ou os metadados Apache Ambari fora do cluster. A Base de Dados Azure SQL que é utilizada para a meta-loja deve permitir a conectividade a outros serviços Azure, incluindo Azure HDInsight. Quando criar uma meta-loja, não diga o nome de uma base de dados com traços ou hífens. Estes caracteres podem fazer com que o processo de criação do cluster falhe.

> [!IMPORTANT]
> Para formas de cluster que suportam metastões, a metastore predefinida fornece uma Base de Dados Azure SQL com um **limite básico de DTU de nível 5 (não atualizável)**! Adequado para fins básicos de teste. Para cargas de trabalho grandes ou de produção, recomendamos migrar para uma meta-loja externa.

Selecione **Seguinte: Segurança + rede >>** para avançar para o separador seguinte.

## <a name="security--networking"></a>Segurança + networking

:::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-security-networking.png" alt-text="HDInsight criar rede de segurança de cluster":::

A partir do separador **Segurança + rede,** forneça as seguintes informações:

|Propriedade |Descrição |
|---|---|
|Pacote de segurança da empresa|Opcional: Selecione a caixa de verificação para utilizar o **Pacote de Segurança Empresarial**. Para obter mais informações, consulte [configurar um cluster HDInsight com pacote de segurança empresarial utilizando os Serviços de Domínio do Diretório Ativo Azure](./domain-joined/apache-domain-joined-configure-using-azure-adds.md).|
|TLS|Opcional: Selecione uma versão TLS da lista de drop-down. Para mais informações, consulte [a Segurança da Camada de Transporte.](./transport-layer-security.md)|
|Rede virtual|Opcional: Selecione uma rede virtual existente e sub-rede da lista de drop-down. Para obter informações, consulte [Plan uma implementação de rede virtual para clusters Azure HDInsight](hdinsight-plan-virtual-network-deployment.md). O artigo inclui requisitos específicos de configuração para a rede virtual.|
|Definições de encriptação de discos|Opcional: Selecione a caixa de verificação para utilizar a encriptação. Para obter mais informações, consulte a [encriptação do disco de chaves gerida pelo Cliente](./disk-encryption.md).|
|Proxy REST do Kafka|Esta definição só está disponível para o tipo de cluster Kafka. Para obter mais informações, consulte [utilizando um representante do REST](./kafka/rest-proxy.md).|
|Identidade|Opcional: Selecione uma identidade de serviço atribuída ao utilizador existente na lista de suspensos. Para obter mais informações, consulte [identidades geridas no Azure HDInsight](./hdinsight-managed-identities.md).|

Selecione **Seguinte: Configuração + preços >>** avançar para o separador seguinte.

## <a name="configuration--pricing"></a>Configuração + preços

:::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-configuration.png" alt-text="HDInsight criar configuração de cluster":::

A partir do **separador Configuração + preços,** forneça as seguintes informações:

|Propriedade |Descrição |
|---|---|
|+ Adicionar aplicação|Opcional: Selecione quaisquer aplicações que pretenda. Microsoft, fornecedores independentes de software (ISVs), ou pode desenvolver estas aplicações. Para obter mais informações, consulte [instalar aplicações durante a criação do cluster.](hdinsight-apps-install-applications.md#install-applications-during-cluster-creation)|
|Tamanho do nó|Opcional: Selecione um nó de tamanho diferente.|
|Número de nós|Opcional: Introduza o número de nós para o tipo de nó especificado. Se planeia mais de 32 nós de trabalhadores, selecione um nó de cabeça com pelo menos oito núcleos e 14 GB de RAM. Planeie os nós na criação de clusters ou escalando o cluster após a criação.|
|Ativar a autoescala|Opcional: Selecione a caixa de verificação para ativar a função. Para obter mais informações, consulte [os clusters Azure HDInsight](./hdinsight-autoscale-clusters.md)de escala automática .|
|+ Adicionar ação de script|Opcional: Esta opção funciona se quiser usar um script personalizado para personalizar um cluster, uma vez que o cluster está a ser criado. Para obter mais informações sobre as ações de script, consulte [os clusters HDInsight baseados em Linux utilizando ações de script](hdinsight-hadoop-customize-cluster-linux.md).|

Selecione **Review + crie >>** para validar a configuração do cluster e avançar para o separador final.

## <a name="review--create"></a>Rever + criar

:::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-review-create-hadoop.png" alt-text="HDInsight criar resumo de cluster":::

Reveja as definições. Selecione **Criar** para criar o cluster.

A criação do cluster demora algum tempo; aproximadamente 20 minutos. **Monitorize notificações** para verificar o processo de provisionamento.

## <a name="post-creation"></a>Pós-criação

Após o fim do processo de criação, selecione **Ir para o Recurso** a partir da notificação de **implementação.** A janela do cluster fornece as seguintes informações.

:::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-completed.png" alt-text="Visão geral do cluster do portal HDI Azure":::

Alguns dos ícones da janela são explicados da seguinte forma:

|Propriedade | Descrição |
|---|---|
|Descrição geral|Fornece toda a informação essencial sobre o cluster. Exemplos são o nome, o grupo de recursos a que pertence, a localização, o sistema operativo e o URL para o painel de cluster.|
|Painéis de cluster|Direciona-o para o portal Ambari associado ao cluster.|
|SSH + Login de Cluster|Fornece informações necessárias para aceder ao cluster utilizando o SSH.|
|Eliminar|Elimina o cluster HDInsight.|

## <a name="delete-the-cluster"></a>Eliminar o cluster

Consulte [Eliminar um cluster HDInsight utilizando o seu navegador, PowerShell ou o Azure CLI](./hdinsight-delete-cluster.md).

## <a name="troubleshoot"></a>Resolução de problemas

Caso se depare com problemas com a criação de clusters do HDInsight, veja [aceder aos requisitos de controlo](./hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="next-steps"></a>Passos seguintes

Criou com sucesso um cluster HDInsight. Agora aprende a trabalhar com o teu grupo.

* [Use a Colmeia Apache com HDInsight](hadoop/hdinsight-use-hive.md)
* [Começar com Apache HBase em HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Personalize os clusters HDInsight baseados em Linux utilizando ações de script](hdinsight-hadoop-customize-cluster-linux.md)