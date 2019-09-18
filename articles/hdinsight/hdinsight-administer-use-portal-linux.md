---
title: Gerenciar clusters Apache Hadoop no HDInsight usando portal do Azure
description: Saiba como criar e gerenciar clusters do Azure HDInsight usando o portal do Azure.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/06/2019
ms.author: hrasheed
ms.openlocfilehash: dfe531f8b298be1d90df45f57918f87744912949
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71077065"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-the-azure-portal"></a>Gerenciar clusters de Apache Hadoop no HDInsight usando o portal do Azure

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Usando o [portal do Azure](https://portal.azure.com), você pode gerenciar [Apache Hadoop](https://hadoop.apache.org/) clusters no Azure HDInsight. Use o seletor de guias acima para obter informações sobre como gerenciar clusters Hadoop no HDInsight usando outras ferramentas.

## <a name="prerequisites"></a>Pré-requisitos

Um cluster Apache Hadoop existente no HDInsight.  Consulte [Criar clusters baseados em Linux no HDInsight usando o portal do Azure](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="getting-started"></a>Introdução
Inicie sessão em [https://portal.azure.com](https://portal.azure.com).

## <a name="showClusters"></a>Listar e mostrar clusters
A página **clusters HDInsight** listará os clusters existentes.  No Portal:
1. Selecione **todos os serviços** no menu à esquerda.
2. Selecione **clusters HDInsight** em **análise**.

## <a name="homePage"></a>home page de cluster 
Selecione o nome do cluster na página [**clusters HDInsight**](#showClusters) .  Isso abrirá o modo de exibição **visão geral** , que é semelhante à imagem a seguir:

![Conceitos básicos do portal do Azure cluster HDInsight](./media/hdinsight-administer-use-portal-linux/hdinsight-essentials2.png)

**Menu superior:**  

| Item| Descrição |
|---|---|
|Mover|Move o cluster para outro grupo de recursos ou para outra assinatura.|
|Eliminar|Exclui o cluster. |
|Atualizar|Atualiza a exibição.|

**Menu à esquerda:**  
  - **Menu superior esquerdo**

    | Item| Descrição |
    |---|---|
    |Descrição geral|Fornece informações gerais para o cluster.|
    |Registo de atividades|Mostrar e consultar logs de atividades.|
    |Controle de acesso (IAM)|Use atribuições de função.  Consulte [usar atribuições de função para gerenciar o acesso aos recursos de assinatura do Azure](../role-based-access-control/role-assignments-portal.md).|
    |Tags|Permite definir pares de chave/valor para definir uma taxonomia personalizada de seus serviços de nuvem. Por exemplo, você pode criar uma chave chamada **projeto**e, em seguida, usar um valor comum para todos os serviços associados a um projeto específico.|
    |Diagnosticar e resolver problemas|Exibir informações de solução de problemas.|
    |Início rápido|Exibe informações que ajudam você a começar a usar o HDInsight.|
    |Ferramentas|Informações de ajuda para as ferramentas relacionadas ao HDInsight.|

  - **Menu de definições**  

    | Item| Descrição |
    |---|---|
    |Tamanho do cluster|Verifique, aumente e diminua o número de nós de trabalho do cluster. Consulte [dimensionar clusters](hdinsight-administer-use-portal-linux.md#scale-clusters).|
    |Limites de quota|Exiba os núcleos usados e disponíveis para sua assinatura.|
    |SSH + logon no cluster|Mostra as instruções para se conectar ao cluster usando a conexão Secure Shell (SSH). Para obter mais informações, veja [Utilizar SSH com o HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).|
    |Armazenamento do Data Lake Ger1|Configurar o Data Lake Storage Gen1 de acesso.  Consulte [início rápido: Configurar clusters no HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).|
    |Contas de armazenamento|Exiba as contas de armazenamento e as chaves. As contas de armazenamento são configuradas durante o processo de criação do cluster.|
    |Aplicações|Adicionar/remover aplicativos do HDInsight.  Consulte [instalar aplicativos personalizados do HDInsight](hdinsight-apps-install-custom-applications.md).|
    |Ações do script|Execute scripts bash no cluster. Consulte [Personalizar clusters HDInsight baseados em Linux usando a ação de script](hdinsight-hadoop-customize-cluster-linux.md).|
    |Metastores externas|Exiba os metarepositórios [Apache Hive](https://hive.apache.org/) e [Apache Oozie](https://oozie.apache.org/) . Os metastores só podem ser configurados durante o processo de criação do cluster.|
    |Parceiro do HDInsight|Adicionar/remover o parceiro HDInsight atual.|
    |properties|Exiba as [Propriedades do cluster](#properties).|
    |Bloqueios|Adicione um bloqueio para impedir que o cluster seja modificado ou excluído.|
    |Exportar modelo|Exibir e exportar o modelo de Azure Resource Manager para o cluster. No momento, você só pode exportar a conta de armazenamento do Azure dependente. Consulte [Criar clusters de Apache Hadoop baseados em Linux no HDInsight usando modelos de Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).|

  - **Menu monitoramento**

    | Item| Descrição |
    |---|---|
    |Alertas|Gerencie os alertas e as ações.|
    |Métricas|Monitore as métricas de cluster em logs de Azure Monitor.|
    |Configurações de diagnóstico|Configurações de onde armazenar as métricas de diagnóstico.|
    |Operations Management Suite|Monitore seu cluster no Azure Operations Management Suite (OMS) e logs de Azure Monitor.|

  - **Suporte + menu de solução de problemas**

    | Item| Descrição |
    |---|---|
    |Estado de funcionamento dos recursos|Consulte [visão geral do Azure Resource Health](../service-health/resource-health-overview.md).|
    |Novo pedido de suporte|Permite que você crie um tíquete de suporte com o suporte da Microsoft.|

## <a name="properties"></a>Propriedades do cluster

No [Home Page de cluster](#homePage), em **configurações** , selecione **Propriedades**.

|Item | Descrição |
|---|---|
|NOME DO ANFITRIÃO|Nome do cluster.|
|URL DO CLUSTER|A URL para a interface da Web Ambari.|
|Ponto Final Privado|O ponto de extremidade privado para o cluster.|
|SSH (Secure Shell)|O nome de usuário e de host a ser usado para acessar o cluster via SSH.|
|ESTADO|Um dos: Abortado, aceito, ClusterStorageProvisioned, AzureVMConfiguration, HDInsightConfiguration, operacional, em execução, erro, exclusão, excluído, TimedOut, DeleteQueued, DeleteTimedout, DeleteError, PatchQueued, CertRolloverQueued, ResizeQueued ou ClusterCustomization.|
|REGIÃO|Local do Azure. Para obter uma lista de locais do Azure com suporte, consulte a caixa de listagem suspensa **região** no [preço do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).|
|DATA DE CRIAÇÃO|A data em que o cluster foi implantado.|
|SISTEMA OPERATIVO|**Windows** ou **Linux**.|
|TIPO|Hadoop, HBase, Storm, Spark.|
|Version|Consulte [versões do HDInsight](hdinsight-component-versioning.md).|
|SUBSCRIÇÃO|Nome da assinatura.|
|FONTE DE DADOS PADRÃO|O sistema de arquivos de cluster padrão.|
|Tamanhos dos nós de trabalho|O tamanho de VM selecionado dos nós de trabalho.|
|Tamanho do nó de cabeçalho|O tamanho de VM selecionado dos nós de cabeçalho.|
|Rede virtual|O nome da rede virtual, em que o cluster é implantado, se um tiver sido selecionado no momento da implantação.|

## <a name="move-clusters"></a>Mover clusters

Você pode mover um cluster HDInsight para outro grupo de recursos do Azure ou outra assinatura.

No [Home Page do cluster](#homePage):

1. Selecione **mover** no menu superior.
2. Selecione **mover para outro grupo de recursos** ou **mover para outra assinatura**.
3. Siga as instruções da nova página.

## <a name="delete-clusters"></a>Excluir clusters
A exclusão de um cluster não exclui a conta de armazenamento padrão nem as contas de armazenamento vinculadas. Você pode recriar o cluster usando as mesmas contas de armazenamento e os mesmos metarepositórios. É recomendável usar um novo contêiner de blob padrão quando você recria o cluster.

No [Home Page do cluster](#homePage):

1. Selecione **excluir** no menu superior.
2. Siga as instruções da nova página.

Consulte também [Pausar/desligar clusters](#pauseshut-down-clusters).

## <a name="add-additional-storage-accounts"></a>Adicionar mais contas de armazenamento

Você pode adicionar contas de armazenamento do Azure e Azure Data Lake Storage contas adicionais depois que um cluster é criado. Para obter mais informações, veja [Adicionar mais contas de armazenamento ao HDInsight](./hdinsight-hadoop-add-storage.md).

## <a name="scale-clusters"></a>Dimensionar clusters

O recurso de dimensionamento de cluster permite que você altere o número de nós de trabalho usados por um cluster HDInsight do Azure, sem precisar recriar o cluster.

Consulte [dimensionar clusters HDInsight](./hdinsight-scaling-best-practices.md) para obter informações completas.

## <a name="pauseshut-down-clusters"></a>Pausar/desligar clusters

A maioria dos trabalhos do Hadoop são trabalhos em lotes que só são executados ocasionalmente. Para a maioria dos clusters do Hadoop, há grandes períodos de tempo em que o cluster não está sendo usado para processamento. Com o HDInsight, os dados são armazenados no Storage do Azure, pelo que pode eliminar um cluster em segurança quando este não está a ser utilizado.
Também lhe é cobrado o valor de um cluster do HDInsight mesmo quando não o está a utilizar. Uma vez que os custos do cluster são muito superiores aos custos do armazenamento, faz sentido do ponto de vista económico eliminar os clusters quando não estiverem a ser utilizados.

Há várias maneiras de programar o processo:

* Azure Data Factory do usuário. Consulte [Criar clusters de Apache Hadoop baseados em Linux sob demanda no HDInsight usando Azure data Factory](hdinsight-hadoop-create-linux-clusters-adf.md) para criar serviços vinculados do HDInsight sob demanda.
* Use Azure PowerShell.  Consulte [analisar dados de atraso de voo](./interactive-query/interactive-query-tutorial-analyze-flight-data.md).
* Use CLI do Azure. Consulte [gerenciar clusters do Azure HDInsight usando o CLI do Azure](hdinsight-administer-use-command-line.md).
* Use o SDK do .NET do HDInsight. Consulte [enviar trabalhos de Apache Hadoop](hadoop/submit-apache-hadoop-jobs-programmatically.md).

Para obter informações sobre preços, consulte [preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/). Para excluir um cluster do portal, consulte [excluir clusters](#delete-clusters)

## <a name="upgrade-clusters"></a>Atualizar clusters

Consulte [atualizar o cluster HDInsight para uma versão mais recente](./hdinsight-upgrade-cluster.md).

## <a name="open-the-apache-ambari-web-ui"></a>Abra a interface do usuário da Web do Apache Ambari

O Ambari fornece uma interface do usuário da Web de gerenciamento do Hadoop intuitiva e fácil de usar apoiada por suas APIs RESTful. O Ambari permite que os administradores de sistema gerenciem e monitorem clusters Hadoop.

No [Home Page do cluster](#homePage):

1. Selecione **painéis de cluster**.

    ![Menu de cluster do HDInsight Apache Hadoop](./media/hdinsight-administer-use-portal-linux/hdinsight-azure-portal-cluster-menu2.png)

1. Selecione **Ambari Home** na página novo.
1. Insira o nome de usuário e a senha do cluster.  O nome de usuário do cluster padrão é _admin_.

Para obter mais informações, consulte [gerenciar clusters HDInsight usando a interface do usuário do Apache AmAmbari Web](hdinsight-hadoop-manage-ambari.md).

## <a name="change-passwords"></a>Alterar senhas
HDInsight An cluster pode ter duas contas de usuário. A conta de usuário do cluster HDInsight (conta de usuário HTTP) e a conta de usuário SSH são criadas durante o processo de criação. Você pode usar o portal para alterar a senha da conta de usuário do cluster e as ações de script para alterar a conta de usuário do SSH.

### <a name="change-the-cluster-user-password"></a>Alterar a senha de usuário do cluster

> [!NOTE]  
> Alterar a senha de usuário do cluster (admin) pode fazer com que as ações de script executadas nesse cluster falhem. Se você tiver ações de script persistentes que destinam-se a nós de trabalho, esses scripts poderão falhar quando você adicionar nós ao cluster por meio de operações de redimensionamento. Para obter mais informações sobre as ações de script, consulte [Personalizar clusters HDInsight usando ações de script](hdinsight-hadoop-customize-cluster-linux.md).

No [Home Page do cluster](#homePage):
1. Selecione **SSH + logon de cluster** em **configurações**.
2. Selecione **Redefinir credencial**.
3. Insira e confirme a nova senha nas caixas de texto.
4. Selecione **OK**.

A senha é alterada em todos os nós no cluster.

### <a name="change-the-ssh-user-password"></a>Alterar a senha de usuário do SSH
1. Usando um editor de texto, salve o texto a seguir como um arquivo chamado **ChangePassword.sh**.

    > [!IMPORTANT]  
    > Você deve usar um editor que usa LF como a terminação de linha. Se o editor usar CRLF, o script não funcionará.

    ```bash
    #! /bin/bash
    USER=$1
    PASS=$2
    usermod --password $(echo $PASS | openssl passwd -1 -stdin) $USER
    ```

2. Carregue o arquivo em um local de armazenamento que possa ser acessado do HDInsight usando um endereço HTTP ou HTTPS. Por exemplo, um armazenamento de arquivo público, como o OneDrive ou o armazenamento de BLOBs do Azure. Salve o URI (endereço HTTP ou HTTPS) no arquivo, pois esse URI é necessário na próxima etapa.
3. No [Home Page de cluster](#homePage), selecione **ações de script** em **configurações**.
4. Na página **ações de script** , selecione **Enviar novo**.
5. Na página **Enviar ação de script** , insira as seguintes informações:

   | Campo | Value |
   | --- | --- |
   | Tipo de script | Selecione **-personalizado** na lista suspensa.|
   | Name |"Alterar senha ssh" |
   | URI do script de bash |O URI para o arquivo changepassword.sh |
   | Tipo (s) de nó: (Cabeçalho, trabalho, Nimbus, supervisor, Zookeeper, etc.) |✓ para todos os tipos de nó listados |
   | Parâmetros |Insira o nome de usuário SSH e, em seguida, a nova senha. Deve haver um espaço entre o nome de usuário e a senha. |
   | Persistir esta ação de script... |Deixe esse campo desmarcado. |

6. Selecione **criar** para aplicar o script. Quando o script for concluído, você poderá se conectar ao cluster usando SSH com a nova senha.

## <a name="grantrevoke-access"></a>Conceder/revogar acesso
Os clusters HDInsight têm os seguintes serviços Web HTTP (todos esses serviços têm pontos de extremidade RESTful):

* ODBC
* JDBC
* Ambari
* Oozie
* Templeton

Por padrão, esses serviços são concedidos para acesso. Você pode revogar/conceder o acesso usando [Azure PowerShell](hdinsight-administer-use-powershell.md#grantrevoke-access).

## <a name="find-the-subscription-id"></a>Localizar a ID da assinatura
Cada cluster está vinculado a uma assinatura do Azure.  A ID da assinatura do Azure é visível no [Home Page do cluster](#homePage).

## <a name="find-the-resource-group"></a>Localizar o grupo de recursos
No modo de Azure Resource Manager, cada cluster HDInsight é criado com um grupo de Azure Resource Manager. O grupo do Gerenciador de recursos é visível no [Home Page do cluster](#homePage).

## <a name="find-the-storage-accounts"></a>Localizar as contas de armazenamento
Os clusters HDInsight usam uma conta de armazenamento do Azure ou Azure Data Lake Storage para armazenar dados. Cada cluster HDInsight pode ter uma conta de armazenamento padrão e um número de contas de armazenamento vinculadas. Para listar as contas de armazenamento, no [Home Page de cluster](#homePage) em **configurações**, selecione contas de **armazenamento**.

## <a name="monitor-jobs"></a>Monitorizar trabalhos
Consulte [gerenciar clusters HDInsight usando a interface do usuário do Apache AmAmbari Web](hdinsight-hadoop-manage-ambari.md#monitoring).

## <a name="cluster-size"></a>Tamanho do cluster

O bloco **tamanho do cluster** do [Home Page de cluster](#homePage) exibe o número de núcleos alocados para esse cluster e como eles são alocados para os nós nesse cluster.

> [!IMPORTANT]  
> Para monitorar os serviços fornecidos pelo cluster HDInsight, você deve usar o Ambari Web ou a API REST do Ambari. Para obter mais informações sobre como usar o Ambari, consulte [gerenciar clusters HDInsight usando o Apache Ambari](hdinsight-hadoop-manage-ambari.md)

## <a name="connect-to-a-cluster"></a>Ligar a um cluster

* [Usar o Apache Hive com o HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md)
* [Utilizar SSH com o HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, você aprendeu algumas funções administrativas básicas. Para saber mais, confira os seguintes artigos:

* [Administrar o HDInsight usando o Azure PowerShell](hdinsight-administer-use-powershell.md)
* [Administrar o HDInsight usando o CLI do Azure](hdinsight-administer-use-command-line.md)
* [Criar clusters HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
* [Detalhes sobre como usar a API REST do Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md)
* [Usar Apache Hive no HDInsight](hadoop/hdinsight-use-hive.md)
* [Usar o Apache Sqoop no HDInsight](hadoop/hdinsight-use-sqoop.md)
* [Usar funções definidas pelo usuário do Python (UDF) com Apache Hive e Apache Pig no HDInsight](hadoop/python-udf-hdinsight.md)
* [Qual versão do Apache Hadoop está no Azure HDInsight?](hdinsight-component-versioning.md)