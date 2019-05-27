---
title: 'Tutorial: Integração do Active Directory do Azure com ThirdLight | Documentos da Microsoft'
description: Neste tutorial, irá aprender como configurar o início de sessão único entre o Azure Active Directory e ThirdLight.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 168aae9a-54ee-4c2b-ab12-650a2c62b901
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 67c8dcfffd78d4c0114a96622235d6548627fa92
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66236966"
---
# <a name="tutorial-azure-active-directory-integration-with-thirdlight"></a>Tutorial: Integração do Active Directory do Azure com ThirdLight

Neste tutorial, irá aprender como integrar ThirdLight com o Azure Active Directory (Azure AD). Esta integração oferece estes benefícios:

* Pode utilizar o Azure AD para controlar quem tem acesso ao ThirdLight.
* Pode permitir que os utilizadores ter automaticamente sessão iniciada no ThirdLight (início de sessão único) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central: portal do Azure.

Se quiser saber mais sobre a integração de aplicações SaaS com o Azure AD, veja [início de sessão único para aplicações no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com ThirdLight, tem de ter:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter um [conta gratuita](https://azure.microsoft.com/free/).
* Uma subscrição de ThirdLight que tem início de sessão único ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, irá configurar e testar o Azure AD início de sessão único num ambiente de teste.

* ThirdLight suporta SSO iniciado por SP.

## <a name="add-thirdlight-from-the-gallery"></a>Adicionar ThirdLight a partir da Galeria

Para configurar a integração do ThirdLight com o Azure AD, terá de adicionar ThirdLight a partir da Galeria à sua lista de aplicações de SaaS geridas.

1. Na [portal do Azure](https://portal.azure.com), no painel esquerdo, selecione **Azure Active Directory**:

    ![Selecione Azure Active Directory](common/select-azuread.png)

2. Aceda a **aplicações empresariais** > **todas as aplicações**:

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar uma aplicação, selecione **nova aplicação** na parte superior da janela:

    ![Selecionar novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **ThirdLight**. Selecione **ThirdLight** nos resultados da pesquisa e, em seguida, selecione **Add**.

     ![Resultados da pesquisa](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, irá configurar e testar o Azure AD início de sessão único com ThirdLight com um utilizador de teste com o nome Eduarda Almeida.
Para ativar o início de sessão único, tem de estabelecer uma relação entre um utilizador do Azure AD e o utilizador correspondente no ThirdLight.

Para configurar e testar o Azure AD início de sessão único com ThirdLight, tem de concluir estes passos:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  para ativar a funcionalidade para os seus utilizadores.
2. **[Configurar ThirdLight início de sessão único](#configure-thirdlight-single-sign-on)**  no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  para testar o Azure AD início de sessão único.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  para ativar o Azure AD início de sessão único para o utilizador.
5. **[Criar um utilizador de teste ThirdLight](#create-a-thirdlight-test-user)**  associado a representação do Azure AD do utilizador.
6. **[Testar início de sessão único](#test-single-sign-on)**  para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, irá ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com ThirdLight, siga estes passos:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicações do ThirdLight, selecione **início de sessão único**:

    ![Selecione início de sessão único](common/select-sso.png)

2. Na **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único:

    ![Selecione um método de início de sessão único](common/select-saml-option.png)

3. Na **definir a segurança de início de sessão único com o SAML** página, selecione a **editar** ícone para abrir o **configuração básica de SAML** caixa de diálogo:

    ![Ícone editar](common/edit-urls.png)

4. Na **configuração básica de SAML** diálogo caixa, conclua os passos seguintes.

    ![Caixa de diálogo de configuração de SAML básica](common/sp-identifier.png)

    1. Na **iniciar sessão no URL** , introduza um URL neste padrão:
    
          `https://<subdomain>.thirdlight.com/`

    1. Na **identificador (ID de entidade)** , introduza um URL neste padrão:

       `https://<subdomain>.thirdlight.com/saml/sp`

       > [!NOTE]
       > Estes valores são marcadores de posição. Tem de utilizar o URL de início de sessão real e o identificador. Contacte os [equipa de suporte de ThirdLight](https://www.thirdlight.com/support) para obter os valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** caixa de diálogo no portal do Azure.

5. No **definir a segurança de início de sessão único com o SAML** na página a **certificado de assinatura SAML** secção, selecione o **transferir** ligação junto a **XML de metadados de Federação** acordo com suas necessidades e guarde-no seu computador:

    ![Ligação de transferência do certificado](common/metadataxml.png)

6. Na **configurar ThirdLight** secção, copie os URLs adequados, com base nos seus requisitos:

    ![Copie os URLs de configuração](common/copy-configuration-urls.png)

    1. **URL de início de sessão**.

    1. **Azure AD Identifier**.

    1. **URL de fim de sessão**.

### <a name="configure-thirdlight-single-sign-on"></a>Configurar ThirdLight início de sessão único

1. Numa nova janela de browser web, inicie sessão no site da sua empresa ThirdLight como um administrador.

1. Aceda a **Configuration** > **administração do sistema** > **SAML2**:

    ![Administração do sistema](./media/thirdlight-tutorial/ic805843.png "administração do sistema")

1. Na secção de configuração SAML2, siga os passos seguintes.
  
    ![Seção de configuração de SAML2](./media/thirdlight-tutorial/ic805844.png "SAML2 seção de configuração")

    1. Selecione **ativar SAML2 início de sessão único**.

    1. Sob **origem para os metadados de IdP**, selecione **carga IdP metadados a partir do XML**.

    1. Abra o ficheiro de metadados que transferiu a partir do portal do Azure na secção anterior. Copiar o conteúdo do arquivo e cole-o para o **XML de metadados de IdP** caixa.

    1. Selecione **SAML2 guardar definições**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta secção, irá criar um utilizador de teste com o nome Eduarda Almeida no portal do Azure.

1. No portal do Azure, selecione **do Azure Active Directory** no painel esquerdo, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**:

    ![Selecione todos os utilizadores](common/users.png)

2. Selecione **novo utilizador** na parte superior da janela:

    ![Selecione o novo utilizador](common/new-user.png)

3. Na **utilizador** diálogo caixa, siga os passos seguintes.

    ![Caixa de diálogo de utilizador](common/user-properties.png)

    1. Na **Name** , introduza **BrittaSimon**.
  
    1. Na **nome de utilizador** , introduza **BrittaSimon @\<yourcompanydomain >.\< extensão >** . (Por exemplo, BrittaSimon@contoso.com.)

    1. Selecione **mostrar palavra-passe**e, em seguida, anote o valor que está a **palavra-passe** caixa.

    1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, irá ativar a Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para ThirdLight.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **ThirdLight**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **ThirdLight**.

    ![Lista de aplicações](common/all-applications.png)

3. No painel esquerdo, selecione **utilizadores e grupos**:

    ![Selecionar utilizadores e grupos](common/users-groups-blade.png)

4. Selecione **adicionar utilizador**e, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Selecione adicionar utilizador](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista de utilizadores e, em seguida, clique o **selecionar** na parte inferior da janela.

6. Se um valor de função na asserção de SAML, que espera do **selecionar função** diálogo caixa, selecione a função adequada para o utilizador a partir da lista. Clique nas **selecione** na parte inferior da janela.

7. Na **adicionar atribuição** caixa de diálogo, selecione **atribuir**.

### <a name="create-a-thirdlight-test-user"></a>Criar um utilizador de teste ThirdLight

Para permitir que utilizadores do Azure AD iniciar sessão no ThirdLight, terá de adicioná-los ao ThirdLight. Tem de adicioná-los manualmente.

Para criar uma conta de utilizador, siga estes passos:

1. Inicie sessão no site da sua empresa ThirdLight como um administrador.

1. Vá para o **utilizadores** separador.

1. Selecione **utilizadores e grupos**.

1. Selecione **adicionar novo utilizador**.

1. Introduza o nome de utilizador, um nome ou descrição e o endereço de e-mail de um válido do Azure AD da conta que pretende aprovisionar. Escolha uma configuração predefinida ou um grupo de novos membros.

1. Selecione **Criar**.

> [!NOTE]
> Pode utilizar qualquer ferramenta de criação de conta de utilizador ou API fornecidos pela ThirdLight para aprovisionar contas de utilizador do Azure AD.

### <a name="test-single-sign-on"></a>Testar início de sessão único

Agora precisa testar a configuração do Azure AD única início de sessão utilizando o painel de acesso.

Ao selecionar o mosaico ThirdLight no painel de acesso, deve ser automaticamente conectado à instância ThirdLight para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [aplicações de acesso e a utilização no portal minhas aplicações](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Tutoriais para integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
