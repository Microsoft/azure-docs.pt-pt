---
title: 'Tutorial: Integração do Active Directory do Azure com Syncplicity | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Syncplicity.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 896a3211-f368-46d7-95b8-e4768c23be08
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/10/2019
ms.author: jeedes
ms.openlocfilehash: c823b1b5d009d836ba3f134623a67cab0d38c180
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67050315"
---
# <a name="tutorial-integrate-syncplicity-with-azure-active-directory"></a>Tutorial: Integrar Syncplicity com o Azure Active Directory

Neste tutorial, irá aprender como integrar Syncplicity com o Azure Active Directory (Azure AD). Quando integrar Syncplicity com o Azure AD, pode:

* Controlar no Azure AD que tenha acesso ao Syncplicity.
* Permita que os utilizadores ser automaticamente sessão iniciada para Syncplicity com as suas contas do Azure AD.
* Gira as suas contas num local central – portal do Azure.

Para saber mais sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver uma subscrição, pode obter a versão de avaliação gratuita de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).
* Syncplicity início de sessão único (SSO) ativado na subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configurar e testar o SSO do Azure AD num ambiente de teste. Suporta Syncplicity **SP** iniciada SSO.

## <a name="adding-syncplicity-from-the-gallery"></a>Adicionando Syncplicity da Galeria

Para configurar a integração do Syncplicity com o Azure AD, terá de adicionar Syncplicity a partir da Galeria à sua lista de aplicações de SaaS geridas.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, selecione o **do Azure Active Directory** serviço.
1. Navegue para **aplicações empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar nova aplicação, selecione **nova aplicação**.
1. Na **adicionar a partir da galeria** secção, escreva **Syncplicity** na caixa de pesquisa.
1. Selecione **Syncplicity** do painel de resultados e, em seguida, adicionar a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso"></a>Configurar e testar o SSO do Azure AD

Configurar e testar o SSO do Azure AD com Syncplicity com um utilizador de teste **B.Simon**. Para SSO para funcionar, tem de estabelecer uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Syncplicity.

