---
title: 'Tutorial: Integração do Azure Ative Directory com Resumo Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Abstract.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/16/2019
ms.author: jeedes
ms.openlocfilehash: baae0f89165e292408150dd419c6de8dc41a320c
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2020
ms.locfileid: "94335563"
---
# <a name="tutorial-integrate-abstract-with-azure-active-directory"></a>Tutorial: Integrar Resumo com Diretório Ativo Azure

Neste tutorial, você vai aprender a integrar o Abstrato com O Diretório Ativo Azure (Azure AD). Quando integra o Abstract com AZure AD, pode:

* Controlo em Azure AD que tem acesso ao Abstract.
* Capacitar os seus utilizadores a serem automaticamente inscritos no Abstract com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Assinatura de inscrição única (SSO) ativada abstrata .

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Resumo apoia **SP e IDP** iniciado SSO

## <a name="adding-abstract-from-the-gallery"></a>Adicionando resumo da galeria

Para configurar a integração do Abstract em AD Azure, é necessário adicionar o Resumo da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **escreva Resumo** na caixa de pesquisa.
1. Selecione **Resumo** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Configure e teste Azure AD SSO com Resumo usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Abstract.

Para configurar e testar o Azure AD SSO com Resumo, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure O SSO Abstrato](#configure-abstract-sso)** - para configurar as definições de Sign-On única no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create Abstract test user](#create-abstract-test-user)** - ter uma contraparte de Britta Simon em Resumo que está ligada à representação AD AD do utilizador.
6. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **Abstratas,** encontre a secção **Gerir** e selecione **'Único sinal de sismo'.**
1. Na página **de método de inscrição única,** selecione **SAML**.
1. Na **configuração single Sign-On com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **de Configuração Básica SAML,** a aplicação está pré-configurada no modo iniciado pelo **IDP** e os URLs necessários já estão pré-povoados com Azure. O utilizador precisa de guardar a configuração clicando no botão **Guardar.**

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL:  `https://app.abstract.com/signin`

4. Na **configuração single Sign-On com página SAML,** na secção Certificado de Assinatura **SAML,** clique no botão de cópia para copiar o **Url de Metadados da Federação de Aplicações** e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/copy-metadataurl.png)

### <a name="configure-abstract-sso"></a>Configurar SSO abstrato

Certifique-se de recuperar o seu `App Federation Metadata Url` e o do portal `Azure AD Identifier` Azure, pois precisará deles para configurar sSO em Resumo.

Encontrará essas informações no Sign-On Único configurado com a página **SAML:**

* O `App Federation Metadata Url` encontra-se na secção **de Certificado de Assinatura SAML.**
* O `Azure AD Identifier` encontra-se na secção **Configuração Abstrata.**


Está agora pronto para configurar sSO em resumo:

>[!Note]
>Terá de autenticar com uma conta de Administração da organização para aceder às definições SSO no Resumo.

1. Abra a [aplicação web Abstract](https://app.abstract.com/).
2. Vá à página **permissões** na barra do lado esquerdo.
3. Na secção **Configure SSO,** insira o URL **dos metadados** e **o ID da Entidade.**
4. Insira quaisquer exceções manuais que possa ter. Os e-mails listados na secção de exceções manuais irão contornar o SSO e poderão iniciar sessão com e-mail e senha. 
5. Clique em **Guardar Alterações**.

>[!Note] 
>Você precisará usar endereços de e-mail primários na lista de exceções manuais. A ativação SSO falhará se o e-mail que fizer for o e-mail secundário de um utilizador. Se isso acontecer, verá uma mensagem de erro com o e-mail principal da conta que falha. Adicione o e-mail primário à lista de exceções manuais depois de ter verificado que conhece o utilizador.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Azure Ative Directory** , selecione **Utilizadores** , e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome** , introduza `B.Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao Abstract.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, **selecione Resumo**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

### <a name="create-abstract-test-user"></a>Criar utilizador de teste abstrato

Para testar SSO em Resumo:

1. Abra a [aplicação web Abstract](https://app.abstract.com/).
2. Vá à página **permissões** na barra do lado esquerdo.
3. Clique **em Testar com a minha conta.** Se o teste falhar, contacte a [nossa equipa de apoio.](https://help.abstract.com/hc/)

>[!Note]
>Terá de autenticar com uma conta de Administração da organização para aceder às definições SSO no Resumo.
Esta conta de Admin da organização terá de ser atribuída ao Abstract no portal Azure.

### <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Abstrato no Painel de Acesso, deverá ser automaticamente inscrito no Resumo para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)