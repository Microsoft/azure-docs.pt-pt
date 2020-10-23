---
title: 'Tutorial: Integração do Azure Ative Directory com FirmPlay - Advocacia dos Colaboradores para Recrutamento Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o FirmPlay - Advocacia dos Empregados para Recrutamento.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: 2230958fb41d8e42967beeca57cf10ea048d1ef9
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92453472"
---
# <a name="tutorial-azure-active-directory-integration-with-firmplay---employee-advocacy-for-recruiting"></a>Tutorial: Integração do Azure Ative Directory com FirmPlay - Advocacia dos Colaboradores para o Recrutamento

Neste tutorial, aprende-se a integrar o FirmPlay - Advocacia dos Colaboradores para o Recrutamento com o Azure Ative Directory (Azure AD).
Integração da FirmPlay - Advocacia dos Colaboradores para Recrutamento com Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a FirmPlay - Advocacia de Empregados para Recrutamento.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no FirmPlay - Advocacia dos Empregados para Recrutamento (Sign-on Único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD da Azure com a FirmPlay - Advocacia dos Colaboradores para o Recrutamento, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* FirmPlay - Advocacia dos Empregados para recrutar subscrição ativada por assinatura única

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* FirmPlay - Advocacia dos Colaboradores para o Recrutamento apoia **SP** iniciado SSO

## <a name="adding-firmplay---employee-advocacy-for-recruiting-from-the-gallery"></a>Adicionar FirmPlay - Advocacia dos Colaboradores para Recrutamento a partir da galeria

Para configurar a integração da FirmPlay - Advocacia dos Colaboradores para o Recrutamento em AZure AD, é necessário adicionar FirmPlay - Advocacia de Empregados para Recrutamento da galeria à sua lista de aplicações geridas para o SaaS.

**Para adicionar FirmPlay - Advocacia dos Colaboradores para Recrutamento a partir da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **escreva FirmPlay - Advocacia dos Empregados para Recrutamento**, selecione **FirmPlay - Advocacia dos Empregados para Recrutamento** a partir do painel de resultados e, em seguida, clique em **Adicionar** botão para adicionar a aplicação.

     ![FirmPlay - Advocacia dos Colaboradores para Recrutamento na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, você configura e testa Azure AD single sign-on com FirmPlay - Advocacia de Empregado para Recrutamento com base em um utilizador de teste chamado **Britta Simon**.
Para um único sign-on para o trabalho, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no FirmPlay - Advocacia dos Empregados para o Recrutamento.

Para configurar e testar um único sign-on da Azure AD com firmPlay - Advocacia dos Empregados para o Recrutamento, você precisa completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure FirmPlay - Advocacia dos Empregados para recrutar um único sign-on](#configure-firmplay---employee-advocacy-for-recruiting-single-sign-on)** - para configurar as definições de Sign-On única no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create FirmPlay - Advocacia dos Empregados para o Utilizador](#create-firmplay---employee-advocacy-for-recruiting-test-user)** de Testes de Recrutamento - para ter uma contrapartida de Britta Simon em FirmPlay - Advocacia de Empregados para Recrutamento que está ligada à representação AD AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar a Azure AD single sign-on com FirmPlay - Advocacia dos Empregados para recrutamento, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na **página FirmPlay - Advocacia dos Colaboradores para Recrutamento** de Aplicações, selecione Single **sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![FirmPlay - Advocacia dos Empregados para o Recrutamento de Informações de Súm e URLs](common/sp-signonurl.png)

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<your-subdomain>.firmplay.com/`

    > [!NOTE]
    > O valor não é real. Atualize o valor com o URL de Sign-On real. Contacte [a FirmPlay - Advocacia dos Colaboradores para recrutar equipa de apoio](mailto:engineering@firmplay.com) ao cliente para obter o valor. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

6. Na **secção Configuração FirmPlay - Advocacia dos Empregados para a** secção de Recrutamento, copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

### <a name="configure-firmplay---employee-advocacy-for-recruiting-single-sign-on"></a>Configure FirmPlay - Advocacia dos Empregados para recrutar Sign-On Individuais

Para configurar um único sign-on no **FirmPlay - Advocacia dos Empregados para o** lado do recrutamento, você precisa enviar o Certificado descarregado **(Base64)** e URLs copiados apropriados do portal Azure para [FirmPlay - Advocacia de Empregados para equipa de apoio ao recrutamento.](mailto:engineering@firmplay.com) Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD 

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.

    ![Os links "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do utilizador](common/user-properties.png)

    a. No campo **Nome** entra **BrittaSimon**.
  
    b. No tipo de campo **do nome do utilizador** brittasimon@yourcompanydomain.extension . Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar caixa de verificação de **palavra-passe** e, em seguida, anotar o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso a FirmPlay - Advocacia de Empregados para Recrutamento.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações,** em seguida, selecione **FirmPlay - Advocacia dos Empregados para Recrutamento**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **FirmPlay - Advocacia dos Colaboradores para Recrutamento.**

    ![O FirmPlay - Associação de Colaboradores para Recrutamento na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-firmplay---employee-advocacy-for-recruiting-test-user"></a>Create FirmPlay - Advocacia dos Empregados para o Utilizador de Testes de Recrutamento

Nesta secção, cria-se um utilizador chamado Britta Simon in FirmPlay - Advocacia dos Colaboradores para Recrutamento. Trabalhar com [a FirmPlay - Advocacia dos Colaboradores para a equipa de apoio](mailto:engineering@firmplay.com) ao recrutamento para adicionar os utilizadores na plataforma FirmPlay - Advocacia dos Empregados para Recrutamento. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no FirmPlay - Advocacia dos Empregados para recrutar azulejos no Painel de Acesso, deverá ser automaticamente inscrito no FirmPlay - Advocacia dos Empregados para recrutamento para o qual criou sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)