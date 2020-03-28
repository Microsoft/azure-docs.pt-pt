---
title: 'Tutorial: Integração do Diretório Ativo Azure com sage intacct Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Sage Intacct.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 92518e02-a62c-4b1b-a8e9-2803eb2b49ac
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b28390dba009226d493f5bfc6a5270b067f5bba0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "73570487"
---
# <a name="tutorial-integrate-sage-intacct-with-azure-active-directory"></a>Tutorial: Integrar Sage Intacct com Diretório Ativo Azure

Neste tutorial, você vai aprender a integrar Sage Intacct com Azure Ative Directory (Azure AD). Quando integrar o Sage Intacct com o Azure AD, pode:

* Controlo em Azure AD que tem acesso a Sage Intacct.
* Ative que os seus utilizadores sejam automaticamente inscritos na Sage Intacct com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Sage Intacct single sign-on (SSO) enabled subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Sage Intacct suporta **IDP** iniciado SSO

## <a name="adding-sage-intacct-from-the-gallery"></a>Adicionando Sage Intacct da galeria

Para configurar a integração da Sage Intacct em Azure AD, você precisa adicionar Sage Intacct da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** digite **Sage Intacct** na caixa de pesquisa.
1. Selecione **Sage Intacct** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sage-intacct"></a>Configure e teste Azure AD único sign-on para Sage Intacct

Configure e teste Azure AD SSO com Sage Intacct utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Sage Intacct.

Para configurar e testar o Azure AD SSO com a Sage Intacct, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
2. **[Configure sage intacct SSO](#configure-sage-intacct-sso)** - para configurar as definições de início de sessão individuais no lado da aplicação.
    1. **[Crie um utilizador de teste Sage Intacct](#create-sage-intacct-test-user)** - para ter uma contraparte de B.Simon em Sage Intacct que esteja ligada à representação da AD Azure do utilizador.
6. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **Sage Intacct,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **Select a Single sign-on,** selecione **SAML**.
1. Na configuração do Single Sign-On com a página **SAML,** clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** introduza os valores para os seguintes campos:

    Na caixa de texto **URL resposta,** escreva um URL:`https://www.intacct.com/ia/acct/sso_response.phtml`

1. A aplicação Sage Intacct espera as afirmações do SAML num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos. Clique no ícone **Editar** para abrir o diálogo de Atributos do Utilizador..

    ![image](common/edit-attribute.png)

1. Além de acima, a aplicação Sage Intacct espera que poucos atributos sejam retransmitidos na resposta SAML. Na secção **Reivindicações** do Utilizador no diálogo **de Atributos** do Utilizador, execute os seguintes passos para adicionar atributo token SAML, conforme indicado no quadro abaixo:

    | Nome  |  Atributo fonte|
    | ---------------| --------------- |
    | Nome da Empresa | **Sage Intacct Company ID** |
    | nome | O valor deve ser o mesmo que o ID do **utilizador**intacct sage , que você introduz na secção de utilizador de **teste Create Sage Intacct**, que é explicado mais tarde no tutorial |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier | O valor deve ser o mesmo que o ID de utilizador sage intacct **federado SSO**, que você introduz na secção de utilizador de **teste Create Sage Intacct**, o que é explicado mais tarde no tutorial |

    a. Clique **Em adicionar nova alegação** para abrir o diálogo de reclamações do utilizador **Gerir.**

    b. Na caixa de texto **Name,** digite o nome do atributo mostrado para essa linha.

    c. Deixe o espaço de **nome em** branco.

    d. Selecione Origem como **Atributo**.

    e. Na lista de **atributos Fonte,** digite o valor do atributo mostrado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Guardar**.

1. Na configuração de um único sinal com página **SAML,** na secção certificado de **assinatura SAML,** encontre **certificado (Base64)** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

1. Na secção **'Sage Intacct',** copie os URL(s) adequados com base na sua exigência.

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

Nesta secção, permitirá que b.Simon use o único sign-on Azure, concedendo acesso à Sage Intacct.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Sage Intacct**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-sage-intacct-sso"></a>Configure Sage Intacct SSO

1. Numa janela de navegador web diferente, inscreva-se no site da empresa Sage Intacct como administrador.

1. Clique no separador **Empresa** e, em seguida, clique em **Informações da Empresa**.

    ![Empresa](./media/intacct-tutorial/ic790037.png "Empresa")

1. Clique no separador **'Segurança'** e, em seguida, clique em **Editar**.

    ![Segurança](./media/intacct-tutorial/ic790038.png "Segurança")

1. Na secção **Deseminávia (SSO),** execute os seguintes passos:

    ![Sinal único ligado](./media/intacct-tutorial/ic790039.png "sinal único em")

    a. **Selecione Ativar um único sinal .**

    b. Como tipo de **fornecedor de identidade,** selecione **SAML 2.0**.

    c. Na caixa de texto **URL emitente,** colhe o valor do **Identificador AD Azure,** que copiou do portal Azure.

    d. Na caixa de texto **LOGIN URL,** colá o valor do URL de **Login,** que copiou do portal Azure.

    e. Abra o seu certificado codificado **base-64** no bloco de notas, copie o conteúdo do mesmo na sua área de sobrercopia e, em seguida, cole-o na caixa de **certificados.**

    f. Clique em **Guardar**.

### <a name="create-sage-intacct-test-user"></a>Criar o utilizador de teste Sage Intacct

Para configurar os utilizadores de Anúncios Azure para que possam iniciar sessão na Sage Intacct, devem ser aprovisionados no Sage Intacct. Para a Sage Intacct, o provisionamento é uma tarefa manual.

**Para fornecer contas de utilizador, execute os seguintes passos:**

1. Inscreva-se no seu inquilino **Sage Intacct.**

1. Clique no separador **Empresa** e, em seguida, clique em **Utilizadores**.

    ![Utilizadores](./media/intacct-tutorial/ic790041.png "Utilizadores")

1. Clique no separador **Adicionar.**

    ![Adicionar](./media/intacct-tutorial/ic790042.png "Adicionar")

1. Na secção informação do **utilizador,** execute os seguintes passos:

    ![Informação do Utilizador](./media/intacct-tutorial/ic790043.png "Informação do Utilizador")

    a. Introduza o ID do **utilizador**, o **apelido,** **o nome,** o **endereço de e-mail,** o **Título**e o **Telefone** de uma conta AD Azure que pretende fornecer na secção informação do **utilizador.**

    > [!NOTE]
    > Certifique-se de que o ID do **utilizador** em screenshot acima e o valor do **Atributo fonte** que está mapeado com o **atributo** de nome na secção **Atributos** do Utilizador no portal Azure devem ser os mesmos.

    b. Selecione os **privilégios admin** de uma conta Azure AD que pretende fornecer.

    c. Clique em **Guardar**. 
    
    d. O titular da conta Azure AD recebe um e-mail e segue um link para confirmar a sua conta antes de se tornar ativo.

1. Clique no separador de entrada de um único sinal e **certifique-se** de que o ID do `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` **utilizador SSO federado** na imagem abaixo e o valor do **Atributo fonte** que está mapeado com a secção de **Atributos** do Utilizador no portal Azure deve ser o mesmo.

    ![Informação do Utilizador](./media/intacct-tutorial/ic790044.png "Informação do Utilizador")

> [!NOTE]
> Para fornecer contas de utilizador Da AD Azure, pode utilizar outras ferramentas de criação de conta de utilizador Sage Intacct ou APIs fornecidas pela Sage Intacct.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Sage Intacct no Painel de Acesso, deve ser automaticamente inscrito no Sage Intacct para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

