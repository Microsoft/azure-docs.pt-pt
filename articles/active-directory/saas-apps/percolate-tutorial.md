---
title: 'Tutorial: Integração do Active Directory do Azure com Percolate | Documentos da Microsoft'
description: Neste tutorial, irá aprender como configurar o início de sessão único entre o Azure Active Directory e Percolate.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 355f9659-b378-44c9-aa88-236e9b529a53
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: 83027e9fbc1826de727f123afe4507c2858c49ff
ms.sourcegitcommit: 179918af242d52664d3274370c6fdaec6c783eb6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/13/2019
ms.locfileid: "65560556"
---
# <a name="tutorial-azure-active-directory-integration-with-percolate"></a>Tutorial: Integração do Active Directory do Azure com Percolate

Neste tutorial, irá aprender como integrar Percolate com o Azure Active Directory (Azure AD).

Esta integração oferece estes benefícios:

* Pode utilizar o Azure AD para controlar quem tem acesso ao Percolate.
* Pode permitir que os utilizadores ter automaticamente sessão iniciada no Percolate (início de sessão único) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central: portal do Azure.

Para saber mais sobre a integração de aplicações SaaS com o Azure AD, veja [início de sessão único para aplicações no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Se não tiver uma subscrição do Azure, [criar uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Percolate, tem de ter:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter um [conta gratuita](https://azure.microsoft.com/free/).
* Uma subscrição de Percolate que tem início de sessão único ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, irá configurar e testar o Azure AD início de sessão único num ambiente de teste.

* Percolate suporta SSO iniciado por SP e iniciado o IdP.

## <a name="add-percolate-from-the-gallery"></a>Adicionar Percolate a partir da Galeria

Para configurar a integração do Percolate com o Azure AD, terá de adicionar Percolate a partir da Galeria à sua lista de aplicações de SaaS geridas.

1. Na [portal do Azure](https://portal.azure.com), no painel esquerdo, selecione **Azure Active Directory**:

    ![Selecione Azure Active Directory](common/select-azuread.png)

2. Aceda a **aplicações empresariais** > **todas as aplicações**:

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar uma aplicação, selecione **nova aplicação** na parte superior da janela:

    ![Selecionar novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **Percolate**. Selecione **Percolate** nos resultados da pesquisa e, em seguida, selecione **Add**.

     ![Resultados da pesquisa](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, irá configurar e testar o Azure AD início de sessão único com Percolate com um utilizador de teste com o nome Eduarda Almeida.
Para ativar o início de sessão único, tem de estabelecer uma relação entre um utilizador do Azure AD e o utilizador correspondente no Percolate.

Para configurar e testar o Azure AD início de sessão único com Percolate, tem de concluir estes passos:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  para ativar a funcionalidade para os seus utilizadores.
2. **[Configurar Percolate início de sessão único](#configure-percolate-single-sign-on)**  no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  para testar o Azure AD início de sessão único.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  para ativar o Azure AD início de sessão único para o utilizador.
5. **[Criar um utilizador de teste Percolate](#create-a-percolate-test-user)**  associado a representação do Azure AD do utilizador.
6. **[Testar início de sessão único](#test-single-sign-on)**  para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, irá ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com Percolate, siga estes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Percolate** página de integração de aplicações, selecione **início de sessão único**:

    ![Selecione início de sessão único](common/select-sso.png)

2. Na **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único:

    ![Selecione um método de início de sessão único](common/select-saml-option.png)

3. Na **definir a segurança de início de sessão único com o SAML** página, selecione a **editar** ícone para abrir o **configuração básica de SAML** caixa de diálogo:

    ![Ícone editar](common/edit-urls.png)

4. Na **configuração básica de SAML** caixa de diálogo, não precisa de tomar medidas para configurar a aplicação no modo de iniciado o IdP. A aplicação já está integrada com o Azure.

    ![Percolate domínio e URLs únicas início de sessão em informações](common/preintegrated.png)

5. Se quiser configurar a aplicação no modo iniciado por SP, selecione **definir URLs adicionais** e, no **iniciar sessão no URL** , introduza **https://percolate.com/app/login**:

   ![Percolate domínio e URLs únicas início de sessão em informações](common/metadata-upload-additional-signon.png)
6. No **definir a segurança de início de sessão único com o SAML** na página a **certificado de assinatura SAML** secção, selecione o **cópia** ícone para copiar o **Url de metadados de Federação de aplicação** . Guarde este URL.

    ![Copie o URL de metadados de Federação da aplicação](common/copy-metadataurl.png)

7. Na **configurar Percolate** secção, copie os URLs adequados, com base nos seus requisitos.

    ![Copie os URLs de configuração](common/copy-configuration-urls.png)

    1. **URL de início de sessão**.

    1. **Azure AD Identifier**.

    1. **URL de fim de sessão**.

### <a name="configure-percolate-single-sign-on"></a>Configurar Percolate início de sessão único

1. Numa nova janela de browser web, inicie sessão no Percolate como um administrador.

2. No lado esquerdo da home page, selecione **definições**:
    
    ![Selecionar definições](./media/percolate-tutorial/configure01.png)

3. No painel esquerdo, selecione **SSO** sob **organização**:

    ![Selecionar SSO na organização](./media/percolate-tutorial/configure02.png)

    1. Na **URL de início de sessão** caixa, cole a **URL de início de sessão** valor que copiou do portal do Azure.

    1. Na **ID de entidade** caixa, cole a **do Azure AD identificador** valor que copiou do portal do Azure.

    1. No bloco de notas, abra o certificado com codificação base 64 que transferiu a partir do portal do Azure. Copie seu conteúdo e cole-o para o **x509 certificados** caixa.

    1. Na **atributo de correio eletrónico** , introduza **emailaddress**.

    1. O **URL de metadados do fornecedor de identidade** caixa é um campo opcional. Se copiar uma **Url de metadados de Federação de aplicação** do portal do Azure, pode colá-lo nesta caixa.

    1. Na **AuthNRequests deve ser assinado?** lista, selecione **não**.

    1. Na **aprovisionamento automático do ativar o SSO** lista, selecione **não**.

    1. Selecione **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta secção, irá criar um utilizador de teste com o nome Eduarda Almeida no portal do Azure.

1. No portal do Azure, selecione **do Azure Active Directory** no painel esquerdo, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**:

    ![Selecione todos os utilizadores](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã:

    ![Selecione o novo utilizador](common/new-user.png)

3. Na **utilizador** diálogo caixa, siga os passos seguintes.

    ![Caixa de diálogo de utilizador](common/user-properties.png)

    1. Na **Name** , introduza **BrittaSimon**.
  
    1. Na **nome de utilizador** , introduza **BrittaSimon @\<yourcompanydomain >.\< extensão >**. (Por exemplo, BrittaSimon@contoso.com.)

    1. Selecione **mostrar palavra-passe**e, em seguida, anote o valor que está a **palavra-passe** caixa.

    1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, irá ativar a Eduarda Almeida utilizar o Azure AD início de sessão único, concedendo acesso para Percolate.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **Percolate**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Percolate**.

    ![Lista de aplicações](common/all-applications.png)

3. No painel esquerdo, selecione **utilizadores e grupos**:

    ![Selecionar utilizadores e grupos](common/users-groups-blade.png)

4. Selecione **adicionar utilizador**e, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Selecionar utilizadores e grupos](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista de utilizadores e, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se um valor de função na asserção de SAML, que espera do **selecionar função** diálogo caixa, selecione a função adequada para o utilizador a partir da lista. Clique nas **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** caixa de diálogo, selecione **atribuir**.

### <a name="create-a-percolate-test-user"></a>Criar um utilizador de teste Percolate

Para permitir que utilizadores do Azure AD iniciar sessão no Percolate, terá de adicioná-los ao Percolate. Tem de adicioná-los manualmente.

Para criar uma conta de utilizador, siga estes passos:

1. Inicie sessão no Percolate como administrador.

2. No painel esquerdo, selecione **usuários** sob **organização**. Selecione **novos utilizadores**:

    ![Selecione os novos utilizadores](./media/percolate-tutorial/configure03.png)

3. Sobre o **criar utilizadores** página, siga os passos seguintes.

    ![Criar página usuários](./media/percolate-tutorial/configure04.png)

    1. Na **E-Mail** , introduza o endereço de e-mail do utilizador. Por exemplo, brittasimon@contoso.com.

    1. Na **FullName** , introduza o nome do utilizador. Por exemplo, **Brittasimon**.

    1. Selecione **criar utilizadores**.

### <a name="test-single-sign-on"></a>Testar início de sessão único

Agora precisa testar a configuração do Azure AD única início de sessão utilizando o painel de acesso.

Ao selecionar o mosaico Percolate no painel de acesso, deve ser automaticamente conectado à instância Percolate para o qual configura o SSO. Para obter mais informações, consulte [aplicações de acesso e a utilização no portal minhas aplicações](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Tutoriais para integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)