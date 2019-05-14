---
title: 'Tutorial: Integração do Active Directory do Azure com BlueJeans | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e BlueJeans.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: dfc634fd-1b55-4ba8-94a8-b8288429b6a9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2830e95f042d5c83d91d6c026d2fa2e70eb3e3b2
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/14/2019
ms.locfileid: "65601636"
---
# <a name="tutorial-azure-active-directory-integration-with-bluejeans"></a>Tutorial: Integração do Active Directory do Azure com BlueJeans

Neste tutorial, saiba como integrar BlueJeans com o Azure Active Directory (Azure AD).
Integrar BlueJeans no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao BlueJeans.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para BlueJeans (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com BlueJeans, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter um [conta gratuita](https://azure.microsoft.com/free/)
* BlueJeans único início de sessão na subscrição ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta blueJeans **SP** iniciada SSO

* Suporta blueJeans [ **automatizada** aprovisionamento de utilizadores](bluejeans-provisioning-tutorial.md)

## <a name="adding-bluejeans-from-the-gallery"></a>Adicionando BlueJeans da Galeria

Para configurar a integração do BlueJeans com o Azure AD, terá de adicionar BlueJeans a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar BlueJeans a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **BlueJeans**, selecione **BlueJeans** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![BlueJeans na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com BlueJeans com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no BlueJeans deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com BlueJeans, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar BlueJeans Single Sign-On](#configure-bluejeans-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste BlueJeans](#create-bluejeans-test-user)**  - para ter um equivalente da Eduarda Almeida na BlueJeans que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com BlueJeans, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **BlueJeans** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](media/bluejeans-tutorial/edit-urls-bluejeans.png)

4. Na **configuração básica de SAML** caixa de diálogo, introduza os seguintes valores:

    ![BlueJeans domínio e URLs únicas início de sessão em informações](media/bluejeans-tutorial/tutorial_bluejeans-basic-configuration.png)

   - Na **identificador** texto, escreva o seguinte: `http://samlsp.bluejeans.com`
    
   - Na **URL de início de sessão** caixa de texto, escreva o URL da página de aterrissagem fornecido pela BlueJeans (para obter este valor, pode contactar o [equipa de suporte de cliente BlueJeans](https://support.bluejeans.com/contact)): `https://<companyname>.bluejeans.com`
    
   - Clique em **Guardar**.

5. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

6. Sobre o **configurar BlueJeans** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-bluejeans-single-sign-on"></a>Configurar BlueJeans início de sessão único

1. Numa janela do browser web diferente, inicie sessão no seu **BlueJeans** site da empresa como administrador.

2. Aceda a **administrador \> definições de grupo \> segurança**.

    ![Admin](./media/bluejeans-tutorial/ic785868.png "Admin")

3. Na **segurança** secção, execute os seguintes passos:

    ![SAML início de sessão único](./media/bluejeans-tutorial/ic785869.png "SAML início de sessão único")

    a. Selecione **SAML início de sessão único**.

    b. Selecione **ativar o aprovisionamento automático**.

4. Avançar com os seguintes passos:

    ![Caminho de certificado](./media/bluejeans-tutorial/ic785870.png "caminho de certificado")

    a. Clique em **Escolher ficheiro**, para carregar o certificado com codificação base 64 que transferiu do portal do Azure.

    b. Na **URL de início de sessão** caixa de texto, cole o valor de **URL de início de sessão** que copiou do portal do Azure.

    c. Na **URL de alteração de palavra-passe** caixa de texto, cole o valor de **alterar o URL da palavra-passe** que copiou do portal do Azure.

    d. Na **URL de fim de sessão** caixa de texto, cole o valor de **URL de fim de sessão** que copiou do portal do Azure.

5. Avançar com os seguintes passos:

    ![Guardar alterações](./media/bluejeans-tutorial/ic785874.png "guardar alterações")

    a. Na **Id de utilizador** caixa de texto, tipo `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    b. Na **E-Mail** caixa de texto, tipo `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    c. Clique em **guardar alterações**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** campo introduza **BrittaSimon**.
  
    b. Na **nome de utilizador** tipo de campo `brittasimon\@yourcompanydomain.extension`. Por exemplo, BrittaSimon@contoso.com.

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para BlueJeans.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **BlueJeans**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **BlueJeans**.

    ![A ligação de BlueJeans na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-bluejeans-test-user"></a>Criar utilizador de teste BlueJeans

O objetivo desta secção é criar um usuário chamado Eduarda Almeida no BlueJeans. BlueJeans suporta o aprovisionamento automático de utilizadores, que está por predefinição, ativada. Pode encontrar mais detalhes [aqui](bluejeans-provisioning-tutorial.md) sobre como configurar o aprovisionamento automático de utilizadores.

**Se precisar de criar manualmente o utilizador, execute os seguintes passos:**

1. Inicie sessão no seu **BlueJeans** site da empresa como administrador.

2. Aceda a **administrador \> GERIR utilizadores \> adicionar utilizador**.

    ![Admin](./media/bluejeans-tutorial/ic785877.png "Admin")

    > [!IMPORTANT]
    > O **adicionar utilizador** separador apenas está disponível se, no **guia de segurança**, **ativar o aprovisionamento automático** está desmarcada.

3. Na **adicionar utilizador** secção, execute os seguintes passos:

    ![Adicionar utilizador](./media/bluejeans-tutorial/ic785886.png "adicionar utilizador")

    a. Na **nome próprio** texto, introduza o nome de utilizador, como **Eduarda**.

    b. Na **sobrenome** texto, digite o apelido do utilizador, como **Simon**.

    c. Na **escolher um nome de utilizador BlueJeans** texto, introduza o nome de utilizador do utilizador, como **Brittasimon**

    d. Na **criar uma palavra-passe** texto, introduza a palavra-passe.

    e. Na **empresa** texto, introduza a sua empresa.

    f. Na **endereço de E-Mail** texto, introduza o e-mail do utilizador, como `brittasimon\@contoso.com`.

    g. Na **criar um I.D de reunião BlueJeans** texto, introduza o ID de reunião.

    h. Na **escolher um código de acesso do moderador** texto, introduza o código de acesso.

    i. Clique em **continuar**.

    ![Adicionar utilizador](./media/bluejeans-tutorial/ic785887.png "adicionar utilizador")

    J. Clique em **adicionar utilizador**.

> [!NOTE]
> Pode utilizar quaisquer outras BlueJeans utilizador conta criação ferramentas ou APIs fornecidas pelo BlueJeans para aprovisionar contas de utilizador do Azure AD.

### <a name="test-single-sign-on"></a>Testar início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico BlueJeans no painel de acesso, deve ser automaticamente sessão iniciada no BlueJeans para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
