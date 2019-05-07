---
title: Gerir serviço de Blockchain do Azure com a CLI do Azure
description: Como criar e gerir o Azure Blockchain Service com a CLI do Azure
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: seal
manager: femila
ms.openlocfilehash: d078ca181b2eed4b80d4f12f1c03b42f4e242194
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65154446"
---
# <a name="manage-azure-blockchain-service-with-azure-cli"></a>Gerir o serviço do Azure Blockchain com a CLI do Azure

Para além do portal do Azure, pode utilizar a CLI do Azure para criar e gerir os membros de blockchain e nós de transação para o seu serviço do Azure Blockchain rapidamente.

Certifique-se de que instalou a versão mais recente [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) e tem sessão iniciada numa conta do Azure com `az login`.

Nos exemplos a seguir, substitua o exemplo `<parameter names>` pelos seus próprios valores.

## <a name="create-blockchain-member"></a>Criar o membro de blockchain

Exemplo cria um membro de blockchain no Azure Blockchain Service que executa o protocolo de contabilidade de quórum num consórcio de novo.

```azurecli
az resource create --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers --is-full-object --properties "{ \"location\": \"<myBlockchainLocation>\", \"properties\": {\"password\": \"<myStrongPassword>\", \"protocol\": \"Quorum\", \"consortium\": \"<myConsortiumName>\", \"consortiumManagementAccountPassword\": \"<myConsortiumManagementAccountPassword>\", \"firewallRules\": [ { \"ruleName\": \"<myRuleName>\", \"startIpAddress\": \"<myStartIpAddress>\", \"endIpAddress\": \"<myEndIpAddress>\" } ] }, \"sku\": { \"name\": \"<skuName>\" } }"
```

