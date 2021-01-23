---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com sistema operativo synerise AI Growth System | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Synerise AI Growth Operating System.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/06/2021
ms.author: jeedes
ms.openlocfilehash: 0ffc6543a2597e39470c0d4aff86e7d5a1631d69
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98736640"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-synerise-ai-growth-operating-system"></a>Tutorial: Azure Ative Directory integração única de sign-on (SSO) com sistema operativo synerise AI Growth

Neste tutorial, você vai aprender a integrar Synerise com Azure Ative Diretório (Azure AD). Quando integrar o Synerise com a AD Azure, pode:

* Controlo em Azure AD que tem acesso a Synerise.
* Permita que os seus utilizadores sejam automaticamente inscritos no Synerise com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Sinarise assinatura única (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Synerise apoia **SP e IDP** iniciado SSO
* Synerise suporta **provisão do** utilizador Just In Time

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.


## <a name="adding-synerise-ai-growth-operating-system-from-the-gallery"></a>Adicionar sistema operativo synerise ai de crescimento da galeria

Para configurar a integração do Synerise no AD AZure, é necessário adicionar Synerise da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **digite Synerise AI Growth Operating System** na caixa de pesquisa.
1. Selecione **Synerise AI Growth Operating System** a partir do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-synerise-ai-growth-operating-system"></a>Configure e teste Azure AD SSO para o Sistema Operativo de Crescimento de AI sinoerizado

Configure e teste Azure AD SSO com Synerise usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado em Synerise.

Para configurar e testar o Azure AD SSO com Synerise, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o Sistema Operativo SSO do Sistema operativo de Crescimento de AI](#configure-synerise-ai-growth-operating-system-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. Crie o utilizador de **[teste do Sistema Operativo Synerise AI Growth](#create-synerise-ai-growth-operating-system-test-user)** - para ter uma contraparte de B.Simon em Synerise que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **synerise AI Growth System,** encontre a secção **Gerir** e selecione um único sinal de **sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:  `https://app.synerise.com/api-portal/uauth/saml/auth/<PROFILE_HASH>`

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://app.synerise.com/api-portal/uauth/saml/auth/<PROFILE_HASH>`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de resposta real e URL de inscrição. Contacte [a equipa de apoio synerise](mailto:support@synerise.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **De Configuração synerise, copie** os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a Synerise.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Synerise AI Growth Operating System**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-synerise-ai-growth-operating-system-sso"></a>Configure Synerise AI Growth Operating System SSO

1. Faça login no Synerise como administrador.

1. Aceda às **Definições > Controlo de Acesso**.

    ![Definições de sinerise](./media/synerise-ai-growth-ecosystem-tutorial/settings.png)

1. Na página **Controlo de Acesso,** clique no botão **Mostrar** no **separador Sign-On único.**

    ![Controlo de Acesso sinerisco](./media/synerise-ai-growth-ecosystem-tutorial/single-sign-on.png)

1. Execute os seguintes passos na página abaixo.

    ![Configuração de sinerise](./media/synerise-ai-growth-ecosystem-tutorial/configuration.png)

    a. Na caixa de texto **ID da Entidade fornecedora de identificação do identifique,** cole o valor **identificador Azure AD** que copiou do portal Azure.

    b. Na caixa de texto **sso (https),** cole o valor URL de **login** que copiou a partir do portal Azure.

    c. Na caixa de texto **de identificação da aplicação do Fornecedor de Identidade,** cole o valor **de ID da aplicação.**

    d. Copy Service Provider redireciona o valor **URI,** cole este valor na caixa de texto **URL de resposta** na secção configuração de SAML básico no portal Azure.

    e. Selecione **HTTP REDIRECT** na **ligação Pedido.**

    f. Ligue a **assinatura 'Pedido'.**

    exemplo, Faça o upload do ficheiro certificado descarregado **(Base64)** para o **Certificado de Assinatura do Fornecedor de Identidade.**

    i. Clique em **Aplicar**.

### <a name="create-synerise-ai-growth-operating-system-test-user"></a>Criar utilizador de teste do Sistema Operativo Synerise AI Growth

Nesta secção, um utilizador chamado Britta Simon é criado em Synerise. A sinaise suporta o provisionamento do utilizador just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir em Synerise, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para Synerise Sign no URL onde pode iniciar o fluxo de login.  

* Vá diretamente ao URL de inscrição de Synerise e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito no Synerise para o qual configura o SSO 

Também pode utilizar o Microsoft My Apps para testar a aplicação em qualquer modo. Quando clicar no azulejo Synerise nas Minhas Apps, se configurado no modo SP, será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito no Synerise para o qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)


## <a name="next-steps"></a>Próximos passos

Uma vez configurado Synerise, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).