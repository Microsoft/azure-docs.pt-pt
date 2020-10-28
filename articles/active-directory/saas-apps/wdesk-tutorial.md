---
title: 'Tutorial: Integração do Azure Ative Directory com a Wdesk Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Wdesk.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/02/2020
ms.author: jeedes
ms.openlocfilehash: 7eac2ed58608ac5814e1f907b863a2977df830d4
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92636719"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-wdesk"></a>Tutorial: Azure Ative Directory integração única (SSO) com wdesk

Neste tutorial, você vai aprender a integrar Wdesk com Azure Ative Directory (Azure AD). Quando integrar o Wdesk com a AD Azure, pode:

* Controlo em Azure AD que tem acesso a Wdesk.
* Permita que os seus utilizadores sejam automaticamente inscritos no Wdesk com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Wdesk assinatura única ativada (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Wdesk apoia **SP** e **IDP** iniciado SSO
* Assim que configurar o Wdesk, pode impor o controlo de Sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controlo da sessão com a Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)

## <a name="adding-wdesk-from-the-gallery"></a>Adicionando Wdesk da galeria

Para configurar a integração do Wdesk no AD AZure, precisa adicionar O Wdesk da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações** .
1. Para adicionar nova aplicação, selecione **Nova aplicação** .
1. Na secção Adicionar da secção **da galeria,** digite **Wdesk** na caixa de pesquisa.
1. Selecione **Wdesk** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, você configura e testa Azure AD single sign-on com Wdesk com base em um utilizador de teste chamado **Britta Simon** .
Para um único sinal de trabalho, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Wdesk.

Para configurar e testar o Azure AD SSO com a Wdesk, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o Wdesk SSO](#configure-wdesk-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Wdesk test user](#create-wdesk-test-user)** - para ter uma contraparte de B.Simon em Wdesk que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com wdesk, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **Wdesk,** selecione **Single sign-on** .

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **De Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** execute os seguintes passos:

    ![A screenshot mostra a Configuração BÁSICA SAML, onde pode introduzir o Identificador, Responder U R L e selecionar Guardar.](common/idp-intiated.png)

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://<subdomain>.wdesk.com/auth/saml/sp/metadata/<instancename>`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<subdomain>.wdesk.com/auth/saml/sp/consumer/<instancename>`

5. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    ![Screenshot mostra Definir U R Ls adicionais onde pode introduzir um sinal em U R L.](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<subdomain>.wdesk.com/auth/login/saml/<instancename>`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL Sign-On. Obtém-se estes valores do portal WDesk quando configura o SSO.

4. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

6. Na secção **Configuração Wdesk,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD 

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory** , selecione **Utilizadores** , e, em seguida, selecione **Todos os utilizadores** .

    ![Os links "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do utilizador](common/user-properties.png)

    a. No campo **Nome** entra **BrittaSimon** .
  
    b. No tipo de campo **do nome do utilizador** brittasimon@yourcompanydomain.extension . Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar caixa de verificação de **palavra-passe** e, em seguida, anotar o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar** .

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso ao Wdesk.

1. No portal Azure, selecione **Aplicações empresariais** , selecione **Todas as aplicações** e, em seguida, selecione **Wdesk** .

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Wdesk** .

    ![O link Wdesk na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos** .

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

## <a name="configure-wdesk-sso"></a>Configurar Wdesk SSO

1. Numa janela diferente do navegador web, inscreva-se no Wdesk como Administrador de Segurança.

2. No canto inferior esquerdo, clique em **Administração** e escolha **O Administrador de Conta:**
 
     ![O Screenshot mostra o Administrador de Conta selecionado do menu Admin.](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

3. No Wdesk Admin, navegue para **a Segurança,** em seguida, **SAML**  >  **SAML SAML Definições** :

    ![A screenshot mostra configurações SAML selecionadas a partir do separador SAML.](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig2.png)

1. Nas **Definições de ID do utilizador SAML,** verifique **se o ID do utilizador SAML é o nome de utilizador Wdesk** .

    ![A screenshot mostra configurações do utilizador SAML I D onde pode selecionar o nome de utilizador do utilizador SAML I D é nome de utilizador de secretária W.](./media/wdesk-tutorial/wdesk-username.png)

4. Em **Definições Gerais** , verifique o **sinal único de Ativação SAML ligado** :

    ![A screenshot mostra configurações de EDITAR SAML onde pode selecionar Ativar O Sinal Único DE SAML.](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig3.png)

5. Em **Detalhes do Prestador de Serviços,** execute os seguintes passos:

    ![A screenshot mostra detalhes do fornecedor de serviços onde pode introduzir os valores descritos.](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig4.png)

      a. Copie o **URL de login** e **cole-o** na caixa de texto url de início de sessão no portal Azure.
   
      b. Copie o **Url de Metadados** e cole-o na caixa de texto **identifier** no portal Azure.
       
      c. Copie o **url do Consumidor** e cole-o na caixa de texto do Url de **resposta** no portal Azure.
   
      d. Clique em **Guardar** no portal Azure para guardar as alterações.      

6. Clique **em Configurar Configurações IdP** para abrir o diálogo **de Definições de Edição.** Clique **em Escolher Ficheiro** para localizar o ficheiro **Metadata.xml** guardado no portal Azure e, em seguida, carregá-lo.
    
    ![A screenshot mostra Configurações Editar I d P onde pode carregar metadados.](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig5.png)
  
7. Clique **em Guardar as alterações** .

    ![A screenshot mostra o botão Guardar alterações.](./media/wdesk-tutorial/tutorial_wdesk_ssoconfigsavebutton.png)

### <a name="create-wdesk-test-user"></a>Criar utilizador de teste Wdesk

Para permitir que os utilizadores da Azure AD inscrevam-se no Wdesk, devem ser a provisionados na Wdesk. Em Wdesk, o provisionamento é uma tarefa manual.

**Para obter uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se na Wdesk como administrador de segurança.

2. Navegue para **administração**  >  **de conta de administração.**

     ![O Screenshot mostra o Administrador de Conta selecionado do menu Admin.](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

3. Clique em **Membros** em **Pessoas** .

4. Clique em **Adicionar Membro** para abrir a caixa de diálogo **do membro.** 
   
    ![A screenshot mostra o separador 'Membros' onde pode selecionar Add Member.](./media/wdesk-tutorial/createuser1.png)  

5. Na caixa de texto **do utilizador,** introduza o nome de utilizador do utilizador b.simon@contoso.com e clique no botão **Continuar.**

    ![A screenshot mostra a caixa de diálogo Add Member onde pode introduzir um utilizador.](./media/wdesk-tutorial/createuser3.png)

6.  Introduza os detalhes como mostrado abaixo:
  
    ![A screenshot mostra a caixa de diálogo Add Member onde pode adicionar Informações Básicas a um utilizador.](./media/wdesk-tutorial/createuser4.png)
 
    a. Na caixa de texto **por e-mail,** insira o e-mail do utilizador como b.simon@contoso.com .

    b. Na caixa de texto **First Name,** insira o primeiro nome do utilizador como **B** .

    c. Na caixa de texto **Last Name,** insira o último nome do utilizador como **Simon** .

7. Clique no botão **"Guardar membro".**  

    ![A screenshot mostra o e-mail de boas-vindas Enviar e-mail de boas-vindas com o botão Save Member.](./media/wdesk-tutorial/createuser5.png)

### <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Wdesk no Painel de Acesso, deverá ser automaticamente inscrito no Wdesk para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)