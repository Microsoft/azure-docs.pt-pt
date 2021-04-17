---
title: 'Tutorial: Integração do Diretório Ativo Azure com | M-Files Microsoft Docs'
description: Saiba como configurar um único sinal de inscrição entre o Azure Ative Directory e o M-Files.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/24/2021
ms.author: jeedes
ms.openlocfilehash: 680380722746522a0eb3fe6518452472be952075
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482846"
---
# <a name="tutorial-azure-active-directory-integration-with-m-files"></a>Tutorial: Integração do Diretório Ativo Azure com M-Files

Neste tutorial, você vai aprender a integrar M-Files com Azure Ative Directory (Azure AD). Quando integrar ficheiros M com AD Azure, pode:

* Controlo em Azure AD que tem acesso a Ficheiros M.
* Capacitar os seus utilizadores a serem automaticamente inscritos nos Ficheiros M com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* M-Files assinatura única (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* M-Files suporta SSO iniciado **SP.**

## <a name="add-m-files-from-the-gallery"></a>Adicionar M-Files da galeria

Para configurar a integração de M-Files em Azure AD, é necessário adicionar Ficheiros M da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** digite **Ficheiros M** na caixa de pesquisa.
1. Selecione **M-Files** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-m-files"></a>Configure e teste Azure AD SSO para M-Files

Configure e teste Azure AD SSO com M-Files usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Ficheiros M.

Para configurar e testar o Azure AD SSO com ficheiros M, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure O SSO M-Files](#configure-m-files-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create M-Files test user](#create-m-files-test-user)** - para ter uma contraparte de B.Simon em Ficheiros M que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração de aplicações **M-Files,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<tenantname>.cloudvault.m-files.com/authentication/MFiles.AuthenticationProviders.Core/sso`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://<tenantname>.cloudvault.m-files.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e no identificador. Contacte [a equipa de suporte do cliente M-Files](mailto:support@m-files.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

6. Na secção **'Configurar Ficheiros M',** copie os URL(s) apropriados de acordo com o seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a M-Files.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **M-Files**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-m-files-sso"></a>Configurar SSO de Ficheiros M

1. Para obter sSO configurado para a sua aplicação, contacte [a equipa de suporte M-Files](mailto:support@m-files.com) e forneça-lhes os Metadados descarregados.
   
    >[!NOTE]
    >Siga os próximos passos se quiser configurar sSO para a sua aplicação de ambiente de trabalho M-File. Não são necessários passos adicionais se apenas pretender configurar sSO para a versão web M-Files.  

1. Siga os próximos passos para configurar a aplicação de ambiente de trabalho M-File para ativar SSO com Azure AD. Para descarregar M-Files, aceda à página [de descarregamento de M-Files.](https://www.m-files.com/customers/product-downloads/download-update-links/)

1. Abra a janela **definições de ambiente de trabalho M-Files.** Em seguida, clique em **Adicionar**.
   
    ![A screenshot mostra as definições de ambiente de trabalho dos ficheiros M onde pode selecionar Adicionar.](./media/m-files-tutorial/settings.png)

1. Na janela Propriedades de **Conexão de Abóbada** de Documento, execute os seguintes passos:
   
    ![A screenshot mostra propriedades de conexão de abóbada de documento onde pode introduzir os valores descritos.](./media/m-files-tutorial/general.png)  

    Sob o tipo de secção servidor, os valores são os seguintes:  

    a. Para **nome,** tipo `<tenant-name>.cloudvault.m-files.com` . 
 
    b. Para **o número de** porta , tipo **4466**. 

    c. Para **protocolo**, selecione **HTTPS**. 

    d. No campo **autenticação,** selecione **Utilizador Do Windows Específico**. Em seguida, é solicitado com uma página de assinatura. Insira as suas credenciais AD AZure. 

    e. Para o **Cofre no Servidor,** selecione o cofre correspondente no servidor.
 
    f. Clique em **OK**.

### <a name="create-m-files-test-user"></a>Criar utilizador de teste de M-Files

O objetivo desta secção é criar um utilizador chamado Britta Simon em M-Files. Trabalhe com a  [equipa de suporte M-Files](mailto:support@m-files.com) para adicionar os utilizadores nos Ficheiros M.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para O URL de Sessão de M-Files, onde pode iniciar o fluxo de login. 

* Vá diretamente ao URL de inscrição de M-Files e inicie o fluxo de login a partir daí.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo M-Files nas Minhas Apps, este irá redirecionar para O URL de S-S-S-On M.. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurados ficheiros M, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
