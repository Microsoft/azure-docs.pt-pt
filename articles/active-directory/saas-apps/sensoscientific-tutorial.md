---
title: 'Tutorial: Integração do Active Directory do Azure com o sistema de monitorização do SensoScientific sem fios temperatura | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o sistema de monitorização do SensoScientific sem fios temperatura.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ee9a924d-ccde-45b0-ab40-877f82f5dfa2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea2f5e33859852388357526052c39fa432471efb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67091274"
---
# <a name="tutorial-azure-active-directory-integration-with-sensoscientific-wireless-temperature-monitoring-system"></a>Tutorial: Integração do Active Directory do Azure com o sistema de monitorização do SensoScientific sem fios temperatura

Neste tutorial, saiba como integrar o sistema de monitorização do SensoScientific sem fios temperatura com o Azure Active Directory (Azure AD).
Integração de sistema de monitorização do SensoScientific sem fios temperatura com o Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao sistema de monitorização do SensoScientific sem fios temperatura.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para o sistema de monitorização do SensoScientific sem fios temperatura (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o sistema de monitorização do SensoScientific sem fios temperatura, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter um [conta gratuita](https://azure.microsoft.com/free/)
* Sistema de monitorização do SensoScientific sem fios temperatura logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta o sistema de monitorização do SensoScientific sem fios temperatura **IDP** iniciada SSO

## <a name="adding-sensoscientific-wireless-temperature-monitoring-system-from-the-gallery"></a>Adicionando o sistema de monitorização do SensoScientific sem fios temperatura da Galeria

Para configurar a integração do sistema de monitorização do SensoScientific sem fios temperatura para o Azure AD, terá de adicionar o sistema de monitorização do SensoScientific sem fios temperatura partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar o sistema de monitorização do SensoScientific sem fios temperatura da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **sistema de monitorização do SensoScientific sem fios temperatura**, selecione **sistema de monitorização do SensoScientific sem fios temperatura** no painel de resultados, em seguida, clique em **adicionar**  botão para adicionar a aplicação.

    ![Sistema de monitorização do SensoScientific sem fios temperatura na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com SensoScientific sem fios temperatura sistema de monitorização com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no sistema de monitorização do SensoScientific sem fios temperatura deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com o sistema de monitorização do SensoScientific sem fios temperatura, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar SensoScientific sem fios temperatura monitorização do sistema de início de sessão único](#configure-sensoscientific-wireless-temperature-monitoring-system-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste de sistema de monitorização do SensoScientific sem fios temperatura](#create-sensoscientific-wireless-temperature-monitoring-system-test-user)**  - para ter um equivalente da Eduarda Almeida na SensoScientific sem fios temperatura sistema de monitorização que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com o sistema de monitorização do SensoScientific sem fios temperatura, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **sistema de monitorização do SensoScientific sem fios temperatura** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, o utilizador não tem de realizar qualquer passo como a aplicação já está pré-integrada com o Azure.

    ![Domínio de sistema de monitorização SensoScientific sem fios temperatura e URLs únicas início de sessão em informações](common/preintegrated.png)

5. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

6. Sobre o **configurar o sistema de monitorização do SensoScientific sem fios temperatura** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-sensoscientific-wireless-temperature-monitoring-system-single-sign-on"></a>Configurar a temperatura sem fios de SensoScientific monitorização sistema início de sessão único

1. Inicie sessão sua aplicação de sistema de monitorização do SensoScientific sem fios temperatura como administrador.

1. No menu de navegação na parte superior, clique em **Configuration** e goto **configurar** sob **início de sessão único** para abrir o início de sessão único em definições e execute os seguintes passos:

    ![Configurar o início de sessão único](./media/sensoscientific-tutorial/tutorial_sensoscientificwtms_admin.png)

    a. Selecione **nome do emissor** como do Azure AD.

    b. Na **URL de emissor** caixa de texto, colar a **do Azure AD identificador** que copiou do portal do Azure.

    c. Na **único URL de início de sessão no serviço** caixa de texto, colar a **URL de início de sessão** que copiou do portal do Azure.

    d. Na **URL de serviço de fim de sessão único** caixa de texto, colar a **URL de fim de sessão** que copiou do portal do Azure.

    e. Procure o certificado que tenha transferido a partir do portal do Azure e carregue aqui.

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
  
    b. Na **nome de utilizador** tipo de campo `brittasimon@yourcompanydomain.extension`. Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso ao sistema de monitorização do SensoScientific sem fios temperatura.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **sistema de monitorização do SensoScientific sem fios temperatura**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **sistema de monitorização do SensoScientific sem fios temperatura**.

    ![A ligação do sistema de monitorização do SensoScientific sem fios temperatura na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-sensoscientific-wireless-temperature-monitoring-system-test-user"></a>Criar utilizador de teste de sistema de monitorização do SensoScientific sem fios temperatura

Para ativar a utilizadores do Azure AD iniciar sessão no sistema de monitorização do SensoScientific sem fios temperatura, tem de ser aprovisionados no sistema de monitorização do SensoScientific sem fios temperatura. Trabalhar com [equipa de suporte do sistema de monitorização do SensoScientific sem fios temperatura](https://www.sensoscientific.com/contact-us/) para adicionar os utilizadores na plataforma do sistema de monitorização do SensoScientific sem fios temperatura. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no mosaico de sistema de monitorização do SensoScientific sem fios temperatura no painel de acesso, deve ser automaticamente conectado ao SensoScientific sem fios temperatura monitorização do sistema para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

