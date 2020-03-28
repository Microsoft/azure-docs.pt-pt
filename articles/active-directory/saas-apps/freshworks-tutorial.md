---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com Freshworks [ Integração de Diretórios Ativos do Azure) com Freshworks [ Integração de Diretórios Ativos de Azure) com Freshworks [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a Freshworks.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 697c3509-0e3a-4b05-9d5b-9ded4269eb60
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b73b8769b5f7360ba4ad778600b1000b8b3f7bc7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "72376414"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-freshworks"></a>Tutorial: Azure Ative Directory integração individual (SSO) com Freshworks

Neste tutorial, você vai aprender a integrar Freshworks com azure Ative Directory (Azure AD). Quando integrar freshworks com a Azure AD, pode:

* Controlo em Azure AD que tem acesso a Freshworks.
* Ative que os seus utilizadores sejam automaticamente inscritos na Freshworks com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Freshworks single sign-on (SSO) enabled subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Freshworks suporta **SP** iniciado SSO

## <a name="adding-freshworks-from-the-gallery"></a>Adicionar Freshworks da galeria

Para configurar a integração da Freshworks em Azure AD, precisa de adicionar Freshworks da galeria à sua lista de aplicações geridas do SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** digite **Freshworks** na caixa de pesquisa.
1. Selecione **Freshworks** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-freshworks"></a>Configure e teste Azure AD único sign-on para Freshworks

Configure e teste Azure AD SSO com Freshworks utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em Freshworks.

Para configurar e testar o Azure AD SSO com freshworks, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure Freshworks SSO](#configure-freshworks-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    1. **[Create Freshworks test user](#create-freshworks-test-user)** - para ter uma contrapartida de B.Simon em Freshworks que está ligada à representação da AD Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **Freshworks,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** introduza os valores para os seguintes campos:

    a. No **Sign on URL** text box, digite um URL utilizando o seguinte padrão:`https://<SUBDOMAIN>.freshworks.com/login`

    b. Na caixa de texto **identificador (Id da entidade),** digite um URL utilizando o seguinte padrão:`https://<SUBDOMAIN>.freshworks.com/sp/SAML/<MODULE_ID>/metadata`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e identificador. Contacte a equipa de suporte ao Cliente da [Freshworks](mailto:support@freshworks.com) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

1. Para modificar as opções de **Assinatura** de acordo com o seu requisito, clique no botão **Editar** para abrir o diálogo do Certificado  **de Assinatura SAML.**

     ![image](common/edit-certificate.png)

     ![Configuração de freshworks](./media/freshworks-tutorial/response.png)

    a. Selecione **A resposta SAML de sinal** como **opção**de assinatura .

    b. Clique em **Guardar**.

1. Na secção **Deconfigurar Freshworks,** copie os URL(s) adequados com base no seu requisito.

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

Nesta secção, permitirá que b.Simon utilize um único sign-on Azure, concedendo acesso à Freshworks.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Freshworks**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-freshworks-sso"></a>Configure Freshworks SSO

1. Abra uma nova janela do navegador web e inscreva-se no site da sua empresa Freshworks como administrador e execute os seguintes passos:

2. Do lado esquerdo do menu, clique no ícone **De segurança,** em seguida, verifique a opção **de entrada individual** e selecione **SAML SSO** em métodos de **autenticação**.

    ![Configuração de freshworks](./media/freshworks-tutorial/configure01.png)

3. Na secção **de inscrição individual,** efetue os seguintes passos:

    ![Configuração de freshworks](./media/freshworks-tutorial/configure02.png)

    a. Clique em **Copiar** para copiar o ID da entidade do Fornecedor de **Serviços (SP)** para a sua instância e cole-o na caixa de texto **Identificador (Id da Entidade)** na secção **de configuração Básica SAML** no portal Azure.

    b. No ID de Entidade fornecido pela caixa de texto **IDP,** colar o valor do **identificador AD Azure,** que copiou do portal Azure.

    c. Na caixa de texto **URL SAML SSO,** colá o valor URL de **Login,** que copiou do portal Azure.

    d. Abra o certificado codificado Base64 no bloco de notas, copie o seu conteúdo e cole-o na caixa de texto do certificado de **segurança.**

    e. Clique em **Guardar**.

### <a name="create-freshworks-test-user"></a>Criar o utilizador de teste Freshworks

Nesta secção, cria-se um utilizador chamado B.Simon em Freshworks. Trabalhe com a equipa de suporte ao [Cliente Freshworks](mailto:support@freshworks.com) para adicionar os utilizadores na plataforma Freshworks. Os utilizadores devem ser criados e ativados antes de utilizar um único sinal. 

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Freshworks no Painel de Acesso, deve ser automaticamente inscrito no Freshworks para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente freshworks com Azure AD](https://aad.portal.azure.com/)

