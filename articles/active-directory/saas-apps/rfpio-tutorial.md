---
title: 'Tutorial: Integração do Azure Ative Directory com o RFPIO Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o RFPIO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.openlocfilehash: ef8a93ebdc5eb389608ef3340b9707bfd040a295
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92105724"
---
# <a name="tutorial-azure-active-directory-integration-with-rfpio"></a>Tutorial: Integração do Diretório Ativo Azure com o RFPIO

Neste tutorial, aprende-se a integrar o RFPIO com o Azure Ative Directory (Azure AD).
A integração do RFPIO com a AD Azure proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso ao RFPIO.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no RFPIO (Sign-on único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD da Azure com o RFPIO, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura única ativada por RFPIO

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* RFPIO apoia **SP e IDP** iniciado SSO

## <a name="adding-rfpio-from-the-gallery"></a>Adicionando RFPIO da galeria

Para configurar a integração do RFPIO no AD Azure, é necessário adicionar o RFPIO da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar RFPIO da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, tipo **RFPIO**, selecione **RFPIO** do painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![RFPIO na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com um único sign-on com RFPIO baseado num utilizador de teste chamado **Britta Simon**.
Para um único sinal de trabalho, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no RFPIO.

Para configurar e testar o Azure AD com rfpio, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Sign-On Único RFPIO](#configure-rfpio-single-sign-on)** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create RFPIO test user](#create-rfpio-test-user)** - ter uma contraparte de Britta Simon em RFPIO que está ligada à representação AD AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com RFPIO, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **RFPIO,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **De Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** execute o seguinte passo:

    ![A screenshot mostra a Configuração Básica SAML, onde pode introduzir um identificador.](common/idp-identifier.png)

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://www.rfpio.com`

    b. Clique **em Definir URLs adicionais**.

    c. Na caixa de texto **do Estado de Retransmissão** introduz um valor de cadeia. Contacte a [equipa de apoio da RFPIO](https://www.rfpio.com/contact/) para obter este valor.

    ![Screenshot mostra Definir U R Ls adicionais.](common/idp-preintegrated-relay.png)

5. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    ![image](common/both-preintegrated-signon.png)

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://www.app.rfpio.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real e URL de inscrição. Contacte [a equipa de suporte do cliente RFPIO](https://www.rfpio.com/contact/) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

6. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

7. Na secção **Configurar RFPIO,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

### <a name="configure-rfpio-single-sign-on"></a>Configurar Sign-On únicas rfpio

1. Numa janela diferente do navegador, inscreva-se no site do **RFPIO** como administrador.

1. Clique no canto inferior esquerdo para descer.

    ![A imagem mostra a seta para baixo na parte inferior do painel.](./media/rfpio-tutorial/app1.png)

1. Clique nas **Definições**de Organização . 

    ![A screenshot mostra configurações da organização selecionadas.](./media/rfpio-tutorial/app2.png)

1. Clique nas **funcionalidades & integração.**

    ![A screenshot mostra funcionalidades e integração selecionadas a partir de Definições.](./media/rfpio-tutorial/app4.png)

1. Na **configuração SSO SSO** **Edit.**

    ![A screenshot mostra a configuração SAML S S O com o botão Edito chamado.](./media/rfpio-tutorial/app3.png)

1. Nesta Secção executam as seguintes ações:

    ![O CScreenshot mostra a configuração SAML S S O com SAML ativada.](./media/rfpio-tutorial/app5.png)
    
    a. Copie o conteúdo do **Metdata Descarregado XML** e cole-o no campo **de configuração de identidade.**

    > [!NOTE]
    >Para copiar o conteúdo dos **metadados da Federação descarregadoS XML** Use **Notepad++** ou **o editor XML**adequado .

    b. Clique **em Validar**.

    c. Depois de clicar **em validar,** inverta **o SAML (Habilitado)** para ligar.

    d. Clique **em Submeter.**

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.

    ![Os links "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do utilizador](common/user-properties.png)

    a. No campo **Nome** entra **BrittaSimon**.
  
    b. No tipo de campo **do nome do utilizador** `brittasimon@yourcompanydomain.extension` . Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar caixa de verificação de **palavra-passe** e, em seguida, anotar o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso ao RFPIO.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações**e, em seguida, selecione **RFPIO**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **RFPIO**.

    ![O link RFPIO na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-rfpio-test-user"></a>Criar utilizador de teste RFPIO

1. Inscreva-se no site da empresa RFPIO como administrador.

1. Clique no canto inferior esquerdo para descer.

    ![A imagem mostra a seta para baixo na parte inferior do painel.](./media/rfpio-tutorial/app1.png)

1. Clique nas **Definições**de Organização . 

    ![A screenshot mostra configurações da organização selecionadas.](./media/rfpio-tutorial/app2.png)

1. Clique **nos membros da equipa.**

    ![A screenshot mostra membros da equipa selecionados a partir de Definições.](./media/rfpio-tutorial/app6.png)

1. Clique em **ADD MEMBERS**.

    ![A screenshot mostra o botão Adicionar Membros.](./media/rfpio-tutorial/app7.png)

1. Na secção **Adicionar Novos Membros.** Executar as seguintes ações:

    ![Screenshot mostra Adicionar Novos Membros onde pode introduzir os valores descritos.](./media/rfpio-tutorial/app8.png)

    a. Introduza o **endereço de e-mail** no campo **De entrada de um e-mail por linha.**

    b. Por favor, selecione **Role** de acordo com os seus requisitos.

    c. CLIQUE **EM ADICIONAR MEMBROS**.

    > [!NOTE]
    > O titular da conta Azure Ative Directory recebe um e-mail e segue um link para confirmar a sua conta antes de ficar ativa.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo RFPIO no Painel de Acesso, deverá ser automaticamente inscrito no RFPIO para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

