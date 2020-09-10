---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com askSpoke / Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e pergunte Aspoke.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/20/2020
ms.author: jeedes
ms.openlocfilehash: 64a95a14f6832e3f109335b5525b9a5dc1a9ec27
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89662277"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-askspoke"></a>Tutorial: Azure Ative Directory integração única (SSO) com askSpoke

Neste tutorial, você vai aprender a integrar askSpoke com Azure Ative Directory (Azure AD). Quando integrar o askSpoke com Azure AD, pode:

* Controle em Azure AD que tem acesso a AskSpoke.
* Permita que os seus utilizadores sejam automaticamente inscritos para solicitar aD com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* solicita a assinatura ativada (SSO) por subscrição única do Spoke.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* askSpoke apoia **SP e IDP** iniciado SSO
* solicitaSpoke suporta **provisão de** utilizadores Just In Time
* Uma vez configurado askSpoke pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.

## <a name="adding-askspoke-from-the-gallery"></a>Adicionar askSpoke da galeria

Para configurar a integração do askSpoke no Azure AD, precisa adicionar askSpoke da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **escreva para pedir Adpoke** na caixa de pesquisa.
1. Selecione **askSpoke** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-askspoke"></a>Configure e teste Azure AD SSO para pedir SSpoke

Configure e teste Azure AD SSO com askSpoke usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no askSpoke.

Para configurar e testar a Azure AD SSO com askSpoke, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure pedir SSO](#configure-askspoke-sso)** para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create askSpoke test user](#create-askspoke-test-user)** - para ter uma contraparte de B.Simon em askSpoke que está ligada à representação AZure AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **askSpoke,** encontre a secção **Gerir** e selecione um único sinal de **sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:  `https://<SUBDOMAIN>.askspoke.com/saml/callback`

    > [!NOTE]
    > O valor URL de resposta não é real. Atualize este valor com o URL de resposta real. O contacto pede à equipa de suporte do [Cliente DaP](mailto:support@askspoke.com) para obter este valor. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite o URL:  `https://askspoke.com/login`

1. askSpoke espera as afirmações DE SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação askSpoke espera que alguns mais atributos sejam repercutidos na resposta SAML que são mostrados abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.

    | Name |  Atributo de origem|
    | ---------------| --------- |
    | nomePróprio | user.givenname |
    | apelido | utilizador.sobrenome |

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **Configuração askSpoke,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a askSpoke.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **askSpoke**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-askspoke-sso"></a>Configure askSpoke SSO

1. Numa janela diferente do navegador web, inscreva-se no site askSpoke como administrador.

1. Clique no **separador Definições** a partir do painel de navegação esquerdo.

    ![pedir separador de definições de Spoke](./media/askspoke-tutorial/configure1.png)

1. Desloque-se para **SSO** e clique no **Connect**.

    ![pedir Ligação DeSpoke](./media/askspoke-tutorial/configure2.png)

1. Na secção **Enable SAML & SCIM,** execute os seguintes passos:

    ![perguntaSpoke Enable SAML & secção SCIM](./media/askspoke-tutorial/configure3.png)

    1. Na caixa de texto **url de acesso,** cole o valor URL do **Login,** que copiou a partir do portal Azure.

    1. Na caixa de texto **emitente,** cole o valor **do identificador Azure AD,** que copiou a partir do portal Azure.

    1. Abra o ficheiro Certificado descarregado **(Base64)** do portal Azure para o Bloco de Notas e copie o conteúdo do ficheiro de certificado e cole-o na caixa de texto do **certificado Público.**

    1. Copie o valor **do URL ACS** e cole-o na caixa de texto **URL de resposta** na secção **configuração BÁSICA SAML** no portal Azure.

    1. Clique na **ligação SAML de teste**.

### <a name="create-askspoke-test-user"></a>Criar o utilizador de teste AskSpoke

Nesta secção, um utilizador chamado B.Simon é criado no askSpoke. solicitaÇão suporta provisão just-in-time, que é ativada por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no askSpoke, um novo é criado quando tenta aceder a askSpoke.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo askSpoke no Painel de Acesso, deverá ser automaticamente inscrito no askSpoke para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente askSpoke com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)