---
title: 'Tutorial: Integração do Azure Ative Directory com o Wandera RADAR Admin | Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o Wandera RADAR Admin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/27/2020
ms.author: jeedes
ms.openlocfilehash: 383b49a7363fdca9327d419f5bed092d489a95d5
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "104951815"
---
# <a name="tutorial-integrate-wandera-radar-admin-with-azure-active-directory"></a>Tutorial: Integrar o Wandera RADAR Admin com diretório ativo Azure

Neste tutorial, você vai aprender a integrar Wandera RADAR Admin com Azure Ative Directory (Azure AD). Quando integrar o Wandera RADAR Admin com Azure AD, pode:

* Controle em Azure AD que tem acesso ao Wandera RADAR Admin.
* Permita que os seus utilizadores sejam automaticamente inscritos no Wandera RADAR Admin com as suas contas AD AZure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Wandera RADAR Admin única subscrição ativada (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Wandera RADAR Administrador suporta **IDP** iniciado SSO
* Uma vez configurado Wandera RADAR Admin, pode impor o controlo de sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-wandera-radar-admin-from-the-gallery"></a>Adicionando Wandera RADAR Administrador da galeria

Para configurar a integração do Wandera RADAR Admin no AD AZure, é necessário adicionar o Wandera RADAR Admin da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **digite Wandera RADAR Admin** na caixa de pesquisa.
1. **Selecione Wandera RADAR Admin** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso"></a>Configurar e testar Azure AD SSO

Configure e teste Azure AD SSO com Wandera RADAR Admin usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no Wandera RADAR Admin.

Para configurar e testar o Azure AD SSO com a Wandera RADAR Admin, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
   * Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
   * **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o Wandera RADAR Admin SSO](#configure-wandera-radar-admin-sso)** - para configurar as definições de Sign-On única no lado da aplicação.
   * **[Create Wandera RADAR Admin test user](#create-wandera-radar-admin-test-user)** - para ter uma contraparte de B.Simon em Wandera RADAR Admin que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **Wandera RADAR,** encontre a secção **Gerir** e selecione **'Único sinal de s-on'.**
1. Na página **de método de inscrição única,** selecione **SAML**.
1. Na **configuração single Sign-On com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:  `https://radar.wandera.com/saml/acs/<tenant id>`

    > [!NOTE]
    > O valor não é real. Atualizar o valor com o URL de resposta real. Contacte [a equipa de apoio ao Cliente do Wandera RADAR](https://www.wandera.com/about-wandera/contact/#supportsection) para obter o valor. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure. Substitua cuidadosamente a <tenant id> parte do URL acima por causa do ID do inquilino mostrado na página De Sign-On Único **administração de definições** dentro da sua conta  >    >   Wandera.


1. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. No Conjunto single Sign-On com a página **SAML,** clique no ícone edit/pen para **O Certificado de Assinatura SAML** para editar as definições.

    ![Opção de assinatura](common/signing-option.png)

    1. Selecione **a Opção de Assinatura** como resposta e **afirmação de Sign SAML**.

    1. Selecione **algoritmo de assinatura** como **SHA-256**.

1. Na secção **De Administração Do Wandera RADAR,** copie os URL(s) apropriados com base na sua exigência.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, permitindo o acesso ao Wandera RADAR Admin.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, **selecione Wandera RADAR Admin**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-wandera-radar-admin-sso"></a>Configure Wandera RADAR Admin SSO

1. Para automatizar a configuração dentro do Wandera RADAR Admin, é necessário instalar a extensão do **navegador 'As aplicações' Secure Sign-in** clicando **em instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique em **Setup Wandera RADAR Admin** irá direcioná-lo para a aplicação Wandera RADAR Admin. A partir daí, forneça as credenciais de administrador para assinar no Wandera RADAR Admin. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-4.

    ![Configuração de configuração](common/setup-sso.png)

3. Se pretender configurar manualmente o Wandera RADAR Admin, abra uma nova janela do navegador web e inscreva-se no site da empresa Wandera RADAR como administrador e execute os seguintes passos:

4. No canto superior direito da página, clique em **Definições**  >  **Administração**  >  **Único Sinal-On** e, em seguida, verifique a opção **Enable SAML 2.0** para executar os seguintes passos.

    ![Configuração de administração Wandera RADAR](./media/wandera-tutorial/config01.png)

    a. Clique em **Ou introduza manualmente os campos necessários.**

    b. Na caixa de texto **IdP EntityId,** cole o valor **do identificador Azure AD,** que copiou a partir do portal Azure.

    c. Abra o Metdata XML da Federação em bloco de notas, copie o seu conteúdo e cole-o na caixa de texto do **Certificado IdP Public X.509.**

    d. Clique em **Guardar**.

### <a name="create-wandera-radar-admin-test-user"></a>Criar o utilizador de teste de administração Wandera RADAR

Nesta secção, cria-se um utilizador chamado B.Simon in Wandera RADAR Admin. Work with [Wandera RADAR Admin support team](https://www.wandera.com/about-wandera/contact/#supportsection) to add the users in the Wandera RADAR Admin platform. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo do Wandera RADAR no Painel de Acesso, deverá ser automaticamente inscrito no Wandera RADAR Admin para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)
