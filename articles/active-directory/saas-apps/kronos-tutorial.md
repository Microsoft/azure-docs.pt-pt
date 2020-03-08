---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com a Kronos  Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a Kronos.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e28d6191-c375-43c6-b2df-22daa88d9939
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/13/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ae90b729db51287f2a98caa36ae43d83a2a207c
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2020
ms.locfileid: "78897799"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-kronos"></a>Tutorial: Azure Ative Directory integração individual (SSO) com a Kronos

Neste tutorial, você vai aprender a integrar kronos com o Azure Ative Directory (Azure AD). Quando integrar a Kronos com a Azure AD, pode:

* Controlo em Azure AD que tem acesso a Kronos.
* Permita que os seus utilizadores sejam automaticamente inscritos na Kronos com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A subscrição ativada pela Kronos (SSO) permitiu a subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Kronos apoia **IDP** iniciado SSO

## <a name="adding-kronos-from-the-gallery"></a>Adicionando Kronos da galeria

Para configurar a integração da Kronos em Azure AD, você precisa adicionar Kronos da galeria à sua lista de aplicações saaS geridas.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **da galeria,** digite **Kronos** na caixa de pesquisa.
1. Selecione **Kronos** do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-kronos"></a>Configure e teste Azure AD único sign-on para Kronos

Configure e teste Azure AD SSO com Kronos utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em Kronos.

Para configurar e testar o Azure AD SSO com a Kronos, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure kronos SSO](#configure-kronos-sso)** - para configurar as definições de início de sessão simples no lado da aplicação.
    1. **[Crie](#create-kronos-test-user)** um utilizador de teste da Kronos - para ter uma contrapartida de B.Simon em Kronos que esteja ligada à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configure Azure AD SSO

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **da Kronos,** encontre a secção **Gerir** e selecione **single sign-on**.
1. Na página **Select a Single sign-on,** selecione **SAML**.
1. Na configuração do Single Sign-On com a página **SAML,** clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na **configuração de um único sign-on com página SAML,** introduza os valores para os seguintes campos:

    a. Na caixa de texto **identificador,** digite um URL utilizando o seguinte padrão: `https://<company name>.kronos.net/`

    b. Na caixa de texto **URL de resposta,** escreva um URL utilizando o seguinte padrão: `https://<company name>.kronos.net/wfc/navigator/logonWithUID`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação e resposta real. Contacte a equipa de [apoio ao Cliente da Kronos](https://www.kronos.in/contact/en-in/form) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. A aplicação Kronos espera as afirmações da SAML num formato específico. Configure as seguintes reclamações para esta aplicação. Pode gerir os valores destes atributos a partir da secção **Atributos** do Utilizador na página de integração de aplicações. Na configuração do Open Sign-On com a página **SAML,** clique no botão **Editar** para abrir o diálogo **de Atributos do Utilizador.**

    ![image](common/edit-attribute.png)

1. Na secção **Reivindicações** do Utilizador no diálogo **de Atributos** do Utilizador, configure o atributo token SAML como mostrado na imagem acima e execute os seguintes passos:

    a. Clique no **ícone Editar** para abrir o diálogo de reivindicações do **utilizador Gerir.**

    ![image](./media/kronos-tutorial/tutorial_usermail.png)

    ![image](./media/kronos-tutorial/tutorial_usermailedit.png)

    b. Na lista **de Transformação,** selecione **ExtractMailPrefix()** .

    c. Na lista **parametómetro 1,** selecione **user.userprincipalname**.

    d. Clique em **Guardar**.

1. Na configuração de um único sign-on com a página **SAML,** na secção certificado de **assinatura SAML,** encontre **metadados da Federação XML** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

1. Na secção **Deconfiguração de Kronos,** copie os URL(s) adequados com base no seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

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

Nesta secção, permitirá que b.Simon use o único sign-on Azure, concedendo acesso à Kronos.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Kronos**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-kronos-sso"></a>Configure Kronos SSO

Para configurar um único sign-on no lado da **Kronos,** você precisa enviar o descarregamento **da Federação Metadados XML** e URLs copiados apropriados do portal Azure para a equipa de [suporte da Kronos](https://www.kronos.in/contact/en-in/form). Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.

### <a name="create-kronos-test-user"></a>Criar o utilizador de teste da Kronos

Nesta secção, cria-se uma utilizadora chamada Britta Simon em Kronos. Trabalhe com a equipa de suporte da [Kronos](https://www.kronos.in/contact/en-in/form) para adicionar os utilizadores na plataforma Kronos. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

## <a name="test-sso"></a>Teste SSO

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clicar no azulejo Kronos no Painel de Acesso, deve ser automaticamente inscrito nos Kronos para os quais configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente Kronos com Azure AD](https://aad.portal.azure.com/)
