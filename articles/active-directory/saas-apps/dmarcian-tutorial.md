---
title: 'Tutorial: Integração do Diretório Ativo Azure com dmarcian Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o dmarcian.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a04b9383-3a60-4d54-9412-123daaddff3b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/01/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 602c885deca429b56417181971ced495831ba5d3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "68823692"
---
# <a name="tutorial-integrate-dmarcian-with-azure-active-directory"></a>Tutorial: Integrar dmarciano com diretório ativo Azure

Neste tutorial, você vai aprender a integrar dmarcian com o Azure Ative Directory (Azure AD). Quando integrar o dmarcian com a Azure AD, pode:

* Controlo em Azure AD que tem acesso a dmarcian.
* Permita que os seus utilizadores sejam automaticamente inscritos para dmarcian com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* dmarcian única assinatura ativada por assinatura.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* dmarcian suporta **SP e IDP** iniciado SSO

## <a name="adding-dmarcian-from-the-gallery"></a>Adicionando dmarcian da galeria

Para configurar a integração do dmarcian em Azure AD, você precisa adicionar dmarcian da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **da galeria,** digite **dmarcian** na caixa de pesquisa.
1. Selecione **dmarcian** no painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Configure e teste Azure AD SSO com dmarciano utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em dmarcian.

Para configurar e testar o Azure AD SSO com dmarciano, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure dmarcian SSO](#configure-dmarcian-sso)** - para configurar as definições de início de sessão simples no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
5. Criar um utilizador de **[teste dmarcian](#create-dmarcian-test-user)** - para ter uma contrapartida de B.Simon em dmarcian que esteja ligada à representação da AD Azure do utilizador.
6. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **dmarcian,** encontre a secção **Gerir** e selecione **single sign-on**.
1. Na página **Select a Single sign-on,** selecione **SAML**.
1. Na configuração do Single Sign-On com a página **SAML,** clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** Se desejar configurar a aplicação no modo iniciado **idp,** execute os seguintes passos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:

    | |
    | -- |
    | `https://us.dmarcian.com/sso/saml/<ACCOUNT_ID>/sp.xml` |
    | `https://dmarcian-eu.com/sso/saml/<ACCOUNT_ID>/sp.xml` |
    | `https://dmarcian-ap.com/sso/saml/<ACCOUNT_ID>/sp.xml` |

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:

    | |
    |--|
    | `https://us.dmarcian.com/login/<ACCOUNT_ID>/handle/` |
    | `https://dmarcian-eu.com/login/<ACCOUNT_ID>/handle/` |
    | `https://dmarcian-ap.com/login/<ACCOUNT_ID>/handle/` |

5. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:
    
    | |
    |--|
    | `https://us.dmarcian.com/login/<ACCOUNT_ID>` |
    | `https://dmarcian-eu.com/login/<ACCOUNT_ID>` |
    | `https://dmarciam-ap.com/login/<ACCOUNT_ID>` |
     
    > [!NOTE] 
    > Estes valores não são reais. Atualizará estes valores com o URL de Identificação, Resposta e URL de Sinal que é explicado mais tarde no tutorial.

4. Na configuração do Single Sign-On com a página **SAML,** na secção Certificado de **Assinatura SAML,** clique no botão de cópia para copiar o Url de **Metadados da Federação** da Aplicação e guarde-o no seu computador.

    ![O link de descarregamento do Certificado](common/copy-metadataurl.png)

### <a name="configure-dmarcian-sso"></a>Configure dmarcian SSO

1. Para automatizar a configuração dentro do dmarcian, é necessário instalar a extensão de **'Sign-in' de Aplicações Seguras,** clicando em **instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique em **Configurar dmarcian** irá direcioná-lo para a aplicação dmarcian. A partir daí, forneça as credenciais de administração para assinar em dmarcian. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-6.

    ![Configuração de configuração de configuração](common/setup-sso.png)

3. Se quiser configurar o dmarcian manualmente, abra uma nova janela do navegador web e inscreva-se no site da sua empresa dmarcian como administrador e execute os seguintes passos:

4. Clique no **Perfil** no canto superior direito e navegue para **Preferências**.

    ![As Preferências](./media/dmarcian-tutorial/tutorial_dmarcian_pref.png)

5. Desloque-se para baixo e clique na secção **De Início único** e, em seguida, clique em **Configurar**.

    ![O único](./media/dmarcian-tutorial/tutorial_dmarcian_sso.png)

6. Na página **SAML Single Sign-On,** delineie o **Estado** como **Ativado** e execute os seguintes passos:

    ![A autenticação](./media/dmarcian-tutorial/tutorial_dmarcian_auth.png)

    * Em **adicionar dmarcian à sua** secção de Fornecedor de Identidade, clique em **COPY** para copiar o URL do Serviço de Consumidor **de Afirmação** para a sua instância e cole-o na caixa de texto URL de **resposta** na secção **de configuração Básica SAML** no portal Azure.

    * Em **adicionar dmarcian à sua** secção fornecedor de identidade, clique em **COPY** para copiar o ID da **entidade** por exemplo e cole-o na caixa de texto **identifier** na **secção basic SAML Configuração** no portal Azure.

    * No âmbito da secção **de autenticação configurada,** na caixa de texto **datatos** do fornecedor de identidade, a caixa de texto de metadados da Federação de **Aplicações,** que copiou do portal Azure.

    * Sob a secção **de autenticação configurada,** na caixa de texto **De atributos** pasta o url`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    * Em configurar a secção URL de **Login,** copie o URL de **Login** para a sua instância e **cole-o** na caixa de texto URL signon na **secção basic SAML Configuração** no portal Azure.

        > [!Note]
        > Pode modificar o URL de **Login de** acordo com a sua organização.

    * Clique em **Guardar**.

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

Nesta secção, permitirá que B.Simon use o único sign-on Azure, concedendo acesso ao dmarciano.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **dmarcian**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

### <a name="create-dmarcian-test-user"></a>Criar o utilizador de teste dmarcian

Para permitir que os utilizadores da AD Azure assinem o dmarciano, devem ser aprovisionados em dmarcian. Em dmarcian, o provisionamento é uma tarefa manual.

**Para fornecer uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se no Dmarcian como administrador de segurança.

2. Clique no **Perfil** no canto superior direito e navegue para **gerir utilizadores**.

    ![O utilizador](./media/dmarcian-tutorial/tutorial_dmarcian_user.png)

3. No lado direito da secção **Utilizadores SSO,** clique em **Adicionar Novo Utilizador**.

    ![O utilizador adicionar](./media/dmarcian-tutorial/tutorial_dmarcian_addnewuser.png)

4. No popup **Add New User,** execute os seguintes passos:

    ![O novo utilizador](./media/dmarcian-tutorial/tutorial_dmarcian_save.png)

    a. Na caixa de texto **new user email,** introduza o e-mail de utilizador como **brittasimon\@contoso.com**.

    b. Se pretender dar direitos de administração ao utilizador, selecione **Faça do Utilizador um Administrador**.

    c. Clique em **Adicionar Utilizador**.

### <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo dmarciano no Painel de Acesso, deve ser automaticamente inscrito no dmarciano para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