| Parâmetro | Descrição |
|---------|-------------|
| **resource-group** | Nome do grupo de recursos onde os recursos de serviço do Azure Blockchain são criados. |
| **name** | Um nome exclusivo que identifica o membro de blockchain de serviço do Azure Blockchain. O nome é utilizado para o endereço de ponto final público. Por exemplo, `myblockchainmember.blockchain.azure.com`. |
| **localização** | Região do Azure onde o membro de blockchain é criado. Por exemplo, `eastus`. Escolha a localização que estiver mais próxima dos seus utilizadores ou das suas outras aplicações do Azure. |
| **password** | A palavra-passe de conta de membro. A palavra-passe da conta de membro é utilizada para autenticar para ponto final público o membro de blockchain usando a autenticação básica. A palavra-passe tem de cumprir três dos quatro requisitos seguintes: comprimento tem de ter entre 12 e 72 carateres, 1 carater em minúsculas, 1 caráter em maiúsculas, 1 número e 1 caráter especial que é o número não sign(#), percent(%), comma(,), star(*), criar uma aspa (\`), faça duplo quote("), plica, travessão (-) e semicolumn(;)|
| **protocol** | Pré-visualização pública suporta quórum. |
| **consortium** | Nome do consortium para aderir ou criar. |
| **consortiumManagementAccountPassword** | A palavra-passe de gestão de consortium. A palavra-passe é utilizada para associar um consórcio. |
| **ruleName** | Nome da regra para a inclusão de um intervalo de endereços IP. Parâmetro opcional para as regras de firewall.|
| **startIpAddress** | Início do intervalo de endereços IP para a inclusão. Parâmetro opcional para as regras de firewall. |
| **endIpAddress** | Fim do intervalo de endereços IP para a inclusão. Parâmetro opcional para as regras de firewall. |
| **skuName** | Tipo de camada. Utilize S0 Standard e B0 para o básico. |

## <a name="change-blockchain-member-password"></a>Alteração de senha de membro de blockchain

Exemplo altera a palavra-passe de um membro de blockchain.

```azurecli
az resource update --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers --set properties.password="<myStrongPassword>" --remove properties.consortiumManagementAccountAddress
```
| Parâmetro | Descrição |
|---------|-------------|
| **resource-group** | Nome do grupo de recursos onde os recursos de serviço do Azure Blockchain são criados. |
| **name** | Nome que identifica o membro de serviço do Azure Blockchain. |
| **password** | A palavra-passe de conta de membro. A palavra-passe tem de cumprir três dos quatro requisitos seguintes: comprimento tem de ter entre 12 e 72 carateres, 1 carater em minúsculas, 1 caráter em maiúsculas, 1 número e 1 caráter especial que é o número não sign(#), percent(%), comma(,), star(*), criar uma aspa (\`), faça duplo quote("), plica, travessão (-) e semicolon(;). |


## <a name="create-transaction-node"></a>Criar o nó de transação

Crie um nó de transação dentro de um membro de blockchain existente. Ao adicionar nós de transação, pode aumentar o isolamento de segurança e distribuir a carga. Por exemplo, poderia ter um ponto de extremidade de nó de transação para as aplicações cliente diferentes.

```azurecli
az resource create --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --resource-type Microsoft.Blockchain/blockchainMembers  --is-full-object --properties "{ \"location\": \"<myRegion>\", \"properties\": { \"password\": \"<myStrongPassword>\", \"firewallRules\": [ { \"ruleName\": \"<myRuleName>\", \"startIpAddress\": \"<myStartIpAddress>\", \"endIpAddress\": \"<myEndIpAddress>\" } ] } }"
```

| Parâmetro | Descrição |
|---------|-------------|
| **resource-group** | Nome do grupo de recursos onde os recursos de serviço do Azure Blockchain são criados. |
| **name** | Nome do membro de blockchain de serviço do Azure Blockchain também inclui o novo nome de nó de transação. |
| **localização** | Região do Azure onde o membro de blockchain é criado. Por exemplo, `eastus`. Escolha a localização que estiver mais próxima dos seus utilizadores ou das suas outras aplicações do Azure. |
| **password** | A palavra-passe de nó de transação. A palavra-passe tem de cumprir três dos quatro requisitos seguintes: comprimento tem de ter entre 12 e 72 carateres, 1 carater em minúsculas, 1 caráter em maiúsculas, 1 número e 1 caráter especial que é o número não sign(#), percent(%), comma(,), star(*), criar uma aspa (\`), faça duplo quote("), plica, travessão (-) e semicolon(;). |
| **ruleName** | Nome da regra para a inclusão de um intervalo de endereços IP. Parâmetro opcional para as regras de firewall. |
| **startIpAddress** | Início do intervalo de endereços IP para a inclusão. Parâmetro opcional para as regras de firewall. |
| **endIpAddress** | Fim do intervalo de endereços IP para a inclusão. Parâmetro opcional para as regras de firewall.|

## <a name="change-transaction-node-password"></a>Alteração de senha de nó de transação

Exemplo altera uma palavra-passe de nó de transação.

```azurecli
az resource update --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --resource-type Microsoft.Blockchain/blockchainMembers  --set properties.password="<myStrongPassword>"
```

| Parâmetro | Descrição |
|---------|-------------|
| **resource-group** | Nome do grupo de recursos onde existem recursos de serviço do Azure Blockchain. |
| **name** | Nome do membro de blockchain de serviço do Azure Blockchain também inclui o novo nome de nó de transação. |
| **password** | A palavra-passe de nó de transação. A palavra-passe tem de cumprir três dos quatro requisitos seguintes: comprimento tem de ter entre 12 e 72 carateres, 1 carater em minúsculas, 1 caráter em maiúsculas, 1 número e 1 caráter especial que é o número não sign(#), percent(%), comma(,), star(*), criar uma aspa (\`), faça duplo quote("), plica, travessão (-) e semicolon(;). |

## <a name="change-consortium-management-account-password"></a>Palavra-passe de conta de gestão de consórcio alteração

A conta de gestão de consórcio é utilizada para gestão de associação de consortium. Cada membro é exclusivamente identificado por uma conta de gestão consortium e pode alterar a palavra-passe desta conta com o seguinte comando.

```azurecli
az resource update --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers --set properties.consortiumManagementAccountPassword="<myConsortiumManagementAccountPassword>" --remove properties.consortiumManagementAccountAddress
```

| Parâmetro | Descrição |
|---------|-------------|
| **resource-group** | Nome do grupo de recursos onde os recursos de serviço do Azure Blockchain são criados. |
| **name** | Nome que identifica o membro de serviço do Azure Blockchain. |
| **consortiumManagementAccountPassword** | A senha da conta de gestão do consórcio. A palavra-passe tem de cumprir três dos quatro requisitos seguintes: comprimento tem de ter entre 12 e 72 carateres, 1 carater em minúsculas, 1 caráter em maiúsculas, 1 número e 1 caráter especial que é o número não sign(#), percent(%), comma(,), star(*), criar uma aspa (\`), faça duplo quote("), plica, travessão (-) e semicolon(;). |
  
## <a name="update-firewall-rules"></a>Atualizar regras de firewall

```azurecli
az resource update --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers --set properties.firewallRules="[ { \"ruleName\": \"<myRuleName>\", \"startIpAddress\": \"<myStartIpAddress>\", \"endIpAddress\": \"<myEndIpAddress>\" } ]" --remove properties.consortiumManagementAccountAddress
```

| Parâmetro | Descrição |
|---------|-------------|
| **resource-group** | Nome do grupo de recursos onde existem recursos de serviço do Azure Blockchain. |
| **name** | Nome do membro de blockchain de serviço do Azure Blockchain. |
| **ruleName** | Nome da regra para a inclusão de um intervalo de endereços IP. Parâmetro opcional para as regras de firewall.|
| **startIpAddress** | Início do intervalo de endereços IP para a inclusão. Parâmetro opcional para as regras de firewall.|
| **endIpAddress** | Fim do intervalo de endereços IP para a inclusão. Parâmetro opcional para as regras de firewall.|

## <a name="list-api-keys"></a>Chaves de API de lista

Chaves de API podem ser utilizadas para acesso de nó semelhante ao nome de utilizador e palavra-passe. Existem duas chaves de API para oferecer suporte a rotação de chaves. Utilize o seguinte comando para listar as chaves de API.

```azurecli
az resource invoke-action --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --action "listApiKeys" --resource-type Microsoft.Blockchain/blockchainMembers
```

| Parâmetro | Descrição |
|---------|-------------|
| **resource-group** | Nome do grupo de recursos onde existem recursos de serviço do Azure Blockchain. |
| **name** | Nome do membro de blockchain de serviço do Azure Blockchain também inclui o novo nome de nó de transação. |

## <a name="regenerate-api-keys"></a>Voltar a gerar chaves de API

Utilize o seguinte comando para regenerar as chaves de API.

```azurecli
az resource invoke-action --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --action "regenerateApiKeys" --resource-type Microsoft.Blockchain/blockchainMembers --request-body '{"keyName":"<keyValue>"}'
```


| Parâmetro | Descrição |
|---------|-------------|
| **resource-group** | Nome do grupo de recursos onde existem recursos de serviço do Azure Blockchain. |
| **name** | Nome do membro de blockchain de serviço do Azure Blockchain também inclui o novo nome de nó de transação. |
| **keyName** | Substitua \<keyValue\> com chave1 ou chave2. |

## <a name="delete-a-transaction-node"></a>Eliminar um nó de transação

Exemplo elimina um nó de transação de membro de blockchain.

```azurecli
az resource delete --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --resource-type Microsoft.Blockchain/blockchainMembers
```

| Parâmetro | Descrição |
|---------|-------------|
| **resource-group** | Nome do grupo de recursos onde existem recursos de serviço do Azure Blockchain. |
| **name** | Nome do membro de blockchain de serviço do Azure Blockchain também inclui o novo nome de nó de transação seja eliminado. |

## <a name="delete-a-blockchain-member"></a>Eliminar um membro de blockchain

Exemplo elimina um membro de blockchain.

```azurecli
az resource delete --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers
```

| Parâmetro | Descrição |
|---------|-------------|
| **resource-group** | Nome do grupo de recursos onde existem recursos de serviço do Azure Blockchain. |
| **name** | Nome do membro de blockchain de serviço do Azure Blockchain para serem eliminados. |

## <a name="azure-active-directory"></a>Azure Active Directory

### <a name="grant-access-for-azure-ad-user"></a>Conceder acesso de utilizador do Azure AD

```azurecli
az role assignment create --role <role> --assignee <assignee> --scope /subscriptions/<subId>/resourceGroups/<groupName>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>
```

| Parâmetro | Descrição |
|---------|-------------|
| **role** | Nome da função do Azure AD. |
| **assignee** | ID de utilizador de AD do Azure. Por exemplo, `user@contoso.com` |
| **scope** | Âmbito da atribuição de função. Pode ser um membro de blockchain do nó de transação. |

**Exemplo:**

Conceder acesso de nó para o utilizador do Azure AD para blockchain **membro**:

```azurecli
az role assignment create \
  --role "myRole" \
  --assignee user@contoso.com \
  --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

**Exemplo:**

Conceder acesso de nó para o utilizador do Azure AD para blockchain **nó da transação**:

```azurecli
az role assignment create \
  --role "MyRole" \
  --assignee user@contoso.com \
  --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1/transactionNodes/contosoTransactionNode1
```

### <a name="grant-node-access-for-azure-ad-group-or-application-role"></a>Conceder acesso de nó para o grupo do Azure AD ou a aplicação de função

```azurecli
az role assignment create --role <role> --assignee-object-id <assignee_object_id>
```
| Parâmetro | Descrição |
|---------|-------------|
| **role** | Nome da função do Azure AD. |
| **assignee-object-id** | ID de aplicação ou ID de grupo do Azure AD. |
| **scope** | Âmbito da atribuição de função. Pode ser um membro de blockchain do nó de transação. |

**Exemplo:**

Conceder acesso de nó para **função de aplicação**

```azurecli
az role assignment create \
  --role "myRole" \
  --assignee-object-id 22222222-2222-2222-2222-222222222222 \
  --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

### <a name="remove-azure-ad-node-access"></a>Remover o acesso de nó do Azure AD

```azurecli
az role assignment delete --role <myRole> --assignee <assignee> --scope /subscriptions/mySubscriptionId/resourceGroups/<myResourceGroup>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>/transactionNodes/<myTransactionNode>
```

| Parâmetro | Descrição |
|---------|-------------|
| **role** | Nome da função do Azure AD. |
| **assignee** | ID de utilizador de AD do Azure. Por exemplo, `user@contoso.com` |
| **scope** | Âmbito da atribuição de função. Pode ser um membro de blockchain do nó de transação. |

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Configurar os nós de transação de serviço do Azure Blockchain com o portal do Azure](configure-transaction-nodes.md)
