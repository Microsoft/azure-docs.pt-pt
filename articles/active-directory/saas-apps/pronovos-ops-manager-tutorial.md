---
title: 'Tutorial: Integração do Azure Ative Directory com o ProNovos Ops Manager Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o ProNovos Ops Manager.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/18/2019
ms.author: jeedes
ms.openlocfilehash: ba1bcc15caee6f65f5a5d10a63c28f74faee753f
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88553396"
---
# <a name="tutorial-integrate-pronovos-ops-manager-with-azure-active-directory"></a>Tutorial: Integrar o ProNovos Ops Manager com o Azure Ative Directory

Neste tutorial, você vai aprender a integrar ProNovos Ops Manager com Azure Ative Directory (Azure AD). Quando integrar o ProNovos Ops Manager com a Azure AD, pode:

* Controle em Azure AD que tem acesso ao ProNovos Ops Manager.
* Permita que os seus utilizadores sejam automaticamente inscritos no ProNovos Ops Manager com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* ProNovos Ops Manager única subscrição ativada (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* ProNovos Ops Manager apoia **SP e IDP** iniciado SSO

## <a name="adding-pronovos-ops-manager-from-the-gallery"></a>Adicionar ProNovos Ops Manager da galeria

Para configurar a integração do ProNovos Ops Manager no AD Azure, precisa adicionar o ProNovos Ops Manager da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **escreva ProNovos Ops Manager** na caixa de pesquisa.
1. Selecione **ProNovos Ops Manager** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Configure e teste Azure AD SSO com ProNovos Ops Manager usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no ProNovos Ops Manager.

Para configurar e testar o Azure AD SSO com o ProNovos Ops Manager, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o ProNovos Ops Manager SSO](#configure-pronovos-ops-manager-sso)** - para configurar as definições de Sign-On único no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
5. Crie o utilizador de **[teste ProNovos Ops Manager](#create-pronovos-ops-manager-test-user)** - para ter uma contraparte de B.Simon em ProNovos Ops Manager que está ligada à representação AD AD do utilizador.
6. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **ProNovos Ops Manager,** encontre a secção **Gerir** e selecione **'Sessão única'.**
1. Na página **de método de inscrição única,** selecione **SAML**.
1. Na **configuração de 'Sessão Única' com** a página SAML, clique no ícone edit/pen para **a configuração básica do SAML** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **de Configuração Básica SAML,** a aplicação está pré-configurada no modo iniciado pelo **IDP** e os URLs necessários já estão pré-povoados com Azure. O utilizador precisa de guardar a configuração clicando no botão **Guardar.**

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL:  `https://gly.smartsubz.com/saml2/acs`


4. Na **configuração de um único sign-on com** a página SAML, na secção **certificado de assinatura SAML,** encontre **o Certificado (Raw)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificateraw.png)

6. Na secção **Configurar ProNovos Ops Manager,** copie os URL(s) apropriados com base na sua exigência.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-pronovos-ops-manager-sso"></a>Configure ProNovos Ops Manager SSO

Para configurar um único sign-on no lado **ProNovos Ops Manager,** você precisa enviar o Certificado descarregado **(Raw)** e URLs copiados apropriados do portal Azure para a equipe de [suporte ProNovos Ops Manager](mailto:support@pronovos.com). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.
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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao ProNovos Ops Manager.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **ProNovos Ops Manager**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

### <a name="create-pronovos-ops-manager-test-user"></a>Criar utilizador de teste ProNovos Ops Manager

Nesta secção, cria-se um utilizador chamado B.Simon in ProNovos Ops Manager. Trabalhe com a [equipa de suporte proNovos Ops Manager](mailto:support@pronovos.com) para adicionar os utilizadores na plataforma ProNovos Ops Manager. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

### <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo ProNovos Ops Manager no Painel de Acesso, deverá ser automaticamente inscrito no ProNovos Ops Manager para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos Adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

