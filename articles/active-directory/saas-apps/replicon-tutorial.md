---
title: 'Tutorial: Integração do Active Directory do Azure com Replicon | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Replicon.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 02a62f15-917c-417c-8d80-fe685e3fd601
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b27615b0c76b5c23bbc79788431b0e909b8bf22a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67092772"
---
# <a name="tutorial-integrate-replicon-with-azure-active-directory"></a>Tutorial: Integrar Replicon com o Azure Active Directory

Neste tutorial, irá aprender como integrar Replicon com o Azure Active Directory (Azure AD). Quando integrar Replicon com o Azure AD, pode:

* Controlar no Azure AD que tenha acesso ao Replicon.
* Permita que os utilizadores ser automaticamente sessão iniciada para Replicon com as suas contas do Azure AD.
* Gira as suas contas num local central – portal do Azure.

Para saber mais sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver uma subscrição, pode obter a versão de avaliação gratuita de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).
* Replicon início de sessão único (SSO) ativado na subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configurar e testar o SSO do Azure AD num ambiente de teste. Suporta replicon **SP** iniciada SSO.

## <a name="adding-replicon-from-the-gallery"></a>Adicionando Replicon da Galeria

Para configurar a integração do Replicon com o Azure AD, terá de adicionar Replicon a partir da Galeria à sua lista de aplicações de SaaS geridas.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, selecione o **do Azure Active Directory** serviço.
1. Navegue para **aplicações empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar nova aplicação, selecione **nova aplicação**.
1. Na **adicionar a partir da galeria** secção, escreva **Replicon** na caixa de pesquisa.
1. Selecione **Replicon** do painel de resultados e, em seguida, adicionar a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Configurar e testar o SSO do Azure AD com Replicon com um utilizador de teste **B.Simon**. Para SSO para funcionar, tem de estabelecer uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Replicon.

