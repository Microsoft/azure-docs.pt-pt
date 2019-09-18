---
title: Usar Azure Data Lake Storage Gen2 com clusters do Azure HDInsight
description: Saiba como usar Azure Data Lake Storage Gen2 com clusters do Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 08/27/2019
ms.author: hrasheed
ms.openlocfilehash: bd459998568287db767e98a2094d6f80a126ccde
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71077118"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>Usar Azure Data Lake Storage Gen2 com clusters do Azure HDInsight

Azure Data Lake Storage Gen2 é um serviço de armazenamento em nuvem dedicado a Big Data Analytics, criado no armazenamento de BLOBs do Azure. Data Lake Storage Gen2 combina os recursos do armazenamento de BLOBs do Azure e Azure Data Lake Storage Gen1. O serviço resultante oferece recursos de Azure Data Lake Storage Gen1, como semântica do sistema de arquivos, segurança em nível de diretório e de arquivo, e escalabilidade, juntamente com os recursos de armazenamento em camadas de baixo custo, de alta disponibilidade e de recuperação de desastres do armazenamento de BLOBs do Azure.

## <a name="data-lake-storage-gen2-availability"></a>Disponibilidade de Data Lake Storage Gen2

Data Lake Storage Gen2 está disponível como uma opção de armazenamento para quase todos os tipos de cluster do Azure HDInsight como um padrão e uma conta de armazenamento adicional. No entanto, o HBase pode ter apenas uma conta de Data Lake Storage Gen2.

Para obter uma comparação completa das opções de criação de cluster usando Data Lake Storage Gen2, consulte [comparar as opções de armazenamento para uso com clusters do Azure HDInsight](hdinsight-hadoop-compare-storage-options.md).

> [!Note]  
> Depois de selecionar Data Lake Storage Gen2 como seu **tipo de armazenamento primário**, você não poderá selecionar uma conta de data Lake Storage Gen1 como armazenamento adicional.

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-portal"></a>Criar um cluster com Data Lake Storage Gen2 por meio do portal do Azure

Para criar um cluster HDInsight que usa Data Lake Storage Gen2 para armazenamento, siga estas etapas para configurar uma conta de Data Lake Storage Gen2.

### <a name="create-a-user-assigned-managed-identity"></a>Criar uma identidade gerida atribuída pelo utilizador

Crie uma identidade gerenciada atribuída pelo usuário, se você ainda não tiver uma. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. No canto superior esquerdo, **crie um recurso**.
1. Na caixa de pesquisa, digite **usuário atribuído** e clique em **identidade gerenciada atribuída pelo usuário**.
1. Clique em **Criar**.
1. Insira um nome para sua identidade gerenciada, selecione a assinatura, o grupo de recursos e o local corretos.
1. Clique em **Criar**.

Para obter mais informações sobre como as identidades gerenciadas funcionam no Azure HDInsight, consulte [identidades gerenciadas no Azure hdinsight](hdinsight-managed-identities.md).

![Criar uma identidade gerida atribuída pelo utilizador](./media/hdinsight-hadoop-use-data-lake-storage-gen2/create-user-assigned-managed-identity-portal.png)

### <a name="create-a-data-lake-storage-gen2-account"></a>Criar uma conta de Data Lake Storage Gen2

