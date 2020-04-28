---
title: Gerir o Serviço Blockchain Azure utilizando o Azure CLI
description: Como gerir o Serviço Azure Blockchain com o Azure CLI
ms.date: 11/22/2019
ms.topic: article
ms.reviewer: janders
ms.openlocfilehash: ac75be644877905c1517395c1c789b1ea16fd49c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74455577"
---
# <a name="manage-azure-blockchain-service-using-azure-cli"></a>Gerir o Serviço Blockchain Azure utilizando o Azure CLI

Além do portal Azure, pode utilizar o Azure CLI para gerir os membros blockchain e os nós de transação para o seu Serviço Azure Blockchain.

Certifique-se de que instalou o mais recente [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) e `az login`fez login numa conta Azure com .

Nos seguintes exemplos, `<parameter names>` substitua o exemplo com os seus próprios valores.

## <a name="create-blockchain-member"></a>Criar membro blockchain

O exemplo cria um membro blockchain no Azure Blockchain Service que executa o protocolo de livros quorum num novo consórcio.

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
| **grupo de recursos** | Nome de grupo de recursos onde os recursos do Serviço Azure Blockchain são criados. |
| **nome** | Um nome único que identifica o seu membro blockchain Azure Blockchain Service. O nome é usado para o endereço final do público. Por exemplo, `myblockchainmember.blockchain.azure.com`. |
| **localização** | Região azul onde o membro blockchain é criado. Por exemplo, `eastus`. Escolha a localização que estiver mais próxima dos seus utilizadores ou das suas outras aplicações do Azure. |
| **palavra-passe** | A senha da conta membro. A palavra-passe da conta membro é usada para autenticar o ponto final do membro blockchain usando a autenticação básica. A palavra-passe deve satisfazer três dos seguintes quatro requisitos: comprimento deve ser entre 12 & 72 caracteres, 1 carácter inferior, 1 caracteres maiúsculos, 1 número e\`1 personagem especial que não seja sinal de número(#), por cento (%), vírem(*), estrela(*), citação dupla(), citação única('), traço (-) e semicoluna (;)|
| **protocolo** | A pré-visualização pública apoia o Quorum. |
| **consórcio** | Nome do consórcio para aderir ou criar. |
| **consórcioManagementAccountPassword** | A senha de gestão do consórcio. A palavra-passe é usada para se juntar a um consórcio. |
| **nome de regras** | Nome de regra para whitelisting um intervalo de endereço IP. Parâmetro opcional para regras de firewall.|
| **iniciarEndereçoIpAddress** | Início da gama de endereços IP para whitelisting. Parâmetro opcional para regras de firewall. |
| **endIpAddress** | Fim da gama de endereços IP para whitelisting. Parâmetro opcional para regras de firewall. |
| **skuName** | Tipo nível. Utilize S0 para Standard e B0 para Básico. |

## <a name="change-blockchain-member-password"></a>Alterar a palavra-passe do membro blockchain

O exemplo altera a palavra-passe de um membro da blockchain.

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
| **grupo de recursos** | Nome de grupo de recursos onde os recursos do Serviço Azure Blockchain são criados. |
| **nome** | Nome que identifique o seu membro do Serviço Azure Blockchain. |
| **palavra-passe** | A senha da conta membro. A palavra-passe deve satisfazer três dos seguintes quatro requisitos: o comprimento deve ser entre 12 & 72 caracteres, 1 carácter minúsculo, 1 caracteres maiúsculos, 1 número\`e 1 personagem especial que não seja sinal de número(#), por cento (%), vírgula(*), citação de volta(), citação dupla(),citação única(', traço (-) e ponto evículo (;). |

## <a name="create-transaction-node"></a>Criar nó de transação

Crie um nó de transação dentro de um membro blockchain existente. Adicionando nós de transação, pode aumentar o isolamento de segurança e distribuir carga. Por exemplo, você poderia ter um ponto final de nó de transação para diferentes aplicações de clientes.

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
| **grupo de recursos** | Nome de grupo de recursos onde os recursos do Serviço Azure Blockchain são criados. |
| **nome** | Nome do membro blockchain do Azure Blockchain Service que também inclui o novo nome do nó de transação. |
| **localização** | Região azul onde o membro blockchain é criado. Por exemplo, `eastus`. Escolha a localização que estiver mais próxima dos seus utilizadores ou das suas outras aplicações do Azure. |
| **palavra-passe** | A senha do nó de transações. A palavra-passe deve satisfazer três dos seguintes quatro requisitos: o comprimento deve ser entre 12 & 72 caracteres, 1 carácter minúsculo, 1 caracteres maiúsculos, 1 número\`e 1 personagem especial que não seja sinal de número(#), por cento (%), vírgula(*), citação de volta(), citação dupla(),citação única(', traço (-) e ponto evículo (;). |
| **nome de regras** | Nome de regra para whitelisting um intervalo de endereço IP. Parâmetro opcional para regras de firewall. |
| **iniciarEndereçoIpAddress** | Início da gama de endereços IP para whitelisting. Parâmetro opcional para regras de firewall. |
| **endIpAddress** | Fim da gama de endereços IP para whitelisting. Parâmetro opcional para regras de firewall.|

## <a name="change-transaction-node-password"></a>Alterar a senha do nó de transações

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
| **grupo de recursos** | Nome de grupo de recursos onde os recursos do Serviço Azure Blockchain existem. |
| **nome** | Nome do membro blockchain do Azure Blockchain Service que também inclui o novo nome do nó de transação. |
| **palavra-passe** | A senha do nó de transações. A palavra-passe deve satisfazer três dos seguintes quatro requisitos: o comprimento deve ser entre 12 & 72 caracteres, 1 carácter minúsculo, 1 caracteres maiúsculos, 1 número\`e 1 personagem especial que não seja sinal de número(#), por cento (%), vírgula(*), citação de volta(), citação dupla(),citação única(', traço (-) e ponto evículo (;). |

## <a name="change-consortium-management-account-password"></a>Alterar a palavra-passe da conta de gestão do consórcio

A conta de gestão do consórcio é utilizada para a gestão de membros do consórcio. Cada membro é identificado de forma única por uma conta de gestão de consórcio e pode alterar a palavra-passe desta conta com o seguinte comando.

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
| **grupo de recursos** | Nome de grupo de recursos onde os recursos do Serviço Azure Blockchain são criados. |
| **nome** | Nome que identifique o seu membro do Serviço Azure Blockchain. |
| **consórcioManagementAccountPassword** | A senha da conta de gestão do consórcio. A palavra-passe deve satisfazer três dos seguintes quatro requisitos: o comprimento deve ser entre 12 & 72 caracteres, 1 carácter minúsculo, 1 caracteres maiúsculos, 1 número\`e 1 personagem especial que não seja sinal de número(#), por cento (%), vírgula(*), citação de volta(), citação dupla(),citação única(', traço (-) e ponto evículo (;). |
  
## <a name="update-firewall-rules"></a>Atualizar regras de firewall

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
| **grupo de recursos** | Nome de grupo de recursos onde os recursos do Serviço Azure Blockchain existem. |
| **nome** | Nome do membro blockchain do Serviço Blockchain Azure. |
| **nome de regras** | Nome de regra para whitelisting um intervalo de endereço IP. Parâmetro opcional para regras de firewall.|
| **iniciarEndereçoIpAddress** | Início da gama de endereços IP para whitelisting. Parâmetro opcional para regras de firewall.|
| **endIpAddress** | Fim da gama de endereços IP para whitelisting. Parâmetro opcional para regras de firewall.|

## <a name="list-api-keys"></a>Lista de chaves API

As teclas API podem ser utilizadas para acesso ao nó semelhante ao nome do utilizador e à palavra-passe. Existem duas teclas API para suportar a rotação da chave. Utilize o seguinte comando para listar as suas teclas API.

```azurecli
az resource invoke-action \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName>/transactionNodes/<myTransactionNode> \
                            --action "listApiKeys" \
                            --resource-type Microsoft.Blockchain/blockchainMembers
