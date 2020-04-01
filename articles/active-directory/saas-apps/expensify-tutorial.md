---
title: 'Tutorial: Integração do Diretório Ativo Azure com a Expensify Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Expensify.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1e761484-7a2f-4321-91f4-6d5d0b69344e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e9ed129bccbd763bf4459fa7818e0dba4e7e65b5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "73156534"
---
# <a name="tutorial-integrate-expensify-with-azure-active-directory"></a>Tutorial: Integrar a Expensifique com o Diretório Ativo Azure

Neste tutorial, aprenderá a integrar a Expensify com o Azure Ative Directory (Azure AD). Quando integrar a Expensify com a Azure AD, pode:

* Controlo em Azure AD que tem acesso a Expensify.
* Permita que os seus utilizadores sejam automaticamente inscritos para expensificar com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Expensifique a subscrição ativada por um único sinal (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Expensificar apoia **SP** iniciado SSO

## <a name="adding-expensify-from-the-gallery"></a>Adicionando Expensify da galeria

Para configurar a integração da Expensify em Azure AD, precisa adicionar Expensify da galeria à sua lista de aplicações geridas do SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No **Add da** secção galeria, **escreva Expensifique** na caixa de pesquisa.
1. **Selecione Expensify** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-expensify"></a>Configure e teste Azure AD único signo para expensificar

Configure e teste Azure AD SSO com Expensify utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado na Expensify.

Para configurar e testar o Azure AD SSO com expensifique, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
2. **[Configure Expensify SSO](#configure-expensify-sso)** - para configurar as definições de início de sessão individuais no lado da aplicação.
    1. **[Criar expensifique o utilizador](#create-expensify-test-user)** do teste - para ter uma contrapartida de B.Simon em Expensify que esteja ligada à representação da AD Azure do utilizador.
6. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **Expensify,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **Select a Single sign-on,** selecione **SAML**.
1. Na configuração do Single Sign-On com a página **SAML,** clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** introduza os valores para os seguintes campos:

    a. No **Sign on URL** text box, digite um URL:`https://www.expensify.com/authentication/saml/login`

    b. Na caixa de texto **identificador (Id da entidade),** digite um URL:`https://www.expensify.com`

    c. b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://www.expensify.com/authentication/saml/loginCallback?domain=<yourdomain>`

    > [!NOTE]
    > O valor url de resposta não é real. Atualize este valor com o URL de Resposta real. Contacte a equipa de suporte do [Cliente](mailto:help@expensify.com) para obter este valor. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. Na configuração de um único sinal com página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **Metadados XML** e selecione **Download** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

1. Na secção **Configurar Expensifique,** copie os URL(s) adequados com base na sua exigência.

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

Nesta secção, permitirá que b.Simon use o único sign-on Azure, concedendo acesso à Expensify.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, **selecione Expensify**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-expensify-sso"></a>Configure Expensifique SSO

Para ativar o SSO na Expensify, primeiro é necessário ativar o Controlo de **Domínio** na aplicação. Pode ativar o Controlo de Domínio na aplicação através dos passos listados [aqui](https://help.expensify.com/domain-control). Para apoio adicional, trabalhe com a equipa de apoio ao [Cliente Expensify.](mailto:help@expensify.com) Uma vez ativado o Controlo de Domínio, siga estes passos:

![Configurar um único sinal](./media/expensify-tutorial/tutorial_expensify_51.png)

1. Inscreva-se na sua aplicação Expensify.

2. No painel esquerdo, clique em **Definições** e navegue para **SAML**.

3. Alternar a opção **de login SAML** como **Ativado**.

4. Abra os metadados da Federação descarregados a partir de Azure AD no bloco de notas, copie o conteúdo e, em seguida, cole-o na caixa de texto de Metadados do Fornecedor de **Identidade.**

### <a name="create-expensify-test-user"></a>Criar expensificar o utilizador do teste

Nesta secção, cria-se um utilizador chamado B.Simon em Expensifique. Trabalhe com a equipa de suporte do [Cliente Expensify](mailto:help@expensify.com) para adicionar os utilizadores na plataforma Expensify.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Expensify no Painel de Acesso, deve ser automaticamente inscrito no Expensify para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)