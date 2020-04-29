---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com OpenAthens [ SSO) integração com OpenAthens [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o OpenAthens.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: dd4adfc7-e238-41d5-8b25-1811f08078b6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 43fc2272a81672ea613bdcbe17c5381e99cafbff
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "73053200"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-openathens"></a>Tutorial: Azure Ative Directory integração de um único sign-on (SSO) com openAthens

Neste tutorial, você aprenderá a integrar OpenAthens com O Diretório Ativo Azure (Azure AD). Quando integrar o OpenAthens com o Azure AD, pode:

* Controlo em Azure AD que tem acesso a OpenAthens.
* Permita que os seus utilizadores sejam automaticamente inscritos no OpenAthens com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* OpenAthens single sign-on (SSO) enabled subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* OpenAthens suporta **IDP** iniciado SSO
* OpenAthens suporta fornecimento de utilizadores **justo no tempo**

## <a name="adding-openathens-from-the-gallery"></a>Adicionando OpenAthens da galeria

Para configurar a integração do OpenAthens em Azure AD, você precisa adicionar OpenAthens da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** digite **OpenAthens** na caixa de pesquisa.
1. Selecione **OpenAthens** do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-openathens"></a>Configure e teste Azure AD único sign-on para OpenAthens

Configure e teste Azure AD SSO com OpenAthens utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no OpenAthens.

Para configurar e testar o Azure AD SSO com o OpenAthens, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    * Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure OpenAthens SSO](#configure-openathens-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    * **[Create OpenAthens test user](#create-openathens-test-user)** - para ter uma contrapartida de B.Simon em OpenAthens que está ligada à representação azure AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **OpenAthens,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **basic SAML Configuration,** faça upload do ficheiro de **metadados**do Prestador de Serviços, as etapas para as quais são mencionadas mais tarde neste tutorial.

    a. Clique no **ficheiro de metadados de upload**.

    ![abrir metadados de upload de atenas](common/upload-metadata.png)

    b. Clique no logotipo da **pasta** para selecionar o ficheiro de metadados e clicar em **Carregar**.

    ![Openathens navegam em metadados de upload](common/browse-upload-metadata.png)

    c. Uma vez que o ficheiro de metadados é carregado com sucesso, o valor **do Identificador** fica automaticamente povoado na caixa de texto da secção de **configuração Básica SAML:**

    ![OpenAthens Domain e URLs informações únicas de inscrição](common/idp-identifier.png)

1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **metadados da Federação XML** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

1. Na secção **set Up OpenAthens,** copie os URL(s) adequados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use o único sign-on Azure, concedendo acesso ao OpenAthens.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **OpenAthens**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-openathens-sso"></a>Configure OpenAthens SSO

1. Numa janela diferente do navegador web, inscreva-se no site da sua empresa OpenAthens como administrador.

1. Selecione **Ligações** da lista sob o separador **Gestão.**

    ![Configurar o início de sessão único](./media/openathens-tutorial/tutorial_openathens_application1.png)

1. Selecione **SAML 1.1/2.0**, e, em seguida, selecione o botão **Configurar.**

    ![Configurar o início de sessão único](./media/openathens-tutorial/tutorial_openathens_application2.png)

1. Para adicionar a configuração, selecione o botão **Browse** para carregar o ficheiro metadados .xml que descarregou do portal Azure e, em seguida, selecione **Adicionar**.

    ![Configurar o início de sessão único](./media/openathens-tutorial/tutorial_openathens_application3.png)

1. Execute os seguintes passos sob o separador **Detalhes.**

    ![Configurar o início de sessão único](./media/openathens-tutorial/tutorial_openathens_application4.png)

    a. No mapeamento do **nome Display,** selecione **Use o atributo**.

    b. No **nome display atribuir** caixa de `http://schema.microsoft.com/identity/claims/displayname`texto, introduza o valor .

    c. No **mapeamento único do utilizador,** selecione **Use atributo**.

    d. Na caixa de texto de atributo `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`único do **utilizador,** introduza o valor .

    e. Em **Status,** selecione todas as três caixas de verificação.

    f. Em **Criar contas locais,** selecione **automaticamente**.

    g. Selecione **Guardar alterações**.

    h. A partir do separador **</> Relying Party,** copie o **URL de Metadados** e abra-o no navegador para descarregar o ficheiro XML de **metadados SP.** Faça upload deste ficheiro de metadados SP na secção **basic SAML Configuração** em Azure AD.

    ![Configurar o início de sessão único](./media/openathens-tutorial/tutorial_openathens_application5.png)

### <a name="create-openathens-test-user"></a>Criar o utilizador de teste OpenAthens

Nesta secção, uma utilizadora chamada Britta Simon é criada em OpenAthens. A OpenAthens suporta o fornecimento de **utilizadores just-in-time**, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no OpenAthens, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo OpenAthens no Painel de Acesso, deve ser automaticamente inscrito no OpenAthens para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente OpenAthens com Azure AD](https://aad.portal.azure.com/)