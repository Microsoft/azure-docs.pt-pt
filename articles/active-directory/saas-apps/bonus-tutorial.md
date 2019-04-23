---
title: 'Tutorial: Integração do Active Directory do Azure com Bonusly | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Bonusly.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 29fea32a-fa20-47b2-9e24-26feb47b0ae6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ffc2e8c186ce485f7df19b3f797aaa8982735f5
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2019
ms.locfileid: "60003960"
---
# <a name="tutorial-azure-active-directory-integration-with-bonusly"></a>Tutorial: Integração do Active Directory do Azure com Bonusly

Neste tutorial, saiba como integrar Bonusly com o Azure Active Directory (Azure AD).
Integrar Bonusly no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Bonusly.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para Bonusly (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Bonusly, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter um [conta gratuita](https://azure.microsoft.com/free/)
* Bonusly logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta bonusly **IDP** iniciada SSO

## <a name="adding-bonusly-from-the-gallery"></a>Adicionando Bonusly da Galeria

Para configurar a integração do Bonusly com o Azure AD, terá de adicionar Bonusly a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Bonusly a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Bonusly**, selecione **Bonusly** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Bonusly na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Bonusly com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Bonusly deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Bonusly, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Bonusly início de sessão único](#configure-bonusly-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste Bonusly](#create-bonusly-test-user)**  - para ter um equivalente da Eduarda Almeida na Bonusly que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com Bonusly, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Bonusly** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![Bonusly URLs de domínio e única informações de início de sessão](common/idp-reply.png)

    Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão:  `https://Bonus.ly/saml/<tenant-name>`

    > [!NOTE]
    > O valor não é real. Atualize o valor com o URL de resposta real. Contacte [equipa de suporte de cliente Bonusly](https://bonus.ly/contact) para obter o valor. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

5. Na **certificado de assinatura SAML** secção, clique em **editar** botão para abrir **certificado de assinatura SAML** caixa de diálogo.

    ![Editar o certificado de assinatura de SAML](common/edit-certificate.png)

6. Na **certificado de assinatura SAML** secção, copie a **THUMBPRINT** e guarde-o no seu computador.

    ![Copie o valor do Thumbprint](common/copy-thumbprint.png)

7. Sobre o **configurar Bonusly** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-bonusly-single-sign-on"></a>Configurar Bonusly início de sessão único

1. Numa janela do browser diferente, inicie sessão no seu **Bonusly** inquilino.

1. Na barra de ferramentas na parte superior, clique em **configurações** e, em seguida, selecione **integrações e aplicações**.

    ![Seção Social bonusly](./media/bonus-tutorial/ic773686.png "Bonusly")
1. Sob **início de sessão único**, selecione **SAML**.

1. Sobre o **SAML** caixa de diálogo página, execute os seguintes passos:

    ![Página de diálogo de Saml bonusly](./media/bonus-tutorial/ic773687.png "Bonusly")

    a. Na **URL de destino de SSO de IdP** caixa de texto, cole o valor de **URL de início de sessão**, que copiou do portal do Azure.

    b. Na **URL de início de sessão do IdP** caixa de texto, cole o valor de **URL de início de sessão**, que copiou do portal do Azure.

    c. Na **IdP emissor** caixa de texto, cole o valor de **do Azure AD identificador**, que copiou do portal do Azure.
    
    d. Colar o **Thumbprint** valor copiados a partir do portal do Azure para o **impressão digital do certificado** caixa de texto.
    
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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Bonusly.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Bonusly**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Bonusly**.

    ![O Bonusly ligação na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-bonusly-test-user"></a>Criar utilizador de teste Bonusly

Para habilitar os utilizadores do Azure AD iniciar sessão no Bonusly, tem de ser aprovisionados em Bonusly. No caso de Bonusly, o aprovisionamento é uma tarefa manual.

> [!NOTE]
> Pode utilizar quaisquer outras ferramentas do utilizador Bonusly conta criação ou APIs fornecidas pelo Bonusly para aprovisionar contas de utilizador do AAD. 

**Para configurar o aprovisionamento de utilizadores, execute os seguintes passos:**

1. Numa janela do browser web, inicie sessão no seu inquilino Bonusly.

1. Clique em **definições**.

    ![As definições](./media/bonus-tutorial/ic781041.png "definições")

1. Clique nas **utilizadores e os bônus** separador.

    ![Os utilizadores e os bônus](./media/bonus-tutorial/ic781042.png "utilizadores e os bônus")

1. Clique em **gerir utilizadores**.

    ![Gerir utilizadores](./media/bonus-tutorial/ic781043.png "gerir utilizadores")

1. Clique em **adicionar utilizador**.

    ![Adicionar utilizador](./media/bonus-tutorial/ic781044.png "adicionar utilizador")

1. Sobre o **adicionar utilizador** caixa de diálogo, execute os seguintes passos:

    ![Adicionar utilizador](./media/bonus-tutorial/ic781045.png "adicionar utilizador")  

    a. Na **nome próprio** caixa de texto, introduza o nome de utilizador, como **Eduarda**.

    b. Na **Apelido** caixa de texto, introduza o apelido do utilizador, como **Simon**.

    c. Na **E-Mail** caixa de texto, introduza o e-mail do utilizador, como `brittasimon\@contoso.com`.

    d. Clique em **Guardar**.

    > [!NOTE]
    > O titular da conta do Azure AD recebe uma mensagem de e-mail que inclui uma ligação para confirmar a conta até se tornar Active Directory.  

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no mosaico Bonusly no painel de acesso, deve ter automaticamente sessão iniciada no Bonusly para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
