---
title: 'Tutorial: Integração do Diretório Ativo Azure com AOVEit Transfer - Azure AD integração | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o MOVEit Transfer - Integração Azure AD.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: jeedes
ms.openlocfilehash: 73f378bb0f71df4df3731a3ef2a1fdb7f8abb4aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101653073"
---
# <a name="tutorial-azure-active-directory-integration-with-moveit-transfer---azure-ad-integration"></a>Tutorial: Integração do Azure Ative Directory com AOVEit Transfer - Integração AZURE AD

Neste tutorial, você vai aprender como integrar a integração da MOVEit Transfer - Azure AD com o Azure Ative Directory (Azure AD). Quando integra a MOVEit Transfer - Integração AD AZure com AZure AD, pode:

* Control em Azure AD que tem acesso à MOVEit Transfer - Integração AZURE AD.
* Permitir que os seus utilizadores sejam automaticamente inscritos na MOVEit Transfer - Integração AZure AD com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* MOVEit Transfer - Azure AD integração única sign-on (SSO) ativada subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* MOVEit Transfer - Integração AD AZure suporta **SSO** iniciado SP

## <a name="add-moveit-transfer---azure-ad-integration-from-the-gallery"></a>Adicionar Transferência MOVEit - Integração AD AZure da galeria

Para configurar a integração da MOVEit Transfer - Integração AD Azure em Azure AD, você precisa adicionar a integração MOVEit AD da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** tipo **MOVEit Transfer - Integração AD AZure** na caixa de pesquisa.
1. Selecione **MOVEit Transfer - Integração AD AZure** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-moveit-transfer---azure-ad-integration"></a>Configure e teste Azure AD SSO para transferência de MOVEit - integração AD Azure

Configure e teste Azure AD SSO com MOVEit Transfer - Integração AD Azure utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado na integração MOVEit AD.

