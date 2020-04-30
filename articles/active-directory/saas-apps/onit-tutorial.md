---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com onit [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Onit.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: bc479a28-8fcd-493f-ac53-681975a5149c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/28/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e908cb76a57f027494230edc648b69da0730ac27
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "70164231"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-onit"></a>Tutorial: Azure Ative Directory integração individual (SSO) com onit

Neste tutorial, aprenderá a integrar o Onit com o Azure Ative Directory (Azure AD). Quando integrar o Onit com o Azure AD, pode:

* Controlo em Azure AD que tem acesso a Onit.
* Ative que os seus utilizadores sejam automaticamente inscritos no Onit com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Onit single sign-on (SSO) enabled subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Onit apoia **SP** iniciado SSO

## <a name="adding-onit-from-the-gallery"></a>Adicionando Onit da galeria

Para configurar a integração do Onit em Azure AD, precisa de adicionar onit da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No **Add da** secção galeria, **escreva Onit** na caixa de pesquisa.
1. Selecione **Onit** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-onit"></a>Configure e teste Azure AD único sign-on para Onit

Configure e teste Azure AD SSO com Onit utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Onit.

Para configurar e testar o Azure AD SSO com o Onit, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure Onit SSO](#configure-onit-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    1. **[Create Onit test user](#create-onit-test-user)** - para ter uma contraparte de B.Simon no Onit que esteja ligada à representação azure AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **Onit,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** introduza os valores para os seguintes campos:

    a. No **Sign on URL** text box, digite um URL utilizando o seguinte padrão:`https://<sub-domain>.onit.com`

    b. Na caixa de texto **identificador (Id da entidade),** digite um URL utilizando o seguinte padrão:`https://<sub-domain>.onit.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e identificador. Contacte a equipa de suporte do [Cliente Onit](https://www.onit.com/support) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. Na secção Certificado de **Assinatura SAML,** clique no botão **Editar** para abrir o diálogo do Certificado de **Assinatura SAML.**

    ![Editar certificado de assinatura SAML](common/edit-certificate.png)

1. Na secção certificado de **assinatura SAML,** copie o **Valor da Impressão Digital** e guarde-o no seu computador.

    ![Copiar valor de impressão digital](common/copy-thumbprint.png)

1. Na secção **set up Onit,** copie os URL(s) adequados com base no seu requisito.

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

Nesta secção, permitirá que b.Simon use o único sign-on Azure, concedendo acesso ao Onit.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Onit**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-onit-sso"></a>Configure Onit SSO

1. Numa janela diferente do navegador web, inicie sessão no site da sua empresa Onit como administrador.

2. No menu em cima, clique em **Administração**.
   
    ![Administration](./media/onit-tutorial/IC791174.png "Administração")

3. Clique em **Editar Corporação**.
   
    ![Editar Corporação](./media/onit-tutorial/IC791175.png "Editar Corporação")
   
4. Clique no separador **Segurança**.
    
    ![Editar Informações da Empresa](./media/onit-tutorial/IC791176.png "Editar Informações da Empresa")

5. No separador **Segurança,** execute os seguintes passos:

    ![Inscrição única](./media/onit-tutorial/IC791177.png "Início de Sessão Único")

    a. Como **Estratégia de Autenticação,** selecione **Single Sign On e Password**.
    
    b. Na caixa de texto **IDP Target URL,** colá o valor do URL de **Login,** que copiou do portal Azure.

    c. Na caixa de texto URL de **logout idp,** colá o valor do URL de **Logout,** que copiou do portal Azure.

    d. Na caixa de texto **IDP Cert Fingerprint (SHA1),** cola o valor de **impressão digital** do certificado, que copiou do portal Azure.

### <a name="create-onit-test-user"></a>Criar utilizador de teste Onit

Para permitir que os utilizadores de Anúncios Azure entrem no Onit, devem ser aprovisionados no Onit. No caso do Onit, o provisionamento é uma tarefa manual.

**Para configurar o fornecimento do utilizador, execute os seguintes passos:**

1. Inscreva-se no site da sua empresa **Onit** como administrador.

2. Clique em **Adicionar Utilizador**.

    ![Administration](./media/onit-tutorial/IC791180.png "Administração")

3. Na página de diálogo **Adicionar utilizador,** execute os seguintes passos:

    ![Adicionar Utilizador](./media/onit-tutorial/IC791181.png "Adicionar Utilizador")

    a. Digite o **Nome** e o Endereço de **E-mail** de uma conta Azure AD válida que pretende fornecer nas caixas de texto relacionadas.

    b. Clique em **Criar**.

    > [!NOTE]
    > O titular da conta Azure Ative Directory recebe um e-mail e segue um link para confirmar a sua conta antes de se tornar ativo.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Onit no Painel de Acesso, deve ser automaticamente inscrito no Onit para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente onit com Azure AD](https://aad.portal.azure.com/)