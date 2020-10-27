---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com Velpic SAML Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o Velpic SAML.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/01/2019
ms.author: jeedes
ms.openlocfilehash: 2916c4c849a602b906a8992b290e3f0887483290
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92517740"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-velpic-saml"></a>Tutorial: Azure Ative Directory integração única (SSO) com Velpic SAML

Neste tutorial, você vai aprender a integrar Velpic SAML com Azure Ative Directory (Azure AD). Quando integrar o Velpic SAML com a Azure AD, pode:

* Controlo em Azure AD que tem acesso a Velpic SAML.
* Permita que os seus utilizadores sejam automaticamente inscritos na Velpic SAML com as suas contas AD AZure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Assinatura ativada por SAML (SSO) ativada pela Velpic SAML.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Velpic SAML suporta **SSO** iniciado SP

## <a name="adding-velpic-saml-from-the-gallery"></a>Adicionando Velpic SAML da galeria

Para configurar a integração da Velpic SAML em AZure AD, é necessário adicionar a Velpic SAML da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações** .
1. Para adicionar nova aplicação, selecione **Nova aplicação** .
1. Na secção Adicionar da secção **da galeria,** digite **Velpic SAML** na caixa de pesquisa.
1. Selecione **Velpic SAML** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.    

## <a name="configure-and-test-azure-ad-single-sign-on-for-velpic-saml"></a>Configurar e testar Azure AD único sinal para Velpic SAML

Configure e teste Azure AD SSO com Velpic SAML usando um utilizador de teste chamado **B.Simon** . Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado na Velpic SAML.

Para configurar e testar o Azure AD SSO com a Velpic SAML, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o Velpic SAML SSO](#configure-velpic-saml-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Velpic SAML test user](#create-velpic-saml-test-user)** - para ter uma contraparte de B.Simon em Velpic SAML que está ligada à representação AD AD Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Velpic SAML,** encontre a secção **Gerir** e selecione **um único sinal de sing.**
1. Na página de método **de inscrição** única, selecione **SAML** .
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<sub-domain>.velpicsaml.net`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://auth.velpic.com/saml/v2/<entity-id>/login`

    > [!NOTE]
    > Por favor, note que o Sinal no URL será fornecido pela equipa Velpic SAML e o valor do Identificador estará disponível quando configurar o Plugin SSO no lado Velpic SAML. Você precisa copiar esse valor a partir da página de aplicação Velpic SAML e colá-lo aqui.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregue** para descarregar o certificado e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção **Configurar Velpic SAML,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a Velpic SAML.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações** .
1. Na lista de candidaturas, selecione **Velpic SAML** .
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos** .

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-velpic-saml-sso"></a>Configurar Velpic SAML SSO

1. Para automatizar a configuração dentro do Velpic SAML, é necessário instalar a extensão do **navegador 'As aplicações' Secure Sign-in** clicando **em instalar a extensão** .

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique em **Configurar a Velpic SAML** irá direcioná-lo para a aplicação Velpic SAML. A partir daí, forneça as credenciais de administração para assinar na Velpic SAML. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-8.

    ![Configuração de configuração](common/setup-sso.png)

3. Se pretender configurar manualmente a Velpic SAML, abra uma nova janela do navegador web e inscreva-se no site da empresa Velpic SAML como administrador e execute os seguintes passos:

4. Clique no **separador Gerir** e vá à secção **de Integração** onde precisa clicar no botão **Plugins** para criar um novo plugin para iniciar sessão.

    ![Plugin](./media/velpicsaml-tutorial/velpic_1.png)

5. Clique no botão **'Adicionar plugin'.**
    
    ![Plugin](./media/velpicsaml-tutorial/velpic_2.png)

6. Clique no azulejo **SAML** na página Add Plugin.
    
    ![Plugin](./media/velpicsaml-tutorial/velpic_3.png)

7. Introduza o nome do novo plugin SAML e clique no botão **'Adicionar'.**

    ![Plugin](./media/velpicsaml-tutorial/velpic_4.png)

8. Insira os detalhes da seguinte forma:

    ![Plugin](./media/velpicsaml-tutorial/velpic_5.png)

    a. Na caixa de texto **Name,** digite o nome do plugin SAML.

    b. Na caixa de texto URL do **emitente,** cole o **identificador AD AD Azure** que copiou a partir da janela **de inscrição** de configuração do portal Azure.

    c. No **Fornecedor De Metadados Config** carregumo o ficheiro Metadados XML que descarregou a partir do portal Azure.

    d. Também pode optar por ativar o SAML mesmo no tempo, permitindo que a caixa de verificação **"Auto crie novos utilizadores".** Se um utilizador não existir em Velpic e esta bandeira não estiver ativada, o login do Azure falhará. Se a bandeira estiver ativa, o utilizador será automaticamente a provisionado em Velpic no momento do início de sessão. 

    e. Copie o **sinal único no URL** a partir da caixa de texto e cole-o no portal Azure.
    
    f. Clique em **Guardar** .

### <a name="create-velpic-saml-test-user"></a>Criar utilizador de teste Velpic SAML

Este passo não é geralmente necessário, uma vez que a aplicação suporta apenas a tempo de fornecimento do utilizador. Se o fornecimento automático do utilizador não estiver ativado, a criação manual do utilizador pode ser feita conforme descrito abaixo.

Inscreva-se no site da sua empresa Velpic SAML como administrador e execute os seguintes passos:
    
1. Clique no separador Gerir e ir à secção dos Utilizadores e, em seguida, clique em Novo botão para adicionar utilizadores.

    ![adicionar utilizador](./media/velpicsaml-tutorial/velpic_7.png)

2. Na página de diálogo **"Criar Novo Utilizador",** execute os seguintes passos.

    ![utilizador](./media/velpicsaml-tutorial/velpic_8.png)
    
    a. Na caixa de texto **Name First,** digite o primeiro nome de B.

    b. Na caixa de texto **do Último Nome,** escreva o último nome de Simon.

    c. Na caixa de texto **do Nome do Utilizador,** digite o nome de utilizador de B.Simon.

    d. Na caixa de sms **do Email,** digite o endereço de e-mail da B.Simon@contoso.com conta.

    e. O resto da informação é opcional, pode preenchê-la se necessário.
    
    f. Clique **em SAVE** .

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

1. Quando clicar no azulejo Velpic SAML no Painel de Acesso, deverá obter a página de login da aplicação Velpic SAML. Deverá ver o botão **'Iniciar sessão com Azure AD'** na página.

    ![Plugin](./media/velpicsaml-tutorial/velpic_6.png)

1. Clique no botão **'Iniciar sessão com Azure AD'** para iniciar sessão no Velpic utilizando a sua conta AZure AD.

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente o Velpic SAML com a Azure AD](https://aad.portal.azure.com/)