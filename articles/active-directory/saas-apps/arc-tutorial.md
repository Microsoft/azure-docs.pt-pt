---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com a Arc Publishing - SSO | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a Arc Publishing - SSO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/16/2020
ms.author: jeedes
ms.openlocfilehash: 0b1be3aa251b0248d3bbd859a183c7483fd9e38c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98735370"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-arc-publishing---sso"></a>Tutorial: Azure Ative Directory integração única (SSO) com a Arc Publishing - SSO

Neste tutorial, você vai aprender a integrar a Arc Publishing - SSO com Azure Ative Directory (Azure AD). Quando integrar a Arc Publishing - SSO com Azure AD, pode:

* Control em Azure AD que tem acesso à Arc Publishing - SSO.
* Capacitar os seus utilizadores a serem automaticamente inscritos na Arc Publishing - SSO com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Arc Publishing - SSO assinatura única ativada (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Arc Publishing - SSO suporta **SP e IDP** iniciado SSO
* Arc Publishing - SSO suporta provisão de utilizadores **just in time**


## <a name="adding-arc-publishing---sso-from-the-gallery"></a>Adicionar Arc Publishing - SSO da galeria

Para configurar a integração da Arc Publishing - SSO em Azure AD, é necessário adicionar a Arc Publishing - SSO da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **digite Arc Publishing - SSO** na caixa de pesquisa.
1. Selecione **Arc Publishing - SSO** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-arc-publishing---sso"></a>Configurar e testar Azure AD SSO para a Arc Publishing - SSO

Configure e teste Azure AD SSO com a Arc Publishing - SSO usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado na Arc Publishing - SSO.

Para configurar e testar a Azure AD SSO com a Arc Publishing - SSO, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure a Arc Publishing - SSO SSO](#configure-arc-publishing---sso-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Arc Publishing - utilizador de teste SSO](#create-arc-publishing---sso-test-user)** - para ter uma contrapartida de B.Simon na Arc Publishing - SSO que está ligada à representação AD Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração de aplicações **Arc Publishing - SSO,** encontre a secção **Gerir** e selecione **um único sinal de sing**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://www.okta.com/saml2/service-provider/<Unique ID>`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://arcpublishing-<Customer>.okta.com/sso/saml2/<Unique ID>`

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://arcpublishing-<Customer>.okta.com/sso/saml2/<Unique ID>`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL de inscrição. Contacte [a Arc Publishing - Equipa de suporte ao Cliente SSO](mailto:inf@washpost.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. A aplicação Arc Publishing - SSO espera as afirmações SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/edit-attribute.png)

1. Além de acima, a aplicação Arc Publishing - SSO espera que alguns mais atributos sejam repercutidos na resposta SAML que são mostrados abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com o seu requisito.


    | Name | Atributo de origem|
    | ---------------| --------------- |    
    | nomePróprio | user.givenname |
    | apelido | utilizador.sobrenome |
    | e-mail | user.mail |
    | grupos | user.assignedroles |

    > [!NOTE]
    > Aqui, o atributo **grupo** é mapeado com **as placas user.assigned**. Estes são papéis personalizados criados em Azure AD para mapear os nomes do grupo de volta na aplicação. Você pode encontrar mais orientação [aqui](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview) sobre como criar papéis personalizados em Azure AD.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **Configuração da Publicação Arc - SSO,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à Arc Publishing - SSO.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Arc Publishing - SSO**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se tiver configurado as funções como explicado no acima, pode selecioná-lo a partir do Dropdown de **função Select.**
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-arc-publishing---sso-sso"></a>Configure Arc Publishing - SSO SSO

Para configurar um único sign-on na **Arc Publishing - SSO** side, você precisa enviar o Certificado descarregado **(Base64)** e URLs copiados apropriados do portal Azure para a equipa de [suporte arc Publishing - SSO](mailto:inf@washpost.com). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-arc-publishing---sso-test-user"></a>Criar a Arc Publishing - Utilizador de teste SSO

Nesta secção, um utilizador chamado Britta Simon é criado na Arc Publishing - SSO. A Arc Publishing - SSO suporta o provisionamento do utilizador just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir na Arc Publishing - SSO, um novo é criado após a autenticação.

> [!Note]
> Se precisar de criar um utilizador manualmente, contacte [a equipa de suporte da Arc Publishing - SSO](mailto:inf@washpost.com).

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para Arc Publishing - SSO Assinar no URL onde pode iniciar o fluxo de login.  

* Vá diretamente à Arc Publishing - SSO Sign-on URL e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito na Arc Publishing - SSO para a qual configura o SSO 

Também pode utilizar o Microsoft My Apps para testar a aplicação em qualquer modo. Quando clicar na Rede Arc Publishing - SSO nas Minhas Apps, se configurado no modo SP será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito na Publicação Arc - SSO para a qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)


## <a name="next-steps"></a>Passos seguintes

Assim que configurar a Arc Publishing - SSO, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
