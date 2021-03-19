---
title: 'Tutorial: Integração do Diretório Ativo Azure com a Wdesk | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Wdesk.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/08/2021
ms.author: jeedes
ms.openlocfilehash: d85d7ef37536b54ecfc1b65d19eafd1d499ca050
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104603275"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-wdesk"></a>Tutorial: Azure Ative Directory integração única (SSO) com wdesk

Neste tutorial, você vai aprender a integrar Wdesk com Azure Ative Directory (Azure AD). Quando integrar o Wdesk com a AD Azure, pode:

* Controlo em Azure AD que tem acesso a Wdesk.
* Permita que os seus utilizadores sejam automaticamente inscritos no Wdesk com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Wdesk assinatura única ativada (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Wdesk apoia **SP** e **IDP** iniciado SSO.

## <a name="add-wdesk-from-the-gallery"></a>Adicione Wdesk da galeria

Para configurar a integração do Wdesk no AD AZure, precisa adicionar O Wdesk da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar da secção **da galeria,** digite **Wdesk** na caixa de pesquisa.
1. Selecione **Wdesk** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-wdesk"></a>Configurar e testar Azure AD SSO para wdesk

Nesta secção, você configura e testa Azure AD single sign-on com Wdesk com base em um utilizador de teste chamado **Britta Simon**.
Para um único sinal de trabalho, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Wdesk.

Para configurar e testar a Azure AD SSO com a Wdesk, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o Wdesk SSO](#configure-wdesk-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Wdesk test user](#create-wdesk-test-user)** - para ter uma contraparte de B.Simon em Wdesk que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Wdesk,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **De Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** execute os seguintes passos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://<subdomain>.wdesk.com/auth/saml/sp/metadata/<instancename>`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<subdomain>.wdesk.com/auth/saml/sp/consumer/<instancename>`

5. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<subdomain>.wdesk.com/auth/login/saml/<instancename>`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL Sign-On. Obtém-se estes valores do portal WDesk quando configura o SSO.

4. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

6. Na secção **Configuração Wdesk,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    
### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD 

Nesta secção, irá criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao Wdesk.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Wdesk**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-wdesk-sso"></a>Configurar Wdesk SSO

1. Numa janela diferente do navegador web, inscreva-se no Wdesk como Administrador de Segurança.

1. No canto inferior esquerdo, clique em **Administração** e escolha **O Administrador de Conta:**
 
    ![O Screenshot mostra o Administrador de Conta selecionado do menu Admin.](./media/wdesk-tutorial/account.png)

1. No Wdesk Admin, navegue para **a Segurança,** em seguida,   >  **SAML SAML Definições**:

    ![A screenshot mostra configurações SAML selecionadas a partir do separador SAML.](./media/wdesk-tutorial/settings.png)

1. Nas **Definições de ID do utilizador SAML,** verifique **se o ID do utilizador SAML é o nome de utilizador Wdesk**.

    ![A screenshot mostra configurações do utilizador SAML I D onde pode selecionar o nome de utilizador do utilizador SAML I D é nome de utilizador de secretária W.](./media/wdesk-tutorial/wdesk-username.png)

4. Em **Definições Gerais**, verifique o **sinal único de Ativação SAML ligado**:

    ![A screenshot mostra configurações de EDITAR SAML onde pode selecionar Ativar O Sinal Único DE SAML.](./media/wdesk-tutorial/user-settings.png)

5. Em **Detalhes do Prestador de Serviços,** execute os seguintes passos:

    ![A screenshot mostra detalhes do fornecedor de serviços onde pode introduzir os valores descritos.](./media/wdesk-tutorial/service-provider.png)

    1. Copie o **URL de login** e **cole-o** na caixa de texto url de início de sessão no portal Azure.

    1. Copie o **Url de Metadados** e cole-o na caixa de texto **identifier** no portal Azure.

    1. Copie o **url do Consumidor** e cole-o na caixa de texto do Url de **resposta** no portal Azure.

    1. Clique em **Guardar** no portal Azure para guardar as alterações.      

1. Clique **em Configurar Configurações IdP** para abrir o diálogo **de Definições de Edição.** Clique **em Escolher Ficheiro** para localizar o ficheiro **Metadata.xml** guardado no portal Azure e, em seguida, carregá-lo.
    
    ![A screenshot mostra Configurações Editar I d P onde pode carregar metadados.](./media/wdesk-tutorial/metadata.png)
  
1. Clique **em Guardar as alterações**.

    ![A screenshot mostra o botão Guardar alterações.](./media/wdesk-tutorial/save.png)

### <a name="create-wdesk-test-user"></a>Criar utilizador de teste Wdesk

Para permitir que os utilizadores da Azure AD inscrevam-se no Wdesk, devem ser a provisionados na Wdesk. Em Wdesk, o provisionamento é uma tarefa manual.

**Para obter uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se na Wdesk como administrador de segurança.

2. Navegue para **administração**  >  **de conta de administração.**

     ![O Screenshot mostra o Administrador de Conta selecionado do menu Admin.](./media/wdesk-tutorial/account.png)

3. Clique em **Membros** em **Pessoas**.

4. Clique em **Adicionar Membro** para abrir a caixa de diálogo **do membro.** 
   
    ![A screenshot mostra o separador 'Membros' onde pode selecionar Add Member.](./media/wdesk-tutorial/create-user-1.png)  

5. Na caixa de texto **do utilizador,** introduza o nome de utilizador do utilizador b.simon@contoso.com e clique no botão **Continuar.**

    ![A screenshot mostra a caixa de diálogo Add Member onde pode introduzir um utilizador.](./media/wdesk-tutorial/create-user-3.png)

6.  Introduza os detalhes como mostrado abaixo:
  
    ![A screenshot mostra a caixa de diálogo Add Member onde pode adicionar Informações Básicas a um utilizador.](./media/wdesk-tutorial/create-user-4.png)
 
    a. Na caixa de texto **por e-mail,** insira o e-mail do utilizador como b.simon@contoso.com .

    b. Na caixa de texto **First Name,** insira o primeiro nome do utilizador como **B**.

    c. Na caixa de texto **Last Name,** insira o último nome do utilizador como **Simon**.

7. Clique no botão **"Guardar membro".**  

    ![A screenshot mostra o e-mail de boas-vindas Enviar e-mail de boas-vindas com o botão Save Member.](./media/wdesk-tutorial/create-user-5.png)

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para Wdesk Sign no URL onde pode iniciar o fluxo de login.  

* Vá diretamente ao URL de inscrição de Wdesk e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito no Wdesk para o qual configura o SSO. 

Também pode utilizar o Microsoft My Apps para testar a aplicação em qualquer modo. Quando clicar no azulejo Wdesk nas Minhas Apps, se configurado no modo SP, será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito no Wdesk para o qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado Wdesk, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
