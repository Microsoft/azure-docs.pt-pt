---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com a Nintex Promapp Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Nintex Promapp.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/30/2020
ms.author: jeedes
ms.openlocfilehash: fc31195e7f544bdce7fe2f135a39cb9992875d0a
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92505923"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-nintex-promapp"></a>Tutorial: Azure Ative Directory single sign-on (SSO) integração com Nintex Promapp

Neste tutorial, você vai aprender a integrar Nintex Promapp com Azure Ative Directory (Azure AD). Quando integrar o Nintex Promapp com Azure AD, pode:

* Controlo em Azure AD que tem acesso ao Nintex Promapp.
* Permita que os seus utilizadores sejam automaticamente inscritos no Nintex Promapp com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Nintex Promapp assinatura única (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Nintex Promapp apoia **SP e IDP** iniciado SSO
* Nintex Promapp suporta **provisão de** utilizadores just in time

## <a name="adding-nintex-promapp-from-the-gallery"></a>Adicionar Nintex Promapp da galeria

Para configurar a integração do Nintex Promapp no AD Azure, precisa adicionar o Nintex Promapp da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Add da secção **da galeria,** **digite Nintex Promapp** na caixa de pesquisa.
1. Selecione **Nintex Promapp** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-nintex-promapp"></a>Configurar e testar Azure AD single sign-on para Nintex Promapp

Configure e teste Azure AD SSO com Nintex Promapp usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no Nintex Promapp.

Para configurar e testar o Azure AD SSO com o Nintex Promapp, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    * **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure Nintex Promapp SSO](#configure-nintex-promapp-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    * **[Create Nintex Promapp test user](#create-nintex-promapp-test-user)** - para ter uma contraparte de B.Simon no Nintex Promapp que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Nintex Promapp,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    1. Na caixa **identifier,** introduza um URL neste padrão:

        ```https
        https://go.promapp.com/TENANTNAME/
        https://au.promapp.com/TENANTNAME/
        https://us.promapp.com/TENANTNAME/
        https://eu.promapp.com/TENANTNAME/
        https://ca.promapp.com/TENANTNAME/
        ```

       > [!NOTE]
       > A integração AD da AD com o Nintex Promapp está atualmente configurada apenas para autenticação iniciada pelo serviço. (Isto é, ir a um URL Nintex Promapp inicia o processo de autenticação.) Mas o campo **URL de resposta** é um campo obrigatório.

    1. Na caixa **URL de resposta,** introduza um URL neste padrão:

       `https://<DOMAINNAME>.promapp.com/TENANTNAME/saml/authenticate.aspx`

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na **caixa de URL** sign in, introduza um URL neste padrão: `https://<DOMAINNAME>.promapp.com/TENANTNAME/saml/authenticate`

    > [!NOTE]
    > Estes valores são espaços reservados. Você precisa usar o identificador real, URL de resposta e URL de inscrição. Contacte a equipa de apoio da [Nintex Promapp](https://www.promapp.com/about-us/contact-us/) para obter os valores. Também pode consultar os padrões mostrados na caixa de diálogo **de configuração SAML básica** no portal Azure.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **Configurar Nintex Promapp,** copie os URL(s) apropriados com base na sua exigência.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao Nintex Promapp.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Nintex Promapp**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-nintex-promapp-sso"></a>Configure Nintex Promapp SSO

1. Inscreva-se no site da empresa Nintex Promapp como administrador.

2. No menu na parte superior da janela, selecione **Administrador:**

    ![Selecione Administrador][12]

3. **Selecione Configurar:**

    ![Selecione Configurar][13]

4. Na caixa de diálogo de **segurança,** tome os seguintes passos.

    ![Caixa de diálogo de segurança][14]

    1. Cole o **URL de login** que copiou do portal Azure para a caixa URL **SSO-Login.**

    1. Na lista **SSO - Modo de Inscrição única,** selecione **Opcional**. Selecione **Guardar**.

       > [!NOTE]
       > O modo opcional é apenas para testes. Depois de ficar satisfeito com a configuração, selecione **Requerido** na lista **de Modo SSO - Único Sinal de Inscrição** para forçar todos os utilizadores a autenticar com Azure AD.

    1. No Bloco de Notas, abra o certificado que descarregou na secção anterior. Copie o conteúdo do certificado sem a primeira linha (**-----BEGIN CERTIFICATE-----)** ou a última linha **(-----END CERTIFICATE-----**). Cole o conteúdo do certificado na caixa de **certificado SSO-x.509** e, em seguida, selecione **Guardar**.

### <a name="create-nintex-promapp-test-user"></a>Criar utilizador de teste Nintex Promapp

Nesta secção, um utilizador chamado B.Simon é criado no Nintex Promapp. O Nintex Promapp suporta o fornecimento de utilizadores just-in-time, o que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no Nintex Promapp, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Nintex Promapp no Painel de Acesso, deverá ser automaticamente inscrito no Nintex Promapp para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente o Nintex Promapp com a Azure AD](https://aad.portal.azure.com/)

<!--Image references-->

[12]: ./media/promapp-tutorial/tutorial_promapp_05.png
[13]: ./media/promapp-tutorial/tutorial_promapp_06.png
[14]: ./media/promapp-tutorial/tutorial_promapp_07.png