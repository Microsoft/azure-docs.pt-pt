---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com a SumoLogic [ Integração de um único sign-on) com a SumoLogic [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a SumoLogic.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: fbb76765-92d7-4801-9833-573b11b4d910
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/03/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: de6d941c6efe42993b6bad7c556582831d179250
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "75659751"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sumologic"></a>Tutorial: Azure Ative Diretório integração individual (SSO) com a SumoLogic

Neste tutorial, você vai aprender a integrar a SumoLogic com o Azure Ative Directory (Azure AD). Quando integrar a SumoLogic com a Azure AD, pode:

* Controlo em Azure AD que tem acesso à SumoLogic.
* Ative que os seus utilizadores sejam automaticamente inscritos na SumoLogic com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* SumoLogic single sign-on (SSO) enabled subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* SumoLogic suporta **IDP** iniciado SSO

## <a name="adding-sumologic-from-the-gallery"></a>Adicionando SumoLogic da galeria

Para configurar a integração da SumoLogic em Azure AD, precisa adicionar a SumoLogic da galeria à sua lista de aplicações geridas do SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No **Add da** secção galeria, **digite SumoLogic** na caixa de pesquisa.
1. Selecione **SumoLogic** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sumologic"></a>Configure e teste Azure AD único sign-on para SumoLogic

Configure e teste Azure AD SSO com SumoLogic utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado na SumoLogic.

Para configurar e testar o Azure AD SSO com a SumoLogic, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    * Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure sumoLogic SSO](#configure-sumologic-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    * **[Crie um utilizador de teste SumoLogic](#create-sumologic-test-user)** - para ter uma contrapartida de B.Simon na SumoLogic que esteja ligada à representação da AD Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **SumoLogic,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na configuração de um único sessão com a página **SAML,** introduza os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:

    | |
    |--|
    | `https://service.sumologic.com`|
    | `https://<tenantname>.us2.sumologic.com`|
    | `https://<tenantname>.us4.sumologic.com`|
    | `https://<tenantname>.eu.sumologic.com`|
    | `https://<tenantname>.jp.sumologic.com`|
    | `https://<tenantname>.de.sumologic.com`|
    | `https://<tenantname>.ca.sumologic.com`|

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:

    | |
    |--|
    | `https://service.sumologic.com/sumo/saml/consume/<tenantname>`|
    | `https://service.us2.sumologic.com/sumo/saml/consume/<tenantname>`|
    | `https://service.us4.sumologic.com/sumo/saml/consume/<tenantname>`|
    | `https://service.eu.sumologic.com/sumo/saml/consume/<tenantname>`|
    | `https://service.jp.sumologic.com/sumo/saml/consume/<tenantname>`|
    | `https://service.de.sumologic.com/sumo/saml/consume/<tenantname>`|
    | `https://service.ca.sumologic.com/sumo/saml/consume/<tenantname>`|
    | `https://service.au.sumologic.com/sumo/saml/consume/<tenantname>`|

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação e resposta real. Contacte a equipa de suporte ao [Cliente SumoLogic](https://www.sumologic.com/contact-us/) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. A aplicação SumoLogic espera as afirmações do SAML num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação SumoLogic espera que poucos atributos sejam retransmitidos na resposta SAML que são mostradas abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.

    |  Nome | Atributo fonte |
    | ---------------| --------------- |
    | FirstName | user.givenname |
    | LastName | utilizador.sobrenome |
    | Funções | user.atribuídos |

    > [!NOTE]
    > Clique [aqui](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management) para saber como configurar o **Papel** em Azure AD.

1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

1. Na secção Configurar a **SumoLogic,** copie os URL(s) adequados com base no seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, você vai criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, **selecione Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo de nome username@companydomain.extensiondo **Utilizador,** introduza o . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permitirá que b.Simon utilize um único sign-on Azure, concedendo acesso à SumoLogic.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **SumoLogic**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-sumologic-sso"></a>Configure SumoLogic SSO

1. Numa janela diferente do navegador web, inscreva-se no site da sua empresa SumoLogic como administrador.

1. Ir para **gerir \> **a segurança.

    ![Gerir](./media/sumologic-tutorial/ic778556.png "Gerir")

1. Clique em **SAML**.

    ![Definições globais de segurança](./media/sumologic-tutorial/ic778557.png "Definições globais de segurança")

1. A partir da **configuração Selecione uma configuração ou crie uma nova** lista, selecione **Azure AD,** e, em seguida, clique em **Configurar**.

    ![Configure SAML 2.0](./media/sumologic-tutorial/ic778558.png "Configure SAML 2.0")

1. No diálogo **Configure SAML 2.0,** execute os seguintes passos:

    ![Configure SAML 2.0](./media/sumologic-tutorial/ic778559.png "Configure SAML 2.0")

    a. Na caixa de texto **'Nome configuração',** **escreva AD Azure**.

    b. Selecione **modo dedeceção**.

    c. Na caixa de texto **emitente,** cola o valor do **Identificador AD Azure,** que copiou do portal Azure.

    d. Na caixa de texto URL **do Pedido de Authn,** colá o valor do URL de **Login,** que copiou do portal Azure.

    e. Abra o seu certificado codificado base-64 no bloco de notas, copie o conteúdo do mesmo na sua área de recção e, em seguida, cole todo o Certificado na caixa de texto **X.509 Certificate.**

    f. Como Atributo de **e-mail,** selecione **Use o tema SAML**.  

    g. Selecione Configuração de **início de sessão sp**.

    h. Na caixa de texto **Sin Path,** **digite Azure** e clique **em Guardar**.

### <a name="create-sumologic-test-user"></a>Criar o utilizador de teste SumoLogic

Para permitir que os utilizadores da AD Azure assinem a SumoLogic, devem ser aprovisionados na SumoLogic. No caso da SumoLogic, o provisionamento é uma tarefa manual.

**Para fornecer uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se no seu inquilino **SumoLogic.**

1. Ir para **Gerir \> utilizadores**.

    ![Utilizadores](./media/sumologic-tutorial/ic778561.png "Utilizadores")

1. Clique em **Adicionar**.

    ![Utilizadores](./media/sumologic-tutorial/ic778562.png "Utilizadores")

1. No diálogo **new user,** execute os seguintes passos:

    ![Novo Utilizador](./media/sumologic-tutorial/ic778563.png "Novo Utilizador")

    a. Digite as informações relacionadas da conta Azure AD que pretende fornecer no **Primeiro Nome,** **Apelido**e caixas de texto de **e-mail.**
  
    b. Selecione um papel.
  
    c. As **Status,** selecione **Ative**.
  
    d. Clique em **Guardar**.

> [!NOTE]
> Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador SumoLogic ou APIs fornecidas pela SumoLogic para fornecer contas de utilizador Azure AD.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo SumoLogic no Painel de Acesso, deve ser automaticamente inscrito na SumoLogic para a qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente sumoLogic com Azure AD](https://aad.portal.azure.com/)