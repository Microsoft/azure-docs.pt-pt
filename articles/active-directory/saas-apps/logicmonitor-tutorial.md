---
title: 'Tutorial: Integração do Diretório Ativo Azure com o LogicMonitor | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o LogicMonitor.
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
ms.openlocfilehash: d5342782c26b5c274699bacc4ea0c7cdf5b7f880
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101649432"
---
# <a name="tutorial-azure-active-directory-integration-with-logicmonitor"></a>Tutorial: Integração do Azure Ative Directory com o LogicMonitor

Neste tutorial, você vai aprender a integrar LogicMonitor com Azure Ative Directory (Azure AD). Quando integrar o LogicMonitor com AD Azure, pode:

* Controlo em Azure AD que tem acesso ao LogicMonitor.
* Ative os seus utilizadores a serem automaticamente inscritos no LogicMonitor com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD do Azure com o LogicMonitor, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Subscrição ativada por LogicMonitor.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* LogicMonitor suporta SSO iniciado **pela SP**

## <a name="add-logicmonitor-from-the-gallery"></a>Adicione LogicMonitor da galeria

Para configurar a integração do LogicMonitor no AD AZure, é necessário adicionar o LogicMonitor da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **digite LogicMonitor** na caixa de pesquisa.
1. Selecione **LogicMonitor** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-logicmonitor"></a>Configurar e testar Azure AD SSO para LogicMonitor

Configure e teste Azure AD SSO com LogicMonitor usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no LogicMonitor.

Para configurar e testar a Azure AD SSO com o LogicMonitor, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure LogicMonitor SSO](#configure-logicmonitor-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create LogicMonitor test user](#create-logicmonitor-test-user)** - para ter uma contraparte de B.Simon in LogicMonitor que está ligada à representação AD Ad Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **LogicMonitor,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![Informações de assinatura única do Domínio LogicMonitor e URLs](common/sp-identifier.png)

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<companyname>.logicmonitor.com`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://<companyname>.logicmonitor.com`
    
    c. Na caixa de texto **URL de resposta (URL do serviço de consumo de afirmação),** digite um URL utilizando o seguinte padrão: `https://companyname.logicmonitor.com/santaba/saml/SSO/` 
  
    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e no identificador. Contacte [a equipa de suporte do Cliente LogicMonitor](https://www.logicmonitor.com/contact/) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

6. Na secção Configurar o **LogicMonitor,** copie os URL(s) apropriados de acordo com o seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao LogicMonitor.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **LogicMonitor**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

### <a name="configure-logicmonitor-sso"></a>Configurar logicMonitor SSO

1. Faça login no seu site da empresa **LogicMonitor** como administrador.

2. No menu em cima, clique em **Definições**.

    ![Definições](./media/logicmonitor-tutorial/ic790052.png "Definições")

3. No taco de navegação do lado esquerdo, clique em **Single Sign On**.

    ![Único sign-on](./media/logicmonitor-tutorial/ic790053.png "Início de Sessão Único")

4. Na secção **de definições de sign-on único (SSO),** execute os seguintes passos:

    ![Definições de Sign-On única](./media/logicmonitor-tutorial/ic790054.png "Definições de Sign-On única")

    a. Selecione **Ativar o único sinal de sôm.**

    b. Como **Atribuição de Função Predefinida**, selecione **readonly**.

    c. Abra o ficheiro de metadados descarregado no bloco de notas e, em seguida, cole o conteúdo do ficheiro na caixa de texto **do Fornecedor de Identidade.**

    d. Clique em **Guardar Alterações**.

### <a name="create-logicmonitor-test-user"></a>Criar utilizador de teste LogicMonitor

Para que os utilizadores de Azure AD possam iniciar sação, devem ser adutorados à aplicação LogicMonitor utilizando os seus nomes de utilizador do Azure Ative Directory.

**Para configurar o provisionamento do utilizador, execute os seguintes passos:**

1. Faça login no seu site da empresa LogicMonitor como administrador.

2. No menu em cima, clique em **Definições** e, em seguida, clique em **Funções e Utilizadores**.

    ![Funções e Utilizadores](./media/logicmonitor-tutorial/ic790056.png "Funções e Utilizadores")

3. Clique em **Adicionar**.

4. Na secção **Adicionar uma conta,** execute os seguintes passos:

    ![Adicionar uma conta](./media/logicmonitor-tutorial/ic790057.png "Adicionar uma conta")

    a. Digite o **nome de utilizador**, **Email**, **Password** e **Retipe** os valores de senha do utilizador do Azure Ative Directory que pretende obter nas caixas de texto relacionadas.

    b. Selecione **Funções**, **Ver Permissões** e **o Estado**.

    c. Clique **em Submeter.**

> [!NOTE]
> Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador LogicMonitor ou APIs fornecidas pelo LogicMonitor para fornecer contas de utilizadores do Azure Ative Directory.

### <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o URL de entrada de sinal do LogicMonitor, onde pode iniciar o fluxo de login. 

* Vá diretamente ao URL de inscrição do LogicMonitor e inicie o fluxo de login a partir daí.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo LogicMonitor nas Minhas Apps, deverá ser automaticamente inscrito no LogicMonitor para o qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado LogicMonitor, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).