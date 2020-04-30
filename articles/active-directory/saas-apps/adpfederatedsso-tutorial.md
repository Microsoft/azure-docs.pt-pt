---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com a ADP [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a ADP.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 7be5331b-0481-48f7-9d6b-619dfec657e1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/26/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b031ded2022078c31bd8570c6a6317c398715480
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "70162664"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-adp"></a>Tutorial: Azure Ative Diretório integração individual (SSO) com a ADP

Neste tutorial, você vai aprender a integrar a ADP com o Azure Ative Directory (Azure AD). Quando integrar a ADP com a AD Azure, pode:

* Controlo em Azure AD que tem acesso à ADP.
* Ative que os seus utilizadores sejam automaticamente inscritos na ADP com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* ADP single sign-on (SSO) ativada subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* ADP suporta **IDP** iniciado SSO

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia, pelo que apenas uma instância pode ser configurada num inquilino.

## <a name="adding-adp-from-the-gallery"></a>Adicionar ADP da galeria

Para configurar a integração da ADP em Azure AD, você precisa adicionar ADP da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** digite **ADP** na caixa de pesquisa.
1. Selecione **ADP** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-adp"></a>Configure e teste Azure AD único sinal para ADP

Configure e teste Azure AD SSO com ADP utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em ADP.

Para configurar e testar o Azure AD SSO com a ADP, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
2. **[Configure ADP SSO](#configure-adp-sso)** - para configurar as definições de início de sessão simples no lado da aplicação.
    1. **[Crie um utilizador de teste ADP](#create-adp-test-user)** - para ter uma contrapartida de B.Simon em ADP que esteja ligada à representação do utilizador da AD Azure.
3. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração de aplicações **da ADP,** clique no **separador Propriedades** e execute os seguintes passos: 

    ![Propriedades únicas de inscrição](./media/adpfederatedsso-tutorial/tutorial_adp_prop.png)

    a. Detete o **Habilitado para que os utilizadores assinem** o valor do campo para **Sim**.

    b. Copie o URL de acesso ao **Utilizador** e terá de colá-lo na secção de URL de **inscrição configurada,** o que é explicado mais tarde no tutorial.

    c. Detete a atribuição do **utilizador de** valor de campo exigido para **Sim**.

    d. Desloque o **valor visível para os utilizadores** no campo para **No**.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **da ADP,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **Select a Single sign-on,** selecione **SAML**.
1. Na configuração do Single Sign-On com a página **SAML,** clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    Na caixa de texto **identificador (Id da entidade),** digite um URL:`https://fed.adp.com`

4. Na configuração de um único sign-on com a página **SAML,** na secção certificado de **assinatura SAML,** encontre **metadados da Federação XML** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

6. Na secção **ADP configurada,** copie os URL(s) adequados com base no seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, você vai criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, **selecione Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo de nome username@companydomain.extensiondo **Utilizador,** introduza o . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permitirá que b.Simon utilize um único sign-on Azure, concedendo acesso à ADP.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **ADP**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-adp-sso"></a>Configure ADP SSO

Para configurar um único sinal no lado **ADP,** precisa de carregar o **DatamesXML** descarregado no [website aDP](https://adpfedsso.adp.com/public/login/index.fcc).

> [!NOTE]  
> Este processo pode demorar alguns dias.

### <a name="configure-your-adp-services-for-federated-access"></a>Configure os seus serviços ADP para acesso federado

>[!Important]
> Os seus colaboradores que necessitem de acesso federado aos seus serviços ADP devem ser atribuídos à aplicação de serviço ADP e, posteriormente, os utilizadores devem ser transferidos para o serviço ADP específico.
Após a receção da confirmação do seu representante da ADP, configure o seu serviço ADP(s) e designe/gerea os utilizadores para controlar o acesso do utilizador ao serviço ADP específico.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** digite **ADP** na caixa de pesquisa.
1. Selecione **ADP** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.
1. No portal Azure, na sua página de integração de aplicações **ADP,** clique no **separador Propriedades** e execute os seguintes passos:  

    ![Propriedades ligadas a um único sign-on](./media/adpfederatedsso-tutorial/tutorial_adp_linkedproperties.png)

    a.  Detete o **Habilitado para que os utilizadores assinem** o valor do campo para **Sim**.

    b.  Detete a atribuição do **utilizador de** valor de campo exigido para **Sim**.

    c.  Desloque o **valor visível para os utilizadores** no campo para **Sim**.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **da ADP,** encontre a secção **Gerir** e selecione **um único sinal.**

1. No diálogo **Select a Single Sign-on,** selecione **Mode** as **Linked**. para ligar a sua aplicação à **ADP**.

    ![Inscrição única ligada](./media/adpfederatedsso-tutorial/tutorial_adp_linked.png)

1. Navegue para a secção url de início de **'Configurar',** efetuar os seguintes passos:

    ![Adereço de inscrição única](./media/adpfederatedsso-tutorial/tutorial_adp_linkedsignon.png)

    a. Colar o URL de acesso ao **utilizador**, que copiou do **separador** de propriedades acima (a partir da aplicação ADP principal).
                                                             
    b. Seguem-se as 5 aplicações que suportam **diferentes URLs de Estado de Retransmissão.** Tem de anexar o valor URL de Estado de **Retransmissão** adequado para aplicação específica manualmente ao URL de acesso ao **Utilizador**.
    
    * **Força de trabalho da ADP agora**
        
        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?WFN`

    * **Força de trabalho da ADP agora tempo melhorado**
        
        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?EETDC2`
    
    * **ADP Vantage HCM**
        
        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?ADPVANTAGE`

    * **ADP Enterprise HR**

        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?PORTAL`

    * **MyADP**

        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?REDBOX`

9. **Guarde** as suas alterações.

10. Após a receção da confirmação do seu representante da ADP, inicie o teste com um ou dois utilizadores.

    a. Atribuir poucos utilizadores à App de serviço ADP para testar o acesso federado.

    b. O teste é bem sucedido quando os utilizadores acedem à aplicação de serviço ADP na galeria e podem aceder ao seu serviço ADP.
 
11. Na confirmação de um teste bem sucedido, atribua o serviço ADP federado a utilizadores individuais ou grupos de utilizadores, o que é explicado mais tarde no tutorial e lança-o aos seus colaboradores.

### <a name="create-adp-test-user"></a>Criar o utilizador de teste ADP

O objetivo desta secção é criar um utilizador chamado B.Simon em ADP. Trabalhe com a [equipa de suporte da ADP](https://www.adp.com/contact-us/overview.aspx) para adicionar os utilizadores na conta ADP. 

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo ADP no Painel de Acesso, deverá ser automaticamente inscrito no ADP para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente a ADP com a AD Azure](https://aad.portal.azure.com)
