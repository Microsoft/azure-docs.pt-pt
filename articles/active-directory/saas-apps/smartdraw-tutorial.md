---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com o SmartDraw Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o SmartDraw.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/02/2020
ms.author: jeedes
ms.openlocfilehash: b4b9aa16dfc474575bd522238f06fe487508e7e0
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92515853"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-smartdraw"></a>Tutorial: Azure Ative Directory integração única (SSO) com o SmartDraw

Neste tutorial, você vai aprender a integrar o SmartDraw com o Azure Ative Directory (Azure AD). Quando integrar o SmartDraw com a AD Azure, pode:

* Controlo em Azure AD que tem acesso ao SmartDraw.
* Ative os seus utilizadores a serem automaticamente inscritos no SmartDraw com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Assinatura ativada por SmartDraw (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* SmartDraw suporta **SP e IDP** iniciado SSO
* SmartDraw suporta **provisão do** utilizador Just In Time

## <a name="adding-smartdraw-from-the-gallery"></a>Adicionando SmartDraw da galeria

Para configurar a integração do SmartDraw no AD Azure, precisa de adicionar o SmartDraw da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar da secção **de galeria,** **digite SmartDraw** na caixa de pesquisa.
1. Selecione **SmartDraw** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-smartdraw"></a>Configurar e testar Azure AD único sinal para SmartDraw

Configure e teste Azure AD SSO com SmartDraw usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no SmartDraw.

Para configurar e testar o Azure AD SSO com o SmartDraw, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    * **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure smartDraw SSO](#configure-smartdraw-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    * **[Create SmartDraw test user](#create-smartdraw-test-user)** - para ter uma contraparte de B.Simon em SmartDraw que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **SmartDraw,** encontre a secção **Gerir** e selecione **um único sinal de sposição**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **De Configuração Básica SAML,** o utilizador não tem de realizar qualquer passo, uma vez que a aplicação já está pré-integrada com o Azure.

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://cloud.smartdraw.com/sso/saml/login/<domain>`

    > [!NOTE]
    > O valor url de inscrição não é real. Irá atualizar o valor URL de acesso com o URL de inscrição real, que é explicado mais tarde no tutorial. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Clique em **Guardar**.

1. A aplicação SmartDraw espera as afirmações DO SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação SmartDraw espera que alguns mais atributos sejam repercutidos na resposta SAML que são mostrados abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.

    | Nome | Atributo de origem|
    | ---------------| --------------- |
    | FirstName | user.givenname |
    | LastName | utilizador.sobrenome |
    | E-mail | user.mail |
    | Grupos | utilizador.grupos |

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregue** para descarregar o certificado e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção **Configurar SmartDraw,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao SmartDraw.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **SmartDraw**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-smartdraw-sso"></a>Configurar smartDraw SSO

1. Para automatizar a configuração dentro do SmartDraw, é necessário instalar a extensão do **navegador 'As aplicações' Secure Sign-in** clicando **em instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

1. Depois de adicionar extensão ao navegador, clique em **Configurar o SmartDraw** irá direcioná-lo para a aplicação SmartDraw. A partir daí, forneça as credenciais de administração para assinar no SmartDraw. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-5.

    ![Configuração de configuração](common/setup-sso.png)

1. Se pretender configurar o SmartDraw manualmente, abra uma nova janela do navegador web e inscreva-se no site da empresa SmartDraw como administrador e execute os seguintes passos:

1. Clique em **'Sign-On'** único em Gerir a sua licença SmartDraw.

    ![A screenshot mostra a caixa de diálogo da licença SmartDraw, onde pode selecionar O Sign-On Único.](./media/smartdraw-tutorial/configure01.png)

1. Na página configuração, execute os seguintes passos:

    ![A screenshot mostra a página de Configuração onde pode introduzir os valores descritos.](./media/smartdraw-tutorial/configure02.png)

    a. Na caixa de texto **Do Seu Domínio (como acme.com),** digite o seu domínio.

    b. Copie o **url de login iniciado sp será** para o seu exemplo e colá-lo na caixa de texto URL de entrada em **Configuração BÁSICA SAML** no portal Azure.

    c. Nos Grupos de Segurança para permitir a caixa de texto **smartDraw Access,** **escreva Todos**.

    d. Na sua caixa de texto **url emitente SAML,** cole o valor do **Identificador AD AZure** que copiou do portal Azure.

    e. No Bloco de Notas, abra o ficheiro Metadados XML que descarregou a partir do portal Azure, copie o seu conteúdo e, em seguida, cole-o na caixa **de MetaData SAML.**

    f. Clique **em Guardar Configuração** 

### <a name="create-smartdraw-test-user"></a>Criar utilizador de teste SmartDraw

Nesta secção, um utilizador chamado B.Simon é criado no SmartDraw. O SmartDraw suporta o fornecimento de utilizadores just-in-time, o que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no SmartDraw, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo SmartDraw no Painel de Acesso, deverá ser automaticamente inscrito no SmartDraw para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente SmartDraw com Azure AD](https://aad.portal.azure.com/)