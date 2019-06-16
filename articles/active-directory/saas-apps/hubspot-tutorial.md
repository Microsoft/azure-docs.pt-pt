---
title: 'Tutorial: Integração do Active Directory do Azure com HubSpot | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e HubSpot.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 57343ccd-53ea-4e62-9e54-dee2a9562ed5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3b2c765778fc2bdd8425cc3f375831c0d317e753
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67100898"
---
# <a name="tutorial-azure-active-directory-integration-with-hubspot"></a>Tutorial: Integração do Active Directory do Azure com HubSpot

Neste tutorial, saiba como integrar HubSpot com o Azure Active Directory (Azure AD).

Integrar HubSpot no Azure AD oferece as seguintes vantagens:

* Pode utilizar o Azure AD para controlar quem tem acesso ao HubSpot.
* Os utilizadores podem ser automaticamente sessão iniciados no HubSpot com as suas contas do Azure AD (início de sessão único).
* Pode gerir as suas contas num local central, o portal do Azure.

Para obter mais informações sobre o software como uma integração de aplicação de serviço (SaaS) com o Azure AD, consulte [início de sessão único para aplicações no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com HubSpot, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver uma subscrição do Azure AD, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Uma subscrição de HubSpot com início de sessão único ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste e integrar HubSpot no Azure AD.

HubSpot suporta as seguintes funcionalidades:

* **Iniciado por SP início de sessão único**
* **IDP iniciada pelo início de sessão único**

## <a name="add-hubspot-in-the-azure-portal"></a>Adicionar HubSpot no portal do Azure

Para integrar HubSpot com o Azure AD, tem de adicionar HubSpot à sua lista de aplicações de SaaS geridas.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. No menu da esquerda, selecione **do Azure Active Directory**.

    ![A opção do Azure Active Directory](common/select-azuread.png)

1. Selecione **aplicações empresariais** > **todas as aplicações**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

1. Para adicionar uma aplicação, selecione **nova aplicação**.

    ![A nova opção de aplicação](common/add-new-app.png)

1. Na caixa de pesquisa, introduza **HubSpot**. Nos resultados da pesquisa, selecione **HubSpot**e, em seguida, selecione **Add**.

    ![HubSpot na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com HubSpot com base num utilizador de teste com o nome **Eduarda Almeida**. Para início de sessão único funcione, tem de estabelecer uma relação ligada entre um utilizador do Azure AD e o utilizador relacionado HubSpot.

Para configurar e testar o Azure AD início de sessão único com HubSpot, tem de concluir os seguintes blocos de construção:

| Tarefa | Descrição |
| --- | --- |
| **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)** | Permite que os utilizadores utilizar esta funcionalidade. |
| **[Configurar HubSpot início de sessão único](#configure-hubspot-single-sign-on)** | Configura as definições de início de sessão únicas no aplicativo. |
| **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)** | Testes do Azure AD início de sessão único para um utilizador com o nome Eduarda Almeida. |
| **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)** | Permite a Eduarda Almeida utilizar o Azure AD início de sessão único. |
| **[Criar um utilizador de teste HubSpot](#create-a-hubspot-test-user)** | Cria um equivalente da Eduarda Almeida na HubSpot que está ligado a representação do Azure AD do utilizador. |
| **[Testar início de sessão único](#test-single-sign-on)** | Verifica-se de que a configuração funciona. |

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, configura Azure AD início de sessão único com HubSpot no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na **HubSpot** painel de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a opção de início de sessão único](common/select-sso.png)

1. Na **selecionar um método de início de sessão único** painel, selecione **SAML** ou **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

1. Na **definir a segurança de início de sessão único com o SAML** painel, selecione **editar** (o ícone de lápis) para abrir o **configuração básica de SAML** painel.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

1. Na **configuração básica de SAML** painel, para configurar *iniciada pelo IDP modo*, conclua os seguintes passos:

    1. Na **identificador** , introduza um URL com o seguinte padrão: https:\//api.hubspot.com/login-api/v1/saml/login?portalId=\<ID de cliente\>.

    1. Na **URL de resposta** , introduza um URL com o seguinte padrão: https:\//api.hubspot.com/login-api/v1/saml/acs?portalId=\<ID de cliente\>.

    ![Informações de início de sessão de único HubSpot domínio e URLs](common/idp-intiated.png)

    > [!NOTE]
    > Para formatar os URLs, também pode consultar os padrões que mostra a **configuração básica de SAML** painel no portal do Azure.

1. Para configurar a aplicação no *iniciado por SP* modo:

    1. Selecione **definir URLs adicionais**.

    1. Na **iniciar sessão no URL** , introduza **https:\//app.hubspot.com/login**.

    ![A opção de URLs adicional do conjunto](common/metadata-upload-additional-signon.png)

1. Na **definir a segurança de início de sessão único com o SAML** painel, na **certificado de assinatura SAML** secção, selecione **transferir** junto a **certificado (Base64)** . Selecione uma opção de download com base nos seus requisitos. Guarde o certificado no seu computador.

    ![A opção de download de certificado (Base64)](common/certificatebase64.png)

1. Na **configurar HubSpot** secção, copie os seguintes URLs com base nos seus requisitos:

    * URL de início de sessão
    * Identificador do Azure AD
    * URL de fim de sessão

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-hubspot-single-sign-on"></a>Configurar HubSpot início de sessão único

1. Abra um novo separador no seu browser e inicie sessão na sua conta de administrador HubSpot.

1. Selecione o **definições** ícone no canto superior direito da página.

    ![O ícone de definições no HubSpot](./media/hubspot-tutorial/config1.png)

1. Selecione **predefinições de conta**.

    ![A opção de conta padrão é de mensagens em fila no HubSpot](./media/hubspot-tutorial/config2.png)

1. Desloque para baixo para o **Security** secção e, em seguida, selecione **configurar**.

    ![A configuração de opção no HubSpot](./media/hubspot-tutorial/config3.png)

1. Na **configurar o início de sessão único** secção, conclua os seguintes passos:

    1. Na **URl de público-alvo (ID de entidade do fornecedor de serviço)** caixa, selecione **cópia** para copiar o valor. No portal do Azure, no **configuração básica de SAML** painel, cole o valor no **identificador** caixa.

    1. Na **iniciar sessão no URl, o ACS, o destinatário ou de redirecionamento** caixa, selecione **cópia** para copiar o valor. No portal do Azure, no **configuração básica de SAML** painel, cole o valor no **URL de resposta** caixa.

    1. No HubSpot, no **identificador do fornecedor de identidade ou URL do emissor** caixa, cole o valor de **do Azure AD identificador** que copiou no portal do Azure.

    1. No HubSpot, no **URL fornecedor de identidade única Sign-On** caixa, cole o valor de **URL de início de sessão** que copiou no portal do Azure.

    1. No bloco de notas do Windows, abra o ficheiro de Certificate(Base64) que transferiu. Selecione e copie o conteúdo do ficheiro. Em seguida, no HubSpot, cole-a no **certificado X.509** caixa.

    1. Selecione **verificar**.

        ![A configuração na secção de início de sessão única em HubSpot](./media/hubspot-tutorial/config4.png)

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

Nesta secção, concede acesso de Eduarda Almeida a HubSpot para que ela pode utilizar o Azure início de sessão único.

1. No portal do Azure, selecione **aplicações empresariais** > **todas as aplicações** > **HubSpot**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

1. Na lista de aplicações, selecione **HubSpot**.

    ![HubSpot na lista de aplicações](common/all-applications.png)

1. No menu, selecione **utilizadores e grupos**.

    ![A opção de utilizadores e grupos](common/users-groups-blade.png)

1. Selecione **adicionar utilizador**. Em seguida, na **adicionar atribuição** painel, selecione **utilizadores e grupos**.

    ![O painel de atribuição de adicionar](common/add-assign-user.png)

1. Na **utilizadores e grupos** painel, selecione **Eduarda Almeida** na lista de utilizadores. Escolha **Selecionar**.

1. Se estiver à espera um valor de função na asserção de SAML, no **selecionar função** painel, selecione a função relevante para o utilizador a partir da lista. Escolha **Selecionar**.

1. Na **adicionar atribuição** painel, selecione **atribuir**.

### <a name="create-a-hubspot-test-user"></a>Criar um utilizador de teste HubSpot

Para ativar o Azure AD que um utilizador para iniciar sessão no HubSpot, o utilizador tem de ser aprovisionado no HubSpot. HubSpot, aprovisionamento é uma tarefa manual.

Para Aprovisionar uma conta de utilizador no HubSpot:

1. Inicie sessão no site da sua empresa HubSpot como administrador.

1. Selecione o **definições** ícone no canto superior direito da página.

    ![O ícone de definições no HubSpot](./media/hubspot-tutorial/config1.png)

1. Selecione **utilizadores e as Equipes**.

    ![A opção de utilizadores e as Equipes em HubSpot](./media/hubspot-tutorial/user1.png)

1. Selecione **criar utilizador**.

    ![A opção de utilizador de criar na HubSpot](./media/hubspot-tutorial/user2.png)

1. Na **adicionar e-mail addess(es)** , introduza o endereço de e-mail do utilizador no brittasimon formato\@contoso.com e, em seguida, selecione **seguinte**.

    ![Os endereços de e-mail de adicionar caixa na seção de utilizadores a criar no HubSpot](./media/hubspot-tutorial/user3.png)

1. Na **criar utilizadores** secção, selecione cada guia. Em cada separador, defina as opções relevantes e as permissões do utilizador. Em seguida, selecione **Seguinte**.

    ![Separadores na seção de utilizadores a criar no HubSpot](./media/hubspot-tutorial/user4.png)

1. Para enviar o convite ao utilizador, selecione **enviar**.

    ![A opção de envio no HubSpot](./media/hubspot-tutorial/user5.png)

    > [!NOTE]
    > O utilizador é ativado depois do utilizador aceitar o convite.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar seu única início de sessão em configuração do Azure AD com o portal as minhas aplicações.

Depois de configurar início de sessão único, quando seleciona **HubSpot** portal as minhas aplicações, é automaticamente tem sessão iniciada no HubSpot. Para obter mais informações sobre o portal as minhas aplicações, consulte [aplicações de acesso e a utilização no portal minhas aplicações](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações, consulte estes artigos:

- [Lista de tutoriais de integração de aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Início de sessão único para aplicações no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
