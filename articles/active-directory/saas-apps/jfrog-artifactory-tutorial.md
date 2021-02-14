---
title: 'Tutorial: Integração do Diretório Ativo Azure com a JFrog Artifactory | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o JFrog Artifactory.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/16/2019
ms.author: jeedes
ms.openlocfilehash: f0fafa5c0cc2e0b1bf0f4e11db3265824feb5296
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100374711"
---
# <a name="tutorial-integrate-jfrog-artifactory-with-azure-active-directory"></a>Tutorial: Integre o Artígino JFrog com o Azure Ative Directory

Neste tutorial, você vai aprender a integrar JFrog Artifactory com Azure Ative Diretório (Azure AD). Quando integrar o JFrog Artifactory com Azure AD, pode:

* Controlo em Azure AD que tem acesso à JFrog Artifactory.
* Permita que os seus utilizadores sejam automaticamente inscritos na JFrog Artifactory com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* JFrog Artifactory única assinatura ativada (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* JFrog Artifactory apoia **SP e IDP** iniciado SSO
* JFrog Artifactory suporta **provisão de** utilizadores just in time

## <a name="adding-jfrog-artifactory-from-the-gallery"></a>Adicionando JFrog Artifactory da galeria

Para configurar a integração do JFrog Artifactory em AD Azure, precisa adicionar o JFrog Artifactory da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Add da secção **de galeria,** digite **JFrog Artifactory** na caixa de pesquisa.
1. Selecione **O Artefacto JFrog** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Configure e teste Azure AD SSO com AFrog Artifactory usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado na JFrog Artifactory.

Para configurar e testar a Azure AD SSO com a JFrog Artifactory, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure jFrog Artifactory SSO](#configure-jfrog-artifactory-sso)** - para configurar as definições de Sign-On única no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
5. **[Create JFrog Artifactory test user](#create-jfrog-artifactory-test-user)** - para ter uma contraparte de B.Simon em JFrog Artifactory que está ligada à representação AD AD do utilizador.
6. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **JFrog Artifactory,** encontre a secção **Gerir** e selecione **Single sign-on**.
1. Na página **de método de inscrição única,** selecione **SAML**.
1. Na **configuração single Sign-On com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `<servername>.jfrog.io`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:
    
    - Para o Artifactory 6.x: `https://<servername>.jfrog.io/artifactory/webapp/saml/loginResponse`
    - Para o Artifactory 7.x: `https://<servername>.jfrog.io/<servername>/webapp/saml/loginResponse`

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:
    - Para o Artifactory 6.x: `https://<servername>.jfrog.io/<servername>/webapp/`
    - Para o Artifactory 7.x: `https://<servername>.jfrog.io/ui/login`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL de inscrição. Contacte a [equipa de apoio ao cliente da JFrog Artifactory](https://support.jfrog.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. A aplicação JFrog Artifactory espera as afirmações SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos. Clique no ícone **Editar** para abrir o diálogo Atributos do Utilizador.

    ![A screenshot mostra atributos do utilizador com o controlo de edição chamado.](common/edit-attribute.png)

1. Além do acima, a JFrog Artifactory espera que uma série de atributos adicionais sejam repercutidos na resposta SAML. Na secção **"Atributos & Reclamações** do Utilizador no diálogo **'Pré-visualização)** do Grupo, execute os seguintes passos:

    a. Clique na **caneta** ao lado **de Grupos devolvidos em reivindicação**.

    ![A screenshot mostra atributos do utilizador & Reclamações com o ícone editar selecionado.](./media/jfrog-artifactory-tutorial/config04.png)

    ![A screenshot mostra a secção 'Reclamações de Grupo' com todos os grupos selecionados.](./media/jfrog-artifactory-tutorial/config05.png)

    b. Selecione **Todos os Grupos** da lista de rádio.

    c. Clique em **Guardar**.

4. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** localizar o **Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](./media/jfrog-artifactory-tutorial/certificate-base.png)

6. Configure o Artifactory (Nome do Fornecedor de Serviços SAML) com o campo 'Identificador' (ver passo 4). Na secção **Configuração do Artefacto JFrog,** copie os URL(s) apropriados com base na sua exigência.

   - Para o Artifactory 6.x: `https://<servername>.jfrog.io/artifactory/webapp/saml/loginResponse` 
   - Para o Artifactory 7.x: `https://<servername>.jfrog.io/<servername>/webapp/saml/loginResponse`

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-jfrog-artifactory-sso"></a>Configurar JFrog Artifactory SSO

Tudo o que precisa para configurar um único sinal no lado do **Artefacto JFrog** é configurável pelo administrador artifactório no ecrã de configugração SAML.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao JFrog Artifactory.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **JFrog Artifactory**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

### <a name="create-jfrog-artifactory-test-user"></a>Criar utilizador de teste de artefactos JFrog

Nesta secção, um utilizador chamado B.Simon é criado na JFrog Artifactory. A JFrog Artifactory suporta o fornecimento de utilizadores just-in-time, o que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no JFrog Artifactory, um novo é criado após a autenticação.

### <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo do Artefacto JFrog no Painel de Acesso, deverá ser automaticamente inscrito no Artilório JFrog para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)