---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com IQNavigator VMS / Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o IQNavigator VMS.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/23/2019
ms.author: jeedes
ms.openlocfilehash: 947834f0ca32b81a8a5e292ce63507f93a389bb7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88552790"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-iqnavigator-vms"></a>Tutorial: Azure Ative Directy integração única (SSO) com IQNavigator VMS

Neste tutorial, você vai aprender a integrar o IQNavigator VMS com Azure Ative Directory (Azure AD). Quando integrar o IQNavigator VMS com AZure AD, pode:

* Controlo em Azure AD que tem acesso ao IQNavigator VMS.
* Ative os seus utilizadores a serem automaticamente inscritos no IQNavigator VMS com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* IQNavigator VMS assinatura única (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.


* IQNavigator VMS suporta **IDP** iniciado SSO

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.



## <a name="adding-iqnavigator-vms-from-the-gallery"></a>Adicionar VMS do IQNavigator da galeria

Para configurar a integração do VMS do IQNavigator em Azure AD, é necessário adicionar VMS do IQNavigator da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **digite IQNavigator VMS** na caixa de pesquisa.
1. Selecione **o IQNavigator VMS** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on-for-iqnavigator-vms"></a>Configurar e testar Azure AD único sinal para IQNavigator VMS

Configure e teste Azure AD SSO com VMS IQNavigator usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no IQNavigator VMS.

Para configurar e testar o Azure AD SSO com o IQNavigator VMS, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o VMS SSO do IQNavigator VMS](#configure-iqnavigator-vms-sso)** - para configurar as definições de sinalização única no lado da aplicação.
    1. Crie o utilizador de **[teste VMS do IQNavigator](#create-iqnavigator-vms-test-user)** - para ter uma contraparte de B.Simon no IQNavigator VMS que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **VMS do IQNavigator,** encontre a secção **Gerir** e selecione um único sinal de **sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    a. Na caixa de texto **identifier,** digite um URL: `iqn.com`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<subdomain>.iqnavigator.com/security/login?client_name=https://sts.window.net/<instance name>`

    c. Clique **em Definir URLs adicionais**.

    d. Na caixa de texto **do Estado de retransmissão,** digite um URL utilizando o seguinte padrão: `https://<subdomain>.iqnavigator.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de resposta real e estado de retransmissão. Contacte a equipa de [suporte do cliente IQNavigator VMS](https://www.beeline.com/support-iqn/) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. A aplicação IQNavigator espera o valor único do identificador do utilizador na reclamação do Identificador de Nome. O cliente pode mapear o valor correto para a reclamação do identificador de nome. Neste caso, mapeamos o utilizador. UserPrincipalName para o propósito de demonstração. Mas de acordo com as configurações da sua organização deve mapear o valor correto para o mesmo.

    ![image](common/edit-attribute.png)

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** clique no botão de cópia para copiar o Url de **metadados da Federação de Aplicações** e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/copy-metadataurl.png)

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, permitindo o acesso ao IQNavigator VMS.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **IQNavigator VMS**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-iqnavigator-vms-sso"></a>Configure IQNavigator VMS SSO

Para configurar um único sinal no lado **VMS do IQNavigator,** é necessário enviar o **Url de Metadados da Federação de Aplicações** para a equipa de [suporte do VMS do IQNavigator](https://www.beeline.com/support-iqn/). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-iqnavigator-vms-test-user"></a>Criar utilizador de teste VMS do IQNavigator

Nesta secção, cria-se um utilizador chamado Britta Simon no IQNavigator VMS. Trabalhe com a [equipa de suporte do IQNavigator VMS](https://www.beeline.com/support-iqn/) para adicionar os utilizadores na plataforma VMS do IQNavigator. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo VMS do IQNavigator no Painel de Acesso, deverá ser automaticamente inscrito no VMS do IQNavigator para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o IQNavigator VMS com Azure AD](https://aad.portal.azure.com/)

