---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com a ThousandEyes [ Diretório Ativo) integração com a ThousandEyes [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o ThousandEyes.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 790e3f1e-1591-4dd6-87df-590b7bf8b4ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/04/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd179049f88454c62244cf1819cee08ef78d0633
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "72373237"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-thousandeyes"></a>Tutorial: Azure Ative Diretório integração individual (SSO) com a ThousandEyes

Neste tutorial, aprenderá a integrar o ThousandEyes com o Azure Ative Directory (Azure AD). Quando integrar o ThousandEyes com o Azure AD, pode:

* Controlo em Azure AD que tem acesso a ThousandEyes.
* Ative que os seus utilizadores sejam automaticamente inscritos na ThousandEyes com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A subscrição ativada por um único sinal (SSO) da ThousandEyes ..

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* MilEyes suporta **SP e IDP** iniciadoS SSO
* MilEyes suporta fornecimento [ **automatizado** de utilizadores](https://docs.microsoft.com/azure/active-directory/saas-apps/thousandeyes-provisioning-tutorial)

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia, pelo que apenas uma instância pode ser configurada num inquilino.

## <a name="adding-thousandeyes-from-the-gallery"></a>Adicionando MilOlhos da galeria

Para configurar a integração do ThousandEyes no Azure AD, precisa de adicionar milEyes da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** digite **MilOlhos** na caixa de pesquisa.
1. Selecione **ThousandEyes** do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-thousandeyes"></a>Configure e teste Azure AD single sign-on para ThousandEyes

Configure e teste Azure AD SSO com ThousandEyes utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em ThousandEyes.

Para configurar e testar o Azure AD SSO com o ThousandEyes, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    * Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure ThousandEyes SSO](#configure-thousandeyes-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    * **[Create ThousandEyes test user](#create-thousandeyes-test-user)** - para ter uma contrapartida de B.Simon em ThousandEyes que está ligada à representação da AD Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **ThousandEyes,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **de Configuração Básica do SAML,** a aplicação está pré-configurada e os URLs necessários já estão pré-povoados com o Azure. O utilizador precisa de guardar a configuração clicando no botão **Guardar.**

1. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    Na caixa de texto **de URL sign-on,** escreva um URL:`https://app.thousandeyes.com/login/sso`

1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

1. Na secção **Configurar MilOlhos,** copie os URL(s) adequados com base no seu requisito.

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

Nesta secção, permitirá que b.Simon use o único sign-on Azure, concedendo acesso ao ThousandEyes.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **ThousandEyes**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-thousandeyes-sso"></a>Configure MilOlhos SSO

1. Numa janela diferente do navegador web, inscreva-se no site da sua empresa **ThousandEyes** como administrador.

2. No menu em cima, clique em **Definições**.

    ![Definições](./media/thousandeyes-tutorial/ic790066.png "Definições")

3. Clique na **conta**

    ![Conta](./media/thousandeyes-tutorial/ic790067.png "Conta")

4. Clique no separador **de autenticação & de segurança.**

    ![Autenticação & de segurança](./media/thousandeyes-tutorial/ic790068.png "Autenticação & de segurança")

5. Na secção **Setup Single Sign-On,** execute os seguintes passos:

    ![Configuração de inscrição única](./media/thousandeyes-tutorial/ic790069.png "Configuração de inscrição única")

    a. **Selecione ativar um único sinal .**

    b. Na caixa de texto URL da **página de login,** colhe o **URL de Login,** que copiou do portal Azure.

    c. Na caixa de texto URL da **página de logout,** colhe o **URL de Logout,** que copiou do portal Azure.

    d. Caixa de texto **emitente** de fornecedor de identidade, identificador **ad azure aD,** que copiou do portal Azure.

    e. No Certificado de **Verificação,** clique em **Escolher ficheiro**, e depois faça upload do certificado que descarregou do portal Azure.

    f. Clique em **Guardar**.

### <a name="create-thousandeyes-test-user"></a>Criar utilizadores de teste MilEyes

O objetivo desta secção é criar um utilizador chamado Britta Simon em ThousandEyes. O ThousandEyes suporta o fornecimento automático de utilizadores, que é por defeito. Pode encontrar mais detalhes [aqui](thousandeyes-provisioning-tutorial.md) sobre como configurar o fornecimento automático de utilizadores.

**Se precisar de criar manualmente o utilizador, execute os seguintes passos:**

1. Inscreva-se no site da sua empresa ThousandEyes como administrador.

2. Clique em **Definições**.

    ![Definições](./media/thousandeyes-tutorial/IC790066.png "Definições")

3. Clique na **conta**.

    ![Conta](./media/thousandeyes-tutorial/IC790067.png "Conta")

4. Clique no separador **Contas & Utilizadores.**

    ![Contas & Utilizadores](./media/thousandeyes-tutorial/IC790073.png "Contas & Utilizadores")

5. Na secção **Adicionar Utilizadores & Contas,** execute os seguintes passos:

    ![Adicionar Contas de Utilizador](./media/thousandeyes-tutorial/IC790074.png "Adicionar Contas de Utilizador")

    a. Em **Nome** da caixa de texto, digite o nome do utilizador como **B.Simon**.

    b. Na caixa de texto **por e-mail,** digite o e-mail do utilizador como b.simon@contoso.com.

    b. Clique em **adicionar novo utilizador à conta**.

    > [!NOTE]
    > O titular da conta Azure Ative Diretório receberá um e-mail incluindo um link para confirmar e ativar a conta.

> [!NOTE]
> Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador milEyes ou APIs fornecidas pela ThousandEyes para fornecer contas de utilizadores do Diretório Ativo Azure.


## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo ThousandEyes no Painel de Acesso, deverá ser automaticamente inscrito nos MilOlhos para os quais configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente MilOlhos com Anúncio Azure](https://aad.portal.azure.com/)

- [Configurar o fornecimento de utilizadores](https://docs.microsoft.com/azure/active-directory/saas-apps/thousandeyes-provisioning-tutorial)