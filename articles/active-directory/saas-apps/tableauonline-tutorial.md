---
title: 'Tutorial: Integração do Diretório Ativo Azure com o Tableau Online [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Tableau Online.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1d4b1149-ba3b-4f4e-8bce-9791316b730d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.openlocfilehash: 187600edb599f5a5775e1b847ed1cb3a49f3b827
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "76985616"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tableau-online"></a>Tutorial: Azure Ative Diretório integração individual (SSO) com tableau online

Neste tutorial, você vai aprender a integrar tableau online com o Azure Ative Directory (Azure AD). Quando integrar o Tableau Online com o Azure AD, pode:

* Controlo em Azure AD que tem acesso ao Tableau Online.
* Ative que os seus utilizadores sejam automaticamente inscritos no Tableau Online com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Tableau Online de inscrição única (SSO) ativada por subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Tableau Online suporta **SP** iniciado SSO
* Assim que configurar o Tableau Online, pode impor o controlo de Sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controlo de sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-tableau-online-from-the-gallery"></a>Adicionar Tableau Online a partir da galeria

Para configurar a integração do Tableau Online em Azure AD, precisa adicionar tableau Online da galeria à sua lista de aplicações geridas do SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No **Add da** secção galeria, digite **Tableau Online** na caixa de pesquisa.
1. Selecione **Tableau Online** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o single sign-on azure com o Tableau Online com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Tableau Online.

Para configurar e testar o Azure AD SSO com o Tableau Online, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure tableau Online SSO](#configure-tableau-online-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    1. **[Create Tableau Online test user](#create-tableau-online-test-user)** - para ter uma contrapartida de B.Simon no Tableau Online que está ligada à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sign-on azure ad com o Tableau Online, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **Tableau Online,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    ![Tableau Domínio Online e URLs informações únicas de inscrição](common/sp-identifier.png)

    a. No **Sign on URL** text box, escreva o URL:`https://sso.online.tableau.com/public/sp/login?alias=<entityid>`

    b. Na caixa de texto **identificador (Id** da entidade), escreva o URL:`https://sso.online.tableau.com/public/sp/metadata?alias=<entityid>`

    > [!NOTE]
    > Você receberá `<entityid>` o valor da secção **Tableau Online configurar** neste tutorial. O valor de ID da entidade será o valor do **identificador Azure AD** na **secção Tableau Online.**

5. Na configuração de um único sign-on com a página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** para descarregar o **Federation Metadata XML** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

6. Na secção Configuração do **Tableau Online,** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure AD

    c. Logout URL

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
    Por exemplo, BrittaSimon\@contoso.com

    c. Selecione Mostrar a caixa de verificação de **palavra-passe** e, em seguida, anote o valor que está apresentado na caixa password.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que britta Simon utilize um único sign-on Azure, concedendo acesso ao Tableau Online.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações,** em seguida, selecione **Tableau Online**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Tableau Online**.

    ![O link Tableau Online na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

## <a name="configure-tableau-online-sso"></a>Configure Tableau Online SSO

1. Numa janela de navegador diferente, inscreva-se na sua aplicação Tableau Online. Vá para **Definições** e, em seguida, **autenticação.**

    ![Configurar um único sinal](./media/tableauonline-tutorial/tutorial_tableauonline_09.png)

2. Para ativar a secção de tipos de **Autenticação** SAML. Verifique **Ativar um método de autenticação adicional** e, em seguida, verificar a caixa de verificação **SAML.**

    ![Configurar um único sinal](./media/tableauonline-tutorial/tutorial_tableauonline_12.png)

3. Desloque-se até ao ficheiro de metadados de importação na secção **Tableau Online.**  Clique em Navegar e importar o ficheiro de metadados, que descarregou a partir de Azure AD. Em seguida, clique **em Aplicar**.

   ![Configurar um único sinal](./media/tableauonline-tutorial/tutorial_tableauonline_13.png)

4. Na secção **de afirmações do Match,** insira o nome de afirmação correspondente do Fornecedor de Identidade para endereço de **e-mail,** **primeiro nome**e **apelido**. Para obter esta informação da Azure AD: 
  
    a. No portal Azure, vá na página de integração de aplicações **Tableau Online.**

    b. Na secção **Atributos do Utilizador & Reclamações,** clique no ícone editar.

   ![Configurar um único sinal](./media/tableauonline-tutorial/attributesection.png)

    c. Copie o valor do espaço de nome para estes atributos: nome dado, e-mail e sobrenome utilizando os seguintes passos:

   ![Sinal único azure AD](./media/tableauonline-tutorial/tutorial_tableauonline_10.png)

    d. Clique no valor **do utilizador.dado**

    e. Copie o valor da caixa de texto **Namespace.**

    ![Configurar um único sinal](./media/tableauonline-tutorial/attributesection2.png)

    f. Para copiar os valores do espaço de nome para o e-mail e sobrenome, repita os passos acima.

    g. Mude para a aplicação Tableau Online e, em seguida, delineie a secção **de Atributos de Utilizador & Reivindicações** da seguinte forma:

    * **E-mail: mail** ou **userprincipalname**

    * Primeiro nome: **nome próprio**

    * Sobrenome: **sobrenome**

    ![Configurar um único sinal](./media/tableauonline-tutorial/tutorial_tableauonline_14.png)

### <a name="create-tableau-online-test-user"></a>Criar o utilizador de teste online Tableau

Nesta secção, cria-se uma utilizadora chamada Britta Simon em Tableau Online.

1. No **Tableau Online,** clique em **Definições** e, em seguida, secção **de autenticação.** Percorra para baixo a secção **De Gestão de Utilizadores.** Clique em **Adicionar Utilizadores** e, em seguida, clique em **Introduzir endereços de e-mail**.
  
    ![Criação de um utilizador de teste azure AD](./media/tableauonline-tutorial/tutorial_tableauonline_15.png)

2. Selecione **Adicionar utilizadores para autenticação (SAML).** No **endereço de e-mail Enter endereços** de texto adicionar britta.simon\@contoso.com
  
    ![Criação de um utilizador de teste azure AD](./media/tableauonline-tutorial/tutorial_tableauonline_11.png)

3. Clique em **Adicionar Utilizadores**.

### <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Tableau Online no Painel de Acesso, deverá ser automaticamente inscrito no Tableau Online para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)