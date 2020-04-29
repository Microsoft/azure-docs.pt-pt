---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com Elementos SD [ SD) Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Diretório Ativo Azure e os Elementos SD.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f0386307-bb3b-4810-8d4b-d0bfebda04f4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a387659e2375444fd32cf731ab4bccc210b669a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "74081671"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sd-elements"></a>Tutorial: Azure Ative Diretório integração individual (SSO) com Elementos SD

Neste tutorial, você vai aprender a integrar Elementos SD com O Diretório Ativo Azure (Azure AD). Quando integrar elementos SD com AD Azure, pode:

* Controlo em Azure AD que tem acesso a Elementos SD.
* Ative que os seus utilizadores sejam automaticamente inscritos nos Elementos SD com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* SD Elements single sign-on (SSO) enabled subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* SD Elements suporta **IDP** iniciado SSO

## <a name="adding-sd-elements-from-the-gallery"></a>Adicionando Elementos SD da galeria

Para configurar a integração de Elementos SD em AD Azure, precisa adicionar Elementos SD da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No **Add da** secção galeria, digite **Elementos SD** na caixa de pesquisa.
1. Selecione **Elementos SD** do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on-for-sd-elements"></a>Configure e teste azure AD único sign-on para elementos SD

Configure e teste Azure AD SSO com elementos SD utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em Elementos SD.

Para configurar e testar o Azure AD SSO com elementos SD, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    * Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure elementos SD SSO](#configure-sd-elements-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    * **[Crie o utilizador de teste de Elementos SD](#create-sd-elements-test-user)** - para ter uma contraparte de B.Simon em Elementos SD que esteja ligado à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações de **Elementos SD,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na configuração de um único sessão com a página **SAML,** introduza os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:`https://<tenantname>.sdelements.com/sso/saml2/metadata`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://<tenantname>.sdelements.com/sso/saml2/acs/`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação e resposta real. Contacte a equipa de suporte do [Cliente de Elementos SD](mailto:support@sdelements.com) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. A aplicação SD Elements espera que as afirmações do SAML num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação SD Elements espera que poucos atributos sejam retransmitidos na resposta SAML que são mostradas abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.

    | Nome |  Atributo fonte|
    | --- | --- |
    | e-mail |utilizador.mail |
    | primeiro nome |user.givenname |
    | apelido |utilizador.sobrenome |

1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

1. Na secção **De configurar elementos SD,** copie os URL(s) adequados com base na sua exigência.

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

Nesta secção, permitirá que b.Simon utilize um único sign-on Azure, concedendo acesso a Elementos SD.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Elementos SD**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-sd-elements-sso"></a>Configurar elementos SD SSO

1. Para obter um único sinal de inscrição ativado, contacte a sua equipa de suporte para [elementos SD](mailto:support@sdelements.com) e forneça-lhes o ficheiro de certificado descarregado.

1. Numa janela de navegador diferente, inscreva-se no seu inquilino sd elements como administrador.

1. No menu em cima, clique no **Sistema**, e depois **no Single Sign-on**.

    ![Configurar um único sinal](./media/sd-elements-tutorial/tutorial_sd-elements_09.png)

1. No diálogo de definições de **início de sessão simples,** execute os seguintes passos:

    ![Configurar um único sinal](./media/sd-elements-tutorial/tutorial_sd-elements_10.png)

    a. Como **Tipo SSO,** selecione **SAML**.

    b. Na caixa de texto **ID da Entidade fornecedora** de identidade, colhe o valor do **Identificador Azure AD,** que copiou do portal Azure.

    c. Na caixa de texto single **sign-on do fornecedor** de identidade, colá o valor do URL de **Login,** que copiou do portal Azure.

    d. Clique em **Guardar**.

### <a name="create-sd-elements-test-user"></a>Criar o utilizador de teste de elementos SD

O objetivo desta secção é criar um utilizador chamado B.Simon em Elementos SD. No caso dos Elementos SD, criar elementos SD é uma tarefa manual.

**Para criar B.Simon em Elementos SD, execute os seguintes passos:**

1. Numa janela do navegador web, inscreva-se no site da sua empresa DeElementos SD como administrador.

1. No menu em cima, clique na **Gestão**do Utilizador , e depois **nos Utilizadores.**

    ![Criação de um utilizador de teste de elementos SD](./media/sd-elements-tutorial/tutorial_sd-elements_11.png) 

1. Clique em **adicionar novo utilizador**.

    ![Criação de um utilizador de teste de elementos SD](./media/sd-elements-tutorial/tutorial_sd-elements_12.png)

1. No diálogo **Adicionar Novo Utilizador,** execute os seguintes passos:

    ![Criação de um utilizador de teste de elementos SD](./media/sd-elements-tutorial/tutorial_sd-elements_13.png) 

    a. Na caixa de texto **por e-mail,** introduza o e-mail do utilizador como **b.simon@contoso.com**.

    b. Na caixa de texto **First Name,** introduza o primeiro nome do utilizador como **B.**.

    c. Na caixa de texto **Sobre Nome,** introduza o último nome de utilizador como **Simon**.

    d. Como **Função,** selecione **Utilizador**.

    e. Clique em **Criar utilizador**.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo SD Elements no Painel de Acesso, deve ser automaticamente inscrito nos Elementos SD para os quais configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente elementos SD com AD Azure](https://aad.portal.azure.com/)