Para configurar e testar o SSO do Azure AD com Replicon, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar o Replicon SSO](#configure-replicon-sso)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com B.Simon.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar B.Simon utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste Replicon](#create-replicon-test-user)**  - para ter um equivalente de B.Simon no Replicon que está ligado à representação de utilizador do Azure AD.
6. **[Testar o SSO](#test-sso)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na **Replicon** página de integração de aplicativo, encontrar o **gerir** secção e selecione **início de sessão único**.
1. Sobre o **selecionar um método de início de sessão único** , selecione **SAML**.
1. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique no ícone de edição/caneta para **configuração básica de SAML** para editar as definições.

   ![Editar a configuração SAML do básico](common/edit-urls.png)

1. Sobre o **configuração básica de SAML** página, introduza os valores para os seguintes campos:

    1. Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://global.replicon.com/!/saml2/<client name>/sp-sso/post`

    1. Na **identificador** caixa, escreva um URL com o seguinte padrão: `https://global.replicon.com/!/saml2/<client name>`

    1. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://global.replicon.com/!/saml2/<client name>/sso/post`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de início de sessão, identificador e o URL de resposta real. Contacte [equipa de suporte de cliente Replicon](https://www.replicon.com/customerzone/contact-support) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

1. Clique no ícone de edição/caneta da **certificado de assinatura SAML** para editar as definições.

    ![Algoritmo de assinatura](common/signing-algorithm.png)

    1. Selecione **asserção de SAML de início de sessão** como o **opção assinatura**.

    1. Selecione **SHA-256** como o **algoritmo de assinatura**.

1. No **definir a segurança de início de sessão único com o SAML** na página a **certificado de assinatura SAML** secção, encontrar **XML de metadados de Federação** e selecione **transferir** para transferir o certificado e guarde-o no seu computador.

   ![O link de download de certificado](common/metadataxml.png)

### <a name="configure-replicon-sso"></a>Configurar Replicon SSO

1. Numa janela do browser web diferente, inicie sessão no site da sua empresa Replicon como administrador.

2. Para configurar SAML 2.0, execute os seguintes passos:

    ![Ativar a autenticação SAML](./media/replicon-tutorial/ic777805.png "autenticação ativar SAML")

    a. Para apresentar os **EnableSAML Authentication2** caixa de diálogo, anexe o seguinte ao seu URL, após a sua chave de acesso da empresa: `/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`

    * O código a seguir mostra o esquema do URL completa: `https://na2.replicon.com/\<YourCompanyKey\>/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`

   b. Clique nas **+** para expandir a **v20Configuration** secção.

   c. Clique nas **+** para expandir a **metaDataConfiguration** secção.

   d. Selecione **SHA256** para xmlSignatureAlgorithm

   e. Clique em **Escolher ficheiro**para selecionar o ficheiro XML de metadados de fornecedor de identidade e clique em **submeter**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta secção, irá criar um utilizador de teste no portal do Azure chamado B.Simon.

1. No painel à esquerda no portal do Azure, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.
1. Selecione **novo utilizador** na parte superior do ecrã.
1. Na **utilizador** propriedades, siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. Na **nome de utilizador** , insira o username@companydomain.extension. Por exemplo, `BrittaSimon@contoso.com`.
   1. Selecione o **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na **palavra-passe** caixa.
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, irá ativar B.Simon utilizar o Azure início de sessão único ao conceder acesso para Replicon.

1. No portal do Azure, selecione **aplicações empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicações, selecione **Replicon**.
1. Na página de descrição geral da aplicação, localize a **Manage** secção e selecione **utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **adicionar utilizador**, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![A ligação de adicionar utilizador](common/add-assign-user.png)

1. Na **utilizadores e grupos** caixa de diálogo, selecione **B.Simon** a partir da lista de utilizadores, em seguida, clique nas **selecione** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na asserção de SAML, no **selecionar função** caixa de diálogo, selecione a função adequada para o utilizador a partir da lista e, em seguida, clique nas **selecione** na parte inferior do ecrã.
1. Na **adicionar atribuição** caixa de diálogo, clique nas **atribuir** botão.

### <a name="create-replicon-test-user"></a>Criar utilizador de teste Replicon

O objetivo desta secção é criar um usuário chamado B.Simon no Replicon.

**Se precisar de criar manualmente o utilizador, execute os seguintes passos:**

1. Numa janela do browser web, inicie sessão no site da sua empresa Replicon como administrador.

2. Aceda a **Administration \> utilizadores**.

    ![Os utilizadores](./media/replicon-tutorial/ic777806.png "utilizadores")

3. Clique em **+ adicionar utilizador**.

    ![Adicionar utilizador](./media/replicon-tutorial/ic777807.png "adicionar utilizador")

4. Na **perfil de utilizador** secção, execute os seguintes passos:

    ![Perfil de usuário](./media/replicon-tutorial/ic777808.png "perfil de utilizador")

    a. Na **nome de início de sessão** caixa de texto, introduzir o Azure AD o endereço de e-mail do utilizador do Azure AD que pretende aprovisionar como `B.Simon@contoso.com`.

    > [!NOTE]
    > Nome de início de sessão tem de corresponder ao endereço de e-mail do utilizador no Azure AD

    b. Como **tipo de autenticação**, selecione **SSO**.

    c. ID do conjunto de autenticação para o mesmo valor que o nome de início de sessão (o endereço de e-mail do Azure AD do utilizador)

    d. Na **departamento** caixa de texto, digite departamento do utilizador.

    e. Como **tipo de funcionário**, selecione **administrador**.

    f. Clique em **guardar o perfil de utilizador**.

> [!NOTE]
> Pode utilizar quaisquer outras Replicon utilizador conta criação ferramentas ou APIs fornecidas pelo Replicon para aprovisionar contas de utilizador do Azure AD.

### <a name="test-sso"></a>Teste SSO

Ao selecionar o mosaico Replicon no painel de acesso, deve ser automaticamente sessão iniciada no Replicon para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)