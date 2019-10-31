---
title: 'Tutorial: integração do Azure Active Directory com a área de trabalho central | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e a área de trabalho central.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b805d485-93db-49b4-807a-18d446c7090e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fda8e928b530001faeae34c364dfed91d7620f0a
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73157509"
---
# <a name="tutorial-azure-active-directory-integration-with-central-desktop"></a>Tutorial: integração do Azure Active Directory com a área de trabalho central

Neste tutorial, você aprenderá a integrar o central desktop com o Azure Active Directory (Azure AD).
A integração do Central desktop ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso à área de trabalho central.
* Você pode permitir que seus usuários sejam automaticamente conectados à área de trabalho central (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um local central-a portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Central desktop, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do Central desktop

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* A área de trabalho central dá suporte ao SSO iniciado pelo **SP**

## <a name="adding-central-desktop-from-the-gallery"></a>Adicionando a área de trabalho central da Galeria

Para configurar a integração da área de trabalho central ao Azure AD, você precisa adicionar a área de trabalho central da Galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar a área de trabalho central da galeria, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique no ícone de **Azure Active Directory** .

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **aplicativos empresariais** e, em seguida, selecione a opção **todos os aplicativos** .

    ![A folha aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, clique no botão **novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **central desktop**, selecione **central desktop** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Área de trabalho central na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com a área de trabalho central, com base em um usuário de teste chamado **Brenda Simon**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Central desktop.

Para configurar e testar o logon único do Azure AD com a área de trabalho central, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure ad](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único da área de trabalho central](#configure-central-desktop-single-sign-on)** -para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com o Brenda Simon.
4. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para permitir que Brenda Simon use o logon único do Azure AD.
5. **[Criar usuário de teste do Central desktop](#create-central-desktop-test-user)** – para ter um equivalente de Brenda Simon na área de trabalho central que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o central desktop, execute as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos **da área de trabalho central** , selecione **logon único**.

    ![Link configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-enalimentado** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com SAML** , clique no ícone **Editar** para abrir a caixa de diálogo **configuração básica de SAML** .

    ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , execute as seguintes etapas:

    ![Informações de logon único de domínio e URLs da área de trabalho central](common/sp-identifier-reply.png)

    a. Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://<companyname>.centraldesktop.com`

    b. Na caixa **identificador** , digite uma URL usando o seguinte padrão:
    
    | |
    |--|
    | `https://<companyname>.centraldesktop.com/saml2-metadata.php`|
    | `https://<companyname>.imeetcentral.com/saml2-metadata.php`|
    | |

    c. Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão: `https://<companyname>.centraldesktop.com/saml2-assertion.php`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de logon, o identificador e a URL de resposta reais. Contate a [equipe de suporte ao cliente do Central desktop](https://imeetcentral.com/contact-us) para obter esses valores. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

5. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o **certificado (bruto)** das opções determinadas de acordo com seu requisito e salvá-lo no computador.

    ![O link de download do certificado](common/certificateraw.png)

6. Na seção **Configurar área de trabalho central** , copie as URLs apropriadas de acordo com seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logout

### <a name="configure-central-desktop-single-sign-on"></a>Configurar o logon único da área de trabalho central

1. Entre no seu locatário **da área de trabalho central** .

2. Vá para **configurações**. Selecione **avançado**e, em seguida, selecione **logon único**.

    ![Instalação-avançada](./media/central-desktop-tutorial/ic769563.png "Instalação-avançada")

3. Na página **configurações de logon único** , execute as seguintes etapas:

    ![Configurações de logon único](./media/central-desktop-tutorial/ic769564.png "Configurações de logon único")

    a. Selecione **habilitar logon único do SAML v2**.

    b. Na caixa **URL de SSO** , Cole o valor do **identificador do Azure ad** que você copiou do portal do Azure.

    c. Na caixa **URL de logon SSO** , Cole o valor da **URL de logon** que você copiou do portal do Azure.

    d. Na caixa **URL de logout de SSO** , Cole o valor da **URL de logout** que você copiou do portal do Azure.

4. Na seção **método de verificação de assinatura de mensagem** , execute as seguintes etapas:

    ![Método de verificação de assinatura de mensagem](./media/central-desktop-tutorial/ic769565.png "Método de verificação de assinatura de mensagem")
    
    a. Selecione **Certificado**.

    b. Na lista **certificado de SSO** , selecione **rsh SHA256**.

    c. Abra o certificado baixado no bloco de notas. Em seguida, copie o conteúdo do certificado e cole-o no campo **certificado SSO** .

    d. Selecione **exibir um link para a página de logon do SAMLv2**.

    e. Selecione **Atualizar**.

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

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo acesso à área de trabalho central.

1. Na portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **área de trabalho central**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **área de trabalho central**.

    ![O link da área de trabalho central na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![O link "usuários e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar usuário** e selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **usuários e grupos** , selecione **Brenda Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.

6. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-central-desktop-test-user"></a>Criar usuário de teste do Central desktop

Para que os usuários do Azure AD possam entrar, eles devem ser provisionados no aplicativo da área de trabalho central. Esta seção descreve como criar contas de usuário do Azure AD na área de trabalho central.

> [!NOTE]
> Para provisionar contas de usuário do Azure AD, você pode usar qualquer outra ferramenta de criação de conta de usuário da área de trabalho central ou APIs fornecidas pela área de trabalho central.

**Para provisionar contas de usuário na área de trabalho central:**

1. Entre no seu locatário da área de trabalho central.

2. Selecione **pessoas** e, em seguida, selecione **adicionar membros internos**.

    ![Porta](./media/central-desktop-tutorial/ic781051.png "Pessoas")

3. Na caixa **endereço de email de novos membros** , digite uma conta do Azure AD que você deseja provisionar e, em seguida, selecione **Avançar**.

    ![Endereços de email de novos membros](./media/central-desktop-tutorial/ic781052.png "Endereços de email de novos membros")

4. Selecione **Adicionar membro (s) interno**.

    ![Adicionar membro interno](./media/central-desktop-tutorial/ic781053.png "Adicionar membro interno")
  
   > [!NOTE]
   > Os usuários que você adicionar receberão um email que inclui um link de confirmação para ativar suas contas.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao clicar no bloco área de trabalho central no painel de acesso, você deverá entrar automaticamente na área de trabalho central para a qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