Para configurar e testar a Azure AD SSO com a MOVEit Transfer - Integração AD Azure, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure A Transferência de MOVEit - Azure AD integração SSO](#configure-moveit-transfer---azure-ad-integration-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Criar MOVEit Transfer - utilizador de teste de integração AD Azure](#create-moveit-transfer---azure-ad-integration-test-user)** - para ter uma contrapartida de B.Simon em MOVEit Transfer - Integração AZURE AD que está ligada à representação AD Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração de aplicações **de integração MOVEit Transfer - Azure AD,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** se tiver **um ficheiro de metadados do Fornecedor de Serviços,** execute os seguintes passos:

    a. Clique **em Carregar o ficheiro de metadados**.

    ![Carregar ficheiro de metadados](common/upload-metadata.png)

    b. Clique no **logotipo da pasta** para selecionar o ficheiro de metadados e clique em **Upload**.

    ![escolher arquivo de metadados](common/browse-upload-metadata.png)

    c. Após o carregamento com sucesso do ficheiro de metadados, o valor DE URL **de identificação** e **resposta** é preenchido automaticamente na secção **de Configuração BÁSICA SAML:**

    ![MOVEit Transfer - Domínio de integração AD Azure e informações únicas de súbdido](common/sp-identifier-reply.png)

    Na caixa de texto **URL de entrada de inscrição,** digite o URL:  `https://contoso.com`

    > [!NOTE]
    > O valor **url de inscrição** não é real. Atualize o valor com o URL de Sign-On real. Contacte [a MOVEit Transfer - Azure AD integração Equipa de suporte ao cliente](https://community.ipswitch.com/s/support) para obter o valor. Pode descarregar o **ficheiro de metadados do Fornecedor** de Serviços a partir do URL de **Metadados do Fornecedor** de Serviços, que é explicado mais tarde na secção **Configure MOVEit Transfer - Azure AD integração única sign-on** do tutorial. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

4. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

6. Na secção **Configuração MOVEit Transfer - Azure AD integration,** copie os URL(s) apropriados de acordo com o seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à integração MOVEit AD.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **MOVEit Transfer - Integração AD Azure**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

### <a name="configure-moveit-transfer---azure-ad-integration-sso"></a>Configure Transferência MOVEit - Azure AD integração SSO

1. Inscreva-se no seu inquilino da MOVEit Transfer como administrador.

2. No painel de navegação à esquerda, clique em **Definições**.

    ![Secção de definições no lado da aplicação](./media/moveittransfer-tutorial/settings.png)

3. Clique no link **Single Signon,** que está em **Políticas de Segurança -> User Auth**.

    ![Políticas de segurança no lado da app](./media/moveittransfer-tutorial/sso.png)

4. Clique no link URL dos metadados para descarregar o documento de metadados.

    ![URL de metadados do fornecedor de serviços](./media/moveittransfer-tutorial/metadata.png)
    
   * Verifique se **a entidadeID** corresponde **ao identificador** na secção **de Configuração Básica SAML** .
   * Verifique **afirmaçõesConsumerService** Location URL corresponde **a URL de resposta** na secção de **configuração básica saml.**
    
     ![Configurar Sign-On única no lado da app](./media/moveittransfer-tutorial/xml.png)

5. Clique no botão **Adicionar Fornecedor de Identidade** para adicionar um novo Fornecedor de Identidade Federado.

    ![Adicionar Fornecedor de Identidade](./media/moveittransfer-tutorial/idp.png)

6. Clique **em Procurar...** para selecionar o ficheiro de metadados que descarregou a partir do portal Azure e, em seguida, clique em **Adicionar Fornecedor de Identidade** para carregar o ficheiro descarregado.

    ![Fornecedor de identidade SAML](./media/moveittransfer-tutorial/saml.png)

7. Selecione "**Sim**" como **Ativado** nas Definições do **Fornecedor de Identidade Federada de Edição...** e clique em **Guardar**.

    ![Definições federadas do fornecedor de identidade](./media/moveittransfer-tutorial/save.png)

8. Na página **'Configurações de Utilizador do Fornecedor de Identidade Federada' de Edição,** execute as seguintes ações:
    
    ![Editar Definições de fornecedor de identidade federadas](./media/moveittransfer-tutorial/attributes.png)
    
    a. Selecione **SAML NameID** como **nome de Login**.
    
    b. Selecione **Outro** **como nome completo** e na caixa de texto do nome **Atributo** coloque o valor: `http://schemas.microsoft.com/identity/claims/displayname` .
    
    c. Selecione **Outros** como **Email** e na caixa de texto **do nome Atributo** coloque o valor: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` .
    
    d. Selecione **Sim** como **Conta de criação automática no signon**.
    
    e. Clique no botão **Guardar.**

### <a name="create-moveit-transfer---azure-ad-integration-test-user"></a>Criar transferência MOVEit - Utilizador de teste de integração AD Azure

O objetivo desta secção é criar um utilizador chamado Britta Simon na MOVEit Transfer - Integração AD AZure. MOVEit Transfer - A integração AZure AD suporta o provisionamento just-in-time, que você ativou. Não há nenhum item de ação para si nesta secção. Um novo utilizador é criado durante uma tentativa de aceder à integração MOVEit Transfer - Azure AD se ainda não existir.

>[!NOTE]
>Se precisar de criar um utilizador manualmente, tem de contactar a equipa de suporte ao cliente de [integração MOVEit Transfer - Azure AD](https://community.ipswitch.com/s/support).

### <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para MOVEit Transfer - URL de integração AD Azure onde pode iniciar o fluxo de login. 

* Vá para MOVEit Transfer - Azure AD integração URL de entrada diretamente e inicie o fluxo de login a partir daí.

* Pode utilizar as minhas apps do Microsoft. Quando clicar na integração MOVEit Transfer - Azure AD nas Minhas Apps, deverá ser automaticamente inscrito na integração MOVEit AD para a qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado a integração MOVEit Transfer - Azure AD, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).