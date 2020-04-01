---
title: 'Tutorial: Integração de Diretório Ativo Azure com Veritas Enterprise Vault.cloud SSO [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Veritas Enterprise Vault.cloud SSO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c47894b1-f5df-4755-845d-f12f4c602dc4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 43094cabab3cfc93e0dffa59a15867d01b036d38
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "67087607"
---
# <a name="tutorial-azure-active-directory-integration-with-veritas-enterprise-vaultcloud-sso"></a>Tutorial: Integração de Diretório Ativo Azure com Veritas Enterprise Vault.cloud SSO

Neste tutorial, aprende-se a integrar a Veritas Enterprise Vault.cloud SSO com o Azure Ative Directory (Azure AD).
Integrar veritas Enterprise Vault.cloud SSO com Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a Veritas Enterprise Vault.cloud SSO.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Veritas Enterprise Vault.cloud SSO (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com veritas Enterprise Vault.cloud SSO, você precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Veritas Enterprise Vault.cloud SSO assinatura única ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Veritas Enterprise Vault.cloud SSO suporta **SP** iniciado SSO

## <a name="adding-veritas-enterprise-vaultcloud-sso-from-the-gallery"></a>Adicionando Veritas Enterprise Vault.cloud SSO da galeria

Para configurar a integração do Veritas Enterprise Vault.cloud SSO em Azure AD, você precisa adicionar Veritas Enterprise Vault.cloud SSO da galeria para a sua lista de aplicações geridas saaS.

**Para adicionar Veritas Enterprise Vault.cloud SSO da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **digite Veritas Enterprise Vault.cloud SSO**, **selecione Veritas Enterprise Vault.cloud SSO** do painel de resultados e, em seguida, clique em **Adicionar** botão para adicionar a aplicação.

     ![Veritas Enterprise Vault.cloud SSO na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o único sign-on azure ad com veritas Enterprise Vault.cloud SSO com base num utilizador de teste chamado **Britta Simon**.
Para um único início de inscrição funcionar, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em Veritas Enterprise Vault.cloud SSO.

Para configurar e testar o único sinal de Azure AD com veritas Enterprise Vault.cloud SSO, você precisa completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure Veritas Enterprise Vault.cloud SSO Single Sign-On](#configure-veritas-enterprise-vaultcloud-sso-single-sign-on)** - para configurar as definições de entrada única no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. Crie o utilizador de **[teste Veritas Enterprise Vault.cloud SSO](#create-veritas-enterprise-vaultcloud-sso-test-user)** - para ter uma contrapartida de Britta Simon em Veritas Enterprise Vault.cloud SSO que está ligada à representação do utilizador da AD Azure.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sign-on azure ad com veritas Enterprise Vault.cloud SSO, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **Veritas Enterprise Vault.cloud SSO,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    ![Veritas Enterprise Vault.cloud Domínio SSO e URLs informações únicas de inscrição](common/sp-identifier-reply.png)

    a. Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://personal.ap.archive.veritas.com/CID=<CUSTOMERID>`

    b. Na caixa **de identificador,** utilize o URL de acordo com o Datacenter:

    | Datacenter| do IdP |
    |----------|----|
    | América do Norte| `https://auth.lax.archivecloud.net` |
    | Europa | `https://auth.ams.archivecloud.net` |
    | Ásia-Pacífico| `https://auth.syd.archivecloud.net`|

    c. Na caixa de texto **URL resposta,** utilize o URL de acordo com o Datacenter:

    | Datacenter| do IdP |
    |----------|----|
    | América do Norte| `https://auth.lax.archivecloud.net` |
    | Europa | `https://auth.ams.archivecloud.net` |
    | Ásia-Pacífico| `https://auth.syd.archivecloud.net`|

    > [!NOTE]
    > Este valor não é real. Atualize este valor com o URL de Início de Sinal real. Contacte a equipa de suporte ao [cliente Veritas Enterprise Vault.cloud SSO](https://www.veritas.com/support/.html) para obter este valor. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

5. Na configuração de um único sinal com página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

6. Na **secção Veritas Enterprise Vault.cloud SSO,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure AD

    c. Logout URL

### <a name="configure-veritas-enterprise-vaultcloud-sso-single-sign-on"></a>Configure Veritas Enterprise Vault.cloud SSO Single Sign-On

Para configurar um único sinal no lado **Veritas Enterprise Vault.cloud SSO,** você precisa enviar o Certificado descarregado **(Base64)** e URLs copiados apropriados do portal Azure para [Veritas Enterprise Vault.cloud SSO equipe](https://www.veritas.com/support/.html)de suporte . Eles definiram esta definição para ter a ligação SAML SSO corretamente definida em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.

    ![As ligações "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do Utilizador](common/user-properties.png)

    a. No campo **Nome** entrar **BrittaSimon.**
  
    b. No **User name** tipo brittasimon@yourcompanydomain.extensionde campo do nome do utilizador . Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar a caixa de verificação de **palavra-passe** e, em seguida, anote o valor que está apresentado na caixa password.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que Britta Simon use o único sign-on Azure, concedendo acesso ao Veritas Enterprise Vault.cloud SSO.

1. No portal Azure, selecione **Aplicações Empresariais**, selecione **Todas as aplicações,** em seguida, selecione **Veritas Enterprise Vault.cloud SSO**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, **selecione Veritas Enterprise Vault.cloud SSO**.

    ![O link Veritas Enterprise Vault.cloud SSO na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-veritas-enterprise-vaultcloud-sso-test-user"></a>Criar veritas Enterprise Vault.cloud SSO utilizador de teste

Nesta secção, cria-se um utilizador chamado Britta Simon em Veritas Enterprise Vault.cloud SSO. Trabalhe com a equipa de [suporte Veritas Enterprise Vault.cloud SSO](https://www.veritas.com/support/.html) para adicionar os utilizadores na plataforma Veritas Enterprise Vault.cloud SSO. Os utilizadores devem ser criados e ativados antes de utilizar um único sinal.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Veritas Enterprise Vault.cloud SSO no Painel de Acesso, deve ser automaticamente inscrito no Veritas Enterprise Vault.cloud SSO para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

