---
title: 'Tutorial: Integração do Diretório Ativo Azure com a Lucidchart | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Lucidchart.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/15/2021
ms.author: jeedes
ms.openlocfilehash: 5d5b07e761d5ed38cb2083054708265189bdd72f
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101651586"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-lucidchart"></a>Tutorial: Azure Ative Directory integração única (SSO) com Lucidchart

Neste tutorial, você vai aprender a integrar Lucidchart com Azure Ative Directory (Azure AD). Quando integrar lucidchart com Azure AD, pode:

* Controlo em Azure AD que tem acesso a Lucidchart.
* Permita que os seus utilizadores sejam automaticamente inscritos na Lucidchart com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Assinatura ativada por Lucidchart (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Lucidchart apoia **SP** iniciado SSO
* Lucidchart suporta **provisão** de utilizadores just in time

## <a name="add-lucidchart-from-the-gallery"></a>Adicione Lucidchart da galeria

Para configurar a integração da Lucidchart no AD Azure, precisa adicionar Lucidchart da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar da secção **da galeria,** **digite Lucidchart** na caixa de pesquisa.
1. Selecione **Lucidchart** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-lucidchart"></a>Configurar e testar Azure AD SSO para Lucidchart

Configure e teste Azure AD SSO com Lucidchart usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado em Lucidchart.

Para configurar e testar a Azure AD SSO com Lucidchart, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    * **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o Lucidchart SSO](#configure-lucidchart-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    * **[Create Lucidchart test user](#create-lucidchart-test-user)** - para ter uma contraparte de B.Simon em Lucidchart que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Lucidchart,** encontre a secção **Gerir** e selecione um único sinal de **sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

   Na caixa de texto **URL de entrada de inscrição,** digite o URL como:  `https://chart2.office.lucidchart.com/saml/sso/azure`

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

6. Na secção **Configurar Lucidchart,** copie os URL(s) apropriados de acordo com o seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a Lucidchart.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Lucidchart**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-lucidchart-sso"></a>Configure Lucidchart SSO

1. Numa janela diferente do navegador web, inicie sessão no site da empresa Lucidchart como administrador.

2. No menu em cima, clique em **Equipa**.

    ![Equipa](./media/lucidchart-tutorial/ic791190.png "Equipa")

3. Clique **em Aplicações \> Gerir SAML**.

    ![Gerir a SAML](./media/lucidchart-tutorial/ic791191.png "Gerir a SAML")

4. Na página de diálogo de definições de **autenticação SAML,** execute os seguintes passos:

    a. Selecione **Ativar a autenticação SAML** e, em seguida, clique **em Opcional**.

    ![Definições de autenticação SAML](./media/lucidchart-tutorial/ic791192.png "Definições de autenticação SAML")

    b. Na caixa de texto **do Domínio,** digite o seu domínio e, em seguida, clique em **Alterar o Certificado**.

    ![Certificado de Alteração](./media/lucidchart-tutorial/ic791193.png "Certificado de Alteração")

    c. Abra o seu ficheiro de metadados descarregado, copie o conteúdo e, em seguida, cole-o na caixa de texto **de Metadados upload.**

    ![Carregar metadados](./media/lucidchart-tutorial/ic791194.png "Carregar metadados")

    d. **Selecione Automaticamente Adicione novos utilizadores à equipa** e, em seguida, clique em Guardar **alterações**.

    ![Guardar alterações](./media/lucidchart-tutorial/ic791195.png "Guardar alterações")

### <a name="create-lucidchart-test-user"></a>Criar utilizador de teste Lucidchart

Não existe nenhum item de ação para configurar o fornecimento do utilizador à Lucidchart.  Quando um utilizador designado tenta entrar no Lucidchart utilizando o painel de acesso, a Lucidchart verifica se o utilizador existe.  

Se ainda não houver conta de utilizador disponível, esta é automaticamente criada pela Lucidchart.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o URL de entrada de lucidchart, onde pode iniciar o fluxo de login. 

* Vá diretamente ao URL de inscrição de Lucidchart e inicie o fluxo de login a partir daí.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo Lucidchart nas Minhas Apps, deverá ser automaticamente inscrito no Lucidchart para o qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

 Uma vez configurado o Lucidchart, pode impor controlos de sessão, que protegem a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. Os controlos de sessão estendem-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com a Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)