---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com a ADP Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o ADP.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/26/2019
ms.author: jeedes
ms.openlocfilehash: 0d78f2c8da338a92ef88734371647a48ddb190c3
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97591230"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-adp"></a>Tutorial: Azure Ative Directy integração única (SSO) com a ADP

Neste tutorial, você vai aprender a integrar a ADP com Azure Ative Directory (Azure AD). Quando integrar a ADP com Azure AD, pode:

* Controlo em Azure AD que tem acesso à ADP.
* Capacitar os seus utilizadores a serem automaticamente inscritos na ADP com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A ASSINATURA ADP individual (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* ADP apoia **IDP** iniciado SSO

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.

## <a name="adding-adp-from-the-gallery"></a>Adicionar ADP da galeria

Para configurar a integração da ADP no Azure AD, é necessário adicionar ADP da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **digite ADP** na caixa de pesquisa.
1. Selecione **ADP** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-adp"></a>Configurar e testar Azure AD único sinal para a ADP

Configure e teste Azure AD SSO com ADP usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no ADP.

Para configurar e testar o Azure AD SSO com a ADP, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
2. **[Configure o ADP SSO](#configure-adp-sso)** - para configurar as definições de Sign-On única no lado da aplicação.
    1. **[Create ADP test user](#create-adp-test-user)** - para ter uma contraparte de B.Simon em ADP que está ligada à representação AD AD Azure do utilizador.
3. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração de aplicações **ADP,** clique no **separador Propriedades** e execute os seguintes passos: 

    ![Propriedades de inscrição única](./media/adpfederatedsso-tutorial/tutorial_adp_prop.png)

    a. Descreva o valor de campo **de inscrição dos utilizadores** para **Sim**.

    b. Copie o **URL de acesso** ao Utilizador e terá de colá-lo na secção URL de **configuração,** que é explicada mais tarde no tutorial.

    c. Desa estava o valor de campo **exigido** para **Sim.**

    d. Desa estale o valor de campo **visível para os utilizadores** para **o nº.**

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **ADP,** encontre a secção **Gerir** e selecione **'Único sinal de s-on'.**
1. Na página **de método de inscrição única,** selecione **SAML**.
1. Na **configuração single Sign-On com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    Na caixa de texto **identifier (Entity ID),** digite um URL:  `https://fed.adp.com`

4. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

6. Na secção **ADP configurada,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao ADP.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **ADP**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-adp-sso"></a>Configurar a ADP SSO

Para configurar um único sinal no lado **ADP,** você precisa carregar o **Metdata XML** descarregado no site da [ADP](https://adpfedsso.adp.com/public/login/index.fcc).

> [!NOTE]  
> Este processo pode demorar alguns dias.

### <a name="configure-your-adp-services-for-federated-access"></a>Configure os seus serviços ADP para acessos federados

>[!Important]
> Os seus colaboradores que necessitem de acesso federado aos seus serviços ADP devem ser designados para a aplicação de serviçoS ADP e, posteriormente, os utilizadores devem ser transferidos para o serviço específico da ADP.
Após a receção da confirmação do seu representante da ADP, configuure os seus serviços ADP e atribua/gere os utilizadores para controlar o acesso do utilizador ao serviço ADP específico.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **digite ADP** na caixa de pesquisa.
1. Selecione **ADP** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.
1. No portal Azure, na sua página de integração de aplicações **ADP,** clique no **separador Propriedades** e execute os seguintes passos:  

    ![Propriedades ligadas de inscrição única](./media/adpfederatedsso-tutorial/tutorial_adp_linkedproperties.png)

    1. Descreva o valor de campo **de inscrição dos utilizadores** para **Sim**.

    1. Desa estava o valor de campo **exigido** para **Sim.**

    1. Desa estale o valor de campo **visível para os utilizadores** para **Sim**.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **ADP,** encontre a secção **Gerir** e selecione **'Único sinal de s-on'.**

1. No **diálogo do método de inscrição única,** selecione **Mode** as **Linked**. para ligar a sua candidatura à **ADP**.

    ![Inscrição única ligada](./media/adpfederatedsso-tutorial/tutorial_adp_linked.png)

1. Navegue para a secção **URL de inscrição de configuração,** execute os seguintes passos:

    ![Adereço único de inscrição](./media/adpfederatedsso-tutorial/tutorial_adp_linkedsignon.png)

    1. Cole o URL de acesso ao **Utilizador,** que copiou a partir do **separador de propriedades** acima (da aplicação principal ADP).

    1. Seguem-se as 5 aplicações que **suportam diferentes URLs do Estado de Retransmissão.** Tem de anexar manualmente o valor **de URL do Estado de Retransmissão** apropriado para uma aplicação específica ao URL de acesso ao **Utilizador.**

        * **Força de trabalho da ADP agora**

            `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?WFN`

        * **Força de trabalho da ADP agora tempo reforçado**

            `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?EETDC2`

        * **ADP Vantage HCM**

            `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?ADPVANTAGE`

        * **ADP Enterprise HR**

            `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?PORTAL`

        * **MyADP**

            `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?REDBOX`

1. **Guarde** as suas alterações.

1. Após a receção da confirmação do seu representante da ADP, comece a testar com um ou dois utilizadores.

    1. Atribua poucos utilizadores à App de serviço ADP para testar o acesso federado.

    1. O teste é bem sucedido quando os utilizadores acedem à aplicação de serviço ADP na galeria e podem aceder ao seu serviço ADP.

1. Na confirmação de um teste bem sucedido, atribua o serviço ADP federado a utilizadores individuais ou grupos de utilizadores, o que é explicado mais tarde no tutorial e lançá-lo aos seus colaboradores.

### <a name="create-adp-test-user"></a>Criar utilizador de teste ADP

O objetivo desta secção é criar um utilizador chamado B.Simon na ADP. Trabalhe com [a equipa de apoio da ADP](https://www.adp.com/contact-us/overview.aspx) para adicionar os utilizadores na conta ADP. 

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo ADP no Painel de Acesso, deverá ser automaticamente inscrito no ADP para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)