---
title: 'Tutorial: Integração do Active Directory do Azure com monday.com | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e monday.com.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9e8ad807-0664-4e31-91de-731097c768e2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 08108a1718ee917a317b4864de81959ae139eaa5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67097018"
---
# <a name="tutorial-azure-active-directory-integration-with-mondaycom"></a>Tutorial: Integração do Active Directory do Azure com monday.com

Neste tutorial, saiba como integrar monday.com com o Azure Active Directory (Azure AD).

Integrar monday.com no Azure AD oferece as seguintes vantagens:

* Pode utilizar o Azure AD para controlar quem tem acesso ao monday.com.
* Os utilizadores podem ser automaticamente sessão iniciados no monday.com com as suas contas do Azure AD (início de sessão único).
* Pode gerir as suas contas num local central, o portal do Azure.

Para obter mais informações sobre o software como uma integração de aplicação de serviço (SaaS) com o Azure AD, consulte [início de sessão único para aplicações no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com monday.com, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver uma subscrição do Azure AD, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Uma subscrição de monday.com com início de sessão único ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste e integrar monday.com no Azure AD.

Monday.com suporta as seguintes funcionalidades:

* **Iniciado por SP início de sessão único**
* **IDP iniciada pelo início de sessão único**
* **Aprovisionamento de utilizadores de just-in-time**

## <a name="add-mondaycom-in-the-azure-portal"></a>Adicionar monday.com no portal do Azure

Para integrar monday.com com o Azure AD, tem de adicionar monday.com à sua lista de aplicações de SaaS geridas.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. No menu da esquerda, selecione **do Azure Active Directory**.

    ![A opção do Azure Active Directory](common/select-azuread.png)

1. Selecione **aplicações empresariais** > **todas as aplicações**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

1. Para adicionar uma aplicação, selecione **nova aplicação**.

    ![A nova opção de aplicação](common/add-new-app.png)

1. Na caixa de pesquisa, introduza **monday.com**. Nos resultados da pesquisa, selecione **monday.com**e, em seguida, selecione **Add**.

    ![Monday.com na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com monday.com com base num utilizador de teste com o nome **Eduarda Almeida**. Para início de sessão único funcione, tem de estabelecer uma relação ligada entre um utilizador do Azure AD e o utilizador relacionado monday.com.

Para configurar e testar o Azure AD início de sessão único com monday.com, tem de concluir os seguintes blocos de construção:

| Tarefa | Descrição |
| --- | --- |
| **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)** | Permite que os utilizadores utilizar esta funcionalidade. |
| **[Configurar monday.com início de sessão único](#configure-mondaycom-single-sign-on)** | Configura as definições de início de sessão únicas no aplicativo. |
| **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)** | Testes do Azure AD início de sessão único para um utilizador com o nome Eduarda Almeida. |
| **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)** | Permite a Eduarda Almeida utilizar o Azure AD início de sessão único. |
| **[Criar um utilizador de teste monday.com](#create-a-mondaycom-test-user)** | Cria um equivalente da Eduarda Almeida na monday.com que está ligado a representação do Azure AD do utilizador. |
| **[Testar início de sessão único](#test-single-sign-on)** | Verifica-se de que a configuração funciona. |

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, irá configurar o Azure AD início de sessão único com monday.com no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na **monday.com** painel de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a opção de início de sessão único](common/select-sso.png)

1. Na **selecionar um método de início de sessão único** painel, selecione **SAML** ou **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

1. Na **definir a segurança de início de sessão único com o SAML** painel, selecione **editar** (o ícone de lápis) para abrir o **configuração básica de SAML** painel.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

1. Na **configuração básica de SAML** painel, se tiver um ficheiro de metadados do fornecedor de serviço e que pretende configurar *iniciada pelo IDP modo*, conclua os seguintes passos:

    1. Selecione **carregamento de ficheiro de metadados**.

       ![A opção de ficheiro de metadados de carregamento](common/upload-metadata.png)

    1. Para selecionar o ficheiro de metadados, selecione o ícone de pasta e, em seguida, selecione **carregar**.

       ![Selecione o ficheiro de metadados e, em seguida, selecione o botão de carregamento](common/browse-upload-metadata.png)

    1. Depois do ficheiro de metadados é carregado com êxito, o **identificador** e **URL de resposta** valores são preenchidos automaticamente na **configuração básica de SAML** painel:

       ![Os valores de IDP no painel de configuração básica de SAML](common/idp-intiated.png)

       > [!Note]
       > Se o **identificador** e **URL de resposta** valores não são automaticamente preenchidos, introduza os valores manualmente.

1. Para configurar a aplicação no *iniciado por SP modo*:

    1. Selecione **definir URLs adicionais**.
    
    1. Na **iniciar sessão no URL** , introduza um URL com o seguinte padrão: https:\//\<seu-dominio >. monday.com. Contacte os [equipa de suporte de cliente monday.com](mailto:support@monday.com) para obter o URL de início de sessão.

        ![A opção de URLs adicional do conjunto](common/metadata-upload-additional-signon.png)

1. O aplicativo monday.com espera que as asserções SAML para estar num formato específico. Configure as seguintes declarações para esta aplicação. Para gerir estes valores de atributo na **definir a segurança de início de sessão único com o SAML** painel, selecione **editar** para abrir o **atributos de utilizador** painel.

    ![O painel de atributos de utilizador](common/edit-attribute.png)

1. Sob **afirmações de utilizador**, selecione **editar** para editar as afirmações. Para adicionar uma afirmação, selecione **Adicionar nova afirmação**e, em seguida, configure o atributo de token SAML, conforme mostrado na imagem anterior. Em seguida, conclua os seguintes passos: 

    1. Selecione **Adicionar nova afirmação**.

        ![Adicionar nova opção no painel de afirmações de utilizador de afirmação](common/new-save-attribute.png)

    1. Na **Gerir afirmações de utilizador** painel, defina os seguintes valores:
        
       1. Na **nome** , introduza o nome de atributo apresentado para a linha de afirmação de utilizador.

       1. Deixe **espaço de nomes** em branco.

       1. Para **origem**, selecione **atributo**.

       1. Na **atributo de origem** , selecione o valor do atributo apresentado para a linha de afirmação de utilizador.

       1. Selecione **OK**e, em seguida, selecione **guardar**.

       ![As afirmações de utilizador de gerir](common/new-attribute-details.png)

1. Na **definir a segurança de início de sessão único com o SAML** painel, em **certificado de assinatura SAML**, selecione **transferir** junto a **certificado (Base64)** . Selecione uma opção de download com base nos seus requisitos. Guarde o certificado no seu computador.

    ![A opção de download de certificado (Base64)](common/certificatebase64.png)

1. Na **configurar monday.com** secção, copie os seguintes URLs com base nos seus requisitos:

    * URL de início de sessão
    * Identificador do Azure AD
    * URL de fim de sessão

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-mondaycom-single-sign-on"></a>Configurar monday.com início de sessão único

Para configurar o início de sessão único no lado do monday.com, envie o ficheiro transferido do certificado (Base64) e os URLs relevantes que copiou do portal do Azure para o [equipa de suporte de monday.com](mailto:support@monday.com). A equipa de suporte de monday.com utiliza as informações de que enviá-las para se certificar de que a SAML única início de sessão na ligação está definida corretamente em ambos os lados.

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

Nesta secção, concede acesso de Eduarda Almeida a monday.com para que ela pode utilizar o Azure início de sessão único.

1. No portal do Azure, selecione **aplicações empresariais** > **todas as aplicações** > **monday.com**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

1. Na lista de aplicações, selecione **monday.com**.

    ![Monday.com na lista de aplicações](common/all-applications.png)

1. No menu, selecione **utilizadores e grupos**.

    ![A opção de utilizadores e grupos](common/users-groups-blade.png)

1. Selecione **adicionar utilizador**. Em seguida, na **adicionar atribuição** painel, selecione **utilizadores e grupos**.

    ![O painel de atribuição de adicionar](common/add-assign-user.png)

1. Na **utilizadores e grupos** painel, selecione **Eduarda Almeida** na lista de utilizadores. Escolha **Selecionar**.

1. Se estiver à espera um valor de função na asserção de SAML, no **selecionar função** painel, selecione a função relevante para o utilizador a partir da lista. Escolha **Selecionar**.

1. Na **adicionar atribuição** painel, selecione **atribuir**.

### <a name="create-a-mondaycom-test-user"></a>Criar um utilizador de teste monday.com

Nesta secção, um usuário chamado Eduarda Almeida é criado no aplicativo monday.com. Monday.com suporta o aprovisionamento de utilizadores de just-in-time, que está ativado por predefinição. Não existe nenhum item de ação para nesta secção. Se um utilizador já não existir no monday.com, é criado um novo após a autenticação.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar seu única início de sessão em configuração do Azure AD com o portal as minhas aplicações.

Depois de configurar início de sessão único, quando seleciona **monday.com** portal as minhas aplicações, é automaticamente tem sessão iniciada no monday.com. Para obter mais informações sobre o portal as minhas aplicações, consulte [aplicações de acesso e a utilização no portal minhas aplicações](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações, consulte estes artigos:

- [Lista de tutoriais de integração de aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Início de sessão único para aplicações no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
