---
title: 'Tutorial: Integração do Active Directory do Azure com Picturepark | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Picturepark.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 31c21cd4-9c00-4cad-9538-a13996dc872f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/18/2019
ms.author: jeedes
ms.openlocfilehash: 617c75024b45dab7ff2466b99bfb71c18cdd778a
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2019
ms.locfileid: "65904582"
---
# <a name="tutorial-azure-active-directory-integration-with-picturepark"></a>Tutorial: Integração do Active Directory do Azure com Picturepark

Neste tutorial, saiba como integrar Picturepark com o Azure Active Directory (Azure AD).
Integrar Picturepark no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Picturepark.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para Picturepark (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Picturepark, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter um [conta gratuita](https://azure.microsoft.com/free/)
* Picturepark logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta Picturepark **SP** iniciada SSO

## <a name="adding-picturepark-from-the-gallery"></a>Adicionando Picturepark da Galeria

Para configurar a integração do Picturepark com o Azure AD, terá de adicionar Picturepark a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Picturepark a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Picturepark**, selecione **Picturepark** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Picturepark na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Picturepark com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Picturepark deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Picturepark, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Picturepark Single Sign-On](#configure-picturepark-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste Picturepark](#create-picturepark-test-user)**  - para ter um equivalente da Eduarda Almeida na Picturepark que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com Picturepark, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Picturepark** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![Picturepark domínio e URLs únicas início de sessão em informações](common/sp-identifier.png)

    a. Na **iniciar sessão no URL** caixa de texto, escreva um URL com o seguinte padrão: `https://<companyname>.picturepark.com`

    b. Na **identificador (ID de entidade)** caixa de texto, escreva um URL com o seguinte padrão:

    |  |
    |--|
    | `https://<companyname>.current-picturepark.com`|
    | `https://<companyname>.picturepark.com`|
    | `https://<companyname>.next-picturepark.com`|
    | |

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o início de sessão real URL e o identificador. Contacte [equipa de suporte de cliente Picturepark](https://picturepark.com/about/contact/) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

5. Na **certificado de assinatura SAML** secção, clique em **editar** botão para abrir **certificado de assinatura SAML** caixa de diálogo.

    ![Editar o certificado de assinatura de SAML](common/edit-certificate.png)

6. Na **certificado de assinatura SAML** secção, copie a **Thumbprint** e guarde-o no seu computador.

    ![Copie o valor do Thumbprint](common/copy-thumbprint.png)

7. Sobre o **configurar Picturepark** secção, copie os URLs apropriados de acordo com seus requisitos. Para **URL de início de sessão**, utilize o valor com o seguinte padrão: `https://login.microsoftonline.com/_my_directory_id_/wsfed`

    > [!Note]
    > _my_directory_id_ é a id de inquilino da subscrição do Azure AD.

    ![URLs de configuração de cópia](./media/picturepark-tutorial/configurls.png)

    a. Identificador do Azure AD

    b. URL de fim de sessão

### <a name="configure-picturepark-single-sign-on"></a>Configurar Picturepark Single Sign-On

1. Numa janela do browser web diferente, inicie sessão no site da sua empresa Picturepark como administrador.

2. Na barra de ferramentas na parte superior, clique em **ferramentas administrativas**e, em seguida, clique em **Console de gerenciamento**.
   
    ![Console de gerenciamento](./media/picturepark-tutorial/ic795062.png "consola de gestão")

3. Clique em **autenticação**e, em seguida, clique em **fornecedores de identidade**.
   
    ![Autenticação](./media/picturepark-tutorial/ic795063.png "autenticação")

4. Na **configuração do fornecedor de identidade** secção, execute os seguintes passos:
   
    ![Configuração do fornecedor de identidade](./media/picturepark-tutorial/ic795064.png "configuração do fornecedor de identidade")
   
    a. Clique em **Adicionar**.
  
    b. Escreva um nome para a sua configuração.
   
    c. Selecione **predefinir**.
   
    d. Na **URI de emissor** caixa de texto, cole o valor de **URL de início de sessão** que copiou do portal do Azure.
   
    e. Na **impressão de Thumb de emissor fidedigno** caixa de texto, cole o valor de **Thumbprint** que copiou do **certificado de assinatura SAML** secção. 

5. Clique em **JoinDefaultUsersGroup**.

6. Para definir o **Emailaddress** atributo a **afirmação** caixa de texto, tipo `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` e clique em **guardar**.

      ![Configuração](./media/picturepark-tutorial/ic795065.png "configuração")

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Picturepark.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Picturepark**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Picturepark**.

    ![A ligação de Picturepark na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-picturepark-test-user"></a>Criar utilizador de teste Picturepark

Para habilitar os utilizadores do Azure AD iniciar sessão em Picturepark, tem de ser aprovisionados em Picturepark. No caso de Picturepark, o aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no seu **Picturepark** inquilino.

1. Na barra de ferramentas na parte superior, clique em **ferramentas administrativas**e, em seguida, clique em **utilizadores**.
   
    ![Os utilizadores](./media/picturepark-tutorial/ic795067.png "utilizadores")

1. Na **descrição geral de utilizadores** separador, clique em **New**.
   
    ![Gestão de utilizadores](./media/picturepark-tutorial/ic795068.png "gestão de utilizadores")

1. Sobre o **criar utilizador** caixa de diálogo, execute os seguintes passos de um utilizador válido do Azure Active Directory Directory que pretende aprovisionar:
   
    ![Criar utilizador](./media/picturepark-tutorial/ic795069.png "criar utilizador")
   
    a. Na **endereço de E-Mail** caixa de texto, tipo a **endereço de e-mail** do utilizador `BrittaSimon@contoso.com`.  
   
    b. Na **palavra-passe** e **Confirmar palavra-passe** caixas de texto, tipo o **palavra-passe** de BrittaSimon. 
   
    c. Na **nome próprio** caixa de texto, tipo a **nome próprio** do utilizador **Eduarda**. 
   
    d. Na **Apelido** caixa de texto, tipo a **Apelido** do utilizador **Simon**.
   
    e. Na **empresa** caixa de texto, tipo a **nome da empresa** do utilizador. 
   
    f. Na **país** caixa de texto, selecione a **país/região** do utilizador.
  
    g. Na **ZIP** caixa de texto, tipo a **CEP** da cidade.
   
    h. Na **Cidade** caixa de texto, tipo a **nome da cidade** do utilizador.

    i. Selecione um **linguagem**.
   
    j. Clique em **Criar**.

>[!NOTE]
>Pode utilizar quaisquer outras Picturepark utilizador conta criação ferramentas ou APIs fornecidas pelo Picturepark para aprovisionar contas de utilizador do Azure AD.
>

### <a name="test-single-sign-on"></a>Testar início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Picturepark no painel de acesso, deve ser automaticamente sessão iniciada no Picturepark para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

