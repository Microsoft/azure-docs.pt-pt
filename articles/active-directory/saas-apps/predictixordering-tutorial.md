---
title: 'Tutorial: Integração do Active Directory do Azure com a ordenação de Predictix | Documentos da Microsoft'
description: Neste tutorial, irá aprender como configurar o início de sessão único entre o Azure Active Directory e a ordenação de Predictix.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2fe2f976-e97f-4368-9695-3e1624409e8b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: 79e9952aefc44b7c490bb9fe375487c1a3335fc8
ms.sourcegitcommit: 179918af242d52664d3274370c6fdaec6c783eb6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/13/2019
ms.locfileid: "65560512"
---
# <a name="tutorial-azure-active-directory-integration-with-predictix-ordering"></a>Tutorial: Integração do Active Directory do Azure com Predictix ordenação

Neste tutorial, irá aprender como integrar a Predictix ordenação de mensagens em fila com o Azure Active Directory (Azure AD).
Esta integração oferece estes benefícios:

* Pode utilizar o Azure AD para controlar quem tem acesso a ordenação de Predictix.
* Pode permitir que os utilizadores ter automaticamente sessão iniciada no Predictix ordenação (início de sessão único) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central: portal do Azure.

Para saber mais sobre a integração de aplicações SaaS com o Azure AD, veja [início de sessão único para aplicações no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Se não tiver uma subscrição do Azure, [criar uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Predictix ordenação, tem de ter:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter um [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Uma subscrição de ordenação Predictix que tem início de sessão único ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, irá configurar e testar o Azure AD início de sessão único num ambiente de teste.

* Ordenação de Predictix suporta SSO iniciado por SP.

## <a name="add-predictix-ordering-from-the-gallery"></a>Adicionar Predictix ordenação da Galeria

Para configurar a integração de ordenação Predictix com o Azure AD, terá de adicionar Predictix ordenação da Galeria à sua lista de aplicações de SaaS geridas.

1. Na [portal do Azure](https://portal.azure.com), no painel esquerdo, selecione **Azure Active Directory**:

    ![Selecione Azure Active Directory](common/select-azuread.png)

2. Aceda a **aplicações empresariais** > **todas as aplicações**:

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar uma aplicação, selecione **nova aplicação** na parte superior da janela:

    ![Selecionar novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **ordenação Predictix**. Selecione **Predictix ordenação** nos resultados da pesquisa e, em seguida, selecione **Add**.

     ![Resultados da pesquisa](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, irá configurar e testar o Azure AD início de sessão único com Predictix ordenação com um utilizador de teste com o nome Eduarda Almeida.
Para ativar o início de sessão único, tem de estabelecer uma relação entre um utilizador do Azure AD e o utilizador correspondente no Predictix ordenação.

Para configurar e testar o Azure AD início de sessão único com Predictix ordenação, terá de concluir estes passos:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  para ativar a funcionalidade para os seus utilizadores.
2. **[Configurar a ordenação de Predictix início de sessão único](#configure-predictix-ordering-single-sign-on)**  no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  para testar o Azure AD início de sessão único.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  para ativar o Azure AD início de sessão único para o utilizador.
5. **[Criar um utilizador de teste Predictix ordenação](#create-a-predictix-ordering-test-user)**  associado a representação do Azure AD do utilizador.
6. **[Testar início de sessão único](#test-single-sign-on)**  para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, irá ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com Predictix ordenação, siga estes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Predictix ordenação** página de integração de aplicações, selecione **início de sessão único**:

    ![Selecione início de sessão único](common/select-sso.png)

2. Na **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único:

    ![Selecione um método de início de sessão único](common/select-saml-option.png)

3. Na **definir a segurança de início de sessão único com o SAML** página, selecione a **editar** ícone para abrir o **configuração básica de SAML** caixa de diálogo:

    ![Ícone editar](common/edit-urls.png)

4. Na **configuração básica de SAML** diálogo caixa, conclua os passos seguintes.

    ![Caixa de diálogo de configuração de SAML básica](common/sp-identifier.png)

    1. Na **iniciar sessão no URL** , introduza um URL neste padrão:

       `https://<companyname-pricing>.ordering.predictix.com/sso/request`

    1. Na **identificador (ID de entidade)** , introduza um URL neste padrão:

        | |
        |--|
        | `https://<companyname-pricing>.dev.ordering.predictix.com` |
        | `https://<companyname-pricing>.ordering.predictix.com` |
        | |

    > [!NOTE]
    > Estes valores são marcadores de posição. Tem de utilizar o URL de início de sessão real e o identificador. Contacte os [Predictix ordenação a equipa de suporte](https://www.predix.io/support/) para obter os valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** caixa de diálogo no portal do Azure.

5. No **definir a segurança de início de sessão único com o SAML** na página a **certificado de assinatura SAML** secção, selecione o **transferir** ligação junto a **certificado (Base64)** acordo com suas necessidades e guarde o certificado no seu computador:

    ![Ligação de transferência do certificado](common/certificatebase64.png)

6. Na **configurar a ordenação de Predictix** secção, copie os URLs adequados, com base nos seus requisitos:

    ![Copie os URLs de configuração](common/copy-configuration-urls.png)

    1. **URL de início de sessão**.

    2. **Azure AD Identifier**.

    3. **URL de fim de sessão**.

### <a name="configure-predictix-ordering-single-sign-on"></a>Configurar a ordenação de Predictix início de sessão único

Para configurar o início de sessão único no lado do Predictix ordenação, terá de enviar o certificado que transferiu e os URLs que copiou do portal do Azure para o [Predictix ordenação a equipa de suporte](https://www.predix.io/support/). Esta equipe garante que a ligação de SAML SSO está definida corretamente em ambos os lados.

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

Nesta secção, irá ativar a Eduarda Almeida utilizar o Azure AD início de sessão único, concedendo acesso para ordenação Predictix.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **Predictix ordenação**:

    ![Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **ordenação Predictix**.

    ![Lista de aplicações](common/all-applications.png)

3. No painel esquerdo, selecione **utilizadores e grupos**:

    ![Selecionar utilizadores e grupos](common/users-groups-blade.png)

4. Selecione **adicionar utilizador**e, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Selecione adicionar utilizador](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista de utilizadores e, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se um valor de função na asserção de SAML, que espera do **selecionar função** diálogo caixa, selecione a função adequada para o utilizador a partir da lista. Clique nas **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** caixa de diálogo, selecione **atribuir**.

### <a name="create-a-predictix-ordering-test-user"></a>Criar um utilizador de teste Predictix ordenação

Em seguida, terá de criar um utilizador com o nome Eduarda Almeida na Predictix ordenação. Trabalhar com o [Predictix ordenação a equipa de suporte](https://www.predix.io/support/) para adicionar utilizadores. Os utilizadores têm de ser criado e ativado antes de utilizar o início de sessão único.

### <a name="test-single-sign-on"></a>Testar início de sessão único

Agora precisa testar a configuração do Azure AD única início de sessão utilizando o painel de acesso.

Ao selecionar o mosaico Predictix ordenação no painel de acesso, deve ser automaticamente conectado para a instância de Predictix ordenação de mensagens em fila para o qual configura o SSO. Para obter mais informações, consulte [aplicações de acesso e a utilização no portal minhas aplicações](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Tutoriais para integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
