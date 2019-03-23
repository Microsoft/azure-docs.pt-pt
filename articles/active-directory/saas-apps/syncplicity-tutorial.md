---
title: 'Tutorial: Integração do Active Directory do Azure com Syncplicity | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Syncplicity.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 896a3211-f368-46d7-95b8-e4768c23be08
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 11ac9d859ec76c782f762dfc6f424d70ea487665
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58360233"
---
# <a name="tutorial-azure-active-directory-integration-with-syncplicity"></a>Tutorial: Integração do Active Directory do Azure com Syncplicity

Neste tutorial, saiba como integrar Syncplicity com o Azure Active Directory (Azure AD).
Integrar Syncplicity no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Syncplicity.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para Syncplicity (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Syncplicity, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Syncplicity logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta Syncplicity **SP** iniciada SSO

## <a name="adding-syncplicity-from-the-gallery"></a>Adicionando Syncplicity da Galeria

Para configurar a integração do Syncplicity com o Azure AD, terá de adicionar Syncplicity a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Syncplicity a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Syncplicity**, selecione **Syncplicity** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Syncplicity na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Syncplicity com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Syncplicity deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Syncplicity, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Syncplicity Single Sign-On](#configure-syncplicity-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste Syncplicity](#create-syncplicity-test-user)**  - para ter um equivalente da Eduarda Almeida na Syncplicity que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com Syncplicity, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Syncplicity** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![Syncplicity domínio e URLs únicas início de sessão em informações](common/sp-identifier.png)

    a. Na **iniciar sessão no URL** caixa de texto, escreva um URL com o seguinte padrão: `https://<companyname>.syncplicity.com`

    b. Na **identificador (ID de entidade)** caixa de texto, escreva um URL com o seguinte padrão: `https://<companyname>.syncplicity.com/sp`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o início de sessão real URL e o identificador. Contacte [equipa de suporte de cliente Syncplicity](https://www.syncplicity.com/contact-us) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

5. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

6. Sobre o **configurar Syncplicity** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-syncplicity-single-sign-on"></a>Configurar Syncplicity Single Sign-On

1. Inicie sessão no seu **Syncplicity** inquilino.

1. No menu na parte superior, clique em **administrador**, selecione **definições**e, em seguida, clique em **domínio personalizado e início de sessão único**.

    ![Syncplicity](./media/syncplicity-tutorial/ic769545.png "Syncplicity")

1. Sobre o **único início de sessão (SSO)** caixa de diálogo página, execute os seguintes passos:

    ![Início de sessão único \(SSO\)](./media/syncplicity-tutorial/ic769550.png "Single Sign-On \\\(SSO\\\)")

    a. Na **Custom Domain** caixa de texto, escreva o nome do seu domínio.
  
    b. Selecione **habilitado** como **único início de sessão em estado**.

    c. Na **Id de entidade** caixa de texto, cole o valor de **do Azure Ad identificador** que copiou do portal do Azure.

    d. Na **URL da página de início de sessão** caixa de texto, colar a **URL de início de sessão** que copiou do portal do Azure.

    e. Na **URL de página de fim de sessão** caixa de texto, colar a **URL de fim de sessão** que copiou do portal do Azure.

    f. Na **certificado do fornecedor de identidade**, clique em **Escolher ficheiro**e, em seguida, carregue o certificado que transferiu do portal do Azure.

    g. Clique em **guardar alterações**.

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Syncplicity.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Syncplicity**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Syncplicity**.

    ![A ligação de Syncplicity na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.
    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-syncplicity-test-user"></a>Criar utilizador de teste Syncplicity

Para utilizadores do AAD conseguir iniciar sessão, têm de ser aprovisionados para aplicação Syncplicity. Esta secção descreve como criar contas de utilizador do AAD no Syncplicity.

**Para Aprovisionar uma conta de utilizador para Syncplicity, execute os seguintes passos:**

1. Inicie sessão no seu **Syncplicity** inquilino (por exemplo: `https://company.Syncplicity.com`).

1. Clique em **administrador** e selecione **contas de utilizador** e, em seguida, clique em **adicionar um utilizador**.

    ![Gerir utilizadores](./media/syncplicity-tutorial/ic769764.png "gerir utilizadores")

1. Tipo de **endereços de E-Mail** de uma conta do Azure AD que pretende aprovisionar, selecione **utilizador** como **função**e, em seguida, clique em **seguinte**.

    ![Informações da conta](./media/syncplicity-tutorial/ic769765.png "informações da conta")

    > [!NOTE]
    > O titular da conta do AAD, obtém um e-mail, incluindo uma ligação para confirmar e ativar a conta.

1. Selecione um grupo na sua empresa que deve se tornar um membro do seu novo utilizador e, em seguida, clique em **seguinte**.

    ![Associação de grupo](./media/syncplicity-tutorial/ic769772.png "associação de grupo")

    > [!NOTE]
    > Se não houver nenhum grupo listado, clique em **seguinte**.

1. Selecione as pastas que pretende colocar sob o controle do Syncplicity no computador do usuário e, em seguida, clique em **seguinte**.

    ![Pastas de Syncplicity](./media/syncplicity-tutorial/ic769773.png "Syncplicity pastas")

> [!NOTE]
> Pode utilizar quaisquer outras Syncplicity utilizador conta criação ferramentas ou APIs fornecidas pelo Syncplicity para aprovisionar contas de utilizador do AAD.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Syncplicity no painel de acesso, deve ser automaticamente sessão iniciada no Syncplicity para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [ Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
