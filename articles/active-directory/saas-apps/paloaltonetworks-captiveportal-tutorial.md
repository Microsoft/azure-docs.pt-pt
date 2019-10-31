---
title: 'Tutorial: integração de Azure Active Directory com o portal cativo de redes Palo Alto | Microsoft Docs'
description: Saiba como configurar o logon único entre Azure Active Directory e o portal cativo de redes Palo Alto.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 67a0b476-2305-4157-8658-2ec3625850d5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/25/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f926741bde3bdcc69cb4ea30f54daca79606047e
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73160158"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks-captive-portal"></a>Tutorial: integração do Azure Active Directory com o portal cativo de redes Palo Alto

Neste tutorial, você aprenderá a integrar o portal cativo do Palo Alto Networks com o Azure Active Directory (Azure AD).

Você Obtém os seguintes benefícios ao integrar o portal cativo do Palo Alto Networks ao Azure AD:

* No Azure AD, você pode controlar quem tem acesso ao portal cativo de redes Palo Alto.
* Você pode conectar automaticamente os usuários no portal cativo de redes Palo Alto (logon único) usando contas de usuário do Azure AD.
* Você pode gerenciar suas contas em um local central, o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS (software como serviço) com o Azure AD, consulte [logon único para aplicativos no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Pré-requisitos

Para integrar o Azure AD ao portal cativo de redes Palo Alto, você precisa dos seguintes itens:

* Uma assinatura Azure Active Directory. Se você não tiver o Azure AD, poderá obter uma [avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).
* Uma assinatura habilitada para SSO (logon único) do portal do Palo Alto Networks.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

O portal cativo de redes Palo Alto dá suporte a esses cenários:

* **Logon único iniciado pelo IDP**
* **Provisionamento de usuário just-in-time**

## <a name="add-palo-alto-networks-captive-portal-from-the-gallery"></a>Adicionar portal cativo de redes Palo Alto por meio da Galeria

Para começar, na Galeria, adicione um portal cativo do Palo Alto Networks à sua lista de aplicativos SaaS gerenciados:

1. No [portal do Azure](https://portal.azure.com), no menu à esquerda, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Selecione **aplicativos empresariais** > **todos os aplicativos**.

    ![A opção aplicativos empresariais no menu](common/enterprise-applications.png)

3. Selecione **Nova aplicação**.

    ![O botão novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Palo Alto Networks cativo portal**. Nos resultados da pesquisa, selecione **Palo Alto Networks – portal cativo**e, em seguida, selecione **Adicionar**.

     ![Palo Alto Networks – portal cativo na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o logon único do Azure AD

Você configura e testa o logon único do Azure AD com o portal cativo de redes Palo Alto, com base em um usuário de teste chamado *Brenda Simon*. Para que o logon único funcione, você deve estabelecer uma relação entre um usuário do Azure AD e o mesmo usuário no portal cativo de redes Palo Alto. 

Para configurar e testar o logon único do Azure AD com o portal cativo de redes Palo Alto, conclua as seguintes tarefas:

1. **[Configurar o logon único do Azure ad](#configure-azure-ad-single-sign-on)** : habilite o usuário a usar esse recurso.
2. **[Configurar logon único do portal cativo de redes Palo Alto](#configure-palo-alto-networks-captive-portal-single-sign-on)** : configurar as configurações de logon único no aplicativo.
3. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** : testar o logon único do Azure AD com o usuário *Brenda Simon*.
4. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** : Configure Brenda Simon para usar o logon único do Azure AD.
5. **Criar um usuário de teste do portal cativo do Palo Alto Networks**: criar um usuário equivalente *Brenda Simon* no Palo Alto Networks cativo portal que está vinculado ao usuário do Azure AD.
6. **[Testar logon único](#test-single-sign-on)** : Verifique se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Primeiro, habilite o logon único do Azure AD no portal do Azure:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **Palo Alto Networks – portal cativo** , selecione **logon único**.

    ![Link configurar logon único](common/select-sso.png)

2. No painel **selecionar um método de logon único** , selecione **SAML**.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. No painel **Configurar logon único com SAML** , selecione o ícone de **edição** de lápis.

    ![O ícone de edição com lápis](common/edit-urls.png)

4. No painel **configuração básica do SAML** , conclua as seguintes etapas:

    ![Painel de configuração SAML de redes do Palo Alto Networks do portal cativo](common/idp-intiated.png)

   1. Para **identificador**, insira uma URL que tenha o padrão `https://<customer_firewall_host_name>/SAML20/SP`.

   2. Para **URL de resposta**, insira uma URL que tenha o padrão `https://<customer_firewall_host_name>/SAML20/SP/ACS`.

      > [!NOTE]
      > Atualize os valores de espaço reservado nesta etapa com o identificador real e as URLs de resposta. Para obter os valores reais, entre em contato com a [equipe de suporte ao cliente do Palo Alto Networks](https://support.paloaltonetworks.com/support).

5. Na seção **certificado de autenticação SAML** , ao lado de **XML de metadados de Federação**, selecione **baixar**. Salve o arquivo baixado em seu computador.

    ![O link de download de XML de metadados de Federação](common/metadataxml.png)

### <a name="configure-palo-alto-networks-captive-portal-single-sign-on"></a>Configurar logon único do portal cativo de redes Palo Alto

Em seguida, configure o logon único no portal cativo de redes Palo Alto:

1. Em uma janela de navegador diferente, entre no site do Palo Alto Networks como administrador.

2. Selecione a guia **dispositivo** .

    ![A guia dispositivo do site do Palo Alto Networks](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin1.png)

3. No menu, selecione **provedor de identidade SAML**e, em seguida, selecione **importar**.

    ![O botão importar](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin2.png)

4. Na caixa de diálogo **importação de perfil do servidor do provedor de identidade SAML** , conclua as seguintes etapas:

    ![Configurar o logon único de redes Palo Alto](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin3.png)

    1. Para **nome do perfil**, insira um nome, como **AzureAD-CaptivePortal**.
    
    2. Ao lado de **metadados do provedor de identidade**, selecione **procurar**. Selecione o arquivo Metadata. XML que você baixou na portal do Azure.
    
    3. Selecione **OK**.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

Em seguida, crie um usuário de teste chamado *Brenda Simon* no portal do Azure:

1. Na portal do Azure, selecione **Azure Active Directory** > **usuários** > **todos os usuários**.

    ![Os links "usuários e grupos" e "todos os usuários"](common/users.png)

2. Selecione **novo usuário**.

    ![O botão novo usuário](common/new-user.png)

3. No painel **usuário** , conclua as seguintes etapas:

    ![A caixa de diálogo usuário](common/user-properties.png)

    1. Para **nome**, insira **brendafernandes**.
  
    2. Para **nome de usuário**, insira **brendafernandes\@\<your_company_domain\>** . Por exemplo, **brendafernandes\@contoso.com**.

    3. Para **senha**, insira uma senha. Recomendamos que você mantenha um registro da senha digitada. Você pode marcar a caixa de seleção **Mostrar senha** para exibir a senha.

    4. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Em seguida, conceda acesso ao portal cativo de redes Palo Alto para que Brenda Simon possa usar o logon único do Azure:

1. Na portal do Azure, selecione **aplicativos empresariais** > **todos os aplicativos**.

    ![O painel aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, insira **Palo Alto Networks – portal cativo**e, em seguida, selecione o aplicativo.

    ![O link do portal do Palo Alto Networks-cativo na lista de aplicativos](common/all-applications.png)

3. No menu, selecione **usuários e grupos**.

    ![O link "usuários e grupos"](common/users-groups-blade.png)

4. Selecione **Adicionar usuário**. Em seguida, no painel **Adicionar atribuição** , selecione **usuários e grupos**.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. No painel **usuários e grupos** , na lista **usuários** , selecione **Brenda Simon**. Selecione **selecionar**.

6. Para adicionar um valor de função à Asserção SAML, no painel **selecionar função** , selecione a função relevante para o usuário. Selecione **selecionar**.

7. No painel **Adicionar atribuição** , selecione **atribuir**.

### <a name="create-a-palo-alto-networks-captive-portal-test-user"></a>Criar um usuário de teste do portal cativo do Palo Alto Networks

Em seguida, crie um usuário chamado *Brenda Simon* no portal cativo de redes do Palo Alto. O portal cativo de redes Palo Alto dá suporte ao provisionamento de usuário just-in-time, que é habilitado por padrão. Você não precisa concluir nenhuma tarefa nesta seção. Se um usuário ainda não existir no portal cativo de redes Palo Alto, um novo será criado após a autenticação.

> [!NOTE]
> Se você quiser criar um usuário manualmente, entre em contato com a [equipe de suporte ao cliente do portal do Palo Alto Networks](https://support.paloaltonetworks.com/support).

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

O portal cativo de redes Palo Alto é instalado atrás do firewall em uma VM do Windows. Para testar o logon único no portal cativo de redes Palo Alto, entre na VM do Windows usando o protocolo RDP (RDP). Na sessão RDP, abra um navegador e vá para qualquer site. A URL de SSO é aberta e você será solicitado a autenticar. Quando a autenticação for concluída, você poderá acessar os sites.

## <a name="additional-resources"></a>Recursos adicionais

Para saber mais, consulte estes artigos:

- [Tutoriais sobre a integração de aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Logon único para aplicativos no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Acesso condicional no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

