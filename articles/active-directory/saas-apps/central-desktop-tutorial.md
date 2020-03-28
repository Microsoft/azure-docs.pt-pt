---
title: 'Tutorial: Integração do Diretório Ativo Azure com desktop central [ Desktop] Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Central Desktop.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b805d485-93db-49b4-807a-18d446c7090e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fda8e928b530001faeae34c364dfed91d7620f0a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "73157509"
---
# <a name="tutorial-azure-active-directory-integration-with-central-desktop"></a>Tutorial: Integração de Diretório Ativo Azure com Desktop Central

Neste tutorial, aprende-se a integrar o Desktop Central com o Azure Ative Directory (Azure AD).
Integrar o Desktop Central com a AD Azure proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD quem tem acesso ao Desktop Central.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Desktop Central (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o Desktop Central, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Subscrição ativada por sinal único do Central Desktop

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Central Desktop suporta **SP** iniciado SSO

## <a name="adding-central-desktop-from-the-gallery"></a>Adicionar desktop central da galeria

Para configurar a integração do Desktop Central em Azure AD, você precisa adicionar o Desktop Central da galeria à sua lista de aplicações SaaS geridas.

**Para adicionar o Desktop Central da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite o **Ambiente**central , selecione **O Ambiente** central do painel de resultados e, em seguida, clique em **Adicionar** o botão para adicionar a aplicação.

     ![Ambiente central na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa um único sinal de Azure AD com o Central Desktop com base num utilizador de teste chamado **Britta Simon**.
Para que o início de sessão simples funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Desktop Central.

Para configurar e testar o único sinal de Azure AD com o Desktop Central, é necessário completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o sign-on único](#configure-central-desktop-single-sign-on)** do ambiente de trabalho central - para configurar as definições de início de sessão simples no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Crie](#create-central-desktop-test-user)** o utilizador central de testes de desktop - para ter uma contrapartida de Britta Simon no Desktop Central que esteja ligada à representação do utilizador da AD Azure.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sinal de Azure AD com o Desktop Central, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página central de integração de aplicações **desktop,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    ![Central Desktop Domain e URLs informações únicas de inscrição](common/sp-identifier-reply.png)

    a. Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://<companyname>.centraldesktop.com`

    b. Na caixa **de identificador,** digite um URL utilizando o seguinte padrão:
    
    | |
    |--|
    | `https://<companyname>.centraldesktop.com/saml2-metadata.php`|
    | `https://<companyname>.imeetcentral.com/saml2-metadata.php`|
    | |

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://<companyname>.centraldesktop.com/saml2-assertion.php`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL, Identificador e Resposta real. Contacte a equipa de suporte do [Cliente Central desktop](https://imeetcentral.com/contact-us) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

5. Na configuração de um único sign-on com a página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** o **Certificado (Raw)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificateraw.png)

6. Na secção Configurar o Ambiente de **Trabalho Central,** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de anúncio sinuoso

    c. Logout URL

### <a name="configure-central-desktop-single-sign-on"></a>Configure Central Desktop Single Sign-On

1. Inscreva-se no seu inquilino do Centro de Ambiente de **Trabalho.**

2. Ir para **Definições**. Selecione **Advanced**, e, em seguida, selecione **Single Sign On**.

    ![Configuração - Avançada](./media/central-desktop-tutorial/ic769563.png "Configuração - Avançada")

3. Na página **De Definições de Sinal Único,** tome os seguintes passos:

    ![Definições únicas de inscrição](./media/central-desktop-tutorial/ic769564.png "Sinal único nas definições")

    a. **Selecione ativar o sinal único SAML v2**.

    b. Na caixa **DEURL SSO,** cola o valor do Identificador de **Anúncios Azure** que copiou do portal Azure.

    c. Na caixa **URL sSO Login,** colhe o valor URL de **Login** que copiou do portal Azure.

    d. Na caixa **DEURL SSO Logout,** colhe o valor URL de **Logout** que copiou do portal Azure.

4. Na secção método de verificação de assinatura de **mensagem,** tome os seguintes passos:

    ![Método de verificação de assinatura de mensagem](./media/central-desktop-tutorial/ic769565.png "Método de Verificação de Assinatura de Mensagem")
    
    a. Selecione **Certificado**.

    b. Na lista de **Certificados SSO,** selecione **RSH SHA256**.

    c. Abra o seu certificado descarregado no Bloco de Notas. Em seguida, copie o conteúdo do certificado e cole-o no campo **de certificado SSO.**

    d. Selecione Exibir um link para a sua página de **login SAMLv2**.

    e. Selecione **Atualizar**.

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

Nesta secção, permite que britta Simon utilize um único sign-on Azure, concedendo acesso ao Desktop Central.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações**e, em seguida, selecione **Central Desktop**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Central Desktop**.

    ![O link Central Desktop na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-central-desktop-test-user"></a>Criar o utilizador de teste central do ambiente de trabalho

Para que os utilizadores da AD Azure possam iniciar o seu início, devem ser aprovisionados na aplicação Central Desktop. Esta secção descreve como criar contas de utilizadores da AD Azure no Centro de Ambiente de Trabalho.

> [!NOTE]
> Para fornecer contas de utilizador da AD Azure, pode utilizar quaisquer outras ferramentas de criação de conta de utilizador do Centro de Ambiente de Trabalho ou APIs fornecidas pelo Central Desktop.

**Para fornecer contas de utilizador para o Ambiente de Trabalho Central:**

1. Inscreva-se no seu inquilino do Centro de Ambiente de Trabalho.

2. Selecione **Pessoas** e, em seguida, **selecione Adicionar Membros Internos**.

    ![People](./media/central-desktop-tutorial/ic781051.png "People")

3. No **endereço de e-mail da caixa de Novos Membros,** digite uma conta Azure AD que pretende fornecer e, em seguida, selecione **Next**.

    ![Endereços de e-mail de novos membros](./media/central-desktop-tutorial/ic781052.png "Endereços de e-mail de novos membros")

4. **Selecione Adicionar membros internos.**

    ![Adicionar membro interno](./media/central-desktop-tutorial/ic781053.png "Adicionar membro interno")
  
   > [!NOTE]
   > Os utilizadores que adiciona recebem um e-mail que inclui um link de confirmação para ativar as suas contas.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo central do ambiente de trabalho no Painel de Acesso, deve ser automaticamente inscrito no Ambiente central para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
