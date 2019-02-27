---
title: 'Tutorial: Integração do Active Directory do Azure com o Software de RH Cezanne | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o Software de RH Cezanne.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 62b42e15-c282-492d-823a-a7c1c539f2cc
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b3c4eff92fa0ca63eccf331f5a6ba9e43610fc7b
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56874069"
---
# <a name="tutorial-azure-active-directory-integration-with-cezanne-hr-software"></a>Tutorial: Integração do Active Directory do Azure com o Software de RH Cezanne

Neste tutorial, saiba como integrar o Software de RH Cezanne com o Azure Active Directory (Azure AD).
Integrar o Software de RH Cezanne no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Software de RH Cezanne.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para o Software de RH Cezanne (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Software de RH Cezanne, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Software de RH Cezanne logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Oferece suporte a Software de RH Cezanne **SP** iniciada SSO

## <a name="adding-cezanne-hr-software-from-the-gallery"></a>Adicionando o Software de RH Cezanne da Galeria

Para configurar a integração de Software de RH Cezanne com o Azure AD, terá de adicionar Cezanne RH Software a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Cezanne RH Software a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Software de RH Cezanne**, selecione **Cezanne RH Software** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Software de RH Cezanne na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com o Software de RH Cezanne com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Software de RH Cezanne deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com o Software de RH Cezanne, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Cezanne RH Software início de sessão único](#configure-cezanne-hr-software-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste de Software de RH Cezanne](#create-cezanne-hr-software-test-user)**  - para ter um equivalente da Eduarda Almeida no Software de RH Cezanne que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com o Software de RH Cezanne, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Cezanne RH Software** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![Domínio de Software de RH Cezanne e URLs únicas início de sessão em informações](common/sp-identifier-reply.png)

    a. Na **iniciar sessão no URL** caixa de texto, escreva um URL com o seguinte padrão: `https://w3.cezanneondemand.com/CezanneOnDemand/-/<tenantidentifier>`

    b. Na **identificador (ID de entidade)** caixa de texto, escreva o URL: `https://w3.cezanneondemand.com/CezanneOnDemand/`

    c. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://w3.cezanneondemand.com:443/cezanneondemand/-/<tenantidentifier>/Saml/samlp`
    
    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de início de sessão e o URL de resposta real. Contacte [equipa de suporte de cliente de Software de RH Cezanne](https://cezannehr.com/services/support/) obter esses valores.

5. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

6. Sobre o **configurar o Software de RH Cezanne** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Azure Ad Identifier

    c. URL de fim de sessão

### <a name="configure-cezanne-hr-software-single-sign-on"></a>Configurar Cezanne RH Software início de sessão único

1. Numa janela do browser web diferente, início de sessão no seu inquilino de Software de RH Cezanne como administrador.

2. No painel de navegação esquerdo, clique em **configuração do System**. Aceda a **definições de segurança**. Em seguida, navegue até **configuração do início de sessão único**.

    ![Configure o lado de início de sessão na aplicação única](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_000.png)

3. Na **permitir que os utilizadores iniciar sessão com o seguinte serviço único início de sessão (SSO)** painel, verifique o **SAML 2.0** caixa e selecione o **configuração avançada** opção.

    ![Configure o lado de início de sessão na aplicação única](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_001.png)

4. Clique em **adicionar novo** botão.

    ![Configure o lado de início de sessão na aplicação única](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_002.png)

5. Execute os seguintes passos no **fornecedores de identidade do SAML 2.0** secção.

    ![Configure o lado de início de sessão na aplicação única](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_003.png)

    a. Introduza o nome do seu fornecedor de identidade como o **nome a apresentar**.

    b. Na **identificador da entidade** caixa de texto, cole o valor de **do Azure Ad identificador** que copiou do portal do Azure.

    c. Alteração da **vinculação de SAML** para "POST".

    d. Na **segurança ponto final do serviço de Token** caixa de texto, cole o valor de **URL de início de sessão** que copiou do portal do Azure.

    e. Na caixa de texto nome de atributo de ID de utilizador, introduza `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    f. Clique em **carregar** ícone para carregar o certificado transferido a partir do portal do Azure.

    g. Clique no botão **OK**.

6. Clique em **guardar** botão.

    ![Configure o lado de início de sessão na aplicação única](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_004.png)

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso ao Software de RH Cezanne.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Cezanne RH Software**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Cezanne RH Software**.

    ![A ligação de Software de RH Cezanne na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-cezanne-hr-software-test-user"></a>Criar utilizador de teste de Software de RH Cezanne

Para habilitar os utilizadores do Azure AD iniciar sessão no Software de RH Cezanne, tem de ser aprovisionados em Software de RH Cezanne. No caso do Software de RH Cezanne, aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no site da sua empresa Cezanne RH Software como um administrador.

2. No painel de navegação esquerdo, clique em **configuração do System**. Aceda a **gerir utilizadores**. Em seguida, navegue até **adicionar novo utilizador**.

    ![Novo utilizador](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_005.png "novo utilizador")

3. No **detalhes de pessoa** secção, executar passos abaixo:

    ![Novo utilizador](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_006.png "novo utilizador")

    a. Definir **utilizador interno** como OFF.

    b. Na **nome próprio** caixa de texto, como o tipo o primeiro nome de utilizador **Eduarda**.  

    c. Na **sobrenome** caixa de texto, como o tipo o apelido do utilizador **Simon**.

    d. Na **email** caixa de texto, como o tipo de endereço de e-mail do utilizador Brittasimon@contoso.com.

4. No **informações da conta** secção, executar passos abaixo:

    ![Novo utilizador](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_007.png "novo utilizador")

    a. Na **nome de utilizador** caixa de texto, como o tipo de e-mail do utilizador Brittasimon@contoso.com.

    b. Na **palavra-passe** caixa de texto, escreva a palavra-passe do utilizador.

    c. Selecione **RH Professional** como **função de segurança**.

    d. Clique em **OK**.

5. Navegue para **início de sessão único** separador e selecione **adicionar novo** no **SAML 2.0 identificadores** área.

    ![Usuário](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_008.png "utilizador")

6. Escolha o fornecedor de identidade para o **fornecedor de identidade** e, na caixa de texto de **identificador de utilizador**, introduza o endereço de e-mail da conta da Eduarda Almeida.

    ![Usuário](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_009.png "utilizador")

7. Clique em **guardar** botão.

    ![Usuário](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_010.png "utilizador")

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico de Software de RH Cezanne no painel de acesso, deve ser automaticamente conectado para o Software de RH Cezanne para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
