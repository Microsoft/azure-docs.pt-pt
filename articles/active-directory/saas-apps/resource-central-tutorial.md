---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com resource Central | Microsoft Docs'
description: Saiba como configurar um único sinal de inscrição entre o Azure Ative Directory e o Resource Central.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/13/2021
ms.author: jeedes
ms.openlocfilehash: dbc148fcbcd9c3be86a29df1e08755611a347b07
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100586563"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-resource-central"></a>Tutorial: Azure Ative Directy integração única (SSO) com Resource Central

Neste tutorial, você vai aprender a integrar o Resource Central com o Azure Ative Directory (Azure AD). Quando integra a Resource Central com AZure AD, pode:

* Controlo em Azure AD que tem acesso à Resource Central.
* Ative os seus utilizadores a serem automaticamente inscritos na Resource Central com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Assinatura ativada por SSO (SSO) de recurso Central.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Resource Central suporta **SSO** iniciado SP

* Resource Central suporta **provisão do** utilizador Just In Time

## <a name="add-resource-central-from-the-gallery"></a>Adicione o Resource Central da galeria

Para configurar a integração da Resource Central no Azure AD, é necessário adicionar a Resource Central da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar uma nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** na caixa de pesquisa, insira **a Central de Recursos.**
1. Selecione **Resource Central** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-resource-central"></a>Configurar e testar Azure AD SSO para a Central de Recursos

Configure e teste Azure AD SSO com Resource Central usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado na Resource Central.

Para configurar e testar o Azure AD SSO com a Resource Central, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
    1. **[Create Resource Central test user](#create-resource-central-test-user)** - para ter uma contraparte de B.Simon in Resource Central que está ligada à representação AD AD do utilizador.
1. **[Configure o SSO Central de Recursos -](#configure-resource-central-sso)** para configurar as definições de inscrição única no lado da aplicação.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração de aplicações **Resource Central,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na **Configuração Básica SAML,** insira os valores para os seguintes campos:

   1. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<DOMAIN_NAME>/ResourceCentral`

   1. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão:  `https://<DOMAIN_NAME>/ResourceCentral`

   1. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<DOMAIN_NAME>/ResourceCentral/ExAuth/Saml2Authentication/Acs`

    > [!NOTE]
    > Estes valores não são valores literais. Atualize estes valores com os valores reais de URL, Identifier e Response URL. Contacte [a equipa de suporte do Cliente Central de Recursos](mailto:st@aod.vn) para obter estes valores.  Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. No **set up single-on com** a página SAML, no **Certificado de Assinatura SAML**, encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na **Configuração Central de Recursos,** copie os URL(s) apropriados com base no seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste chamado B.Simon no portal Azure.

1. A partir do painel esquerdo no portal Azure, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome do utilizador,** insira o `username@companydomain.extension` . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à Resource Central.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Resource Central**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no painel **de atribuição de adicionar.**
1. No painel **de Utilizadores e grupos,** selecione **B.Simon** da lista **de Utilizadores** e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la em **Selecione uma função**. Se não tiver sido configurada qualquer função para esta aplicação, vê a função **De Acesso Predefinido** selecionada.
1. No painel **'Adicionar Atribuição',** clique no botão **Atribuir.**

### <a name="create-resource-central-test-user"></a>Criar utilizador de teste Central de Recursos

Nesta secção, um utilizador chamado **B.Simon** é criado na **Resource Central**.

1. Na Central de Recursos, selecione  >  **Security Persons**  >  **New**.
  
    :::image type="content" source="./media/resource-central/new-person.png" alt-text="Screenshot que mostra o painel de Pessoas na Central de Recursos, com o novo botão realçado.":::

1. Em **Detalhes pessoais**, para **o nome display**, insira o utilizador **B.Simon**. Para **o endereço SMTP,** insira o nome de utilizador Azure AD do utilizador. Por exemplo, `B.Simon@contoso.com`.

    :::image type="content" source="./media/resource-central/person.png" alt-text="Screenshot que mostra o painel de detalhes da pessoa na Central de Recursos.":::

## <a name="configure-resource-central-sso"></a>Configurar recursos central SSO

Nesta secção, irá configurar um único sinal de inscrição no **Administrador do Sistema Central de Recursos.**

1. No Administrador do Sistema Central de Recursos, selecione **Autenticação Externa.**
1.  Para **ativar a configuração**, selecione **Sim**.

    ![Screenshot que mostra a opção de Configuração ativa selecionada no painel de autenticação externa no Centro de Recursos.](./media/resource-central/enable.png)

1. No **Protocolo de Autenticação**, selecione **SAML2**. 

   :::image type="content" source="./media/resource-central/protocol.png" alt-text="Screenshot que mostra SAML2 selecionado para Protocolo de Autenticação em Resource Central.":::

1. Na **Configuração SAML2,** insira os valores para os seguintes campos:

    1. Para **identificador (ID de entidade)**, **URL de login,** URL de **logot,** e **identificador AD Azure,** insira os URLs relevantes:

       :::image type="content" source="./media/resource-central/auth.png" alt-text="Screenshot do painel de configuração SAML2 na Central de Recursos.":::

        Copie os URLs do painel **De Recursos Central de Configuração:**

        :::image type="content" source="./media/resource-central/setup.png" alt-text="Screenshot do painel De recursos Central de Configuração na Central de Recursos.":::

   1. Para **URL de retorno,** insira `https://<DOMAIN_NAME>/ResourceCentral/ExAuth/Saml2Authentication/CallbackHandler` .
  
1. Para **Certificado,** faça upload do seu certificado e introduza a sua senha.

   ![Screenshot da secção de certificados na Central de Recursos.](./media/resource-central/cert.png)
   
1. Selecione **Guardar**.

1. Regresse ao **portal do Azure**. No **Certificado de Assinatura SAML,** faça upload do seu certificado e insira a sua senha.

   ![Screenshot do painel de certificados de importação no portal Azure.](./media/resource-central/cert2.png).

1. Selecione **Adicionar**.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure. Para testar um único sinal, tem três opções:

* No portal Azure, selecione **Testar esta aplicação**. O link redireciona para o URL de sinal de início de sessão, onde pode iniciar o login.

* Aceda diretamente ao URL de acesso ao início de Sessão da Central de Recursos e inicie o login.

   :::image type="content" source="./media/resource-central/test.png" alt-text="Screenshot da página de teste de teste de síptica central de recurso.":::

* Utilize o portal My Apps da Microsoft. No portal My Apps, selecione o azulejo **Central de Recursos** para redirecionar para o URL de sinal de sign-on central de recursos. Para obter mais informações, consulte [iniciar sômis e inicie aplicações a partir do portal My Apps.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Depois de configurar a Resource Central para um único sign-on com a Azure AD, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
