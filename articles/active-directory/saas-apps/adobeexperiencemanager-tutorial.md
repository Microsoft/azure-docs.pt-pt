---
title: 'Tutorial: Integração de Diretório Sonâmbulo Azure com a Adobe Experience Manager [ Gestor de Experiências Adobe ] Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Adobe Experience Manager.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 88a95bb5-c17c-474f-bb92-1f80f5344b5a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f39751f40b32c5da24e13d75d2607d7da0a57ad3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "73154108"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-experience-manager"></a>Tutorial: Integração de Diretório Ativo Azure com Adobe Experience Manager

Neste tutorial, aprende-se a integrar o Adobe Experience Manager com o Azure Ative Directory (Azure AD).
Integrar o Adobe Experience Manager com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a Adobe Experience Manager.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Adobe Experience Manager (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o Adobe Experience Manager, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura de inscrição única do Adobe Experience Manager

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Adobe Experience Manager apoia **SP e IDP** iniciado SSO

* Adobe Experience Manager suporta o fornecimento de utilizadores **justo no tempo**

## <a name="adding-adobe-experience-manager-from-the-gallery"></a>Adicionar Adobe Experience Manager da galeria

Para configurar a integração do Adobe Experience Manager no Azure AD, precisa de adicionar o Adobe Experience Manager da galeria à sua lista de aplicações geridas do SaaS.

**Para adicionar a Adobe Experience Manager da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, tipo **Adobe Experience Manager,** selecione **Adobe Experience Manager** do painel de resultados e, em seguida, clique em **Adicionar** botão para adicionar a aplicação.

     ![Gestor de experiências da Adobe na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa um único signo da Azure AD com [nome de aplicação] com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em [Nome de aplicação].

Para configurar e testar o único sinal de Azure AD com [nome de aplicação], é necessário completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Single Sign-On do Adobe Experience Manager](#configure-adobe-experience-manager-single-sign-on)** - para configurar as definições de início de sessão simples no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Crie o utilizador de teste Adobe Experience Manager](#create-adobe-experience-manager-test-user)** - para ter uma contrapartida da Britta Simon no Adobe Experience Manager que está ligada à representação do utilizador da AD Azure.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único signo da Azure AD com [nome da aplicação], execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações do **Adobe Experience Manager,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** Se desejar configurar a aplicação no modo iniciado **idp,** execute os seguintes passos:

    ![Adobe Experience Manager Domain e URLs informações únicas de inscrição](common/idp-intiated.png)

    a. Na caixa de texto **Identifier,** escreva um valor único que também define no seu servidor AEM.

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://<AEM Server Url>/saml_login`

    > [!NOTE]
    > O valor url de resposta não é real. Atualizar valor URL resposta com o URL de resposta real. Para obter este valor, contacte a equipa de suporte ao [Cliente do Adobe Experience Manager](https://helpx.adobe.com/support/experience-manager.html) para obter este valor. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

5. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    ![Adobe Experience Manager Domain e URLs informações únicas de inscrição](common/metadata-upload-additional-signon.png)

    Na caixa de texto **de URL sign-on,** escreva o URL do servidor do Adobe Experience Manager.

6. Na configuração de um único sinal com página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

7. Na secção **Configurar o Adobe Experience Manager,** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de anúncio sinuoso

    c. Logout URL

### <a name="configure-adobe-experience-manager-single-sign-on"></a>Configure Adobe Experience Manager Single Sign-On

1. Noutra janela do navegador, abra o portal de administração do **Adobe Experience Manager.**

2. Selecione **Definições** > **Utilizadores de****Segurança** > .

    ![Configure o botão de salvamento de sinal único](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user.png)

3. Selecione **Administrador** ou qualquer outro utilizador relevante.

    ![Configurar o botão de salvamento de sinal único](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin6.png)

4. Selecione **definições de** > conta**Gerir TrustStore**.

    ![Configurar o botão de salvamento de sinal único](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_managetrust.png)

5. Em **'Adicionar Certificado' do ficheiro CER,** clique em **Selecione Ficheiro de Certificado**. Navegue e selecione o ficheiro de certificado, que já descarregou no portal Azure.

    ![Configure um único botão de salvamento de sinal](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user2.png)

6. O certificado é adicionado à TrustStore. Reparem no pseudónimo do certificado.

    ![Configurar o botão de salvamento de sinal único](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin7.png)

7. Na página **Utilizadores,** selecione o **serviço de autenticação**.

    ![Configurar o botão de salvamento de sinal único](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin8.png)

8. Selecione **definições** > de conta**Criar/Gerir a KeyStore**. Crie keyStore fornecendo uma senha.

    ![Configurar o botão de salvamento de sinal único](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin9.png)

9. Volta para o ecrã da administração. Em seguida, selecione **Definições** > **De Operações** > **Consola**Web .

    ![Configurar o botão de salvamento de sinal único](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin1.png)

    Isto abre a página de configuração.

    ![Configure o botão de salvamento de sinal único](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin2.png)

10. Encontre a **Adobe Granite SAML 2.0 Manipulador de Autenticação**. Em seguida, selecione o ícone **Adicionar.**

    ![Configurar o botão de salvamento de sinal único](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin3.png)

11. Tome as seguintes ações nesta página.

    ![Configurar o botão de salvamento de sinal único](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin4.png)

    a. Na caixa **Caminho,** introduza **/**.

    b. Na caixa **DEURL IDP,** introduza o valor URL de **Login** que copiou do portal Azure.

    c. Na caixa **idp Certificate Alias,** insira o valor do **Certificado Alias** que adicionou na TrustStore.

    d. Na caixa **de ID da Entidade Fornecida de Segurança,** introduza o valor exclusivo do Identificador de **Anúncios Azure** que configurado no portal Azure.

    e. Na caixa URL do **Serviço de Consumidores de Afirmação,** introduza o valor URL de **resposta** configurado no portal Azure.

    f. Na caixa **password da Key Store,** introduza a **palavra-passe** que definiu na KeyStore.

    g. Na caixa de identificação do **Utilizador Atributo,** introduza o ID de **nome** ou outro ID do utilizador relevante no seu caso.

    h. Selecione **Utilizadores CRX autocriar automaticamente**.

    i. Na caixa **de URL de Logout,** introduza o valor URL de **Logout** único que obteve do portal Azure.

    j. Selecione **Guardar**.

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

Nesta secção, permite que britta Simon utilize um único sign-on Azure, concedendo acesso ao Adobe Experience Manager.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações,** em seguida, selecione **Adobe Experience Manager**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Adobe Experience Manager**.

    ![O link Adobe Experience Manager na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-adobe-experience-manager-test-user"></a>Criar o utilizador de teste adobe Experience Manager

Nesta secção, cria-se uma utilizadora chamada Britta Simon no Adobe Experience Manager. Se selecionou a opção **Utilizadores CRX Autocreate,** os utilizadores são criados automaticamente após a autenticação bem sucedida.

Se pretender criar utilizadores manualmente, trabalhe com a equipa de suporte do [Adobe Experience Manager](https://helpx.adobe.com/support/experience-manager.html)para adicionar os utilizadores na plataforma Adobe Experience Manager.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Dobe Experience Manager no Painel de Acesso, deve ser automaticamente inscrito no Adobe Experience Manager para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
