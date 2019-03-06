---
title: 'Tutorial: Integração do Active Directory do Azure com o New Relic | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o New Relic.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3186b9a8-f4d8-45e2-ad82-6275f95e7aa6
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/04/2019
ms.author: jeedes
ms.openlocfilehash: 5b7ba564c0113f6432812b4381c7ad210978a7d5
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57404863"
---
# <a name="tutorial-azure-active-directory-integration-with-new-relic"></a>Tutorial: Integração do Active Directory do Azure com o New Relic

Neste tutorial, saiba como integrar o New Relic com o Azure Active Directory (Azure AD).
Integração de New Relic com o Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao New Relic.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para o New Relic (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o New Relic, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Novo Relic logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta o New Relic **SP** iniciada SSO

## <a name="adding-new-relic-from-the-gallery"></a>Adicionando o New Relic da Galeria

Para configurar a integração de New Relic para o Azure AD, terá de adicionar o New Relic na Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar o New Relic a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **New Relic**, selecione **New Relic** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![O New Relic na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com o New Relic com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado em New Relic deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com o New Relic, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar novo Relic início de sessão único](#configure-new-relic-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste de New Relic](#create-new-relic-test-user)**  - para ter um equivalente da Eduarda Almeida em New Relic que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com o New Relic, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **New Relic** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![Novo Relic domínio e URLs único informações de início de sessão](common/sp-identifier.png)

    a. Na **iniciar sessão no URL** caixa de texto, escreva um URL com o seguinte padrão: `https://rpm.newrelic.com/accounts/{acc_id}/sso/saml/login` -não se esqueça de substituir o seu ID da conta de New Relic.

    b. Na **identificador (ID de entidade)** caixa de texto, escreva um URL: `rpm.newrelic.com`

5. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

6. Sobre o **configurar o New Relic** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Azure Ad Identifier

    c. URL de fim de sessão

### <a name="configure-new-relic-single-sign-on"></a>Configurar novo Relic início de sessão único

1. Numa janela do browser web diferente, inicie sessão no seu **New Relic** site da empresa como administrador.

2. No menu na parte superior, clique em **definições de conta**.
   
    ![Definições da conta](./media/new-relic-tutorial/ic797036.png "definições da conta")

3. Clique nas **segurança e autenticação** separador e, em seguida, clique nas **início de sessão único** separador.
   
    ![Início de sessão único](./media/new-relic-tutorial/ic797037.png "início de sessão único")

4. Na página de diálogo de SAML, execute os seguintes passos:
   
    ![SAML](./media/new-relic-tutorial/ic797038.png "SAML")
   
    a. Clique em **Escolher ficheiro** para carregar o certificado transferido do Azure Active Directory.

    b. Na **URL de início de sessão remoto** caixa de texto, cole o valor de **URL de início de sessão**, que copiou do portal do Azure.
   
    c. Na **URL de destino de fim de sessão** caixa de texto, cole o valor de **URL de fim de sessão**, que copiou do portal do Azure.

    d. Clique em **salvar minhas alterações**.

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso a New Relic.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **New Relic**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **New Relic**.

    ![A ligação de New Relic na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-new-relic-test-user"></a>Criar utilizador de teste de New Relic

Para que os utilizadores do Azure Active Directory iniciar sessão no New Relic, tem de ser aprovisionados em New Relic. No caso de New Relic, o aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador para o New Relic, execute os seguintes passos:**

1. Inicie sessão no seu **New Relic** site da empresa como administrador.

2. No menu na parte superior, clique em **definições de conta**.
   
    ![Definições da conta](./media/new-relic-tutorial/ic797040.png "definições da conta")

3. Na **conta** painel no lado esquerdo, clique em **resumo**e, em seguida, clique em **adicionar utilizador**.
   
    ![Definições da conta](./media/new-relic-tutorial/ic797041.png "definições da conta")

4. Sobre o **utilizadores ativos** caixa de diálogo, execute os seguintes passos:
   
    ![Utilizadores ativos](./media/new-relic-tutorial/ic797042.png "utilizadores ativos")
   
    a. Na **E-Mail** caixa de texto, escreva o endereço de e-mail de um utilizador válido do Azure Active Directory que pretende aprovisionar.

    b. Como **função** selecionar **utilizador**.

    c. Clique em **adicionar este utilizador**.

>[!NOTE]
>Pode utilizar quaisquer outras New Relic utilizador conta criação ferramentas ou APIs fornecidas pelo New Relic para aprovisionar contas de utilizador do AAD.
> 

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico de New Relic no painel de acesso, deve ser automaticamente sessão iniciada no New Relic para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [ Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

