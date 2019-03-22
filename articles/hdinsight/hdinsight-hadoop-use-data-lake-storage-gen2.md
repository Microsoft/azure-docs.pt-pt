---
title: Utilize a geração 2 de armazenamento do Azure Data Lake com clusters do HDInsight do Azure
description: Saiba como utilizar a geração 2 de armazenamento do Azure Data Lake com clusters do HDInsight do Azure.
services: hdinsight
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: hrasheed
ms.openlocfilehash: 45b34d12fbcecbf5f6bf1225c5bb82c5385224ed
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58338399"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>Utilize a geração 2 de armazenamento do Azure Data Lake com clusters do HDInsight do Azure

Geração de armazenamento 2 do Azure Data Lake é um serviço de armazenamento na cloud dedicado a análise de macrodados, criado no armazenamento de Blobs do Azure. Geração 2 de armazenamento do Data Lake combina os recursos de armazenamento de Blobs do Azure e de geração 1 de armazenamento do Azure Data Lake. O serviço resultante oferece recursos de geração 1 armazenamento do Azure Data Lake, por exemplo, a semântica de sistema de ficheiros, a segurança ao nível do diretório e ao nível do ficheiro e a escalabilidade, juntamente com o armazenamento em camadas e de baixo custo, elevada disponibilidade e capacidades de recuperação após desastre do armazenamento de Blobs do Azure.

## <a name="data-lake-storage-gen2-availability"></a>Disponibilidade de Lake Gen2 de armazenamento de dados

Geração 2 de armazenamento do Data Lake está disponível como uma opção de armazenamento para quase todos os tipos de cluster do Azure HDInsight como um padrão e uma conta de armazenamento adicional. No entanto, o HBase, pode ter apenas uma conta de geração 2 de armazenamento do Data Lake.

> [!Note] 
> Depois de selecionar geração 2 de armazenamento do Data Lake como sua **tipo de armazenamento primário**, não é possível selecionar uma conta de geração 1 de armazenamento do Data Lake como armazenamento adicional.

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-portal"></a>Criar um cluster com geração 2 de armazenamento do Data Lake através do portal do Azure

Para criar um cluster do HDInsight que utiliza a geração 2 de armazenamento do Data Lake para o armazenamento, siga estes passos para configurar uma conta de geração 2 de armazenamento do Data Lake.

### <a name="create-a-user-managed-identity"></a>Criar uma identidade de utilizador gerido

Crie uma identidade gerida atribuído ao utilizador, se ainda não tiver uma. Ver [Create, lista, delete ou o atribuir uma função para uma identidade gerida atribuído ao utilizador com o portal do Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity). Para obter mais informações sobre como geridos de identidades de trabalho no Azure HDInsight, consulte [geridos identidades no Azure HDInsight](hdinsight-managed-identities.md).

![Criar uma identidade gerida atribuída pelo utilizador](./media/hdinsight-hadoop-data-lake-storage-gen2/create-user-assigned-managed-identity-portal.png)

### <a name="create-a-data-lake-storage-gen2-account"></a>Criar uma conta de geração 2 de armazenamento do Data Lake

Crie uma conta de armazenamento de geração 2 de armazenamento do Azure Data Lake. Certifique-se de que o **espaço de nomes hierárquico** opção está ativada. Para obter mais informações, consulte [início rápido: Criar uma conta de armazenamento de geração 2 de armazenamento do Azure Data Lake](../storage/blobs/data-lake-storage-quickstart-create-account.md).

![Captura de ecrã que mostra a criação da conta de armazenamento no portal do Azure](./media/hdinsight-hadoop-data-lake-storage-gen2/azure-data-lake-storage-account-create-advanced.png)

### <a name="setup-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2-account"></a>Configurar permissões para a identidade gerida na conta de geração 2 de armazenamento do Data Lake

Atribuir a identidade gerida para o **proprietário de dados de Blob de armazenamento (pré-visualização)** função na conta de armazenamento. Para obter mais informações, consulte [gerir direitos de acesso aos dados de Blobs do Azure e a fila com o RBAC (pré-visualização)](../storage/common/storage-auth-aad-rbac.md).

