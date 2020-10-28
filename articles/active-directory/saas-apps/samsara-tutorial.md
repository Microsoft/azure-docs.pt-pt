---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com Samsara Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Samsara.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: jeedes
ms.openlocfilehash: 9512287f656c6c64399cb8749b7451a5a780bba8
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92677638"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-samsara"></a>Tutorial: Azure Ative Directory integração única (SSO) com Samsara

Neste tutorial, você vai aprender a integrar Samsara com Azure Ative Direy (Azure AD). Quando integrar Samsara com Azure AD, pode:

* Controlo em Azure AD que tem acesso a Samsara.
* Permita que os seus utilizadores sejam automaticamente inscritos na Samsara com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Samsara assinatura única (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Samsara apoia **SP** e **IDP** iniciaM SSO
* Samsara suporta **provisão de** utilizadores just in time

## <a name="adding-samsara-from-the-gallery"></a>Adicionando Samsara da galeria

Para configurar a integração de Samsara no AZure AD, você precisa adicionar Samsara da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)
    
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações** .

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

1. Para adicionar nova aplicação, selecione **Nova aplicação** .

    ![O novo botão de aplicação](common/add-new-app.png)

1. Na secção Add da secção **da galeria,** digite **Samsara** na caixa de pesquisa.

     ![Software de gestão de privacidade OneTrust na lista de resultados](common/search-new-app.png)

1. Selecione **Samsara** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-samsara"></a>Configure e teste Azure AD SSO para Samsara

Configure e teste Azure AD SSO com Samsara usando um utilizador de teste chamado **B.Simon** . Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Samsara.

Para configurar e testar a Azure AD SSO com a Samsara, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o Samsara SSO](#configure-samsara-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Samsara test user](#create-samsara-test-user)** - para ter uma contraparte de B.Simon em Samsara que está ligada à representação AZure AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Samsara,** encontre a secção **Gerir** e selecione **um único sinal de sação** .

    ![Configurar link único de inscrição](common/select-sso.png)

1. Na página de método **de inscrição** única, selecione **SAML** .

    ![Único modo de seleção de s-on](common/select-saml-option.png)

1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    a. Na caixa de texto **url de entrada de sinais,** digite um URL utilizando o seguinte padrão: para `https://cloud.samsara.com/signin/<ORGID>` clientes em nuvem dos EUA para `https://cloud.eu.samsara.com/signin/<ORGID>` clientes em nuvem da UE

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `urn:auth0:samsara-dev:samlp-orgid-<ORGID>`

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://samsara-dev.auth0.com/login/callback?connection=samlp-orgid-<ORGID>`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de inscrição real, URL de resposta e identificador. Contacte a equipa de suporte do [Cliente Samsara](mailto:support@samsara.com) para obter estes valores, ou em Samsara, vá a **Definições**  >  **Single-Sign-On**  >  **New SAML Connection** para obter o \<ORGID\> . Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **Configuração Samsara,** copie o **URL de login**

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)
    
### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Azure Ative Directory** , selecione **Utilizadores** , e, em seguida, selecione **Todos os utilizadores** .
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome** , introduza `B.Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
   1. Clique em **Criar** .

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a Samsara.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações** .
1. Na lista de candidaturas, selecione **Samsara.**
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos** .
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-samsara-sso"></a>Configure Samsara SSO

Para configurar um único sign-on no lado **de Samsara,** você precisa enviar o Certificado descarregado **(Base64)** e URL de **login** do portal Azure para a equipe de [suporte Samsara](mailto:support@samsara.com). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-samsara-test-user"></a>Criar utilizador de teste samsara

Nesta secção, um utilizador chamado B.Simon é criado em Samsara. A Samsara suporta o fornecimento de utilizadores just-in-time, o que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir em Samsara, um novo é criado após a autenticação com um papel padrão de Standard Admin (No Dash Cam Access) para Organização. O acesso do utilizador pode então ser aumentado ou diminuído conforme necessário em Samsara.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

1. Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o URL de inscrição de Samsara, onde pode iniciar o fluxo de login. 

2. Vá diretamente ao URL de inscrição de Samsara e inicie o fluxo de login a partir daí.

3. Pode utilizar o Microsoft Access Panel. Quando clicar no azulejo samsara no Painel de Acesso, este será redirecionado para o URL de inscrição de Samsara. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)


## <a name="next-steps"></a>Passos seguintes

Uma vez configurado Samsara, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).