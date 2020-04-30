---
title: 'Tutorial: Integração do Diretório Ativo Azure com kiteworks Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Kiteworks.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f7984aaf-ab1f-4a85-9646-a9523f5275d9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/11/2019
ms.author: jeedes
ms.openlocfilehash: 52b113d92fa83795e94d5179ea47ed5d9d9e9a26
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "68248906"
---
# <a name="tutorial-integrate-kiteworks-with-azure-active-directory"></a>Tutorial: Integrar Kiteworks com Diretório Ativo Azure

Neste tutorial, você vai aprender a integrar Kiteworks com o Azure Ative Directory (Azure AD). Quando integrar kiteworks com a Azure AD, pode:

* Controlo em Azure AD que tem acesso a Kiteworks.
* Ative que os seus utilizadores sejam automaticamente inscritos na Kiteworks com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode ter um mês de experiência gratuita [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Kiteworks single sign-on (SSO) ativado subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Kiteworks suporta **SP** iniciado SSO
* Kiteworks suporta o provisionamento de utilizadores **justo no tempo**

## <a name="adding-kiteworks-from-the-gallery"></a>Adicionar Kiteworks da galeria

Para configurar a integração das Kiteworks no Azure AD, precisa de adicionar Kiteworks da galeria à sua lista de aplicações geridas do SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** **digite Kiteworks** na caixa de pesquisa.
1. Selecione **Kiteworks** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Configure e teste Azure AD SSO com Kiteworks utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em Kiteworks.

Para configurar e testar o Azure AD SSO com kiteworks, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure Kiteworks SSO](#configure-kiteworks-sso)** - para configurar as definições de início de sessão simples no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Create Kiteworks test user](#create-kiteworks-test-user)** - para ter uma contrapartida de Britta Simon em Kiteworks que está ligada à representação da AD Azure do utilizador.
6. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **kiteworks,** encontre a secção **Gerir** e selecione **single sign-on**.
1. Na página **Select a Single sign-on,** selecione **SAML**.
1. Na configuração do Single Sign-On com a página **SAML,** clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** introduza os valores para os seguintes campos:

    a. No **Sign on URL** text box, digite um URL utilizando o seguinte padrão:`https://<kiteworksURL>.kiteworks.com`

    b. Na caixa de texto **identificador (Id da entidade),** digite um URL utilizando o seguinte padrão:`https://<kiteworksURL>/sp/module.php/saml/sp/saml2-acs.php/sp-sso`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e identificador. Contacte a equipa de suporte ao [cliente kiteworks](https://accellion.com/support) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. Na configuração de um único sinal com página **SAML,** na secção certificado de **assinatura SAML,** encontre **certificado (Base64)** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

1. Na secção De Configuração de **Kiteworks,** copie os URL(s) adequados com base no seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-kiteworks-sso"></a>Configure Kiteworks SSO

1. Inscreva-se no site da empresa Kiteworks como administrador.

1. Na barra de ferramentas na parte superior, clique em **Definições**.

    ![Configurar um único sinal](./media/kiteworks-tutorial/tutorial_kiteworks_06.png)

1. Na secção **autenticação e autorização,** clique em **Configuração SSO**.

    ![Configurar um único sinal](./media/kiteworks-tutorial/tutorial_kiteworks_07.png)

1. Na página de Configuração SSO, execute os seguintes passos:

    ![Configurar um único sinal](./media/kiteworks-tutorial/tutorial_kiteworks_09.png)

    a. **Selecione Autenticar via SSO**.

    b. Selecione **Iniciar AuthnRequest**.

    c. Na caixa de texto **ID entity ID,** colhe o valor do **Identificador Azure AD,** que copiou do portal Azure.

    d. Na caixa de texto URL do serviço de **assinatura única,** colá o valor do URL de **Login,** que copiou do portal Azure.

    e. Na caixa de texto URL do **Serviço De Logout Único,** colá o valor do URL de **Logout,** que copiou do portal Azure.

    f. Abra o seu certificado descarregado no Bloco de Notas, copie o conteúdo e, em seguida, cole-o na caixa de texto do Certificado de **Chave Pública RSA.**

    g. Clique em **Guardar**.

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

Nesta secção, permitirá que b.Simon utilize um único sign-on Azure, concedendo acesso à Kiteworks.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Kiteworks**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

### <a name="create-kiteworks-test-user"></a>Criar kiteworks utilizador de teste

O objetivo desta secção é criar uma utilizadora chamada Britta Simon em Kiteworks.

Kiteworks suporta o provisionamento just-in-time, que é por padrão habilitado. Não há nenhum item de ação para si nesta secção. Um novo utilizador é criado durante uma tentativa de aceder à Kiteworks se ainda não existir.

> [!NOTE]
> Se precisar de criar um utilizador manualmente, tem de contactar a equipa de [suporte kiteworks](https://accellion.com/support).

### <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Kiteworks no Painel de Acesso, deve ser automaticamente inscrito nas Kiteworks para as quais configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
