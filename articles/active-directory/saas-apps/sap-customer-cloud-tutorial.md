---
title: 'Tutorial: Integração única de sign-on (SSO) do Azure Ative Directory com a NUVEM SAP para | cliente Microsoft Docs'
description: Saiba como configurar um único sinal de inscrição entre o Azure Ative Directory e o SAP Cloud para o Cliente.
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
ms.openlocfilehash: 60381c7d8c452277b53e1af67ae7fc85349521c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98735678"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-cloud-for-customer"></a>Tutorial: Azure Ative Directy integração única de sign-on (SSO) com a NUVEM SAP para o Cliente

Neste tutorial, você vai aprender a integrar a NUVEM SAP para cliente com Azure Ative Directory (Azure AD). Quando integrar a Nuvem SAP para cliente com Azure AD, pode:

* Controle em Azure AD que tem acesso a NUVEM SAP para Cliente.
* Ative os seus utilizadores a serem automaticamente inscritos na SAP Cloud para cliente com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.


## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Sap Cloud para assinatura única de assinatura (SSO) ativada pelo cliente.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Nuvem SAP para clientes suporta **SSO** iniciado SP

## <a name="adding-sap-cloud-for-customer-from-the-gallery"></a>Adicionar nuvem SAP para cliente da galeria

Para configurar a integração da NUVEM SAP para Cliente em AD Azure, é necessário adicionar a Nuvem SAP para cliente da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **escreva nuvem SAP para cliente** na caixa de pesquisa.
1. Selecione **SAP Cloud para Cliente** a partir do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-sap-cloud-for-customer"></a>Configure e teste Azure AD SSO para nuvem SAP para cliente

Configure e teste Azure AD SSO com a NUVEM SAP para cliente usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado na Nuvem SAP para o Cliente.

Para configurar e testar o Azure AD SSO com a NUVEM SAP para o Cliente, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure a nuvem SAP para o Cliente SSO](#configure-sap-cloud-for-customer-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Crie a Nuvem SAP para utilizador](#create-sap-cloud-for-customer-test-user)** de teste de cliente - para ter uma contraparte de B.Simon em NUVEM SAP para cliente que está ligada à representação AD Ad Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página DE Integração de aplicações **SAP Cloud para Cliente,** encontre a secção **Gerir** e selecione um único sinal **de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<server name>.crm.ondemand.com`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://<server name>.crm.ondemand.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e no identificador. Contacte [a SAP Cloud para a equipa de suporte ao cliente](https://www.sap.com/about/agreements.sap-cloud-services-customers.html) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. A aplicação SAP Cloud para Cliente espera as afirmações SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos. Clique **em Editar** o ícone para abrir o diálogo dos Atributos do Utilizador.

    ![Screenshot que mostra o diálogo "Atributos do Utilizador" com o ícone "Editar" selecionado.](common/edit-attribute.png)

1. Na secção **Atributos** do Utilizador no **diálogo "Atributos & Reclamações** do Utilizador", execute os seguintes passos:

    a. Clique **em Editar o ícone** para abrir o diálogo de reclamações do utilizador **Gerir.**

    ![Screenshot que mostra o "User Atributos & Claims" com o ícone "Editar" selecionado.](./media/sap-customer-cloud-tutorial/tutorial_usermail.png)

    ![image](./media/sap-customer-cloud-tutorial/tutorial_usermailedit.png)

    b. Selecione **a Transformação** como **fonte**.

    c. Na lista **de Transformação,** selecione **ExtractMailPrefix()**.

    d. Na lista **do Parâmetro 1,** selecione o atributo do utilizador que pretende utilizar para a sua implementação.
    Por exemplo, se pretender utilizar o EmployeeID como identificador único do utilizador e tiver armazenado o valor do atributo na ExtensionAttribute2, então selecione user.extensionattribute2.

    e. Clique em **Guardar**.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregue** para descarregar o certificado e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na **configuração da nuvem SAP para** a secção cliente, copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à NUVEM SAP para Cliente.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **SAP Cloud para Cliente**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-sap-cloud-for-customer-sso"></a>Configure nuvem SAP para SSO do cliente

1. Abra uma nova janela do navegador web e inscreva-se na sua Nuvem SAP para site da empresa cliente como administrador.

2. A partir do lado esquerdo do menu, clique em **Fornecedores** de Identidade De Identidade  >  **Corporativa**  >  **Adicionar** e no pop-up adicionar o nome do fornecedor de identidade como **Azure AD,** clique em **Guardar** e clicar na **Configuração SAML 2.0**.

    ![Screenshot que mostra a página "Fornecedores de Identidade" com a caixa de texto de diálogo "Adicionar Fornecedor de Identidade" realçada e o botão "Guardar" selecionado.](./media/sap-customer-cloud-tutorial/configure01.png)

3. Na secção **de configuração SAML 2.0,** execute os seguintes passos:

    ![Screenshot que mostra a configuração "S A M L 2.0" com o botão "Procurar" selecionado.](./media/sap-customer-cloud-tutorial/configure02.png)

    a. Clique **em Procurar** para carregar o ficheiro XML dos Metadados da Federação, que descarregou a partir do portal Azure.

    b. Uma vez que o ficheiro XML é carregado com sucesso, os valores abaixo serão automaticamente povoados e depois clicar em **Guardar**.

### <a name="create-sap-cloud-for-customer-test-user"></a>Criar nuvem SAP para utilizador de teste de cliente

Para permitir que os utilizadores de Azure AD inscrevam-se na NUVEM SAP para Cliente, devem ser a provisionados na Nuvem SAP para o Cliente. Na nuvem SAP para cliente, o provisionamento é uma tarefa manual.

**Para obter uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se na NUVEM SAP para Cliente como Administrador de Segurança.

2. Do lado esquerdo do menu, clique em **Utilizadores & Dedesem o**  >  **Utilizador de Gestão do Utilizador**  >  .

    ![Screenshot que mostra a página "Gestão do Utilizador" com o botão "Adicionar utilizador" selecionado.](./media/sap-customer-cloud-tutorial/configure03.png)

3. Na secção **Adicionar Novo Utilizador,** execute os seguintes passos:

    ![Configuração SAP](./media/sap-customer-cloud-tutorial/configure04.png)

    a. Na caixa de texto do **Primeiro Nome,** insira o nome do utilizador como **B**.

    b. Na caixa de texto **do Último Nome,** insira o nome do utilizador como **Simon**.

    c. Na caixa de texto **do E-Mail,** insira o e-mail do utilizador como `B.Simon@contoso.com` .

    d. Na caixa de texto **'Nome de Início'** insira o nome do utilizador como **B.Simon**.

    e. Selecione **o Tipo de Utilizador** de acordo com o seu requisito.

    f. Selecione a opção **de ativação de conta** de acordo com o seu requisito.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para a NUVEM SAP para URL de inscrição do cliente, onde pode iniciar o fluxo de login. 

* Vá diretamente à NUVEM SAP para obter URL de inscrição do cliente e inicie o fluxo de login a partir daí.

* Pode utilizar as minhas apps do Microsoft. Quando clicar na Nuvem SAP para azulejo do cliente nas Minhas Apps, este irá redirecionar para a Nuvem SAP para URL de inscrição do cliente. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)


## <a name="next-steps"></a>Passos seguintes

Uma vez configurada a Nuvem SAP para Cliente, pode impor controlos de sessão, o que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. Os controlos de sessão estendem-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).