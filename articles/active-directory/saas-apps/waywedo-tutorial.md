---
title: 'Tutorial: Integração do Active Directory do Azure com a forma como fazemos | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e a forma como fazemos.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 84fc4f36-ecd1-42c6-8a70-cb0f3dc15655
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 541be5466b65705daa0485976eab3df8eb3d707f
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2019
ms.locfileid: "65905324"
---
# <a name="tutorial-azure-active-directory-integration-with-way-we-do"></a>Tutorial: Integração do Active Directory do Azure com a forma como fazemos

Neste tutorial, saiba como integrar a forma como fazemos com o Azure Active Directory (Azure AD).
Integrar a forma como fazemos com o Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD, quem tem acesso a forma como fazemos.
* Pode permitir que os utilizadores iniciar sessão automaticamente forma podemos fazer (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com a forma como fazemos, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter um [conta gratuita](https://azure.microsoft.com/free/)
* Ativado de forma podemos início de sessão único subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Oferece suporte a forma como fazemos **SP** iniciada SSO

* Oferece suporte a forma como fazemos **Just In Time** aprovisionamento de utilizadores

## <a name="adding-way-we-do-from-the-gallery"></a>Adicionando a forma como fazemos da Galeria

Para configurar a integração de forma podemos fazer com o Azure AD, terá de adicionar a maneira que fazemos em partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar a forma como fazemos a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **forma fazemos**, selecione **forma fazemos** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Forma como fazemos na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com a forma como fazemos com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado na forma como fazemos tem de ser estabelecida.

Para configurar e testar o Azure AD início de sessão único com a forma como fazemos, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar a forma podemos início de sessão único](#configure-way-we-do-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar de forma podemos testar utilizador](#create-way-we-do-test-user)**  - para ter um equivalente da Eduarda Almeida na forma como fazemos isso está ligada à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com a forma como fazemos, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **forma fazemos** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![Informações de início de sessão de individual de forma podemos fazer domínio e URLs](common/sp-identifier.png)

    a. Na **iniciar sessão no URL** caixa de texto, escreva um URL com o seguinte padrão: `https://<SUBDOMAIN>.waywedo.com/Authentication/ExternalSignIn`

    b. Na **identificador (ID de entidade)** caixa de texto, escreva um URL com o seguinte padrão: `https://<SUBDOMAIN>.waywedo.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o início de sessão real URL e o identificador. Contacte [equipa de suporte de forma podemos fazer cliente](mailto:support@waywedo.com) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

5. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (bruto)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificateraw.png)

6. Sobre o **configurar a forma como fazemos** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-way-we-do-single-sign-on"></a>Configurar a forma podemos início de sessão único

1. Numa janela do browser web diferente, inicie sessão na forma como fazemos como um administrador de segurança.

2. Clique nas **ícone de pessoa** no canto superior direito de qualquer página na forma como fazemos, em seguida, clique em **conta** no menu pendente.

    ![Conta de fazemos forma](./media/waywedo-tutorial/tutorial_waywedo_account.png) 

3. Clique nas **ícone de menu** para abrir o menu de navegação de push e clique em **início de sessão único**.

    ![Forma como fazemos único](./media/waywedo-tutorial/tutorial_waywedo_single.png)

4. Sobre o **configuração do início de sessão única** página, execute os seguintes passos:

    ![Forma como fazemos guardar](./media/waywedo-tutorial/tutorial_waywedo_save.png)

    a. Clique nas **ativar o início de sessão único** alternar para **Sim** para ativar o início de sessão único.

    b. Na **nome de início de sessão único** caixa de texto, introduza o seu nome.

    c. Na **ID de entidade** caixa de texto, cole o valor de **do Azure AD identificador**, que copiou do portal do Azure.

    d. Na **URL de SSO SAML** caixa de texto, cole o valor de **URL de início de sessão**, que copiou do portal do Azure.

    e. Carregue o certificado ao clicar o **selecionar** junto a **certificado**.

    f. **Definições opcionais** -
    
    * Permitir palavras-passe - quando esta opção está desativada, a palavra-passe regular funciona de forma podemos fazer para que os utilizadores só podem utilizar o início de sessão único.

    * Ativar aprovisionamento automático - quando esta opção estiver ativada, o endereço de e-mail utilizado para início de sessão será automaticamente comparado com a lista de utilizadores na forma como fazemos. Se o endereço de e-mail não corresponder a um utilizador ativo na forma como fazemos, ele adiciona automaticamente uma nova conta de utilizador da pessoa a iniciar sessão, solicitando todas as informações em falta.

      > [!NOTE]
      > Os utilizadores adicionados através do início de sessão único são adicionados como usuários gerais e não estão atribuídos uma função do sistema. Um administrador pode entrar e modificar a sua função de segurança como um editor ou administrador e também pode atribuir um ou vários organograma funções. 

    g. Clique em **guardar** para manter as suas definições.

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

Nesta secção, vai ativar Eduarda Almeida a utilizar do Azure-início de sessão único, conceder acesso à forma como fazemos.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **forma fazemos**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **forma fazemos**.

    ![A ligação de forma que fazemos na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-way-we-do-test-user"></a>Criar utilizador de teste de forma podemos fazer

Nesta secção, um usuário chamado Eduarda Almeida é criado na forma como fazemos. Forma como fazemos suporta o aprovisionamento de utilizadores de just-in-time, que está ativado por predefinição. Não existe nenhum item de ação para nesta secção. Se um utilizador já não existir na forma como fazemos, é criado um novo após a autenticação.

> [!Note]
> Se precisar de criar manualmente um utilizador, contacte [equipa de suporte de forma podemos fazer cliente](mailto:support@waywedo.com).

### <a name="test-single-sign-on"></a>Testar início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico de forma que fazemos no painel de acesso, deve ser automaticamente conectado para a forma como fazemos para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

