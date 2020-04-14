---
title: 'Tutorial: Integração do Diretório Ativo Azure com 123FormBuilder SSO [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o 123FormBuilder SSO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5211910a-ab96-4709-959a-524c4d57c43e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/14/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 947a9d632089b18f6b950c5eecbcb74d061f32eb
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81274215"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-123formbuilder-sso"></a>Tutorial: Azure Ative Diretório integração individual (SSO) com 123FormBuilder SSO

Neste tutorial, você aprenderá a integrar 123FormBuilder SSO com o Azure Ative Directory (Azure AD). Quando integrar o 123FormBuilder SSO com o Azure AD, pode:

* Controlo em Azure AD que tem acesso a 123FormBuilder SSO.
* Ative que os seus utilizadores sejam automaticamente inscritos no 123FormBuilder SSO com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* 123FormBuilder SSO única subscrição ativada (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* 123FormBuilder SSO suporta **SP e IDP** iniciado SSO.
* 123FormBuilder SSO suporta o fornecimento de utilizadores **justo no tempo.**
* Assim que configurar o 123FormBuilder SSO, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)da sessão com o Microsoft Cloud App Security .

## <a name="adding-123formbuilder-sso-from-the-gallery"></a>Adicionar 123FormBuilder SSO da galeria

Para configurar a integração do 123FormBuilder SSO no Azure AD, é necessário adicionar 123FormBuilder SSO da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** tipo **123FormBuilder SSO** na caixa de pesquisa.
1. Selecione **123FormBuilder SSO** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-123formbuilder-sso"></a>Configure e teste o único sinal de Azure AD para 123FormBuilder SSO

Configure e teste Azure AD SSO com 123FormBuilder SSO utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em 123FormBuilder SSO.

Para configurar e testar o Azure AD SSO com 123FormBuilder SSO, preencha os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    * Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure 123FormBuilder SSO](#configure-123formbuilder-sso)** - para configurar as definições de sinal única no lado da aplicação.
    * **[Crie um utilizador de teste SSO 123FormBuilder](#create-123formbuilder-sso-test-user)** - para ter uma contraparte de B.Simon em 123FormBuilder SSO que esteja ligado à representação azure AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **123FormBuilder SSO,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** Se desejar configurar a aplicação no modo iniciado **idp,** execute os seguintes passos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:`https://www.123formbuilder.com/saml/azure_ad/<tenant_id>/metadata`


    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://www.123formbuilder.com/saml/azure_ad/<tenant_id>/acs`

5. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://www.123formbuilder.com/saml/azure_ad/<tenant_id>/sso`

    > [!NOTE]
    > Estes valores não são reais. Você precisará atualizar estes valor supor urls reais e identificador que é explicado mais tarde no tutorial.

1. Na **configuração de um único sessão com** a página SAML, na secção Certificado de **Assinatura SAML,** encontre **metadados da Federação XML** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

1. Na secção **Configurar 123FormBuilder SSO,** copie os URL(s) adequados com base no seu requisito.

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

Nesta secção, permitirá que a B.Simon utilize um único sign-on Azure, concedendo acesso ao 123FormBuilder SSO.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **123FormBuilder SSO**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-123formbuilder-sso"></a>Configure 123FormBuilder SSO

1. Para configurar um único sinal no lado **123FormBuilder SSO,** vá e [https://www.123formbuilder.com/form-2709121/](https://www.123formbuilder.com/form-2709121/) execute os seguintes passos:

    ![Configurar um único sinal](./media/123formbuilder-tutorial/submit.png) 

    a. Na caixa de texto **e-mail,** `B.Simon@Contoso.com`digite o e-mail do utilizador como .

    b. Clique em **Upload** e navegue no ficheiro Metadata XML descarregado, que descarregou do portal Azure.

    c. Clique em **submeter formulário**.

2. No **AD Microsoft Azure - Entrada única - Configurar as definições** de aplicações executam os seguintes passos:

    ![Configurar um único sinal](./media/123formbuilder-tutorial/url3.png)

    a. Se desejar configurar a aplicação no **modo iniciado idp,** copie o valor **IDENTIFICADOR** para a sua instância e cole-o na caixa de texto **identifier** na secção **de configuração Básica SAML** no portal Azure.

    b. Se desejar configurar a aplicação no **modo iniciado idp,** copie o valor **URL resposta** para a sua instância e cole-a na caixa de texto **URL** resposta na secção **de configuração Básica SAML** no portal Azure.

    c. Se desejar configurar a aplicação no **modo iniciado sp,** copie o valor **URL SIGN ON** para a sua instância e cole-o no Sign On **URL** textbox na secção **de configuração Básica SAML** no portal Azure.

### <a name="create-123formbuilder-sso-test-user"></a>Criar utilizador de teste SSO 123FormBuilder

Nesta secção, um utilizador chamado Britta Simon é criado em 123FormBuilder SSO. 123FormBuilder SSO suporta o fornecimento de utilizadores just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no 123FormBuilder SSO, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo SSO 123FormBuilder no Painel de Acesso, deve ser automaticamente inscrito no SSO 123FormBuilder para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente 123FormBuilder SSO com AD Azure](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
