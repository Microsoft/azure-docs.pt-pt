---
title: 'Tutorial: Integração do Active Directory do Azure com serviços de gestão de participação | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e dos serviços de gestão de participação.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1f56e612-728b-4203-a545-a81dc5efda00
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7143d0afce7a3644286703a9eba0da1ee45305f2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67106547"
---
# <a name="tutorial-azure-active-directory-integration-with-attendance-management-services"></a>Tutorial: Integração do Active Directory do Azure com serviços de gestão de participação

Neste tutorial, saiba como integrar os serviços de gestão de participação no Azure Active Directory (Azure AD).
Integração de serviços de gestão de participação com o Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso aos serviços de gestão de participação.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para serviços de gestão de participação (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com os serviços de gestão de participação, precisa do seguinte:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter um [conta gratuita](https://azure.microsoft.com/free/)
* Serviços de gestão de participação no único início de sessão na subscrição ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Oferece suporte a serviços de gestão de participação **SP** iniciada SSO

## <a name="adding-attendance-management-services-from-the-gallery"></a>Adicionando os serviços de gestão de participação da Galeria

Para configurar a integração dos serviços de gestão de participação no Azure AD, terá de adicionar serviços de gestão de participação na partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar serviços de gestão de participação da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **dos serviços de gestão de participação**, selecione **dos serviços de gestão de participação** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Serviços de gestão de participação na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com os serviços de gestão de presença com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado nos serviços de gestão de participação deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com os serviços de gestão de participação, precisa concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar a participação no gerenciamento de serviços de início de sessão único](#configure-attendance-management-services-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste de serviços de gestão de participação](#create-attendance-management-services-test-user)**  - para ter um equivalente da Eduarda Almeida na participação dos serviços de gestão que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com os serviços de gestão de participação, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **dos serviços de gestão de participação** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![Participação no gerenciamento de serviços de domínio e URLs únicas início de sessão em informações](common/sp-identifier.png)

    a. Na **iniciar sessão no URL** caixa de texto, escreva um URL com o seguinte padrão: `https://id.obc.jp/<tenant information >/`

    b. Na **identificador (ID de entidade)** caixa de texto, escreva um URL com o seguinte padrão: `https://id.obc.jp/<tenant information >/`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o início de sessão real URL e o identificador. Contacte [equipa de suporte de cliente de serviços de gestão de participação](https://www.obcnet.jp/) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

5. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

6. Sobre o **configurar serviços de gestão de participação** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-attendance-management-services-single-sign-on"></a>Configurar a participação no gerenciamento dos serviços início de sessão único

1. Numa janela do browser diferente, início de sessão para o site da sua empresa de serviços de gestão de participação como administrador.

1. Clique em **autenticação SAML** sob a **seção de gerenciamento de segurança**.

    ![Configuração de serviços de gestão de participação](./media/attendancemanagementservices-tutorial/user1.png)

1. Execute os seguintes passos:

    ![Configuração de serviços de gestão de participação](./media/attendancemanagementservices-tutorial/user2.png)

    a. Selecione **autenticação de utilização SAML**.

    b. Na **identificador** caixa de texto, cole o valor de **do Azure AD identificador** valor, que copiou do portal do Azure.

    c. Na **URL de ponto final de autenticação** caixa de texto, cole o valor de **URL de início de sessão** valor, que copiou do portal do Azure.

    d. Clique em **selecionar um ficheiro** para carregar o certificado que transferiu a partir do Azure AD.

    e. Selecione **desative a autenticação de palavra-passe**.

    f. Clique em **registo**

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

Nesta secção, vai ativar Eduarda Almeida utilizar do Azure-início de sessão único, concedendo acesso aos serviços de gestão de participação.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **serviços de gestão de participação**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **dos serviços de gestão de participação**.

    ![A ligação de serviços de gestão de participação na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-attendance-management-services-test-user"></a>Criar utilizador de teste de serviços de gestão de participação

Para ativar a iniciar sessão nos serviços de gestão de participação de utilizadores do Azure AD, tem de ser aprovisionados para os serviços de gestão de participação. No caso dos serviços de gestão de participação, o aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão para o site da sua empresa de serviços de gestão de participação, como um administrador.

1. Clique em **gestão de utilizadores** sob a **seção de gerenciamento de segurança**.

    ![Adicionar o funcionário](./media/attendancemanagementservices-tutorial/user5.png)

1. Clique em **novo início de sessão de regras**.

    ![Adicionar o funcionário](./media/attendancemanagementservices-tutorial/user3.png)

1. Na **OBCiD informações** secção, execute os seguintes passos:

    ![Adicionar o funcionário](./media/attendancemanagementservices-tutorial/user4.png)

    a. Na **OBCiD** caixa de texto, como o tipo de e-mail do utilizador `BrittaSimon\@contoso.com`.

    b. Na **palavra-passe** caixa de texto, escreva a palavra-passe do utilizador.

    c. Clique em **registo**

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico de serviços de gestão de participação no painel de acesso, deve ser automaticamente conectado para os serviços de gestão de participação para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)