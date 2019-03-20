---
title: 'Tutorial: Aplicação LOB do departamento de ativada para a integração do Active Directory do Azure com o SAML 1.1 Token | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e SAML 1.1 Token de aplicação LOB para ativado.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: ced1d88d-0e48-40d5-9aea-ef991cd9d270
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/24/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2692d4109b5ca673b2acffe19eac1b141e5b8067
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57891455"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-11-token-enabled-lob-app"></a>Tutorial: Aplicação LOB do departamento de ativada para a integração do Active Directory do Azure com o SAML 1.1 Token

Neste tutorial, saiba como integrar SAML 1.1 Token LOB aplicação ativada com o Azure Active Directory (Azure AD).
Integração de SAML 1.1 Token LOB aplicação ativada com o Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Token do SAML 1.1 habilitado aplicação LOB.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para SAML 1.1 Token aplicação LOB do departamento (Single Sign-On) ativada com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar o Azure aplicação LOB para a integração do AD com o SAML 1.1 Token ativada, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* SAML 1.1 Token de aplicação LOB para início de sessão ativada subscrição individual ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* SAML 1.1 Token ativada suporta aplicações LOB **SP** iniciada SSO

## <a name="adding-saml-11-token-enabled-lob-app-from-the-gallery"></a>Adicionar Token do SAML 1.1 ativada uma aplicação LOB a partir da Galeria

Para configurar a integração de SAML 1.1 Token ativada aplicação LOB para o Azure AD, tem de adicionar que SAML 1.1 Token de aplicação LOB a partir da Galeria sua lista de aplicações de SaaS geridas ativada.

**Para adicionar o que token do SAML 1.1 habilitado aplicação LOB a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **SAML 1.1 Token de aplicação LOB ativada**, selecione **SAML 1.1 Token de aplicação LOB para ativado** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![SAML 1.1 Token aplicação LOB ativada na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único SAML 1.1 Token com ativado com base num utilizador de teste com o nome de aplicação LOB **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no SAML 1.1 Token ativada necessidades de aplicação LOB seja estabelecida.

Para configurar e testar o Azure AD início de sessão único com o SAML 1.1 Token ativado aplicação LOB, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar SAML 1.1 Token ativada LOB aplicação início de sessão único](#configure-saml-11-token-enabled-lob-app-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar SAML 1.1 Token ativada utilizador de teste de aplicação LOB](#create-saml-11-token-enabled-lob-app-test-user)**  - para ter um equivalente da Eduarda Almeida no SAML 1.1 Token ativada aplicação LOB que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com o SAML 1.1 Token ativado aplicação LOB, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **SAML 1.1 Token de aplicação LOB para ativado** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![SAML 1.1 Token ativada URLs de LOB do departamento de domínio de aplicativo e únicas início de sessão em informações](common/sp-identifier.png)

    a. Na **iniciar sessão no URL** caixa de texto, escreva um URL com o seguinte padrão: `https://your-app-url`

    b. Na **identificador (ID de entidade)** caixa de texto, escreva um URL com o seguinte padrão: `https://your-app-url`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o início de sessão real URL e o identificador. Token de 1.1 de SAML contacto ativada a equipa de suporte de cliente de aplicação LOB para obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

4. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

6. Sobre o **LOB aplicação ativada para configurar SAML 1.1 Token** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Azure Ad Identifier

    c. URL de fim de sessão

### <a name="configure-saml-11-token-enabled-lob-app-single-sign-on"></a>Configurar SAML 1.1 Token ativada LOB aplicação início de sessão único

Para configurar o início de sessão único num **SAML 1.1 Token de aplicação LOB ativada** lado, terá de enviar o transferido **certificado (Base64)** e LOB aplicação ativada para URLs copiados adequados partir do portal do Azure para SAML 1.1 Token equipa de suporte. Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** campo introduza **BrittaSimon**.
  
    b. Na **nome de utilizador** tipo de campo **brittasimon\@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar a Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso para SAML 1.1 Token de aplicação LOB para ativado.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **SAML 1.1 Token de aplicação LOB ativada**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, escreva e selecione **SAML 1.1 Token de aplicação LOB ativada**.

    ![O Token SAML 1.1 habilitado a ligação de aplicação LOB na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-saml-11-token-enabled-lob-app-test-user"></a>Criar SAML 1.1 Token ativada utilizador de teste de aplicação LOB

Nesta secção, vai criar um usuário chamado Eduarda Almeida no SAML 1.1 Token de aplicação LOB para ativado. Trabalhar com SAML 1.1 Token ativada a equipa de suporte de aplicação LOB adicionar que os utilizadores no Token SAML 1.1 ativados plataforma de aplicação LOB. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no Token SAML 1.1 mosaico de aplicação LOB no painel de acesso de ativado, deve ser automaticamente a sessão iniciada aplicação LOB para o qual configura o SSO de ativado o Token SAML 1.1. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

