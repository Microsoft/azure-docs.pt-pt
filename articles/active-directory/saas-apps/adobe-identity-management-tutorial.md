---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com a Adobe Identity Management | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a Adobe Identity Management.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/15/2021
ms.author: jeedes
ms.openlocfilehash: fdca04c645e1bb956c8e9f294c702b639c8e2f74
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98726405"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-adobe-identity-management"></a>Tutorial: Azure Ative Directory integração única (SSO) com a Adobe Identity Management

Neste tutorial, você vai aprender a integrar a Adobe Identity Management com a Azure Ative Directory (Azure AD). Quando integrar a Adobe Identity Management com AZure AD, pode:

* Controlo em Azure AD que tem acesso à Adobe Identity Management.
* Capacitar os seus utilizadores a serem automaticamente inscritos na Adobe Identity Management com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Subscrição ativada pela Adobe Identity Management (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Adobe Identity Management suporta **SSO** iniciado sp

## <a name="adding-adobe-identity-management-from-the-gallery"></a>Adicionar Adobe Gestão de Identidade da galeria

Para configurar a integração da Adobe Identity Management em Azure AD, é necessário adicionar a Adobe Identity Management da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **digite a Gestão de Identidade da Adobe** na caixa de pesquisa.
1. Selecione **Adobe Identity Management** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-adobe-identity-management"></a>Configurar e testar Azure AD SSO para a Gestão de Identidade da Adobe

Configure e teste Azure AD SSO com a Adobe Identity Management utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado na Adobe Identity Management.

Para configurar e testar a Azure AD SSO com a Adobe Identity Management, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o SSO de Gestão de Identidade da Adobe](#configure-adobe-identity-management-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Adobe Identity Management test user](#create-adobe-identity-management-test-user)** - para ter uma contraparte de B.Simon na Adobe Identity Management que está ligada à representação AZure AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Adobe Identity Management,** encontre a secção **Gerir** e selecione um único sinal **de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    a. Na caixa de texto **URL, digite** o URL: `https://adobe.com`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://federatedid-na1.services.adobe.com/federated/saml/metadata/alias/<CUSTOM_ID>`

    > [!NOTE]
    > O valor do Identificador não é real. Atualize o valor com o identificador real. Contacte [a equipa de suporte ao cliente da Adobe Identity Management](mailto:identity@adobe.com) para obter o valor. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregue** para descarregar o certificado e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção **De Gestão de Identidade da Adobe,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à Adobe Identity Management.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Adobe Identity Management**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-adobe-identity-management-sso"></a>Configurar a Adobe Identity Management SSO

1. Para automatizar a configuração dentro da Adobe Identity Management, é necessário instalar a **extensão do navegador 'As aplicações' Secure Sign-in** clicando **em instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique em **Configurar a Gestão de Identidade da Adobe** irá direcioná-lo para a aplicação Adobe Identity Management. A partir daí, forneça as credenciais de administração para assinar na Adobe Identity Management. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-8.

    ![Configuração de configuração](common/setup-sso.png)

3. Se pretender configurar a Adobe Identity Management manualmente, numa janela diferente do navegador web, inscreva-se no site da empresa Adobe Identity Management como administrador.

4. Vá ao **separador Definições** e clique no **Criar Diretório**.

    ![Definições de Gestão de Identidade da Adobe](./media/adobe-identity-management-tutorial/settings.png)

5. Dê o nome do diretório na caixa de texto e selecione **Identificação Federada,** clique em **Seguinte**.

    ![Adobe Identity Management cria diretório](./media/adobe-identity-management-tutorial/create-directory.png)

6. Selecione os **Outros Fornecedores SAML** e clique em **Seguinte**.
 
    ![Fornecedores de saml de Gestão de Identidade da Adobe](./media/adobe-identity-management-tutorial/saml-providers.png)

7. Clique em **selecionar** para carregar o ficheiro **Metadadata XML** que descarregou a partir do portal Azure.

    ![Configuração saml de gestão de identidade da Adobe](./media/adobe-identity-management-tutorial/saml-configuration.png)

8. Clique em **'Feito'.**

### <a name="create-adobe-identity-management-test-user"></a>Criar utilizador de teste de Gestão de Identidade da Adobe

1. Aceda ao **separador Utilizadores** e clique em **Adicionar Utilizador.**

    ![Adobe Identity Management adicionar utilizador](./media/adobe-identity-management-tutorial/add-user.png)

2. Na caixa de texto **do endereço de e-mail do utilizador DoR,** forneça o **endereço de e-mail**.

    ![Adobe Identity Management salvar utilizador](./media/adobe-identity-management-tutorial/save-user.png)

3. Clique em **Guardar**.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções.

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o URL de inscrição de gestão de identidade da Adobe, onde pode iniciar o fluxo de login.

* Vá diretamente ao URL de Gestão de Identidade da Adobe e inicie o fluxo de login a partir daí.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo da Adobe Identity Management nas Minhas Apps, este será redirecionado para o URL de Gestão de Identidade da Adobe. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado a Adobe Identity Management, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).