---
title: 'Tutorial: Integração do Azure Ative Directory com 123FormBuilder SSO | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o 123FormBuilder SSO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/14/2020
ms.author: jeedes
ms.openlocfilehash: aa4bab2f7ecb90c61e22de46b01a5ed81342a408
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92319188"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-123formbuilder-sso"></a>Tutorial: Azure Ative Directory integração única (SSO) com 123FormBuilder SSO

Neste tutorial, você vai aprender a integrar 123FormBuilder SSO com Azure Ative Directory (Azure AD). Quando integrar o 123FormBuilder SSO com AZure AD, pode:

* Control em Azure AD que tem acesso a 123FormBuilder SSO.
* Ative os seus utilizadores a serem automaticamente inscritos no 123FormBuilder SSO com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* 123FormBuilder SSO assinatura única (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* 123FormBuilder SSO suporta SSO iniciado **SP e IDP.**
* 123FormBuilder SSO suporta o fornecimento do utilizador **Just In Time.**
* Uma vez configurado 123FormBuilder SSO, pode impor o controlo de sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-123formbuilder-sso-from-the-gallery"></a>Adicionar 123FormBuilder SSO da galeria

Para configurar a integração do 123FormBuilder SSO em AD Azure, é necessário adicionar 123FormBuilder SSO da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **digite 123FormBuilder SSO** na caixa de pesquisa.
1. Selecione **123FormBuilder SSO** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-123formbuilder-sso"></a>Configure e teste Azure AD único sinal de inscrição para 123FormBuilder SSO

Configure e teste Azure AD SSO com 123FormBuilder SSO usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado em 123FormBuilder SSO.

Para configurar e testar o Azure AD SSO com 123FormBuilder SSO, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    * **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure 123FormBuilder SSO](#configure-123formbuilder-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    * **[Crie 123FormBuilder SSO utilizador](#create-123formbuilder-sso-test-user)** de teste - para ter uma contraparte de B.Simon em 123FormBuilder SSO que está ligada à representação AD Ad Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **123FormBuilder SSO,** encontre a secção **Gerir** e selecione um único sinal de **sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **De Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** execute os seguintes passos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://www.123formbuilder.com/saml/azure_ad/<tenant_id>/metadata`


    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://www.123formbuilder.com/saml/azure_ad/<tenant_id>/acs`

5. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão: `https://www.123formbuilder.com/saml/azure_ad/<tenant_id>/sso`

    > [!NOTE]
    > Estes valores não são reais. Você precisará atualizar estes valor a partir de URLs reais e Identificador, que é explicado mais tarde no tutorial.

1. Na **configuração de um único sinal com** a página SAML, na secção Certificado de Assinatura **SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregue** para descarregar o certificado e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção **Configuração 123FormBuilder SSO,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a 123FormBuilder SSO.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **123FormBuilder SSO**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-123formbuilder-sso"></a>Configurar 123FormBuilder SSO

1. Para configurar um único sinal de **sSO 123FormBuilder,** vá para [https://www.123formbuilder.com/form-2709121/](https://www.123formbuilder.com/form-2709121/) e execute os seguintes passos:

    ![Screenshot que mostra o ecrã de configuração SSO SAML - Fornecedor de Identidade.](./media/123formbuilder-tutorial/submit.png) 

    a. Na caixa de texto **por e-mail,** digite o e-mail do utilizador como `B.Simon@Contoso.com` .

    b. Clique **em Upload** e navegue no ficheiro Metadata XML descarregado, que descarregou a partir do portal Azure.

    c. CLIQUE **EM FORMULÁRIO DE SUBMISSÃO.**

2. No **Microsoft Azure AD - Single sign-on - Configurações de aplicações configuradas** executam os seguintes passos:

    ![Configurar Sign-On Individuais](./media/123formbuilder-tutorial/url3.png)

    a. Se desejar configurar a aplicação no **modo iniciado pelo IDP,** copie o valor **IDENTIFIER** para o seu exemplo e cole-o na caixa de texto **Identifier** na secção **de Configuração SAML Básica** no portal Azure.

    b. Se desejar configurar a aplicação no **modo iniciado pelo IDP,** copie o valor **URL de resposta** para o seu exemplo e cole-o na caixa de texto **URL resposta** na secção **de Configuração SAML Básica** no portal Azure.

    c. Se desejar configurar a aplicação no **modo iniciado sp,** copie o valor **DE URL SIGN ON** para o seu exemplo e cole-o em Sign On **URL** textbox na secção **de configuração SAML Básica** no portal Azure.

### <a name="create-123formbuilder-sso-test-user"></a>Criar 123 Utilizador de teste SSO do 123FormBuilder

Nesta secção, um utilizador chamado Britta Simon é criado em 123FormBuilder SSO. O 123FormBuilder SSO suporta o provisionamento do utilizador just-in-time, o que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no 123FormBuilder SSO, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo SSO 123FormBuilder no Painel de Acesso, deverá ser automaticamente inscrito no SSO 123FormBuilder para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente 123FormBuilder SSO com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)