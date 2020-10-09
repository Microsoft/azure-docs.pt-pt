---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com blogIn Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o BlogIn.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/21/2020
ms.author: jeedes
ms.openlocfilehash: 2a50d7b037cec2c10f83fdbbd875f80513c00a6d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88517073"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-blogin"></a>Tutorial: Azure Ative Directory integração única (SSO) com blogIn

Neste tutorial, você vai aprender a integrar blogIn com Azure Ative Directory (Azure AD). Quando integra o BlogIn com AZure AD, pode:

* Controle em Azure AD que tem acesso ao BlogIn.
* Capacitar os seus utilizadores a serem automaticamente inscritos no BlogIn com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* BlogIn assinatura única (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* BlogIn suporta **SP e IDP** iniciado SSO
* BlogIn suporta **o fornecimento de utilizadores just in time**
* Uma vez configurado o BlogIn, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-blogin-from-the-gallery"></a>Adicionar BlogIn da galeria

Para configurar a integração do BlogIn no AD Azure, precisa adicionar o BlogIn da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **escreva BlogIn** na caixa de pesquisa.
1. Selecione **BlogIn** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-blogin"></a>Configure e teste Azure AD SSO para blogIn

Configure e teste Azure AD SSO com blogIn usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no BlogIn.

Para configurar e testar o Azure AD SSO com o BlogIn, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure blogIn SSO](#configure-blogin-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create BlogIn test user](#create-blogin-test-user)** - para ter uma contraparte de B.Simon no BlogIn que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **BlogIn,** encontre a secção **Gerir** e selecione **um único sinal de sing.**
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://<SUBDOMAIN>.blogin.co/`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<SUBDOMAIN>.blogin.co/sso/saml/callback`

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se quiser configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<SUBDOMAIN>.blogin.co/`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL de inscrição. Pode obter os valores exatos para estes campos na página **Definições** no BlogIn (separador de**visão** do utilizador > **Configurar SSO e Provisioning do Utilizador).** Em alternativa, pode contactar a [equipa de suporte do Cliente BlogIn](mailto:support@blogin.co) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. A aplicação BlogIn espera as afirmações DO SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação BlogIn espera que alguns mais atributos sejam repercutidos na resposta SAML que são mostrados abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.
    
    | Nome | Atributo de origem |
    | ------ | --------- |
    | título |user.jobtitle |
    

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** clique no botão de cópia para copiar o Url de **metadados da Federação de Aplicações** e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/copy-metadataurl.png)

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao BlogIn.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **BlogIn**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-blogin-sso"></a>Configure BlogIn SSO

Para configurar o login único no **BlogIn** na sua conta BlogIn e siga estes passos:

1. Ir para **Definições**  >  **Configurações Configurações Configuração Configuração**  >  **Configuração Configuração SSO & fornecimento do utilizador**.
2. No ecrã seguinte, altere o estado de Sign-On único para **On** e escolha um nome personalizado para o botão de login SSO que será exibido no ecrã de login.

3. Se guardou o Url de **Metadados da Federação** de Aplicações no último passo da secção anterior, escolha o método de configuração **MEtadados URL** e cole **o Url de metadados da Federação de Aplicações** no campo URL dos metadados. Caso contrário, altere o método de Configuração para **manual,** povoe manualmente **o URL SSO (URL do Fornecedor de Início de Sessão)** e o Emissor de Fornecedor de Identidade **(ID da entidade)** e faça o upload do **Certificado (base64)**   que obteve do Azure AD.

4. Escolha a função de utilizador predefinido para novos utilizadores que se juntem ao BlogIn utilizando sSO.

5. Selecione **Guardar alterações**.

Para obter uma explicação mais detalhada da criação do SSO no BlogIn, consulte [Como configurar o SSO para o Microsoft Azure AD no BlogIn](https://blogin.co/blog/how-to-set-up-single-sign-on-sso-for-microsoft-azure-active-directory-azure-ad-267/). Sinta-se à vontade para contactar a [equipa de suporte do BlogIn](mailto:support@blogin.co) a qualquer momento se tiver alguma dúvida ou precisar de ajuda.

### <a name="create-blogin-test-user"></a>Criar utilizador de teste BlogIn

Nesta secção, um utilizador chamado B.Simon é criado no BlogIn. O BlogIn suporta o fornecimento de utilizadores just-in-time, o que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no BlogIn, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo BlogIn no Painel de Acesso, deverá ser automaticamente inscrito no BlogIn para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente blogIn com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger o BlogIn com visibilidade e controlos avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
