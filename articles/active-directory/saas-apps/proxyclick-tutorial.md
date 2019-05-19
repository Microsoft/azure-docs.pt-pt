---
title: 'Tutorial: Integração do Active Directory do Azure com Proxyclick | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Proxyclick.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5c58a859-71c2-4542-ae92-e5f16a8e7f18
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: b797c7d49495aac90abb31a9214bf2928784a866
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "65868665"
---
# <a name="tutorial-azure-active-directory-integration-with-proxyclick"></a>Tutorial: Integração do Active Directory do Azure com Proxyclick

Neste tutorial, saiba como integrar Proxyclick com o Azure Active Directory (Azure AD).
Integrar Proxyclick no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Proxyclick.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para Proxyclick (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Proxyclick, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Proxyclick logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta Proxyclick **SP** e **IDP** iniciada SSO

## <a name="adding-proxyclick-from-the-gallery"></a>Adicionando Proxyclick da Galeria

Para configurar a integração do Proxyclick com o Azure AD, terá de adicionar Proxyclick a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Proxyclick a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Proxyclick**, selecione **Proxyclick** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Proxyclick na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Proxyclick com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Proxyclick deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Proxyclick, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Proxyclick Single Sign-On](#configure-proxyclick-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste Proxyclick](#create-proxyclick-test-user)**  - para ter um equivalente da Eduarda Almeida na Proxyclick que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com Proxyclick, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Proxyclick** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, se desejar configurar a aplicação no **IDP** iniciada pelo modo, execute os seguintes passos:

    ![Proxyclick domínio e URLs únicas início de sessão em informações](common/idp-intiated.png)

    a. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://saml.proxyclick.com/init/<companyId>`

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://saml.proxyclick.com/consume/<companyId>`

5. Clique em **definir URLs adicionais** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    ![Proxyclick domínio e URLs únicas início de sessão em informações](common/metadata-upload-additional-signon.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão:  `https://saml.proxyclick.com/init/<companyId>`

    > [!NOTE]
    > Estes valores não são reais. Atualizar estes valores com o identificador de real, a URL de resposta e o início de sessão no URL, que é explicado mais tarde no tutorial.

6. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

7. Sobre o **configurar Proxyclick** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-proxyclick-single-sign-on"></a>Configurar Proxyclick Single Sign-On

1. Numa janela do browser web diferente, inicie sessão no site da sua empresa Proxyclick como um administrador.

2. Selecione **conta de & definições**.

    ![Configuração de Proxyclick](./media/proxyclick-tutorial/configure1.png)

3. Desloque para baixo para o **INTEGRAÇÕES** e selecione **SAML**.

    ![Configuração de Proxyclick](./media/proxyclick-tutorial/configure2.png)

4. Na **SAML** secção, execute os seguintes passos:

    ![Configuração de Proxyclick](./media/proxyclick-tutorial/configure3.png)

    a. Cópia **URL de consumidor de SAML** valor e cole-a na **URL de resposta** caixa de texto no **configuração básica de SAML** secção no portal do Azure.

    b. Cópia **URL de redirecionamento de SSO SAML** valor e cole-a na **iniciar sessão no URL** e **identificador** caixas de texto no **configuração básica de SAML** secção no portal do Azure.

    c. Selecione **método de pedido SAML** como **redirecionamento de HTTP**.

    d. Na **emissor** caixa de texto, cole o valor de **do Azure AD identificador** valor, que copiou do portal do Azure.

    e. Na **URL de ponto final do SAML 2.0** caixa de texto, cole o valor de **URL de início de sessão** copiados a partir do portal do Azure.

    f. Abra o ficheiro de certificado transferido a partir do portal do Azure no bloco de notas e, em seguida, cole-o para o **certificado** caixa de texto.

    g. Clique em **guardar alterações**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** campo introduza **BrittaSimon**.
  
    b. Na **nome de utilizador** tipo de campo brittasimon@yourcompanydomain.extension. Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Proxyclick.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Proxyclick**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Proxyclick**.

    ![A ligação de Proxyclick na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-proxyclick-test-user"></a>Criar utilizador de teste Proxyclick

Para ativar a utilizadores do Azure AD iniciar sessão no Proxyclick, tem de ser aprovisionados em Proxyclick. No caso de Proxyclick, o aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no site da sua empresa Proxyclick como um administrador.

1. Clique em **colegas** da barra de navegação superior.

    ![Adicionar o funcionário](./media/proxyclick-tutorial/user1.png)

1. Clique em **adicionar colega**

    ![Adicionar o funcionário](./media/proxyclick-tutorial/user2.png)

1. Na **adicionar um colega** secção, execute os seguintes passos:

    ![Adicionar o funcionário](./media/proxyclick-tutorial/user3.png)

    a. Na **E-Mail** caixa de texto, como o tipo de endereço de e-mail do utilizador brittasimon@contoso.com.

    b. Na **nome próprio** caixa de texto, tipo, o primeiro nome de utilizador, como a Eduarda.

    c. Na **Apelido** caixa de texto, digite o apelido do utilizador, como Simon.

    d. Clique em **adicionar utilizador**.

### <a name="test-single-sign-on"></a>Testar início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Proxyclick no painel de acesso, deve ser automaticamente sessão iniciada no Proxyclick para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

