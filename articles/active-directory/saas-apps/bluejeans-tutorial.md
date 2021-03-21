---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com BlueJeans para Azure AD | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o BlueJeans para a Azure AD.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/09/2020
ms.author: jeedes
ms.openlocfilehash: a920abacb0df030f7d38aed11e04a693cfa352a6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101686602"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-bluejeans-for-azure-ad"></a>Tutorial: Azure Ative Directory integração única de sign-on (SSO) com BlueJeans para Azure AD

Neste tutorial, você vai aprender a integrar BlueJeans para Azure AD com Azure Ative Directory (Azure AD). Quando integrar blueJeans para Azure AD com Azure AD, você pode:

* Controle em Azure AD que tem acesso a BlueJeans para Azure AD.
* Ative os seus utilizadores a serem automaticamente inscritos na BlueJeans para Azure AD com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* BlueJeans for Azure AD single sign-on (SSO) enabled subscription.

> [!NOTE]
> Esta integração também está disponível para usar a partir do ambiente cloud do governo dos EUA Azure AD. Você pode encontrar esta aplicação na Azure AD US Government Cloud Application Gallery e configurá-la da mesma forma que você faz a partir de nuvem pública.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* BlueJeans para Azure AD suporta **SP** iniciado SSO

* BlueJeans for Azure AD suporta fornecimento [ **automatizado** de utilizadores](bluejeans-provisioning-tutorial.md)

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.

## <a name="adding-bluejeans-for-azure-ad-from-the-gallery"></a>Adicionando BlueJeans para Azure AD da galeria

Para configurar a integração de BlueJeans para Azure AD em Azure AD, você precisa adicionar BlueJeans para AZure AD da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Add a partir da secção **da galeria,** digite **BlueJeans para Azure AD** na caixa de pesquisa.
1. Selecione **BlueJeans para Azure AD** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on-for-bluejeans-for-azure-ad"></a>Configurar e testar Azure AD único sign-on para BlueJeans para Azure AD

Configure e teste Azure AD SSO com BlueJeans para AZure AD usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado em BlueJeans para Azure AD.

