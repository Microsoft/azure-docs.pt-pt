---
title: Gerenciar o serviço Blockchain do Azure usando o CLI do Azure
description: Como criar e gerenciar o serviço Blockchain do Azure com o CLI do Azure
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: seal
manager: femila
ms.openlocfilehash: 4dd58f2542674633f2d5e2a1724adc7934d7f030
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70307051"
---
# <a name="manage-azure-blockchain-service-using-azure-cli"></a>Gerenciar o serviço Blockchain do Azure usando o CLI do Azure

Além do portal do Azure, você pode usar CLI do Azure para gerenciar Membros blockchain e nós de transação para o serviço Blockchain do Azure.

Verifique se você instalou o [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) mais recente e fez logon em uma conta do Azure no com `az login`o.

Nos exemplos a seguir, substitua o `<parameter names>` exemplo pelos seus próprios valores.

## <a name="create-blockchain-member"></a>Criar membro blockchain

Exemplo cria um membro blockchain no serviço Blockchain do Azure que executa o protocolo de razão de quorum em um novo consórcio.

```azurecli
az resource create --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers --is-full-object --properties "{ \"location\": \"<myBlockchainLocation>\", \"properties\": {\"password\": \"<myStrongPassword>\", \"protocol\": \"Quorum\", \"consortium\": \"<myConsortiumName>\", \"consortiumManagementAccountPassword\": \"<myConsortiumManagementAccountPassword>\", \"firewallRules\": [ { \"ruleName\": \"<myRuleName>\", \"startIpAddress\": \"<myStartIpAddress>\", \"endIpAddress\": \"<myEndIpAddress>\" } ] }, \"sku\": { \"name\": \"<skuName>\" } }"
```

