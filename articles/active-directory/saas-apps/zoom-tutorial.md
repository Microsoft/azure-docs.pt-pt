---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com Zoom [ Integração de diretório soactivo) com zoom [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Zoom.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 0ebdab6c-83a8-4737-a86a-974f37269c31
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5f9d727154adf0a2099d7a9144c109cef9c91238
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "70743972"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zoom"></a>Tutorial: Azure Ative Directory integração de um único sign-on (SSO) com zoom

Neste tutorial, você vai aprender a integrar zoom com o Azure Ative Directory (Azure AD). Quando integrar o Zoom com o Azure AD, pode:

* Controlo em Azure AD que tem acesso a Zoom.
* Ative que os seus utilizadores sejam automaticamente inscritos no Zoom com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Zoom single sign-on (SSO) enabled subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Zoom suporta **SP** iniciado SSO e 
* Zoom suporta o fornecimento [ **automatizado** de utilizadores](https://docs.microsoft.com/azure/active-directory/saas-apps/zoom-provisioning-tutorial).

## <a name="adding-zoom-from-the-gallery"></a>Adicionando Zoom da galeria

Para configurar a integração do Zoom em Azure AD, precisa adicionar Zoom da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No **Add da** secção galeria, **digite Zoom** na caixa de pesquisa.
1. Selecione **Zoom** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-zoom"></a>Configure e teste Azure AD single sign-on para Zoom

Configure e teste Azure AD SSO com Zoom utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em Zoom.

Para configurar e testar o Azure AD SSO com zoom, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
2. **[Configure zoom SSO](#configure-zoom-sso)** - para configurar as definições de início de sessão individuais no lado da aplicação.
    1. **[Criar o utilizador](#create-zoom-test-user)** de teste Zoom - para ter uma contrapartida de B.Simon em Zoom que esteja ligada à representação do utilizador da AD Azure.
3. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **Zoom,** encontre a secção **Gerir** e selecione **single sign-on**.
1. Na página **Select a Single sign-on,** selecione **SAML**.
1. Na configuração do Single Sign-On com a página **SAML,** clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    a. No **Sign on URL** text box, digite um URL utilizando o seguinte padrão:`https://<companyname>.zoom.us`

    b. Na caixa de texto **identificador (Id da entidade),** digite um URL utilizando o seguinte padrão:`<companyname>.zoom.us`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e identificador. Contacte a equipa de suporte do [Cliente Zoom](https://support.zoom.us/hc/) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. Na configuração de um único sinal com página **SAML,** na secção certificado de **assinatura SAML,** encontre **certificado (Base64)** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

1. Na secção **'Configurar Zoom',** copie os URL(s) adequados com base no seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

> [!NOTE]
> Para aprender a configurar o papel em Azure AD, consulte [Configurar a alegação de função emitida no token SAML para aplicações empresariais](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management).

> [!NOTE]
> Zoom pode esperar uma reivindicação de grupo na carga útil SAML. Se criou algum grupo, contacte a equipa de suporte do [Cliente Zoom](https://support.zoom.us/hc/) com as informações do grupo para que possam configurar as informações do grupo na sua extremidade. Também precisa de fornecer o ID do Objeto à equipa de suporte do [Cliente Zoom](https://support.zoom.us/hc/) para que possam configurar o ID do Objeto na sua extremidade. Para obter o ID do objeto, consulte [Configurar zoom com Azure](https://support.zoom.us/hc/articles/115005887566).

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

Nesta secção, permitirá que b.Simon utilize um único sign-on Azure, concedendo acesso ao Zoom.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, **selecione Zoom**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-zoom-sso"></a>Configure Zoom SSO

1. Numa janela diferente do navegador web, inscreva-se no site da sua empresa Zoom como administrador.

2. Clique no separador **'Sinal Único'.**

    ![Separador de inscrição única](./media/zoom-tutorial/ic784700.png "Início de sessão único")

3. Clique no separador Controlo de **Segurança** e, em seguida, vá para as definições **de Iniciar a Minha Parte** única.

4. Na secção De Sessão Individual, execute os seguintes passos:

    ![Secção única de sinalização](./media/zoom-tutorial/ic784701.png "Início de sessão único")

    a. Na caixa de texto URL da **página signina,** colhe o valor do URL de **Login** que copiou do portal Azure.

    b. Para obter o valor URL da **página sign-out,** você precisa ir ao portal Azure e clicar no **Diretório Ativo Azure** à esquerda e depois navegar para **as inscrições da App**.

    ![O botão Azure Ative Directory](./media/zoom-tutorial/appreg.png)

    c. Clique em **Pontos Finais**

    ![O botão ponto final](./media/zoom-tutorial/endpoint.png)

    d. Copie o PONTO FINAL DE SINAL DE **SINAL SAML-P** e cole-o na caixa de texto URL da **página sign-out.**

    ![O botão de ponto de fim de cópia](./media/zoom-tutorial/endpoint1.png)

    e. Abra o seu certificado codificado base-64 no bloco de notas, copie o conteúdo do mesmo na sua área de receita e, em seguida, cole-o na caixa de texto do certificado do fornecedor de **identidade.**

    f. Na caixa de texto **emitente,** cola o valor do **Identificador Azure AD** que copiou do portal Azure. 

    g. Clique em **Guardar**.

    > [!NOTE]
    > Para mais informações, visite a documentação de zoom[https://zoomus.zendesk.com/hc/articles/115005887566](https://zoomus.zendesk.com/hc/articles/115005887566)

### <a name="create-zoom-test-user"></a>Criar o utilizador do teste zoom

O objetivo desta secção é criar um utilizador chamado B.Simon em Zoom. O Zoom suporta o fornecimento automático do utilizador, que está por defeito. Pode encontrar mais detalhes [aqui](https://docs.microsoft.com/azure/active-directory/saas-apps/zoom-provisioning-tutorial) sobre como configurar o fornecimento automático de utilizadores.

> [!NOTE]
> Se precisar de criar um utilizador manualmente, precisa de contactar a equipa de suporte do [Cliente Zoom](https://support.zoom.us/hc/)

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Zoom no Painel de Acesso, deve ser automaticamente inscrito no Zoom para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente zoom com anúncio azure](https://aad.portal.azure.com/)
