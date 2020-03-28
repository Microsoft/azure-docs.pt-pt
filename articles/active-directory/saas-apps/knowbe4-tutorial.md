---
title: 'Tutorial: Integração do Diretório Ativo Azure com formação de sensibilização para a segurança knowBe4 Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o KnowBe4 Security Awareness Training.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: b80d2212-cc5f-4adb-836c-570640810c39
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f774357dc2fd1e37466c2c338e8e8616ae599d12
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "73159667"
---
# <a name="tutorial-azure-active-directory-integration-with-knowbe4-security-awareness-training"></a>Tutorial: Integração do Diretório Ativo Azure com formação de sensibilização para a segurança knowBe4

Neste tutorial, aprende-se a integrar a Formação de Sensibilização para a Segurança do KnowBe4 com o Diretório Ativo Azure (Azure AD).
Integrar a Formação de Sensibilização para a Segurança knowBe4 com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a KnowBe4 Security Awareness Training.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no KnowBe4 Security Awareness Training (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o KnowBe4 Security Awareness Training, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* KnowBe4 Security Awareness Training assinatura única ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* KnowBe4 Security Awareness Training apoia **SP** iniciado SSO

* KnowBe4 Security Awareness Training suporta o provisionamento de utilizadores **justos no tempo**

## <a name="adding-knowbe4-security-awareness-training-from-the-gallery"></a>Adicionar formação de sensibilização para a segurança knowBe4 da galeria

Para configurar a integração do KnowBe4 Security Awareness Training em Azure AD, você precisa adicionar KnowBe4 Security Awareness Training da galeria à sua lista de aplicações geridas saaS.

**Para adicionar KnowBe4 Security Awareness Training da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **KnowBe4 Security Awareness Training**, selecione **KnowBe4 Security Awareness Training** from result panel e, em seguida, clique em **Adicionar** botão para adicionar a aplicação.

     ![Formação de Sensibilização para a Segurança KnowBe4 na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa um único sign-on azure com o KnowBe4 Security Awareness Training baseado num utilizador de teste chamado **Britta Simon**.
Para um único início de inscrição para funcionar, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no KnowBe4 Security Awareness Training.

Para configurar e testar o único sign-on azure ad com o KnowBe4 Security Awareness Training, você precisa completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure KnowBe4 Security Awareness Training Single Sign-On](#configure-knowbe4-security-awareness-training-single-sign-on)** - para configurar as definições de entrada única no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Create KnowBe4 Security Awareness Training user](#create-knowbe4-security-awareness-training-test-user)** - para ter uma contrapartida de Britta Simon no KnowBe4 Security Awareness Training que está ligada à representação da AD Azure do utilizador.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sign-on da Azure AD com o KnowBe4 Security Awareness Training, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações de formação de sensibilização para **a segurança KnowBe4,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    ![KnowBe4 Security Awareness Training Domain and URLs single sign-on information](common/sp-identifier.png)

    a. No **Sign on URL** text box, digite um URL utilizando o seguinte padrão:`https://<companyname>.KnowBe4.com/auth/saml/<instancename>`

    > [!NOTE]
    > O sinal sobre o valor url não é real. Atualize este valor com o sinal real no URL. Contacte a equipa de apoio ao cliente de formação de [segurança KnowBe4](mailto:support@KnowBe4.com) para obter este valor. Também pode consultar o padrão mostrado na secção **de configuração SAML Básica** no portal Azure.

    b. Na caixa de texto **identificador (Id** da entidade), digite o valor de cadeia:`KnowBe4`

    > [!NOTE]
    > Isto é sensível ao caso.

5. Na configuração de um único sign-on com a página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** o **Certificado (Raw)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificateraw.png)

6. Na secção de Formação de Sensibilização para a **Segurança KnowBe4,** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de anúncio sinuoso

    c. Logout URL

### <a name="configure-knowbe4-security-awareness-training-single-sign-on"></a>Configure KnowBe4 Formação de Sensibilização para a Segurança Único Sign-On

Para configurar um único sign-on no lado de Formação de Sensibilização para a **Segurança KnowBe4,** você precisa enviar o Certificado descarregado **(Raw)** e URLs copiados apropriados do portal Azure para a equipe de apoio de formação de consciência de [segurança KnowBe4](mailto:support@KnowBe4.com). Eles definiram esta definição para ter a ligação SAML SSO corretamente definida em ambos os lados.

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

Nesta secção, permite que Britta Simon utilize um único sign-on Azure, concedendo acesso ao KnowBe4 Security Awareness Training.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações,** em seguida, selecione **KnowBe4 Security Awareness Training**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **KnowBe4 Security Awareness Training**.

    ![O link knowBe4 Security Awareness Training na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-knowbe4-security-awareness-training-test-user"></a>Criar o utilizador de teste de formação de sensibilização para a segurança KnowBe4

O objetivo desta secção é criar uma utilizadora chamada Britta Simon no KnowBe4 Security Awareness Training. A KnowBe4 Security Awareness Training suporta o fornecimento just-in-time, que está por defeito habilitado.

Não há nenhum item de ação para si nesta secção. Um novo utilizador é criado durante uma tentativa de aceder ao KnowBe4 Security Awareness Training se ainda não existir.

> [!NOTE]
> Se precisar de criar um utilizador manualmente, tem de contactar a equipa de apoio ao [KnowBe4 Security Awareness Training](mailto:support@KnowBe4.com).

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo knowBe4 Security Awareness Training no Painel de Acesso, deve ser automaticamente inscrito no KnowBe4 Security Awareness Training para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
