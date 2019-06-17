---
title: 'Tutorial: Integração do Active Directory do Azure com o Gestor de certificados do Sectigo | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Gestor de certificados de Sectigo.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 62cd6987-3373-4b58-b1ff-589f4a3d70a9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 15-04-2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d7c7cf4972b1ee0a5add3b4611dc4c8655da875
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67091540"
---
# <a name="tutorial-azure-active-directory-integration-with-sectigo-certificate-manager"></a>Tutorial: Integração do Active Directory do Azure com o Gestor de certificados do Sectigo

Neste tutorial, saiba como integrar o Gestor de certificados de Sectigo com o Azure Active Directory (Azure AD).

Integrar o Gestor de certificados de Sectigo no Azure AD oferece as seguintes vantagens:

* Pode utilizar o Azure AD para controlar quem tem acesso para o Gestor de certificados do Sectigo.
* Os utilizadores podem iniciar sessão automaticamente Gestor de certificados para Sectigo com as suas contas do Azure AD (início de sessão único).
* Pode gerir as suas contas num local central, o portal do Azure.

Para obter mais informações sobre o software como uma integração de aplicação de serviço (SaaS) com o Azure AD, consulte [início de sessão único para aplicações no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Gestor de certificados do Sectigo, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver uma subscrição do Azure AD, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Gestor de certificados de Sectigo subscrição com início de sessão único ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste e integrar o Gestor de certificados de Sectigo no Azure AD.

Gestor de certificados de Sectigo suporta as seguintes funcionalidades:

* **Iniciado por SP início de sessão único**
* **IDP iniciada pelo início de sessão único**

## <a name="add-sectigo-certificate-manager-in-the-azure-portal"></a>Adicionar Gestor de certificados de Sectigo no portal do Azure

Para integrar o Gestor de certificados de Sectigo com o Azure AD, tem de adicionar o Gestor de certificados de Sectigo à sua lista de aplicações de SaaS geridas.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. No menu da esquerda, selecione **do Azure Active Directory**.

    ![A opção do Azure Active Directory](common/select-azuread.png)

1. Selecione **aplicações empresariais** > **todas as aplicações**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

1. Para adicionar uma aplicação, selecione **nova aplicação**.

    ![A nova opção de aplicação](common/add-new-app.png)

1. Na caixa de pesquisa, introduza **Gestor de certificados de Sectigo**. Nos resultados da pesquisa, selecione **Gestor de certificados de Sectigo**e, em seguida, selecione **Add**.

    ![Gestor de certificados de Sectigo na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Sectigo Gestor de certificados com base num utilizador de teste com o nome **Eduarda Almeida**. Para o início de sessão único funcione, tem de estabelecer uma relação ligada entre um utilizador do Azure AD e o utilizador relacionado no Gerenciador de certificados do Sectigo.

Para configurar e testar o Azure AD início de sessão único com o Gestor de certificados do Sectigo, tem de concluir os seguintes blocos de construção:

| Tarefa | Descrição |
| --- | --- |
| **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)** | Permite que os utilizadores utilizar esta funcionalidade. |
| **[Configurar o Gestor de certificados de Sectigo início de sessão único](#configure-sectigo-certificate-manager-single-sign-on)** | Configura as definições de início de sessão únicas no aplicativo. |
| **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)** | Testes do Azure AD início de sessão único para um utilizador com o nome Eduarda Almeida. |
| **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)** | Permite a Eduarda Almeida utilizar o Azure AD início de sessão único. |
| **[Criar um utilizador de teste do Gestor de certificados de Sectigo](#create-a-sectigo-certificate-manager-test-user)** | Cria um equivalente da Eduarda Almeida no Sectigo Gerenciador de certificados que está ligado a representação do Azure AD do utilizador. |
| **[Testar início de sessão único](#test-single-sign-on)** | Verifica-se de que a configuração funciona. |

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, é configurar o Azure AD início de sessão único com o Gestor de certificados do Sectigo no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na **Gestor de certificados de Sectigo** painel de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a opção de início de sessão único](common/select-sso.png)

1. Na **selecionar um método de início de sessão único** painel, selecione **SAML** ou **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

1. Na **definir a segurança de início de sessão único com o SAML** painel, selecione **editar** (o ícone de lápis) para abrir o **configuração básica de SAML** painel.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

1. Na **configuração básica de SAML** painel, para configurar *iniciada pelo IDP modo*, conclua os seguintes passos:

    1. Na **identificador** , introduza um dos seguintes URL:
       * https:\//cert-manager.com/shibboleth
       * https:\//hard.cert-manager.com/shibboleth

    1. Na **URL de resposta** , introduza um dos seguintes URL:
        * https:\//cert-manager.com/Shibboleth.sso/SAML2/POST
        * https:\//hard.cert-manager.com/Shibboleth.sso/SAML2/POST

    1. Selecione **definir URLs adicionais**.

    1. Na **estado de reencaminhamento** , introduza um dos seguintes URL:
       * https:\//cert-manager.com/customer/SSLSupport/idp
       * https:\//hard.cert-manager.com/customer/SSLSupport/idp

    ![URLs de domínio do Gestor de certificados de Sectigo e único informações de início de sessão](common/idp-relay.png)

1.  Para configurar a aplicação no *iniciado por SP modo*, conclua os seguintes passos:

    * Na **iniciar sessão no URL** , introduza um dos seguintes URL:
      * https:\//cert-manager.com/Shibboleth.sso/Login
      * https:\//hard.cert-manager.com/Shibboleth.sso/Login

      ![URLs de domínio do Gestor de certificados de Sectigo e único informações de início de sessão](common/both-signonurl.png)

1. Na **definir a segurança de início de sessão único com o SAML** painel, na **certificado de assinatura SAML** secção, selecione **transferir** junto a **certificado (Base64)** . Selecione uma opção de download com base nos seus requisitos. Guarde o certificado no seu computador.

    ![A opção de download de certificado (Base64)](common/certificatebase64.png)

1. Na **configure o Gestor de certificados do Sectigo** secção, copie os seguintes URLs com base nos seus requisitos:

    * URL de início de sessão
    * Identificador do Azure AD
    * URL de fim de sessão

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-sectigo-certificate-manager-single-sign-on"></a>Configurar o Gestor de certificados de Sectigo início de sessão único

Para configurar o início de sessão único no lado do Gestor de certificados de Sectigo, envie o ficheiro transferido do certificado (Base64) e os URLs relevantes que copiou do portal do Azure para o [equipa de suporte do Gestor de certificados de Sectigo](https://sectigo.com/support). A equipa de suporte do Gestor de certificados de Sectigo utiliza as informações de que enviá-las para se certificar de que a SAML única início de sessão na ligação está definida corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD 

Nesta secção, vai criar um utilizador de teste com o nome Eduarda Almeida no portal do Azure.

1. No portal do Azure, selecione **do Azure Active Directory** > **utilizadores** > **todos os utilizadores**.

    ![Os utilizadores e todas as opções de utilizadores](common/users.png)

1. Selecione **novo utilizador**.

    ![A nova opção de utilizador](common/new-user.png)

1. Na **utilizador** painel, conclua os seguintes passos:

    1. Na **Name** , introduza **BrittaSimon**.
  
    1. Na **nome de utilizador** , introduza **brittasimon\@\<seu domínio da empresa >.\< extensão\>** . Por exemplo, **brittasimon\@contoso.com**.

    1. Selecione o **palavra-passe de Show** caixa de verificação. Anote o valor que é apresentado no **palavra-passe** caixa.

    1. Selecione **Criar**.

    ![O painel do utilizador](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, pode conceder acesso Eduarda Almeida para o Gestor de certificados do Sectigo para que ela pode utilizar o Azure início de sessão único.

1. No portal do Azure, selecione **aplicações empresariais** > **todas as aplicações** > **Gestor de certificados de Sectigo**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

1. Na lista de aplicações, selecione **Gestor de certificados de Sectigo**.

    ![Gestor de certificados de Sectigo na lista de aplicações](common/all-applications.png)

1. No menu, selecione **utilizadores e grupos**.

    ![A opção de utilizadores e grupos](common/users-groups-blade.png)

1. Selecione **adicionar utilizador**. Em seguida, na **adicionar atribuição** painel, selecione **utilizadores e grupos**.

    ![O painel de atribuição de adicionar](common/add-assign-user.png)

1. Na **utilizadores e grupos** painel, selecione **Eduarda Almeida** na lista de utilizadores. Escolha **Selecionar**.

1. Se estiver à espera um valor de função na asserção de SAML, no **selecionar função** painel, selecione a função relevante para o utilizador a partir da lista. Escolha **Selecionar**.

1. Na **adicionar atribuição** painel, selecione **atribuir**.

### <a name="create-a-sectigo-certificate-manager-test-user"></a>Criar um utilizador de teste do Gestor de certificados de Sectigo

Nesta secção, vai criar um utilizador com o nome Eduarda Almeida no Gestor de certificados do Sectigo. Trabalhar com o [equipa de suporte do Gestor de certificados de Sectigo](https://sectigo.com/support) para adicionar o utilizador na plataforma do Gestor de certificados de Sectigo. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar seu única início de sessão em configuração do Azure AD com o portal as minhas aplicações.

Depois de configurar início de sessão único, quando seleciona **Gestor de certificados de Sectigo** no portal as minhas aplicações, tem automaticamente sessão iniciada para o Gestor de certificados do Sectigo. Para obter mais informações sobre o portal as minhas aplicações, consulte [aplicações de acesso e a utilização no portal minhas aplicações](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações, consulte estes artigos:

- [Lista de tutoriais de integração de aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Início de sessão único para aplicações no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


