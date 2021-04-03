---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com Coggle | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Coggle.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/30/2020
ms.author: jeedes
ms.openlocfilehash: 0aab45d50b9864457adce3cf77d2b210d0c363d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92455617"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-coggle"></a>Tutorial: Azure Ative Directy integração única (SSO) com Coggle

Neste tutorial, você vai aprender a integrar Coggle com Azure Ative Directory (Azure AD). Quando integrar o Coggle com a AD Azure, pode:

* Controlo em Azure AD que tem acesso a Coggle.
* Ative os seus utilizadores a serem automaticamente inscritos no Coggle com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Coggle única inscrição (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Coggle apoia **SP e IDP** iniciado SSO
* Coggle suporta **provisão do** utilizador Just In Time

* Uma vez configurado Coggle, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-coggle-from-the-gallery"></a>Adicionando Coggle da galeria

Para configurar a integração do Coggle no Azure AD, precisa adicionar Coggle da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** escreva **Coggle** na caixa de pesquisa.
1. Selecione **Coggle** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-coggle"></a>Configure e teste Azure AD SSO para Coggle

Configure e teste Azure AD SSO com Coggle usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Coggle.

Para configurar e testar o Azure AD SSO com Coggle, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o Coggle SSO](#configure-coggle-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Coggle test user](#create-coggle-test-user)** - para ter uma contraparte de B.Simon em Coggle que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Coggle,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **De Configuração Básica SAML,** o utilizador não tem de realizar qualquer passo, uma vez que a aplicação já está pré-integrada com o Azure.

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://coggle.it/<TENANT_NAME>/login`

    > [!NOTE]
    > O valor não é real. Atualize o valor com o URL de inscrição real. Contacte [a equipa de suporte do Cliente Coggle](mailto:hello@Coggle.it) para obter o valor. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Clique em **Guardar**.

1. A aplicação Coggle espera as afirmações SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação Coggle espera que alguns mais atributos sejam repercutidos na resposta SAML, que são mostrados abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.
    
    | Name | Atributo de origem|
    | ---------------| --------- |
    | nomePróprio | user.givenname |
    | apelido | utilizador.sobrenome |
    | e-mail | user.mail |

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **Configurar Coggle,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a Coggle.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Coggle**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-coggle-sso"></a>Configurar Coggle SsO

1. Numa janela de navegador diferente, inscreva-se no site da empresa Coggle como administrador.

2. Clique na conta **Coggle** e selecione **As Definições**.

    ![A screenshot mostra o site da empresa Coggle com as minhas definições selecionadas.](./media/Coggle-tutorial/configure1.png)

3. Clique no seguinte **logotipo** e selecione **Autenticação.**

    ![A screenshot mostra um ícone de baleia e autenticação selecionada.](./media/Coggle-tutorial/configure2.png)

4. Clique em **Editar SAML Config.**

    ![A screenshot mostra a página de Integração SAML com a opção Edit SAML Config.](./media/Coggle-tutorial/configure3.png)

5. Na página de diálogo de **integração SAML,** execute os seguintes passos:

    ![A screenshot mostra a página de Integração SAML onde pode introduzir as informações neste passo.](./media/Coggle-tutorial/configure4.png)

    a. Na caixa de texto **'Entrada' (ID Provider SSO URL),** cole o valor URL de **login,** que copiou a partir do portal Azure.

    b. Abra o Certificado descarregado **(Base64)** do portal Azure para o Bloco de Notas e cole o conteúdo na caixa de texto **do Certificado.**

    c. Clique em **Guardar**.

### <a name="create-coggle-test-user"></a>Criar utilizador de teste Coggle

Nesta secção, um utilizador chamado B.Simon é criado em Coggle. O Coggle suporta o provisionamento do utilizador just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no Coggle, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Coggle no Painel de Acesso, deverá ser automaticamente inscrito no Coggle para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente Coggle com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Como proteger Coggle com visibilidade e controlos avançados](/cloud-app-security/proxy-intro-aad)