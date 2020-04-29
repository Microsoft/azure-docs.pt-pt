---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com Claromentis [ Integração de diretórios ativos) com Claromentis [ Integração de Um Único Diretório) com Claromentis Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Claromentis.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 7980e0c5-e4d8-4678-afa2-7ec219432114
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/04/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c6eaf23950f83592a33709574be464f4499f0ab
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "74823222"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-claromentis"></a>Tutorial: Azure Ative Diretório integração individual (SSO) com Claromentis

Neste tutorial, você vai aprender a integrar Claromentis com o Azure Ative Directory (Azure AD). Quando integrar o Claromentis com a Azure AD, pode:

* Controlo em Azure AD que tem acesso a Claromentis.
* Permita que os seus utilizadores sejam automaticamente inscritos no Claromentis com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Claromentis uma única subscrição ativada por si (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Claromentis apoia **SP e IDP** iniciado SSO
* Claromentis suporta o fornecimento de utilizadores **justo no tempo**

## <a name="adding-claromentis-from-the-gallery"></a>Adicionando Claromentis da galeria

Para configurar a integração de Claromentis em Azure AD, você precisa adicionar Claromentis da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** digite **Claromentis** na caixa de pesquisa.
1. Selecione **Claromentis** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-claromentis"></a>Configure e teste Azure AD único sign-on para Claromentis

Configure e teste Azure AD SSO com Claromentis utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em Claromentis.

Para configurar e testar o Azure AD SSO com Claromentis, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    * Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure Claromentis SSO](#configure-claromentis-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    * **[Create Claromentis test user](#create-claromentis-test-user)** - para ter uma contrapartida de B.Simon em Claromentis que está ligada à representação da AD Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **De Claromentis,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** caso deseje configurar a aplicação no modo iniciado do **IDP,** introduza os valores para os seguintes campos:

    a. Na caixa de texto **Identificador,** introduza o valor do identificador de acordo com o requisito da sua organização.

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://<customer_site_url>/custom/loginhandler/simplesaml/www/module.php/saml/sp/saml2-acs.php/claromentis`

1. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:

    | | |
    |-|-|
    | `https://<customer_site_url>/login`|
    | `https://<customer_site_url>/login?no_auto=0`|

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação, resposta real e URL de sinalização que é explicado mais tarde no turorial.

1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **metadados da Federação XML** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

1. Na secção **Configurar Claromentis,** copie os URL(s) adequados com base no seu requisito.

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

Nesta secção, permitirá que b.Simon use o único sign-on Azure, concedendo acesso a Claromentis.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Claromentis**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-claromentis-sso"></a>Configurar Claromentis SSO

1. Numa janela de navegador diferente, inscreva-se no site do Claromentis como administrador.

1. Clique no ícone de **aplicações** e selecione **Administrador**.

    ![Configuração de Claromentis](./media/claromentis-tutorial/config1.png)

1. Selecione o separador **Custom Login Handler.**

    ![Configuração de Claromentis](./media/claromentis-tutorial/config2.png)

1. Selecione **SAML Config**.

    ![Configuração de Claromentis](./media/claromentis-tutorial/config3.png)

1. No **separador SAML Config,** desloque-se até à secção **Config** e execute os seguintes passos:

    ![Configuração de Claromentis](./media/claromentis-tutorial/config4.png)

    a. Na caixa de texto nome de **contacto técnico,** introduza o nome de pessoa de contacto técnico.

    b. Na caixa de texto de email de **contacto técnico,** introduza o endereço de e-mail da pessoa de contacto técnico.

    c. Forneça a palavra-passe na caixa de **texto auth Admin** Password.

1. Percorra até **Auth Sources** e execute os seguintes passos:

    ![Configuração de Claromentis](./media/claromentis-tutorial/config5.png)

    a. Na caixa de texto **IDP,** introduza o valor do **Identificador AD Azure,** que copiou do portal Azure.

    b. Na caixa de texto **Id da Entidade,** introduza o valor id da Entidade.

    c. Faça upload do ficheiro **Federation Metadata XML,** que descarregou a partir do portal Azure.

    d. Clique em **Guardar**.

1. Irá agora notar que todos os URLs foram povoados dentro da secção **fornecedor de identidade** na secção **SAML Config.**

    ![Configuração de Claromentis](./media/claromentis-tutorial/config6.png)

    a. Valor do Identificador de Cópia **(ID da Entidade),** cole este valor na caixa de texto **do identificador** na secção **de configuração Básica SAML** no portal Azure.

    b. Valor URL de **resposta** de cópia, cole este valor na caixa de texto URL de **resposta** na secção **de configuração Básica SAML** no portal Azure.

    c. Copy **Sign On URL** value, cole este valor na caixa de texto URL **Sign-on** na secção **de configuração SAML básica** no portal Azure.

### <a name="create-claromentis-test-user"></a>Criar o utilizador de teste De Claromentis

Nesta secção, um utilizador chamado B.Simon é criado em Claromentis. A Claromentis suporta o fornecimento de utilizadores just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir em Claromentis, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Claromentis no Painel de Acesso, deverá ser automaticamente inscrito no Claromentis para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente Claromentis com Azure AD](https://aad.portal.azure.com/)