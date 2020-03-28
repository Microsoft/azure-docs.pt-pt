---
title: 'Tutorial: Integração do Diretório Ativo Azure com a Wdesk Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Wdesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 06900a91-a326-4663-8ba6-69ae741a536e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.openlocfilehash: a4cfcf20fc7a6a3532f65c3e797da6c876844d2c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "76985569"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-wdesk"></a>Tutorial: Azure Ative Directory integração de um único sign-on (SSO) com a Wdesk

Neste tutorial, você vai aprender a integrar a Wdesk com o Azure Ative Directory (Azure AD). Quando integrar a Wdesk com a Azure AD, pode:

* Controlo em Azure AD que tem acesso a Wdesk.
* Ative que os seus utilizadores sejam automaticamente inscritos na Wdesk com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Wdesk single sign-on (SSO) enabled subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Wdesk suporta **SP** e **IDP** iniciadoS SSO
* Assim que configurar o Wdesk, pode impor o controlo da Sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controlo de sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-wdesk-from-the-gallery"></a>Adicionando Wdesk da galeria

Para configurar a integração da Wdesk em Azure AD, precisa adicionar Wdesk da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** digite **Wdesk** na caixa de pesquisa.
1. Selecione **Wdesk** do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o único sign-on azure ad com wdesk com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Wdesk.

Para configurar e testar o Azure AD SSO com wdesk, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure wdesk SSO](#configure-wdesk-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    1. **[Crie o utilizador](#create-wdesk-test-user)** de teste Wdesk - para ter uma contraparte de B.Simon na Wdesk que esteja ligada à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sign-on azure com o Wdesk, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **wdesk,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** se pretender configurar a aplicação no modo iniciado **idp,** execute os seguintes passos:

    ![Wdesk Domain e URLs informações únicas de inscrição](common/idp-intiated.png)

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:`https://<subdomain>.wdesk.com/auth/saml/sp/metadata/<instancename>`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://<subdomain>.wdesk.com/auth/saml/sp/consumer/<instancename>`

5. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    ![Wdesk Domain e URLs informações únicas de inscrição](common/metadata-upload-additional-signon.png)

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://<subdomain>.wdesk.com/auth/login/saml/<instancename>`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação, resposta e URL de sinal. Obtém estes valores no portal WDesk quando configura o SSO.

4. Na configuração de um único sign-on com a página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** para descarregar o **Federation Metadata XML** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

6. Na secção **'Set-up Wdesk',** copie os URL(s) adequados de acordo com o seu requisito.

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
  
    b. No **User name** tipo brittasimon@yourcompanydomain.extensionde campo do nome do utilizador . Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar a caixa de verificação de **palavra-passe** e, em seguida, anote o valor que está apresentado na caixa password.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que Britta Simon utilize um único sign-on Azure, concedendo acesso à Wdesk.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações**e, em seguida, selecione **Wdesk**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Wdesk**.

    ![O link Wdesk na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

## <a name="configure-wdesk-sso"></a>Configure Wdesk SSO

1. Numa janela de navegador web diferente, inscreva-se no Wdesk como administrador de segurança.

2. No canto inferior esquerdo, clique **em Administrador** e escolha O Administrador da **Conta:**
 
     ![Configurar um único sinal](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

3. No Wdesk Admin, navegue para **a Segurança,** em seguida, **definições SAML** > **SAML:**

    ![Configurar um único sinal](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig2.png)

4. Em **definições gerais,** verifique o **sinal único Ativado SAML ligado:**

    ![Configurar um único sinal](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig3.png)

5. Em detalhes do prestador de **serviços,** execute os seguintes passos:

    ![Configurar um único sinal](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig4.png)

      a. Copie o URL de **Login** e cole-o na caixa de texto **Sin-on Url** no portal Azure.
   
      b. Copie o **Url de Metadados** e cole-o na caixa de texto **identifier** no portal Azure.
       
      c. Copie a **url do Consumidor** e cole-a na caixa de texto Resposta **Url** no portal Azure.
   
      d. Clique em **Guardar** no portal Azure para guardar as alterações.      

6. Clique em **configurar definições idp** para abrir o diálogo de **definições de idp de edição.** Clique **em Escolher Ficheiro** para localizar o ficheiro **Metadata.xml** que guardou do portal Azure e, em seguida, faça o upload.
    
    ![Configurar um único sinal](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig5.png)
  
7. Clique em **Guardar alterações**.

    ![Configurar um único sinal](./media/wdesk-tutorial/tutorial_wdesk_ssoconfigsavebutton.png)

### <a name="create-wdesk-test-user"></a>Criar o utilizador de teste Wdesk

Para permitir que os utilizadores da AD Azure assinem a Wdesk, devem ser aprovisionados na Wdesk. Em Wdesk, o provisionamento é uma tarefa manual.

**Para fornecer uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se no Wdesk como administrador de segurança.

2. Navegue para **administrador** > **de conta.**

     ![Configurar um único sinal](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

3. Clique em **Membros** em **Pessoas**.

4. Agora clique em **Adicionar Membro** para abrir caixa de diálogo do **membro adicionar.** 
   
    ![Criação de um utilizador de teste azure AD](./media/wdesk-tutorial/createuser1.png)  

5. Na caixa de texto **do Utilizador,** introduza o nome de utilizador do utilizador como brittasimon@contoso.com e clique no botão **Continuar.**

    ![Criação de um utilizador de teste azure AD](./media/wdesk-tutorial/createuser3.png)

6.  Insira os detalhes como mostrado abaixo:
  
    ![Criação de um utilizador de teste azure AD](./media/wdesk-tutorial/createuser4.png)
 
    a. Na caixa de texto **por e-mail,** introduza o e-mail do utilizador como brittasimon@contoso.com.

    b. Na caixa de texto **First Name,** introduza o primeiro nome de utilizador como **Britta**.

    c. Na caixa de texto **De Apelido,** introduza o último nome de utilizador como **Simon**.

7. Clique no botão **Salvar membro.**  

    ![Criação de um utilizador de teste azure AD](./media/wdesk-tutorial/createuser5.png)

### <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Wdesk no Painel de Acesso, deve ser automaticamente inscrito no Wdesk para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)