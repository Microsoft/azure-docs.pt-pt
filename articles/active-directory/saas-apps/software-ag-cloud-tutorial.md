---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com Software AG Cloud Microsoft Docs'
description: Saiba como configurar um único sinal de acesso entre o Azure Ative Directory e o Software AG Cloud.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/23/2020
ms.author: jeedes
ms.openlocfilehash: ccf945f8bfec85a18493d515dce48f4cb3e3b612
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96182370"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-software-ag-cloud"></a>Tutorial: Azure Ative Directory integração única (SSO) com Software AG Cloud

Neste tutorial, você vai aprender a integrar software AG Cloud com Azure Ative Directory (Azure AD). Quando integra o Software AG Cloud com AD Azure, pode:

* Control em Azure AD que tem acesso a Software AG Cloud.
* Capacitar os seus utilizadores a serem automaticamente inscritos no Software AG Cloud com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Subscrição ativada por Software AG Cloud (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Software AG Cloud suporta **SSO** iniciado sp
* Software AG Cloud suporta **provisão de** utilizadores just in time

## <a name="adding-software-ag-cloud-from-the-gallery"></a>Adicionar software AG Cloud da galeria

Para configurar a integração do Software AG Cloud em Azure AD, precisa de adicionar o Software AG Cloud da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **digite Software AG Cloud** na caixa de pesquisa.
1. Selecione **software AG Cloud** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-software-ag-cloud"></a>Configure e teste Azure AD SSO para software AG Cloud

Configure e teste Azure AD SSO com software AG Cloud usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado na Cloud Software AG.

Para configurar e testar O Azure AD SSO com software AG Cloud, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure software AG Cloud SSO](#configure-software-ag-cloud-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Software AG Cloud test user](#create-software-ag-cloud-test-user)** - para ter uma contraparte de B.Simon em Software AG Cloud que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Software AG Cloud,** encontre a secção **Gerir** e selecione um único sinal **de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<SUBDOMAIN>.softwareag.cloud/auth/realms/TENANT-NAME/broker/IDENTITY-PROVIDER-NAME/endpoint`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://<SUBDOMAIN>.softwareag.cloud/auth/realms/TENANT-NAME`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e no identificador. Contacte [a equipa de suporte do Software AG Cloud](mailto:support@softwareag.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregue** para descarregar o certificado e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção Configurar o **Software AG Cloud,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a Software AG Cloud.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Software AG Cloud**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-software-ag-cloud-sso"></a>Configurar software AG cloud SSO

1. Numa janela diferente do navegador web, inscreva-se no website do Software AG Cloud como administrador.

1.  Clique na **Administração**

    ![Configurar software AG Cloud1](./media/software-ag-cloud-tutorial/admin.png)

1. Aceda ao **signo único no > Adicionar fornecedor de identidade**

    ![Configurar software AG Cloud2](./media/software-ag-cloud-tutorial/add-identity-provider.png)

1. Execute os seguintes passos na página seguinte.

    ![Configurar software AG Cloud3](./media/software-ag-cloud-tutorial/saml-1.png)

    a. Na caixa de texto **do nome de exibição** do fornecedor de identidade, dê o nome de `azure ad` .

    b. No **identificador exclusivo do fornecedor de identidade para utilização no Software AG Cloud redireciona** a caixa de texto URI, cole o valor **de ID** da Entidade que copiou a partir do portal Azure.

    c. Importe o ficheiro **XML dos metadados da Federação** na **configuração do fornecedor de identidade** e clique em **Seguinte**.

    d. Vá à página **Configuração** e complete os campos conforme necessário.

### <a name="create-software-ag-cloud-test-user"></a>Criar utilizador de teste de nuvem AG de software

Nesta secção, um utilizador chamado Britta Simon é criado em Software AG Cloud. O Software AG Cloud suporta o fornecimento de utilizadores just-in-time, o que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no Software AG Cloud, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

1. Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o URL de entrada de cloud do Software AG, onde pode iniciar o fluxo de login. 

2. Vá diretamente ao URL de inscrição na nuvem de software AG e inicie o fluxo de login a partir daí.

3. Pode utilizar o Microsoft Access Panel. Quando clicar no azulejo de nuvem AG de software no Painel de Acesso, este irá redirecionar para o URL de sinal de cloud do Software AG. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado software AG Cloud, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).