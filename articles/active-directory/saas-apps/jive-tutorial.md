---
title: 'Tutorial: Integração do Diretório Ativo Azure com a Jive Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Jive.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9fc5659a-c116-4a1b-a601-333325a26b46
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ccdab373be4bab876ef52ba478076b6a8b6e0845
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "76291180"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-jive"></a>Tutorial: Azure Ative Diretório integração individual (SSO) com jive

Neste tutorial, você vai aprender a integrar jive com o Azure Ative Directory (Azure AD). Quando integrar o Jive com o Azure AD, pode:

* Controlo em Azure AD que tem acesso a Jive.
* Permita que os seus utilizadores sejam automaticamente inscritos na Jive com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A assinatura ativada por um único sinal (SSO) da Jive.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Jive apoia **SP** iniciado SSO
* Jive suporta fornecimento [ **automatizado** de utilizadores](jive-provisioning-tutorial.md)
* Assim que configurar o Jive, pode impor controlos de sessão, que protegem a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. Os controlos de sessão estendem-se a partir do Acesso Condicional. [Saiba como impor o controlo de sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-jive-from-the-gallery"></a>Adicionando Jive da galeria

Para configurar a integração da Jive em Azure AD, você precisa adicionar Jive da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **da galeria,** digite **Jive** na caixa de pesquisa.
1. Selecione **Jive** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on-for-jive"></a>Configure e teste Azure AD único sign-on para Jive

Configure e teste Azure AD SSO com Jive utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em Jive.

Para configurar e testar o Azure AD SSO com o Jive, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    * Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure jive SSO](#configure-jive-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    * **[Crie um utilizador](#create-jive-test-user)** de teste Jive - para ter uma contrapartida de B.Simon em Jive que esteja ligada à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **Jive,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** introduza os valores para os seguintes campos:

a. No **Sign on URL** text box, digite um URL utilizando o seguinte padrão:`https://<instance name>.jivecustom.com`

    b. In the **Identifier (Entity ID)** text box, type a URL using the following pattern:
    `https://<instance name>.jiveon.com`

    > [!NOTE]
    > These values are not real. Update these values with the actual Sign on URL and Identifier. Contact [Jive Client support team](https://www.jivesoftware.com/services-support/) to get these values. You can also refer to the patterns shown in the **Basic SAML Configuration** section in the Azure portal.

5. Na configuração de um único sign-on com a página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** para descarregar o **Federation Metadata XML** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

6. Na secção **'Configurar',** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure AD

    c. Logout URL

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

Nesta secção, você permitirá que B.Simon use o único sign-on Azure, concedendo acesso ao Jive.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Jive**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-jive-sso"></a>Configure Jive SSO

1. Para configurar um único sinal no lado **jive,** inscreva-se no seu inquilino Jive como administrador.

1. No menu em cima, clique em **SAML**.

    ![Configure um único sinal no lado da aplicação](./media/jive-tutorial/tutorial_jive_002.png)

    a. Selecione **Ativado** sob o separador **Geral.**

    b. Clique no botão **GUARDAR TODAS AS DEFINIÇÕES SAML.**

1. Navegue para o **separador IDP METADATA.**

    ![Configure um único sinal no lado da aplicação](./media/jive-tutorial/tutorial_jive_003.png)

    a. Copie o conteúdo do ficheiro XML dos metadados descarregados e, em seguida, cole-o na caixa de texto de metadados do Fornecedor de **Identidade (IDP).**

    b. Clique no botão **GUARDAR TODAS AS DEFINIÇÕES SAML.**

1. Selecione separador DE **MAPEAMENTO DE ATRIBUIÇÃO** DE UTILIZADOR.

    ![Configure um único sinal no lado da aplicação](./media/jive-tutorial/tutorial_jive_004.png)

    a. Na caixa de texto **e-mail,** copie e cole o nome do atributo do valor do **correio.**

    b. Na caixa de texto **de primeiro nome,** copie e cole o nome do atributo do valor **dadonome.**

    c. Na caixa de texto **De Apelido,** copie e cole o nome do atributo do valor do **apelido.**

### <a name="create-jive-test-user"></a>Criar utilizador de teste Jive

O objetivo desta secção é criar um utilizador chamado Britta Simon em Jive. A Jive suporta o fornecimento automático de utilizadores, que é por defeito. Pode encontrar mais detalhes [aqui](jive-provisioning-tutorial.md) sobre como configurar o fornecimento automático de utilizadores.

Se precisar de criar o utilizador manualmente, trabalhe com a equipa de suporte do [Cliente Jive](https://www.jivesoftware.com/services-support/) para adicionar os utilizadores na plataforma Jive.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Jive no Painel de Acesso, deve ser automaticamente inscrito no Jive para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente Jive com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Configurar o fornecimento de utilizadores](jive-provisioning-tutorial.md)

- [Como proteger jive com visibilidade avançada e controlos](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
