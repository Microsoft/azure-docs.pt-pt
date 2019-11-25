---
title: Manage Azure Blockchain Service using Azure CLI
description: How to manage Azure Blockchain Service with Azure CLI
ms.date: 11/22/2019
ms.topic: article
ms.reviewer: janders
ms.openlocfilehash: ac75be644877905c1517395c1c789b1ea16fd49c
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455577"
---
# <a name="manage-azure-blockchain-service-using-azure-cli"></a>Manage Azure Blockchain Service using Azure CLI

In addition to the Azure portal, you can use Azure CLI to manage blockchain members and transaction nodes for your Azure Blockchain Service.

Make sure that you have installed the latest [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) and logged in to an Azure account in with `az login`.

In the following examples, replace example `<parameter names>` with your own values.

## <a name="create-blockchain-member"></a>Create blockchain member

Example creates a blockchain member in Azure Blockchain Service that runs the Quorum ledger protocol in a new consortium.

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
| **resource-group** | Resource group name where Azure Blockchain Service resources are created. |
| **name** | A unique name that identifies your Azure Blockchain Service blockchain member. The name is used for the public endpoint address. Por exemplo, `myblockchainmember.blockchain.azure.com`. |
| **localização** | Azure region where the blockchain member is created. Por exemplo, `eastus`. Escolha a localização que estiver mais próxima dos seus utilizadores ou das suas outras aplicações do Azure. |
| **password** | The member account password. The member account password is used to authenticate to the blockchain member's public endpoint using basic authentication. The password must meet three of the following four requirements: length needs to be between 12 & 72 characters, 1 lower case character, 1 upper case character, 1 number, and 1 special character that is not number sign(#), percent(%), comma(,), star(*), back quote(\`), double quote("), single quote('), dash(-) and semicolumn(;)|
| **protocol** | Public preview supports Quorum. |
| **consortium** | Name of the consortium to join or create. |
| **consortiumManagementAccountPassword** | The consortium management password. The password is used for joining a consortium. |
| **ruleName** | Rule name for whitelisting an IP address range. Optional parameter for firewall rules.|
| **startIpAddress** | Start of the IP address range for whitelisting. Optional parameter for firewall rules. |
| **endIpAddress** | End of the IP address range for whitelisting. Optional parameter for firewall rules. |
| **skuName** | Tier type. Use S0 for Standard and B0 for Basic. |

## <a name="change-blockchain-member-password"></a>Change blockchain member password

