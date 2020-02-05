---
title: 'Tutorial: Integração do Diretório Ativo Azure com o Tableau Online  Microsoft Docs'
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
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76985616"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tableau-online"></a>Tutorial: Azure Ative Diretório integração individual (SSO) com tableau online

Neste tutorial, você vai aprender a integrar tableau online com o Azure Ative Directory (Azure AD). Quando integrar o Tableau Online com o Azure AD, pode:

* Controlo em Azure AD que tem acesso ao Tableau Online.
* Ative que os seus utilizadores sejam automaticamente inscritos no Tableau Online com as suas contas Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Tableau Online de inscrição única (SSO) ativada por subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* Tableau Online suporta **SP** iniciado SSO
* Assim que configurar o Tableau Online, pode impor o controlo de Sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controle de sessão com Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-tableau-online-from-the-gallery"></a>Adicionar Tableau Online a partir da galeria

Para configurar a integração do Tableau Online em Azure AD, precisa adicionar tableau Online da galeria à sua lista de aplicações geridas do SaaS.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. No **Add da** secção galeria, digite **Tableau Online** na caixa de pesquisa.
1. Selecione **Tableau Online** a partir do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configura e testa o single sign-on azure com o Tableau Online com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Tableau Online.

Para configurar e testar o Azure AD SSO com o Tableau Online, complete os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configure tableau Online SSO](#configure-tableau-online-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    1. **[Create Tableau Online test user](#create-tableau-online-test-user)** - para ter uma contrapartida de B.Simon no Tableau Online que está ligada à representação do utilizador da AD Azure.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o único sign-on azure ad com o Tableau Online, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **Tableau Online,** selecione **Single sign-on**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-enalimentado** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , execute as seguintes etapas:

    ![Tableau Domínio Online e URLs informações únicas de inscrição](common/sp-identifier.png)

    a. No **Sign on URL** text box, digite o URL: `https://sso.online.tableau.com/public/sp/login?alias=<entityid>`

    b. Na caixa de texto **identificador (Id** da entidade), escreva o URL: `https://sso.online.tableau.com/public/sp/metadata?alias=<entityid>`

    > [!NOTE]
    > Você receberá o valor `<entityid>` da secção **Tableau Online configurar** neste tutorial. O valor de ID da entidade será o valor do **identificador Azure AD** na **secção Tableau Online.**

5. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o XML de **metadados de Federação** das opções determinadas de acordo com seu requisito e salvá-lo em seu computador.

    ![O link de download de certificado](common/metadataxml.png)

6. Na secção Configuração do **Tableau Online,** copie os URL(s) adequados de acordo com o seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Botão novo usuário](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. No campo **nome** , insira **brendafernandes**.
  
    b. No campo **nome de usuário** , digite **brendafernandes\@yourcompanydomain. Extension**  
    Por exemplo, brittaSimon\@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e anote o valor exibido na caixa senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, permite que britta Simon utilize um único sign-on Azure, concedendo acesso ao Tableau Online.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações,** em seguida, selecione **Tableau Online**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Tableau Online**.

    ![O link Tableau Online na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar usuário** e selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-tableau-online-sso"></a>Configure Tableau Online SSO

1. Numa janela de navegador diferente, inscreva-se na sua aplicação Tableau Online. Vá para **Definições** e, em seguida, **autenticação.**

    ![Configurar o início de sessão único](./media/tableauonline-tutorial/tutorial_tableauonline_09.png)

2. Para ativar a secção de tipos de **Autenticação** SAML. Verifique **Ativar um método de autenticação adicional** e, em seguida, verificar a caixa de verificação **SAML.**

    ![Configurar o início de sessão único](./media/tableauonline-tutorial/tutorial_tableauonline_12.png)

3. Desloque-se até ao ficheiro de metadados de importação na secção **Tableau Online.**  Clique em Navegar e importar o ficheiro de metadados, que descarregou a partir de Azure AD. Em seguida, clique **em Aplicar**.

   ![Configurar o início de sessão único](./media/tableauonline-tutorial/tutorial_tableauonline_13.png)

4. Na secção **de afirmações do Match,** insira o nome de afirmação correspondente do Fornecedor de Identidade para endereço de **e-mail,** **primeiro nome**e **apelido**. Para obter esta informação da Azure AD: 
  
    a. No portal Azure, vá na página de integração de aplicações **Tableau Online.**

    b. Na secção **Atributos e Reclamações** do Utilizador, clique no ícone de edição.

   ![Configurar o início de sessão único](./media/tableauonline-tutorial/attributesection.png)

    c. Copie o valor do espaço de nome para estes atributos: nome dado, e-mail e sobrenome utilizando os seguintes passos:

   ![Sinal único azure AD](./media/tableauonline-tutorial/tutorial_tableauonline_10.png)

    d. Clique no valor **do utilizador.dado**

    e. Copie o valor da caixa de texto **Namespace.**

    ![Configurar o início de sessão único](./media/tableauonline-tutorial/attributesection2.png)

    f. Para copiar os valores do espaço de nome para o e-mail e sobrenome, repita os passos acima.

    g. Mude para a aplicação Tableau Online e, em seguida, delineie a secção **Deatributos e Reclamações** do Utilizador da seguinte forma:

    * **E-mail: mail** ou **userprincipalname**

    * Primeiro nome: **nome próprio**

    * Sobrenome: **sobrenome**

    ![Configurar o início de sessão único](./media/tableauonline-tutorial/tutorial_tableauonline_14.png)

### <a name="create-tableau-online-test-user"></a>Criar o utilizador de teste online Tableau

Nesta secção, cria-se uma utilizadora chamada Britta Simon em Tableau Online.

1. No **Tableau Online,** clique em **Definições** e, em seguida, secção **de autenticação.** Percorra para baixo a secção **De Gestão de Utilizadores.** Clique em **Adicionar Utilizadores** e, em seguida, clique em **Introduzir endereços de e-mail**.
  
    ![Criar um utilizador de teste do Azure AD](./media/tableauonline-tutorial/tutorial_tableauonline_15.png)

2. Selecione **Adicionar utilizadores para autenticação (SAML).** No **endereço de e-mail Enter endereços** de texto adicionar britta.simon\@contoso.com
  
    ![Criar um utilizador de teste do Azure AD](./media/tableauonline-tutorial/tutorial_tableauonline_11.png)

3. Clique em **Adicionar Utilizadores**.

### <a name="test-sso"></a>Testar SSO

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clicar no azulejo Tableau Online no Painel de Acesso, deverá ser automaticamente inscrito no Tableau Online para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [O que é o controle de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)