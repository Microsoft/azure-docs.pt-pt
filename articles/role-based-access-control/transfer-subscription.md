---
title: Transfira uma subscrição do Azure para um diretório AD Azure diferente
description: Saiba como transferir uma subscrição do Azure e recursos relacionados conhecidos para um diretório Azure Ative (Azure AD) diferente.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.workload: identity
ms.date: 12/10/2020
ms.author: rolyon
ms.openlocfilehash: 5a4be6052e72c27ad83b5af64f1acb3ad8d4e3be
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100555898"
---
# <a name="transfer-an-azure-subscription-to-a-different-azure-ad-directory"></a>Transfira uma subscrição do Azure para um diretório AD Azure diferente

As organizações podem ter várias assinaturas Azure. Cada subscrição está associada a um diretório azure ative (Azure AD). Para facilitar a gestão, pode querer transferir uma subscrição para um diretório AD Azure diferente. Quando transfere uma subscrição para um diretório AD Azure diferente, alguns recursos não são transferidos para o diretório alvo. Por exemplo, todas as atribuições de funções e funções personalizadas no controlo de acesso baseado em funções Azure (Azure RBAC) são **permanentemente** eliminadas do diretório de origem e não são transferidas para o directório-alvo.

Este artigo descreve os passos básicos que pode seguir para transferir uma subscrição para um diretório AD Azure diferente e recriar alguns dos recursos após a transferência.

> [!NOTE]
> Para as subscrições Azure Cloud Solution Providers (CSP), a alteração do diretório AZure AD para a subscrição não é suportada.

## <a name="overview"></a>Descrição geral

Transferir uma subscrição do Azure para um diretório AD Azure diferente é um processo complexo que deve ser cuidadosamente planeado e executado. Muitos serviços da Azure exigem que os princípios de segurança (identidades) operem normalmente ou mesmo gerem outros recursos da Azure. Este artigo tenta cobrir a maioria dos serviços da Azure que dependem fortemente dos princípios de segurança, mas não é abrangente.

> [!IMPORTANT]
> Em alguns cenários, a transferência de uma subscrição pode requerer tempo de inatividade para concluir o processo. É necessário um planeamento cuidadoso para avaliar se será necessário tempo de inatividade para a sua transferência.

O diagrama que se segue mostra os passos básicos que deve seguir quando transfere uma subscrição para um diretório diferente.

1. Preparar para a transferência

1. Transferir a assinatura do Azure para um diretório diferente

1. Recriar recursos no directório-alvo, tais como atribuições de funções, funções personalizadas e identidades geridas

    ![Diagrama de subscrição de transferência](./media/transfer-subscription/transfer-subscription.png)

### <a name="deciding-whether-to-transfer-a-subscription-to-a-different-directory"></a>Decidir se transfere uma subscrição para um diretório diferente

Seguem-se algumas razões pelas quais poderá querer transferir uma subscrição:

- Devido a uma fusão ou aquisição da empresa, pretende gerir uma subscrição adquirida no seu diretório principal da Azure AD.
- Alguém na sua organização criou uma subscrição e quer consolidar a gestão para um determinado diretório AD Azure.
- Tem aplicações que dependem de um determinado ID ou URL de subscrição e não é fácil modificar a configuração ou código da aplicação.
- Uma parte do seu negócio foi dividida numa empresa separada e precisa de transferir alguns dos seus recursos para um diretório AD Azure diferente.
- Você quer gerir alguns dos seus recursos em um diretório AD Azure diferente para fins de isolamento de segurança.

### <a name="alternate-approaches"></a>Abordagens alternativas

A transferência de uma subscrição requer tempo de inatividade para concluir o processo. Dependendo do seu cenário, pode considerar as seguintes abordagens alternativas:

- Re-crie os recursos e copie os dados para o diretório e subscrição alvo.
- Adotar uma arquitetura multi-directório e deixar a subscrição no diretório de origem. Utilize o Farol Azure para delegar recursos para que os utilizadores no directório-alvo possam aceder à subscrição no diretório de origem. Para mais informações, consulte [o Farol Azure em cenários empresariais.](../lighthouse/concepts/enterprise.md)

### <a name="understand-the-impact-of-transferring-a-subscription"></a>Compreender o impacto da transferência de uma subscrição

