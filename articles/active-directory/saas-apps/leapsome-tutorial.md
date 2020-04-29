---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com Leapsome [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Leapsome.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: cb523e97-add8-4289-b106-927bf1a02188
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef28b95e779e2b814b0ae91059c3edd12644d7c9
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "75430946"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-leapsome"></a>Tutorial: Azure Ative Directory integração single sign-on (SSO) com Leapsome

Neste tutorial, aprenderá a integrar o Leapsome com o Azure Ative Directory (Azure AD). Quando integrar o Leapsome com o Azure AD, pode:

* Controlo em Azure AD que tem acesso a Leapsome.
* Permita que os seus utilizadores sejam automaticamente inscritos no Leapsome com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Salta um único sinal on (SSO) ativado subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Leapsome suporta **SP e IDP** iniciadoS SSO

## <a name="adding-leapsome-from-the-gallery"></a>Adicionando Leapsome da galeria

Para configurar a integração do Leapsome no Azure AD, precisa de adicionar Leapsome da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **da galeria,** digite **Leapsome** na caixa de pesquisa.
1. Selecione **Leapsome** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-leapsome"></a>Configure e teste Azure AD único sign-on para Leapsome

Configure e teste Azure AD SSO com Leapsome utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em Leapsome.

Para configurar e testar o Azure AD SSO com leapsome, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    * Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure Leapsome SSO](#configure-leapsome-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    * **[Create Leapsome test user](#create-leapsome-test-user)** - para ter uma contrapartida de B.Simon em Leapsome que está ligada à representação azure AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **Leapsome,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** caso deseje configurar a aplicação no modo iniciado do **IDP,** introduza os valores para os seguintes campos:

    a. Na caixa de texto **identificador,** digite um URL:`https://www.leapsome.com`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://www.leapsome.com/api/users/auth/saml/<CLIENTID>/assert`

1. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://www.leapsome.com/api/users/auth/saml/<CLIENTID>/login`

    > [!NOTE]
    > O url de resposta anterior e o valor do URL de inscrição não são de valor real. Irá atualizá-los com os valores reais, o que é explicado mais tarde no tutorial.

1. A aplicação Leapsome espera as afirmações do SAML num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação Leapsome espera que poucos atributos sejam retransmitidos na resposta SAML que são mostradas abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.

    | Nome | Atributo fonte | Espaço de nomes |
    | ---------------| --------------- | --------- |  
    | primeiro nome | user.givenname | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | apelido | utilizador.sobrenome | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | título | user.jobtitle | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | imagem | URL para a foto do empregado | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | | |

    > [!Note]
    > O valor do atributo da imagem não é real. Atualize este valor com URL de imagem real. Para obter este valor contacte a equipa de [suporte do Cliente Leapsome.](mailto:support@leapsome.com)

1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

1. Na secção **set up Leapsome,** copie os URL(s) adequados com base no seu requisito.

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

Nesta secção, permitirá que b.Simon use o único sign-on Azure, concedendo acesso ao Leapsome.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Leapsome**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-leapsome-sso"></a>Configure Leapsome SSO

1. Numa janela de navegador web diferente, inscreva-se no Leapsome como administrador de segurança.

1. No lado superior direito, clique no logótipo Definições e, em seguida, clique em **Definições de Administrador**.

    ![Conjunto saltado](./media/leapsome-tutorial/tutorial_leapsome_admin.png)

1. Na barra de menu esquerdo clique em **Single Sign On (SSO)** e na página de **inscrição única (SSO) baseada em SAML** executa os seguintes passos:

    ![Saml saml saltado](./media/leapsome-tutorial/tutorial_leapsome_samlsettings.png)

    a. **Selecione ativar um único sinal baseado em SAML**.

    b. Copie o URL de **Login (aponte os seus utilizadores aqui para iniciar o login)** e cole-o na caixa de texto **url sign-on** na secção **de configuração Básica SAML** no portal Azure.

    c. Copie o valor do **URL de resposta (recebe resposta do seu fornecedor de identidade)** e cole-o na caixa de texto URL de **resposta** na secção **basic SAML Configuração** no portal Azure.

    d. Na caixa de texto **SSO Login (fornecida pelo fornecedor** de identidade), cola o valor do URL de **Login,** que copiou do portal Azure.

    e. Copie o Certificado que descarregou do `--BEGIN CERTIFICATE and END CERTIFICATE--` portal Azure sem comentários e cole-o na caixa de texto **Certificado (fornecido pelo fornecedor** de identidade).

    f. Clique em **ATUALIZAÇÕES SSO DEFINIÇÕES**.

### <a name="create-leapsome-test-user"></a>Criar utilizador de teste Leapsome

Nesta secção, cria-se uma utilizadora chamada Britta Simon em Leapsome. Trabalhe com a equipa de suporte do [Leapsome Client](mailto:support@leapsome.com) para adicionar os utilizadores ou o domínio que deve ser adicionado a uma lista de permitir a plataforma Leapsome. Se o domínio for adicionado pela equipa, os utilizadores serão automaticamente aprovisionados na plataforma Leapsome. Os utilizadores devem ser criados e ativados antes de utilizar um único sinal.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Leapsome no Painel de Acesso, deve ser automaticamente inscrito no Leapsome para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente Leapsome com Azure AD](https://aad.portal.azure.com/)