---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com | Splashtop Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o Splashtop.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/04/2020
ms.author: jeedes
ms.openlocfilehash: b6dda20487caf6fe3ba49578cfdc0b65434a8dfa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92520562"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-splashtop"></a>Tutorial: Azure Ative Directory integração única (SSO) com Splashtop

Neste tutorial, você vai aprender a integrar Splashtop com Azure Ative Directory (Azure AD). Quando integrar o Splashtop com Azure AD, pode:

* Controlo em Azure AD que tem acesso ao Splashtop.
* Ative os seus utilizadores a serem automaticamente inscritos no Splashtop com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Assinatura ativada por Splashtop single sign-on (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Splashtop suporta **SP** iniciado SSO

* Uma vez configurado o Splashtop, pode impor controlos de sessão, que protegem a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. Os controlos de sessão estendem-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-splashtop-from-the-gallery"></a>Adicionar Splashtop da galeria

Para configurar a integração do Splashtop no Azure AD, é necessário adicionar o Splashtop da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **escreva Splashtop** na caixa de pesquisa.
1. Selecione **Splashtop** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on-for-splashtop"></a>Configurar e testar Azure AD único sinal para Splashtop

Configure e teste Azure AD SSO com Splashtop usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no Splashtop.

Para configurar e testar o Azure AD SSO com o Splashtop, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    * **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o Splashtop SSO](#configure-splashtop-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    * **[Create Splashtop test user](#create-splashtop-test-user)** - para ter uma contraparte de B.Simon em Splashtop que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Splashtop,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **único sign-on de Configuração com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    Na caixa de texto **URL de entrada de inscrição,** digite o URL:  `https://my.splashtop.com/login/sso`

1. A aplicação Splashtop espera as afirmações SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem seguinte mostra a lista de atributos predefinidos, enquanto **o nome** é mapeado com o **nome user.userprincipal**. A aplicação TicketManager espera que **o identificador** de nomes seja mapeado com **o user.mail,** pelo que é necessário editar o mapeamento do atributo clicando no ícone **Editar** e alterar o mapeamento do atributo.

    ![A screenshot mostra atributos do utilizador com o ícone editar selecionado.](common/edit-attribute.png)

1. Na **configuração de um único sessão com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **De Configuração do Splashtop,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao Splashtop.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Splashtop**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-splashtop-sso"></a>Configurar sso splashtop

Nesta secção, terá de se candidatar a um novo método SSO a partir do [portal web Splashtop](https://my.splashtop.com/login).
1. No portal web Splashtop, vá ao  /  separador **'Informações de Conta' Equipa,** desloque-se para baixo para encontrar **o único sinal na** secção . Em seguida, clique **em Aplicar para novo método SSO**.

    ![A screenshot mostra o sinal único na página onde pode selecionar Aplicar para novo método S S O.](media/splashtop-tutorial/apply-for-new-SSO-method.png)

1. Na janela de aplicação, dê um **nome SSO**. Por exemplo, New Azure, em seguida, selecione **Azure** como o tipo IDP, e insira **URL de login** e **identificador AD Ad Azure** copiado da aplicação Splashtop no portal Azure.

    ![A screenshot mostra a página do método 'Aplicar para S O' onde pode introduzir um nome e outras informações.](media/splashtop-tutorial/azure-sso-1.png)

1. Para obter informações sobre o certificado, clique no ficheiro cert descarregado da aplicação Splashtop no portal Azure, edite-o com o Notepad e, em seguida, copie o conteúdo, cole-o no campo **Desembóia (Base64).**

    ![Screenshot show selecionando um arquivo de certificado e abrindo-o com o Bloco de Notas.](media/splashtop-tutorial/cert-1.png)
    ![A screenshot mostra o conteúdo do ficheiro do certificado.](media/splashtop-tutorial/cert-2.png)
    ![A screenshot mostra a caixa de texto do Certificado de Descarregamento.](media/splashtop-tutorial/azure-sso-2.png)

1. Já está! Clique em **Save** e Splashtop A equipa de validação SSO entrará em contacto consigo para obter informações de verificação e, em seguida, ativará o método SSO.

### <a name="create-splashtop-test-user"></a>Criar utilizador de teste Splashtop

1. Após a ativação do método SSO, verifique o método SSO recém-criado para o ativar na secção **Single Sign On.**

    ![A screenshot mostra a página Single Sign On onde pode ativar o novo método.](media/splashtop-tutorial/enable.png)

1. Convide o utilizador de teste, por exemplo, `B.Simon@contoso.com` para a sua equipa Splashtop com o método SSO recém-criado.

    ![A screenshot mostra a página 'Convidar Utilizadores' onde pode selecionar o seu novo método.](media/splashtop-tutorial/invite.png)

1. Também pode alterar uma conta Splashtop existente para uma conta SSO, ver [instruções](https://support-splashtopbusiness.splashtop.com/hc/en-us/articles/360038685691-How-to-associate-SSO-method-to-existing-team-admin-member-).

1. Já está! Pode utilizar a conta SSO para iniciar sessão no portal web do Splashtop ou na aplicação Splashtop Business.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo splashtop no Painel de Acesso, deverá ser automaticamente inscrito no Splashtop para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Try Splashtop com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Como proteger o Splashtop com visibilidade e controlos avançados](/cloud-app-security/proxy-intro-aad)