---
title: Gerir o Serviço Azure Blockchain utilizando o Azure CLI
description: Como gerir o Azure Blockchain Service com a Azure CLI
ms.date: 11/22/2019
ms.topic: how-to
ms.reviewer: janders
ms.openlocfilehash: fc00bedee5ff55033a1d65c6d5d6bfa766f0f01e
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/23/2020
ms.locfileid: "85208166"
---
# <a name="manage-azure-blockchain-service-using-azure-cli"></a>Gerir o Serviço Azure Blockchain utilizando o Azure CLI

Além do portal Azure, pode utilizar o Azure CLI para gerir membros blockchain e nóns de transação para o seu Serviço Azure Blockchain.

Certifique-se de que instalou o mais recente [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) e iniciou sessão numa conta Azure com `az login` .

Nos exemplos seguintes, substitua o exemplo `<parameter names>` pelos seus próprios valores.

## <a name="create-blockchain-member"></a>Criar membro blockchain

Exemplo cria um membro blockchain no Azure Blockchain Service que executa o protocolo de contabilidade Do Quorum num novo consórcio.

```azurecli
az resource create \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --is-full-object \
                     --properties '{ "location":"<myBlockchainLocation>", "properties": {"password":"<myStrongPassword>", "protocol":"Quorum","consortium":"<myConsortiumName>", "consortiumManagementAccountPassword":"<myConsortiumManagementAccountPassword>", "firewallRules":[{"ruleName":"<myRuleName>","startIpAddress":"<myStartIpAddress>", "endIpAddress":"<myEndIpAddress>"}]}, "sku":{"name":"<skuName>"}}'
```

