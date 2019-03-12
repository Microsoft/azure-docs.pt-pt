---
title: 'Tutorial: Integração do Active Directory do Azure com Clarizen | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Clarizen.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/21/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fca7b474531ff224ddcd089cd1ac8f40810dfb8a
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2019
ms.locfileid: "57732224"
---
# <a name="tutorial-azure-active-directory-integration-with-clarizen"></a>Tutorial: Integração do Active Directory do Azure com Clarizen

Neste tutorial, saiba como integrar Clarizen com o Azure Active Directory (Azure AD).
Integrar Clarizen no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Clarizen.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para Clarizen (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Clarizen, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Clarizen logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta Clarizen **IDP** iniciada SSO

## <a name="adding-clarizen-from-the-gallery"></a>Adicionando Clarizen da Galeria

Para configurar a integração do Clarizen com o Azure AD, terá de adicionar Clarizen a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Clarizen a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Clarizen**, selecione **Clarizen** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Clarizen na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Clarizen com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Clarizen deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Clarizen, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Clarizen Single Sign-On](#configure-clarizen-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste Clarizen](#create-clarizen-test-user)**  - para ter um equivalente da Eduarda Almeida na Clarizen que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com Clarizen, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Clarizen** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **definir a segurança de início de sessão único com o SAML** página, execute os seguintes passos:

    ![Clarizen domínio e URLs únicas início de sessão em informações](common/idp-intiated.png)

    a. Na **identificador** caixa de texto, escreva um valor: `Clarizen`

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://.clarizen.com/Clarizen/Pages/Integrations/SAML/SamlResponse.aspx`

    > [!NOTE]
    > Essas não são os valores reais. Tem de utilizar o identificador real e URL de resposta. Aqui sugerimos que utilize o valor exclusivo de uma cadeia de caracteres como o identificador. Para obter os valores reais, entre em contato com o [equipa de suporte de Clarizen](https://success.clarizen.com/hc/en-us/requests/new).

4. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

6. Sobre o **configurar Clarizen** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Azure Ad Identifier

    c. URL de fim de sessão

### <a name="configure-clarizen-single-sign-on"></a>Configurar Clarizen Single Sign-On

1. Numa janela do browser web diferente, inicie sessão no site da sua empresa Clarizen como um administrador.

1. Clique em seu nome de utilizador e, em seguida, clique em **definições**.

    ![Clicar em "Definições" em seu nome de utilizador](./media/clarizen-tutorial/tutorial_clarizen_001.png "definições")

1. Clique nas **definições globais** separador. Em seguida, junto a **autenticação federada**, clique em **editar**.

    ![Separador "Definições globais"](./media/clarizen-tutorial/tutorial_clarizen_002.png "definições globais")

1. Na **autenticação federada** diálogo caixa, execute os seguintes passos:

    !["Autenticação federada" caixa de diálogo](./media/clarizen-tutorial/tutorial_clarizen_003.png "autenticação federada")

    a. Selecione **Enable federado autenticação**.

    b. Clique em **carregar** para carregar o certificado transferido.

    c. Na **URL de início de sessão** , introduza o valor de **URL de início de sessão** partir da janela de configuração de aplicação do Azure AD.

    d. Na **URL de fim de sessão** , introduza o valor de **URL de fim de sessão** partir da janela de configuração de aplicação do Azure AD.

    e. Selecione **utilize POST**.

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
  
    b. Na **nome de utilizador** tipo de campo **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Clarizen.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Clarizen**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Clarizen**.

    ![A ligação de Clarizen na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-clarizen-test-user"></a>Criar utilizador de teste Clarizen

O objetivo desta secção é criar um usuário chamado Eduarda Almeida no Clarizen.

**Se precisar de criar manualmente o utilizador, execute, os seguintes passos:**

Para ativar a utilizadores do Azure AD iniciar sessão no Clarizen, terá de aprovisionar contas de utilizador. No caso de Clarizen, o aprovisionamento é uma tarefa manual.

1. Inicie sessão no site da sua empresa Clarizen como um administrador.

2. Clique em **pessoas**.

    ![Clicar em "Quem"](./media/clarizen-tutorial/create_aaduser_001.png "pessoas")

3. Clique em **convidar utilizador**.

    ![Botão "Convidar o utilizador"](./media/clarizen-tutorial/create_aaduser_002.png "convidar utilizadores")

1. Na **convidar pessoas** diálogo caixa, execute os seguintes passos:

    !["Convidar pessoas" caixa de diálogo](./media/clarizen-tutorial/create_aaduser_003.png "convidar pessoas")

    a. Na **E-Mail** , escreva o endereço de e-mail da conta Eduarda Almeida.

    b. Clique em **convidar**.

    > [!NOTE]
    > O titular da conta do Azure Active Directory irá receber um e-mail e seguir uma ligação para confirmar a respetiva conta até se tornar Active Directory.


### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Clarizen no painel de acesso, deve ser automaticamente sessão iniciada no Clarizen para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
