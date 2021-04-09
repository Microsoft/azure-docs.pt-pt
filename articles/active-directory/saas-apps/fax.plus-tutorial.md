---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com FAX. MAIS | Microsoft Docs'
description: Saiba como configurar um único sinal de inscrição entre o Azure Ative Directory e o FAX. E MAIS.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/03/2021
ms.author: jeedes
ms.openlocfilehash: 528e1056574379f922b5de15f442b7fd92d8cf8c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104592460"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-faxplus"></a>Tutorial: Azure Ative Directory integração única de sign-on (SSO) com FAX. MAIS

Neste tutorial, aprenderá a integrar o FAX. PLUS com Azure Ative Directory (Azure AD). Quando integra o FAX. Mais com Azure AD, você pode:

* Controlo em Azure AD que tem acesso a FAX. E MAIS.
* Ativar os seus utilizadores para serem automaticamente inscritos no FAX. Mais com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* UM FAX. A subscrição ativada por um único sinal de SSO (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* UM FAX. A PLUS apoia **o SP e o IDP** iniciado sSO.
* UM FAX. O PLUS suporta o fornecimento do utilizador **Just In Time.**

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.

## <a name="add-faxplus-from-the-gallery"></a>Adicione FAX. MAIS da galeria

Para configurar a integração do FAX. ALÉM do Azure AD, tem de adicionar FAX. MAIS da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** escreva **FAX. Mais** na caixa de pesquisa.
1. Selecione **FAX. MAIS** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-faxplus"></a>Configure e teste Azure AD SSO para FAX. MAIS

Configure e teste Azure AD SSO com FAX. PLUS usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em FAX. E MAIS.

Para configurar e testar Azure AD SSO com FAX. PLUS, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure FAX. PLUS SSO](#configure-faxplus-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Criar FAX. PLUS test user](#create-faxplus-test-user)** - para ter uma contraparte de B.Simon em FAX. PLUS que está ligado à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, no **FAX. Página de** integração de aplicações PLUS, encontre a secção **Gerir** e selecione um único sinal **de sôs-in**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **De Configuração Básica SAML,** o utilizador não tem de realizar qualquer passo, uma vez que a aplicação já está pré-integrada com o Azure.

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite o URL:  `https://www.fax.plus/login`

1. Clique em **Guardar**.

1. UM FAX. A aplicação PLUS espera as afirmações SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de ficha SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, FAX. A aplicação PLUS espera que alguns mais atributos sejam repercutidos na resposta SAML, que são mostrados abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.
    
    | Name | Atributo de origem|
    | --------------- | --------- |
    | nome de primeiro nome  | user.givenname |
    | último nome   | utilizador.sobrenome   |

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. No **set Up FAX. Secção PLUS,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a FAX. E MAIS.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **FAX. MAIS.**
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função &quot;Acesso Predefinido&quot; selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name=&quot;configure-faxplus-sso&quot;></a>Configure FAX. MAIS SSO

1. Faça login no seu FAX. Site da empresa PLUS como administrador.

2. Vá à secção **de Segurança** no seu Perfil de Administração e desloque-se até **Advanced**.

3. No painel **de configuração,** clique no botão Iniciar O **Sinal Único** e execute os seguintes passos.
    
    ![Conta](./media/fax.plus-tutorial/configuration.png &quot;Conta") 

    a. Na caixa de texto ID da **Entidade,** cole o valor **identificador Azure AD** que copiou do portal Azure.

    b. Na caixa de texto **URL de Sign-On única,** cole o valor URL de **login** que copiou a partir do portal Azure.

    c. Abra o Certificado descarregado **(Base64)** do portal Azure para o Bloco de Notas e cole o conteúdo na caixa de texto **do Certificado X.509.**

    d. Se pretender iniciar sessão através do SSO, ative **apenas o SSO Login para a** caixa de verificação do Utilizador Admin. 

    e. Clique em **Confirmar**.

### <a name="create-faxplus-test-user"></a>Criar FAX. Utilizador de teste PLUS

Nesta secção, um utilizador chamado Britta Simon é criado em FAX. E MAIS. UM FAX. O PLUS suporta o provisionamento do utilizador just-in-time, o que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no FAX. PLUS, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para FAX. SINAL PLUS no URL onde pode iniciar o fluxo de login.  

* Vai para o FAX. PLUS Iniciar o url de login diretamente e iniciar o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito no FAX. Mais para o qual criou o SSO. 

Também pode utilizar o Microsoft My Apps para testar a aplicação em qualquer modo. Quando clicar no FAX. O azulejo PLUS nas Minhas Apps, se configurado no modo SP, será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito no FAX. Mais para o qual criou o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurar o FAX. ALÉM disso, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).