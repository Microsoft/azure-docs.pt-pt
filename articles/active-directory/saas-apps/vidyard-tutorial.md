---
title: 'Tutorial: Integração do Active Directory do Azure com Vidyard | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Vidyard.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: bed7df23-6e13-4e7c-b4cc-53ed4804664d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 5ff939fa514e067c26daf609270ef49de8c16db2
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66143166"
---
# <a name="tutorial-azure-active-directory-integration-with-vidyard"></a>Tutorial: Integração do Active Directory do Azure com Vidyard

Neste tutorial, saiba como integrar Vidyard com o Azure Active Directory (Azure AD).
Integrar Vidyard no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Vidyard.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para Vidyard (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Vidyard, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter um [conta gratuita](https://azure.microsoft.com/free/)
* Vidyard logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta Vidyard **SP** e **IDP** iniciada SSO

* Suporta Vidyard **Just In Time** aprovisionamento de utilizadores

## <a name="adding-vidyard-from-the-gallery"></a>Adicionando Vidyard da Galeria

Para configurar a integração do Vidyard com o Azure AD, terá de adicionar Vidyard a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Vidyard a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Vidyard**, selecione **Vidyard** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Vidyard na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Vidyard com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Vidyard deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Vidyard, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Vidyard Single Sign-On](#configure-vidyard-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste Vidyard](#create-vidyard-test-user)**  - para ter um equivalente da Eduarda Almeida na Vidyard que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com Vidyard, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Vidyard** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, se desejar configurar a aplicação no **IDP** iniciada pelo modo, execute os seguintes passos:

    ![Vidyard domínio e URLs únicas início de sessão em informações](common/idp-intiated.png)

    a. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://secure.vidyard.com/sso/saml/<unique id>/metadata`

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://secure.vidyard.com/sso/saml/<unique id>/consume`

5. Clique em **definir URLs adicionais** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    ![Vidyard domínio e URLs únicas início de sessão em informações](common/metadata-upload-additional-signon.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão:  `https://secure.vidyard.com/sso/saml/<unique id>/login`

    > [!NOTE]
    > Estes valores não são reais. Atualizar estes valores com o identificador de real, a URL de resposta e o início de sessão no URL, que é explicado mais tarde no tutorial. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

6. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

7. Sobre o **configurar Vidyard** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-vidyard-single-sign-on"></a>Configurar Vidyard Single Sign-On

1. Numa janela do browser web diferente, inicie sessão no site da sua empresa Vidyard Software como um administrador.

2. A partir do Vidyard dashboard, selecione **grupo** > **segurança**

    ![Configuração de Vidyard](./media/vidyard-tutorial/configure1.png)

3. Clique em **novo perfil** separador.

    ![Configuração de Vidyard](./media/vidyard-tutorial/configure2.png)

4. Na **configuração SAML do** secção, execute os seguintes passos:

    ![Configuração de Vidyard](./media/vidyard-tutorial/configure3.png)

    a. Introduza o nome do perfil geral na **nome do perfil** caixa de texto.

    b. Cópia **página de início de sessão de utilizador de SSO** valor e cole-a na **iniciar sessão no URL** caixa de texto no **configuração básica de SAML** secção no portal do Azure.

    c. Cópia **URL de ACS** valor e cole-a na **URL de resposta** caixa de texto no **configuração básica de SAML** secção no portal do Azure.

    d. Cópia **URL de emissor/metadados** valor e cole-a na **identificador** caixa de texto no **configuração básica de SAML** secção no portal do Azure.

    e. Abra o ficheiro de certificado transferido a partir do portal do Azure no bloco de notas e, em seguida, cole-o para o **certificado X.509** caixa de texto.

    f. Na **URL de ponto final de SAML** caixa de texto, cole o valor de **URL de início de sessão** copiados a partir do portal do Azure.

    g. Clique em **Confirmar**.

5. No separador de início de sessão único, selecione **atribuir** junto a um perfil existente

    ![Configuração de Vidyard](./media/vidyard-tutorial/configure4.png)

    > [!NOTE]
    > Depois de criar um perfil SSO, atribuí-la para qualquer grupo (s) para o qual os utilizadores precisarão de acesso através do Azure. Se o utilizador não existir dentro do grupo ao qual foram atribuídos, Vidyard irá automaticamente criar uma conta de utilizador e atribuir a respetiva função em tempo real.

6. Selecione o seu grupo de organização, o que é visível no **grupos disponíveis para atribuir**.

    ![Configuração de Vidyard](./media/vidyard-tutorial/configure5.png)

7. Pode ver os grupos atribuídos sob o **grupos atribuídos**. Selecione uma função para o grupo de acordo com a sua organização e clique em **confirmar**.

    ![Configuração de Vidyard](./media/vidyard-tutorial/configure6.png)

    > [!NOTE]
    > Para obter mais informações, consulte [este documento](https://knowledge.vidyard.com/hc/articles/360009990033-SAML-based-Single-Sign-On-SSO-in-Vidyard).

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Vidyard.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Vidyard**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Vidyard**.

    ![A ligação de Vidyard na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-vidyard-test-user"></a>Criar utilizador de teste Vidyard

Nesta secção, um usuário chamado Eduarda Almeida é criado na Vidyard. Vidyard suporta o aprovisionamento de utilizadores de just-in-time, que está ativado por predefinição. Não existe nenhum item de ação para nesta secção. Se um utilizador já não existir no Vidyard, é criado um novo após a autenticação.

>[!Note]
>Se precisar de criar manualmente um utilizador, contacte [equipa de suporte de Vidyard](mailto:support@vidyard.com).

### <a name="test-single-sign-on"></a>Testar início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Vidyard no painel de acesso, deve ser automaticamente sessão iniciada no Vidyard para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

