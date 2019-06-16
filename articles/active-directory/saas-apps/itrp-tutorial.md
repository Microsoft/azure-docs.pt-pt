---
title: 'Tutorial: Integração do Active Directory do Azure com ITRP | Documentos da Microsoft'
description: Neste tutorial, irá aprender como configurar o início de sessão único entre o Azure Active Directory e ITRP.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e09716a3-4200-4853-9414-2390e6c10d98
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: c3be09c998399cdca886f207bf5fc621bf51d8dd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67099602"
---
# <a name="tutorial-azure-active-directory-integration-with-itrp"></a>Tutorial: Integração do Active Directory do Azure com ITRP

Neste tutorial, irá aprender como integrar ITRP com o Azure Active Directory (Azure AD).
Esta integração oferece estes benefícios:

* Pode utilizar o Azure AD para controlar quem tem acesso ao ITRP.
* Pode permitir que os utilizadores ter automaticamente sessão iniciada no ITRP (início de sessão único) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central: portal do Azure.

Para saber mais sobre a integração de aplicações SaaS com o Azure AD, veja [início de sessão único para aplicações no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com ITRP, tem de ter:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter um [conta gratuita](https://azure.microsoft.com/free/).
* Uma subscrição de ITRP com início de sessão único ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, irá configurar e testar o Azure AD início de sessão único num ambiente de teste.

* ITRP suporta SSO iniciado por SP.

## <a name="add-itrp-from-the-gallery"></a>Adicionar ITRP a partir da Galeria

Para configurar a integração do ITRP com o Azure AD, terá de adicionar ITRP a partir da Galeria à sua lista de aplicações de SaaS geridas.

1. Na [portal do Azure](https://portal.azure.com), no painel esquerdo, selecione **Azure Active Directory**:

    ![Selecione Azure Active Directory](common/select-azuread.png)

2. Aceda a **aplicações empresariais** > **todas as aplicações**:

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar uma aplicação, selecione **nova aplicação** na parte superior da janela:

    ![Selecionar novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **ITRP**. Selecione **ITRP** nos resultados da pesquisa e, em seguida, selecione **Add**.

     ![Resultados da pesquisa](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, irá configurar e testar o Azure AD início de sessão único com ITRP com um utilizador de teste com o nome Eduarda Almeida.
Para ativar o início de sessão único, tem de estabelecer uma relação entre um utilizador do Azure AD e o utilizador correspondente no ITRP.

Para configurar e testar o Azure AD início de sessão único com ITRP, tem de concluir estes passos:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  para ativar a funcionalidade para os seus utilizadores.
2. **[Configurar ITRP início de sessão único](#configure-itrp-single-sign-on)**  no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  para testar o Azure AD início de sessão único.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  para ativar o Azure AD início de sessão único para o utilizador.
5. **[Criar um utilizador de teste ITRP](#create-an-itrp-test-user)**  associado a representação do Azure AD do utilizador.
6. **[Testar início de sessão único](#test-single-sign-on)**  para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, irá ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com ITRP, siga estes passos:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicações do ITRP, selecione **início de sessão único**:

    ![Selecione início de sessão único](common/select-sso.png)

2. Na **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único:

    ![Selecionar um método de início de sessão único](common/select-saml-option.png)

3. Na **definir a segurança de início de sessão único com o SAML** página, selecione a **editar** ícone para abrir o **configuração básica de SAML** caixa de diálogo:

    ![Ícone editar](common/edit-urls.png)

4. Na **configuração básica de SAML** diálogo caixa, siga os passos seguintes.

    ![Caixa de diálogo de configuração de SAML básica](common/sp-identifier.png)

    1. Na **iniciar sessão no URL** , introduza um URL neste padrão:
    
       `https://<tenant-name>.itrp.com`

    1. Na **identificador (ID de entidade)** , introduza um URL neste padrão:

       `https://<tenant-name>.itrp.com`

    > [!NOTE]
    > Estes valores são marcadores de posição. Tem de utilizar o URL de início de sessão real e o identificador. Contacte os [equipa de suporte de ITRP](https://www.itrp.com/support) para obter os valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** caixa de diálogo no portal do Azure.

5. Na **certificado de assinatura SAML** secção, selecione a **editar** ícone para abrir o **certificado de assinatura SAML** caixa de diálogo:

    ![Ícone editar](common/edit-certificate.png)

6. Na **certificado de assinatura SAML** caixa de diálogo, copie a **Thumbprint** valor e guardá-lo:

    ![Copie o valor do Thumbprint](common/copy-thumbprint.png)

7. Na **configurar ITRP** secção, copie os URLs adequados, com base nos seus requisitos:

    ![Copie os URLs de configuração](common/copy-configuration-urls.png)

    1. **URL de início de sessão**.

    1. **Azure AD Identifier**.

    1. **URL de fim de sessão**.

### <a name="configure-itrp-single-sign-on"></a>Configurar ITRP início de sessão único

1. Numa nova janela de browser web, inicie sessão no site da sua empresa ITRP como um administrador.

1. Na parte superior da janela, selecione o **definições** ícone:

    ![Ícone de definições](./media/itrp-tutorial/ic775570.png "ícone de definições")

1. No painel esquerdo, selecione **Single Sign-On**:

    ![Selecione início de sessão único](./media/itrp-tutorial/ic775571.png "selecione início de sessão único")

1. Na **Single Sign-On** configuração secção, siga os passos seguintes.

    ![Single Sign-On seção](./media/itrp-tutorial/ic775572.png "secção início de sessão único")

    ![Single Sign-On seção](./media/itrp-tutorial/ic775573.png "secção início de sessão único")

    1. Selecione **ativada**.

    1. Na **URL de fim de sessão remoto** caixa, cole a **URL de fim de sessão** valor que copiou do portal do Azure.

    1. Na **URL de SSO SAML** caixa, cole a **URL de início de sessão** valor que copiou do portal do Azure.

    1. Na **impressão digital de certificado** caixa, cole a **Thumbprint** valor do certificado, que copiou do portal do Azure.

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
  
    1. Na **nome de utilizador** , introduza **BrittaSimon @\<yourcompanydomain >.\< extensão >** . (Por exemplo, BrittaSimon@contoso.com.)

    1. Selecione **mostrar palavra-passe**e, em seguida, anote o valor que está a **palavra-passe** caixa.

    1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, irá ativar a Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para ITRP.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **ITRP**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **ITRP**.

    ![Lista de aplicações](common/all-applications.png)

3. No painel esquerdo, selecione **utilizadores e grupos**:

    ![Selecionar utilizadores e grupos](common/users-groups-blade.png)

4. Selecione **adicionar utilizador**e, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Selecione adicionar utilizador](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista de utilizadores e, em seguida, clique o **selecionar** na parte inferior da janela.

6. Se um valor de função na asserção de SAML, que espera do **selecionar função** diálogo caixa, selecione a função adequada para o utilizador a partir da lista. Clique nas **selecione** na parte inferior da janela.

7. Na **adicionar atribuição** caixa de diálogo, selecione **atribuir**.

### <a name="create-an-itrp-test-user"></a>Criar um utilizador de teste ITRP

Para permitir que utilizadores do Azure AD iniciar sessão no ITRP, terá de adicioná-los ao ITRP. Tem de adicioná-los manualmente.

Para criar uma conta de utilizador, siga estes passos:

1. Inicie sessão no seu inquilino ITRP.

1. Na parte superior da janela, selecione o **registos** ícone:

    ![Ícone de registos](./media/itrp-tutorial/ic775575.png "ícone de registos")

1. No menu, selecione **pessoas**:

    ![Selecione as pessoas](./media/itrp-tutorial/ic775587.png "selecionar pessoas")

1. Selecione o sinal ( **+** ) para adicionar uma nova pessoa:

    ![Selecione o sinal](./media/itrp-tutorial/ic775576.png "selecione o sinal")

1. Na **Adicionar nova pessoa** diálogo caixa, siga os passos seguintes.

    ![Caixa de diálogo Adicionar nova pessoa](./media/itrp-tutorial/ic775577.png "caixa de diálogo Adicionar nova pessoa")

    1. Introduza o nome e endereço de e-mail de um Azure válido conta AD que pretende adicionar.

    1. Selecione **Guardar**.

> [!NOTE]
> Pode utilizar qualquer ferramenta de criação de conta de utilizador ou API fornecidos pela ITRP para aprovisionar contas de utilizador do Azure AD.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Agora precisa testar a configuração do Azure AD única início de sessão utilizando o painel de acesso.

Ao selecionar o mosaico ITRP no painel de acesso, deve ser automaticamente conectado à instância ITRP para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [aplicações de acesso e a utilização no portal minhas aplicações](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Tutoriais para integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