```

| Parâmetro | Descrição |
|---------|-------------|
| **grupo de recursos** | Nome de grupo de recursos onde os recursos do Serviço Azure Blockchain existem. |
| **nome** | Nome do membro blockchain do Azure Blockchain Service que também inclui o novo nome do nó de transação. |

## <a name="regenerate-api-keys"></a>Chaves API regeneradas

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
| **grupo de recursos** | Nome de grupo de recursos onde os recursos do Serviço Azure Blockchain existem. |
| **nome** | Nome do membro blockchain do Azure Blockchain Service que também inclui o novo nome do nó de transação. |
| **nome chave** | Substitua \<\> a teclaValue por tecla1 ou tecla2. |

## <a name="delete-a-transaction-node"></a>Eliminar um nó de transações

Exemplo elimina um nó de transação de membro blockchain.

```azurecli
az resource delete \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>/transactionNodes/<myTransactionNode> \
                     --resource-type Microsoft.Blockchain/blockchainMembers
```

| Parâmetro | Descrição |
|---------|-------------|
| **grupo de recursos** | Nome de grupo de recursos onde os recursos do Serviço Azure Blockchain existem. |
| **nome** | Nome do membro blockchain do Serviço Blockchain Azure que também inclui o nome do nó de transações a ser eliminado. |

## <a name="delete-a-blockchain-member"></a>Eliminar um membro blockchain

Exemplo elimina um membro blockchain.

```azurecli
az resource delete \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers
```

| Parâmetro | Descrição |
|---------|-------------|
| **grupo de recursos** | Nome de grupo de recursos onde os recursos do Serviço Azure Blockchain existem. |
| **nome** | Nome do membro blockchain do Serviço Blockchain Azure a ser eliminado. |

## <a name="azure-active-directory"></a>Azure Active Directory

### <a name="grant-access-for-azure-ad-user"></a>Conceder acesso ao utilizador da AD Azure

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee <assignee> \
                            --scope /subscriptions/<subId>/resourceGroups/<groupName>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>
```

