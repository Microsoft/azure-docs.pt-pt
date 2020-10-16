---
title: 'Tutorial: Integração do Azure Ative Directory com o RingCentral Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o RingCentral.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: jeedes
ms.openlocfilehash: b8539f3f2a5882f12f131c95b0c17efd3acba3cf
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92105180"
---
# <a name="tutorial-integrate-ringcentral-with-azure-active-directory"></a>Tutorial: Integrar RingCentral com Diretório Ativo Azure

Neste tutorial, você vai aprender a integrar o RingCentral com o Azure Ative Directory (Azure AD). Quando integrar o RingCentral com a AD Azure, pode:

* Controlo em Azure AD que tem acesso ao RingCentral.
* Ativar os seus utilizadores a serem automaticamente inscritos no RingCentral com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Assinatura ativada por RingCentral (SSO).

> [!NOTE]
> Esta integração também está disponível para usar a partir do ambiente cloud do governo dos EUA Azure AD. Você pode encontrar esta aplicação na Azure AD US Government Cloud Application Gallery e configurá-la da mesma forma que você faz a partir de nuvem pública.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* RingCentral suporta **IDP** iniciado SSO

## <a name="adding-ringcentral-from-the-gallery"></a>Adicionando RingCentral da galeria

Para configurar a integração do RingCentral no AD Azure, é necessário adicionar o RingCentral da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** digite **RingCentral** na caixa de pesquisa.
1. Selecione **RingCentral** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Configure e teste Azure AD SSO com RingCentral usando um utilizador de teste chamado **Britta Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no RingCentral.

Para configurar e testar o Azure AD SSO com o RingCentral, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    * **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o RingCentral SSO](#configure-ringcentral-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    * **[Create RingCentral test user](#create-ringcentral-test-user)** - para ter uma contraparte de B.Simon no RingCentral que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **RingCentral,** encontre a secção **Gerir** e selecione **'Único sinal de s-on'.**
1. Na página **de método de inscrição única,** selecione **SAML**.
1. Na **configuração single Sign-On com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **de Configuração Básica SAML,** se tiver **um ficheiro de metadados do Fornecedor de Serviços,** execute os seguintes passos:

    1. Clique **em Carregar o ficheiro de metadados**.
    1. Clique no **logotipo da pasta** para selecionar o ficheiro de metadados e clique em **Upload**.
    1. Após o carregamento com sucesso do ficheiro de metadados, os valores de URL **de identificação** e **resposta** são preenchidos automaticamente na secção **de Configuração BÁSICA SAML.**

    > [!Note]
    > Obtém o **ficheiro de metadados do Fornecedor de Serviços** na página de Configuração SSO do RingCentral, que é explicado mais tarde no tutorial.

1. Se não tiver **um ficheiro de metadados do Fornecedor de Serviços,** insira os valores para os seguintes campos:

    a. Na caixa de texto **identifier,** digite um URL:
  
    | Identificador |
    |--|
    |  `https://sso.ringcentral.com` |
    | `https://ssoeuro.ringcentral.com` |

    b. Na caixa de texto **URL de resposta,** digite um URL:

    | URL de Resposta |
    |--|
    | `https://sso.ringcentral.com/sp/ACS.saml2` |
    | `https://ssoeuro.ringcentral.com/sp/ACS.saml2` |

1. Na **configuração single Sign-On com página SAML,** na secção Certificado de Assinatura **SAML,** clique no botão de cópia para copiar o **Url de Metadados da Federação de Aplicações** e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste no portal Azure chamado Britta Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `Britta Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `BrittaSimon@contoso.com`.
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que Britta Simon use a Azure single sign-on, concedendo acesso ao RingCentral.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **RingCentral**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **Britta Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-ringcentral-sso"></a>Configurar ringCentral SsO

1. Para automatizar a configuração dentro do RingCentral, é necessário instalar a extensão do **navegador 'As aplicações' Secure Sign-in** clicando **em instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

1. Depois de adicionar extensão ao navegador, clique em **Configurar o RingCentral** irá direcioná-lo para a aplicação RingCentral. A partir daí, forneça as credenciais de administração para assinar no RingCentral. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-7.

    ![Configuração de configuração](common/setup-sso.png)

1. Se pretender configurar o RingCentral manualmente, abra uma nova janela do navegador web e inscreva-se no site da empresa RingCentral como administrador e execute os seguintes passos:

1. Em cima, clique em **Ferramentas.**

    ![A screenshot mostra ferramentas selecionadas a partir do site da empresa RingCentral.](./media/ringcentral-tutorial/ringcentral1.png)

1. Navegue para **um único sign-on**.

    ![A screenshot mostra single Sign-On selecionados a partir do menu Ferramentas.](./media/ringcentral-tutorial/ringcentral2.png)

1. Na página **'Sign-on' único,** na secção **de Configuração SSO,** a partir do **passo 1** clique em **Editar** e execute os seguintes passos:

    ![A screenshot mostra a página de configuração S S O onde pode selecionar Editar.](./media/ringcentral-tutorial/ringcentral3.png)

1. Na página **de inscrição única,** execute os seguintes passos:

    ![A screenshot mostra a página De Sign-On Único configurado onde pode carregar metadados I D P.](./media/ringcentral-tutorial/ringcentral4.png)

    a. Clique **em Procurar** para carregar o ficheiro de metadados que descarregou a partir do portal Azure.

    b. Após o upload dos metadados, os valores são auto-povoados na secção **SSO General Information.**

    c. Na secção **de Mapeamento de Atributos,** selecione **Map Email Attribute para** como `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. Clique em **Guardar**.

    e. A partir do **passo 2** clique **em baixar** o ficheiro **de metadados do Fornecedor de Serviço** e carregá-lo na secção de **Configuração Sudeste SAML** para preencher automaticamente os valores **de URL** de **identificação** e resposta no portal Azure.

    ![A screenshot mostra a página de Configuração S O onde pode selecionar Download.](./media/ringcentral-tutorial/ringcentral6.png) 

    f. Na mesma página, navegue para ativar a secção **SSO** e execute os seguintes passos:

    ![A screenshot mostra a secção Enable S S O onde pode terminar a configuração.](./media/ringcentral-tutorial/ringcentral5.png)

    * **Selecione Ative O serviço SSO**.

    * Selecione **Permitir que os utilizadores iniciem sessão com credencial SSO ou RingCentral**.

    * Clique em **Guardar**.

### <a name="create-ringcentral-test-user"></a>Criar utilizador de teste RingCentral

Nesta secção, cria-se um utilizador chamado Britta Simon no RingCentral. Trabalhe com [a equipa de suporte do Cliente RingCentral](https://success.ringcentral.com/RCContactSupp) para adicionar os utilizadores na plataforma RingCentral. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

### <a name="test-sso"></a>Teste SSO

Quando selecionar o azulejo RingCentral no Painel de Acesso, deverá ser automaticamente inscrito no RingCentral para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente RingCentral com Azure AD](https://aad.portal.azure.com/)
