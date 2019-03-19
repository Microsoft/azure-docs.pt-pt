---
title: 'Tutorial: Integração do Active Directory do Azure com ajudar Scout | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e ajudar a Scout.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 0aad9910-0bc1-4394-9f73-267cf39973ab
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3253c26f99c470a256b937844138d8875219cbd1
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57878045"
---
# <a name="tutorial-azure-active-directory-integration-with-help-scout"></a>Tutorial: Integração do Active Directory do Azure com ajudar Scout

Neste tutorial, saiba como integrar Scout ajudar com o Azure Active Directory (Azure AD).
Integração do Scout ajudar com o Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso para ajudar a Scout.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para ajudar a Scout (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com ajudar Scout, precisa do seguinte:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Ajuda do Scout logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Ajuda do Scout suporta **SP e IDP** iniciada SSO
* Ajuda do Scout suporta **Just In Time** aprovisionamento de utilizadores

## <a name="adding-help-scout-from-the-gallery"></a>Adicionar Scout ajudar a partir da Galeria

Para configurar a integração do Scout ajudar com o Azure AD, terá de adicionar Scout ajudar a partir da galeria, à sua lista de aplicações de SaaS geridas.

**Para adicionar Scout ajudar a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **ajudar Scout**, selecione **Scout ajudar** no painel de resultados, em seguida, clique em **adicionar** botão para adicionar a aplicação.

     ![Scout de ajuda na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com ajudar Scout, com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado em ajudar a Scout deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Scout ajudar, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar ajudar Scout início de sessão único](#configure-help-scout-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste de ajudar a Scout](#create-help-scout-test-user)**  - para ter um equivalente da Eduarda Almeida na ajuda Scout que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com Scout ajudar a, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Scout ajudar** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, se desejar configurar a aplicação no **IDP** iniciada pelo modo, execute os seguintes passos:

    ![Ajudar a Scout domínio e URLs únicas início de sessão em informações](common/idp-intiated.png)

    a. **Identificador** é o **URI de audiência (ID de entidade do fornecedor de serviço)** do Scout ajudar, começa com `urn:`

    b. **URL de resposta** é o **pós-back URL (URL do serviço de consumidor de asserção)** do Scout ajudar, começa com `https://` 

    > [!NOTE]
    > Os valores estes URLs são para apenas demonstração. Tem de atualizar estes valores a partir do URL de resposta e o identificador real. Obtém estes valores a partir da **Single Sign-On** separador na seção de autenticação, o que é explicada mais tarde no tutorial.

5. Clique em **definir URLs adicionais** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    ![Ajudar a Scout domínio e URLs únicas início de sessão em informações](common/metadata-upload-additional-signon.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL como: `https://secure.helpscout.net/members/login/`

6. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

7. Sobre o **configurar Scout ajudar** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Azure Ad Identifier

    c. URL de fim de sessão

### <a name="configure-help-scout-single-sign-on"></a>Configurar ajuda Scout início de sessão único

1. Numa janela do browser web diferente, inicie sessão no site da sua empresa ajudar Scout como administrador.

2. Clique em **Manage** no menu superior e, em seguida, selecione **empresa** no menu pendente.

    ![Configurar o início de sessão único](./media/helpscout-tutorial/settings1.png)

3. Selecione **autenticação** no painel de navegação esquerdo.

    ![Configurar o início de sessão único](./media/helpscout-tutorial/settings2.png)

4. Isto leva-o para a secção de definições de SAML e execute os seguintes passos:

    ![Configurar o início de sessão único](./media/helpscout-tutorial/settings3.png)

    a. Cópia a **pós-back URL (URL do serviço de consumidor de asserção)** valor e cole o valor no **URL de resposta** caixa de texto **configuração básica de SAML** secção no Azure Portal.

    b. Cópia a **URI de audiência (ID de entidade do fornecedor de serviço)** valor e cole o valor no **identificador** caixa de texto **configuração básica de SAML** secção no portal do Azure.

5. Botão de alternar **ativar SAML** no e execute os seguintes passos:

    ![Configurar o início de sessão único](./media/helpscout-tutorial/settings4.png)

    a. Na **URL de início de sessão único** caixa de texto, cole o valor de **URL de início de sessão**, que copiou do portal do Azure.

    b. Clique em **carregar certificado** para carregar o **Certificate(Base64)** transferido a partir do portal do Azure.

    c. Introduza a e-mail domínios e.x. - sua organização `contoso.com` no **domínios de E-Mail** caixa de texto. É possível separar vários domínios com uma vírgula. Em qualquer altura um utilizador de Scout ajudar ou administrador que insere esse domínio específico no [Scout ajudam a iniciar sessão na página](https://secure.helpscout.net/members/login/) serão encaminhados para o fornecedor de identidade para autenticar com as respetivas credenciais.

    d. Por último, pode alternar **início de sessão SAML força em** se pretende que os utilizadores apenas iniciar sessão para ajudar a Scout através de através deste método. Se ainda quiser deixar a opção para os mesmos iniciar sessão com as credenciais do Scout ajudar, pode deixá-lo ativado/desativado desativado. Mesmo se esta opção estiver ativada, um proprietário da conta sempre será capaz de iniciar sessão para ajudar a Scout, com a palavra-passe de conta.

    e. Clique em **Guardar**.

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso para ajudar a Scout.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **ajudar Scout**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **ajudar Scout**.

    ![A ligação de ajuda Scout na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-help-scout-test-user"></a>Criar utilizador de teste de ajudar a Scout

Nesta secção, um usuário chamado Eduarda Almeida é criado na ajuda Scout. Ajuda do Scout suporta o aprovisionamento de utilizadores de just-in-time, que está ativado por predefinição. Não existe nenhum item de ação para nesta secção. Se um utilizador já não existir no Scout ajudar, é criado um novo após a autenticação.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico do Scout ajudar no painel de acesso, deve ser automaticamente sessão iniciada do Scout ajuda para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)