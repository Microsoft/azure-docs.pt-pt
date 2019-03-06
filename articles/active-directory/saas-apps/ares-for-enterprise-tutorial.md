---
title: 'Tutorial: Integração do Active Directory do Azure com ARES para empresas | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e ARES para a empresa.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 002b941e-0345-43f8-a102-baab9bb1e1dd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 67e571d002b300729e6f208f8a7ee43b4edfe47c
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57406597"
---
# <a name="tutorial-azure-active-directory-integration-with-ares-for-enterprise"></a>Tutorial: Integração do Active Directory do Azure com ARES para a empresa

Neste tutorial, saiba como integrar ARES para a empresa com o Azure Active Directory (Azure AD).
Integrar ARES para Enterprise no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso para ARES para a empresa.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para ARES para empresas (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com ARES para empresa, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Subscrição de ativado ARES para Enterprise início de sessão único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta ARES para empresas **SP** iniciada SSO

* Suporta ARES para empresas **Just In Time** aprovisionamento de utilizadores

## <a name="adding-ares-for-enterprise-from-the-gallery"></a>Adicionando ARES para a empresa da Galeria

Para configurar a integração do ARES para a empresa com o Azure AD, terá de adicionar ARES para a empresa a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar ARES para a empresa a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **ARES para empresas**, selecione **ARES para Enterprise** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![ARES para a empresa na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com ARES para Enterprise com base num utilizador de teste **Eduarda Almeida**.
Para início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no ARES para a empresa tem de ser estabelecida.

Para configurar e testar o Azure AD início de sessão único com ARES para empresa, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar ARES para Enterprise Single Sign-On](#configure-ares-for-enterprise-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo. 
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar ARES para o utilizador de teste de Enterprise](#create-ares-for-enterprise-test-user)**  - para ter um equivalente da Eduarda Almeida na ARES para a empresa que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com ARES para empresa, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **ARES para Enterprise** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, executar o passo seguinte:

    ![ARES para domínio de empresa e URLs únicos início de sessão em informações](common/sp-intiated.png)

    Na **iniciar sessão no URL** caixa de texto, escreva um URL:  `https://login.graebert.com`

5. No **definir a segurança de início de sessão único com o SAML** na página a **certificado de assinatura SAML** secção, clique em botão Copiar para copiar **Url de metadados de Federação de aplicação** e guarde-o no seu computador.

    ![O link de download de certificado](common/copy-metadataurl.png)

### <a name="configure-ares-for-enterprise-single-sign-on"></a>Configurar ARES para Enterprise Single Sign-On

Para configurar o início de sessão único num **ARES para empresas** lado, terá de enviar o **Url de metadados de Federação de aplicação** para [ARES para a empresa a equipa de suporte](mailto:support@graebert.com). Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** campo introduza **BrittaSimon**.
  
    b. Na **nome de utilizador** tipo de campo **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para ARES para a empresa.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **ARES para a empresa**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **ARES para a empresa**.

    ![ARES para ligação de Enterprise na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-ares-for-enterprise-test-user"></a>Criar ARES para o utilizador de teste de Enterprise

Nesta secção, um usuário chamado Eduarda Almeida é criado na ARES para a empresa. Suporta ARES para empresas **just-in-time aprovisionamento**, que está ativado por predefinição. Não existe nenhum item de ação para nesta secção. Se um utilizador já não existir no ARES para empresa, uma nova é criada quando tentar acessar ARES para a empresa.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica ARES para mosaico Enterprise no painel de acesso, deve ser automaticamente sessão iniciada no ARES para a empresa para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