| Parâmetro | Descrição |
|---------|-------------|
| **papel** | Nome do papel da AD Azure. |
| **designado** | Id do utilizador da Azure AD. Por exemplo, `user@contoso.com` |
| **âmbito** | Âmbito da atribuição de funções. Pode ser um membro blockchain ou um nó de transação. |

**Exemplo:**

Conceder acesso ao nó para o utilizador da AD Azure ao **membro**blockchain:

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

**Exemplo:**

Conceder acesso ao nó para o utilizador da AD Azure ao nó de **transação**blockchain:

```azurecli
az role assignment create \
                            --role 'MyRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1/transactionNodes/contosoTransactionNode1
```

### <a name="grant-node-access-for-azure-ad-group-or-application-role"></a>Conceder acesso ao nó para o grupo Azure AD ou função de candidatura

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee-object-id <assignee_object_id>
```

| Parâmetro | Descrição |
|---------|-------------|
| **papel** | Nome do papel da AD Azure. |
| **atribuir objeto-id** | Id de grupo Azure AD ou ID de aplicação. |
| **âmbito** | Âmbito da atribuição de funções. Pode ser um membro blockchain ou um nó de transação. |

**Exemplo:**

Conceder acesso ao nó para **função** de candidatura

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee-object-id 22222222-2222-2222-2222-222222222222 \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

### <a name="remove-azure-ad-node-access"></a>Remover o acesso ao nó da AD Azure

```azurecli
az role assignment delete \
                            --role <myRole> \
                            --assignee <assignee> \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/<myResourceGroup>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>/transactionNodes/<myTransactionNode>
```

| Parâmetro | Descrição |
|---------|-------------|
| **papel** | Nome do papel da AD Azure. |
| **designado** | Id do utilizador da Azure AD. Por exemplo, `user@contoso.com` |
| **âmbito** | Âmbito da atribuição de funções. Pode ser um membro blockchain ou um nó de transação. |

## <a name="next-steps"></a>Passos seguintes

Saiba como configurar os nódos os nódos de transação do [Azure Blockchain Service com o portal Azure](configure-transaction-nodes.md).
