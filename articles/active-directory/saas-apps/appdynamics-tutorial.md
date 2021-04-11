---
title: 'Tutorial: Integração do Azure Ative Directory com a AppDynamics | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o AppDynamics.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/25/2021
ms.author: jeedes
ms.openlocfilehash: b83ec6b63d908483801a28ba89227c8431401424
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104589281"
---
# <a name="tutorial-azure-active-directory-integration-with-appdynamics"></a>Tutorial: Integração do Diretório Ativo Azure com a AppDynamics

Neste tutorial, você vai aprender a integrar AppDynamics com Azure Ative Directory (Azure AD). Quando integra a AppDynamics com AZure AD, pode:

* Controlo em Azure AD que tem acesso a AppDynamics.
* Capacitar os seus utilizadores a serem automaticamente inscritos na AppDynamics com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Subscrição única de SSO (SSO) ativada appDynamics.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* A AppDynamics suporta o SSO iniciado pela **SP.**

* A AppDynamics suporta o fornecimento do utilizador **Just In Time.**

## <a name="add-appdynamics-from-the-gallery"></a>Adicione AppDynamics da galeria

Para configurar a integração da AppDynamics no AD Azure, é necessário adicionar AppDynamics da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **digite AppDynamics** na caixa de pesquisa.
1. **Selecione AppDynamics** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-appdynamics"></a>Configurar e testar Azure AD SSO para AppDynamics

Configure e teste Azure AD SSO com AppDynamics usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado na AppDynamics.

Para configurar e testar o Azure AD SSO com AppDynamics, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure AppDynamics SSO](#configure-appdynamics-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create AppDynamics test user](#create-appdynamics-test-user)** - para ter uma contraparte de B.Simon em AppDynamics que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração de **aplicações AppDynamics,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<companyname>.saas.appdynamics.com?accountName=<companyname>`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://<companyname>.saas.appdynamics.com/controller`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e no identificador. Contacte [a equipa de suporte do Cliente AppDynamics](https://www.appdynamics.com/support/) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

4. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

6. Na secção **Configuração AppDynamics,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user&quot;></a>Criar um utilizador de teste AZure AD 

Nesta secção, irá criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
   1. Clique em **Criar**.

### <a name=&quot;assign-the-azure-ad-test-user&quot;></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a AppDynamics.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, **selecione AppDynamics**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função &quot;Acesso Predefinido&quot; selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name=&quot;configure-appdynamics-sso&quot;></a>Configure AppDynamics SSO

1. Numa janela diferente do navegador web, inicie sessão no site da empresa AppDynamics como administrador.

1. Na barra de ferramentas na parte superior, clique em **Definições** e, em seguida, clique em **Administração**.

    ![Administration](./media/appdynamics-tutorial/settings.png &quot;Administração")

1. Clique no **separador Fornecedor de Autenticação.**

    ![Fornecedor de Autenticação](./media/appdynamics-tutorial/authentication.png "Fornecedor de Autenticação")

1. Na secção **Fornecedor de Autenticação,** execute os seguintes passos:

    ![Configuração SAML](./media/appdynamics-tutorial/configuration.png "Configuração SAML")

   a. Como **Fornecedor de Autenticação**, selecione **SAML**.

   b. Na caixa de texto **url de login,** cole o valor do URL de **login** que copiou do portal Azure.

   c. Na caixa de texto **url logout,** cole o valor do **URL logout** que copiou do portal Azure.

   d. Abra o certificado codificado base-64 no bloco de notas, copie o conteúdo do mesmo na sua área de transferência e, em seguida, cole-o na caixa de texto **do Certificado**

   e. Clique em **Guardar**.

### <a name="create-appdynamics-test-user"></a>Criar utilizador de teste AppDynamics

Nesta secção, um utilizador chamado B.Simon é criado na AppDynamics. A AppDynamics suporta o fornecimento de utilizadores just-in-time, o que é ativado por padrão. Não há nada de ação para ti nesta secção. Se um utilizador já não existir no AppDynamics, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o URL de entrada de assinaturas appDynamics, onde pode iniciar o fluxo de login. 

* Vá diretamente ao URL de login da AppDynamics e inicie o fluxo de login a partir daí.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo AppDynamics nas Minhas Apps, este será redirecionado para O URL de Sign-on AppDynamics. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado a AppDynamics, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).