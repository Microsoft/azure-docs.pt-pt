---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com NS1 SSO para Azure [ Azure] Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o NS1 SSO para o Azure.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 24a1afb6-b8b6-4787-bd4b-8fe3a32f8def
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/12/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c5ba34aeb97808c19c78f187dcc68309893d1d1b
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2020
ms.locfileid: "77429748"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ns1-sso-for-azure"></a>Tutorial: Azure Ative Directory integração de um único sign-on (SSO) com NS1 SSO para Azure

Neste tutorial, você vai aprender a integrar NS1 SSO para Azure com Azure Ative Directory (Azure AD). Quando integrar ns1 SSO para Azure com Azure AD, pode:

* Controlo em Azure AD que tem acesso a NS1 SSO para Azure.
* Ative que os seus utilizadores sejam automaticamente inscritos no NS1 SSO para o Azure com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* NS1 SSO para assinatura única (SSO) ativada por NS1 SSO.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* NS1 SSO para Azure suporta **SP e IDP** iniciado SSO
* Assim que configurar o NS1 SSO para o Azure, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controlo](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)da sessão com o Microsoft Cloud App Security .


## <a name="adding-ns1-sso-for-azure-from-the-gallery"></a>Adicionar NS1 SSO para Azure a partir da galeria

Para configurar a integração do NS1 SSO para azure em Azure AD, você precisa adicionar NS1 SSO para Azure da galeria para a sua lista de aplicações saaS geridas.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No **Add da** secção galeria, digite **NS1 SSO para Azure** na caixa de pesquisa.
1. Selecione **NS1 SSO para Azure** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on-for-ns1-sso-for-azure"></a>Configure e teste Azure AD único sign-on para NS1 SSO para Azure

Configure e teste Azure AD SSO com NS1 SSO para Azure utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no NS1 SSO para o Azure.

Para configurar e testar o Azure AD SSO com NS1 SSO para Azure, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure NS1 SSO para Azure SSO](#configure-ns1-sso-for-azure-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    1. **[Crie NS1 SSO para utilizador](#create-ns1-sso-for-azure-test-user)** de teste Azure - para ter uma contrapartida de B.Simon no NS1 SSO para o Azure que esteja ligada à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configure Azure AD SSO

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **DAN1 SSO para Azure,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** caso deseje configurar a aplicação no modo iniciado do **IDP,** introduza os valores para os seguintes campos:

    a. Na caixa de texto **identificador,** digite o URL: `https://api.nsone.net/saml/metadata`

    b. Na caixa de texto **URL de resposta,** escreva um URL utilizando o seguinte padrão: `https://api.nsone.net/saml/sso/<ssoid>`

1. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    Na caixa de texto **de URL sign-on,** escreva o URL: `https://my.nsone.net/#/login/sso`

    > [!NOTE]
    > O valor url de resposta não é real. Atualizar valor URL resposta com o URL de resposta real. Contacte a equipa de suporte do [Cliente AZUR1 para](mailto:techops@nsone.net) obter o valor. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. Ns1 SSO para aplicação Azure espera as afirmações do SAML num formato específico. Configure as seguintes reclamações para esta aplicação. Pode gerir os valores destes atributos a partir da secção **Atributos** do Utilizador na página de integração de aplicações. Na configuração do Open Sign-On com a página **SAML,** clique no botão **Editar** para abrir o diálogo **de Atributos do Utilizador.**

    ![Secção de atributos](./media/ns1-sso-for-azure-tutorial/attribute-edit-option.png)

1. Clique no nome do atributo para editar a reclamação.

    ![Secção de atributos](./media/ns1-sso-for-azure-tutorial/attribute-claim-edit.png)

1. Selecione **A Transformação**.

    ![Secção de atributos](./media/ns1-sso-for-azure-tutorial/prefix-edit.png)

1. Na secção **de transformação Gerir,** execute os seguintes passos:

    ![Secção de atributos](./media/ns1-sso-for-azure-tutorial/prefix-added.png)

    1. Selecione **ExactMailPrefix()** como **Transformação**.

    1. Selecione **user.userprincipalname** as **Parameter 1**.

    1. Clique em **Adicionar**.

    1. Clique em **Guardar**

1. No **set single sign-on com** a página SAML, na secção Certificado de **Assinatura SAML,** clique no botão de cópia para copiar o Url de **Metadados da Federação** da Aplicação e guarde-o no seu computador.

    ![O link de download de certificado](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta secção, você vai criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, **selecione Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **de nome do utilizador,** introduza o username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, permitirá que b.Simon utilize um único sign-on Azure, concedendo acesso ao NS1 SSO para o Azure.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **NS1 SSO para Azure**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-ns1-sso-for-azure-sso"></a>Configure NS1 SSO para SSO Azure

Para configurar um único sinal no **NS1 SSO para** o lado Azure, você precisa enviar o Url de **Metadados da Federação de Aplicações** para [NS1 SSO para a equipa](mailto:techops@nsone.net)de suporte Azure . Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.

### <a name="create-ns1-sso-for-azure-test-user"></a>Criar NS1 SSO para utilizador de teste Azure

Nesta secção, cria-se um utilizador chamado B.Simon no NS1 SSO para o Azure. Trabalhe com a equipa de suporte NS1 SSO para o Azure para adicionar os utilizadores na plataforma NS1 SSO para a plataforma Azure. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clicar no NS1 SSO para azulejos Azure no Painel de Acesso, deve ser automaticamente inscrito no NS1 SSO para Azure para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [O que é o acesso condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente NS1 SSO para Azure com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)