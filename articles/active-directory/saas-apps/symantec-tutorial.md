---
title: 'Tutorial: integração do Azure Active Directory com o Symantec WSS (Web Security Service) | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Symantec WSS (Web Security Service).
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: d6e4d893-1f14-4522-ac20-0c73b18c72a5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/25/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d496015440deb80a0159ed0ec234ae60c2c64a66
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73159942"
---
# <a name="tutorial-azure-active-directory-integration-with-symantec-web-security-service-wss"></a>Tutorial: integração do Azure Active Directory com o Symantec WSS (Web Security Service)

Neste tutorial, você aprenderá a integrar sua conta do Symantec WSS (Web Security Service) à sua conta do Azure Active Directory (AD do Azure) para que o WSS possa autenticar um usuário final provisionado no Azure AD usando a autenticação SAML e impor o usuário ou regras de política de nível de grupo.

A integração do Symantec WSS (Web Security Service) ao Azure AD oferece os seguintes benefícios:

- Gerencie todos os usuários finais e grupos usados por sua conta do WSS do portal do Azure AD.

- Permitir que os usuários finais se autentiquem no WSS usando suas credenciais do Azure AD.

- Habilite a imposição de regras de política de nível de usuário e de grupo definidas em sua conta do WSS.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Symantec WSS (Web Security Service), você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do Symantec WSS (Web Security Service)

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Symantec WSS (Web Security Service) dá suporte ao SSO iniciado pelo **IDP**

## <a name="adding-symantec-web-security-service-wss-from-the-gallery"></a>Adicionando o Symantec WSS (Web Security Service) por meio da Galeria

Para configurar a integração do Symantec WSS (Web Security Service) ao Azure AD, você precisa adicionar o Symantec WSS (Web Security Service) da Galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Symantec WSS (Web Security Service) por meio da galeria, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique no ícone de **Azure Active Directory** .

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **aplicativos empresariais** e, em seguida, selecione a opção **todos os aplicativos** .

    ![A folha aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, clique no botão **novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Symantec WSS (Web Security Service)** , selecione **Symantec WSS (Web Security Service)** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![O Symantec WSS (Web Security Service) na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Symantec WSS (Web Security Service), com base em um usuário de teste chamado **Brenda Simon**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Symantec WSS (Web Security Service).

Para configurar e testar o logon único do Azure AD com o Symantec WSS (Web Security Service), você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure ad](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **Configure o logon único do Symantec WSS (Web Security Service)** – para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com o Brenda Simon.
4. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para permitir que Brenda Simon use o logon único do Azure AD.
5. **[Criar um usuário de teste do Symantec WSS (Web Security Service)](#create-symantec-web-security-service-wss-test-user)** – para ter um equivalente de Brenda Simon no Symantec WSS (Web Security Service) que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Symantec WSS (Web Security Service), execute as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Symantec WSS (Web Security Service)** , selecione **logon único**.

    ![Link configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-enalimentado** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com SAML** , clique no ícone **Editar** para abrir a caixa de diálogo **configuração básica de SAML** .

    ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na caixa de diálogo **configuração básica do SAML** , execute as seguintes etapas:

    ![Informações de logon único de domínio e URLs do Symantec WSS (Web Security Service)](common/idp-intiated.png)

    a. Na caixa de texto **identificador** , digite uma URL: `https://saml.threatpulse.net:8443/saml/saml_realm`

    b. Na caixa de texto **URL de resposta** , digite uma URL: `https://saml.threatpulse.net:8443/saml/saml_realm/bcsamlpost`

    > [!NOTE]
    > Contate a [equipe de suporte ao cliente do Symantec WSS (Web Security Service)](https://www.symantec.com/contact-us) f os valores para o **identificador** e a **URL de resposta** não estão funcionando por algum motivo. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

5. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o XML de **metadados de Federação** das opções determinadas de acordo com seu requisito e salvá-lo em seu computador.

    ![O link de download do certificado](common/metadataxml.png)

### <a name="configure-symantec-web-security-service-wss-single-sign-on"></a>Configurar o logon único do Symantec WSS (Web Security Service)

Para configurar o logon único no lado do Symantec WSS (Web Security Service), consulte a documentação online do WSS. O **XML de metadados de Federação** baixado precisará ser importado para o portal do WSS. Entre em contato com a [equipe de suporte do Symantec WSS (Web Security Service)](https://www.symantec.com/contact-us) se precisar de assistência com a configuração no portal do WSS.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

O objetivo desta seção é criar um usuário de teste no portal do Azure chamado Brenda Simon.

1. No portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **usuários**e, em seguida, selecione **todos os usuários**.

    ![Os links "usuários e grupos" e "todos os usuários"](common/users.png)

2. Selecione **novo usuário** na parte superior da tela.

    ![Botão novo usuário](common/new-user.png)

3. Nas propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo usuário](common/user-properties.png)

    a. No campo **nome** , insira **brendafernandes**.
  
    b. No campo **nome de usuário** , digite **brendafernandes\@yourcompanydomain. Extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e anote o valor exibido na caixa senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo acesso ao Symantec WSS (Web Security Service).

1. Na portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **Symantec WSS (Web Security Service)** .

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, digite e selecione **Symantec WSS (Web Security Service)** .

    ![O link do Symantec WSS (Web Security Service) na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![O link "usuários e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar usuário** e selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **usuários e grupos** , selecione **Brenda Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.

6. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-symantec-web-security-service-wss-test-user"></a>Criar o usuário de teste do Symantec WSS (Web Security Service)

Nesta seção, você criará um usuário chamado Brenda Simon no Symantec WSS (Web Security Service). O nome de usuário final correspondente pode ser criado manualmente no portal do WSS ou você pode aguardar que os usuários/grupos provisionados no Azure AD sejam sincronizados com o portal do WSS após alguns minutos (aproximadamente 15 minutos). Os usuários devem ser criados e ativados antes de usar o logon único. O endereço IP público do computador do usuário final, que será usado para procurar sites também precisará ser provisionado no portal do Symantec WSS (Web Security Service).

> [!NOTE]
> Clique [aqui](https://www.bing.com/search?q=my+ip+address&qs=AS&pq=my+ip+a&sc=8-7&cvid=29A720C95C78488CA3F9A6BA0B3F98C5&FORM=QBLH&sp=1) para obter o IPAddress público do seu computador.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta seção, você testará a funcionalidade de logon único agora que configurou sua conta do WSS para usar o Azure AD para autenticação SAML.

Depois de configurar o navegador da Web para o tráfego de proxy para o WSS, ao abrir o navegador da Web e tentar navegar até um site, você será redirecionado para a página de logon do Azure. Insira as credenciais do usuário final de teste que foi provisionado no Azure AD (ou seja, Brendafernandes) e a senha associada. Depois de autenticado, você poderá navegar até o site que você escolheu. Se você criar uma regra de política no lado do WSS para impedir que o Brendafernandes navegue para um site específico, você deverá ver a página de bloco do WSS quando tentar navegar até esse site como usuário Brendafernandes.

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

