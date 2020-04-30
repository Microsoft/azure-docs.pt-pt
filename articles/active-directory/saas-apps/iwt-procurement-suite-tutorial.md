---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com iWT Procurement Suite [ IWT Procurement Suite ] Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a IWT Procurement Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 53e37c8d-85f4-4a9c-a46a-d4cdd91e38a1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/23/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f938c4e8692ce5836a46a990a607cfe229c3a705
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82204373"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-iwt-procurement-suite"></a>Tutorial: Azure Ative Directory integração individual de assinatura (SSO) com iWT Procurement Suite

Neste tutorial, você aprenderá a integrar a IWT Procurement Suite com o Azure Ative Directory (Azure AD). Quando integrar a IWT Procurement Suite com a Azure AD, pode:

* Controlo em Azure AD que tem acesso à IWT Procurement Suite.
* Ative que os seus utilizadores sejam automaticamente inscritos na IWT Procurement Suite com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* IWT Procurement Suite única subscrição ativada (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* IWT Procurement Suite suporta **IDP** iniciado SSO
* Assim que configurar a IWT Procurement Suite, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controlo](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)da sessão com o Microsoft Cloud App Security .

## <a name="adding-iwt-procurement-suite-from-the-gallery"></a>Adicionar iWT Procurement Suite da galeria

Para configurar a integração da IWT Procurement Suite em Azure AD, você precisa adicionar iWT Procurement Suite da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No **Add da** secção galeria, digite **a Suite de Aquisição IWT** na caixa de pesquisa.
1. Selecione **IWT Procurement Suite** do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on-for-iwt-procurement-suite"></a>Configure e teste Azure AD single sign-on para IWT Procurement Suite

Configure e teste Azure AD SSO com IWT Procurement Suite utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado na IWT Procurement Suite.

Para configurar e testar o Azure AD SSO com a IWT Procurement Suite, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure a Suíte de Aquisição IWT SSO](#configure-iwt-procurement-suite-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    1. **[Crie o utilizador de teste IWT Procurement Suite](#create-iwt-procurement-suite-test-user)** - para ter uma contrapartida de B.Simon na IWT Procurement Suite que está ligada à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **da IWT Procurement Suite,** encontre a secção **Gerir** e selecione um **único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na configuração de um único sessão com a página **SAML,** introduza os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:`https://[customersubdomain].ionwave.net/sso/[customerid]`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://[customersubdomain].ionwave.net/sso/[customerid]`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação e resposta real. Contacte a equipa de suporte do [Cliente iWT Procurement Suite](mailto:support@ionwave.net) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. A aplicação IWT Procurement Suite espera as afirmações do SAML num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração de atributos de token SAML. A imagem seguinte mostra a lista de atributos predefinidos, onde como **o emailaddress** é mapeado com **user.mail**. A aplicação IWT Procurement Suite espera que o **emailaddress** seja mapeado com o nome do **utilizador.userprincipal,** pelo que precisa editar o mapeamento do atributo clicando no ícone **Editar** e alterar o mapeamento do atributo.

    ![image](common/default-attributes.png)



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

Nesta secção, você permitirá que B.Simon use o único sign-on Azure, concedendo acesso à IWT Procurement Suite.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **IWT Procurement Suite**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-iwt-procurement-suite-sso"></a>Configure IWT Procurement Suite SSO

Para configurar um único sign-on no lado **da IWT Procurement Suite,** você precisa enviar o Url de **Metadados da Federação de Aplicações** para a equipe de [suporte iWT Procurement Suite](mailto:support@ionwave.net). Eles definiram esta definição para ter a ligação SAML SSO corretamente definida em ambos os lados.

### <a name="create-iwt-procurement-suite-test-user"></a>Criar o utilizador de teste iWT Procurement Suite

Nesta secção, cria-se uma utilizadora chamada Britta Simon na IWT Procurement Suite. Trabalhe com a equipa de [suporte da IWT Procurement Suite](mailto:support@ionwave.net) para adicionar os utilizadores na plataforma IWT Procurement Suite. Os utilizadores devem ser criados e ativados antes de utilizar um único sinal.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo IWT Procurement Suite no Painel de Acesso, deverá ser automaticamente inscrito na Suite de Aquisição IWT para a qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente a Suíte de Aquisição IWT com AD Azure](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger a IWT Procurement Suite com visibilidade e controlos avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