Para configurar e testar o SSO do Azure AD com Syncplicity, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar o Syncplicity SSO](#configure-syncplicity-sso)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com B.Simon.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar B.Simon utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste Syncplicity](#create-syncplicity-test-user)**  - para ter um equivalente de B.Simon no Syncplicity que está ligado à representação de utilizador do Azure AD.
6. **[Testar o SSO](#test-sso)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na **Syncplicity** página de integração de aplicativo, encontrar o **gerir** secção e selecione **início de sessão único**.
1. Sobre o **selecionar um método de início de sessão único** , selecione **SAML**.
1. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique no ícone de edição/caneta para **configuração básica de SAML** para editar as definições.

   ![Editar a configuração SAML do básico](common/edit-urls.png)

1. Sobre o **configuração básica de SAML** página, introduza os valores para os seguintes campos:

    a. Na **iniciar sessão no URL** caixa de texto, escreva um URL com o seguinte padrão: `https://<companyname>.syncplicity.com`

    b. Na **identificador (ID de entidade)** caixa de texto, escreva um URL com o seguinte padrão: `https://<companyname>.syncplicity.com/sp`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o início de sessão real URL e o identificador. Contacte [equipa de suporte de cliente Syncplicity](https://www.syncplicity.com/contact-us) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

1. No **definir a segurança de início de sessão único com o SAML** na página a **certificado de assinatura SAML** secção, encontrar **certificado (Base64)** e selecione **transferir** para transferir o certificado e guarde-o no seu computador.

   ![O link de download de certificado](common/certificatebase64.png)

1. Sobre o **configurar Syncplicity** secção, copie o URL adequado com base nos seus requisitos.

   ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-syncplicity-sso"></a>Configurar o Syncplicity SSO

1. Inicie sessão no seu **Syncplicity** inquilino.

1. No menu na parte superior, clique em **administrador**, selecione **definições**e, em seguida, clique em **domínio personalizado e início de sessão único**.

    ![Syncplicity](./media/syncplicity-tutorial/ic769545.png "Syncplicity")

1. Sobre o **único início de sessão (SSO)** caixa de diálogo página, execute os seguintes passos:

    ![Início de sessão único \(SSO\)](./media/syncplicity-tutorial/ic769550.png "Single Sign-On \\\(SSO\\\)")

    a. Na **Custom Domain** caixa de texto, escreva o nome do seu domínio.
  
    b. Selecione **habilitado** como **único início de sessão em estado**.

    c. Na **Id de entidade** caixa de texto, colar a **identificador (ID de entidade)** valor, que utilizou no **configuração básica de SAML** no portal do Azure.

    d. Na **URL da página de início de sessão** caixa de texto, colar a **URL de início de sessão** que copiou do portal do Azure.

    e. Na **URL de página de fim de sessão** caixa de texto, colar a **URL de fim de sessão** que copiou do portal do Azure.

    f. Na **certificado do fornecedor de identidade**, clique em **Escolher ficheiro**e, em seguida, carregue o certificado que transferiu do portal do Azure.

    g. Clique em **guardar alterações**.

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

Nesta secção, irá ativar B.Simon utilizar o Azure início de sessão único ao conceder acesso para Syncplicity.

1. No portal do Azure, selecione **aplicações empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicações, selecione **Syncplicity**.
1. Na página de descrição geral da aplicação, localize a **Manage** secção e selecione **utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **adicionar utilizador**, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![A ligação de adicionar utilizador](common/add-assign-user.png)

1. Na **utilizadores e grupos** caixa de diálogo, selecione **B.Simon** a partir da lista de utilizadores, em seguida, clique nas **selecione** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na asserção de SAML, no **selecionar função** caixa de diálogo, selecione a função adequada para o utilizador a partir da lista e, em seguida, clique nas **selecione** na parte inferior do ecrã.
1. Na **adicionar atribuição** caixa de diálogo, clique nas **atribuir** botão.

### <a name="create-syncplicity-test-user"></a>Criar utilizador de teste Syncplicity

Para utilizadores do AAD conseguir iniciar sessão, têm de ser aprovisionados para aplicação Syncplicity. Esta secção descreve como criar contas de utilizador do AAD no Syncplicity.

**Para Aprovisionar uma conta de utilizador para Syncplicity, execute os seguintes passos:**

1. Inicie sessão no seu **Syncplicity** inquilino (por exemplo: `https://company.Syncplicity.com`).

1. Clique em **administrador** e selecione **contas de utilizador** e, em seguida, clique em **adicionar um utilizador**.

    ![Gerir utilizadores](./media/syncplicity-tutorial/ic769764.png "gerir utilizadores")

1. Tipo de **endereços de E-Mail** de uma conta do Azure AD que pretende aprovisionar, selecione **utilizador** como **função**e, em seguida, clique em **seguinte**.

    ![Informações da conta](./media/syncplicity-tutorial/ic769765.png "informações da conta")

    > [!NOTE]
    > O titular da conta do AAD, obtém um e-mail, incluindo uma ligação para confirmar e ativar a conta.

1. Selecione um grupo na sua empresa que deve se tornar um membro do seu novo utilizador e, em seguida, clique em **seguinte**.

    ![Associação de grupo](./media/syncplicity-tutorial/ic769772.png "associação de grupo")

    > [!NOTE]
    > Se não houver nenhum grupo listado, clique em **seguinte**.

1. Selecione as pastas que pretende colocar sob o controle do Syncplicity no computador do usuário e, em seguida, clique em **seguinte**.

    ![Pastas de Syncplicity](./media/syncplicity-tutorial/ic769773.png "Syncplicity pastas")

> [!NOTE]
> Pode utilizar quaisquer outras Syncplicity utilizador conta criação ferramentas ou APIs fornecidas pelo Syncplicity para aprovisionar contas de utilizador do AAD.

### <a name="test-sso"></a>Teste SSO

Ao selecionar o mosaico Syncplicity no painel de acesso, deve ser automaticamente sessão iniciada no Syncplicity para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)