Vários recursos Azure têm uma dependência de uma subscrição ou de um diretório. Dependendo da sua situação, a tabela que se segue enumera o impacto conhecido da transferência de uma subscrição. Ao executar os passos neste artigo, pode recriar alguns dos recursos que existiam antes da transferência de subscrição.

> [!IMPORTANT]
> Esta secção lista os serviços ou recursos conhecidos da Azure que dependem da sua subscrição. Como os tipos de recursos em Azure estão em constante evolução, pode haver dependências adicionais não listadas aqui que podem causar uma mudança de rutura no seu ambiente. 

| Serviço ou recurso | Impactado | Recuperável | Foi atingido? | O que pode fazer |
| --------- | --------- | --------- | --------- | --------- |
| Atribuições de funções | Yes | Yes | [Listar atribuições de função](#save-all-role-assignments) | Todas as atribuições de funções são permanentemente eliminadas. Deve mapear utilizadores, grupos e princípios de serviço para objetos correspondentes no diretório alvo. Tens de recriar as atribuições de papéis. |
| Funções personalizadas | Yes | Yes | [Listar funções personalizadas](#save-custom-roles) | Todas as funções personalizadas são permanentemente eliminadas. Deve recriar as funções personalizadas e quaisquer atribuições de papéis. |
| Identidades geridas atribuídas pelo sistema | Yes | Yes | [Lista de identidades geridas](#list-role-assignments-for-managed-identities) | Deve desativar e reativar as identidades geridas. Tens de recriar as atribuições de papéis. |
| Identidades geridas atribuídas pelo utilizador | Yes | Yes | [Lista de identidades geridas](#list-role-assignments-for-managed-identities) | Deve eliminar, recriar e anexar as identidades geridas ao recurso apropriado. Tens de recriar as atribuições de papéis. |
| Azure Key Vault | Yes | Yes | [Políticas de acesso ao cofre de chaves de lista](#list-key-vaults) | Tem de atualizar a identificação do inquilino associada aos cofres das chaves. Tem de remover e adicionar novas políticas de acesso. |
| Bases de dados Azure SQL com integração de autenticação AD AZure habilitados | Yes | No | [Consulte as bases de dados do Azure SQL com a autenticação AZure AD](#list-azure-sql-databases-with-azure-ad-authentication) |  |  |
| Azure Storage e Azure Data Lake Storage Gen2 | Yes | Yes |  | Tens de recriar quaisquer ACLs. |
| Azure Data Lake Storage Gen1 | Sim | Yes |  | Tens de recriar quaisquer ACLs. |
| Ficheiros do Azure | Yes | Yes |  | Tens de recriar quaisquer ACLs. |
| Azure File Sync | Yes | Yes |  |  |
| Managed Disks do Azure | Yes | Yes |  |  Se estiver a utilizar os Conjuntos de Encriptação do Disco para encriptar Discos Geridos com teclas geridas pelo cliente, tem de desativar e reativar as identidades atribuídas ao sistema associadas aos Conjuntos de Encriptação do Disco. E deve recriar as atribuições de funções, ou seja, conceder novamente as permissões necessárias aos Conjuntos de Encriptação de Discos nos Cofres-Chave. |
| Azure Kubernetes Service | Yes | Yes |  |  |
| Azure Policy | Yes | No | Todos os objetos da Política Azure, incluindo definições personalizadas, atribuições, isenções e dados de conformidade. | Deve [exportar,](../governance/policy/how-to/export-resources.md)importar e reatribuir definições. Em seguida, criar novas atribuições políticas e quaisquer [isenções políticas necessárias.](../governance/policy/concepts/exemption-structure.md) |
| Azure Active Directory Domain Services | Yes | No |  |  |
| Registos de aplicações | Yes | Yes |  |  |

> [!WARNING]
> Se estiver a usar encriptação em repouso para um recurso, como uma conta de armazenamento ou uma base de dados SQL, que tenha uma dependência de um cofre chave que **não** esteja na mesma subscrição que está a ser transferida, pode levar a um cenário irrecuperável. Se tiver esta situação, deve tomar medidas para utilizar um cofre de chave diferente ou desativar temporariamente as chaves geridas pelo cliente para evitar este cenário irrecuperável.

## <a name="prerequisites"></a>Pré-requisitos

Para completar estes passos, você precisará:

- [Bash em Azure Cloud Shell](../cloud-shell/overview.md) ou [Azure CLI](/cli/azure)
- Administrador de conta da subscrição que pretende transferir no diretório de origem
- [Papel do proprietário](built-in-roles.md#owner) no diretório-alvo

## <a name="step-1-prepare-for-the-transfer"></a>Passo 1: Preparar a transferência

### <a name="sign-in-to-source-directory"></a>Inscreva-se no diretório de origem

1. Inscreva-se no Azure como administrador.

1. Obtenha uma lista das suas subscrições com o comando [da lista de conta az.](/cli/azure/account#az_account_list)

    ```azurecli
    az account list --output table
    ```

1. Utilize [a conta az definida](/cli/azure/account#az_account_set) para definir a subscrição ativa que pretende transferir.

    ```azurecli
    az account set --subscription "Marketing"
    ```

### <a name="install-the-azure-resource-graph-extension"></a>Instale a extensão do Gráfico de Recursos Azure

 A extensão Azure CLI para [Azure Resource Graph](../governance/resource-graph/index.yml), *gráfico de recursos, permite-lhe* utilizar o comando [azgraph](/cli/azure/ext/resource-graph/graph) para consultar os recursos geridos pelo Azure Resource Manager. Usará este comando em passos posteriores.

1. Utilize [a lista de extensões az](/cli/azure/extension#az_extension_list) para ver se tem a extensão de gráfico de *recursos* instalada.

    ```azurecli
    az extension list
    ```

1. Caso contrário, instale a extensão *do gráfico de recurso.*

    ```azurecli
    az extension add --name resource-graph
    ```

### <a name="save-all-role-assignments"></a>Salvar todas as atribuições de funções

1. Utilize [a lista de atribuições de funções az](/cli/azure/role/assignment#az_role_assignment_list) para listar todas as atribuições de funções (incluindo atribuições de funções herdadas).

    Para facilitar a revisão da lista, pode exportar a produção como JSON, TSV ou tabela. Para obter mais informações, consulte [as atribuições de funções da Lista utilizando a Azure RBAC e a Azure CLI](role-assignments-list-cli.md).

    ```azurecli
    az role assignment list --all --include-inherited --output json > roleassignments.json
    az role assignment list --all --include-inherited --output tsv > roleassignments.tsv
    az role assignment list --all --include-inherited --output table > roleassignments.txt
    ```

1. Guarde a lista de atribuições de papéis.

    Ao transferir uma subscrição, todas as atribuições de funções são **permanentemente** eliminadas, pelo que é importante guardar uma cópia.

1. Reveja a lista de atribuições de papéis. Pode haver atribuições de papéis que não vai precisar no diretório alvo.

### <a name="save-custom-roles"></a>Guardar papéis personalizados

1. Utilize a [lista de definições de funções az](/cli/azure/role/definition#az_role_definition_list) para listar as suas funções personalizadas. Para obter mais informações, consulte [Criar ou atualizar as funções personalizadas Azure utilizando o Azure CLI](custom-roles-cli.md).

    ```azurecli
    az role definition list --custom-role-only true --output json --query '[].{roleName:roleName, roleType:roleType}'
    ```

1. Guarde cada papel personalizado que necessitará no directório-alvo como um ficheiro JSON separado.

    ```azurecli
    az role definition list --name <custom_role_name> > customrolename.json
    ```

1. Faça cópias dos ficheiros de funções personalizados.

1. Modifique cada cópia para utilizar o seguinte formato.

    Usará estes ficheiros mais tarde para recriar as funções personalizadas no diretório alvo.

    ```json
    {
      "Name": "",
      "Description": "",
      "Actions": [],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": []
    }
    ```

### <a name="determine-user-group-and-service-principal-mappings"></a>Determine mapeamentos principais do utilizador, grupo e serviço

1. Com base na sua lista de atribuições de funções, determine os utilizadores, grupos e diretores de serviço para os quais irá mapear no directório-alvo.

    Você pode identificar o tipo de diretor olhando para a `principalType` propriedade em cada atribuição de funções.

1. Se necessário, no directório-alvo, crie quaisquer utilizadores, grupos ou principais de serviço que necessite.

### <a name="list-role-assignments-for-managed-identities"></a>Listar atribuições de funções para identidades geridas

As identidades geridas não são atualizadas quando uma subscrição é transferida para outro diretório. Como resultado, quaisquer identidades geridas atribuídas pelo sistema ou atribuídas pelo utilizador serão quebradas. Após a transferência, pode voltar a ativar quaisquer identidades geridas atribuídas pelo sistema. Para identidades geridas atribuídas pelo utilizador, terá de reexame e anexá-las no directório-alvo.

1. Reveja a [lista de serviços Azure que suportam identidades geridas](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md) para notar onde pode estar a usar identidades geridas.

1. Utilize [a lista de anúncios ad sp](/cli/azure/ad/sp#az_ad_sp_list) para listar as identidades geridas atribuídas pelo sistema e atribuídas ao utilizador.

    ```azurecli
    az ad sp list --all --filter "servicePrincipalType eq 'ManagedIdentity'"
    ```

1. Na lista de identidades geridas, determine quais são atribuídas ao sistema e que são atribuídas pelo utilizador. Pode utilizar os seguintes critérios para determinar o tipo.

    | Critérios | Tipo de identidade gerido |
    | --- | --- |
    | `alternativeNames` propriedade inclui `isExplicit=False` | Afetado pelo sistema |
    | `alternativeNames` propriedade não inclui `isExplicit` | Afetado pelo sistema |
    | `alternativeNames` propriedade inclui `isExplicit=True` | Atribuído pelo utilizador |

    Também pode utilizar [a lista de identidades az](/cli/azure/identity#az_identity_list) para listar apenas identidades geridas atribuídas pelo utilizador. Para obter mais informações, consulte [Criar, listar ou eliminar uma identidade gerida atribuída pelo utilizador utilizando o CLI Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md).

    ```azurecli
    az identity list
    ```

1. Obtenha uma lista dos `objectId` valores das suas identidades geridas.

1. Procure na sua lista de atribuições de funções para ver se existem atribuições de papel para as suas identidades geridas.

### <a name="list-key-vaults"></a>Lista de cofres-chave

Quando cria um cofre chave, está automaticamente ligado ao ID do inquilino Azure Ative Directory predefinido para a subscrição em que é criado. Todas as entradas de política de acesso também estão associadas a este ID de inquilino. Para obter mais informações, consulte [movendo um Cofre de Chaves Azure para outra subscrição](../key-vault/general/move-subscription.md).

> [!WARNING]
> Se estiver a usar encriptação em repouso para um recurso, como uma conta de armazenamento ou uma base de dados SQL, que tenha uma dependência de um cofre chave que **não** esteja na mesma subscrição que está a ser transferida, pode levar a um cenário irrecuperável. Se tiver esta situação, deve tomar medidas para utilizar um cofre de chave diferente ou desativar temporariamente as chaves geridas pelo cliente para evitar este cenário irrecuperável.

- Se tiver um cofre chave, use [o show az keyvault](/cli/azure/keyvault#az_keyvault_show) para listar as políticas de acesso. Para obter mais informações, consulte [a política de acesso a um cofre de chaves.](../key-vault/general/assign-access-policy-cli.md)

    ```azurecli
    az keyvault show --name MyKeyVault
    ```

### <a name="list-azure-sql-databases-with-azure-ad-authentication"></a>Lista de bases de dados Azure SQL com autenticação AD Azure

- Utilize [a lista de ad-admin do servidor az sql](/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_list) e a extensão de gráfico [az](/cli/azure/ext/resource-graph/graph) para ver se está a utilizar bases de dados Azure SQL com integração de autenticação AD AD ativada. Para mais informações, consulte [Configure e gerencie a autenticação do Azure Ative Directory com SQL](../azure-sql/database/authentication-aad-configure.md).

    ```azurecli
    az sql server ad-admin list --ids $(az graph query -q 'resources | where type == "microsoft.sql/servers" | project id' -o tsv | cut -f1)
    ```

### <a name="list-acls"></a>Lista DE ACLs

1. Se estiver a utilizar o Azure Data Lake Storage Gen1, liste os ACLs que são aplicados a qualquer ficheiro utilizando o portal Azure ou o PowerShell.

1. Se estiver a utilizar o Azure Data Lake Storage Gen2, liste os ACLs que são aplicados a qualquer ficheiro utilizando o portal Azure ou o PowerShell.

1. Se estiver a utilizar ficheiros Azure, liste os ACLs que são aplicados a qualquer ficheiro.

### <a name="list-other-known-resources"></a>Listar outros recursos conhecidos

1. Use [o programa de conta az](/cli/azure/account#az_account_show) para obter o seu ID de subscrição.

    ```azurecli
    subscriptionId=$(az account show --query id | sed -e 's/^"//' -e 's/"$//')
    ```

1. Utilize a extensão de [gráfico az](/cli/azure/ext/resource-graph/graph) para listar outros recursos Azure com dependências conhecidas do diretório Azure.

    ```azurecli
    az graph query -q \
    'resources | where type != "microsoft.azureactivedirectory/b2cdirectories" | where  identity <> "" or properties.tenantId <> "" or properties.encryptionSettingsCollection.enabled == true | project name, type, kind, identity, tenantId, properties.tenantId' \
    --subscriptions $subscriptionId --output table
    ```

## <a name="step-2-transfer-the-subscription"></a>Passo 2: Transferir a subscrição

Neste passo, transfere a subscrição do diretório de origem para o diretório alvo. Os passos serão diferentes dependendo se você também quer transferir a propriedade da faturação.

> [!WARNING]
> Ao transferir a subscrição, todas as atribuições de funções no diretório de origem são **permanentemente** eliminadas e não podem ser restauradas. Não pode voltar uma vez que transfere a subscrição. Certifique-se de que completa os passos anteriores antes de realizar este passo.

1. Determine se também pretende transferir a propriedade da faturação para outra conta.

1. Transfira a subscrição para um diretório diferente.

    - Se quiser manter a propriedade de faturação atual, siga os passos no [Associado ou adicione uma subscrição Azure ao seu inquilino Azure Ative Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).
    - Se também pretender transferir a propriedade da faturação, siga os passos na [propriedade de faturação de transferência de uma subscrição do Azure para outra conta.](../cost-management-billing/manage/billing-subscription-transfer.md) Para transferir a subscrição para um diretório diferente, você deve verificar a caixa de verificação do **inquilino Azure Ad subscrição.**

1. Assim que terminar a transferência da subscrição, volte a este artigo para recriar os recursos no directório-alvo.

## <a name="step-3-re-create-resources"></a>Passo 3: Recriar recursos

### <a name="sign-in-to-target-directory"></a>Inscreva-se no diretório de destino

1. No directório-alvo, inscreva-se como o utilizador que aceitou o pedido de transferência.

    Apenas o utilizador da nova conta que aceitou o pedido de transferência terá acesso à gestão dos recursos.

1. Obtenha uma lista das suas subscrições com o comando [da lista de conta az.](/cli/azure/account#az_account_list)

    ```azurecli
    az account list --output table
    ```

1. Utilize [a conta az definida](/cli/azure/account#az_account_set) para definir a subscrição ativa que pretende utilizar.

    ```azurecli
    az account set --subscription "Contoso"
    ```

### <a name="create-custom-roles"></a>Criar funções personalizadas
        
- Use [a definição de função az criar](/cli/azure/role/definition#az_role_definition_create) para criar cada papel personalizado a partir dos ficheiros que criou anteriormente. Para obter mais informações, consulte [Criar ou atualizar as funções personalizadas Azure utilizando o Azure CLI](custom-roles-cli.md).

    ```azurecli
    az role definition create --role-definition <role_definition>
    ```

### <a name="assign-roles"></a>Atribuir funções

- Use [a az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) para atribuir funções a utilizadores, grupos e diretores de serviço. Para obter mais informações, consulte [atribuir funções Azure utilizando O Azure CLI](role-assignments-cli.md).

    ```azurecli
    az role assignment create --role <role_name_or_id> --assignee <assignee> --resource-group <resource_group>
    ```

### <a name="update-system-assigned-managed-identities"></a>Atualizar identidades geridas atribuídas pelo sistema

1. Desativar e reativar identidades geridas atribuídas pelo sistema.

    | Serviço do Azure | Mais informações | 
    | --- | --- |
    | Máquinas virtuais | [Configure identidades geridas para recursos Azure em um Azure VM usando Azure CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#system-assigned-managed-identity) |
    | Conjuntos de dimensionamento de máquinas virtuais | [Configure identidades geridas para recursos Azure em um conjunto de escala de máquina virtual usando Azure CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vmss.md#system-assigned-managed-identity) |
    | Outros serviços | [Serviços que suportam identidades geridas para recursos da Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md) |

1. Use [a az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) para atribuir funções a identidades geridas atribuídas pelo sistema. Para obter mais informações, consulte [Atribuir um acesso de identidade gerido a um recurso utilizando o Azure CLI](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md).

    ```azurecli
    az role assignment create --assignee <objectid> --role '<role_name_or_id>' --scope <scope>
    ```

### <a name="update-user-assigned-managed-identities"></a>Atualizar identidades geridas atribuídas pelo utilizador

1. Eliminar, recriar e anexar identidades geridas atribuídas pelo utilizador.

    | Serviço do Azure | Mais informações | 
    | --- | --- |
    | Máquinas virtuais | [Configure identidades geridas para recursos Azure em um Azure VM usando Azure CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity) |
    | Conjuntos de dimensionamento de máquinas virtuais | [Configure identidades geridas para recursos Azure em um conjunto de escala de máquina virtual usando Azure CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vmss.md#user-assigned-managed-identity) |
    | Outros serviços | [Serviços que suportam identidades geridas para recursos da Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)<br/>[Criar, listar ou eliminar uma identidade gerida atribuída pelo utilizador utilizando o CLI Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md) |

1. Utilize [a az designação de funções](/cli/azure/role/assignment#az_role_assignment_create) para atribuir funções a identidades geridas atribuídas pelo utilizador. Para obter mais informações, consulte [Atribuir um acesso de identidade gerido a um recurso utilizando o Azure CLI](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md).

    ```azurecli
    az role assignment create --assignee <objectid> --role '<role_name_or_id>' --scope <scope>
    ```

### <a name="update-key-vaults"></a>Atualizar cofres-chave

Esta secção descreve os passos básicos para atualizar os cofres das chaves. Para obter mais informações, consulte [movendo um Cofre de Chaves Azure para outra subscrição](../key-vault/general/move-subscription.md).

1. Atualize o ID do inquilino associado a todos os cofres-chave existentes na subscrição do diretório-alvo.

1. Remover todas as entradas de política de acesso existentes.

1. Adicione novas entradas de política de acesso associadas ao directório-alvo.

### <a name="update-acls"></a>Atualizar ACLs

1. Se estiver a utilizar a Azure Data Lake Storage Gen1, atribua os ACLs apropriados. Para obter mais informações, consulte [os dados de segurança armazenados na Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-secure-data.md).

1. Se estiver a utilizar a Azure Data Lake Storage Gen2, atribua os ACLs apropriados. Para obter mais informações, veja [Controlo de acesso no Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-access-control.md).

1. Se estiver a utilizar ficheiros Azure, atribua os ACLs apropriados.

### <a name="review-other-security-methods"></a>Rever outros métodos de segurança

Mesmo que as atribuições de funções sejam removidas durante a transferência, os utilizadores na conta original do proprietário podem continuar a ter acesso à subscrição através de outros métodos de segurança, incluindo:

- As chaves de acesso para serviços, como o Armazenamento.
- [Certificados de gestão](../cloud-services/cloud-services-certs-create.md) que concedem ao administrador do utilizador acesso aos recursos de subscrição.
- As credenciais de Acesso Remoto para serviços, como as Máquinas Virtuais do Azure.

Se a sua intenção é remover o acesso dos utilizadores no diretório de origem para que não tenham acesso no directório-alvo, deve considerar a rotação de quaisquer credenciais. Até que as credenciais sejam atualizadas, os utilizadores continuarão a ter acesso após a transferência.

1. Rode as teclas de acesso à conta de armazenamento. Para obter mais informações, consulte [gerir as teclas de acesso à conta de armazenamento.](../storage/common/storage-account-keys-manage.md)

1. Se estiver a utilizar chaves de acesso para outros serviços, como a Base de Dados Azure SQL ou a Mensagem de Autocarro do Serviço Azure, rode as teclas de acesso.

1. Para recursos que usam segredos, abra as definições para o recurso e atualize o segredo.

1. Para os recursos que utilizam certificados, atualize o certificado.

## <a name="next-steps"></a>Passos seguintes

- [Transferir a propriedade de faturação de uma subscrição do Azure para outra conta](../cost-management-billing/manage/billing-subscription-transfer.md)
- [Transferir subscrições do Azure entre subscritores e CSPs](../cost-management-billing/manage/transfer-subscriptions-subscribers-csp.md)
- [Associar ou adicionar uma subscrição do Azure ao inquilino do Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)
- [Azure Lighthouse em cenários empresariais](../lighthouse/concepts/enterprise.md)