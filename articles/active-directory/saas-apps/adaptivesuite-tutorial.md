---
title: 'Tutorial: Integração do Diretório Ativo Azure com Insights Adaptive | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Adaptive Insights.
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
ms.openlocfilehash: 6372cd9d778210163c461c55119343e6c6911e4d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101649092"
---
# <a name="tutorial-integrate-adaptive-insights-with-azure-active-directory"></a>Tutorial: Integrar Insights Adaptativos com Diretório Ativo Azure

Neste tutorial, você vai aprender a integrar Insights Adaptativos com Azure Ative Directory (Azure AD). Quando integra Insights Adaptativos com AD AZure, pode:

* Controle em Azure AD que tem acesso a Insights Adaptativos.
* Capacitar os seus utilizadores a serem automaticamente inscritos no Adaptive Insights com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Adaptação Insights única sindes de sinstro (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Adaptive Insights suporta **IDP** iniciado SSO

## <a name="add-adaptive-insights-from-the-gallery"></a>Adicionar Insights Adaptivos da galeria

Para configurar a integração de Insights Adaptativos em AZure AD, é necessário adicionar Insights Adaptativos da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **escreva Insights Adaptativos** na caixa de pesquisa.
1. Selecione **Insights Adaptivos** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-adaptive-insights"></a>Configure e teste Azure AD SSO para insights adaptativos

Configure e teste Azure AD SSO com Insights Adaptivos usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado em Insights Adaptive.

Para configurar e testar o Azure AD SSO com Insights Adaptáveis, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure a Adaptive Insights SSO](#configure-adaptive-insights-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. Crie o utilizador de **[teste Desapresentado Insights](#create-adaptive-insights-test-user)** - para ter uma contrapartida de B.Simon em Insights Adaptivos que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Adaptive Insights,** encontre a secção **Gerir** e selecione **'Único sinal de s-on'.**
1. Na página **de método de inscrição única,** selecione **SAML**.
1. Na **configuração single Sign-On com página SAML,** clique no ícone de lápis para **configuração SAML básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    > [!NOTE]
    > Pode obter valores de URL de identificação (Entity ID) e resposta a partir da página **de Definições SSO SSO** da Adaptive Insights.

4. Na **configuração single Sign-On com página SAML,** na secção Certificado de Assinatura **SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

6. Na secção **Configurar Insights Adaptativos,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a Insights Adaptativos.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Adaptive Insights**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

### <a name="configure-adaptive-insights-sso"></a>Configurar insights adaptativos SSO

1. Numa janela diferente do navegador web, inscreva-se no site da empresa Adaptive Insights como administrador.

2. Vá para a **Administração.**

    ![Screenshot que destaca a Administração no painel de navegação.](./media/adaptivesuite-tutorial/administration.png "Administrador")

3. Na secção **Utilizadores e Funções,** clique em **Definições SSO SAML**.

    ![Gerir as definições SSO da SAML](./media/adaptivesuite-tutorial/settings.png "Gerir as definições SSO da SAML")

4. Na página **SSO Definições SAML,** execute os seguintes passos:

    ![Definições SSO SAML](./media/adaptivesuite-tutorial/saml.png "Definições SSO SAML")

    a. Na caixa de texto **do nome do fornecedor de identidade,** escreva um nome para a sua configuração.

    b. Cole o valor do **identificador Azure AD** copiado do portal Azure para a caixa de texto **iD da entidade fornecedora** de identidade.

    c. Cole o valor URL de **login** copiado do portal Azure para a caixa de texto **SSO URL do fornecedor de identidade.**

    d. Cole o valor **URL logout** copiado do portal Azure para a caixa de texto **URL de logout personalizado.**

    e. Para fazer o upload do certificado descarregado, clique em **Escolher o ficheiro**.

    f. Selecione o seguinte, para:

     * **Id do utilizador SAML**, selecione **o nome de utilizador adaptável do utilizador**.

     * **Local de identificação do utilizador SAML**, selecione **o id do utilizador no NomeID do Assunto**.

     * **Formato SAML NameID**, selecione **endereço de e-mail**.

     * **Ativar o SAML**, **selecione Permitir o login SAML SSO e Direct Adaptive Insights**.

    exemplo, Copie **o URL SSO insights adaptativos** e cole-se nas caixas de texto **de Identifier (Entity ID)** e **Answer URL** na secção **de Configuração SAML Básica** no portal Azure.

    h. Clique em **Guardar**.

### <a name="create-adaptive-insights-test-user"></a>Criar utilizador de teste de Insights Adaptativos

Para permitir que os utilizadores de Azure AD inscrevam-se em Insights Adaptativos, devem ser ateados em Insights Adaptativos. No caso de Insights Adaptativos, o provisionamento é uma tarefa manual.

**Para configurar o provisionamento do utilizador, execute os seguintes passos:**

1. Inscreva-se no site da empresa **Adaptive Insights** como administrador.

2. Vá para a **Administração.**

   ![Administração](./media/adaptivesuite-tutorial/administration.png "Administrador")

3. Na secção **Utilizadores e Funções,** clique nos **Utilizadores**.

   ![Adicionar utilizador](./media/adaptivesuite-tutorial/users.png "Adicionar Utilizador")

4. Na secção **Novo Utilizador,** execute os seguintes passos:

   ![Enviar](./media/adaptivesuite-tutorial/new.png "Enviar")

   a. Digite o **Nome**, **Nome de Utilizador**, **Email**, **Password** de um utilizador válido do Azure Ative Directory que pretende apresentar nas caixas de texto relacionadas.

   b. Selecione um **papel**.

   c. Clique **em Submeter.**

> [!NOTE]
> Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador do Adaptive Insights fornecidas pela Adaptive Insights para fornecer contas de utilizador Azure AD.

### <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções.

* Clique em Testar esta aplicação no portal Azure e deverá ser automaticamente inscrito no Adaptive Insights para o qual configura o SSO.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo De Insights Adaptativos nas Minhas Apps, deverá ser automaticamente inscrito nos Insights Adaptativos para os quais configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado Insights Adaptativos, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).