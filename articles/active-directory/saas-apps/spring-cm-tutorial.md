---
title: 'Tutorial: Integração do Active Directory do Azure com SpringCM | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e SpringCM.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4a42f797-ac58-4aca-a8e6-53bfe5529083
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04-08-2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 04ddc09d3d2475afe9be90cf1d5585697b9c36cf
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/15/2019
ms.locfileid: "59565585"
---
# <a name="tutorial-azure-active-directory-integration-with-springcm"></a>Tutorial: Integração do Active Directory do Azure com SpringCM

Neste tutorial, saiba como integrar SpringCM com o Azure Active Directory (Azure AD).
Integrar SpringCM no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao SpringCM.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para SpringCM (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com SpringCM, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter um [conta gratuita](https://azure.microsoft.com/free/)
* SpringCM logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta SpringCM **SP** iniciada SSO

## <a name="adding-springcm-from-the-gallery"></a>Adicionando SpringCM da Galeria

Para configurar a integração do SpringCM com o Azure AD, terá de adicionar SpringCM a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar SpringCM a partir da galeria, execute os seguintes passos:**

1. Na  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique nas **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, clique a **nova aplicação** botão na parte superior da caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **SpringCM**, selecione **SpringCM** a partir do painel de resultados, em seguida, clique nas **Add** botão para adicionar a aplicação.

    ![SpringCM na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com SpringCM com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no SpringCM deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com SpringCM, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar SpringCM Single Sign-On](#configure-springcm-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste SpringCM](#create-springcm-test-user)**  - para ter um equivalente da Eduarda Almeida na SpringCM que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com SpringCM, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **SpringCM** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![SpringCM domínio e URLs únicas início de sessão em informações](common/sp-signonurl.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão:  `https://na11.springcm.com/atlas/SSO/SSOEndpoint.ashx?aid=<identifier>`

    > [!NOTE]
    > O valor não é real. Atualize o valor com o URL de início de sessão real. Contacte [equipa de suporte de cliente SpringCM](https://knowledge.springcm.com/support) para obter o valor. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

4. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (bruto)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificateraw.png)

6. Sobre o **configurar SpringCM** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-springcm-single-sign-on"></a>Configurar SpringCM Single Sign-On

1. Numa janela do browser web diferente, inicie sessão no seu **SpringCM** site da empresa como administrador.

1. No menu na parte superior, clique em **Ir para**, clique em **preferências**e, em seguida, no **preferências de conta** secção, clique em **SAML SSO**.
   
    ![SAML SSO](./media/spring-cm-tutorial/ic797051.png "SAML SSO")

1. Na secção de configuração do fornecedor de identidade, execute os seguintes passos:
   
    ![Configuração do fornecedor de identidade](./media/spring-cm-tutorial/ic797052.png "configuração do fornecedor de identidade")
    
    a. Para carregar o certificado transferido do Azure Active Directory, clique em **selecionar o certificado de emissor** ou **certificados de emissor de alteração**.
    
    b. Na **emissor** caixa de texto, colar **do Azure AD identificador** valor, que copiou do portal do Azure.
    
    c. Na **ponto de extremidade do fornecedor de serviços (SP) iniciada** caixa de texto, colar **URL de início de sessão** valor, que copiou do portal do Azure.
            
    d. Selecione **SAML ativada** como **ativar**.

    e. Clique em **Guardar**.

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para SpringCM.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **SpringCM**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **SpringCM**.

    ![A ligação de SpringCM na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-springcm-test-user"></a>Criar utilizador de teste SpringCM

Para que os utilizadores do Azure Active Directory iniciar sessão no SpringCM, tem de ser aprovisionados em SpringCM. No caso de SpringCM, o aprovisionamento é uma tarefa manual.

> [!NOTE]
> Para obter mais informações, consulte [criar e editar um utilizador SpringCM](https://knowledge.springcm.com/create-and-edit-a-springcm-user). 

**Para Aprovisionar uma conta de utilizador para SpringCM, execute os seguintes passos:**

1. Inicie sessão no seu **SpringCM** site da empresa como administrador.

1. Clique em **GOTO**e, em seguida, clique em **livro de endereços**.
   
    ![Criar utilizador](./media/spring-cm-tutorial/ic797054.png "criar utilizador")

1. Clique em **criar utilizador**.

1. Selecione um **função de utilizador**.

1. Selecione **Enviar E-Mail de ativação**.

1. Escreva o nome próprio, apelido e endereço de e-mail de uma conta de utilizador válido do Azure Active Directory que pretende aprovisionar em caixas de texto relacionadas.

1. Adicione o utilizador para um **grupo de segurança**.

1. Clique em **Guardar**.

   > [!NOTE]
   > Pode utilizar quaisquer outras SpringCM utilizador conta criação ferramentas ou APIs fornecidas pelo SpringCM para aprovisionar contas de utilizador do AAD.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico SpringCM no painel de acesso, deve ser automaticamente sessão iniciada no SpringCM para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