Example changes a blockchain member's password.

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
| **resource-group** | Resource group name where Azure Blockchain Service resources are created. |
| **name** | Name that identifies your Azure Blockchain Service member. |
| **password** | The member account password. The password must meet three of the following four requirements: length needs to be between 12 & 72 characters, 1 lower case character, 1 upper case character, 1 number, and 1 special character that is not number sign(#), percent(%), comma(,), star(*), back quote(\`), double quote("), single quote('), dash(-) and semicolon(;). |

## <a name="create-transaction-node"></a>Create transaction node

Create a transaction node inside an existing blockchain member. By adding transaction nodes, you can increase security isolation and distribute load. For example, you could have a transaction node endpoint for different client applications.

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
| **resource-group** | Resource group name where Azure Blockchain Service resources are created. |
| **name** | Name of the Azure Blockchain Service blockchain member that also includes the new transaction node name. |
| **localização** | Azure region where the blockchain member is created. Por exemplo, `eastus`. Escolha a localização que estiver mais próxima dos seus utilizadores ou das suas outras aplicações do Azure. |
| **password** | The transaction node password. The password must meet three of the following four requirements: length needs to be between 12 & 72 characters, 1 lower case character, 1 upper case character, 1 number, and 1 special character that is not number sign(#), percent(%), comma(,), star(*), back quote(\`), double quote("), single quote('), dash(-) and semicolon(;). |
| **ruleName** | Rule name for whitelisting an IP address range. Optional parameter for firewall rules. |
| **startIpAddress** | Start of the IP address range for whitelisting. Optional parameter for firewall rules. |
| **endIpAddress** | End of the IP address range for whitelisting. Optional parameter for firewall rules.|

## <a name="change-transaction-node-password"></a>Change transaction node password

Example changes a transaction node password.

```azurecli
az resource update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>/transactionNodes/<myTransactionNode> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --set properties.password='<myStrongPassword>'
```

| Parâmetro | Descrição |
|---------|-------------|
| **resource-group** | Resource group name where Azure Blockchain Service resources exist. |
| **name** | Name of the Azure Blockchain Service blockchain member that also includes the new transaction node name. |
| **password** | The transaction node password. The password must meet three of the following four requirements: length needs to be between 12 & 72 characters, 1 lower case character, 1 upper case character, 1 number, and 1 special character that is not number sign(#), percent(%), comma(,), star(*), back quote(\`), double quote("), single quote('), dash(-) and semicolon(;). |

## <a name="change-consortium-management-account-password"></a>Change consortium management account password

The consortium management account is used for consortium membership management. Each member is uniquely identified by a consortium management account and you can change the password of this account with the following command.

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
| **resource-group** | Resource group name where Azure Blockchain Service resources are created. |
| **name** | Name that identifies your Azure Blockchain Service member. |
| **consortiumManagementAccountPassword** | The consortium management account password. The password must meet three of the following four requirements: length needs to be between 12 & 72 characters, 1 lower case character, 1 upper case character, 1 number, and 1 special character that is not number sign(#), percent(%), comma(,), star(*), back quote(\`), double quote("), single quote('), dash(-) and semicolon(;). |
  
## <a name="update-firewall-rules"></a>Update firewall rules

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
| **resource-group** | Resource group name where Azure Blockchain Service resources exist. |
| **name** | Name of the Azure Blockchain Service blockchain member. |
| **ruleName** | Rule name for whitelisting an IP address range. Optional parameter for firewall rules.|
| **startIpAddress** | Start of the IP address range for whitelisting. Optional parameter for firewall rules.|
| **endIpAddress** | End of the IP address range for whitelisting. Optional parameter for firewall rules.|

## <a name="list-api-keys"></a>List API keys

API keys can be used for node access similar to user name and password. There are two API keys to support key rotation. Use the following command to list your API keys.

```azurecli
az resource invoke-action \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName>/transactionNodes/<myTransactionNode> \
                            --action "listApiKeys" \
                            --resource-type Microsoft.Blockchain/blockchainMembers
```

| Parâmetro | Descrição |
|---------|-------------|
| **resource-group** | Resource group name where Azure Blockchain Service resources exist. |
| **name** | Name of the Azure Blockchain Service blockchain member that also includes the new transaction node name. |

## <a name="regenerate-api-keys"></a>Regenerate API keys

Use the following command to regenerate your API keys.

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
| **resource-group** | Resource group name where Azure Blockchain Service resources exist. |
| **name** | Name of the Azure Blockchain Service blockchain member that also includes the new transaction node name. |
| **keyName** | Replace \<keyValue\> with either key1 or key2. |

## <a name="delete-a-transaction-node"></a>Delete a transaction node

Example deletes a blockchain member transaction node.

```azurecli
az resource delete \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>/transactionNodes/<myTransactionNode> \
                     --resource-type Microsoft.Blockchain/blockchainMembers
```

| Parâmetro | Descrição |
|---------|-------------|
| **resource-group** | Resource group name where Azure Blockchain Service resources exist. |
| **name** | Name of the Azure Blockchain Service blockchain member that also includes the transaction node name to be deleted. |

## <a name="delete-a-blockchain-member"></a>Delete a blockchain member

Example deletes a blockchain member.

```azurecli
az resource delete \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers
```

| Parâmetro | Descrição |
|---------|-------------|
| **resource-group** | Resource group name where Azure Blockchain Service resources exist. |
| **name** | Name of the Azure Blockchain Service blockchain member to be deleted. |

## <a name="azure-active-directory"></a>Azure Active Directory

### <a name="grant-access-for-azure-ad-user"></a>Grant access for Azure AD user

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee <assignee> \
                            --scope /subscriptions/<subId>/resourceGroups/<groupName>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>
```

| Parâmetro | Descrição |
|---------|-------------|
| **role** | Name of the Azure AD role. |
| **assignee** | Azure AD user ID. Por exemplo, `user@contoso.com` |
| **scope** | Scope of the role assignment. Can be either a blockchain member or transaction node. |

**Exemplo:**

Grant node access for Azure AD user to blockchain **member**:

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

**Exemplo:**

Grant node access for Azure AD user to blockchain **transaction node**:

```azurecli
az role assignment create \
                            --role 'MyRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1/transactionNodes/contosoTransactionNode1
```

### <a name="grant-node-access-for-azure-ad-group-or-application-role"></a>Grant node access for Azure AD group or application role

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee-object-id <assignee_object_id>
```

| Parâmetro | Descrição |
|---------|-------------|
| **role** | Name of the Azure AD role. |
| **assignee-object-id** | Azure AD group ID or application ID. |
| **scope** | Scope of the role assignment. Can be either a blockchain member or transaction node. |

**Exemplo:**

Grant node access for **application role**

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee-object-id 22222222-2222-2222-2222-222222222222 \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

### <a name="remove-azure-ad-node-access"></a>Remove Azure AD node access

```azurecli
az role assignment delete \
                            --role <myRole> \
                            --assignee <assignee> \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/<myResourceGroup>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>/transactionNodes/<myTransactionNode>
```

| Parâmetro | Descrição |
|---------|-------------|
| **role** | Name of the Azure AD role. |
| **assignee** | Azure AD user ID. Por exemplo, `user@contoso.com` |
| **scope** | Scope of the role assignment. Can be either a blockchain member or transaction node. |

## <a name="next-steps"></a>Passos seguintes

Learn how to [Configure Azure Blockchain Service transaction nodes with the Azure portal](configure-transaction-nodes.md).
