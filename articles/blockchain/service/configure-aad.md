---
title: Configure Azure Active Directory access - Azure Blockchain Service
description: How to configure Azure Blockchain Service with Azure Active Directory access
ms.date: 11/22/2019
ms.topic: article
ms.reviewer: janders
ms.openlocfilehash: 682ab282036fcd592e66942d08a84cdce46d8915
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455863"
---
# <a name="how-to-configure-azure-active-directory-access-for-azure-blockchain-service"></a>How to configure Azure Active Directory access for Azure Blockchain Service

In this article, you learn how to grant access and connect to Azure Blockchain Service nodes using Azure Active Directory (Azure AD) user, group, or application IDs.

Azure AD provides cloud-based identity management and allows you to use a single identity across an entire enterprise and access applications in Azure. Azure Blockchain Service is integrated with Azure AD and offers benefits such as ID federation, single sign-on and multi-factor authentication.

## <a name="prerequisites"></a>Pré-requisitos

* [Create a blockchain member using the Azure portal](create-member.md)

## <a name="grant-access"></a>Conceder acesso

You can grant access at both the member level and the node level. Granting access rights at the member level will in turn grant access to all nodes under the member.

### <a name="grant-member-level-access"></a>Grant member level access

To grant access permission at the member level.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Navigate to **Access control (IAM) > Add > Add role assignment**.
1. Select the **Blockchain Member Node Access (Preview)** role and add the Azure AD ID object you wish to grant access to. Azure AD ID object can be:

    | Azure AD object | Exemplo |
    |-----------------|---------|
    | Azure AD user   | `kim@contoso.onmicrosoft.com` |
    | Azure AD group  | `sales@contoso.onmicrosoft.com` |
    | ID da aplicação  | `13925ab1-4161-4534-8d18-812f5ca1ab1e` |

    ![Add role assignment](./media/configure-aad/add-role-assignment.png)

1. Selecione **Guardar**.

### <a name="grant-node-level-access"></a>Grant node level access

You can grant access at the node level by navigating to node security and click on the node name that you wish to grant access.

Select the Blockchain Member Node Access (Preview) role and add the Azure AD ID object you wish to grant access to.

For more information, see [Configure Azure Blockchain Service transaction nodes](configure-transaction-nodes.md#azure-active-directory-access-control).

## <a name="connect-using-azure-blockchain-connector"></a>Connect using Azure Blockchain Connector

Download or clone the [Azure Blockchain Connector from GitHub](https://github.com/Microsoft/azure-blockchain-connector/).

```bash
git clone https://github.com/Microsoft/azure-blockchain-connector.git
```

The follow the quickstart section in the **readme** to build the connector from the source code.

### <a name="connect-using-an-azure-ad-user-account"></a>Connect using an Azure AD user account

1. Run the following command to authenticate using an Azure AD user account. Replace \<myAADDirectory\> with an Azure AD domain. Por exemplo, `yourdomain.onmicrosoft.com`.

    ```
    connector.exe -remote <myMemberName>.blockchain.azure.com:3200 -method aadauthcode -tenant-id <myAADDirectory> 
    ```

1. Azure AD prompts for credentials.
1. Sign in with your user name and password.
1. Upon successful authentication, your local proxy connects to your blockchain node. You can now attach your Geth client with the local endpoint.

    ```bash
    geth attach http://127.0.0.1:3100
    ```

### <a name="connect-using-an-application-id"></a>Connect using an application ID

Many applications authenticate with Azure AD using an application ID instead of an Azure AD user account.

To connect to your node using an application ID, replace **aadauthcode** with **aadclient**.

```
connector.exe -remote <myBlockchainEndpoint>  -method aadclient -client-id <myClientID> -client-secret "<myClientSecret>" -tenant-id <myAADDirectory>
```

| Parâmetro | Descrição |
|-----------|-------------|
| tenant-id | Azure AD domain, For example, `yourdomain.onmicrosoft.com`
| client-id | Client ID of the registered application in Azure AD
| client-secret | Client secret of the registered application in Azure AD

For more information on how to register an application in Azure AD, see [How to: Use the portal to create an Azure AD application and service principal that can access resources](../../active-directory/develop/howto-create-service-principal-portal.md)

### <a name="connect-a-mobile-device-or-text-browser"></a>Connect a mobile device or text browser

For a mobile device or text-based browser where the Azure AD authentication pop-up display is not possible, Azure AD generates a one-time passcode. You can copy the passcode and proceed with Azure AD authentication in another environment.

To generate the passcode, replace **aadauthcode** with **aaddevice**. Replace \<myAADDirectory\> with an Azure AD domain. Por exemplo, `yourdomain.onmicrosoft.com`.

```
connector.exe -remote <myBlockchainEndpoint>  -method aaddevice -tenant-id <myAADDirectory>
```

## <a name="next-steps"></a>Passos seguintes

For more information about data security in Azure Blockchain Service, see [Azure Blockchain Service security](data-security.md).