| Parâmetro | Descrição |
|---------|-------------|
| **grupo de recursos** | Nome do grupo de recursos onde são criados os recursos do Serviço Azure Blockchain. |
| **nome** | Um nome único que identifica o seu membro blockchain do Azure Blockchain Service. O nome é usado para o endereço de ponto final público. Por exemplo, `myblockchainmember.blockchain.azure.com`. |
| **localização** | Região de Azure onde o membro blockchain é criado. Por exemplo, `eastus`. Escolha a localização que estiver mais próxima dos seus utilizadores ou das suas outras aplicações do Azure. |
| **palavra-passe** | A senha da conta do membro. A palavra-passe da conta do membro é usada para autenticar o ponto final público do membro blockchain utilizando a autenticação básica. A palavra-passe deve satisfazer três dos quatro requisitos seguintes: o comprimento deve estar entre 12 & 72 caracteres, 1 caracteres maiúsculos, 1 caracteres maiúsculos, 1 número e 1 caracteres especiais que não seja sinal de número (#), por cento(,), vírgula(,), citação \` traseira(, citação dupla(, citação única('), traço(-) e semicolumn (;)|
| **protocolo** | A pré-visualização pública apoia o Quorum. |
| **consórcio** | Nome do consórcio para aderir ou criar. |
| **consórcioManagementAcountPassword** | A senha de gestão do consórcio. A palavra-passe é usada para se juntar a um consórcio. |
| **regraName** | Nome da regra para listar um intervalo de endereço IP. Parâmetro opcional para regras de firewall.|
| **startIpAddress** | Início da gama de endereços IP para lista de whitelisting. Parâmetro opcional para regras de firewall. |
| **endIpAddress** | Fim do intervalo de endereços IP para lista de whitelisting. Parâmetro opcional para regras de firewall. |
| **skuName** | Tipo de nível. Use S0 para Standard e B0 para Básico. |

## <a name="change-blockchain-member-password"></a>Alterar palavra-passe do membro blockchain

Exemplo altera a palavra-passe de um membro blockchain.

```azurecli
az resource update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --set properties.password='<myStrongPassword>' \
                     --remove properties.consortiumManagementAccountAddress
```

| Parâmetro | Descrição |
|---------|-------------|
| **grupo de recursos** | Nome do grupo de recursos onde são criados os recursos do Serviço Azure Blockchain. |
| **nome** | Nome que identifica o seu membro do Serviço Azure Blockchain. |
| **palavra-passe** | A senha da conta do membro. A palavra-passe deve satisfazer três dos quatro requisitos seguintes: o comprimento deve estar entre 12 & 72 caracteres, 1 personagem minúscula, 1 personagem maiústário, 1 número e 1 caracteres especiais que não seja sinal de número (#), por cento(, vírgula(,), citação traseira(, \` citação dupla(, citação única(', traço(-) e ponto de e vírgula (;)). |

## <a name="create-transaction-node"></a>Criar nó de transação

Crie um nó de transação dentro de um membro blockchain existente. Ao adicionar nós de transação, pode aumentar o isolamento de segurança e distribuir carga. Por exemplo, pode ter um ponto final de nó de transação para diferentes aplicações do cliente.

```azurecli
az resource create \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>/transactionNodes/<myTransactionNode> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --is-full-object \
                     --properties '{"location":"<myRegion>", "properties":{"password":"<myStrongPassword>", "firewallRules":[{"ruleName":"<myRuleName>", "startIpAddress":"<myStartIpAddress>", "endIpAddress":"<myEndIpAddress>"}]}}'
```

| Parâmetro | Descrição |
|---------|-------------|
| **grupo de recursos** | Nome do grupo de recursos onde são criados os recursos do Serviço Azure Blockchain. |
| **nome** | Nome do membro blockchain do Azure Blockchain Service que também inclui o nome do novo nó de transação. |
| **localização** | Região de Azure onde o membro blockchain é criado. Por exemplo, `eastus`. Escolha a localização que estiver mais próxima dos seus utilizadores ou das suas outras aplicações do Azure. |
| **palavra-passe** | A senha do nó de transação. A palavra-passe deve satisfazer três dos quatro requisitos seguintes: o comprimento deve estar entre 12 & 72 caracteres, 1 personagem minúscula, 1 personagem maiústário, 1 número e 1 caracteres especiais que não seja sinal de número (#), por cento(, vírgula(,), citação traseira(, \` citação dupla(, citação única(', traço(-) e ponto de e vírgula (;)). |
| **regraName** | Nome da regra para listar um intervalo de endereço IP. Parâmetro opcional para regras de firewall. |
| **startIpAddress** | Início da gama de endereços IP para lista de whitelisting. Parâmetro opcional para regras de firewall. |
| **endIpAddress** | Fim do intervalo de endereços IP para lista de whitelisting. Parâmetro opcional para regras de firewall.|

## <a name="change-transaction-node-password"></a>Alterar senha de nó de transação

Exemplo altera uma senha de nó de transação.

```azurecli
az resource update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>/transactionNodes/<myTransactionNode> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --set properties.password='<myStrongPassword>'
```

| Parâmetro | Descrição |
|---------|-------------|
| **grupo de recursos** | Nome do grupo de recursos onde existem recursos do Serviço Azure Blockchain. |
| **nome** | Nome do membro blockchain do Azure Blockchain Service que também inclui o nome do novo nó de transação. |
| **palavra-passe** | A senha do nó de transação. A palavra-passe deve satisfazer três dos quatro requisitos seguintes: o comprimento deve estar entre 12 & 72 caracteres, 1 personagem minúscula, 1 personagem maiústário, 1 número e 1 caracteres especiais que não seja sinal de número (#), por cento(, vírgula(,), citação traseira(, \` citação dupla(, citação única(', traço(-) e ponto de e vírgula (;)). |

## <a name="change-consortium-management-account-password"></a>Alterar senha de conta de gestão de consórcio

A conta de gestão do consórcio é utilizada para a gestão de membros do consórcio. Cada membro é identificado exclusivamente por uma conta de gestão de consórcio e pode alterar a palavra-passe desta conta com o seguinte comando.

```azurecli
az resource update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --set properties.consortiumManagementAccountPassword='<myConsortiumManagementAccountPassword>' \
                     --remove properties.consortiumManagementAccountAddress
```

| Parâmetro | Descrição |
|---------|-------------|
| **grupo de recursos** | Nome do grupo de recursos onde são criados os recursos do Serviço Azure Blockchain. |
| **nome** | Nome que identifica o seu membro do Serviço Azure Blockchain. |
| **consórcioManagementAcountPassword** | A senha da conta de gestão do consórcio. A palavra-passe deve satisfazer três dos quatro requisitos seguintes: o comprimento deve estar entre 12 & 72 caracteres, 1 personagem minúscula, 1 personagem maiústário, 1 número e 1 caracteres especiais que não seja sinal de número (#), por cento(, vírgula(,), citação traseira(, \` citação dupla(, citação única(', traço(-) e ponto de e vírgula (;)). |
  
## <a name="update-firewall-rules"></a>Atualizar as regras de firewall

```azurecli
az resource update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --set properties.firewallRules='[{"ruleName":"<myRuleName>", "startIpAddress":"<myStartIpAddress>", "endIpAddress":"<myEndIpAddress>"}]' \
                     --remove properties.consortiumManagementAccountAddress
```

| Parâmetro | Descrição |
|---------|-------------|
| **grupo de recursos** | Nome do grupo de recursos onde existem recursos do Serviço Azure Blockchain. |
| **nome** | Nome do membro blockchain do Serviço Azure Blockchain. |
| **regraName** | Nome da regra para listar um intervalo de endereço IP. Parâmetro opcional para regras de firewall.|
| **startIpAddress** | Início da gama de endereços IP para lista de whitelisting. Parâmetro opcional para regras de firewall.|
| **endIpAddress** | Fim do intervalo de endereços IP para lista de whitelisting. Parâmetro opcional para regras de firewall.|

## <a name="list-api-keys"></a>Listar chaves API

As teclas API podem ser usadas para acesso a nó semelhante ao nome de utilizador e à palavra-passe. Existem duas chaves API para suportar a rotação da chave. Utilize o seguinte comando para listar as suas teclas API.

```azurecli
az resource invoke-action \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName>/transactionNodes/<myTransactionNode> \
                            --action "listApiKeys" \
                            --resource-type Microsoft.Blockchain/blockchainMembers
```

| Parâmetro | Descrição |
|---------|-------------|
| **grupo de recursos** | Nome do grupo de recursos onde existem recursos do Serviço Azure Blockchain. |
| **nome** | Nome do membro blockchain do Azure Blockchain Service que também inclui o nome do novo nó de transação. |

## <a name="regenerate-api-keys"></a>Chaves API regeneração

Utilize o seguinte comando para regenerar as suas teclas API.

```azurecli
az resource invoke-action \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName>/transactionNodes/<myTransactionNode> \
                            --action "regenerateApiKeys" \
                            --resource-type Microsoft.Blockchain/blockchainMembers \
                            --request-body '{"keyName":"<keyValue>"}'
```

| Parâmetro | Descrição |
|---------|-------------|
| **grupo de recursos** | Nome do grupo de recursos onde existem recursos do Serviço Azure Blockchain. |
| **nome** | Nome do membro blockchain do Azure Blockchain Service que também inclui o nome do novo nó de transação. |
| **nome chave** | \<keyValue\>Substitua-a por uma chave1 ou uma chave2. |

## <a name="delete-a-transaction-node"></a>Apagar um nó de transação

Exemplo elimina um nó de transação de membro blockchain.

```azurecli
az resource delete \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>/transactionNodes/<myTransactionNode> \
                     --resource-type Microsoft.Blockchain/blockchainMembers
```

| Parâmetro | Descrição |
|---------|-------------|
| **grupo de recursos** | Nome do grupo de recursos onde existem recursos do Serviço Azure Blockchain. |
| **nome** | Nome do membro blockchain do Azure Blockchain Service que também inclui o nome do nó de transação a ser eliminado. |

## <a name="delete-a-blockchain-member"></a>Excluir um membro blockchain

O exemplo elimina um membro da blockchain.

```azurecli
az resource delete \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers
```

| Parâmetro | Descrição |
|---------|-------------|
| **grupo de recursos** | Nome do grupo de recursos onde existem recursos do Serviço Azure Blockchain. |
| **nome** | Nome do membro blockchain do Serviço Azure Blockchain a ser eliminado. |

## <a name="azure-active-directory"></a>Azure Active Directory

### <a name="grant-access-for-azure-ad-user"></a>Conceder acesso a utilizador Azure AD

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee <assignee> \
                            --scope /subscriptions/<subId>/resourceGroups/<groupName>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>
```

| Parâmetro | Descrição |
|---------|-------------|
| **papel** | Nome do papel da AD Azure. |
| **destinatário** | Identificação do utilizador AD Azure. Por exemplo, `user@contoso.com` |
| **âmbito** | Âmbito da atribuição de papel. Pode ser um membro blockchain ou nó de transação. |

**Exemplo:**

Conceder acesso ao nó para utilizador Azure AD para **membro**blockchain:

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

**Exemplo:**

Conceder acesso ao nó para o utilizador Azure AD para **o nó de transação**blockchain:

```azurecli
az role assignment create \
                            --role 'MyRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1/transactionNodes/contosoTransactionNode1
```

### <a name="grant-node-access-for-azure-ad-group-or-application-role"></a>Acesso de nó de subvenção para grupo AZure AD ou papel de candidatura

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee-object-id <assignee_object_id>
```

| Parâmetro | Descrição |
|---------|-------------|
| **papel** | Nome do papel da AD Azure. |
| **assignee-object-id** | ID do grupo AD Azure ou ID de aplicação. |
| **âmbito** | Âmbito da atribuição de papel. Pode ser um membro blockchain ou nó de transação. |

**Exemplo:**

Conceder acesso a nó para **função de candidatura**

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee-object-id 22222222-2222-2222-2222-222222222222 \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

### <a name="remove-azure-ad-node-access"></a>Remover acesso ao nó AD AZure

```azurecli
az role assignment delete \
                            --role <myRole> \
                            --assignee <assignee> \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/<myResourceGroup>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>/transactionNodes/<myTransactionNode>
```

| Parâmetro | Descrição |
|---------|-------------|
| **papel** | Nome do papel da AD Azure. |
| **destinatário** | Identificação do utilizador AD Azure. Por exemplo, `user@contoso.com` |
| **âmbito** | Âmbito da atribuição de papel. Pode ser um membro blockchain ou nó de transação. |

## <a name="next-steps"></a>Passos seguintes

Saiba como [configurar os nós de transação do Serviço Azure Blockchain com o portal Azure](configure-transaction-nodes.md).
