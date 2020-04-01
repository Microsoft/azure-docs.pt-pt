---
title: 'Tutorial: Integração de Diretório Ativo Azure com Keeper Password Manager & Cofre Digital [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Keeper Password Manager & Digital Vault.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: e1a98f6a-2dae-4734-bdbf-4fba742a61d2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71fecbe924c1511c247ff846d3b2a39d309ecf0d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "73159893"
---
# <a name="tutorial-azure-active-directory-integration-with-keeper-password-manager--digital-vault"></a>Tutorial: Integração de Diretório Ativo Azure com Keeper Password Manager & Cofre Digital

Neste tutorial, aprende-se a integrar o Keeper Password Manager & Digital Vault com o Azure Ative Directory (Azure AD).
Integrar o Gestor de Passwords do Guardião & Cofre Digital com AD Azure fornece-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a Keeper Password Manager & Cofre Digital.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Keeper Password Manager & Digital Vault (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Anúncio Azure com o Keeper Password Manager & Digital Vault, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Keeper Password Manager & assinatura de um único sinal do Cofre Digital

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Keeper Password Manager & Digital Vault suporta **SP** iniciado SSO

* Keeper Password Manager & Digital Vault suporta o fornecimento de utilizadores **justo no tempo**

## <a name="adding-keeper-password-manager--digital-vault-from-the-gallery"></a>Adicionar gestor de passwords do Guardião & Cofre Digital da galeria

Para configurar a integração do Keeper Password Manager & Digital Vault em Azure AD, você precisa adicionar Keeper Password Manager & Digital Vault da galeria para a sua lista de aplicações geridas saaS.

**Para adicionar Keeper Password Manager & Digital Vault da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite o **Gestor de Passwords do Guardião & Cofre Digital,** selecione **Keeper Password Manager & Digital Vault** do painel de resultados e, em seguida, clique em **Adicionar** botão para adicionar a aplicação.

     ![Gestor de passwords do Guardião & Cofre Digital na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o single sign-on do Azure AD com o Keeper Password Manager & Digital Vault com base num utilizador de teste chamado **Britta Simon**.
Para um único início de sessão funcionar, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Keeper Password Manager & Digital Vault.

Para configurar e testar o único sinal do Azure AD com o Keeper Password Manager & Digital Vault, é necessário completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **Configure Keeper Password Manager & Digital Vault Single Sign-On** - para configurar as definições de início de sessão simples no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Create Keeper Password Manager & utilizador](#create-keeper-password-manager--digital-vault-test-user)** de teste Digital Vault - para ter uma contrapartida de Britta Simon no Keeper Password Manager & Digital Vault que está ligado à representação do utilizador da AD Azure.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o single sign-on do Azure AD com o Keeper Password Manager & Digital Vault, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), no Gestor de **Passwords do Guardião &** página de integração de aplicações do Cofre Digital, selecione **single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    ![Gestor de passwords do Guardião & domínio digital do cofre e informações de inscrição única dos URLs](common/sp-identifier-reply.png)

    a. No **Sign on URL** text box, digite um URL utilizando o seguinte padrão:`https://{SSO CONNECT SERVER}/sso-connect/saml/login`

    b. Na caixa de texto **identificador (Id da entidade),** digite um URL utilizando o seguinte padrão:`https://{SSO CONNECT SERVER}/sso-connect`

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://{SSO CONNECT SERVER}/sso-connect/saml/sso`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL, Identifier e URL de Resposta real. Contacte [o Gestor de Passwords keeper & equipa](https://keepersecurity.com/contact.html) de suporte ao Cliente digital Vault para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

5. Na configuração de um único sign-on com a página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** para descarregar o **Federation Metadata XML** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

6. No **'Gestor de passwords' de & Digital Vault,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de anúncio sinuoso

    c. Logout URL

### <a name="configure-keeper-password-manager--digital-vault-single-sign-on"></a>Configure Keeper Password Manager & Digital Vault Single Sign-On

Para configurar um único sinal no Gestor de Passwords do Guardião & lado de **configuração do cofre digital,** siga as diretrizes dadas no Guia de Suporte do [Guardião](https://keepersecurity.com/assets/pdf/KeeperSSOConnect_v11.pdf).

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.

    ![As ligações "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do Utilizador](common/user-properties.png)

    a. No campo **Nome** entrar **BrittaSimon.**
  
    b. No **tipo** de campo de nome utilizador **brittasimon\@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar a caixa de verificação de **palavra-passe** e, em seguida, anote o valor que está apresentado na caixa password.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que britta Simon utilize um único sign-on Azure, concedendo acesso ao Keeper Password Manager & Digital Vault.

1. No portal Azure, selecione **Aplicações Empresariais**, selecione **Todas as aplicações,** em seguida, selecione **Keeper Password Manager & Digital Vault**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Keeper Password Manager & Digital Vault**.

    ![O Gestor de Passwords keeper & ligação digital vault na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-keeper-password-manager--digital-vault-test-user"></a>Criar gestor de passwords do Guardião & utilizador de teste digital Vault

Para permitir que os utilizadores de Anúncios Azure entrem no Keeper Password Manager & Digital Vault, devem ser aprovisionados no Keeper Password Manager & Digital Vault. Suportes de aplicação Apenas a tempo o fornecimento do utilizador e após a autenticação os utilizadores serão criados automaticamente na aplicação. Pode contactar o [Suporte do Guardião,](https://keepersecurity.com/contact.html)se pretender configurar os utilizadores manualmente.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no Gestor de Passwords do Guardião & azulejo digital vault no Painel de Acesso, deve ser automaticamente inscrito no Gestor de Passwords do Guardião & Cofre Digital para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

