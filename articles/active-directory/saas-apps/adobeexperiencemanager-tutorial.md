---
title: 'Tutorial: Integração do Azure Ative Directory com o Adobe Experience Manager Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Adobe Experience Manager.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.openlocfilehash: 4607d4dfba68ca8f78ec4e3aaa4da36966ea843a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91760840"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-experience-manager"></a>Tutorial: Integração do Azure Ative Directory com o Adobe Experience Manager

Neste tutorial, aprende-se a integrar o Adobe Experience Manager com o Azure Ative Directory (Azure AD).
Integrar o Adobe Experience Manager com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso ao Adobe Experience Manager.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Adobe Experience Manager (Sign-On Único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD da Azure com o Adobe Experience Manager, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Adobe Experience Manager única subscrição ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Adobe Experience Manager suporta SSO iniciado **sp e IDP**

* Adobe Experience Manager suporta **o fornecimento de utilizadores just in time**

## <a name="adding-adobe-experience-manager-from-the-gallery"></a>Adicionar Adobe Experience Manager da galeria

Para configurar a integração do Adobe Experience Manager no Azure AD, é necessário adicionar o Adobe Experience Manager da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar o Adobe Experience Manager da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **digite Adobe Experience Manager**, selecione **Adobe Experience Manager** do painel de resultados e clique em Adicionar botão **adicionar** a aplicação.

     ![Adobe Experience Manager na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com [Nome de aplicação] com base num utilizador de teste chamado **Britta Simon**.
Para um único sinal de sação a funcionar, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em [Nome de aplicação].

Para configurar e testar o único sinal de Azure AD com [Nome da aplicação], é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Adobe Experience Manager Single Sign-On](#configure-adobe-experience-manager-single-sign-on)** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create Adobe Experience Manager test user](#create-adobe-experience-manager-test-user)** - para ter uma contraparte de Britta Simon no Adobe Experience Manager que está ligada à representação AD AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com [Nome da aplicação], execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Adobe Experience Manager,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **De Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** execute os seguintes passos:

    ![Screenshot que mostra a secção de configuração básica do SAML e destaca as caixas de texto de URL de identificação e resposta.](common/idp-intiated.png)

    a. Na caixa de texto **identifier,** digite um valor único que também define no seu servidor AEM.

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<AEM Server Url>/saml_login`

    > [!NOTE]
    > O valor URL de resposta não é real. Atualizar valor URL de resposta com o URL de resposta real. Para obter este valor, contacte a equipa de suporte do [Adobe Experience Manager Client](https://helpx.adobe.com/support/experience-manager.html) para obter este valor. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    ![Adobe Experience Manager Domain e URLs informações únicas de súmis](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de entrada de sinais,** digite o URL do seu servidor Adobe Experience Manager.

6. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

7. Na secção Configurar o **Adobe Experience Manager,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure Ad

    c. Logout URL

### <a name="configure-adobe-experience-manager-single-sign-on"></a>Configurar adobe Experience Manager Single Sign-On

1. Noutra janela do navegador, abra o portal de administração **Adobe Experience Manager.**

2. Selecione **Definições**  >  **Utilizadores de segurança**  >  **Users**.

    ![Screenshot que mostra o azulejo dos Utilizadores no Adobe Experience Manager.](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user.png)

3. Selecione **Administrador** ou qualquer outro utilizador relevante.

    ![Screenshot que destaca o utilizador Adminisrator.](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin6.png)

4. Selecione **definições de Conta**  >  **Gerir a Loja TrustStore**.

    ![Screenshot que mostra Manage TrustStore nas definições de Conta.](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_managetrust.png)

5. Sob **o Certificado de Adicionar a partir do ficheiro CER,** clique em Selecionar Ficheiro de **Certificado.** Navegue e selecione o ficheiro de certificado, que já descarregou a partir do portal Azure.

    ![Screenshot que realça o botão 'Select Certificate File'.](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user2.png)

6. O certificado é adicionado à TrustStore. Reparem no pseudónimo do certificado.

    ![Screenshot que mostra que o certificado é adicionado à TrustStore.](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin7.png)

7. Na página **Utilizadores,** selecione **o serviço de autenticação.**

    ![Sreenshot que destaca o serviço de autenticação no ecrã.](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin8.png)

8. Selecione **definições de conta**  >  **Criar/Gerir a Loja de Chaves**. Crie a KeyStore fornecendo uma senha.

    ![Screenshot que destaca Gerir a KeyStore.](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin9.png)

9. Volta para o ecrã de administração. Em seguida, selecione **Definições**  >  **Operações**  >  **Consola Web**.

    ![Screenshot que realça a Consola Web em Operações dentro da secção Definições.](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin1.png)

    Isto abre a página de configuração.

    ![Configure o botão de poupança de inscrição única](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin2.png)

10. Encontre **o Adobe Granite SAML 2.0 Authentication Handler**. Em seguida, selecione o ícone **Adicionar.**

    ![Screenshot que destaca o Adobe Granite SAML 2.0 Authentication Handler.](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin3.png)

11. Tome as seguintes ações nesta página.

    ![Configure botão de poupança de Sign-On único](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin4.png)

    a. Na caixa **Caminho,** insira. **/**

    b. Na caixa URL do **IDP,** introduza o valor **URL de login** que copiou a partir do portal Azure.

    c. Na caixa **IDP Certificate Alias, insira** o valor **de Alias certificado** que adicionou na TrustStore.

    d. Na caixa de **ID da Entidade Fornecida por Segurança,** insira o valor único **do Identificador Ad Azure** que configura no portal Azure.

    e. Na caixa **URL do Serviço de Apoio ao Consumidor de Afirmação,** introduza o valor URL de **resposta** que configurado no portal Azure.

    f. Na **caixa Password da Key Store,** insira a **palavra-passe** que definiu na KeyStore.

    exemplo, Na caixa **ID do Atributo utilizador,** insira o **ID do nome** ou outro ID do utilizador que seja relevante no seu caso.

    h. Selecione **Utilizadores de CRX autocreados**.

    i. Na caixa **URL logout,** insira o valor URL único **logout** que obteve a partir do portal Azure.

    j. Selecione **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.

    ![Os links "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do utilizador](common/user-properties.png)

    a. No campo **Nome** entra **BrittaSimon**.
  
    b. No tipo de campo **nome de utilizador** **brittasimon \@ yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar caixa de verificação de **palavra-passe** e, em seguida, anotar o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso ao Adobe Experience Manager.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações,** em seguida, selecione **Adobe Experience Manager**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Adobe Experience Manager**.

    ![O link Adobe Experience Manager na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-adobe-experience-manager-test-user"></a>Criar utilizador de teste adobe Experience Manager

Nesta secção, cria-se um utilizador chamado Britta Simon in Adobe Experience Manager. Se selecionou a opção **Utilizadores de CRX de Autocreate,** os utilizadores são criados automaticamente após a autenticação bem sucedida.

Se pretender criar utilizadores manualmente, trabalhe com a equipa de suporte do [Adobe Experience Manager](https://helpx.adobe.com/support/experience-manager.html)para adicionar os   utilizadores na plataforma Adobe Experience Manager.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo do Adobe Experience Manager no Painel de Acesso, deverá ser automaticamente inscrito no Adobe Experience Manager para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
