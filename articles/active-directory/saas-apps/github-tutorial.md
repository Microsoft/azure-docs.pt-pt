---
title: 'Tutorial: Integração do Azure Ative Directory com o GitHub Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o GitHub.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/07/2020
ms.author: jeedes
ms.openlocfilehash: cb5ef751a3fc2241924eaee1c5da9507006389cc
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92449277"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-github"></a>Tutorial: Azure Ative Directory integração única (SSO) com GitHub

Neste tutorial, você vai aprender a integrar GitHub com Azure Ative Directory (Azure AD). Quando integrar o GitHub com a AD Azure, pode:

* Controle em Azure AD que tem acesso à sua GitHub Enterprise Cloud Organization.
* Gerencie o acesso à sua GitHub Enterprise Cloud Organization numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD da Azure com o GitHub, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Uma organização GitHub criada em [GitHub Enterprise Cloud,](https://help.github.com/articles/github-s-products/#github-enterprise)que requer o [plano de faturação](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations) da GitHub Enterprise

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* GitHub apoia **SP** iniciado SSO

* GitHub suporta fornecimento [ **automatizado** de utilizadores (convites de organização)](github-provisioning-tutorial.md)
* Uma vez configurado GitHub, pode impor o controlo de Sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com a Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)

## <a name="adding-github-from-the-gallery"></a>Adicionando GitHub da galeria

Para configurar a integração do GitHub no AD Azure, precisa adicionar o GitHub da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Add a partir da secção **da galeria,** escreva **GitHub** na caixa de pesquisa.
1. Selecione **GitHub** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-github"></a>Configurar e testar Azure AD único sinal para GitHub

Configure e teste Azure AD SSO com GitHub usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no GitHub.

Para configurar e testar o Azure AD SSO com o GitHub, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    * **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure GitHub SSO](#configure-github-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    * **[Create GitHub test user](#create-github-test-user)** - para ter uma contraparte de B.Simon no GitHub que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **GitHub,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

   a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://github.com/orgs/<Organization ID>/sso`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://github.com/orgs/<Organization ID>`

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://github.com/orgs/<Organization ID>/saml/consume`


    > [!NOTE]
    > Por favor, note que estes não são os valores reais. Tem de atualizar estes valores com o sinal real em URL, Identifier e URL de resposta. Aqui sugerimos que use o valor único da corda no Identificador. Vá à secção GitHub Admin para recuperar estes valores.

5. A sua aplicação GitHub espera as afirmações SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos, onde como **Identificador de Utilizador Único (ID nome)** é mapeado com **user.userprincipalname**. A aplicação GitHub espera que **o Identificador Exclusivo do Utilizador (ID)** seja mapeado com **o user.mail,** pelo que é necessário editar o mapeamento do atributo clicando no ícone **Editar** e alterar o mapeamento do atributo.

    ![Screenshot que mostra a secção "Atributos do utilizador" com o ícone "Editar" selecionado.](common/edit-attribute.png)

6. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

7. Na secção **Configurar GitHub,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure Ad

    c. Logout URL

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao GitHub.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **GitHub**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação DE SAML, no **diálogo de funções Selecione,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

    ![função de utilizador](./media/github-tutorial/user-role.png)

    > [!NOTE]
    > **Selecione uma** opção de função será desativada e a função predefinitiva é USER para utilizador selecionado.

7. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-github-sso"></a>Configurar GitHub SSO

1. Numa janela diferente do navegador web, inscreva-se no seu site de organização GitHub como administrador.

2. Navegue para **Definições** e clique em **Segurança**

    ![Screenshot que mostra o menu GitHub "Organizações" com "Segurança" selecionado.](./media/github-tutorial/security.png)

3. Verifique a caixa **de autenticação ATIVA SAML,** revelando os campos de configuração de inscrição de assinatura única. executar os seguintes passos:

    ![Screenshot que mostra a secção "S A M L single sign-on" com "Enable S A M L authentication" com caixas de texto U R L realçadas.](./media/github-tutorial/saml-sso.png)

    a. Copie o valor **de URL de inscrição única** e cole este valor na caixa de texto do Sinal na caixa de texto **SÍBL na Configuração Básica SAML** no portal Azure. **Sign on URL**
    
    b. Copiar o valor **url do serviço de afirmação do consumidor** e colar este valor na caixa de texto URL de **resposta** na **Configuração Básica SAML** no portal Azure.

4. Configure os seguintes campos:

    ![Screenshot que mostra as caixas de texto "Sign on URL", "Emitente" e "Certificado Público".](./media/github-tutorial/configure.png)

    a. Na placa de texto **URL,** cole o valor URL do **Login** que copiou do portal Azure.

    b. Na caixa de texto **emitente,** cole o valor **do identificador Azure AD** que copiou do portal Azure.

    c. Abra o certificado descarregado do portal Azure em bloco de notas, cole o conteúdo na caixa de texto **do Certificado Público.**

    d. Clique no ícone **Editar** para editar o Método de **Assinatura** e **Digerir** de **RSA-SHA1** e **SHA1** para **RSA-SHA256** e **SHA256** como mostrado abaixo.
    
    e. Atualize o URL do serviço de apoio ao consumidor de **afirmação (URL de resposta)** a partir do URL padrão para que o URL no GitHub corresponda ao URL no registo da aplicação Azure.

    ![image](./media/github-tutorial/tutorial_github_sha.png)

5. Clique na **configuração do Test SAML** para confirmar que não há falhas de validação ou erros durante o SSO.

    ![Definições](./media/github-tutorial/test.png)

6. Clique em **Guardar**

> [!NOTE]
> O único sign-on no GitHub autentica-se a uma organização específica no GitHub e não substitui a autenticação do próprio GitHub. Portanto, se a sessão de github.com do utilizador tiver expirado, poderá ser-lhe pedido que autente para autenticar com o ID/password do GitHub durante o processo de inscrição única.

### <a name="create-github-test-user"></a>Criar utilizador de teste GitHub

O objetivo desta secção é criar um utilizador chamado Britta Simon no GitHub. O GitHub suporta o fornecimento automático do utilizador, que está por defeito. Pode encontrar mais detalhes [aqui](github-provisioning-tutorial.md) sobre como configurar o fornecimento automático do utilizador.

**Se precisar de criar o utilizador manualmente, execute os seguintes passos:**

1. Faça login no seu site da empresa GitHub como administrador.

2. Clique **em Pessoas.**

    ![A screenshot mostra o site GitHub com pessoas selecionadas.](./media/github-tutorial/people.png "People")

3. Clique **em Convidar membro**.

    ![Convidar utilizadores](./media/github-tutorial/invite-member.png "Convidar utilizadores")

4. Na página de diálogo **do membro convidado,** execute os seguintes passos:

    a. Na caixa de sms **do Email,** digite o endereço de e-mail da conta Britta Simon.

    ![Convidar pessoas](./media/github-tutorial/email-box.png "Convidar pessoas")

    b. Clique **em Enviar Convite.**

    ![Screenshot que mostra a página de diálogo "Membro convidado" com "Membro" selecionado e o botão "Enviar convite" selecionado.](./media/github-tutorial/send-invitation.png "Convidar pessoas")

    > [!NOTE]
    > O titular da conta Azure Ative Directory receberá um e-mail e seguirá um link para confirmar a sua conta antes de ficar ativa.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo do GitHub no Painel de Acesso, deverá ser automaticamente inscrito no GitHub para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente GitHub com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)