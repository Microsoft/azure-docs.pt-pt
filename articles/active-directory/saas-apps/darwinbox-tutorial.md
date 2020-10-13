---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com darwinbox Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Darwinbox.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/23/2019
ms.author: jeedes
ms.openlocfilehash: a2c1ba8feb933be7c4970f061dd5acdc1ef68a16
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91775445"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-darwinbox"></a>Tutorial: Azure Ative Directory integração única (SSO) com Darwinbox

Neste tutorial, você vai aprender a integrar darwinbox com Azure Ative Directory (Azure AD). Quando integrar a Darwinbox com a AD Azure, pode:

* Controlo em Azure AD que tem acesso à Darwinbox.
* Permita que os seus utilizadores sejam automaticamente inscritos na Darwinbox com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.
Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Assinatura ativada por Darwinbox single sign-on (SSO).
> [!NOTE]
> Esta integração também está disponível para usar a partir do ambiente cloud do governo dos EUA Azure AD. Você pode encontrar esta aplicação na Azure AD US Government Cloud Application Gallery e configurá-la da mesma forma que você faz a partir de nuvem pública.


## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Darwinbox suporta **SSO** iniciado SP

## <a name="adding-darwinbox-from-the-gallery"></a>Adicionando Darwinbox da galeria

Para configurar a integração da Darwinbox no Azure AD, precisa adicionar darwinbox da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Add da secção **da galeria,** **digite Darwinbox** na caixa de pesquisa.
1. Selecione **Darwinbox** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on-for-darwinbox"></a>Configurar e testar Azure AD único sinal para darwinbox

Configure e teste Azure AD SSO com darwinbox usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado na Darwinbox.

Para configurar e testar o Azure AD SSO com darwinbox, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure darwinbox SSO](#configure-darwinbox-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Darwinbox test user](#create-darwinbox-test-user)** - para ter uma contraparte de B.Simon em Darwinbox que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Darwinbox,** encontre a secção **Gerir** e selecione **um único sinal de sing.**
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

   1. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<SUBDOMAIN>.darwinbox.in/`

   1. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://<SUBDOMAIN>.darwinbox.in/adfs/module.php/saml/sp/metadata.php/<CUSTOMID>`

      > [!NOTE]
      > Estes valores não são reais. Atualize estes valores com o sinal real no URL e no identificador. Contacte [a equipa de suporte do Cliente Darwinbox](https://darwinbox.com/contact-us.php) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregue** para descarregar o certificado e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção **Configurar darwinbox,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à Darwinbox.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Darwinbox**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-darwinbox-sso"></a>Configurar darwinbox SSO

Para configurar um único sinal no lado **darwinbox,** você precisa enviar o **metdata XML da Federação** descarregado e URLs copiados apropriados do portal Azure para a equipa de [suporte darwinbox](https://darwinbox.com/contact-us.php). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-darwinbox-test-user"></a>Criar utilizador de teste Darwinbox

Nesta secção, cria-se um utilizador chamado B.Simon na Darwinbox. Trabalhe com a [equipa de suporte da Darwinbox](https://darwinbox.com/contact-us.php) para adicionar os utilizadores na plataforma Darwinbox. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo darwinbox no Painel de Acesso, deverá ser automaticamente inscrito na Darwinbox para a qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="test-sso-for-darwinbox-mobile"></a>Teste SSO para Darwinbox (Móvel)

1. Aplicação móvel Aberta Darwinbox. Clique em **ENTER Organization URL** agora insira o URL da sua organização na caixa de texto e clique no botão Seta.

    ![Screenshot que mostra a aplicação móvel "Darwinbox" com o selecionado "Enter Organization U R L" e uma organização de exemplo e botão "Arrow" realçados.](media/darwinbox-tutorial/DarwinboxMobile01.jpg)

1. Se tiver vários domínios, clique no seu domínio.

    ![Screenshot que mostra o ecrã "Escolha o seu domínio" com um domínio de exemplo selecionado.](media/darwinbox-tutorial/DarwinboxMobile02.jpg)

1. Introduza o seu e-mail AD AZure na aplicação Darwinbox e clique em **Seguinte**.

    ![Screenshot que mostra o ecrã "Iniciar" com o botão "Seguinte" realçado.](media/darwinbox-tutorial/DarwinboxMobile03.jpg)

1. Introduza a sua palavra-passe AD AZure na aplicação Darwinbox e clique **em Iniciar sing dentro**

    ![Screenshot que mostra o ecrã "Introduzir palavra-passe" com o botão "Seguinte" realçado.](media/darwinbox-tutorial/DarwinboxMobile04.jpg)

1. Finalmente, após o início do sôm.

    ![Aplicativo móvel Darwinbox](media/darwinbox-tutorial/DarwinboxMobile05.jpg)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente darwinbox com Azure AD](https://aad.portal.azure.com/)

