---
title: 'Tutorial: Azure Ative Directory integração individual (SSO) com a Cyara CX Assurance Platform [ Cyara CX Assurance Platform ] Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a Cyara CX Assurance Platform.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 7387bc3b-2006-4d76-8011-efeb75190a2c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 05/06/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6d918bc3e5be41182b6924f4d0534ed143506aec
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82984371"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cyara-cx-assurance-platform"></a>Tutorial: Azure Ative Directory integração individual (SSO) com a Cyara CX Assurance Platform

Neste tutorial, você aprenderá a integrar a Cyara CX Assurance Platform com o Azure Ative Directory (Azure AD). Quando integrar a Cyara CX Assurance Platform com a Azure AD, pode:

* Controlo em Azure AD que tem acesso à Cyara CX Assurance Platform.
* Ative que os seus utilizadores sejam automaticamente inscritos na Cyara CX Assurance Platform com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Cyara CX Assurance Platform single sign-on (SSO) enabled subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Cyara CX Assurance Platform suporta **IDP** iniciado SSO
* Assim que configurar a Cyara CX Assurance Platform, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controlo](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)da sessão com o Microsoft Cloud App Security .

## <a name="adding-cyara-cx-assurance-platform-from-the-gallery"></a>Adicionando cyara CX Assurance Platform da galeria

Para configurar a integração da Cyara CX Assurance Platform em Azure AD, você precisa adicionar Cyara CX Assurance Platform da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **de galeria,** **digite cyara CX Assurance Platform** na caixa de pesquisa.
1. Selecione **Cyara CX Assurance Platform** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-cyara-cx-assurance-platform"></a>Configure e teste Azure AD single sign-on para Cyara CX Assurance Platform

Configure e teste Azure AD SSO com cyara CX Assurance Platform usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado na Cyara CX Assurance Platform.

Para configurar e testar o Azure AD SSO com a Cyara CX Assurance Platform, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure cyara CX Assurance Platform SSO](#configure-cyara-cx-assurance-platform-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    1. Crie o utilizador de **[teste cyara CX Assurance Platform](#create-cyara-cx-assurance-platform-test-user)** - para ter uma contrapartida de B.Simon na Cyara CX Assurance Platform que está ligada à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **cyara CX Assurance Platform,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na configuração de um único sessão com a página **SAML,** introduza os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:`https://www.cyaraportal.us/cyarawebidentity/identity/<provider>`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://www.cyaraportal.us/cyarawebidentity/identity/<provider>/Acs`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação e resposta real. Contacte a equipa de suporte ao Cliente da [Cyara CX Assurance Platform](mailto:support@cyara.com) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. Na secção Certificado de **Assinatura SAML,** clique no botão **Editar** para abrir o diálogo do Certificado de **Assinatura SAML.**

    ![Editar certificado de assinatura SAML](common/edit-certificate.png)

1. Na secção certificado de **assinatura SAML,** copie o **Valor da Impressão Digital** e guarde-o no seu computador.

    ![Copiar valor de impressão digital](common/copy-thumbprint.png)

1. Na secção **"Plataforma de Garantia Cyara CX",** copie os URL(s) adequados com base no seu requisito.

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

Nesta secção, permitirá que a B.Simon utilize um único sign-on Azure, concedendo acesso à Cyara CX Assurance Platform.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Cyara CX Assurance Platform**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-cyara-cx-assurance-platform-sso"></a>Configure Cyara CX Assurance Platform SSO

Para configurar um único sinal no lado da **Plataforma cyara CX Assurance,** você precisa enviar o Valor de **Impressão Digital** e URLs copiados apropriados do portal Azure para [cyara CX Assurance Platform equipe](mailto:support@cyara.com)de suporte . Eles definiram esta definição para ter a ligação SAML SSO corretamente definida em ambos os lados.

### <a name="create-cyara-cx-assurance-platform-test-user"></a>Criar o utilizador de teste da Plataforma cyara CX Assurance

Nesta secção, cria-se um utilizador chamado Britta Simon na Cyara CX Assurance Platform. Trabalhe com a equipa de suporte da [Cyara CX Assurance Platform](mailto:support@cyara.com) para adicionar os utilizadores na plataforma Cyara CX Assurance Platform. Os utilizadores devem ser criados e ativados antes de utilizar um único sinal.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo cyara CX Assurance Platform no Painel de Acesso, deve ser automaticamente inscrito na Plataforma de Garantia Cyara CX para a qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente a Cyara CX Assurance Platform com a Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger a Cyara CX Assurance Platform com visibilidade e controlos avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)