| Parâmetro | Descrição |
|---------|-------------|
| **resource-group** | Nome do grupo de recursos em que os recursos do serviço Blockchain do Azure são criados. |
| **name** | Um nome exclusivo que identifica o membro Blockchain do serviço Blockchain do Azure. O nome é usado para o endereço do ponto de extremidade público. Por exemplo, `myblockchainmember.blockchain.azure.com`. |
| **localização** | Região do Azure em que o membro blockchain é criado. Por exemplo, `eastus`. Escolha a localização que estiver mais próxima dos seus utilizadores ou das suas outras aplicações do Azure. |
| **password** | A senha da conta do membro. A senha da conta do membro é usada para autenticar o ponto de extremidade público do membro blockchain usando a autenticação básica. A senha deve atender a três dos quatro requisitos a seguir: o comprimento precisa ter entre 12 & 72 caracteres, um caractere minúsculo, um caractere maiúsculo, um número e um caractere especial que não seja um sinal numérico (#), porcentagem (%), vírgula (,), estrela (*), aspas de fundo (\`), aspas duplas ("), aspas simples ('), Dash (-) e semicolumn (;)|
| **protocolo** | A visualização pública dá suporte ao Quorum. |
| **consortium** | Nome do consórcio para ingressar ou criar. |
| **consortiumManagementAccountPassword** | A senha de gerenciamento do consórcio. A senha é usada para ingressar em um consórcio. |
| **ruleName** | Nome da regra para a lista de permissões de um intervalo de endereços IP. Parâmetro opcional para regras de firewall.|
| **startIpAddress** | Início do intervalo de endereços IP para a lista de permissões. Parâmetro opcional para regras de firewall. |
| **endIpAddress** | Fim do intervalo de endereços IP para a lista de permissões. Parâmetro opcional para regras de firewall. |
| **skuName** | Tipo de camada. Use S0 para Standard e B0 para Basic. |

## <a name="change-blockchain-member-password"></a>Alterar senha do membro blockchain

Exemplo altera a senha de um membro do blockchain.

```azurecli
az resource update --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers --set properties.password="<myStrongPassword>" --remove properties.consortiumManagementAccountAddress
```
| Parâmetro | Descrição |
|---------|-------------|
| **resource-group** | Nome do grupo de recursos em que os recursos do serviço Blockchain do Azure são criados. |
| **name** | Nome que identifica o membro do serviço Blockchain do Azure. |
| **password** | A senha da conta do membro. A senha deve atender a três dos quatro requisitos a seguir: o comprimento precisa ter entre 12 & 72 caracteres, um caractere minúsculo, um caractere maiúsculo, um número e um caractere especial que não seja um sinal numérico (#), porcentagem (%), vírgula (,), estrela (*), aspas de fundo (\`), aspas duplas ("), aspas simples ('), hífen (-) e ponto-e-vírgula (;). |

## <a name="create-transaction-node"></a>Criar nó de transação

Crie um nó de transação dentro de um membro blockchain existente. Ao adicionar nós de transação, você pode aumentar o isolamento de segurança e distribuir a carga. Por exemplo, você poderia ter um ponto de extremidade de nó de transação para diferentes aplicativos cliente.

```azurecli
az resource create --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --resource-type Microsoft.Blockchain/blockchainMembers  --is-full-object --properties "{ \"location\": \"<myRegion>\", \"properties\": { \"password\": \"<myStrongPassword>\", \"firewallRules\": [ { \"ruleName\": \"<myRuleName>\", \"startIpAddress\": \"<myStartIpAddress>\", \"endIpAddress\": \"<myEndIpAddress>\" } ] } }"
```

| Parâmetro | Descrição |
|---------|-------------|
| **resource-group** | Nome do grupo de recursos em que os recursos do serviço Blockchain do Azure são criados. |
| **name** | Nome do membro Blockchain do serviço Blockchain do Azure que também inclui o novo nome do nó de transação. |
| **localização** | Região do Azure em que o membro blockchain é criado. Por exemplo, `eastus`. Escolha a localização que estiver mais próxima dos seus utilizadores ou das suas outras aplicações do Azure. |
| **password** | A senha do nó da transação. A senha deve atender a três dos quatro requisitos a seguir: o comprimento precisa ter entre 12 & 72 caracteres, um caractere minúsculo, um caractere maiúsculo, um número e um caractere especial que não seja um sinal numérico (#), porcentagem (%), vírgula (,), estrela (*), aspas de fundo (\`), aspas duplas ("), aspas simples ('), hífen (-) e ponto-e-vírgula (;). |
| **ruleName** | Nome da regra para a lista de permissões de um intervalo de endereços IP. Parâmetro opcional para regras de firewall. |
| **startIpAddress** | Início do intervalo de endereços IP para a lista de permissões. Parâmetro opcional para regras de firewall. |
| **endIpAddress** | Fim do intervalo de endereços IP para a lista de permissões. Parâmetro opcional para regras de firewall.|

## <a name="change-transaction-node-password"></a>Alterar senha do nó de transação

Exemplo altera uma senha de nó de transação.

```azurecli
az resource update --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --resource-type Microsoft.Blockchain/blockchainMembers  --set properties.password="<myStrongPassword>"
```

| Parâmetro | Descrição |
|---------|-------------|
| **resource-group** | Nome do grupo de recursos no qual os recursos do serviço Blockchain do Azure existem. |
| **name** | Nome do membro Blockchain do serviço Blockchain do Azure que também inclui o novo nome do nó de transação. |
| **password** | A senha do nó da transação. A senha deve atender a três dos quatro requisitos a seguir: o comprimento precisa ter entre 12 & 72 caracteres, um caractere minúsculo, um caractere maiúsculo, um número e um caractere especial que não seja um sinal numérico (#), porcentagem (%), vírgula (,), estrela (*), aspas de fundo (\`), aspas duplas ("), aspas simples ('), hífen (-) e ponto-e-vírgula (;). |

## <a name="change-consortium-management-account-password"></a>Alterar senha da conta de gerenciamento do consórcio

A conta de gerenciamento do consórcio é usada para o gerenciamento de associação do Consortium. Cada membro é identificado exclusivamente por uma conta de gerenciamento de consórcio e você pode alterar a senha dessa conta com o comando a seguir.

```azurecli
az resource update --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers --set properties.consortiumManagementAccountPassword="<myConsortiumManagementAccountPassword>" --remove properties.consortiumManagementAccountAddress
```

| Parâmetro | Descrição |
|---------|-------------|
| **resource-group** | Nome do grupo de recursos em que os recursos do serviço Blockchain do Azure são criados. |
| **name** | Nome que identifica o membro do serviço Blockchain do Azure. |
| **consortiumManagementAccountPassword** | A senha da conta de gerenciamento do consórcio. A senha deve atender a três dos quatro requisitos a seguir: o comprimento precisa ter entre 12 & 72 caracteres, um caractere minúsculo, um caractere maiúsculo, um número e um caractere especial que não seja um sinal numérico (#), porcentagem (%), vírgula (,), estrela (*), aspas de fundo (\`), aspas duplas ("), aspas simples ('), hífen (-) e ponto-e-vírgula (;). |
  
## <a name="update-firewall-rules"></a>Atualizar regras de firewall

```azurecli
az resource update --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers --set properties.firewallRules="[ { \"ruleName\": \"<myRuleName>\", \"startIpAddress\": \"<myStartIpAddress>\", \"endIpAddress\": \"<myEndIpAddress>\" } ]" --remove properties.consortiumManagementAccountAddress
```

| Parâmetro | Descrição |
|---------|-------------|
| **resource-group** | Nome do grupo de recursos no qual os recursos do serviço Blockchain do Azure existem. |
| **name** | Nome do membro Blockchain do serviço Blockchain do Azure. |
| **ruleName** | Nome da regra para a lista de permissões de um intervalo de endereços IP. Parâmetro opcional para regras de firewall.|
| **startIpAddress** | Início do intervalo de endereços IP para a lista de permissões. Parâmetro opcional para regras de firewall.|
| **endIpAddress** | Fim do intervalo de endereços IP para a lista de permissões. Parâmetro opcional para regras de firewall.|

## <a name="list-api-keys"></a>Listar chaves de API

As chaves de API podem ser usadas para acesso de nó semelhante ao nome de usuário e à senha. Há duas chaves de API para dar suporte à rotação de chaves. Use o comando a seguir para listar suas chaves de API.

```azurecli
az resource invoke-action --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --action "listApiKeys" --resource-type Microsoft.Blockchain/blockchainMembers
```

| Parâmetro | Descrição |
|---------|-------------|
| **resource-group** | Nome do grupo de recursos no qual os recursos do serviço Blockchain do Azure existem. |
| **name** | Nome do membro Blockchain do serviço Blockchain do Azure que também inclui o novo nome do nó de transação. |

## <a name="regenerate-api-keys"></a>Regenerar chaves de API

Use o comando a seguir para regenerar as chaves de API.

```azurecli
az resource invoke-action --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --action "regenerateApiKeys" --resource-type Microsoft.Blockchain/blockchainMembers --request-body '{"keyName":"<keyValue>"}'
```


| Parâmetro | Descrição |
|---------|-------------|
| **resource-group** | Nome do grupo de recursos no qual os recursos do serviço Blockchain do Azure existem. |
| **name** | Nome do membro Blockchain do serviço Blockchain do Azure que também inclui o novo nome do nó de transação. |
| **keyName** | Substitua \<KeyValue\> por key1 ou Key2. |

## <a name="delete-a-transaction-node"></a>Excluir um nó de transação

Exemplo exclui um nó de transação de membro blockchain.

```azurecli
az resource delete --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --resource-type Microsoft.Blockchain/blockchainMembers
```

| Parâmetro | Descrição |
|---------|-------------|
| **resource-group** | Nome do grupo de recursos no qual os recursos do serviço Blockchain do Azure existem. |
| **name** | Nome do membro Blockchain do serviço Blockchain do Azure que também inclui o novo nome do nó de transação a ser excluído. |

## <a name="delete-a-blockchain-member"></a>Excluir um membro do blockchain

Exemplo exclui um membro blockchain.

```azurecli
az resource delete --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers
```

| Parâmetro | Descrição |
|---------|-------------|
| **resource-group** | Nome do grupo de recursos no qual os recursos do serviço Blockchain do Azure existem. |
| **name** | Nome do membro Blockchain do serviço Blockchain do Azure a ser excluído. |

## <a name="azure-active-directory"></a>Azure Active Directory

### <a name="grant-access-for-azure-ad-user"></a>Conceder acesso ao usuário do Azure AD

```azurecli
az role assignment create --role <role> --assignee <assignee> --scope /subscriptions/<subId>/resourceGroups/<groupName>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>
```

| Parâmetro | Descrição |
|---------|-------------|
| **cargo** | Nome da função do Azure AD. |
| **destinatário** | ID de usuário do Azure AD. Por exemplo, `user@contoso.com` |
| **scope** | Escopo da atribuição de função. Pode ser um membro blockchain ou um nó de transação. |

**Example:**

Conceder acesso ao nó para o usuário do Azure AD para blockchain **membro**:

```azurecli
az role assignment create \
  --role "myRole" \
  --assignee user@contoso.com \
  --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

**Example:**

Conceder acesso ao nó para o usuário do Azure AD para o **nó de transação**blockchain:

```azurecli
az role assignment create \
  --role "MyRole" \
  --assignee user@contoso.com \
  --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1/transactionNodes/contosoTransactionNode1
```

### <a name="grant-node-access-for-azure-ad-group-or-application-role"></a>Conceder acesso ao nó para a função de aplicativo ou grupo do Azure AD

```azurecli
az role assignment create --role <role> --assignee-object-id <assignee_object_id>
```
| Parâmetro | Descrição |
|---------|-------------|
| **cargo** | Nome da função do Azure AD. |
| **assignee-object-id** | ID do grupo ou ID do aplicativo do Azure AD. |
| **scope** | Escopo da atribuição de função. Pode ser um membro blockchain ou um nó de transação. |

**Example:**

Conceder acesso ao nó para a **função de aplicativo**

```azurecli
az role assignment create \
  --role "myRole" \
  --assignee-object-id 22222222-2222-2222-2222-222222222222 \
  --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

### <a name="remove-azure-ad-node-access"></a>Remover o acesso ao nó do Azure AD

```azurecli
az role assignment delete --role <myRole> --assignee <assignee> --scope /subscriptions/mySubscriptionId/resourceGroups/<myResourceGroup>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>/transactionNodes/<myTransactionNode>
```

| Parâmetro | Descrição |
|---------|-------------|
| **cargo** | Nome da função do Azure AD. |
| **destinatário** | ID de usuário do Azure AD. Por exemplo, `user@contoso.com` |
| **scope** | Escopo da atribuição de função. Pode ser um membro blockchain ou um nó de transação. |

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Configurar os nós de transação do serviço Blockchain do Azure com o portal do Azure](configure-transaction-nodes.md)