Para configurar e testar O SSO Azure AD com BlueJeans para Azure AD, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure BlueJeans para Azure AD SSO](#configure-bluejeans-for-azure-ad-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Crie BlueJeans para o utilizador de teste Azure AD](#create-bluejeans-for-azure-ad-test-user)** - para ter uma contraparte de B.Simon em BlueJeans para Azure AD que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página **BlueJeans for Azure AD,** encontre a secção **Gerir** e selecione **um único sinal**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão: `https://<companyname>.bluejeans.com`

    a. Na caixa de texto **identifier (Entity ID),** digite um URL: `http://samlsp.bluejeans.com`

    a. Na caixa de texto **URL de resposta,** digite um URL: `https://bluejeans.com/sso/saml2/`

    > [!NOTE]
    > O valor de URL Sign-On não é real. Atualize o valor com o URL de Sign-On real. Contacte [a Equipa de Apoio ao Cliente Azure AD para](https://support.bluejeans.com/contact) obter o valor. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. A aplicação BlueJeans espera as afirmações SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação BlueJeans espera que alguns mais atributos sejam repercutidos na resposta SAML que são mostrados abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.

    | Name |  Atributo de origem|
    | ---------| --------- |
    | Telefone | user.telephonenumber |
    | título | user.jobtitle |

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **Configurar BlueJeans para Azure AD,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a BlueJeans para Azure AD.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **BlueJeans para Azure AD**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-bluejeans-for-azure-ad-sso"></a>Configure BlueJeans para Azure AD SSO

1. Numa janela diferente do navegador web, inscreva-se no seu BlueJeans para o site da empresa **Azure AD** como administrador.

2. Vá para **ADMIN \> DEFINIÇÕES \> DE SEGURANÇA**.

    ![O screenshot mostra parte de uma janela do navegador com o separador Admin selecionado, com definição de grupo e segurança selecionadas.](./media/bluejeans-tutorial/ic785868.png "Administrador")

3. Na secção **SEGURANÇA,** execute os seguintes passos:

    ![Sinal único de SAML em](./media/bluejeans-tutorial/ic785869.png "Sinal único de SAML em")

    a. Selecione **UM único sinal DE SAML .**

    b. **Selecione Ative o provisioning automático**.

4. Siga em frente com os seguintes passos:

    ![Percurso do Certificado](./media/bluejeans-tutorial/ic785870.png "Percurso do Certificado")

    a. Clique **em Escolher Ficheiro**, para carregar o certificado codificado base-64 que descarregou a partir do portal Azure.

    b. Na caixa de texto **url de login,** cole o valor do URL de **login** que copiou do portal Azure.

    c. Na caixa de texto URL de alteração de **palavra-passe,** cole o valor do URL de alteração de **palavra-passe** que copiou do portal Azure.

    d. Na caixa de texto **url logout,** cole o valor do **URL logout** que copiou do portal Azure.

5. Siga em frente com os seguintes passos:

    ![Guardar alterações](./media/bluejeans-tutorial/ic785874.png "Guardar alterações")

    a. Na caixa de texto do **Utilizador,** escreva `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` .

    b. Na caixa de texto **por e-mail,** escreva `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` .

    c. CLIQUE **EM GUARDAR ALTERAÇÕES.**

### <a name="create-bluejeans-for-azure-ad-test-user"></a>Criar BlueJeans para utilizador de teste AZure AD

O objetivo desta secção é criar um utilizador chamado B.Simon em BlueJeans para Azure AD. BlueJeans for Azure AD suporta o fornecimento automático do utilizador, que é por padrão ativado. Pode encontrar mais detalhes [aqui](bluejeans-provisioning-tutorial.md) sobre como configurar o fornecimento automático do utilizador.

**Se precisar de criar o utilizador manualmente, execute os seguintes passos:**

1. Inscreva-se no seu Site da empresa **AD Azure como** administrador.

2. VÁ ao **ADMIN \> GERIR UTILIZADORES \> ADICIONAR UTILIZADOR**.

    ![O Screenshot mostra parte de uma janela do navegador com o separador Admin selecionado, com utilizadores de gestão e utilizadores de adicionar selecionados.](./media/bluejeans-tutorial/ic785877.png "Administrador")

    > [!IMPORTANT]
    > O **separador UTILIZADOR ADD** só está disponível se, no **separador SECUTIRY,** permitir que o **provisionamento automático** não seja verificado.

3. Na secção **ADD USER,** execute os seguintes passos:

    ![A screenshot mostra a secção de utilizador Adicionar onde introduz as informações descritas neste passo.](./media/bluejeans-tutorial/ic785886.png "Adicionar Utilizador")

    a. Na caixa de texto **First Name,** insira o primeiro nome do utilizador como **B**.

    b. Na caixa de texto **Last Name,** insira o último nome do utilizador como **Simon**.

    c. Em **Pick a BlueJeans for Azure AD Username** box, insira o nome de utilizador de um utilizador como **Brittasimon**

    d. Na Criação de uma caixa de texto **de palavra-passe,** insira a sua palavra-passe.

    e. Na caixa de texto **da Empresa,** insira a sua Empresa.

    f. Na caixa de texto **do Endereço de E-mail,** insira o e-mail do utilizador como `b.simon@contoso.com` .

    exemplo, In **Create a BlueJeans for Azure AD Meeting ID,** insira o seu ID de reunião.

    h. Na caixa de texto **'Escolha' 'Escolha' 'Código de Acesso' moderador,** introduza a sua senha.

    i. Clique **em CONTINUAR**.

    ![A screenshot mostra a secção de utilizador Adicionar onde pode visualizar as definições e funcionalidades, com o botão Adicionar Utilizador selecionado.](./media/bluejeans-tutorial/ic785887.png "Adicionar Utilizador")

    J. Clique **em ADD USER**.

> [!NOTE]
> Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador Azure AD ou APIs fornecidas pela BlueJeans para a Ad Azure para fornecer contas de utilizador Azure AD.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar nos BlueJeans para azulejo Azure AD no Painel de Acesso, deverá ser automaticamente inscrito nos BlueJeans para Azure AD para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente BlueJeans para Azure AD com Azure AD](https://aad.portal.azure.com/)