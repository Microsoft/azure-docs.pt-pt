---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com ice Contact Center [ Azure Ative Diretório) integração com ice Contact Center [ Centro de Contacto de Gelo] Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Ice Contact Center.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 85c964d8-7c0f-458d-8df5-b7d0768f298d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 05/15/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f53b3f8dd2a324e4eb3e4b6c073de478bff97a3
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83666085"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ice-contact-center"></a>Tutorial: Azure Ative Directory integração de um único sign-on (SSO) com o Ice Contact Center

Neste tutorial, você vai aprender a integrar o Centro de Contacto de Gelo com o Azure Ative Directory (Azure AD). Quando integrar o Centro de Contacto de Gelo com a AD Azure, pode:

* Controlo em Azure AD que tem acesso ao Centro de Contacto de Gelo.
* Ative que os seus utilizadores sejam automaticamente inscritos no Ice Contact Center com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Ice Contact Center single sign-on (SSO) enabled subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Ice Contact Center suporta **SP** iniciado SSO
* Assim que configurar o Ice Contact Center, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controlo](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)da sessão com o Microsoft Cloud App Security .

## <a name="adding-ice-contact-center-from-the-gallery"></a>Adicionar centro de contacto de gelo a partir da galeria

Para configurar a integração do Ice Contact Center em Azure AD, é necessário adicionar o Ice Contact Center da galeria à sua lista de aplicações geridas do SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** digite **ice Contact Center** na caixa de pesquisa.
1. Selecione **ice Contact Center** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-ice-contact-center"></a>Configure e teste Azure AD único sinal para centro de contacto de gelo

Configure e teste Azure AD SSO com ice Contact Center utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Ice Contact Center.

Para configurar e testar o Azure AD SSO com o Centro de Contacto de Gelo, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. Configure o Centro de Contacto de **[Gelo SSO](#configure-ice-contact-center-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    1. **[Crie o utilizador](#create-ice-contact-center-test-user)** do teste do Ice Contact Center - para ter uma contrapartida de B.Simon no Ice Contact Center que esteja ligada à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações do Centro de **Contacto de Gelo,** encontre a secção **Gerir** e selecione um **único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** introduza os valores para os seguintes campos:

    a. No **Sign on URL** text box, digite um URL utilizando o seguinte padrão:`https://<TENANT>.iceuc.com/iceManager`

    b. Na caixa de texto **identificador (Id** da entidade), digite um URL utilizando um dos seguintes padrões:

    | | |
    |-|-|
    | `https://<TENANT>-imrpool.icescape365.com:PORT/identity`|
    | `https://<TENANT>-imrpool.icescape.com:PORT/identity`|
    | `https://<TENANT>-imrpool.iceuc.com:PORT/identity`|

    c. Na caixa de texto **URL de resposta,** escreva um URL utilizando um dos seguintes padrões:

    | | |
    |-|-|
    | `https://<TENANT>-imrpool.icescape365.com:PORT/identity`|
    | `https://<TENANT>-imrpool.icescape.com:PORT/identity`|
    | `https://<TENANT>-imrpool.iceuc.com:PORT/identity`|

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL, Identifier e URL de Resposta real. [Contacte](mailto:support@computer-talk.com) a equipa de suporte do Cliente do Ice Contact Center para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. No **set single sign-on com** a página SAML, na secção Certificado de **Assinatura SAML,** clique no botão de cópia para copiar o Url de **Metadados da Federação** da Aplicação e guarde-o no seu computador.

    ![O link de descarregamento do Certificado](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, você vai criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, **selecione Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo de **nome do Utilizador,** introduza o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permitirá que b.Simon utilize um único sinal de Azure, concedendo acesso ao Ice Contact Center.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **ice Contact Center**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-ice-contact-center-sso"></a>Configure ice Contact Center SSO

Para configurar um único sinal no lado do Centro de Contacto de **Gelo,** você precisa enviar o Url de **Metadados da Federação de Aplicações** para a equipa de suporte do Centro de [Contacto de Gelo](mailto:support@computer-talk.com). Eles definiram esta definição para ter a ligação SAML SSO corretamente definida em ambos os lados.

### <a name="create-ice-contact-center-test-user"></a>Criar o utilizador de teste do Centro de Contacto de Gelo

Nesta secção, cria-se uma utilizadora chamada Britta Simon no Centro de Contacto de Gelo. Trabalhe com a equipa de suporte do [Ice Contact Center](mailto:support@computer-talk.com) para adicionar os utilizadores na plataforma ice Contact Center. Os utilizadores devem ser criados e ativados antes de utilizar um único sinal.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo do Centro de Contacto de Gelo no Painel de Acesso, deverá ser automaticamente inscrito no Centro de Contacto de Gelo para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o Centro de Contacto de Gelo com a AD Azure](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger o Centro de Contacto de Gelo com visibilidade e controlos avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

