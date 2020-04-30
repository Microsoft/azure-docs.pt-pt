---
title: 'Tutorial: Integração de Diretório Sonâmático Azure com a Aha! | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a Aha!.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ad955d3d-896a-41bb-800d-68e8cb5ff48d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/09/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a63872680d28664c6d5a7ff109f6de72817173d5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "68989653"
---
# <a name="tutorial-integrate-aha-with-azure-active-directory"></a>Tutorial: Integrar Aha! com Diretório Ativo Azure

Neste tutorial, você vai aprender a integrar Aha! com o Azure Ative Directory (Azure AD). Quando integrar a Aha! com a AD Azure, pode:

* Controle em Azure AD que tem acesso a Aha!.
* Ative que os seus utilizadores sejam automaticamente inscritos na Aha! com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Aha! única subscrição ativada por sessão (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Aha! suporta **SP** iniciado SSO
* Aha! suporta o fornecimento de utilizadores **Just In Time**

## <a name="adding-aha-from-the-gallery"></a>Adicionando Aha! da galeria

Para configurar a integração de Aha! em Azure AD, você precisa adicionar Aha! desde a galeria até à sua lista de aplicações geridas do SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **da galeria,** tipo **Aha!** na caixa de pesquisa.
1. Selecione **Aha!** do painel de resultados e, em seguida, adicionar a app. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-aha"></a>Configure e teste Azure AD único sign-on para Aha!

Configure e teste Azure AD SSO com Aha! utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em Aha!.

Para configurar e testar o Azure AD SSO com a Aha!, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
2. **[Configure Aha! SSO](#configure-aha-sso)** - para configurar as definições de início de sessão simples no lado da aplicação.
    1. **[Create Aha! test user](#create-aha-test-user)** - para ter uma contrapartida de B.Simon em Aha! que está ligada à representação da AD Azure do utilizador.
3. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na **Aha!** página de integração de aplicações, encontre a secção **Gerir** e selecione **single sign-on**.
1. Na página **Select a Single sign-on,** selecione **SAML**.
1. Na configuração do Single Sign-On com a página **SAML,** clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    a. No **Sign on URL** text box, digite um URL utilizando o seguinte padrão:`https://<companyname>.aha.io/session/new`

    b. Na caixa de texto **identificador (Id da entidade),** digite um URL utilizando o seguinte padrão:`https://<companyname>.aha.io`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e identificador. Contate [Aha! Equipa de apoio](https://www.aha.io/company/contact) ao cliente para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

4. Na configuração de um único sign-on com a página **SAML,** na secção certificado de **assinatura SAML,** encontre **metadados da Federação XML** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

6. No **set up Aha!** secção, copie os URL(s) adequados com base na sua exigência.

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

Nesta secção, você permitirá que B.Simon use o único sign-on Azure, concedendo acesso a Aha!.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Aha!**
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-aha-sso"></a>Configure Aha! SSO

1. Para automatizar a configuração dentro de Aha!, precisa de instalar a extensão de **navegador Secure-in das Minhas Aplicações** clicando em **instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique em **Configurar Aha!** vai direcioná-lo para o Aha! aplicação. A partir daí, forneça as credenciais de administração para assinar em Aha! A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-8.

    ![Configuração de configuração de configuração](common/setup-sso.png)

3. Se quiser preparar a Aha! manualmente, abra uma nova janela do navegador web e assine no seu Aha! site da empresa como administrador e executa os seguintes passos:

4. No menu em cima, clique em **Definições**.

    ![Definições](./media/aha-tutorial/IC798950.png "Definições")

5. Clique na **conta**.
  
    ![Perfil](./media/aha-tutorial/IC798951.png "Perfil")

6. Clique em **Segurança e único sinal.**

    ![Segurança e inscrição única](./media/aha-tutorial/IC798952.png "Segurança e inscrição única")

7. Na secção **De Sessão Individual,** como Fornecedor de **Identidade,** selecione **SAML2.0**.

    ![Segurança e inscrição única](./media/aha-tutorial/IC798953.png "Segurança e inscrição única")

8. Na página de configuração **single sign-on,** execute os seguintes passos:

    ![Inscrição única](./media/aha-tutorial/IC798954.png "Início de Sessão Único")

    a. Na caixa de texto **Name,** digite um nome para a sua configuração.

    b. Para **configurar a utilização, selecione** **Ficheiro de Metadados**.

    c. Para fazer o upload do ficheiro de metadados descarregado, clique em **Navegar**.

    d. Clique em **Atualizar**.

### <a name="create-aha-test-user"></a>Crie Aha! utilizador de teste

Nesta secção, um utilizador chamado B.Simon é criado em Aha!. Aha! suporta o fornecimento de utilizadores just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir em Aha!, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no Aha! azulejo no Painel de Acesso, deve ser automaticamente inscrito no Aha! para o qual criou o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

