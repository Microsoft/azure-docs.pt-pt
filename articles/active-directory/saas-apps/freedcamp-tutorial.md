---
title: 'Tutorial: Integração do Active Directory do Azure com Freedcamp | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Freedcamp.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: bfc73563-017d-458f-b634-162f93e03b74
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/29/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39aeaa3edd3682272d63f6edca1dd2a341c00ba5
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64927059"
---
# <a name="tutorial-azure-active-directory-integration-with-freedcamp"></a>Tutorial: Integração do Active Directory do Azure com Freedcamp

Neste tutorial, saiba como integrar Freedcamp com o Azure Active Directory (Azure AD).
Integrar Freedcamp no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Freedcamp.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para Freedcamp (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Freedcamp, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter um [conta gratuita](https://azure.microsoft.com/free/)
* Freedcamp logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta Freedcamp **SP e IDP** iniciada SSO

## <a name="adding-freedcamp-from-the-gallery"></a>Adicionando Freedcamp da Galeria

Para configurar a integração do Freedcamp com o Azure AD, terá de adicionar Freedcamp a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Freedcamp a partir da galeria, execute os seguintes passos:**

1. Na  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique nas **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, clique em **nova aplicação** botão na parte superior da caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Freedcamp**, selecione **Freedcamp** do painel de resultados e, em seguida, clique o **Add** botão para adicionar a aplicação.

    ![Freedcamp na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Freedcamp com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Freedcamp deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Freedcamp, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Freedcamp Single Sign-On](#configure-freedcamp-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste Freedcamp](#create-freedcamp-test-user)**  - para ter um equivalente da Eduarda Almeida na Freedcamp que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com Freedcamp, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Freedcamp** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Na **definir a segurança de início de sessão único com o SAML** página, clique no **editar** ícone para abrir o **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, se desejar configurar a aplicação no **IDP** iniciada pelo modo, execute os seguintes passos:

    ![Freedcamp domínio e URLs únicas início de sessão em informações](common/idp-intiated.png)

    a. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://<SUBDOMAIN>.freedcamp.com/sso/<UNIQUEID>`

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://<SUBDOMAIN>.freedcamp.com/sso/acs/<UNIQUEID>`

5. Clique em **definir URLs adicionais** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    ![Freedcamp domínio e URLs únicas início de sessão em informações](common/metadata-upload-additional-signon.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão:  `https://<SUBDOMAIN>.freedcamp.com/login`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificador, o URL de resposta e início de sessão real. Os utilizadores também podem introduzir os valores de url em relação ao seu próprio domínio de cliente e podem não ser necessariamente do padrão `freedcamp.com`, podem introduzir qualquer valor ao cliente domínio específico, específico para a sua instância da aplicação. Também pode contactar [equipa de suporte de cliente Freedcamp](mailto:devops@freedcamp.com) para obter mais informações sobre padrões de url.

4. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

6. Sobre o **configurar Freedcamp** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-freedcamp-single-sign-on"></a>Configurar Freedcamp Single Sign-On

1. Numa janela do browser web diferente, inicie sessão no Freedcamp como um administrador de segurança.

2. No canto superior direito da página, clique em **perfil** e, em seguida, navegue até **minha conta**.

    ![Configuração de Freedcamp](./media/freedcamp-tutorial/config01.png)

3. Do lado esquerdo da barra de menus, clique em **SSO** e, no **ligações de Your SSO** página execute os seguintes passos:

    ![Configuração de Freedcamp](./media/freedcamp-tutorial/config02.png)

    a. Na **Title** texto, escreva o título.

    b. Na **ID de entidade** caixa de texto, colar a **do Azure AD identificador** valor, que copiou do portal do Azure.

    c. Na **URL de início de sessão** caixa de texto, colar a **URL de início de sessão** valor, que copiou do portal do Azure.

    d. Abra o certificado com codificação Base64 no bloco de notas, copie o seu conteúdo e colá-lo no **certificado** caixa de texto.

    e. Clique em **Submit** (Submeter).

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** , insira **BrittaSimon**.
  
    b. Na **nome de utilizador** , digite `brittasimon@yourcompanydomain.extension`. Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Freedcamp.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Freedcamp**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Freedcamp**.

    ![A ligação de Freedcamp na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** caixa de diálogo, clique nas **atribuir** botão.

### <a name="create-freedcamp-test-user"></a>Criar utilizador de teste Freedcamp

Para ativar a utilizadores do Azure AD iniciar sessão no Freedcamp, tem de ser aprovisionados em Freedcamp. Freedcamp, aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Numa janela do browser web diferente, inicie sessão no Freedcamp como um administrador de segurança.

2. No canto toright da parte superior da página, clique em **perfil** e, em seguida, navegue até **sistema gerir**.

    ![Configuração de Freedcamp](./media/freedcamp-tutorial/config03.png)

3. No lado direito da página Gerir o sistema, execute os seguintes passos:

    ![Configuração de Freedcamp](./media/freedcamp-tutorial/config04.png)

    a. Clique em **adicionar ou convidar utilizadores**.

    b. Na **E-Mail** texto, introduza o e-mail do utilizador, como `Brittasimon@contoso.com`.

    c. Clique em **adicionar utilizador**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Freedcamp no painel de acesso, deve ser automaticamente sessão iniciada no Freedcamp para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

