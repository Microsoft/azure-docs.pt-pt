---
title: 'Tutorial: Integração do Active Directory do Azure com ThousandEyes | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e ThousandEyes.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 790e3f1e-1591-4dd6-87df-590b7bf8b4ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 587d6f8ecdc447672c14ce400ebc64609e4d472d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59273911"
---
# <a name="tutorial-azure-active-directory-integration-with-thousandeyes"></a>Tutorial: Integração do Active Directory do Azure com ThousandEyes

Neste tutorial, saiba como integrar ThousandEyes com o Azure Active Directory (Azure AD).
Integrar ThousandEyes no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao ThousandEyes.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para ThousandEyes (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com ThousandEyes, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* ThousandEyes único início de sessão na subscrição ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta ThousandEyes **SP** iniciada SSO

* Suporta ThousandEyes [ **automatizada** aprovisionamento de utilizadores](https://docs.microsoft.com/azure/active-directory/saas-apps/thousandeyes-provisioning-tutorial)

## <a name="adding-thousandeyes-from-the-gallery"></a>Adicionando ThousandEyes da Galeria

Para configurar a integração do ThousandEyes com o Azure AD, terá de adicionar ThousandEyes a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar ThousandEyes a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **ThousandEyes**, selecione **ThousandEyes** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![ThousandEyes na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com ThousandEyes com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no ThousandEyes deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com ThousandEyes, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar ThousandEyes Single Sign-On](#configure-thousandeyes-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste ThousandEyes](#create-thousandeyes-test-user)**  - para ter um equivalente da Eduarda Almeida na ThousandEyes que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com ThousandEyes, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **ThousandEyes** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![ThousandEyes domínio e URLs únicas início de sessão em informações](common/sp-signonurl.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL:  `https://app.thousandeyes.com/login/sso`

5. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

6. Sobre o **configurar ThousandEyes** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-thousandeyes-single-sign-on"></a>Configurar ThousandEyes Single Sign-On

1. Numa janela do browser web diferente, inicie sessão no seu **ThousandEyes** site da empresa como administrador.

2. No menu na parte superior, clique em **definições**.

    ![As definições](./media/thousandeyes-tutorial/ic790066.png "definições")

3. Clique em **conta**

    ![Conta](./media/thousandeyes-tutorial/ic790067.png "conta")

4. Clique nas **segurança e autenticação** separador.

    ![Segurança e autenticação](./media/thousandeyes-tutorial/ic790068.png "segurança e autenticação")

5. Na **configuração do início de sessão único** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/thousandeyes-tutorial/ic790069.png "configurar o início de sessão único")

    a. Selecione **ativar o início de sessão único**.

    b. Na **URL de página de início de sessão** caixa de texto, colar **URL de início de sessão**, que copiou do portal do Azure.

    c. Na **URL de página de fim de sessão** caixa de texto, colar **URL de fim de sessão**, que copiou do portal do Azure.

    d. **Emissor do fornecedor de identidade** caixa de texto, colar **do Azure AD identificador**, que copiou do portal do Azure.

    e. Na **certificado de verificação**, clique em **Escolher ficheiro**em seguida, carregue o certificado que transferiu a partir do portal do Azure.

    f. Clique em **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** campo introduza **BrittaSimon**.
  
    b. Na **nome de utilizador** tipo de campo brittasimon@yourcompanydomain.extension. Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para ThousandEyes.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **ThousandEyes**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **ThousandEyes**.

    ![A ligação de ThousandEyes na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-thousandeyes-test-user"></a>Criar utilizador de teste ThousandEyes

O objetivo desta secção é criar um usuário chamado Eduarda Almeida no ThousandEyes. ThousandEyes suporta o aprovisionamento automático de utilizadores, que está por predefinição, ativada. Pode encontrar mais detalhes [aqui](thousandeyes-provisioning-tutorial.md) sobre como configurar o aprovisionamento automático de utilizadores.

**Se precisar de criar manualmente o utilizador, execute os seguintes passos:**

1. Inicie sessão no site da sua empresa ThousandEyes como um administrador.

2. Clique em **definições**.

    ![As definições](./media/thousandeyes-tutorial/IC790066.png "definições")

3. Clique em **conta**.

    ![Conta](./media/thousandeyes-tutorial/IC790067.png "conta")

4. Clique nas **contas e utilizadores** separador.

    ![Contas e utilizadores](./media/thousandeyes-tutorial/IC790073.png "contas e utilizadores")

5. Na **adicionar utilizadores e contas de** secção, execute os seguintes passos:

    ![Adicionar contas de utilizador](./media/thousandeyes-tutorial/IC790074.png "adicionar contas de utilizador")

    a. Na **Name** como a caixa de texto, escreva o nome de utilizador **Eduarda Almeida**.

    b. Na **E-Mail** caixa de texto, como o tipo de e-mail do utilizador brittasimon@contoso.com.

    b. Clique em **adicionar novo utilizador à conta**.

    > [!NOTE]
    > O titular da conta do Azure Active Directory irá receber um e-mail, incluindo uma ligação para confirmar e ativar a conta.

> [!NOTE]
> Pode utilizar quaisquer outras ThousandEyes utilizador conta criação ferramentas ou APIs fornecidas pelo ThousandEyes para aprovisionar o Azure Active Directory contas de utilizador.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico ThousandEyes no painel de acesso, deve ser automaticamente sessão iniciada no ThousandEyes para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Configurar o aprovisionamento do utilizador](https://docs.microsoft.com/azure/active-directory/saas-apps/thousandeyes-provisioning-tutorial)
