---
title: 'Tutorial: Integração do Azure Ative Directory com Tableau Online Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Tableau Online.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: d6b1fad722fa0a2f44c7e415f208f7e29a272a70
ms.sourcegitcommit: 1140ff2b0424633e6e10797f6654359947038b8d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/30/2020
ms.locfileid: "97813960"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tableau-online"></a>Tutorial: Azure Ative Directory integração única (SSO) com Tableau Online

Neste tutorial, você vai aprender a integrar Tableau Online com Azure Ative Directory (Azure AD). Quando integra o Tableau Online com a AD Azure, pode:

* Controle em Azure AD que tem acesso ao Tableau Online.
* Ative os seus utilizadores a serem automaticamente inscritos no Tableau Online com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Tableau Online assinatura única (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Tableau Online suporta **SSO** iniciado SP

## <a name="adding-tableau-online-from-the-gallery"></a>Adicionar Tableau Online da galeria

Para configurar a integração do Tableau Online no AZure AD, é necessário adicionar tableau Online da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** escreva **Tableau Online** na caixa de pesquisa.
1. Selecione **Tableau Online** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-tableau-online"></a>Configure e teste Azure AD SSO para Tableau Online

Nesta secção, configura e testa o Azure AD com tableau online com base num utilizador de teste chamado **Britta Simon**.
Para um único sinal de trabalho, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no Tableau Online.

Para configurar e testar o Azure AD SSO com tableau online, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o Tableau Online SSO](#configure-tableau-online-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Tableau Online test user](#create-tableau-online-test-user)** - para ter uma contraparte de B.Simon em Tableau Online que está ligada à representação AZure AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração de aplicações **Tableau Online,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    a. Na caixa de texto **URL, digite** o URL: `https://sso.online.tableau.com/public/sp/login?alias=<entityid>`

    b. Na caixa de texto **identifier (Entity ID),** digite o URL: `https://sso.online.tableau.com/public/sp/metadata?alias=<entityid>`

    > [!NOTE]
    > Você receberá o `<entityid>` valor da secção **Configurar Tableau Online** neste tutorial. O valor de ID da entidade será o valor **do identificador Azure AD** na secção **Configurar Tableau Online.**

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

6. Na secção **Configurar Tableau Online,** copie os URL(s) apropriados de acordo com o seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao Tableau Online.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Tableau Online**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-tableau-online-sso"></a>Configurar tableau online SSO

1. Para automatizar a configuração dentro do Tableau Online, é necessário instalar a extensão do **navegador 'As aplicações' Secure Sign-in** clicando **em instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique em **Configurar Tableau Online** irá direcioná-lo para a aplicação Tableau Online. A partir daí, forneça as credenciais de administração para assinar no Tableau Online. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-7.

    ![Configuração de configuração](common/setup-sso.png)

3. Se pretender configurar o Tableau Online manualmente, numa janela diferente do navegador, inscreva-se no site da empresa Tableau Online como administrador.

1. Ir para **Definições** e, em seguida, **Autenticação**.

    ![A screenshot mostra autenticação selecionada no menu Definições.](./media/tableauonline-tutorial/tutorial_tableauonline_09.png)

2. Para ativar a secção SAML, **Tipos de Autenticação Sub-Autenticação.** Verifique **Ativar um método de autenticação adicional** e, em seguida, verifique a caixa de verificação **SAML.**

    ![A screenshot mostra a secção de tipos de autenticação onde pode selecionar os valores.](./media/tableauonline-tutorial/tutorial_tableauonline_12.png)

3. Desloque-se até ao **ficheiro de metadados de importação para a secção Tableau Online.**  Clique em Procurar e importe o ficheiro metadados, que descarregou a partir de Azure AD. Em seguida, clique **em Aplicar**.

   ![A screenshot mostra a secção onde pode importar o ficheiro de metadados.](./media/tableauonline-tutorial/tutorial_tableauonline_13.png)

4. Na secção **de afirmações match, insira** o nome de afirmação do Fornecedor de Identidade correspondente para **endereço de e-mail,** **nome próprio** e **apelido**. Para obter esta informação da Azure AD: 
  
    a. No portal Azure, vá à página de integração de aplicações **Tableau Online.**

    b. Na secção **Atributos & Pedidos** de Utilizador, clique no ícone de edição.

   ![A screenshot mostra a secção "Atributos & Do Utilizador" onde pode selecionar o ícone de edição.](./media/tableauonline-tutorial/attributesection.png)

    c. Copie o valor do espaço de nome para estes atributos: nome dado, e-mail e apelido utilizando os seguintes passos:

   ![A screenshot mostra os atributos Givenname, Surname e Emailaddress.](./media/tableauonline-tutorial/tutorial_tableauonline_10.png)

    d. Clique no valor **do utilizador.dado nome**

    e. Copie o valor da caixa de texto **Namespace.**

    ![A screenshot mostra a secção de reclamações do utilizador Gerir onde pode introduzir o Espaço Nome.](./media/tableauonline-tutorial/attributesection2.png)

    f. Para copiar os valores do espaço de nome para o e-mail e sobrenome repita os passos acima.

    exemplo, Mude para a aplicação Tableau Online e, em seguida, desaver a secção **"Atributos do Utilizador & Reclamações** da seguinte forma:

    * Email: **email** ou **userprincipalname**

    * Primeiro nome: **nome dado**

    * Apelido: **apelido**

    ![A screenshot mostra a secção de atributos Match onde pode introduzir os valores.](./media/tableauonline-tutorial/tutorial_tableauonline_14.png)

### <a name="create-tableau-online-test-user"></a>Criar utilizador de teste Online Tableau

Nesta secção, cria-se um utilizador chamado Britta Simon in Tableau Online.

1. No **Tableau Online,** clique em **Definições** e, em seguida, na secção **de Autenticação.** Desloque-se para baixo para gerir a secção **de utilizadores.** Clique **em Adicionar Utilizadores** e, em seguida, clique **em Introduzir Endereços de E-mail.**
  
    ![A screenshot mostra a secção de utilizadores 'Gerir' onde pode selecionar utilizadores Adicionar.](./media/tableauonline-tutorial/tutorial_tableauonline_15.png)

2. **Selecione Adicionar os utilizadores para a autenticação (SAML).** Na **caixa de e-mail Enter os endereços** de texto adicionam britta.simon \@ contoso.com
  
    ![A screenshot mostra a página 'Adicionar Utilizadores' onde pode introduzir um endereço de e-mail.](./media/tableauonline-tutorial/tutorial_tableauonline_11.png)

3. Clique **em Adicionar Utilizadores**.

### <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções.

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para tableau URL de inscrição on-line onde você pode iniciar o fluxo de login.

* Vá diretamente ao URL de inscrição online do Tableau e inicie o fluxo de login a partir daí.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo Online tableau nas Minhas Apps, este será redirecionado para TABLEAU Online Sign-on URL. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="next-steps"></a>Passos seguintes

Assim que configurar o Tableau Online, pode impor o controlo de Sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com a Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)