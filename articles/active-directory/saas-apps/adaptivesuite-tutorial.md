---
title: 'Tutorial: Integração do Diretório Ativo Azure com Insights Adaptativos [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e os Adaptive Insights.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 13af9d00-116a-41b8-8ca0-4870b31e224c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/19/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf05fc30a2b111a12c0b8e131db5117ec784075b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "74232126"
---
# <a name="tutorial-integrate-adaptive-insights-with-azure-active-directory"></a>Tutorial: Integrar Insights Adaptativos com Diretório Ativo Azure

Neste tutorial, aprenderá a integrar Insights Adaptativos com o Diretório Ativo Azure (Azure AD). Quando integrar Insights Adaptivos com AD Azure, pode:

* Controlo em Azure AD que tem acesso a Insights Adaptivos.
* Ative que os seus utilizadores sejam automaticamente inscritos em Insights Adaptive com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A subscrição ativada por Insights Adaptivos (SSO) permitiu a subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Insights Adaptive suporta **IDP** iniciado SSO

## <a name="adding-adaptive-insights-from-the-gallery"></a>Adicionar Insights Adaptivos da galeria

Para configurar a integração de Insights Adaptativos em Azure AD, precisa adicionar Insights Adaptativos da galeria à sua lista de aplicações geridas do SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No **Add da** secção galeria, **digite Insights Adaptivos** na caixa de pesquisa.
1. Selecione **Adaptive Insights** do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Configure e teste Azure AD SSO com Insights Adaptive utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em Insights Adaptive.

Para configurar e testar o Azure AD SSO com Insights Adaptive, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure Insights Adaptive SSO](#configure-adaptive-insights-sso)** - para configurar as definições de início de sessão simples no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
5. **[Create Adaptive Insights test user](#create-adaptive-insights-test-user)** - para ter uma contrapartida de B.Simon em Insights Adaptive que está ligado à representação do utilizador da AD Azure.
6. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **Adaptive Insights,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **Select a Single sign-on,** selecione **SAML**.
1. Na configuração do Single Sign-On com a página **SAML,** clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:`https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    > [!NOTE]
    > Pode obter valores de URL do Identifier (Id da Entidade) e da Resposta a partir da página de **Definições SAML SSO** da Adaptive Insights.

4. Na configuração de um único sinal com página **SAML,** na secção certificado de **assinatura SAML,** encontre **certificado (Base64)** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

6. Na secção **Configurar Insights Adaptive,** copie os URL(s) adequados com base na sua exigência.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-adaptive-insights-sso"></a>Configure Adaptive Insights SSO

1. Numa janela diferente do navegador web, inscreva-se no site da empresa Adaptive Insights como administrador.

2. Ir para a **Administração.**

    ![Administração](./media/adaptivesuite-tutorial/ic805644.png "Administrador")

3. Na secção **Utilizadores e Funções,** clique em **Definições SAML SSO**.

    ![Gerir as definições saml SSO](./media/adaptivesuite-tutorial/ic805645.png "Gerir as definições saml SSO")

4. Na página **Definições SAML SSO,** execute os seguintes passos:

    ![Definições SAML SSO](./media/adaptivesuite-tutorial/ic805646.png "Definições SAML SSO")

    a. Na caixa de texto do nome do **fornecedor de identidade,** digite um nome para a sua configuração.

    b. Colar o valor do **identificador Azure AD** copiado do portal Azure para a caixa de texto ID do fornecedor de **identidade.**

    c. Colar o valor URL de **Login** copiado do portal Azure para a caixa de texto **URL SSO** do fornecedor de identidade.

    d. Colar o valor URL de **Logout** copiado do portal Azure para a caixa de texto URL de **logout personalizada.**

    e. Para fazer o upload do certificado descarregado, clique em **Escolher o ficheiro**.

    f. Selecione o seguinte, para:

     * **Id do utilizador SAML**, selecione **o nome de utilizador Adaptive Insights do utilizador**.

     * Localização do id do **utilizador SAML**, selecione **id do utilizador no NameID of Subject**.

     * **Formato NomeId SAML,** selecione **endereço de e-mail**.

     * **Ativar o SAML,** selecionar **permitir o SAML SSO e iniciar sessão directade insights adaptáveis**.

    g. Copie **Insights Adaptivos SSO URL** e cole nas caixas de texto URL **do Identificador (Id** da Entidade) e **resposta** na secção **basic SAML Configuração** no portal Azure.

    h. Clique em **Guardar**.

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

Nesta secção, permitirá que b.Simon utilize um único sign-on Azure, concedendo acesso a Insights Adaptivos.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Insights Adaptivos**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

### <a name="create-adaptive-insights-test-user"></a>Criar utilizadores de teste de Insights Adaptive Insights

Para permitir que os utilizadores de Anúncios Azure entrem em Insights Adaptivos, devem ser aprovisionados em Insights Adaptive. No caso de Insights Adaptivos, o provisionamento é uma tarefa manual.

**Para configurar o fornecimento do utilizador, execute os seguintes passos:**

1. Inscreva-se no site da empresa **Adaptive Insights** como administrador.

2. Ir para a **Administração.**

   ![Administração](./media/adaptivesuite-tutorial/IC805644.png "Administrador")

3. Na secção **Utilizadores e Funções,** clique em **Utilizadores.**

   ![Adicionar Utilizador](./media/adaptivesuite-tutorial/IC805648.png "Adicionar Utilizador")

4. Na secção **Novo Utilizador,** execute os seguintes passos:

   ![Submeter](./media/adaptivesuite-tutorial/IC805649.png "Submeter")

   a. Digite o nome de **utilizador,** nome de **utilizador,** **e-mail,** **palavra-passe** de um utilizador ativo do Diretório Azure válido que pretende fornecer nas caixas de texto relacionadas.

   b. Selecione um **Papel**.

   c. Clique em **Submeter**.

> [!NOTE]
> Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador adaptáveis insights ou APIs fornecidas pela Adaptive Insights para fornecer contas de utilizador Azure AD.

### <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Adaptive Insights no Painel de Acesso, deve ser automaticamente inscrito nos Insights Adaptivos para os quais configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

