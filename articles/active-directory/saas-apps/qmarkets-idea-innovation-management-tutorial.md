---
title: 'Tutorial: Azure Ative Diretório integração única (SSO) com Qmarkets Idea & Innovation Management [ Azure Ative Diretório) integração com Qmarkets Idea & Innovation Management [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Qmarkets Idea & Innovation Management.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5ca125c7-f778-4a2d-a573-7cebe1ff634d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1d3e3f86d761a686993e6ecf32718aa2e15dac92
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "74536289"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-qmarkets-idea--innovation-management"></a>Tutorial: Azure Ative Diretório integração única (SSO) com Qmarkets Idea & Innovation Management

Neste tutorial, aprenderá a integrar a Qmarkets Idea & Innovation Management com o Azure Ative Directory (Azure AD). Quando integrar a Qmarkets Idea & Innovation Management com a Azure AD, pode:

* Controle em Azure AD que tem acesso a Qmarkets Idea & Innovation Management.
* Permitir que os seus utilizadores sejam automaticamente inscritos na Qmarkets Idea & Innovation Management com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Qmarkets Idea & Innovation Management um único sign-on (SSO) permitiu a subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.



* Qmarkets Idea & Innovation Management apoia **SP e IDP** iniciadoS SSO
* Qmarkets Idea & Innovation Management suporta o provisionamento de utilizadores **justos no tempo**


## <a name="adding-qmarkets-idea--innovation-management-from-the-gallery"></a>Adicionar A Ideia de Mercados Qmarkets & A Gestão da Inovação da galeria

Para configurar a integração da Qmarkets Idea & Innovation Management no Azure AD, é necessário adicionar qmarkets Idea & Innovation Management da galeria à sua lista de aplicações geridas do SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No **Add da** secção galeria, o tipo **Qmarkets Idea & Innovation Management** na caixa de pesquisa.
1. Selecione **Qmarkets Idea & Innovation Management** do painel de resultados e, em seguida, adicione a app. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on-for-qmarkets-idea--innovation-management"></a>Configure e teste Azure AD single sign-on for Qmarkets Idea & Innovation Management

Configure e teste Azure AD SSO com Qmarkets Idea & Innovation Management usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado na Qmarkets Idea & Innovation Management.

Para configurar e testar o Azure AD SSO com a Ideia de Mercado si & A Gestão da Inovação, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure Qmarkets Idea & Innovation Management SSO](#configure-qmarkets-idea--innovation-management-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    1. **[Create Qmarkets Idea & Innovation Management user](#create-qmarkets-idea--innovation-management-test-user)** - para ter uma contrapartida de B.Simon em Qmarkets Idea & Innovation Management que está ligada à representação da AD Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações de Gestão **de Inovação da Ideia de Qmarkets & Inovação,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** caso deseje configurar a aplicação no modo iniciado do **IDP,** introduza os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:`https://<app_url>/sso/saml2/metadata/qmarkets_sp_<endpoint_id>`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://<app_url>/sso/saml2/acs/qmarkets_sp_<endpoint_id>`

1. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://<app_url>/sso/saml2/endpoint/qmarkets_sp_<endpoint_id>`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação, resposta real e URL de sinalização. Contacte [a Qmarkets Idea & equipa](mailto:support@qmarkets.net) de apoio ao Cliente de Gestão de Inovação para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. No **set single sign-on com** a página SAML, na secção Certificado de **Assinatura SAML,** clique no botão de cópia para copiar o Url de **Metadados da Federação** da Aplicação e guarde-o no seu computador.

    ![O link de descarregamento do Certificado](common/copy-metadataurl.png)

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

Nesta secção, permitirá à B.Simon utilizar o leton único azure, concedendo acesso à Qmarkets Idea & Innovation Management.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Qmarkets Idea & Innovation Management.**
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-qmarkets-idea--innovation-management-sso"></a>Configure Qmarkets Idea & Innovation Management SSO

Para configurar um único sign-on no lado da **Qmarkets Idea & Innovation Management,** você precisa enviar o Url de **Metadados da Federação de Aplicações** para Qmarkets Idea & equipa de apoio à [Inovação.](mailto:support@qmarkets.net) Eles definiram esta definição para ter a ligação SAML SSO corretamente definida em ambos os lados.

### <a name="create-qmarkets-idea--innovation-management-test-user"></a>Criar Qmarkets Idea & Utilizador de testes de Inovação

Nesta secção, uma utilizadora chamada Britta Simon é criada na Qmarkets Idea & Innovation Management. Qmarkets Idea & Innovation Management suporta o fornecimento de utilizadores just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir na Qmarkets Idea & Innovation Management, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar na Ideia de Qmarkets & innovation Management no Painel de Acesso, deve ser automaticamente inscrito na Qmarkets Idea & Innovation Management para a qual criou o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente a Ideia de Mercados Qmarkets & A Gestão de Inovação com a Azure AD](https://aad.portal.azure.com/)

