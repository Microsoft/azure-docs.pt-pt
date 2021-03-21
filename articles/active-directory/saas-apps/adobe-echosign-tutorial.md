---
title: 'Tutorial: Integração do Diretório Ativo Azure com a Adobe Sign | Microsoft Docs'
description: Saiba como configurar um único sinal de inscrição entre o Azure Ative Directory e o Adobe Sign.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/19/2021
ms.author: jeedes
ms.openlocfilehash: 7162c38aae2fec4ea21aae56fa8c3649f7a55425
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101649978"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-sign"></a>Tutorial: Integração do Diretório Ativo Azure com Sinal de Adobe

Neste tutorial, você vai aprender a integrar o Adobe Sign com O Diretório Ativo Azure (Azure AD). Quando integrar o Adobe Sign com AZure AD, pode:

* Controlo em Azure AD que tem acesso ao Adobe Sign.
* Ative os seus utilizadores a iniciarem automaticamente o contrato com o Adobe Assinar com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:
 
* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Adobe Assinar uma única assinatura ativada (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Adobe Sign suporta **SSO** iniciado SP

## <a name="add-adobe-sign-from-the-gallery"></a>Adicione Sinal de Adobe da galeria

Para configurar a integração do Adobe Sign em Azure AD, é necessário adicionar o Adobe Sign da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **digite o Sinal de Adobe** na caixa de pesquisa.
1. Selecione **Adobe Assinar** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-adobe-sign"></a>Configurar e testar Azure AD SSO para sinal de Adobe

Nesta secção, configura e testa o Azure AD com o Adobe Sign baseado num utilizador de teste chamado **Britta Simon**.
Para um único sinal de sação a funcionar, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no Adobe Sign.

Para configurar e testar o Azure AD com o Adobe Sign, tem de executar os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
1. **[Configure o Adobe Sign SSO](#configure-adobe-sign-sso)** - para configurar as definições de Sign-On única no lado da aplicação.
    1. **[Create Adobe Sign test user](#create-adobe-sign-test-user)** - para ter uma contraparte de Britta Simon no Adobe Sign que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com o Adobe Sign, execute os seguintes passos:

1. No portal Azure, na página de integração da aplicação **Adobe Sign,** selecione **Single sign-on**.

1. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

1. Na **configuração single Sign-On com página SAML,** clique no ícone de lápis para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<companyname>.echosign.com/`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://<companyname>.echosign.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e no identificador. Contacte [a equipa de suporte do Adobe Sign Client](https://helpx.adobe.com/in/contact/support.html) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

6. Na secção **De Configuração do Sinal de Adobe,** copie os URL(s) apropriados de acordo com o seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao Adobe Sign.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Adobe Sign**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

### <a name="configure-adobe-sign-sso"></a>Configurar o sinal de Adobe SSO

1. Antes da configuração, contacte a equipa de suporte do [Adobe Sign Client](https://helpx.adobe.com/in/contact/support.html) para adicionar o seu domínio na lista de autorizações do Adobe Sign. Eis como adicionar o domínio:

    a. A [equipa de suporte do Adobe Sign Client](https://helpx.adobe.com/in/contact/support.html) envia-lhe um token gerado aleatoriamente. Para o seu domínio, o token será como o seguinte: **adobe-sign-verification= xxxxx**

    b. Publique o token de verificação num registo de texto DNS e notifique a equipa de suporte do [Adobe Sign Client](https://helpx.adobe.com/in/contact/support.html).

    > [!NOTE]
    > Isto pode levar alguns dias, ou mais. Note que os atrasos de propagação do DNS significam que um valor publicado no DNS pode não ser visível por uma hora ou mais. O seu administrador de TI deve saber como publicar este token num registo de texto DNS.

    c. Quando notifica a equipa de suporte do [Adobe Sign Client](https://helpx.adobe.com/in/contact/support.html) através do bilhete de suporte, após a publicação do token, eles validam o domínio e adicionam-no à sua conta.

    d. Geralmente, eis como publicar o símbolo num registo de DNS:

    * Inscreva-se na sua conta de domínio
    * Encontre a página para atualizar o registo dns. Esta página pode ser chamada de Gestão de DNS, Gestão de Servidores de Nomes ou Definições Avançadas.
    * Encontre os registos TXT para o seu domínio.
    * Adicione um registo TXT com o valor total do token fornecido pela Adobe.
    * Guarde as alterações.

1. Numa janela diferente do navegador web, inscreva-se no site da empresa Adobe Sign como administrador.

1. No menu SAML, selecione **Definições** DE  >  **SAML de Definições** de Contas .

    ![Screenshot da página de Definições SAML do Sinal de Adobe](./media/adobe-echosign-tutorial/settings.png "Conta")

1. Na secção **Definições SAML,** execute os seguintes passos:

    ![Screenshot que destaca as definições DE SAML, incluindo a SAML Mandatory.](./media/adobe-echosign-tutorial/saml1.png "Definições SAML")

   ![Screenshot das Definições SAML](./media/adobe-echosign-tutorial/saml.png "Definições SAML")

   a. No **modo SAML**, selecione **SAML Mandatory**.

   b. Selecione **Permitir que os administradores de conta echosign iniciem o login utilizando as suas credenciais de ecosign**.

   c. Sob **a Criação do Utilizador**, selecione adicionar automaticamente os **utilizadores autenticados através do SAML**.

   d. Pasta **Azure Ad Identifier**, que copiou do portal Azure para a caixa de texto **Idp Entity ID.**

   e. Colar **URL de login**, que copiou do portal Azure para a caixa de texto **URL do Idp Login.**

   f. Pasta **URL logout,** que copiou do portal Azure para a caixa de texto **Idp Logout URL.**

   exemplo, Abra o seu ficheiro **Certificado (Base64)** descarregado no Bloco de Notas. Copie o conteúdo da sua pasta e, em seguida, cole-o na caixa de texto **do Certificado IdP.**

   h. Selecione **Guardar alterações**.

### <a name="create-adobe-sign-test-user"></a>Criar utilizador de teste de sinal de Adobe

Para permitir que os utilizadores de Azure AD inscrevam-se no Adobe Sign, devem ser a provisionados no Adobe Sign. Esta é uma tarefa manual.

>[!NOTE]
>Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador do Adobe Sign ou APIs fornecidas pela Adobe Sign para fornecer contas de utilizador Azure AD. 

1. Inscreva-se no site da empresa **Adobe Sign** como administrador.

2. No menu em cima, selecione **Conta.** Em seguida, no painel esquerdo, selecione **Utilizadores & Grupos**  >  **Criar um novo utilizador**.

    ![Screenshot do site da empresa Adobe Sign, com Conta, Utilizadores &Grupos e Criar um novo utilizador em destaque](./media/adobe-echosign-tutorial/account.png "Conta")

3. Na secção **Criar Novo Utilizador,** execute os seguintes passos:

    ![Screenshot da secção criar novo utilizador](./media/adobe-echosign-tutorial/user.png "Criar Utilizador")

    a. Digite o **Endereço de E-mail,** **Primeiro Nome** e **Último Nome** de uma conta AD válida que pretenda obter nas caixas de texto relacionadas.

    b. Selecione **Criar Utilizador**.

>[!NOTE]
>O titular da conta Azure Ative Directory recebe um e-mail que inclui um link para confirmar a conta, antes de ficar ativa. 

### <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o URL de sinal de sinais de Adobe, onde pode iniciar o fluxo de login. 

* Vá diretamente ao URL de sinal de sinal de Adobe e inicie o fluxo de login a partir daí.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo do Sinal de Adobe nas Minhas Apps, deverá ser automaticamente inscrito no Sinal de Adobe para o qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado o Adobe Sign pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).