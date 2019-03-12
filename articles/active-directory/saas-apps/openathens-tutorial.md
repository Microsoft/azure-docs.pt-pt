---
title: 'Tutorial: Integração do Active Directory do Azure com OpenAthens | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e OpenAthens.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: dd4adfc7-e238-41d5-8b25-1811f08078b6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/4/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 61ee9b7f6155396d520ca4e093db02812cd488ee
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57780342"
---
# <a name="tutorial-azure-active-directory-integration-with-openathens"></a>Tutorial: Integração do Active Directory do Azure com OpenAthens

Neste tutorial, saiba como integrar OpenAthens com o Azure Active Directory (Azure AD).
Integrar OpenAthens no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao OpenAthens.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para OpenAthens (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com OpenAthens, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* OpenAthens único início de sessão na subscrição ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta OpenAthens **IDP** iniciada SSO

* Suporta OpenAthens **Just In Time** aprovisionamento de utilizadores

## <a name="adding-openathens-from-the-gallery"></a>Adicionando OpenAthens da Galeria

Para configurar a integração do OpenAthens com o Azure AD, terá de adicionar OpenAthens a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar OpenAthens a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **OpenAthens**, selecione **OpenAthens** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![OpenAthens na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com OpenAthens com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no OpenAthens deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com OpenAthens, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar OpenAthens Single Sign-On](#configure-openathens-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste OpenAthens](#create-openathens-test-user)**  - para ter um equivalente da Eduarda Almeida na OpenAthens que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com OpenAthens, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **OpenAthens** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

5. Sobre o **configuração básica de SAML** secção, carregue o **ficheiro de metadados do fornecedor de serviços**, os passos para os quais são mencionados mais tarde neste tutorial.

    a. Clique em **carregamento de ficheiro de metadados**.

    ![openathens carregar metadados](common/upload-metadata.png)

    b. Clique em **logótipo da pasta** para selecionar o ficheiro de metadados e clique em **carregar**.

    ![Openathens procurar metadados de carregamento](common/browse-upload-metadata.png)

    c. Depois do ficheiro de metadados é carregado com êxito, o **identificador** automática de get do valor preenchidas nos **configuração básica de SAML** secção caixa de texto:

    ![OpenAthens domínio e URLs únicas início de sessão em informações](common/idp-identifier.png)

6. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **XML de metadados de Federação**  entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

### <a name="configure-openathens-single-sign-on"></a>Configurar OpenAthens Single Sign-On

1. Numa janela do browser web diferente, inicie sessão no site da sua empresa OpenAthens como administrador.

2. Selecione **conexões** da lista sob a **gestão** separador. 

    ![Configurar o início de sessão único](./media/openathens-tutorial/tutorial_openathens_application1.png)

3. Selecione **SAML 1.1/2.0**e, em seguida, selecione a **configurar** botão.

    ![Configurar o início de sessão único](./media/openathens-tutorial/tutorial_openathens_application2.png)
    
4. Para adicionar a configuração, selecione o **navegue** para carregar o ficheiro. XML de metadados que transferiu a partir do portal do Azure e, em seguida, selecione **Add**.

    ![Configurar o início de sessão único](./media/openathens-tutorial/tutorial_openathens_application3.png)

5. Execute os seguintes passos sob o **detalhes** separador.

    ![Configurar o início de sessão único](./media/openathens-tutorial/tutorial_openathens_application4.png)

    a. Na **mapeamento de nome de exibição**, selecione **utilizar atributo**.

    b. Na **atributo de nome de exibição** texto, digite o valor `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.
    
    c. Na **mapeamento de utilizador exclusivo**, selecione **utilizar atributo**.

    d. Na **atributo de utilizador exclusivo** texto, digite o valor `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    e. Na **estado**, selecione todas as três caixas de verificação.

    f. Na **criar contas locais**, selecione **automaticamente**.

    g. Selecione **guardar alterações**.

    h. Do **<> / Confiadora** separador, copie a **URL de metadados** e abri-lo no browser para transferir o **SP metadados XML** ficheiro. Carregar este ficheiro de metadados de SP no **configuração básica de SAML** secção no Azure AD.

    ![Configurar o início de sessão único](./media/openathens-tutorial/tutorial_openathens_application5.png)

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para OpenAthens.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **OpenAthens**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, escreva e selecione **OpenAthens**.

    ![A ligação de OpenAthens na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-openathens-test-user"></a>Criar utilizador de teste OpenAthens

Nesta secção, um usuário chamado Eduarda Almeida é criado na OpenAthens. Suporta OpenAthens **aprovisionamento de utilizadores de just-in-time**, que está ativado por predefinição. Não existe nenhum item de ação para nesta secção. Se um utilizador já não existir no OpenAthens, é criado um novo após a autenticação.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico OpenAthens no painel de acesso, deve ser automaticamente sessão iniciada no OpenAthens para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

