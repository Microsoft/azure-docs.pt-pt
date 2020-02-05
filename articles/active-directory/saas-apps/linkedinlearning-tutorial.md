---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com linkedIn learning  Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o LinkedIn Learning.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d5857070-bf79-4bd3-9a2a-4c1919a74946
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 15509866980cdf85f54fc03cb77eed36f83c982f
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76983372"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-linkedin-learning"></a>Tutorial: Azure Ative Diretório integração de inscrição única (SSO) com linkedIn learning

Neste tutorial, aprenderá a integrar o LinkedIn Learning com o Azure Ative Directory (Azure AD). Quando integrar o LinkedIn Learning com o Azure AD, pode:

* Controlo em Azure AD que tem acesso ao LinkedIn Learning.
* Ative que os seus utilizadores sejam automaticamente inscritos no LinkedIn Learning com as suas contas Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* LinkedIn Learning single sign-on (SSO) enabled subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* LinkedIn Learning suporta **SP e IDP** iniciadoS SSO
* LinkedIn Learning suporta o fornecimento de utilizadores **justo no tempo**
* Assim que configurar o LinkedIn Learning, pode impor o controlo de Sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controle de sessão com Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-linkedin-learning-from-the-gallery"></a>Adicionar LinkedIn Learning a partir da galeria

Para configurar a integração do LinkedIn Learning em Azure AD, precisa de adicionar linkedIn Learning da galeria à sua lista de aplicações geridas do SaaS.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. No **Add da** secção galeria, digite **LinkedIn Learning** na caixa de pesquisa.
1. Selecione **LinkedIn Learning** a partir do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on-for-linkedin-learning"></a>Configure e teste Azure AD único sign-on para LinkedIn Learning

Configure e teste Azure AD SSO com LinkedIn Learning utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no LinkedIn Learning.

Para configurar e testar o Azure AD SSO com o LinkedIn Learning, complete os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configure linkedIn Learning SSO](#configure-linkedin-learning-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    1. **[Create LinkedIn Learning test user](#create-linkedin-learning-test-user)** - para ter uma contrapartida de B.Simon no LinkedIn Learning que está ligada à representação do utilizador da AD Azure.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **LinkedIn Learning,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , se você quiser configurar o aplicativo no modo iniciado pelo **IDP** , insira os valores para os seguintes campos:

     a. Na caixa de texto **Identificador,** introduza o ID da **Entidade** copiado do Portal LinkedIn. 

    b. Na caixa de texto **URL resposta,** introduza o Url do Serviço de **Consumidores de Afirmação (ACS)** copiado do Portal LinkedIn.

    c. Se desejar configurar a aplicação no modo **Iniciado SP,** clique em definir a opção **URLs adicionais** na secção **de Configuração SAML Básica,** onde irá especificar o URL de início de sessão. Para criar o seu Url de login, copie o Url do **Serviço de Consumo de Afirmação (ACS)** e substitua/saml/ por /login/. Uma vez feito, o URL de inscrição deve ter o seguinte padrão:

    `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=learning&applicationInstanceId=<InstanceId>`

    ![LinkedIn Learning Domain e URLs informações únicas de inscrição](common/metadata-upload-additional-signon.png)

    > [!NOTE]
    > Estes valores não são de valor real. Atualizará estes valores com o URL de Identificação e Resposta real, o que é explicado mais tarde na secção **Configure LinkedIn Learning SSO** do tutorial.

1. A aplicação LinkedIn Learning espera as afirmações do SAML num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão, onde o **nameidentifier** é mapeado com **User. UserPrincipalName**. A aplicação LinkedIn Learning espera que o **identificador** de nomes seja mapeado com **user.mail**, por isso precisa editar o mapeamento do atributo clicando no ícone **Editar** e alterar o mapeamento do atributo.

    ![imagem](common/edit-attribute.png)

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **XML de metadados de Federação** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/metadataxml.png)

1. Na secção **Deconfiguração do LinkedIn Learning,** copie os URL(s) adequados com base no seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B. Simon.

1. No painel esquerdo na portal do Azure, selecione **Azure Active Directory**, selecione **usuários**e, em seguida, selecione **todos os usuários**.
1. Selecione **novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **usuário** , siga estas etapas:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome de usuário** , insira o username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **senha** .
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, permitirá que b.Simon utilize um único sign-on Azure, concedendo acesso ao LinkedIn Learning.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicações, selecione **LinkedIn Learning**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-linkedin-learning-sso"></a>Configure LinkedIn Learning SSO

1. Numa janela de navegador web diferente, inscreva-se no seu inquilino linkedIn Learning como administrador.

2. No **centro de contas**, clique em **configurações globais** em **configurações**. Além disso, selecione **Learning - Padrão** da lista de dropdown.

    ![Configurar o início de sessão único](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_01.png)

3. Clique **em OU Clique aqui para carregar e copiar campos individuais do formulário** e copiar o Url do Serviço de Consumidores **id** e de **afirmação (ACS)** e cole-o na secção **de Configuração Básica SAML** no portal Azure.

    ![Configurar o início de sessão único](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_03.png)

4. Acesse a seção **configurações de administração do LinkedIn** . Faça upload do ficheiro XML que descarregou do portal Azure clicando na opção de **ficheiro Upload XML.**

    ![Configurar o início de sessão único](./media/linkedinlearning-tutorial/tutorial_linkedin_metadata_03.png)

5. Clique **em** ativar para habilitar o SSO. O status do SSO muda de **não conectado** para **conectado**

    ![Configurar o início de sessão único](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_05.png)

### <a name="create-linkedin-learning-test-user"></a>Criar o utilizador de teste linkedIn learning

A Aplicação de Aprendizagem LinkedIn suporta apenas a tempo o fornecimento de utilizadores e após a autenticação que os utilizadores são criados automaticamente na aplicação. Na página de definições de administração no portal LinkedIn Learning, ligue o interruptor **Automaticamente Atribuir licenças** para activaaa apenas no fornecimento de tempo e isso também atribuirá uma licença ao utilizador.

   ![Criar um utilizador de teste do Azure AD](./media/linkedinlearning-tutorial/LinkedinUserprovswitch.png)

## <a name="test-sso"></a>Testar SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clicar no azulejo LinkedIn Learning no Painel de Acesso, deve ser automaticamente inscrito no LinkedIn Learning para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o LinkedIn Learning com o Azure AD](https://aad.portal.azure.com/)

- [O que é o controle de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)