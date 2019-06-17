---
title: 'Tutorial: Integração do Active Directory do Azure com ExpenseIn | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e ExpenseIn.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 6ac8053b-a216-45d8-bf5e-ecd37d808e57
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c09542013dff3a18965d1070216a938c26a144e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67102846"
---
# <a name="tutorial-integrate-expensein-with-azure-active-directory"></a>Tutorial: Integrar ExpenseIn com o Azure Active Directory

Neste tutorial, irá aprender como integrar ExpenseIn com o Azure Active Directory (Azure AD). Quando integrar ExpenseIn com o Azure AD, pode:

* Controlar no Azure AD que tenha acesso ao ExpenseIn.
* Permita que os utilizadores ser automaticamente sessão iniciada para ExpenseIn com as suas contas do Azure AD.
* Gira as suas contas num local central – portal do Azure.

Para saber mais sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver uma subscrição, pode obter um [conta gratuita](https://azure.microsoft.com/free/).
* ExpenseIn início de sessão único (SSO) ativado na subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configurar e testar o SSO do Azure AD num ambiente de teste. Suporta ExpenseIn **SP e IDP** iniciada SSO.

## <a name="adding-expensein-from-the-gallery"></a>Adicionando ExpenseIn da Galeria

Para configurar a integração do ExpenseIn com o Azure AD, terá de adicionar ExpenseIn a partir da Galeria à sua lista de aplicações de SaaS geridas.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, selecione o **do Azure Active Directory** serviço.
1. Navegue para **aplicações empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar nova aplicação, selecione **nova aplicação**.
1. Na **adicionar a partir da galeria** secção, escreva **ExpenseIn** na caixa de pesquisa.
1. Selecione **ExpenseIn** do painel de resultados e, em seguida, adicionar a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Configurar e testar o SSO do Azure AD com ExpenseIn com um utilizador de teste **B.Simon**. Para SSO para funcionar, tem de estabelecer uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no ExpenseIn.

Para configurar e testar o SSO do Azure AD com ExpenseIn, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)**  para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Configurar ExpenseIn](#configure-expensein)**  para configurar as definições de SSO no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  para testar o Azure AD início de sessão único com B.Simon.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  para ativar B.Simon utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste ExpenseIn](#create-expensein-test-user)**  para ter um equivalente de B.Simon no ExpenseIn que está ligado à representação de utilizador do Azure AD.
6. **[Testar o SSO](#test-sso)**  para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na **ExpenseIn** página de integração de aplicativo, encontrar o **gerir** secção e selecione **início de sessão único**.
1. Sobre o **selecionar um método de início de sessão único** , selecione **SAML**.
1. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique no ícone de edição/caneta para **configuração básica de SAML** para editar as definições.

   ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, se desejar configurar a aplicação no **IDP** iniciada pelo modo, executar o passo seguinte:

    Na **URL de resposta** texto, escreva qualquer um do URL:

    | |
    |--|
    | `https://app.expensein.com/samlcallback` |
    | `https://mobileapi.expensein.com/identity/samlcallback` |

5. Clique em **definir URLs adicionais** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    Na **URL de início de sessão** caixa de texto, escreva um URL:  `https://app.expensein.com/saml`

1. No **definir a segurança de início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em botão Copiar para copiar **Url de metadados de Federação de aplicação** e clique em **Transferir** para transferir o **certificado (Base64)** e guarde-o no seu computador.

   ![O link de download de certificado](./media/expensein-tutorial/copy-metdataurl-certificate.png)

1. Sobre o **configurar ExpenseIn** secção, copie o URL adequado com base nos seus requisitos.

   ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-expensein"></a>Configurar ExpenseIn

1. Para automatizar a configuração no ExpenseIn, tem de instalar **segura de aplicações meu início de sessão da extensão de browser** ao clicar em **instalar a extensão**.

    ![Extensão My apps](common/install-myappssecure-extension.png)

2. Depois de adicionar a extensão para o navegador, clique em **ExpenseIn configuração** irá direcioná-lo para o aplicativo ExpenseIn. A partir daí, forneça as credenciais de administrador a iniciar sessão em ExpenseIn. A extensão de navegador automaticamente irá configurar o aplicativo para e automatizar passos 3 a 5.

    ![Configuração do programa de configuração](common/setup-sso.png)

3. Se desejar configurar manualmente o ExpenseIn, abra uma nova janela de browser e inicie sessão no site da sua empresa ExpenseIn como administrador e execute os seguintes passos:

4. Clique em **administrador** no topo da página, em seguida, navegue até **Single Sign-On** e clique em **Adicionar fornecedor**.

     ![Configuração de ExpenseIn](./media/expenseIn-tutorial/config01.png)

5. Sobre o **novo fornecedor de identidade** pop-up, execute os seguintes passos:

    ![Configuração de ExpenseIn](./media/expenseIn-tutorial/config02.png)

    a. Na **nome do fornecedor** texto, escreva o nome, como por exemplo: Azure.

    b. Selecione **Sim** como **permitir fornecedor Intitated logon**.

    c. Na **Url de destino** texto caixa, cole o valor de **URL de início de sessão**, que copiou do portal do Azure.

    d. Na **emissor** texto caixa, cole o valor de **do Azure AD identificador**, que copiou do portal do Azure.

    e. Abra o certificado (Base64) no bloco de notas, copie o seu conteúdo e cole-na **certificado** caixa de texto.

    f. Clique em **Criar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta secção, irá criar um utilizador de teste no portal do Azure chamado B.Simon.

1. No painel à esquerda no portal do Azure, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.
1. Selecione **novo utilizador** na parte superior do ecrã.
1. Na **utilizador** propriedades, siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. Na **nome de utilizador** , insira o username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Selecione o **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na **palavra-passe** caixa.
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, irá ativar B.Simon utilizar o Azure início de sessão único ao conceder acesso para ExpenseIn.

1. No portal do Azure, selecione **aplicações empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicações, selecione **ExpenseIn**.
1. Na página de descrição geral da aplicação, localize a **Manage** secção e selecione **utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **adicionar utilizador**, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![A ligação de adicionar utilizador](common/add-assign-user.png)

1. Na **utilizadores e grupos** caixa de diálogo, selecione **B.Simon** a partir da lista de utilizadores, em seguida, clique nas **selecione** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na asserção de SAML, no **selecionar função** caixa de diálogo, selecione a função adequada para o utilizador a partir da lista e, em seguida, clique nas **selecione** na parte inferior do ecrã.
1. Na **adicionar atribuição** caixa de diálogo, clique nas **atribuir** botão.

### <a name="create-expensein-test-user"></a>Criar utilizador de teste ExpenseIn

Para ativar a utilizadores do Azure AD iniciar sessão no ExpenseIn, tem de ser aprovisionados em ExpenseIn. ExpenseIn, aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no ExpenseIn como administrador.

2. Clique em **administrador** no topo da página, em seguida, navegue até **utilizadores** e clique em **novo utilizador**.

     ![Configuração de ExpenseIn](./media/expenseIn-tutorial/config03.png)

3. Sobre o **detalhes** pop-up, execute os seguintes passos:

    ![Configuração de ExpenseIn](./media/expenseIn-tutorial/config04.png)

    a. Na **nome próprio** texto, introduza o nome de utilizador, como **B**.

    b. Na **sobrenome** texto, digite o apelido do utilizador, como **Simon**.

    c. Na **E-Mail** texto, introduza o e-mail do utilizador, como `B.Simon@contoso.com`.

    d. Clique em **Criar**.

### <a name="test-sso"></a>Teste SSO

Ao selecionar o mosaico ExpenseIn no painel de acesso, deve ser automaticamente sessão iniciada no ExpenseIn para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
