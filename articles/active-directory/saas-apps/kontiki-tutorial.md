---
title: 'Tutorial: Integração do Active Directory do Azure com Kontiki | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Kontiki.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8d5e5413-da4c-40d8-b1d0-f03ecfef030b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bfb154bce34b2ceda99b82c7ca3534b8a8ee0a1f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67098493"
---
# <a name="tutorial-azure-active-directory-integration-with-kontiki"></a>Tutorial: Integração do Active Directory do Azure com Kontiki

Neste tutorial, saiba como integrar Kontiki com o Azure Active Directory (Azure AD).

Integrar Kontiki no Azure AD oferece as seguintes vantagens:

* Pode utilizar o Azure AD para controlar quem tem acesso ao Kontiki.
* Os utilizadores podem ser automaticamente sessão iniciados no Kontiki com as suas contas do Azure AD (início de sessão único).
* Pode gerir as suas contas num local central, o portal do Azure.

Para obter mais informações sobre o software como uma integração de aplicação de serviço (SaaS) com o Azure AD, consulte [início de sessão único para aplicações no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Kontiki, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver uma subscrição do Azure AD, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Uma subscrição de Kontiki com início de sessão único ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste e integrar Kontiki no Azure AD.

Kontiki suporta as seguintes funcionalidades:

* **Iniciado por SP início de sessão único**
* **Aprovisionamento de utilizadores de just-in-time**

## <a name="add-kontiki-in-the-azure-portal"></a>Adicionar Kontiki no portal do Azure

Para integrar Kontiki com o Azure AD, tem de adicionar Kontiki à sua lista de aplicações de SaaS geridas.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. No menu da esquerda, selecione **do Azure Active Directory**.

    ![A opção do Azure Active Directory](common/select-azuread.png)

1. Selecione **aplicações empresariais** > **todas as aplicações**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

1. Para adicionar uma aplicação, selecione **nova aplicação**.

    ![A nova opção de aplicação](common/add-new-app.png)

1. Na caixa de pesquisa, introduza **Kontiki**. Nos resultados da pesquisa, selecione **Kontiki**e, em seguida, selecione **Add**.

    ![Kontiki na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Kontiki com base num utilizador de teste com o nome **Eduarda Almeida**. Para início de sessão único funcione, tem de estabelecer uma relação ligada entre um utilizador do Azure AD e o utilizador relacionado Kontiki.

Para configurar e testar o Azure AD início de sessão único com Kontiki, tem de concluir os seguintes blocos de construção:

| Tarefa | Descrição |
| --- | --- |
| **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)** | Permite que os utilizadores utilizar esta funcionalidade. |
| **[Configurar Kontiki início de sessão único](#configure-kontiki-single-sign-on)** | Configura as definições de início de sessão únicas no aplicativo. |
| **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)** | Testes do Azure AD início de sessão único para um utilizador com o nome Eduarda Almeida. |
| **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)** | Permite a Eduarda Almeida utilizar o Azure AD início de sessão único. |
| **[Criar um utilizador de teste Kontiki](#create-a-kontiki-test-user)** | Cria um equivalente da Eduarda Almeida na Kontiki que está ligado a representação do Azure AD do utilizador. |
| **[Testar início de sessão único](#test-single-sign-on)** | Verifica-se de que a configuração funciona. |

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, configura Azure AD início de sessão único com Kontiki no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na **Kontiki** painel de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a opção de início de sessão único](common/select-sso.png)

1. Na **selecionar um método de início de sessão único** painel, selecione **SAML** ou **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

1. Na **definir a segurança de início de sessão único com o SAML** painel, selecione **editar** (o ícone de lápis) para abrir o **configuração básica de SAML** painel.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

1. Na **configuração básica de SAML** painel, na **iniciar sessão no URL** texto, introduza um URL com o seguinte padrão: `https://<companyname>.mc.eval.kontiki.com`

    ![Kontiki domínio e URLs únicas início de sessão em informações](common/sp-signonurl.png)

    > [!NOTE]
    > Contacte os [equipa de suporte de cliente Kontiki](https://customersupport.kontiki.com/enterprise/contactsupport.html) para obter o valor correto a utilizar. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

1. Na **definir a segurança de início de sessão único com o SAML** painel, na **certificado de assinatura SAML** secção, selecione **transferir** junto a **XML de metadados de Federação**. Selecione uma opção de download com base nos seus requisitos. Guarde o certificado no seu computador.

    ![A opção de download de certificado do XML de metadados de Federação](common/metadataxml.png)

1. Na **configurar Kontiki** secção, copie os seguintes URLs com base nos seus requisitos:

    * URL de início de sessão
    * Identificador do Azure AD
    * URL de fim de sessão

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-kontiki-single-sign-on"></a>Configurar Kontiki início de sessão único

Para configurar o início de sessão único no lado do Kontiki, envie o ficheiro transferido do XML de metadados de Federação e os URLs relevantes que copiou do portal do Azure para o [equipa de suporte de Kontiki](https://customersupport.kontiki.com/enterprise/contactsupport.html). A equipa de suporte de Kontiki utiliza as informações de que enviá-las para se certificar de que a SAML única início de sessão na ligação está definida corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD 

Nesta secção, vai criar um utilizador de teste com o nome Eduarda Almeida no portal do Azure.

1. No portal do Azure, selecione **do Azure Active Directory** > **utilizadores** > **todos os utilizadores**.

    ![Os utilizadores e todas as opções de utilizadores](common/users.png)

1. Selecione **novo utilizador**.

    ![A nova opção de utilizador](common/new-user.png)

1. Na **utilizador** painel, conclua os seguintes passos:

    1. Na **Name** , introduza **BrittaSimon**.
  
    1. Na **nome de utilizador** , introduza **brittasimon\@\<seu domínio da empresa >.\< extensão >** . Por exemplo, **brittasimon\@contoso.com**.

    1. Selecione o **palavra-passe de Show** caixa de verificação. Anote o valor que é apresentado no **palavra-passe** caixa.

    1. Selecione **Criar**.

    ![O painel do utilizador](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, concede acesso de Eduarda Almeida a Kontiki para que ela pode utilizar o Azure início de sessão único.

1. No portal do Azure, selecione **aplicações empresariais** > **todas as aplicações** > **Kontiki**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

1. Na lista de aplicações, selecione **Kontiki**.

    ![Kontiki na lista de aplicações](common/all-applications.png)

1. No menu, selecione **utilizadores e grupos**.

    ![A opção de utilizadores e grupos](common/users-groups-blade.png)

1. Selecione **adicionar utilizador**. Em seguida, na **adicionar atribuição** painel, selecione **utilizadores e grupos**.

    ![O painel de atribuição de adicionar](common/add-assign-user.png)

1. Na **utilizadores e grupos** painel, selecione **Eduarda Almeida** na lista de utilizadores. Escolha **Selecionar**.

1. Se estiver à espera um valor de função na asserção de SAML, no **selecionar função** painel, selecione a função relevante para o utilizador a partir da lista. Escolha **Selecionar**.

1. Na **adicionar atribuição** painel, selecione **atribuir**.

### <a name="create-a-kontiki-test-user"></a>Criar um utilizador de teste Kontiki

Não existe nenhum item de ação para que possa configurar Kontiki o aprovisionamento de utilizador. Quando um utilizador atribuído tenta iniciar sessão no Kontiki com o portal as minhas aplicações, Kontiki verifica se o usuário existe. Não se for encontrada nenhuma conta de utilizador, Kontiki cria automaticamente a conta de utilizador.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar seu única início de sessão em configuração do Azure AD com o portal as minhas aplicações.

Depois de configurar início de sessão único, quando seleciona **Kontiki** portal as minhas aplicações, é automaticamente tem sessão iniciada no Kontiki. Para obter mais informações sobre o portal as minhas aplicações, consulte [aplicações de acesso e a utilização no portal minhas aplicações](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações, consulte estes artigos:

- [Lista de tutoriais de integração de aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Início de sessão único para aplicações no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
