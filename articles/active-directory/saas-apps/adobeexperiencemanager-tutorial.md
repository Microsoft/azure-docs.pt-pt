---
title: 'Tutorial: Integração do Azure Ative Directory com o Adobe Experience Manager | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Adobe Experience Manager.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/17/2021
ms.author: jeedes
ms.openlocfilehash: 1a0340b9d8971446113862465c67143674bd5668
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101653359"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-experience-manager"></a>Tutorial: Integração do Azure Ative Directory com o Adobe Experience Manager

Neste tutorial, você vai aprender a integrar o Adobe Experience Manager com o Azure Ative Directory (Azure AD). Quando integrar o Adobe Experience Manager com a Ad Azure, pode:

* Controle em Azure AD que tem acesso ao Adobe Experience Manager.
* Ative os seus utilizadores a serem automaticamente inscritos no Adobe Experience Manager com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Subscrição ativada pelo Adobe Experience Manager (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Adobe Experience Manager suporta SSO iniciado **sp e IDP**

* Adobe Experience Manager suporta **o fornecimento de utilizadores just in time**

## <a name="adding-adobe-experience-manager-from-the-gallery"></a>Adicionar Adobe Experience Manager da galeria

Para configurar a integração do Adobe Experience Manager no Azure AD, é necessário adicionar o Adobe Experience Manager da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **digite Adobe Experience Manager** na caixa de pesquisa.
1. Selecione **Adobe Experience Manager** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-adobe-experience-manager"></a>Configurar e testar Azure AD SSO para Adobe Experience Manager

Configure e teste Azure AD SSO com Adobe Experience Manager usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no Adobe Experience Manager.

Para configurar e testar o Azure AD SSO com o Adobe Experience Manager, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
2. **[Configure o Adobe Experience Manager SSO](#configure-adobe-experience-manager-sso)** - para configurar as definições de Sign-On única no lado da aplicação.
    1. **[Create Adobe Experience Manager test user](#create-adobe-experience-manager-test-user)** - para ter uma contraparte de Britta Simon no Adobe Experience Manager que está ligada à representação AD AD do utilizador.
6. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Adobe Experience Manager,** encontre a secção **Gerir** e selecione um único sinal **de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    a. Na caixa de texto **identifier,** digite um valor único que também define no seu servidor AEM.

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<AEM Server Url>/saml_login`

    > [!NOTE]
    > O valor URL de resposta não é real. Atualizar valor URL de resposta com o URL de resposta real. Para obter este valor, contacte a equipa de suporte do [Adobe Experience Manager Client](https://helpx.adobe.com/support/experience-manager.html) para obter este valor. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de sinais,** digite o URL do seu servidor Adobe Experience Manager.

6. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

7. Na secção Configurar o **Adobe Experience Manager,** copie os URL(s) apropriados de acordo com o seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao Adobe Experience Manager.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Adobe Experience Manager**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-adobe-experience-manager-sso"></a>Configurar Adobe Experience Manager SSO

1. Noutra janela do navegador, abra o portal de administração **Adobe Experience Manager.**

2. Selecione **Definições**  >  **Utilizadores de segurança**  >  .

    ![Screenshot que mostra o azulejo dos Utilizadores no Adobe Experience Manager.](./media/adobe-experience-manager-tutorial/user-1.png)

3. Selecione **Administrador** ou qualquer outro utilizador relevante.

    ![Screenshot que destaca o utilizador Adminisrator.](./media/adobe-experience-manager-tutorial/tutorial-admin-6.png)

4. Selecione **definições de Conta**  >  **Gerir a Loja TrustStore**.

    ![Screenshot que mostra Manage TrustStore nas definições de Conta.](./media/adobe-experience-manager-tutorial/manage-trust.png)

5. Sob **o Certificado de Adicionar a partir do ficheiro CER,** clique em Selecionar Ficheiro de **Certificado.** Navegue e selecione o ficheiro de certificado, que já descarregou a partir do portal Azure.

    ![Screenshot que realça o botão 'Select Certificate File'.](./media/adobe-experience-manager-tutorial/user-2.png)

6. O certificado é adicionado à TrustStore. Reparem no pseudónimo do certificado.

    ![Screenshot que mostra que o certificado é adicionado à TrustStore.](./media/adobe-experience-manager-tutorial/tutorial-admin-7.png)

7. Na página **Utilizadores,** selecione **o serviço de autenticação.**

    ![Sreenshot que destaca o serviço de autenticação no ecrã.](./media/adobe-experience-manager-tutorial/tutorial-admin-8.png)

8. Selecione **definições de conta**  >  **Criar/Gerir a Loja de Chaves**. Crie a KeyStore fornecendo uma senha.

    ![Screenshot que destaca Gerir a KeyStore.](./media/adobe-experience-manager-tutorial/tutorial-admin-9.png)

9. Volta para o ecrã de administração. Em seguida, selecione **Definições**  >  **Operações**  >  **Consola Web**.

    ![Screenshot que realça a Consola Web em Operações dentro da secção Definições.](./media/adobe-experience-manager-tutorial/tutorial-admin-1.png)

    Isto abre a página de configuração.

    ![Configure o botão de poupança de inscrição única](./media/adobe-experience-manager-tutorial/tutorial-admin-2.png)

10. Encontre **o Adobe Granite SAML 2.0 Authentication Handler**. Em seguida, selecione o ícone **Adicionar.**

    ![Screenshot que destaca o Adobe Granite SAML 2.0 Authentication Handler.](./media/adobe-experience-manager-tutorial/tutorial-admin-3.png)

11. Tome as seguintes ações nesta página.

    ![Configure botão de poupança de Sign-On único](./media/adobe-experience-manager-tutorial/tutorial-admin-4.png)

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

### <a name="create-adobe-experience-manager-test-user"></a>Criar utilizador de teste adobe Experience Manager

Nesta secção, cria-se um utilizador chamado Britta Simon in Adobe Experience Manager. Se selecionou a opção **Utilizadores de CRX de Autocreate,** os utilizadores são criados automaticamente após a autenticação bem sucedida.

Se pretender criar utilizadores manualmente, trabalhe com a equipa de suporte do [Adobe Experience Manager](https://helpx.adobe.com/support/experience-manager.html) para adicionar os utilizadores na plataforma Adobe Experience Manager.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o Adobe Experience Manager Sign no URL onde pode iniciar o fluxo de login.  

* Vá diretamente ao URL de inscrição do Adobe Experience Manager e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito no Adobe Experience Manager para o qual configura o SSO 

Também pode utilizar o Microsoft My Apps para testar a aplicação em qualquer modo. Quando clicar no azulejo do Adobe Experience Manager nas Minhas Apps, se configurado no modo SP, será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito no Adobe Experience Manager para o qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)


## <a name="next-steps"></a>Passos seguintes

Assim que configurar o Adobe Experience Manager, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).