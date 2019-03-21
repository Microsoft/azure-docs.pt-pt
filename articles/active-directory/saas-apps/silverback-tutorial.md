---
title: 'Tutorial: Integração do Active Directory do Azure com Silverback | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Silverback.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 32cfc96f-2137-49ff-818b-67feadcd73b7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 1a104da9ecb28d2109e82056995ef7a8048eafe2
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57838753"
---
# <a name="tutorial-azure-active-directory-integration-with-silverback"></a>Tutorial: Integração do Active Directory do Azure com Silverback

Neste tutorial, saiba como integrar Silverback com o Azure Active Directory (Azure AD).
Integrar Silverback no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Silverback.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para Silverback (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Silverback, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Silverback logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta silverback **SP** iniciada SSO

## <a name="adding-silverback-from-the-gallery"></a>Adicionando Silverback da Galeria

Para configurar a integração do Silverback com o Azure AD, terá de adicionar Silverback a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Silverback a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Silverback**, selecione **Silverback** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Silverback na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Silverback com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Silverback deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Silverback, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Silverback Single Sign-On](#configure-silverback-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste Silverback](#create-silverback-test-user)**  - para ter um equivalente da Eduarda Almeida na Silverback que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com Silverback, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Silverback** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![Silverback domínio e URLs únicas início de sessão em informações](common/sp-identifier-reply.png)

    a. Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://<YOURSILVERBACKURL>.com/ssp`

    b. Na **identificador** caixa, escreva um URL com o seguinte padrão: `<YOURSILVERBACKURL>.com`

    c. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://<YOURSILVERBACKURL>.com/sts/authorize/login`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de início de sessão, identificador e o URL de resposta real. Contacte [equipa de suporte de cliente Silverback](mailto:helpdesk@matrix42.com) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

5. No **definir a segurança de início de sessão único com o SAML** na página a **certificado de assinatura SAML** secção, clique em botão Copiar para copiar **Url de metadados de Federação de aplicação** e guarde-o no seu computador.

    ![O link de download de certificado](common/copy-metadataurl.png)

### <a name="configure-silverback-single-sign-on"></a>Configurar Silverback início de sessão único

1. Num navegador da web diferentes, inicie sessão no seu servidor Silverback como administrador.

2. Navegue para **administrador** > **fornecedor de autenticação**.

3. Sobre o **definições do fornecedor de autenticação** página, execute os seguintes passos:

    ![O administrador](./media/silverback-tutorial/tutorial_silverback_admin.png)

    a.  Clique em **importação a partir do URL**.

    b.  Cole o URL de metadados copiado e clique em **OK**.

    c.  Confirme com **OK** , em seguida, os valores serão preenchidos automaticamente.

    d.  Ativar **Mostrar na página de início de sessão**.

    e.  Ativar **criação dinâmica de utilizador** se pretender adicionar automaticamente utilizadores do Azure AD autorizado (opcional).

    f.  Criar uma **Title** do botão sobre o Portal Self-Service.

    g.  Carregar uma **ícone** clicando em **Escolher ficheiro**.

    h.  Selecione o plano de fundo **cor** do botão.

    i.  Clique em **Guardar**.

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Silverback.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Silverback**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Silverback**.

    ![A ligação de Silverback na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-silverback-test-user"></a>Criar utilizador de teste Silverback

Para ativar a utilizadores do Azure AD iniciar sessão no Silverback, tem de ser aprovisionados em Silverback. Silverback, aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no seu servidor Silverback como administrador.

2. Navegue para **usuários** e **adicionar um novo utilizador do dispositivo**.

3. Sobre o **básica** página, execute os seguintes passos:

    ![O utilizador](./media/silverback-tutorial/tutorial_silverback_user.png)

    a. Na **nome de utilizador** texto, introduza o nome de utilizador, como **Eduarda**.

    b. Na **nome próprio** texto, introduza o nome de utilizador, como **Eduarda**.

    c. Na **sobrenome** texto, digite o apelido do utilizador, como **Simon**.

    d. Na **endereço de email** texto, introduza o e-mail do utilizador, como **Brittasimon@contoso.com**.

    e. Na **palavra-passe** texto, introduza a palavra-passe.

    f. Na **Confirmar palavra-passe** caixa de texto, volte a introduzir a palavra-passe e confirme.

    g. Clique em **Guardar**.

> [!NOTE]
> Se não quiser criar manualmente a cada utilizador ativar a **criação dinâmica de utilizador** caixa de verificação sob **administração** > **fornecedor de autenticação**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Silverback no painel de acesso, deve ser automaticamente sessão iniciada no Silverback para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [ Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

