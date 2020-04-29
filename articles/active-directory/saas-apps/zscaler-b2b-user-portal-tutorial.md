---
title: 'Tutorial: Azure Ative Directory integração de um único signo (SSO) com o Portal de Utilizador Zscaler B2B [ Portal de Utilizadores] Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Diretório Ativo Azure e o Portal de Utilizador Zscaler B2B.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 75b90449-095d-4e47-a1fd-8b5cbbfe7e3d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/24/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c28af94c92fa56d5217aee91053521d2c12b0aef
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80668931"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zscaler-b2b-user-portal"></a>Tutorial: Azure Ative Diretório integração de um único sign-on (SSO) com o Portal de Utilizador Zscaler B2B

Neste tutorial, você vai aprender a integrar o Portal de Utilizadores Zscaler B2B com o Diretório Ativo Azure (Azure AD). Quando integrar o Portal de Utilizador Zscaler B2B com a AD Azure, pode:

* Controlo em Azure AD que tem acesso ao Portal de Utilizador Zscaler B2B.
* Ative que os seus utilizadores sejam automaticamente inscritos no Portal de Utilizador Zscaler B2B com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A subscrição ativada pelo Portal do Utilizador Zscaler B2B (SSO) permitiu a subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Portal de Utilizador Zscaler B2B suporta **IDP** iniciado

* Portal de utilizador Zscaler B2B suporta o fornecimento de utilizadores **justo no tempo**

* Assim que configurar o Portal de Utilizador Zscaler B2B, pode impor o Controlo de Sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. Controlo de Sessão estende-se a partir de Acesso Condicional. [Saiba como impor o controlo de sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-zscaler-b2b-user-portal-from-the-gallery"></a>Adicionar portal de utilizador Zscaler B2B da galeria

Para configurar a integração do Portal de Utilizador Zscaler B2B no Azure AD, é necessário adicionar o Portal de Utilizador Zscaler B2B da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** digite o Portal do **Utilizador Zscaler B2B** na caixa de pesquisa.
1. Selecione O Portal do **Utilizador Zscaler B2B** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-zscaler-b2b-user-portal"></a>Configure e teste de entrada única do Azure AD para o Portal do Utilizador Zscaler B2B

Configure e teste Azure AD SSO com zscaler B2B User Portal utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Portal de Utilizador Zscaler B2B.

Para configurar e testar o Azure AD SSO com o Portal do Utilizador Zscaler B2B, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. Configure o Portal do **[Utilizador Zscaler B2B SSO](#configure-zscaler-b2b-user-portal-sso)** - para configurar as definições de início de sessão individuais no lado da aplicação.
    1. Crie o utilizador do Portal do **[Utilizador Zscaler B2B](#create-zscaler-b2b-user-portal-test-user)** - para ter uma contraparte de B.Simon no Portal de Utilizador Zscaler B2B que esteja ligado à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **zscaler B2B User Portal,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na configuração de um único sessão com a página **SAML,** introduza os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:`https://samlsp.private.zscaler.com/auth/metadata/<UniqueID>`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://samlsp.private.zscaler.com/auth/login?domain=EXAMPLE`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação e resposta real. Contacte a equipa de suporte do Portal do [Utilizador Zscaler B2B](https://help.zscaler.com/) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **metadados da Federação XML** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

1. Na secção De configurar o Portal do **Utilizador Zscaler B2B,** copie os URL(s) adequados com base na sua exigência.

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

Nesta secção, permitirá que a B.Simon utilize um único sinal de Acesso Azure, concedendo acesso ao Portal de Utilizadores Zscaler B2B.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione Portal de **Utilizador Zscaler B2B**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-zscaler-b2b-user-portal-sso"></a>Configure Zscaler B2B Portal de Utilizador SSO

1. Abra uma nova janela do navegador web e inscreva-se no seu site da empresa Zscaler B2B User Portal como administrador e execute os seguintes passos:

1. Do lado esquerdo do menu, clique em **Administração** e navegue até à secção **DE AUTENTICAÇÃO** clique na **configuração idp**.

    ![Administração de Administrador de Acesso Privado Zscaler](./media/zscaler-b2b-user-tutorial/tutorial-zscaler-private-access-administration.png)

1. No canto superior direito, clique em **Adicionar Configuração IdP**. 

    ![Zscaler Private Access Administrator IDP](./media/zscaler-b2b-user-tutorial/tutorial-zscaler-private-access-idp.png)

1. Na página **de Configuração Add IDP** efetuar os seguintes passos:
 
    ![Selecionador de Acesso Privado Zscaler](./media/zscaler-b2b-user-tutorial/tutorial-zscaler-private-access-select.png)

    a. Clique em **Selecionar Ficheiro** para fazer o upload do ficheiro Metadados descarregado a partir do Azure AD no campo de upload de **ficheiros idp metadados.**

    b. Lê os **metadados idp** da AD Azure e povoa todas as informações dos campos, como mostrado abaixo.

    ![Administrador de Acesso Privado Zscaler config](./media/zscaler-b2b-user-tutorial/config.png)

    c. Selecione o seu domínio a partir do campo **Domínios.**
    
    d. Clique em **Guardar**.

### <a name="create-zscaler-b2b-user-portal-test-user"></a>Criar o utilizador do portal de teste do Portal do Utilizador Zscaler B2B

Nesta secção, um utilizador chamado Britta Simon é criado no Portal de Utilizador Zscaler B2B. O Portal do Utilizador Zscaler B2B suporta o fornecimento de utilizadores just-in-time, que está ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no Portal do Utilizador Zscaler B2B, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no portal do portal de utilizador Zscaler B2B no Painel de Acesso, deverá ser automaticamente inscrito no Portal de Utilizador Zscaler B2B para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o Portal de Utilizador Zscaler B2B com a AD Azure](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)