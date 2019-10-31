---
title: 'Tutorial: integração do Azure Active Directory com o Fidelity netbenefits | Microsoft Docs'
description: Saiba como configurar o logon único entre Azure Active Directory e fidelidade netbenefits.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 77dc8a98-c0e7-4129-ab88-28e7643e432a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 40c1e9d493b0fb7bbf1e484799d56e91da1d0833
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73156321"
---
# <a name="tutorial-azure-active-directory-integration-with-fidelity-netbenefits"></a>Tutorial: integração do Azure Active Directory com o Fidelity netbenefits

Neste tutorial, você aprenderá a integrar o Fidelity netbenefits ao Azure Active Directory (Azure AD).
A integração do Fidelity netbenefits ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao Fidelity netbenefits.
* Você pode permitir que seus usuários façam logon automaticamente no Fidelity netbenefits (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um local central-a portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Fidelity netbenefits, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do Fidelity netbenefits

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* Fidelidade netbenefits dá suporte ao SSO iniciado pelo **IDP**

* O Fidelity netbenefits dá suporte ao provisionamento **de usuário just in time**

## <a name="adding-fidelity-netbenefits-from-the-gallery"></a>Adicionando fidelidade netbenefits da Galeria

Para configurar a integração do Fidelity netbenefits ao Azure AD, você precisará adicionar o Fidelity netbenefits da Galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Fidelity netbenefits da galeria, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique no ícone de **Azure Active Directory** .

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **aplicativos empresariais** e, em seguida, selecione a opção **todos os aplicativos** .

    ![A folha aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, clique no botão **novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Fidelity Netbenefits**, selecione **fidelidade netbenefits** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Fidelidade netbenefits na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Fidelity netbenefits com base em um usuário de teste chamado **Brenda Simon**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Fidelity netbenefits.

Para configurar e testar o logon único do Azure AD com o Fidelity netbenefits, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure ad](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do Fidelity Netbenefits](#configure-fidelity-netbenefits-single-sign-on)** -para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com o Brenda Simon.
4. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para permitir que Brenda Simon use o logon único do Azure AD.
5. **[Criar usuário de teste do Fidelity Netbenefits](#create-fidelity-netbenefits-test-user)** – para ter um equivalente de Brenda Simon no Fidelity netbenefits que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Fidelity netbenefits, execute as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Fidelity netbenefits** , selecione **logon único**.

    ![Link configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-enalimentado** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com SAML** , clique no ícone **Editar** para abrir a caixa de diálogo **configuração básica de SAML** .

    ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na página **Configurar logon único com SAML** , execute as seguintes etapas:

    ![Informações de logon único de domínio e URLs do Fidelity netbenefits](common/idp-intiated.png)

    a. Na caixa de texto **identificador** , digite uma URL usando o seguinte padrão:

    Para o ambiente de teste: `urn:sp:fidelity:geninbndnbparts20:uat:xq1`

    Para o ambiente de produção: `urn:sp:fidelity:geninbndnbparts20`

    b. Na caixa de texto **URL de resposta** , digite uma URL a ser fornecida pela fidelidade no momento da implementação ou entre em contato com o cliente de fidelidade atribuído Service Manager.

5. O aplicativo Fidelity netbenefits espera as asserções SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão, onde o **nameidentifier** é mapeado com **User. UserPrincipalName**. O aplicativo Fidelity netbenefits espera que **nameidentifier** seja mapeado com **EmployeeID** ou qualquer outra declaração que seja aplicável à sua organização como **nameidentifier**, portanto, você precisa editar o mapeamento de atributos clicando em **Editar** ícone e alterar o mapeamento de atributo.

    ![imagem](common/edit-attribute.png)

    >[!Note]
    >A fidelidade netbenefits dá suporte à Federação estática e dinâmica. Estático significa que ele não usará o provisionamento de usuário just in time e dinâmicos significa que ele dá suporte ao provisionamento de usuário just-in-time. Para usar o provisionamento baseado em JIT, os clientes precisam adicionar mais algumas declarações no Azure AD como a DataDeNascimento do usuário, etc. Esses detalhes são fornecidos pelo **cliente de fidelidade atribuído Service Manager** e precisam habilitar essa Federação dinâmica para sua instância.

6. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o XML de **metadados de Federação** das opções determinadas de acordo com seu requisito e salvá-lo em seu computador.

    ![O link de download do certificado](common/metadataxml.png)

7. Na seção **Configurar a fidelidade Netbenefits** , copie as URLs apropriadas de acordo com seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logout

### <a name="configure-fidelity-netbenefits-single-sign-on"></a>Configurar o logon único do Fidelity netbenefits

Para configurar o logon único no lado do **Fidelity Netbenefits** , é necessário enviar o **XML de metadados de Federação** baixado e as URLs copiadas apropriadas de portal do Azure para a equipe de suporte do [Fidelity netbenefits](mailto:SSOMaintenance@fmr.com). Eles definem essa configuração para que a conexão de SSO do SAML seja definida corretamente em ambos os lados.

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

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo acesso ao Fidelity netbenefits.

1. Na portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **fidelidade netbenefits**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **fidelidade Netbenefits**.

    ![O link de fidelidade netbenefits na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![O link "usuários e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar usuário** e selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **usuários e grupos** , selecione **Brenda Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.

6. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-fidelity-netbenefits-test-user"></a>Criar usuário de teste do Fidelity netbenefits

Nesta seção, você criará um usuário chamado Brenda Simon no Fidelity netbenefits. Se você estiver criando uma federação estática, trabalhe com seu **cliente de fidelidade atribuído Service Manager** para criar usuários na plataforma de fidelidade netbenefits. Esses usuários devem ser criados e ativados antes de usar o logon único.

Para a Federação dinâmica, os usuários são criados usando o provisionamento de usuário just in time. Para usar o provisionamento baseado em JIT, os clientes precisam adicionar mais algumas declarações no Azure AD como a DataDeNascimento do usuário, etc. Esses detalhes são fornecidos pelo **cliente de fidelidade atribuído Service Manager** e precisam habilitar essa Federação dinâmica para sua instância.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Quando você clica no bloco fidelidade netbenefits no painel de acesso, você deve entrar automaticamente na fidelidade netbenefits para a qual você configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

