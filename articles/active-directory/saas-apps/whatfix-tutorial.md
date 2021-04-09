---
title: 'Tutorial: Integração do Diretório Ativo Azure com o Whatfix | Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o Whatfix.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/17/2019
ms.author: jeedes
ms.openlocfilehash: fd3ce846abbd2ce310b12634391453ef78ce0094
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92521647"
---
# <a name="tutorial-integrate-whatfix-with-azure-active-directory"></a>Tutorial: Integre o Whatfix com o Azure Ative Directory

Neste tutorial, você vai aprender a integrar o Whatfix com O Diretório Ativo Azure (Azure AD). Quando integrar o Whatfix com AD Azure, pode:

* Controlo em Azure AD que tem acesso ao Whatfix.
* Ativar os seus utilizadores para serem automaticamente inscritos no Whatfix com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode ter um mês de teste gratuito [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* A subscrição ativada por whatfix single sign-on (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste. Whatfix suporta **SP e IDP** iniciado SSO

## <a name="adding-whatfix-from-the-gallery"></a>Adicionar o Quefix da galeria

Para configurar a integração do Whatfix no AD Azure, é necessário adicionar o Whatfix da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **digite Whatfix** na caixa de pesquisa.
1. Selecione **Whatfix** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Configure e teste Azure AD SSO com o Whatfix utilizando um utilizador de teste chamado **Britta Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no Whatfix.

Para configurar e testar o Azure AD SSO com o Whatfix, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Whatfix SSO](#configure-whatfix-sso)** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create Whatfix test user](#create-whatfix-test-user)** - para ter uma contraparte de Britta Simon no Whatfix que está ligada à representação AD AZure do utilizador.
6. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.


### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Whatfix,** encontre a secção **Gerir** e selecione **'Único sinal de s-on'.**
1. Na página **de método de inscrição única,** selecione **SAML**.
1. Na **configuração single Sign-On com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para o seguinte campo:

    1. Clique **em Definir URLs adicionais**.
    1. Na caixa de texto **do Estado de retransmissão,** insira o URL de estado de retransmissão especificado do cliente.
    
    > [!NOTE]
    > Contacte a [equipa de suporte do Cliente Whatfix](https://support.whatfix.com) para obter o valor URL do estado de Retransmissão.

1. Clique **em Definir URLs adicionais** e execute os seguintes passos se desejar configurar a aplicação no modo iniciado **sp:**

    Na caixa de texto **URL de entrada de inscrição,** digite o URL:  `https://whatfix.com`

1. Na **configuração single Sign-On com página SAML,** na secção certificado de assinatura **SAML,** clique no botão de cópia para copiar o **Url de Metadados da Federação de Aplicações**.

    ![O link de descarregamento de certificado](common/copy-metadataurl.png)

### <a name="configure-whatfix-sso"></a>Configure Whatfix SSO

Para configurar um único sinal no lado **do Whatfix,** é necessário enviar o **Url de Metadados da Federação de Aplicações** para a equipa de suporte do [Whatfix](https://support.whatfix.com). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste no portal Azure chamado Britta Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `Britta Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `BrittaSimon@contoso.com`.
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que Britta Simon use a Azure single sign-on, concedendo acesso ao Whatfix.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Whatfix**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **Britta Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

### <a name="create-whatfix-test-user"></a>Criar utilizador de teste whatfix

Nesta secção, cria-se um utilizador chamado Britta Simon no Whatfix. Trabalhe com a [equipa de suporte whatfix](https://support.whatfix.com) para adicionar os utilizadores na plataforma Whatfix. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

### <a name="test-sso"></a>Teste SSO

Quando selecionar o telha Whatfix no Painel de Acesso, deverá ser automaticamente inscrito no Whatfix para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)