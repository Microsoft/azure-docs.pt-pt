---
title: 'Tutorial: Integração do Active Directory do Azure com dmarcian | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e dmarcian.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a04b9383-3a60-4d54-9412-123daaddff3b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/30/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c24cbf8ad21c7dd5875a71532a5278e313774e66
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57883972"
---
# <a name="tutorial-azure-active-directory-integration-with-dmarcian"></a>Tutorial: Integração do Active Directory do Azure com dmarcian

Neste tutorial, saiba como integrar dmarcian com o Azure Active Directory (Azure AD).
Integrar dmarcian no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao dmarcian.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para dmarcian (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com dmarcian, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* dmarcian logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* suporta dmarcian **SP** e **IDP** iniciada SSO

## <a name="adding-dmarcian-from-the-gallery"></a>Adicionando dmarcian da Galeria

Para configurar a integração do dmarcian com o Azure AD, terá de adicionar dmarcian a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar dmarcian a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **dmarcian**, selecione **dmarcian** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![dmarcian na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com dmarcian com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no dmarcian deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com dmarcian, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar dmarcian início de sessão único](#configure-dmarcian-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste dmarcian](#create-dmarcian-test-user)**  - para ter um equivalente da Eduarda Almeida na dmarcian que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com dmarcian, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **dmarcian** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, se desejar configurar a aplicação no **IDP** iniciada pelo modo, execute os seguintes passos:

    ![dmarcian domínio e URLs únicas início de sessão em informações](common/idp-intiated.png)

    a. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão:

    | |
    | -- |
    | `https://us.dmarcian.com/sso/saml/<ACCOUNT_ID>/sp.xml` |
    | `https://dmarcian-eu.com/sso/saml/<ACCOUNT_ID>/sp.xml` |
    | `https://dmarcian-ap.com/sso/saml/<ACCOUNT_ID>/sp.xml` |

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão:

    | |
    |--|
    | `https://us.dmarcian.com/login/<ACCOUNT_ID>/handle/` |
    | `https://dmarcian-eu.com/login/<ACCOUNT_ID>/handle/` |
    | `https://dmarcian-ap.com/login/<ACCOUNT_ID>/handle/` |

5. Clique em **definir URLs adicionais** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    ![dmarcian domínio e URLs únicas início de sessão em informações](common/metadata-upload-additional-signon.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão:
    
    | |
    |--|
    | `https://us.dmarcian.com/login/<ACCOUNT_ID>` |
    | `https://dmarcian-eu.com/login/<ACCOUNT_ID>` |
    | `https://dmarciam-ap.com/login/<ACCOUNT_ID>` |
     
    > [!NOTE] 
    > Estes valores não são reais. Atualizar estes valores com o identificador real, o URL de resposta e URL de início de sessão que é explicado mais tarde no tutorial. 

6. No **definir a segurança de início de sessão único com o SAML** na página a **certificado de assinatura SAML** secção, clique em botão Copiar para copiar **Url de metadados de Federação de aplicação** e guarde-o no seu computador.

    ![O link de download de certificado](common/copy-metadataurl.png)

### <a name="configure-dmarcian-single-sign-on"></a>Configurar dmarcian início de sessão único

1. Numa janela do browser web diferente, inicie sessão no dmarcian como um administrador de segurança.

2. Clique em **perfil** no canto superior direito canto e navegue até à **preferências**.

    ![As preferências](./media/dmarcian-tutorial/tutorial_dmarcian_pref.png)

3. Desloque para baixo e clique em **início de sessão único** secção, em seguida, clique em **configurar**.

    ![A única](./media/dmarcian-tutorial/tutorial_dmarcian_sso.png)

4. Sobre o **SAML Single Sign-On** página conjunto a **estado** como **ativado** e execute os seguintes passos:

    ![A autenticação](./media/dmarcian-tutorial/tutorial_dmarcian_auth.png)

    * Sob **adicionar dmarcian ao seu fornecedor de identidade** secção, clique em **cópia** para copiar o **URL do serviço de consumidor de asserção** para a sua instância e cole-a no  **URL de resposta** na caixa de texto **secção de configuração básica de SAML** no portal do Azure.

    * Sob **adicionar dmarcian ao seu fornecedor de identidade** secção, clique em **cópia** para copiar o **ID de entidade** para a sua instância e cole-a no **identificador**na caixa de texto **secção de configuração básica de SAML** no portal do Azure.

    * Sob **configurar a autenticação** na secção a **metadados do fornecedor de identidade** colar de caixa de texto a **Url de metadados de Federação de aplicação**, que copiou do portal do Azure.

    * Sob **configurar a autenticação** secção, além do **declarações de atributo** caixa de texto, cole o url `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    * Sob **configurar o URL de início de sessão** secção, copie a **URL de início de sessão** para a sua instância e cole-a no **URL de início de sessão** caixa de texto no **secção de configuração básica de SAML** no portal do Azure.

        > [!Note]
        > Pode modificar os **URL de início de sessão** , de acordo com sua organização.

    * Clique em **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** , insira **BrittaSimon**.
  
    b. Na **nome de utilizador** , digite **brittasimon\@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso a dmarcian.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **dmarcian**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **dmarcian**.

    ![A ligação de dmarcian na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** caixa de diálogo, clique nas **atribuir** botão.

### <a name="create-dmarcian-test-user"></a>Criar utilizador de teste dmarcian

Para ativar a utilizadores do Azure AD iniciar sessão no dmarcian, tem de ser aprovisionados em dmarcian. Dmarcian, aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no dmarcian como um administrador de segurança.

2. Clique em **perfil** sobre o canto direito superior e navegue até à **Manage Users**.

    ![O utilizador](./media/dmarcian-tutorial/tutorial_dmarcian_user.png)

3. No lado direito da **utilizadores SSO** secção, clique em **adicionar novo utilizador**.

    ![O adicionar utilizador](./media/dmarcian-tutorial/tutorial_dmarcian_addnewuser.png)

4. Sobre o **adicionar novo utilizador** pop-up, execute os seguintes passos:

    ![O novo utilizador](./media/dmarcian-tutorial/tutorial_dmarcian_save.png)

    a. Na **novo E-Mail do utilizador** caixa de texto, introduza o e-mail do utilizador, como **brittasimon\@contoso.com**.

    b. Se pretende conceder direitos de administrador para o utilizador, selecione **fazer com que o utilizador seja um administrador**.

    c. Clique em **adicionar utilizador**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico dmarcian no painel de acesso, deve ser automaticamente sessão iniciada no dmarcian para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

