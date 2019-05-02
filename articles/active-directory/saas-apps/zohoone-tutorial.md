---
title: 'Tutorial: Integração do Active Directory do Azure com um de Zoho | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Zoho um.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: bbc3038c-0d8b-45dd-9645-368bd3d01a0f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.openlocfilehash: c731919baf3acc8cedfb31c088f9a0a12791251c
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64717987"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho-one"></a>Tutorial: Integração do Active Directory do Azure com Zoho um

Neste tutorial, saiba como integrar Zoho um com o Azure Active Directory (Azure AD).
Integrar Zoho um com o Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Zoho um.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada uma Zoho (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Zoho um, precisa do seguinte:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter um [conta gratuita](https://azure.microsoft.com/free/)
* Zoho um início de sessão único de subscrição ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta um de Zoho **SP** e **IDP** iniciada SSO

## <a name="adding-zoho-one-from-the-gallery"></a>Adicionando Zoho um da Galeria

Para configurar a integração do Zoho um com o Azure AD, terá de adicionar Zoho uma galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Zoho um partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Zoho um**, selecione **Zoho uma** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Zoho um na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com um Zoho com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Zoho um deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Zoho um, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Zoho um início de sessão único](#configure-zoho-one-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar Zoho um utilizador de teste](#create-zoho-one-test-user)**  - para ter um equivalente da Eduarda Almeida na Zoho um que esteja ligado a representação do Azure AD do utilizador.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com Zoho um, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Zoho uma** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, se desejar configurar a aplicação no **IDP** iniciada pelo modo, execute os seguintes passos:

    ![Zoho um domínio e URLs únicas início de sessão em informações](common/idp-relay.png)

    a. Na **identificador** caixa de texto, escreva um URL: `one.zoho.com`

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://accounts.zoho.com/samlresponse/<saml-identifier>`

    > [!NOTE]
    > Precedente **URL de resposta** valor não é real. Obterá o `<saml-identifier>` valor de n. º step4 de **configurar Zoho um início de sessão único** seção, que é explicada mais tarde no tutorial.

    c. Clique em **definir URLs adicionais**.

    d. Na **estado de reencaminhamento** caixa de texto, escreva um URL: `https://one.zoho.com`

5. Se desejar configurar a aplicação no **SP** iniciada pelo modo, executar o passo seguinte:


    ![Zoho um domínio e URLs únicas início de sessão em informações](common/both-signonurl.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão:  `https://accounts.zoho.com/samlauthrequest/<domain_name>?serviceurl=https://one.zoho.com` 

    > [!NOTE] 
    > Precedente **URL de início de sessão** valor não é real. Atualizar o valor com o URL real início de sessão do **configurar Zoho um início de sessão único** seção, que é explicada mais tarde no tutorial. 

6. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

7. Sobre o **configurar Zoho um** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-zoho-one-single-sign-on"></a>Configurar Zoho um início de sessão único

1. Numa janela do browser web diferente, inicie sessão no site da sua empresa Zoho um como um administrador.

2. Sobre o **organização** separador, clique em **configuração** sob **autenticação SAML**.

    ![Zoho One org](./media/zohoone-tutorial/tutorial_zohoone_setup.png)

3. Na página do pop-up, execute os seguintes passos:

    ![Sig Zoho um](./media/zohoone-tutorial/tutorial_zohoone_save.png)

    a. Na **URL de início de sessão** caixa de texto, cole o valor de **URL de início de sessão**, que copiou do portal do Azure.

    b. Na **URL de fim de sessão** caixa de texto, cole o valor de **URL de fim de sessão**, que copiou do portal do Azure.

    c. Clique em **navegue** para carregar o **certificado (Base64)** que transferiu a partir do portal do Azure.

    d. Clique em **Guardar**.

4. Depois de guardar a configuração de autenticação SAML, copie os **identificador SAML** valor e anexe-o com o **URL de resposta** em vez de `<saml-identifier>`, como `https://accounts.zoho.com/samlresponse/one.zoho.com` e cole o valor gerado no **URL de resposta** caixa de texto em **configuração básica de SAML** secção.

    ![Zoho um saml](./media/zohoone-tutorial/tutorial_zohoone_samlidenti.png)

5. Vá para o **domínios** separador e, em seguida, clique em **Adicionar domínio**.

    ![Zoho One domain](./media/zohoone-tutorial/tutorial_zohoone_domain.png)

6. Sobre o **Adicionar domínio** página, execute os seguintes passos:

    ![Zoho um Adicionar domínio](./media/zohoone-tutorial/tutorial_zohoone_adddomain.png)

    a. Na **nome de domínio** caixa de texto, domínio de tipo, como contoso.com.

    b. Clique em **Adicionar**.

    >[!Note]
    >Depois de adicionar a siga de domínio [estes](https://www.zoho.com/one/help/admin-guide/domain-verification.html) passos para verificar o seu domínio. Assim que o domínio é verificado, utilizar o seu nome de domínio no **URL de início de sessão** na **configuração básica de SAML** secção no portal do Azure.

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso Zoho uma.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Zoho um**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Zoho um**.

    ![A ligação Zoho um na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-zoho-one-test-user"></a>Criar Zoho um utilizador de teste

Para ativar a utilizadores do Azure AD iniciar sessão no Zoho um, tem de ser aprovisionados em Zoho um. Em um Zoho, o aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no Zoho um como um administrador de segurança.

2. Sobre o **os utilizadores** separador, clique em **logótipo de utilizador**.

    ![Zoho One user](./media/zohoone-tutorial/tutorial_zohoone_users.png)

3. Sobre o **adicionar utilizador** página, execute os seguintes passos:

    ![Zoho um adicionar utilizador](./media/zohoone-tutorial/tutorial_zohoone_adduser.png)
    
    a. Na **Name** texto, introduza o nome de utilizador, como **Eduarda Almeida**.
    
    b. Na **endereço de E-Mail** texto, introduza o e-mail do utilizador, como brittasimon@contoso.com.

    >[!Note]
    >Selecione o domínio verificado na lista de domínio.

    c. Clique em **Adicionar**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no mosaico Zoho uma no painel de acesso, deve ser automaticamente conectado a uma Zoho para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

