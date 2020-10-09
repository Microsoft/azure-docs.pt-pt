---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com a ScaleX Enterprise Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a ScaleX Enterprise.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: jeedes
ms.openlocfilehash: c7033886d55e381445d99035115654332491441c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88547877"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-scalex-enterprise"></a>Tutorial: Azure Ative Directory integração única (SSO) com a ScaleX Enterprise

Neste tutorial, você vai aprender a integrar a ScaleX Enterprise com o Azure Ative Directory (Azure AD). Quando integrar a ScaleX Enterprise com a AD Azure, pode:

* Controlo em Azure AD que tem acesso à ScaleX Enterprise.
* Capacitar os seus utilizadores a serem automaticamente inscritos na ScaleX Enterprise com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A assinatura ativada pela ScaleX Enterprise (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* ScaleX Enterprise suporta **SP e IDP** iniciado SSO

## <a name="adding-scalex-enterprise-from-the-gallery"></a>Adicionar ScaleX Enterprise da galeria

Para configurar a integração da ScaleX Enterprise em Azure AD, é necessário adicionar a ScaleX Enterprise da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** escreva **ScaleX Enterprise** na caixa de pesquisa.
1. Selecione **ScaleX Enterprise** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on-for-scalex-enterprise"></a>Configurar e testar Azure AD único sign-on para ScaleX Enterprise

Configure e teste Azure AD SSO com a ScaleX Enterprise utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado na ScaleX Enterprise.

Para configurar e testar o Azure AD SSO com a ScaleX Enterprise, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    * **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure scalex Enterprise SSO](#configure-scalex-enterprise-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    * **[Create ScaleX Enterprise test user](#create-scalex-enterprise-test-user)** - para ter uma contraparte de B.Simon na ScaleX Enterprise que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **ScaleX Enterprise,** encontre a secção **Gerir** e selecione **um único sinal de sing.on**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://platform.rescale.com/saml2/<company id>/`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://platform.rescale.com/saml2/<company id>/acs/`

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://platform.rescale.com/saml2/<company id>/sso/`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL de inscrição. Contacte [a equipa de suporte ao cliente da ScaleX Enterprise](https://info.rescale.com/contact_sales) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. A sua aplicação ScaleX Enterprise espera as afirmações DE SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem seguinte mostra a lista de atributos predefinidos, onde como **endereço de e-mail** é mapeado com **user.mail**. A aplicação ScaleX Enterprise espera que o **emailaddress** seja mapeado com **o nome do utilizador.userprincipalname**, pelo que é necessário editar o mapeamento do atributo clicando no ícone **Editar** e alterar o mapeamento do atributo.

    ![image](common/edit-attribute.png)

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **Configurar ScaleX Enterprise,** copie os URL(s) apropriados com base no seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à ScaleX Enterprise.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **ScaleX Enterprise**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-scalex-enterprise-sso"></a>Configurar Scalex Enterprise SSO

1. Para automatizar a configuração dentro da ScaleX Enterprise, é necessário instalar a extensão do **navegador 'As aplicações' Secure Sign-in** clicando **em instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

1. Depois de adicionar extensão ao navegador, clique em **Configurar ScaleX Enterprise** irá direcioná-lo para a aplicação ScaleX Enterprise. A partir daí, forneça as credenciais de administração para assinar na ScaleX Enterprise. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-6.

    ![Configuração de configuração](common/setup-sso.png)

1. Se pretender configurar a ScaleX Enterprise manualmente, abra uma nova janela do navegador web e inscreva-se no site da empresa ScaleX Enterprise como administrador e execute os seguintes passos:

1. Clique no menu no canto superior direito e **selecione a Administração Contoso.**

    > [!NOTE]
    > Contoso é apenas um exemplo. Este deve ser o seu nome de empresa.

    ![Configurar o início de sessão único](./media/scalex-enterprise-tutorial/Test_Admin.png)

1. Selecione **Integrações** a partir do menu superior e selecione **um único sinal de sôsmes**.

    ![Configurar o início de sessão único](./media/scalex-enterprise-tutorial/admin_sso.png) 

1. Preencha o formulário da seguinte forma:

    ![Configurar o início de sessão único](./media/scalex-enterprise-tutorial/scalex_admin_save.png)

    a. Selecione **Criar qualquer utilizador que possa autenticar com SSO**

    b. **Serviço Fornecedor saml**: Cole a urna de ***valor:oasis:names:tc:SAML:2.0:nameid-formato:persistent***

    c. **Nome do campo de e-mail do Fornecedor de Identidade na resposta ACS**: Cole o valor `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. **Fornecedor de identidade EntidadeDescritor de Identidade ID:** Cole o valor do **identificador Azure AD** copiado do portal Azure.

    e. **Fornecedor de identidade SingleSignOnService URL:** Cole o URL de **login** do portal Azure.

    f. **Certificado público fornecedor de identidade X509:** Abra o certificado X509 descarregado do Azure em bloco de notas e cole o conteúdo desta caixa. Certifique-se de que não existem quebras de linha no meio do conteúdo do certificado.

    exemplo, Verifique as seguintes caixas de verificação: **Ativadas, Encriptadas NameID e Sign AuthnRequests.**

    h. Clique em **Atualização SSO Definições** para guardar as definições.

### <a name="create-scalex-enterprise-test-user"></a>Criar utilizador de teste ScaleX Enterprise

Para permitir que os utilizadores de Azure AD inscrevam-se na ScaleX Enterprise, devem ser adusitados à ScaleX Enterprise. No caso da ScaleX Enterprise, o provisionamento é uma tarefa automática e não são necessários passos manuais. Qualquer utilizador que possa autenticar com sucesso com credenciais SSO será automaticamente a provisionado no lado da ScaleX.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo da Empresa ScaleX no Painel de Acesso, deverá ser automaticamente inscrito na Empresa ScaleX para a qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente a ScaleX Enterprise com Azure AD](https://aad.portal.azure.com/)