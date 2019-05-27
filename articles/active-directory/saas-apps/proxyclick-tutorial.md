---
title: 'Tutorial: Integração do Active Directory do Azure com Proxyclick | Documentos da Microsoft'
description: Neste tutorial, irá aprender como configurar o início de sessão único entre o Azure Active Directory e Proxyclick.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5c58a859-71c2-4542-ae92-e5f16a8e7f18
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: fa146ad5a36cc74a65ec6d3dee98b2ef35bc65ad
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66240393"
---
# <a name="tutorial-azure-active-directory-integration-with-proxyclick"></a>Tutorial: Integração do Active Directory do Azure com Proxyclick

Neste tutorial, irá aprender como integrar Proxyclick com o Azure Active Directory (Azure AD).
Esta integração oferece estes benefícios:

* Pode utilizar o Azure AD para controlar quem tem acesso ao Proxyclick.
* Pode permitir que os utilizadores ter automaticamente sessão iniciada no Proxyclick (início de sessão único) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central: portal do Azure.

Para saber mais sobre a integração de aplicações SaaS com o Azure AD, veja [início de sessão único para aplicações no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Proxyclick, tem de ter:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode inscrever-se para obter um [versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).
* Uma subscrição de Proxyclick que tem início de sessão único ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, irá configurar e testar o Azure AD início de sessão único num ambiente de teste.

* Proxyclick suporta SSO iniciado por SP e iniciado o IdP.

## <a name="add-proxyclick-from-the-gallery"></a>Adicionar Proxyclick a partir da Galeria

Para configurar a integração do Proxyclick com o Azure AD, terá de adicionar Proxyclick a partir da Galeria à sua lista de aplicações de SaaS geridas.

1. Na [portal do Azure](https://portal.azure.com), no painel esquerdo, selecione **Azure Active Directory**:

    ![Selecione Azure Active Directory](common/select-azuread.png)

2. Aceda a **aplicações empresariais** > **todas as aplicações**:

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar uma aplicação, selecione **nova aplicação** na parte superior da janela:

    ![Selecionar novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **Proxyclick**. Selecione **Proxyclick** nos resultados da pesquisa e, em seguida, selecione **Add**.

     ![Resultados da pesquisa](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, irá configurar e testar o Azure AD início de sessão único com Proxyclick com um utilizador de teste com o nome Eduarda Almeida.
Para ativar o início de sessão único, tem de estabelecer uma relação entre um utilizador do Azure AD e o utilizador correspondente no Proxyclick.

Para configurar e testar o Azure AD início de sessão único com Proxyclick, tem de concluir estes passos:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  para ativar a funcionalidade para os seus utilizadores.
2. **[Configurar Proxyclick início de sessão único](#configure-proxyclick-single-sign-on)**  no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  para testar o Azure AD início de sessão único.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  para ativar o Azure AD início de sessão único para o utilizador.
5. **[Criar um utilizador de teste Proxyclick](#create-a-proxyclick-test-user)**  associado a representação do Azure AD do utilizador.
6. **[Testar início de sessão único](#test-single-sign-on)**  para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, irá ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com Proxyclick, siga estes passos:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicações do Proxyclick, selecione **início de sessão único**:

    ![Selecione início de sessão único](common/select-sso.png)

2. Na **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único:

    ![Selecione um método de início de sessão único](common/select-saml-option.png)

3. Na **definir a segurança de início de sessão único com o SAML** página, selecione a **editar** ícone para abrir o **configuração básica de SAML** caixa de diálogo:

    ![Ícone editar](common/edit-urls.png)

4. Na **configuração básica de SAML** caixa de diálogo, se quiser configurar a aplicação no modo de iniciado o IdP, siga os passos seguintes.

    ![Caixa de diálogo de configuração de SAML básica](common/idp-intiated.png)

    1. Na **identificador** , introduza um URL neste padrão:
   
       `https://saml.proxyclick.com/init/<companyId>`

    1. Na **URL de resposta** , introduza um URL neste padrão:

       `https://saml.proxyclick.com/consume/<companyId>`

5. Se quiser configurar a aplicação no modo iniciado por SP, selecione **definir URLs adicionais**. Na **iniciar sessão no URL** , introduza um URL neste padrão:
   
   `https://saml.proxyclick.com/init/<companyId>`

    ![Proxyclick domínio e URLs únicas início de sessão em informações](common/metadata-upload-additional-signon.png)

    

    > [!NOTE]
    > Estes valores são marcadores de posição. Tem de utilizar o identificador real, URL de resposta e início de sessão no URL. Passos para obter estes valores são descritos posteriormente neste tutorial.

6. No **definir a segurança de início de sessão único com o SAML** na página a **certificado de assinatura SAML** secção, selecione o **transferir** ligação junto a **certificado (Base64)** acordo com suas necessidades e guarde o certificado no seu computador:

    ![Ligação de transferência do certificado](common/certificatebase64.png)

7. Na **configurar Proxyclick** secção, copie os URLs adequados, com base nos seus requisitos:

    ![Copie os URLs de configuração](common/copy-configuration-urls.png)

    1. **URL de início de sessão**.

    1. **Azure AD Identifier**.

    1. **URL de fim de sessão**.

### <a name="configure-proxyclick-single-sign-on"></a>Configurar Proxyclick início de sessão único

1. Numa nova janela de browser web, inicie sessão no site da sua empresa Proxyclick como um administrador.

2. Selecione **conta de & definições**:

    ![Selecione a conta & definições](./media/proxyclick-tutorial/configure1.png)

3. Desloque para baixo para o **integrações** secção e selecione **SAML**:

    ![Selecione SAML](./media/proxyclick-tutorial/configure2.png)

4. Na **SAML** secção, siga os passos seguintes.

    ![Secção SAML](./media/proxyclick-tutorial/configure3.png)

    1. Cópia a **URL de consumidor de SAML** valor e cole-o no **URL de resposta** caixa o **configuração básica de SAML** caixa de diálogo no portal do Azure.

    1. Copiar o **URL de redirecionamento de SSO SAML** valor e cole-o no **iniciar sessão no URL** e **identificador** caixas no **configuração básica de SAML** caixa de diálogo no portal do Azure.

    1. Na **método de pedido de SAML** lista, selecione **redirecionamento de HTTP**.

    1. Na **emissor** caixa, cole a **do Azure AD identificador** valor que copiou do portal do Azure.

    1. Na **URL de ponto final do SAML 2.0** caixa, cole a **URL de início de sessão** valor que copiou do portal do Azure.

    1. No bloco de notas, abra o ficheiro de certificado que transferiu a partir do portal do Azure. Cole o conteúdo deste ficheiro para o **certificado** caixa.

    1. Selecione **guardar alterações**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta secção, irá criar um utilizador de teste com o nome Eduarda Almeida no portal do Azure.

1. No portal do Azure, selecione **do Azure Active Directory** no painel esquerdo, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**:

    ![Selecione todos os utilizadores](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã:

    ![Selecione o novo utilizador](common/new-user.png)

3. Na **utilizador** diálogo caixa, siga os passos seguintes.

    ![Caixa de diálogo de utilizador](common/user-properties.png)

    1. Na **Name** , introduza **BrittaSimon**.
  
    1. Na **nome de utilizador** , introduza **BrittaSimon @\<yourcompanydomain >.\< extensão >** . (Por exemplo, BrittaSimon@contoso.com.)

    1. Selecione **mostrar palavra-passe**e, em seguida, anote o valor que está a **palavra-passe** caixa.

    1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, irá ativar a Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Proxyclick.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **Proxyclick**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Proxyclick**.

    ![Lista de aplicações](common/all-applications.png)

3. No painel esquerdo, selecione **utilizadores e grupos**:

    ![Selecionar utilizadores e grupos](common/users-groups-blade.png)

4. Selecione **adicionar utilizador**e, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Selecione adicionar utilizador](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista de utilizadores e, em seguida, clique o **selecionar** na parte inferior da janela.

6. Se um valor de função na asserção de SAML, que espera do **selecionar função** diálogo caixa, selecione a função adequada para o utilizador a partir da lista. Clique nas **selecione** na parte inferior da janela.

7. Na **adicionar atribuição** caixa de diálogo, selecione **atribuir**.

### <a name="create-a-proxyclick-test-user"></a>Criar um utilizador de teste Proxyclick

Para permitir que utilizadores do Azure AD iniciar sessão no Proxyclick, terá de adicioná-los ao Proxyclick. Tem de adicioná-los manualmente.

Para criar uma conta de utilizador, siga estes passos:

1. Inicie sessão no site da sua empresa Proxyclick como um administrador.

1. Selecione **colegas** na parte superior da janela:

    ![Selecione os colegas](./media/proxyclick-tutorial/user1.png)

1. Selecione **adicionar colega**:

    ![Selecione adicionar colega](./media/proxyclick-tutorial/user2.png)

1. Na **adicionar um colega** secção, siga os passos seguintes.

    ![Adicionar uma secção de colega](./media/proxyclick-tutorial/user3.png)

    1. Na **E-Mail** , introduza o endereço de e-mail do utilizador. Neste caso, **brittasimon\@contoso.com**.

    1. Na **nome próprio** , introduza o nome do utilizador. Neste caso, **Eduarda**.

    1. Na **Apelido** , digite o apelido do utilizador. Neste caso, **Simon**.

    1. Selecione **adicionar utilizador**.

### <a name="test-single-sign-on"></a>Testar início de sessão único

Agora precisa testar a configuração do Azure AD única início de sessão utilizando o painel de acesso.

Ao selecionar o mosaico Proxyclick no painel de acesso, deve ser automaticamente conectado à instância Proxyclick para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [aplicações de acesso e a utilização no portal minhas aplicações](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Tutoriais para integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

