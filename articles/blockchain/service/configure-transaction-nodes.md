---
title: Configure Azure Blockchain Service transaction nodes
description: How to configure Azure Blockchain Service transaction nodes
ms.date: 11/20/2019
ms.topic: article
ms.reviewer: janders
ms.openlocfilehash: 4a9a4f660dd171e65b600ec4cd66714ca476b091
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326310"
---
# <a name="configure-azure-blockchain-service-transaction-nodes"></a>Configure Azure Blockchain Service transaction nodes

Transaction nodes are used to send blockchain transactions to Azure Blockchain Service through a public endpoint. The default transaction node contains the private key of the Ethereum account registered on the blockchain, and as such cannot be deleted.

To view the default transaction node details:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Navigate to your Azure Blockchain Service member. Select **Transaction nodes**.

    ![Select default transaction node](./media/configure-transaction-nodes/nodes.png)

    Overview details include public endpoint addresses and public key.

## <a name="create-transaction-node"></a>Create transaction node

You can add up to nine additional transaction nodes to your blockchain member, for a total of 10 transaction nodes. By adding transaction nodes, you can increase scalability or distribute load. For example, you could have a transaction node endpoint for different client applications.

To add a transaction node:

1. In the Azure portal, navigate to your Azure Blockchain Service member and select **Transaction nodes > Add**.
1. Complete the settings for the new transaction node.

    ![Add transaction node](./media/configure-transaction-nodes/add-node.png)

    | Definição | Descrição |
    |---------|-------------|
    | Nome | Transaction node name. The name is used to create the DNS address for the transaction node endpoint. Por exemplo, `newnode-myblockchainmember.blockchain.azure.com`. The node name cannot be changed once it is created. |
    | Palavra-passe | Set a strong password. Use the password to access the transaction node endpoint with basic authentication.

1. Selecione **Criar**.

    Provisioning a new transaction node takes about 10 minutes. Additional transaction nodes incur cost. For more information on costs, see [Azure pricing](https://aka.ms/ABSPricing).

## <a name="endpoints"></a>Pontos Finais

Transaction nodes have a unique DNS name and public endpoints.

To view a transaction node's endpoint details:

1. In the Azure portal, navigate to one of your Azure Blockchain Service member transaction nodes and select **Overview**.

    ![Pontos Finais](./media/configure-transaction-nodes/endpoints.png)

Transaction node endpoints are secure and require authentication. You can connect to a transaction endpoint using Azure AD authentication, HTTPS basic authentication, and using an access key over HTTPS or Websocket over SSL.

### <a name="azure-active-directory-access-control"></a>Controlo de acesso do Azure Active Directory

Azure Blockchain Service transaction node endpoints support Azure Active Directory (Azure AD) authentication. You can grant Azure AD user, group, and service principal access to your endpoint.

To grant Azure AD access control to your endpoint:

1. In the Azure portal, navigate to your Azure Blockchain Service member and select **Transaction nodes > Access control (IAM) > Add > Add role assignment**.
1. Create a new role assignment for a user, group, or service principal (application roles).

    ![Add IAM role](./media/configure-transaction-nodes/add-role.png)

    | Definição | Ação |
    |---------|-------------|
    | Função | Select **Owner**, **Contributor**, or **Reader**.
    | Assign access to | Select **Azure AD user, group, or service principal**.
    | Selecione | Search for the user, group, or service principal you want to add.

1. Select **Save** to add the role assignment.

For more information on Azure AD access control, see [Manage access to Azure resources using RBAC and the Azure portal](../../role-based-access-control/role-assignments-portal.md)

For details on how to connect using Azure AD authentication, see [connect to your node using AAD authentication](configure-aad.md).

### <a name="basic-authentication"></a>Autenticação básica

For HTTPS basic authentication, user name and password credentials are passed in the HTTPS header of the request to the endpoint.

You can view a transaction node's basic authentication endpoint details in the Azure portal. Navigate to one of your Azure Blockchain Service member transaction nodes and select **Basic Authentication** in settings.

![Autenticação básica](./media/configure-transaction-nodes/basic.png)

The user name is the name of your node and cannot be changed.

To use the URL, replace \<password\> with the password set when the node was provisioned. You can update the password by selecting **Reset password**.

### <a name="access-keys"></a>Chaves de acesso

For access key authentication, the access key is included in the endpoint URL. When the transaction node is provisioned, two access keys are generated. Either access key can be used for authentication. Two keys enable you to change and rotate keys.

You can view a transaction node's access key details and copy endpoint addresses that include the access keys. Navigate to one of your Azure Blockchain Service member transaction nodes and select **Access Keys** in settings.

### <a name="firewall-rules"></a>Regras da firewall

Firewall rules enable you to limit the IP addresses that can attempt to authenticate to your transaction node.  If no firewall rules are configured for your transaction node, it cannot be accessed by any party.  

To view a transaction node's firewall rules, navigate to one of your Azure Blockchain Service member transaction nodes and select **Firewall rules** in settings.

You can add firewall rules by entering a rule name, starting IP address, and an ending IP address in the **Firewall rules** grid.

![Regras da firewall](./media/configure-transaction-nodes/firewall-rules.png)

To enable:

* **Single IP address:** Configure the same IP address for the starting and ending IP addresses.
* **IP address range:** Configure the starting and ending IP address range. For example, a range starting at 10.221.34.0 and ending at 10.221.34.255 would enable the entire 10.221.34.xxx subnet.
* **Allow all IP addresses:** Configure the starting IP address to 0.0.0.0 and the ending IP address to 255.255.255.255.

## <a name="connection-strings"></a>Cadeias de ligação

Connection string syntax for your transaction node is provided for basic authentication or using access keys. Connection strings including access keys over HTTPS and WebSockets are provided.

You can view a transaction node's connection strings and copy endpoint addresses. Navigate to one of your Azure Blockchain Service member transaction nodes and select **Connection strings** in settings.

![Cadeias de ligação](./media/configure-transaction-nodes/connection-strings.png)

## <a name="sample-code"></a>Código de exemplo

Sample code is provided to quickly enable connecting to your transaction node via Web3, Nethereum, Web3js, and Truffle.

You can view a transaction node's sample connection code and copy it to use with popular developer tools. Go to one of your Azure Blockchain Service member transaction nodes and select **Sample Code** in settings.

Choose the Web3, Nethereum, Truffle, or Web3j tab to view the code sample you want to use.

![Código de exemplo](./media/configure-transaction-nodes/sample-code.png)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Configure transaction nodes using Azure CLI](manage-cli.md)
