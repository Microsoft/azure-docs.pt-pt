---
title: 'Tutorial: Integração do Active Directory do Azure com Percolate | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Percolate.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 355f9659-b378-44c9-aa88-236e9b529a53
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: 2110b1ec7d5d6b317341855ff19acf7975733e71
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2019
ms.locfileid: "59617874"
---
# <a name="tutorial-azure-active-directory-integration-with-percolate"></a>Tutorial: Integração do Active Directory do Azure com Percolate

Neste tutorial, saiba como integrar Percolate com o Azure Active Directory (Azure AD).
Integrar Percolate no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Percolate.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para Percolate (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Percolate, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter um [conta gratuita](https://azure.microsoft.com/free/).
* Percolate única início de sessão ativada subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Percolate suporta **SP** e **IDP** iniciada SSO

## <a name="adding-percolate-from-the-gallery"></a>Adicionando Percolate da Galeria

Para configurar a integração do Percolate com o Azure AD, terá de adicionar Percolate a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Percolate a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Percolate**, selecione **Percolate** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Percolate na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Percolate com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Percolate deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Percolate, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Percolate início de sessão único](#configure-percolate-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste Percolate](#create-percolate-test-user)**  - para ter um equivalente da Eduarda Almeida na Percolate que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com Percolate, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Percolate** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, se desejar configurar a aplicação na **IDP** modo iniciado, o utilizador não tem de realizar qualquer passo dado que a aplicação já está pré-integrado com o Azure.

    ![Percolate domínio e URLs únicas início de sessão em informações](common/preintegrated.png)

5. Clique em **definir URLs adicionais** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    ![Percolate domínio e URLs únicas início de sessão em informações](common/metadata-upload-additional-signon.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL:  `https://percolate.com/app/login`

6. No **definir a segurança de início de sessão único com o SAML** na página a **certificado de assinatura SAML** secção, clique em botão Copiar para copiar **Url de metadados de Federação de aplicação** e guarde-o no seu computador.

    ![O link de download de certificado](common/copy-metadataurl.png)

7. Sobre o **configurar Percolate** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-percolate-single-sign-on"></a>Configurar Percolate início de sessão único

1. Numa janela do browser web diferente, inicie sessão no Percolate como um administrador.

2. No lado esquerdo da home page, clique em **definições**.
    
    ![Configurar o início de sessão único](./media/percolate-tutorial/configure01.png)

3. Do lado esquerdo da barra de menus, clique em **SSO** sob **organização**.

    ![Configurar o início de sessão único](./media/percolate-tutorial/configure02.png)

    a. Na **URL de início de sessão** caixa de texto, cole o valor de **URL de início de sessão** que copiou do portal do Azure.

    b. Na **ID de entidade** caixa de texto, cole o valor de **do Azure AD identificador** que copiou do portal do Azure.

    c. No bloco de notas, abra o certificado com codificação base 64 que transferiu a partir do portal do Azure, copie o seu conteúdo e, em seguida, cole-o para o **x509 certificados** caixa.

    d. Na **atributo de correio eletrónico** caixa de texto, tipo **emailaddress**.

    e. **URL de metadados do fornecedor de identidade** campo é opcional e, se tem **url de metadados de Federação de aplicação** que copiou do portal do Azure, cole-a no **URL de metadados do fornecedor de identidade** caixa de texto.

    f. Selecione **não** como **AuthNRequests deve ser assinado?**.

    g. Selecione **não** como **aprovisionamento automático do ativar o SSO**.

    h. Clique em **Guardar**.

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Percolate.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Percolate**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Percolate**.

    ![A ligação de Percolate na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-percolate-test-user"></a>Criar utilizador de teste Percolate

Para ativar a utilizadores do Azure AD iniciar sessão no Percolate, tem de ser aprovisionados em Percolate. Percolate, aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no Percolate como administrador.

2. Do lado esquerdo da barra de menus, clique em **usuários** sob **organização** e navegue para **novos utilizadores**.

    ![Configurar o início de sessão único](./media/percolate-tutorial/configure03.png)

3. Sobre o **criar utilizadores** página, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/percolate-tutorial/configure04.png)

    a. Na **E-Mail** texto, introduza o e-mail do utilizador, como brittasimon@contoso.com.

    b. Na **Fullname** texto, introduza o nome de utilizador, como **Brittasimon**.

    c. Clique em **criar utilizadores**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Percolate no painel de acesso, deve ser automaticamente sessão iniciada no Percolate para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

