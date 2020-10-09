---
title: 'Tutorial: Integração do Azure Ative Directory com Projectplace Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Projectplace.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/29/2019
ms.author: jeedes
ms.openlocfilehash: cbc65e64dfd18cdc16873a2d82aecadabeec4c28
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88553517"
---
# <a name="tutorial-integrate-projectplace-with-azure-active-directory"></a>Tutorial: Integrar projectplace com diretório ativo Azure

Neste tutorial, você vai aprender a integrar Projectplace com Azure Ative Directory (Azure AD). Quando integrar o Projectplace com a AD Azure, pode:

* Controlo em Azure AD que tem acesso ao Projectplace.
* Permita que os seus utilizadores sejam automaticamente inscritos no Projectplace com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.
* Os utilizadores podem ser a provisionados automaticamente no Projectplace.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Projectplace única inscrição (SSO) ativada subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste. Projectplace suporta **SP e IDP** iniciado SSO e suporta o fornecimento de usuário **Just In Time.**

## <a name="adding-projectplace-from-the-gallery"></a>Adicionar Projectplace da galeria

Para configurar a integração do Projectplace no AD Azure, precisa adicionar Projectplace da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **escreva Projectplace** na caixa de pesquisa.
1. Selecione **Projectplace** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Configure e teste Azure AD SSO com Projectplace usando um utilizador de teste chamado **B. Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no Projectplace.

Para configurar e testar o Azure AD SSO com projectplace, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configurar projectplace](#configure-projectplace)** para configurar as definições SSO no lado da aplicação.
3. **[Crie um utilizador de teste AD Azure](#create-an-azure-ad-test-user)** para testar o Azure AD com B. Simon.
4. **[Atribua ao utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** para permitir que b. Simon utilize um único sinal de Ad AD.
5. Crie o utilizador de **[teste projectplace](#create-projectplace-test-user)** para ter uma contraparte de B. Simon em Projectplace que esteja ligada à representação AD AD do utilizador.
6. **[Teste SSO](#test-sso)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração da aplicação **Projectplace,** encontre a secção **Gerir** e selecione **'Único sinal de súb'.**
1. Na página **de método de inscrição única,** selecione **SAML**.
1. Na **configuração single Sign-On com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **de Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** a aplicação está pré-configurada e os URLs necessários já estão pré-povoados com O azul. O utilizador precisa de guardar a configuração clicando no botão **Guardar.**

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL:  `https://service.projectplace.com`

1. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique no **ícone** de cópia para copiar o Url **de metadados da Federação de Aplicações**, de acordo com o seu requisito e guarde-o no Bloco de Notas.

   ![O link de descarregamento de certificado](common/copy-metadataurl.png)

1. Na secção **Configurar Projectplace,** copie os URL(s) apropriados com base na sua exigência.

   ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-projectplace"></a>Configurar projectplace

Para configurar um único sinal no lado do **Projectplace,** é necessário enviar o **Url de Metadados copiado da Federação** de Aplicações do portal Azure para a equipa de suporte do [Projectplace](https://success.planview.com/Projectplace/Support). Esta equipa garante que a ligação SSO SAML está corretamente definida em ambos os lados.

>[!NOTE]
>A configuração de inscrição única tem de ser executada pela equipa de apoio do [Projectplace](https://success.planview.com/Projectplace/Support). Receberá uma notificação assim que a configuração estiver completa. 

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste no portal Azure chamado B. Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B. Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `BrittaSimon@contoso.com`.
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá a B. Simon usar a Azure um único sinal de acesso, concedendo acesso ao Projectplace.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Projectplace**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B. Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

### <a name="create-projectplace-test-user"></a>Criar utilizador de teste projectplace

>[!NOTE]
>Pode saltar este passo se tiver o provisionamento habilitado no Projectplace. Pode solicitar à equipa de apoio do [Projectplace](https://success.planview.com/Projectplace/Support) para ativar a provisão, uma vez que os utilizadores acabados serão criados no Projectplace durante o primeiro login.

Para permitir que os utilizadores de Azure AD entrem no Projectplace, é necessário adicioná-los ao Projectplace. Precisa adicioná-las manualmente.

**Para criar uma conta de utilizador, tome estes passos:**

1. Inscreva-se no site da empresa **Projectplace** como administrador.

2. Vá às **Pessoas**, e, em seguida, selecione **Deputados:**
   
    ![Vá para as pessoas e, em seguida, selecione membros](./media/projectplace-tutorial/ic790228.png "People")

3. Selecione **Adicionar Membro**:
   
    ![Selecione Adicionar Membro](./media/projectplace-tutorial/ic790232.png "Adicionar membros")

4. Na secção **Adicionar Membro,** tome os seguintes passos.
   
    ![Adicionar secção de membros](./media/projectplace-tutorial/ic790233.png "Novos Membros")
   
    1. Na caixa dos **Novos Membros,** insira o endereço de e-mail de uma conta Azure AD válida que pretende adicionar.
   
    1. Selecione **Send** (Enviar).

   Um e-mail contendo um link para confirmar a conta antes de ficar ativa é enviado para o titular da conta Azure AD.

>[!NOTE]
>Também pode utilizar qualquer outra ferramenta de criação de conta de utilizador ou API fornecida pelo Projectplace para adicionar contas de utilizador Azure AD.


### <a name="test-sso"></a>Teste SSO

Ao selecionar o azulejo projectplace no Painel de Acesso, deverá ser automaticamente inscrito no Projectplace para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)