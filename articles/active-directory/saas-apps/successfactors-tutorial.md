---
title: 'Tutorial: Azure Active Directory integração de SSO (logon único) com SuccessFactors | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o SuccessFactors.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 32bd8898-c2d2-4aa7-8c46-f1f5c2aa05f1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/13/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 38d40a2f72e73dde0f99ebbc9701e02c8d03738b
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68989496"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-successfactors"></a>Tutorial: Azure Active Directory integração de SSO (logon único) com SuccessFactors

Neste tutorial, você aprenderá a integrar o SuccessFactors com o Azure Active Directory (Azure AD). Ao integrar o SuccessFactors ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao SuccessFactors.
* Habilite seus usuários a serem conectados automaticamente ao SuccessFactors com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do SuccessFactors.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O SuccessFactors dá suporte ao SSO iniciado por **SP**

## <a name="adding-successfactors-from-the-gallery"></a>Adicionando o SuccessFactors da Galeria

Para configurar a integração do SuccessFactors ao Azure AD, você precisará adicionar o SuccessFactors da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **SuccessFactors** na caixa de pesquisa.
1. Selecione **SuccessFactors** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-successfactors"></a>Configurar e testar o SSO do Azure AD para SuccessFactors

Configure e teste o SSO do Azure AD com o SuccessFactors usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no SuccessFactors.

Para configurar e testar o SSO do Azure AD com o SuccessFactors, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
2. **[Configurar o SSO do SuccessFactors](#configure-successfactors-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste do SuccessFactors](#create-successfactors-test-user)** – para ter um equivalente de B. Simon em SuccessFactors que esteja vinculado à representação do usuário no Azure AD.
3. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **SuccessFactors** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , execute as seguintes etapas:

    a. Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão:

    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.eu`|

    b. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão:

    | |
    |--|
    | `https://www.successfactors.com/<companyname>`|
    | `https://www.successfactors.com`|
    | `https://<companyname>.successfactors.eu`|
    | `https://www.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.com`|
    | `https://hcm4preview.sapsf.com/<companyname>`|
    | `https://<companyname>.sapsf.eu`|
    | `https://www.successfactors.cn`|
    | `https://www.successfactors.cn/<companyname>`|

    c. Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão:

    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.successfactors.com`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.sapsf.com`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.successfactors.eu`|
    | `https://<companyname>.sapsf.eu`|
    | `https://<companyname>.sapsf.eu/<companyname>`|
    | `https://<companyname>.sapsf.cn`|
    | `https://<companyname>.sapsf.cn/<companyname>`|

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de logon, o identificador e a URL de resposta reais. Contate a [equipe de suporte ao cliente do SuccessFactors](https://www.successfactors.com/content/ssf-site/en/support.html) para obter esses valores.

4. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **certificado (Base64)** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/certificatebase64.png)

6. Na seção **Configurar SuccessFactors** , copie as URLs apropriadas com base em seu requisito.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo-lhe acesso ao SuccessFactors.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **SuccessFactors**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-successfactors-sso"></a>Configurar o SSO do SuccessFactors

1. Em uma janela diferente do navegador da Web, faça logon no **portal de administração do SuccessFactors** como administrador.

2. Visite **segurança do aplicativo** e recurso nativo para **logon único**.

3. Coloque qualquer valor no **token** de redefinição e clique em **salvar token** para habilitar o SSO do SAML.

    ![Configurando o logon único no lado do aplicativo][11]

    > [!NOTE]
    > Esse valor é usado como a opção on/off. Se qualquer valor for salvo, o SSO do SAML estará ativado. Se um valor em branco for salvo, o SSO do SAML será desativado.

4. Nativo para a captura de tela abaixo e execute as seguintes ações:

    ![Configurando o logon único no lado do aplicativo][12]
  
    a. Selecione o botão de opção **SSO v2 do SAML**
  
    b. Defina o **nome da parte de asserção do SAML**(por exemplo, emissor SAML + nome da empresa).

    c. Na caixa de texto **URL do emissor** , Cole o valor do **identificador do Azure ad** que você copiou do portal do Azure.

    d. Selecione **asserção** como **exigir assinatura obrigatória**.

    e. Selecione **habilitado** como **habilitar sinalizador SAML**.

    f. Selecione **não** como **assinatura de solicitação de logon (it generated/SP/RP)** .

    g. Selecione **navegador/pós-perfil** como **perfil SAML**.

    h. Selecione **não** como **impor o período válido do certificado**.

    i. Copie o conteúdo do arquivo de certificado baixado de portal do Azure e cole-o na caixa de texto **certificado de verificação SAML** .

    > [!NOTE] 
    > O conteúdo do certificado deve ter marcas Begin Certificate e End Certificate.

5. Navegue até SAML V2 e execute as seguintes etapas:

    ![Configurando o logon único no lado do aplicativo][13]

    a. Selecione **Sim** como **suporte ao logout global iniciado por SP**.

    b. Na caixa de texto **URL do serviço de logout global (destino LogoutRequest)** , Cole o valor da **URL de saída** que você copiou para o portal do Azure.

    c. Selecione **não** , pois **exigir SP deve criptografar todos os elementos NameID**.

    d. Selecione **não especificado** como **formato NameID**.

    e. Selecione **Sim** como **habilitar logon iniciado pelo SP (AuthnRequest)** .

    f. Na caixa de texto **Enviar solicitação como emissor de toda a empresa** , Cole o valor da **URL de logon** copiado do portal do Azure.

6. Execute estas etapas se desejar fazer com que os nomes de acesso de User-Case de logon não diferenciam maiúsculas de minúsculas.

    ![Configurar o início de sessão único][29]

    a. Visite **configurações da empresa**(perto da parte inferior).

    b. Marque a caixa de seleção próximo **habilitar nome de usuário que diferencia maiúsculas**de minúsculas.

    c. Clique em **Guardar**.

    > [!NOTE]
    > Se você tentar habilitar isso, o sistema verificará se ele cria um nome de logon SAML duplicado. Por exemplo, se o cliente tiver nomes de usuário usuário1 e Usuário1. Fazer a diferenciação de maiúsculas e minúsculas faz essas duplicatas. O sistema fornece uma mensagem de erro e não habilita o recurso. O cliente precisa alterar um dos nomes de usuário para que ele seja escrito diferente.

### <a name="create-successfactors-test-user"></a>Criar usuário de teste do SuccessFactors

Para permitir que os usuários do Azure AD entrem no SuccessFactors, eles devem ser provisionados no SuccessFactors. No caso do SuccessFactors, o provisionamento é uma tarefa manual.

Para obter os usuários criados no SuccessFactors, você precisa entrar em contato com a [equipe de suporte do SuccessFactors](https://www.successfactors.com/content/ssf-site/en/support.html).

## <a name="test-sso"></a>Testar SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco do SuccessFactors no painel de acesso, você deverá ser conectado automaticamente ao SuccessFactors para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o SuccessFactors com o Azure AD](https://aad.portal.azure.com)

<!--Image references-->

[11]: ./media/successfactors-tutorial/tutorial_successfactors_07.png
[12]: ./media/successfactors-tutorial/tutorial_successfactors_08.png
[13]: ./media/successfactors-tutorial/tutorial_successfactors_09.png
[29]: ./media/successfactors-tutorial/tutorial_successfactors_10.png
