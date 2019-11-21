---
title: 'Tutorial: Azure Active Directory single sign-on (SSO) integration with Sugar CRM | Microsoft Docs'
description: Learn how to configure single sign-on between Azure Active Directory and Sugar CRM.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3331b9fc-ebc0-4a3a-9f7b-bf20ee35d180
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fae7b80fd4d2fcec32bbef5e4cdf18e576412a86
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231967"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sugar-crm"></a>Tutorial: Azure Active Directory single sign-on (SSO) integration with Sugar CRM

In this tutorial, you'll learn how to integrate Sugar CRM with Azure Active Directory (Azure AD). When you integrate Sugar CRM with Azure AD, you can:

* Control in Azure AD who has access to Sugar CRM.
* Enable your users to be automatically signed-in to Sugar CRM with their Azure AD accounts.
* Manage your accounts in one central location - the Azure portal.

To learn more about SaaS app integration with Azure AD, see [What is application access and single sign-on with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

To get started, you need the following items:

* An Azure AD subscription. If you don't have a subscription, you can get a [free account](https://azure.microsoft.com/free/).
* Sugar CRM single sign-on (SSO) enabled subscription.

## <a name="scenario-description"></a>Descrição do cenário

In this tutorial, you configure and test Azure AD SSO in a test environment.

* Sugar CRM supports **SP** initiated SSO

> [!NOTE]
> Identifier of this application is a fixed string value so only one instance can be configured in one tenant.

## <a name="adding-sugar-crm-from-the-gallery"></a>Adding Sugar CRM from the gallery

To configure the integration of Sugar CRM into Azure AD, you need to add Sugar CRM from the gallery to your list of managed SaaS apps.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. On the left navigation pane, select the **Azure Active Directory** service.
1. Navigate to **Enterprise Applications** and then select **All Applications**.
1. To add new application, select **New application**.
1. In the **Add from the gallery** section, type **Sugar CRM** in the search box.
1. Select **Sugar CRM** from results panel and then add the app. Wait a few seconds while the app is added to your tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sugar-crm"></a>Configure and test Azure AD single sign-on for Sugar CRM

Configure and test Azure AD SSO with Sugar CRM using a test user called **B.Simon**. For SSO to work, you need to establish a link relationship between an Azure AD user and the related user in Sugar CRM.

To configure and test Azure AD SSO with Sugar CRM, complete the following building blocks:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - to enable your users to use this feature.
    1. **[Create an Azure AD test user](#create-an-azure-ad-test-user)** - to test Azure AD single sign-on with B.Simon.
    1. **[Assign the Azure AD test user](#assign-the-azure-ad-test-user)** - to enable B.Simon to use Azure AD single sign-on.
1. **[Configure Sugar CRM SSO](#configure-sugar-crm-sso)** - to configure the single sign-on settings on application side.
    1. **[Create Sugar CRM test user](#create-sugar-crm-test-user)** - to have a counterpart of B.Simon in Sugar CRM that is linked to the Azure AD representation of user.
1. **[Test SSO](#test-sso)** - to verify whether the configuration works.

## <a name="configure-azure-ad-sso"></a>Configure Azure AD SSO

Follow these steps to enable Azure AD SSO in the Azure portal.

1. In the [Azure portal](https://portal.azure.com/), on the **Sugar CRM** application integration page, find the **Manage** section and select **single sign-on**.
1. On the **Select a single sign-on method** page, select **SAML**.
1. On the **Set up single sign-on with SAML** page, click the edit/pen icon for **Basic SAML Configuration** to edit the settings.

   ![Edit Basic SAML Configuration](common/edit-urls.png)

1. On the **Basic SAML Configuration** section, enter the values for the following fields:

    a. In the **Sign-on URL** text box, type a URL using the following pattern:

    | |
    |--|
    | `https://<companyname>.sugarondemand.com`|
    | `https://<companyname>.trial.sugarcrm`|

    b. In the **Reply URL** text box, type a URL using the following pattern:

    | |
    |--|
    | `https://<companyname>.sugarondemand.com/<companyname>`|
    | `https://<companyname>.trial.sugarcrm.com/<companyname>`|
    | `https://<companyname>.trial.sugarcrm.eu/<companyname>`|

    > [!NOTE]
    > These values are not real. Update these values with the actual Sign-On URL and Reply URL. Contact [Sugar CRM Client support team](https://support.sugarcrm.com/) to get these values. You can also refer to the patterns shown in the **Basic SAML Configuration** section in the Azure portal.

1. On the **Set up single sign-on with SAML** page, in the **SAML Signing Certificate** section,  find **Certificate (Base64)** and select **Download** to download the certificate and save it on your computer.

    ![The Certificate download link](common/certificatebase64.png)

1. On the **Set up Sugar CRM** section, copy the appropriate URL(s) based on your requirement.

    ![Copy configuration URLs](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Create an Azure AD test user

In this section, you'll create a test user in the Azure portal called B.Simon.

1. From the left pane in the Azure portal, select **Azure Active Directory**, select **Users**, and then select **All users**.
1. Select **New user** at the top of the screen.
1. In the **User** properties, follow these steps:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. In the **User name** field, enter the username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Select the **Show password** check box, and then write down the value that's displayed in the **Password** box.
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Assign the Azure AD test user

In this section, you'll enable B.Simon to use Azure single sign-on by granting access to Sugar CRM.

1. In the Azure portal, select **Enterprise Applications**, and then select **All applications**.
1. In the applications list, select **Sugar CRM**.
1. In the app's overview page, find the **Manage** section and select **Users and groups**.

   ![The "Users and groups" link](common/users-groups-blade.png)

1. Select **Add user**, then select **Users and groups** in the **Add Assignment** dialog.

    ![The Add User link](common/add-assign-user.png)

1. In the **Users and groups** dialog, select **B.Simon** from the Users list, then click the **Select** button at the bottom of the screen.
1. If you're expecting any role value in the SAML assertion, in the **Select Role** dialog, select the appropriate role for the user from the list and then click the **Select** button at the bottom of the screen.
1. In the **Add Assignment** dialog, click the **Assign** button.

## <a name="configure-sugar-crm-sso"></a>Configure Sugar CRM SSO

1. In a different web browser window, sign in to your Sugar CRM company site as an administrator.

1. Go to **Admin**.

    ![Admin](./media/sugarcrm-tutorial/ic795888.png "Admin")

1. In the **Administration** section, click **Password Management**.

    ![Administration](./media/sugarcrm-tutorial/ic795889.png "Administração")

1. Select **Enable SAML Authentication**.

    ![Administration](./media/sugarcrm-tutorial/ic795890.png "Administração")

1. In the **SAML Authentication** section, perform the following steps:

    ![SAML Authentication](./media/sugarcrm-tutorial/ic795891.png "SAML Authentication")  

    a. In the **Login URL** textbox, paste the value of **Login URL**, which you have copied from Azure portal.
  
    b. In the **SLO URL** textbox, paste the value of **Logout URL**, which you have copied from Azure portal.
  
    c. Open your base-64 encoded certificate in notepad, copy the content of it into your clipboard, and then paste the entire Certificate into **X.509 Certificate** textbox.
  
    d. Clique em **Guardar**.

### <a name="create-sugar-crm-test-user"></a>Create Sugar CRM test user

In order to enable Azure AD users to sign in to Sugar CRM, they must be provisioned to Sugar CRM. In the case of Sugar CRM, provisioning is a manual task.

**To provision a user account, perform the following steps:**

1. Sign in to your **Sugar CRM** company site as administrator.

1. Go to **Admin**.

    ![Admin](./media/sugarcrm-tutorial/ic795888.png "Admin")

1. In the **Administration** section, click **User Management**.

    ![Administration](./media/sugarcrm-tutorial/ic795893.png "Administração")

1. Go to **Users \> Create New User**.

    ![Create New User](./media/sugarcrm-tutorial/ic795894.png "Create New User")

1. On the **User Profile** tab, perform the following steps:

    ![New User](./media/sugarcrm-tutorial/ic795895.png "New User")

    * Type the **user name**, **last name**, and **email address** of a valid Azure Active Directory user into the related textboxes.
  
1. As **Status**, select **Active**.

1. On the Password tab, perform the following steps:

    ![New User](./media/sugarcrm-tutorial/ic795896.png "New User")

    a. Type the password into the related textbox.

    b. Clique em **Guardar**.

> [!NOTE]
> You can use any other Sugar CRM user account creation tools or APIs provided by Sugar CRM to provision Azure AD user accounts.

## <a name="test-sso"></a>Test SSO 

In this section, you test your Azure AD single sign-on configuration using the Access Panel.

When you click the Sugar CRM tile in the Access Panel, you should be automatically signed in to the Sugar CRM for which you set up SSO. For more information about the Access Panel, see [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [What is conditional access in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Try Sugar CRM with Azure AD](https://aad.portal.azure.com/)

