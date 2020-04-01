---
title: 'Tutorial: Integração do Diretório Ativo Azure com o Projectplace [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Projectplace.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 298059ca-b652-4577-916a-c31393d53d7a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/29/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 179721075484c35c5ebbb3d936b83bc407b75a8d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "67093530"
---
# <a name="tutorial-integrate-projectplace-with-azure-active-directory"></a>Tutorial: Integrar o Projectplace com o Diretório Ativo Azure

Neste tutorial, você vai aprender a integrar o Projectplace com o Azure Ative Directory (Azure AD). Quando integrar o Projectplace com o Azure AD, pode:

* Controlo em Azure AD que tem acesso ao Projectplace.
* Ative que os seus utilizadores sejam automaticamente inscritos no Projectplace com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.
* Os utilizadores podem ser aprovisionados automaticamente no Projectplace.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Projectplace single sign-on (SSO) enabled subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste. O Projectplace suporta **SP e IDP** iniciados SSO e suporta o fornecimento de utilizadores **Just In Time.**

## <a name="adding-projectplace-from-the-gallery"></a>Adicionar Projectplace da galeria

Para configurar a integração do Projectplace em Azure AD, você precisa adicionar Projectplace da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** digite **Projectplace** na caixa de pesquisa.
1. Selecione **Projectplace** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Configure e teste Azure AD SSO com Projectplace utilizando um utilizador de teste chamado **B. Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Projectplace.

Para configurar e testar o Azure AD SSO com o Projectplace, complete os seguintes blocos de construção:

1. **[Configure O SSO AD Azure](#configure-azure-ad-sso)** para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure](#configure-projectplace)** o Projectplace para configurar as definições sSO no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** para testar o único sinal de Azure AD com B. Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** para permitir que b. Simon utilize um único sinal de AD Azure.
5. **[Crie](#create-projectplace-test-user)** o utilizador do teste Projectplace para ter uma contrapartida de B. Simon no Projectplace que esteja ligada à representação do utilizador da AD Azure.
6. **[Teste sSO](#test-sso)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **do Projectplace,** encontre a secção **Gerir** e selecione **single sign-on**.
1. Na página **Select a Single sign-on,** selecione **SAML**.
1. Na configuração do Single Sign-On com a página **SAML,** clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **de Configuração Básica do SAML,** se pretender configurar a aplicação no modo iniciado **idp,** a aplicação está pré-configurada e os URLs necessários já estão pré-povoados com o Azure. O utilizador precisa de guardar a configuração clicando no botão **Guardar.**

1. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    Na caixa de texto **de URL sign-on,** escreva um URL:`https://service.projectplace.com`

1. Na configuração do Single Sign-On com a página **SAML,** na secção Certificado de **Assinatura SAML,** clique no **ícone** da cópia para copiar o Url de Metadados da Federação da **Aplicação,** de acordo com o seu requisito e guarde-o no Bloco de Notas.

   ![O link de descarregamento do Certificado](common/copy-metadataurl.png)

1. Na secção **Configurar projectplace,** copie os URL(s) adequados com base no seu requisito.

   ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-projectplace"></a>Configure Projectplace

Para configurar um único sign-on no lado do **Projectplace,** você precisa enviar o url copiado da Federação de **Metadados** da App do portal Azure para a equipe de [suporte do Projectplace](https://success.planview.com/Projectplace/Support). Esta equipa garante que a ligação SAML SSO está corretamente definida em ambos os lados.

>[!NOTE]
>A configuração de inscrição única tem de ser realizada pela equipa de suporte do [Projectplace](https://success.planview.com/Projectplace/Support). Receberá uma notificação assim que a configuração estiver completa. 

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, você vai criar um utilizador de teste no portal Azure chamado B. Simon.

1. A partir do painel esquerdo no portal Azure, **selecione Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B. Simon`.  
   1. No campo de nome username@companydomain.extensiondo **Utilizador,** introduza o . Por exemplo, `BrittaSimon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permitirá que B. Simon utilize um único sign-on azure, concedendo acesso ao Projectplace.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Projectplace**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B. Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

### <a name="create-projectplace-test-user"></a>Criar o utilizador de teste Projectplace

>[!NOTE]
>Pode saltar este passo se tiver o provisionamento habilitado no Projectplace. Pode pedir à equipa de suporte do [Projectplace](https://success.planview.com/Projectplace/Support) que permita a sua condição, uma vez que os utilizadores realizados serão criados no Projectplace durante o primeiro login.

Para permitir que os utilizadores de Anúncios Azure entrem no Projectplace, é necessário adicioná-los ao Projectplace. Precisa adicioná-las manualmente.

**Para criar uma conta de utilizador, tome estas medidas:**

1. Inscreva-se no site da empresa **Projectplace** como administrador.

2. Ir ao **People**, e depois selecionar **Membros:**
   
    ![Ir ao People e, em seguida, selecionar Membros](./media/projectplace-tutorial/ic790228.png "People")

3. Selecione **Adicionar Membro:**
   
    ![Selecione Adicionar Membro](./media/projectplace-tutorial/ic790232.png "Adicionar Membros")

4. Na secção **Adicionar Membro,** tome os seguintes passos.
   
    ![Adicionar secção de membros](./media/projectplace-tutorial/ic790233.png "Novos Membros")
   
    1. Na caixa **dos Novos Membros,** insira o endereço de e-mail de uma conta Azure AD válida que pretende adicionar.
   
    1. Selecione **Enviar**.

   Um e-mail contendo um link para confirmar a conta antes de se tornar ativo é enviado ao titular da conta Azure AD.

>[!NOTE]
>Também pode utilizar qualquer outra ferramenta de criação de conta de utilizador ou API fornecida pelo Projectplace para adicionar contas de utilizador Azure AD.


### <a name="test-sso"></a>Teste SSO

Quando selecionar o azulejo Projectplace no Painel de Acesso, deve ser automaticamente inscrito no Projectplace para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)