Crie uma conta de armazenamento Azure Data Lake Storage Gen2. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. No canto superior esquerdo, **crie um recurso**.
1. Na caixa de pesquisa, digite **armazenamento** e clique em **conta de armazenamento**.
1. Clique em **Criar**.
1. Na tela **criar conta de armazenamento** :
    1. Selecione a assinatura e o grupo de recursos corretos.
    1. Insira um nome para sua conta de Data Lake Storage Gen2. Para obter mais informações sobre as convenções de nomenclatura da conta de armazenamento, consulte [convenções de nomenclatura para recursos do Azure](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions#storage).
    1. Clique na guia **avançado** .
    1. Clique em **habilitado** ao lado de **namespace hierárquico** em **Data Lake Storage Gen2**.
    1. Clique em **Rever + criar**.
    1. Clique em **Criar**

Para obter mais informações sobre outras opções durante a criação da conta [de armazenamento, consulte início rápido: Crie uma conta](../storage/blobs/data-lake-storage-quickstart-create-account.md)de armazenamento Azure data Lake Storage Gen2.

![Captura de tela mostrando a criação da conta de armazenamento no portal do Azure](./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-data-lake-storage-account-create-advanced.png)

### <a name="set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2-account"></a>Configurar permissões para a identidade gerenciada na conta de Data Lake Storage Gen2

Atribua a identidade gerenciada à função de **proprietário de dados do blob de armazenamento** na conta de armazenamento.

1. Na [portal do Azure](https://portal.azure.com), vá para sua conta de armazenamento.
1. Selecione sua conta de armazenamento e, em seguida, selecione **controle de acesso (iam)** para exibir as configurações de controle de acesso para a conta. Selecione a guia **atribuições de função** para ver a lista de atribuições de função.
    
    ![Captura de tela mostrando as configurações de controle de acesso de armazenamento](./media/hdinsight-hadoop-use-data-lake-storage-gen2/portal-access-control.png)
    
1. Selecione o botão **+ Adicionar atribuição de função** para adicionar uma nova função.
1. Na janela **Adicionar atribuição de função** , selecione a função **proprietário de dados do blob de armazenamento** . Em seguida, selecione a assinatura que tem a identidade gerenciada e a conta de armazenamento. Em seguida, pesquise para localizar a identidade gerenciada atribuída pelo usuário que você criou anteriormente. Por fim, selecione a identidade gerenciada e ela será listada em **Membros selecionados**.
    
    ![Captura de tela mostrando como atribuir uma função de RBAC](./media/hdinsight-hadoop-use-data-lake-storage-gen2/add-rbac-role3-window.png)
    
1. Selecione **Guardar**. A identidade atribuída pelo usuário que você selecionou agora está listada na função selecionada.
1. Depois que essa configuração inicial for concluída, você poderá criar um cluster por meio do Portal. O cluster deve estar na mesma região do Azure que a conta de armazenamento. Na seção **armazenamento** do menu de criação de cluster, selecione as seguintes opções:
        
    * Para **tipo de armazenamento primário**, selecione **Azure data Lake Storage Gen2**.
    * Em **selecionar uma conta de armazenamento**, procure e selecione a conta de armazenamento de data Lake Storage Gen2 recém-criada.
        
        ![Configurações de armazenamento para usar o Data Lake Storage Gen2 com o Azure HDInsight](./media/hdinsight-hadoop-use-data-lake-storage-gen2/primary-storage-type-adls-gen2.png)
    
    * Em **identidade**, selecione a assinatura correta e a identidade gerenciada atribuída pelo usuário recém-criada.
        
        ![Configurações de identidade para usar o Data Lake Storage Gen2 com o Azure HDInsight](./media/hdinsight-hadoop-use-data-lake-storage-gen2/managed-identity-cluster-creation.png)
        
> [!Note]
> Para adicionar uma conta de Data Lake Storage Gen2 secundária, no nível da conta de armazenamento, basta atribuir a identidade gerenciada criada anteriormente à nova conta de armazenamento de Data Lake Storage Gen2 que você deseja adicionar. Lembre-se de que não há suporte para a adição de uma conta de Data Lake Storage Gen2 secundária por meio da folha "contas de armazenamento adicionais" no HDInsight. 

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-cli"></a>Criar um cluster com Data Lake Storage Gen2 por meio do CLI do Azure

Você pode [baixar um arquivo de modelo de exemplo](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/hdinsight-adls-gen2-template.json) e [baixar um arquivo de parâmetros de exemplo](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/parameters.json). Antes de usar o modelo e o trecho de código de CLI do Azure abaixo, substitua os seguintes espaços reservados pelos seus valores corretos:

| Marcador de posição | Descrição |
|---|---|
| `<SUBSCRIPTION_ID>` | A ID da sua assinatura do Azure |
| `<RESOURCEGROUPNAME>` | O grupo de recursos em que você deseja criar o novo cluster e a conta de armazenamento. |
| `<MANAGEDIDENTITYNAME>` | O nome da identidade gerenciada que receberá permissões em sua conta de Azure Data Lake Storage Gen2. |
| `<STORAGEACCOUNTNAME>` | A nova conta de Azure Data Lake Storage Gen2 que será criada. |
| `<CLUSTERNAME>` | O nome do cluster HDInsight. |
| `<PASSWORD>` | Sua senha escolhida para entrar no cluster usando SSH, bem como o painel do Ambari. |

O trecho de código abaixo faz as seguintes etapas iniciais:

1. Faz logon em sua conta do Azure.
1. Define a assinatura ativa em que as operações de criação serão feitas.
1. Cria um novo grupo de recursos para as novas atividades de implantação. 
1. Cria uma identidade gerenciada atribuída pelo usuário.
1. Adiciona uma extensão ao CLI do Azure para usar recursos para Data Lake Storage Gen2.
1. Cria uma nova conta de data Lake Storage Gen2 usando o `--hierarchical-namespace true` sinalizador. 

```azurecli
az login
az account set --subscription <SUBSCRIPTION_ID>

# Create resource group
az group create --name <RESOURCEGROUPNAME> --location eastus

# Create managed identity
az identity create -g <RESOURCEGROUPNAME> -n <MANAGEDIDENTITYNAME>

az extension add --name storage-preview

az storage account create --name <STORAGEACCOUNTNAME> \
    --resource-group <RESOURCEGROUPNAME> \
    --location eastus --sku Standard_LRS \
    --kind StorageV2 --hierarchical-namespace true
```

Em seguida, entre no Portal. Adicione a nova identidade gerenciada atribuída pelo usuário à função **colaborador de dados do blob de armazenamento** na conta de armazenamento, conforme descrito na etapa 3 em [usando o portal do Azure](hdinsight-hadoop-use-data-lake-storage-gen2.md).

Depois de atribuir a função para a identidade gerenciada atribuída pelo usuário, implante o modelo usando o trecho de código a seguir.

```azurecli
az group deployment create --name HDInsightADLSGen2Deployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file hdinsight-adls-gen2-template.json \
    --parameters parameters.json
```

## <a name="access-control-for-data-lake-storage-gen2-in-hdinsight"></a>Controle de acesso para Data Lake Storage Gen2 no HDInsight

### <a name="what-kinds-of-permissions-does-data-lake-storage-gen2-support"></a>A quais tipos de permissões Data Lake Storage Gen2 suporte?

O Data Lake Storage Gen2 usa um modelo de controle de acesso que dá suporte a RBAC (controle de acesso baseado em função) e ACLs (listas de controle de acesso) semelhantes a POSIX. Data Lake Storage Gen1 dá suporte a listas de controle de acesso somente para controlar o acesso aos dados.

O RBAC usa atribuições de função para aplicar efetivamente conjuntos de permissões a usuários, grupos e entidades de serviço para recursos do Azure. Normalmente, esses recursos do Azure são restritos a recursos de nível superior (por exemplo, contas de armazenamento do Azure). Para o armazenamento do Azure, e também Data Lake Storage Gen2, esse mecanismo foi estendido para o recurso do sistema de arquivos.

 Para obter mais informações sobre permissões de arquivo com o RBAC, consulte [controle de acesso baseado em função do Azure (RBAC)](../storage/blobs/data-lake-storage-access-control.md#azure-role-based-access-control-rbac).

Para obter mais informações sobre permissões de arquivo com ACLs, consulte [listas de controle de acesso em arquivos e diretórios](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

### <a name="how-do-i-control-access-to-my-data-in-data-lake-storage-gen2"></a>Como fazer controlar o acesso aos meus dados no Data Lake Storage Gen2?

A capacidade do cluster HDInsight de acessar arquivos no Data Lake Storage Gen2 é controlada por meio de identidades gerenciadas. Uma identidade gerenciada é uma identidade registrada no Azure Active Directory (AD do Azure) cujas credenciais são gerenciadas pelo Azure. Com identidades gerenciadas, você não precisa registrar entidades de serviço no Azure AD ou manter credenciais como certificados.

Os serviços do Azure têm dois tipos de identidades gerenciadas: atribuído pelo sistema e atribuído pelo usuário. O HDInsight usa identidades gerenciadas atribuídas pelo usuário para acessar Data Lake Storage Gen2. Uma identidade gerenciada atribuída pelo usuário é criada como um recurso autônomo do Azure. Através de um processo de criação, o Azure cria uma identidade no inquilino do Azure AD no qual a subscrição que está a ser utilizada confia. Depois de criada, a identidade pode ser atribuída a uma ou mais instâncias do serviço do Azure.

O ciclo de vida das identidades atribuídas pelo utilizador é gerido separadamente do ciclo de vida das instâncias do serviço do Azure ao qual estão atribuídas. Para obter mais informações sobre identidades gerenciadas, consulte [como funcionam as identidades gerenciadas dos recursos do Azure?](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work).

### <a name="how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services"></a>Como fazer definir permissões para que os usuários do Azure AD consultem dados em Data Lake Storage Gen2 usando o hive ou outros serviços?

Para definir permissões para os usuários consultarem dados, use os grupos de segurança do Azure AD como a entidade atribuída em ACLs. Não atribua permissões de acesso de arquivo diretamente a usuários individuais ou a entidades de serviço. Ao usar grupos de segurança do Azure AD para controlar o fluxo de permissões, você pode adicionar e remover usuários ou entidades de serviço sem reaplicar ACLs a uma estrutura de diretório inteira. Você só precisa adicionar ou remover os usuários do grupo de segurança apropriado do Azure AD. As ACLs não são herdadas, portanto, reaplicar ACLs requer a atualização da ACL em todos os arquivos e subdiretórios.

## <a name="next-steps"></a>Passos seguintes

* [Integração do Azure HDInsight com Data Lake Storage Gen2 Preview-ACL e atualização de segurança](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/)
* [Introdução ao Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