1. Na [portal do Azure](https://portal.azure.com), aceda à sua conta de armazenamento.
1. Selecione a sua conta de armazenamento, em seguida, selecione **controlo de acesso (IAM)** para apresentar as definições de controlo de acesso para a conta. Selecione o **atribuições de funções** separador para ver a lista de atribuições de funções.
    
    ![Captura de ecrã que mostra as definições de controlo de acesso de armazenamento](./media/hdinsight-hadoop-data-lake-storage-gen2/portal-access-control.png)
    
1. Selecione o **+ adicionar atribuição de função** botão para adicionar uma nova função.
1. Na **adicionar atribuição de função** janela, selecione a **proprietário de dados de Blob de armazenamento (pré-visualização)** função. Em seguida, selecione a subscrição que tem a conta de armazenamento e de identidade gerida. Em seguida, procure para localizar a identidade de gerido atribuído ao utilizador que criou anteriormente. Por fim, selecione a identidade gerida e serão listada sob **membros selecionados**.
    
    ![Captura de ecrã que mostra como atribuir uma função RBAC](./media/hdinsight-hadoop-data-lake-storage-gen2/add-rbac-role3.png)
    
1. Selecione **Guardar**. A identidade de utilizador atribuída que selecionou é agora listada na função selecionada.
1. Depois de concluída a configuração inicial, pode criar um cluster através do portal. O cluster tem de estar na mesma região do Azure como a conta de armazenamento. Na **armazenamento** secção do menu de criação do cluster, selecione as seguintes opções:
        
    * Para **tipo de armazenamento primário**, selecione **Gen2 de armazenamento do Azure Data Lake**.
    * Sob **Selecione uma conta de armazenamento**, procure e selecione a conta de armazenamento de geração 2 de armazenamento do Data Lake recém-criado.
        
        ![Definições de armazenamento para a utilização de geração 2 de armazenamento do Data Lake com o Azure HDInsight](./media/hdinsight-hadoop-data-lake-storage-gen2/primary-storage-type-adls-gen2.png)
    
    * Sob **identidade**, selecione a subscrição correta e o recém-criado atribuído ao utilizador identidade gerida.
        
        ![Definições de identidade para a utilização de geração 2 de armazenamento do Data Lake com o Azure HDInsight](./media/hdinsight-hadoop-data-lake-storage-gen2/managed-identity-cluster-creation.png)
        
> [!Note]
> Pode adicionar uma ou mais contas de geração 2 de armazenamento do Data Lake como armazenamento secundário no mesmo cluster. Apenas os repetimos as etapas acima em cada conta de geração 2 de armazenamento do Data Lake que pretende adicionar a utilizar a mesma identidade gerida.

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-cli"></a>Criar um cluster com geração 2 de armazenamento do Data Lake através da CLI do Azure

Pode [transferir um ficheiro de modelo de exemplo](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/hdinsight-adls-gen2-template.json) e [transferir um ficheiro de parâmetros de exemplo](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/parameters.json). Antes de utilizar o modelo, substitua a cadeia de caracteres `<SUBSCRIPTION_ID>` com o ID de subscrição do Azure real. Além disso, substitua a cadeia de caracteres `<PASSWORD>` com a palavra-passe escolhida para definir tanto a palavra-passe que pretende utilizar para iniciar sessão no seu cluster e a palavra-passe SSH.

O trecho de código abaixo faz as seguintes etapas iniciais:

1. Registos na sua conta do Azure.
1. Define a subscrição ativa em que as operações criar serão feitas.
1. Cria um novo grupo de recursos para as novas atividades de implementação com o nome `hdinsight-deployment-rg`.
1. Cria uma identidade gerida atribuído ao utilizador com o nome `test-hdinsight-msi`.
1. Adiciona uma extensão para a CLI do Azure para utilizar as funcionalidades de geração 2 de armazenamento do Data Lake.
1. Cria uma nova conta de geração 2 de armazenamento do Data Lake com o nome `hdinsightadlsgen2`, utilizando o `--hierarchical-namespace true` sinalizador.

```azurecli
az login
az account set --subscription <subscription_id>

# Create resource group
az group create --name hdinsight-deployment-rg --location eastus

# Create managed identity
az identity create -g hdinsight-deployment-rg -n test-hdinsight-msi

az extension add --name storage-preview

az storage account create --name hdinsightadlsgen2 \
    --resource-group hdinsight-deployment-rg \
    --location eastus --sku Standard_LRS \
    --kind StorageV2 --hierarchical-namespace true
```

Em seguida, inicie sessão no portal. Adicionar a novo utilizador atribuído identidade gerida para o **contribuinte de dados de Blob de armazenamento (pré-visualização)** função na conta de armazenamento, conforme descrito no passo 3 sob [com o portal do Azure](hdinsight-hadoop-use-data-lake-storage-gen2.md).

Depois de atribuir a função para a identidade gerida atribuído ao utilizador, implemente o modelo ao utilizar o seguinte trecho de código.

```azurecli
az group deployment create --name HDInsightADLSGen2Deployment \
    --resource-group hdinsight-deployment-rg \
    --template-file hdinsight-adls-gen2-template.json \
    --parameters parameters.json
```

## <a name="access-control-for-data-lake-storage-gen2-in-hdinsight"></a>Controlo de acesso para a geração 2 de armazenamento do Data Lake no HDInsight

### <a name="what-kinds-of-permissions-does-data-lake-storage-gen2-support"></a>Os tipos de permissão suporta a geração 2 de armazenamento do Data Lake?

Geração 2 de armazenamento do Data Lake utiliza um modelo de controlo de acesso que suporte o controlo de acesso baseado em funções (RBAC) e listas de controlo de acesso POSIX semelhante ao (ACLs). Suporta de geração 1 de armazenamento do Data Lake acessar listas de controlo apenas para controlar o acesso aos dados.

RBAC utiliza as atribuições de funções para aplicar efetivamente os conjuntos de permissões a utilizadores, grupos e principais de serviço para recursos do Azure. Normalmente, esses recursos do Azure estão restritos a recursos de nível superior (por exemplo, contas de armazenamento do Azure). Para armazenamento do Azure e também de geração 2 de armazenamento do Data Lake, esse mecanismo foi expandido para o recurso de sistema de ficheiros.

 Para obter mais informações sobre as permissões de ficheiro com o RBAC, veja [controlo de acesso baseado em função do Azure (RBAC)](../storage/blobs/data-lake-storage-access-control.md#azure-role-based-access-control-rbac).

Para obter mais informações sobre as permissões de arquivo com ACLs, consulte [apresenta uma lista de controlo de acesso em arquivos e diretórios](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

### <a name="how-do-i-control-access-to-my-data-in-data-lake-storage-gen2"></a>Como controlar o acesso aos meus dados na geração 2 de armazenamento do Data Lake?

Capacidade do seu cluster HDInsight para aceder aos ficheiros na geração 2 de armazenamento do Data Lake é controlada através de identidades geridas. Uma identidade gerida é uma identidade registrada no Azure Active Directory (Azure AD) cujas credenciais são geridos pelo Azure. Com identidades geridas, não precisa de registar os principais de serviço no Azure AD ou manter as credenciais, tais como certificados.

Serviços do Azure têm dois tipos de identidades geridas: atribuído de sistema e atribuído ao utilizador. HDInsight utiliza identidades geridas atribuído ao utilizador para acesso de geração 2 de armazenamento do Data Lake. Uma identidade gerida atribuído ao utilizador é criada como uma autónoma recursos do Azure. Através de um processo de criação, o Azure cria uma identidade no inquilino do Azure AD no qual a subscrição que está a ser utilizada confia. Depois de criada, a identidade pode ser atribuída a uma ou mais instâncias do serviço do Azure.

O ciclo de vida das identidades atribuídas pelo utilizador é gerido separadamente do ciclo de vida das instâncias do serviço do Azure ao qual estão atribuídas. Para obter mais informações sobre identidades geridas, consulte [como fazer as identidades geridas para o trabalho de recursos do Azure?](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work).

### <a name="how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services"></a>Como definir as permissões para utilizadores do Azure AD para consultar dados na geração 2 de armazenamento do Data Lake ao utilizar o Hive ou outros serviços?

Para definir permissões dos utilizadores para consultar dados, utilize grupos de segurança do Azure AD como o principal atribuído nas ACLs. Não atribua diretamente o permissões de acesso aos ficheiros para utilizadores individuais ou principais de serviço. Quando utiliza grupos de segurança do Azure AD para controlar o fluxo de permissões, pode adicionar e remover utilizadores ou principais de serviço sem reaplicando ACLs para uma estrutura de diretório inteiro. Só tem de adicionar ou remover os utilizadores do apropriado grupo de segurança do Azure AD. ACLs não são herdadas, para que reaplicando ACLs requer a atualização a ACL em todos os ficheiros e o subdiretório.

## <a name="next-steps"></a>Passos Seguintes

* [Utilizar a pré-visualização do Azure Data Lake Storage Gen2 com clusters do HDInsight do Azure](../storage/blobs/data-lake-storage-use-hdi-cluster.md)
* [Atualizar a integração do Azure HDInsight com a pré-visualização de geração 2 de armazenamento do Data Lake - ACL e segurança](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/)
* [Introdução à pré-visualização do Azure Data Lake Storage geração 2](../storage/blobs/data-lake-storage-introduction.md)
