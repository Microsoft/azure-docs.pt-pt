---
title: 'Tutorial: Integração do Azure Ative Directory com a Wandera Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Wandera.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/30/2019
ms.author: jeedes
ms.openlocfilehash: 4af2fa415c22abe5cd8ac98d510feb44abab0e97
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88523991"
---
# <a name="tutorial-integrate-wandera-with-azure-active-directory"></a>Tutorial: Integre Wandera com diretório ativo Azure

Neste tutorial, você vai aprender a integrar Wandera com Azure Ative Directory (Azure AD). Quando integrar wandera com Azure AD, você pode:

* Controlo em Azure AD que tem acesso a Wandera.
* Ative os seus utilizadores a serem automaticamente inscritos no Wandera com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Wandera assinatura única (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Wandera apoia **IDP** iniciado SSO

## <a name="adding-wandera-from-the-gallery"></a>Adicionando Wandera da galeria

Para configurar a integração do Wandera no AZure AD, precisa adicionar Wandera da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Add a partir da secção **da galeria,** **escreva Wandera** na caixa de pesquisa.
1. Selecione **Wandera** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Configure e teste Azure AD SSO com Wandera usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado em Wandera.

Para configurar e testar a Azure AD SSO com a Wandera, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Wandera SSO](#configure-wandera-sso)** - para configurar as definições de Sign-On único no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
5. **[Create Wandera test user](#create-wandera-test-user)** - para ter uma contraparte de B.Simon em Wandera que está ligada à representação AD AD do utilizador.
6. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Wandera,** encontre a secção **Gerir** e selecione **Single sign-on**.
1. Na página **de método de inscrição única,** selecione **SAML**.
1. Na **configuração de 'Sessão Única' com** a página SAML, clique no ícone edit/pen para **a configuração básica do SAML** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:  `https://radar.wandera.com/saml/acs/<tenant id>`

    > [!NOTE]
    > O valor não é real. Atualizar o valor com o URL de resposta real. Contacte [a equipa de apoio ao Cliente Wandera](https://www.wandera.com/about-wandera/contact/#supportsection) para obter o valor. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração de um único sign-on com** a página SAML, na secção **certificado de assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. No **set up Single Sign-On com** a página SAML, clique no ícone edit/pen para **o Certificado de Assinatura SAML** para editar as definições.

    ![Opção de assinatura](common/signing-option.png)

    1. Selecione **a Opção de Assinatura** como resposta e **afirmação de Sign SAML**.

    1. Selecione **algoritmo de assinatura** como **SHA-256**.

1. Na secção **Configurar Wandera,** copie os URL(s) apropriados com base no seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-wandera-sso"></a>Configure Wandera SSO

1. Para automatizar a configuração dentro do Wandera, é necessário instalar a extensão do **navegador 'As aplicações' Secure Sign-in** clicando **em instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique em **Setup Wandera** irá direcioná-lo para a aplicação Wandera. A partir daí, forneça as credenciais de administrador para assinar em Wandera. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-4.

    ![Configuração de configuração](common/setup-sso.png)

3. Se quiser configurar o Wandera manualmente, abra uma nova janela do navegador web e inscreva-se no site da empresa Wandera como administrador e execute os seguintes passos:

4. No canto superior direito da página, clique em **Definições**  >  **Administração**  >  **Único Sinal-On** e, em seguida, verifique a opção **Enable SAML 2.0** para executar os seguintes passos.

    ![Configuração Wandera](./media/wandera-tutorial/config01.png)

    a. Clique em **Ou introduza manualmente os campos necessários.**

    b. Na caixa de texto **IdP EntityId,** cole o valor **do identificador Azure AD,** que copiou a partir do portal Azure.

    c. Abra o Metdata XML da Federação em bloco de notas, copie o seu conteúdo e cole-o na caixa de texto do **Certificado IdP Public X.509.**

    d. Clique em **Guardar**.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao Wandera.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, **selecione Wandera**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

### <a name="create-wandera-test-user"></a>Criar utilizador de teste Wandera

Nesta secção, cria-se um utilizador chamado B.Simon in Wandera. Trabalhe com [a equipa de apoio da Wandera](https://www.wandera.com/about-wandera/contact/#supportsection) para adicionar os utilizadores na plataforma Wandera. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

### <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Wandera no Painel de Acesso, deverá ser automaticamente inscrito no Wandera para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

