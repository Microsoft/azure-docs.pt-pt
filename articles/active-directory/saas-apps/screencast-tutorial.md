---
title: 'Tutorial: Azure Ative Directory integração individual (SSO) com Screencast-O-Matic [ Integração de um único sign-on do Diretório Ativo azure com screencast-O-Matic ] Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Screencast-O-Matic.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 525ad47d-5488-40e2-aeaf-ae6183745682
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc887e95b6fa6f8b17fbbb3dbaae5105385a07fa
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "74132151"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-screencast-o-matic"></a>Tutorial: Azure Ative Directory integração de um único sign-on (SSO) com screencast-O-Matic

Neste tutorial, você vai aprender a integrar Screencast-O-Matic com O Diretório Ativo Azure (Azure AD). Quando integrar o Screencast-O-Matic com o Azure AD, pode:

* Controlo em Azure AD que tem acesso ao Screencast-O-Matic.
* Ative que os seus utilizadores sejam automaticamente inscritos no Screencast-O-Matic com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Screencast-O-Matic single sign-on (SSO) enabled subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Screencast-O-Matic suporta **SP** iniciado SSO
* Screencast-O-Matic suporta o fornecimento de utilizadores **justo no tempo**

## <a name="adding-screencast-o-matic-from-the-gallery"></a>Adicionar Screencast-O-Matic da galeria

Para configurar a integração do Screencast-O-Matic no Azure AD, precisa de adicionar screencast-O-Matic da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **da galeria,** digite **Screencast-O-Matic** na caixa de pesquisa.
1. Selecione **Screencast-O-Matic** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-screencast-o-matic"></a>Configure e teste Azure AD único sign-on para Screencast-O-Matic

Configure e teste Azure AD SSO com Screencast-O-Matic utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Screencast-O-Matic.

Para configurar e testar o Azure AD SSO com screencast-O-Matic, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    * Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure screencast-O-Matic SSO](#configure-screencast-o-matic-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    * **[Crie o utilizador de teste Screencast-O-Matic](#create-screencast-o-matic-test-user)** - para ter uma contrapartida de B.Simon no Screencast-O-Matic que esteja ligada à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **Screencast-O-Matic,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** introduza os valores para os seguintes campos:

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://screencast-o-matic.com/<InstanceName>`

    > [!NOTE]
    > O valor não é real. Atualize o valor com o URL de Sign-On real. Contacte a equipa de suporte ao [cliente Screencast-O-Matic](mailto:support@screencast-o-matic.com) para obter o valor. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **metadados da Federação XML** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

1. Na secção **Screencast-O-Matic,** copie os URL(s) adequados com base no seu requisito.

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

Nesta secção, permitirá que b.Simon utilize um único sign-on Azure, concedendo acesso ao Screencast-O-Matic.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Screencast-O-Matic**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-screencast-o-matic-sso"></a>Configure Screencast-O-Matic SSO

1. Para automatizar a configuração dentro do Screencast-O-Matic, é necessário instalar a extensão de **navegador Secure-in das Minhas Aplicações** clicando em **instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

1. Depois de adicionar extensão ao navegador, clique em **Configurar Screencast-O-Matic** irá direcioná-lo para a aplicação Screencast-O-Matic. A partir daí, forneça as credenciais de administração para assinar no Screencast-O-Matic. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-11.

    ![Configuração de configuração de configuração](common/setup-sso.png)

1. Se pretender configurar o Screencast-O-Matic manualmente, abra uma nova janela do navegador web e inscreva-se no site da empresa Screencast-O-Matic como administrador e execute os seguintes passos:

1. Clique em **Subscrição**.

    ![A Subscrição](./media/screencast-tutorial/tutorial_screencast_sub.png)

1. Na secção da **página de acesso,** clique em **configurar**.

    ![O Acesso](./media/screencast-tutorial/tutorial_screencast_setup.png)

1. Na página de acesso de **configuração,** execute os seguintes passos.

1. Na secção URL de **acesso,** escreva o seu nome de caso na caixa de texto especificada.

    ![O Acesso](./media/screencast-tutorial/tutorial_screencast_access.png)

1. Selecione **Exigir utilizador de domínio** sob a secção de restrição de utilizador **SAML (opcional).**

1. Em **'Upload IDP Metadata XML File',** clique **em escolher ficheiro** para fazer o upload dos metadados que descarregou do portal Azure.

1. Clique em **OK**.

    ![O Acesso](./media/screencast-tutorial/tutorial_screencast_save.png)

### <a name="create-screencast-o-matic-test-user"></a>Criar o utilizador de teste Screencast-O-Matic

Nesta secção, um utilizador chamado Britta Simon é criado no Screencast-O-Matic. O Screencast-O-Matic suporta o fornecimento de utilizadores just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no Screencast-O-Matic, um novo é criado após a autenticação. Se precisar de criar um utilizador manualmente, contacte a equipa de suporte ao [Cliente Screencast-O-Matic](mailto:support@screencast-o-matic.com).

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Screencast-O-Matic no Painel de Acesso, deve ser automaticamente inscrito no Screencast-O-Matic para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente Screencast-O-Matic com Anúncio Azure](https://aad.portal.azure.com/)