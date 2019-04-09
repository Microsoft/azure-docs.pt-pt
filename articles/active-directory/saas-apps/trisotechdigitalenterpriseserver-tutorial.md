---
title: 'Tutorial: Integração do Active Directory do Azure com Trisotech Digital Enterprise Server | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Trisotech Digital Enterprise Server.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6d54d20c-eca1-4fa6-b56a-4c3ed0593db0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: c5d1a791de5c295b4644e5633983a5369200498e
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59256588"
---
# <a name="tutorial-azure-active-directory-integration-with-trisotech-digital-enterprise-server"></a>Tutorial: Integração do Active Directory do Azure com Trisotech Digital Enterprise Server

Neste tutorial, saiba como integrar Trisotech Digital Enterprise Server no Azure Active Directory (Azure AD).
Integrar Trisotech Digital Enterprise Server no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Trisotech Digital Enterprise Server.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para Trisotech Digital Enterprise Server (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Trisotech Digital Enterprise Server, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Trisotech Digital Enterprise Server logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Trisotech Digital Enterprise Server suporta **SP** iniciada SSO

* Trisotech Digital Enterprise Server suporta **Just In Time** aprovisionamento de utilizadores

## <a name="adding-trisotech-digital-enterprise-server-from-the-gallery"></a>Adicionando Trisotech Digital Enterprise Server da Galeria

Para configurar a integração do Trisotech Digital Enterprise Server no Azure AD, terá de adicionar Trisotech Digital Enterprise Server a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Trisotech Digital Enterprise Server a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Trisotech Digital Enterprise Server**, selecione **Trisotech Digital Enterprise Server** no painel de resultados, em seguida, clique em **Add** botão para adicionar o aplicação.

     ![Trisotech Digital Enterprise Server na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Trisotech Digital Enterprise Server com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Trisotech Digital Enterprise Server deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Trisotech Digital Enterprise Server, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Trisotech Digital Enterprise Server início de sessão único](#configure-trisotech-digital-enterprise-server-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste do servidor de Enterprise Digital Trisotech](#create-trisotech-digital-enterprise-server-test-user)**  - para ter um equivalente da Eduarda Almeida na Trisotech Digital Enterprise Server que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com Trisotech Digital Enterprise Server, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Trisotech Digital Enterprise Server** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![URLs de domínio do servidor do Enterprise Digital Trisotech e únicas início de sessão em informações](common/sp-identifier.png)

    a. Na **iniciar sessão no URL** caixa de texto, escreva um URL com o seguinte padrão: `https://<companyname>.trisotech.com`

    b. Na **identificador (ID de entidade)** caixa de texto, escreva um URL com o seguinte padrão: `https://<companyname>.trisotech.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o início de sessão real URL e o identificador. Contacte [equipa de suporte de cliente de servidor de Enterprise Digital Trisotech](mailto:support@trisotech.com) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

4. No **definir a segurança de início de sessão único com o SAML** na página a **certificado de assinatura SAML** secção, clique em botão Copiar para copiar **Url de metadados de Federação de aplicação** e guarde-o no seu computador.

    ![O link de download de certificado](common/copy-metadataurl.png)

### <a name="configure-trisotech-digital-enterprise-server-single-sign-on"></a>Configurar Trisotech Digital Enterprise Server início de sessão único

1. Numa janela do browser web diferente, inicie sessão no site da sua empresa Trisotech Digital Enterprise Server configuração como administrador.

2. Clique nas **ícone de Menu** e, em seguida, selecione **administração**.

    ![Configurar o início de sessão único](./media/trisotechdigitalenterpriseserver-tutorial/user1.png)

3. Selecione **fornecedor de utilizador**.

    ![Configurar o início de sessão único](./media/trisotechdigitalenterpriseserver-tutorial/user2.png)

4. Na **configurações de fornecedor de utilizador** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/trisotechdigitalenterpriseserver-tutorial/user3.png)

    a. Selecione **protegida Assertion Markup Language 2 (SAML 2)** na lista pendente na **método de autenticação**.

    b. Na **URL de metadados** caixa de texto, colar a **Url de metadados de Federação de aplicação** formam de valor, que copiou do portal do Azure.

    c. Na **ID da aplicação** caixa de texto, introduza o URL com o seguinte padrão: `https://<companyname>.trisotech.com`.

    d. Clique em **Guardar**

    e. Introduza o nome de domínio na **permitidos domínios (vazio significa que todas as pessoas)** caixa de texto, ela atribui automaticamente licenças para utilizadores correspondentes os domínios permitido

    f. Clique em **Guardar**

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Trisotech Digital Enterprise Server.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Trisotech Digital Enterprise Server**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Trisotech Digital Enterprise Server**.

    ![A ligação de Trisotech Digital Enterprise Server na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-trisotech-digital-enterprise-server-test-user"></a>Criar utilizador de teste Trisotech Digital Enterprise Server

Nesta secção, um usuário chamado Eduarda Almeida é criado na Trisotech Digital Enterprise Server. Trisotech Digital Enterprise Server suporta o aprovisionamento de utilizadores de just-in-time, que está ativado por predefinição. Não existe nenhum item de ação para nesta secção. Se um utilizador já não existir no Trisotech Digital Enterprise Server, é criado um novo após a autenticação.

>[!Note]
>Se precisar de criar manualmente um utilizador, contacte [equipa de suporte do Trisotech Digital Enterprise Server](mailto:support@trisotech.com).

### <a name="test-single-sign-on"></a>Testar início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Trisotech Digital Enterprise Server no painel de acesso, deve ser automaticamente conectado para o servidor de Enterprise Digital Trisotech para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

