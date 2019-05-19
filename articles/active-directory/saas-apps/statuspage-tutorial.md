---
title: 'Tutorial: Integração do Active Directory do Azure com StatusPage | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e StatusPage.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f6ee8bb3-df43-4c0d-bf84-89f18deac4b9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/22/2019
ms.author: jeedes
ms.openlocfilehash: 49f77da5843bc2438ea7f82475b4faf753b66f09
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "65866706"
---
# <a name="tutorial-azure-active-directory-integration-with-statuspage"></a>Tutorial: Integração do Active Directory do Azure com StatusPage

Neste tutorial, saiba como integrar StatusPage com o Azure Active Directory (Azure AD).
Integrar StatusPage no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao StatusPage.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para StatusPage (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com StatusPage, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter um [conta gratuita](https://azure.microsoft.com/free/)
* StatusPage logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta StatusPage **IDP** iniciada SSO

## <a name="adding-statuspage-from-the-gallery"></a>Adicionando StatusPage da Galeria

Para configurar a integração do StatusPage com o Azure AD, terá de adicionar StatusPage a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar StatusPage a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **StatusPage**, selecione **StatusPage** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![StatusPage na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com StatusPage com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no StatusPage deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com StatusPage, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar StatusPage Single Sign-On](#configure-statuspage-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste StatusPage](#create-statuspage-test-user)**  - para ter um equivalente da Eduarda Almeida na StatusPage que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com StatusPage, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **StatusPage** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **definir a segurança de início de sessão único com o SAML** página, execute os seguintes passos:

    ![StatusPage domínio e URLs únicas início de sessão em informações](common/idp-intiated.png)

    a. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão:
    
    | |
    |--|
    | `https://<subdomain>.statuspagestaging.com/`|
    | `https://<subdomain>.statuspage.io/`|

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão:
    
    | |
    |--|
    | `https://<subdomain>.statuspagestaging.com/sso/saml/consume`|
    | `https://<subdomain>.statuspage.io/sso/saml/consume`|

    > [!NOTE]
    > Contacte a equipa de suporte de StatusPage em [ SupportTeam@statuspage.io ](mailto:SupportTeam@statuspage.io)solicite os metadados necessários para configurar o início de sessão único. 
    >
    > a. Dos metadados, copie o valor de emissor e, em seguida, cole-o para o **identificador** caixa de texto.
    >
    > b. Dos metadados, copie o URL de resposta e, em seguida, cole-o para o **URL de resposta** caixa de texto.

5. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

6. Sobre o **configurar StatusPage** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-statuspage-single-sign-on"></a>Configurar StatusPage Single Sign-On

1. Na outra janela do browser, inicie sessão no site da sua empresa StatusPage como um administrador.

1. Na barra de ferramentas principal, clique em **gerir conta**.

    ![Configurar o início de sessão único](./media/statuspage-tutorial/tutorial_statuspage_06.png)

1. Clique nas **início de sessão único** separador.

    ![Configurar o início de sessão único](./media/statuspage-tutorial/tutorial_statuspage_07.png)

1. Na página de configuração de SSO, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/statuspage-tutorial/tutorial_statuspage_08.png)

    ![Configurar o início de sessão único](./media/statuspage-tutorial/tutorial_statuspage_09.png)

    a. Na **URL de destino de SSO** caixa de texto, cole o valor de **URL de início de sessão**, que copiou do portal do Azure.

    b. Abra o seu certificado transferido no bloco de notas, copiar o conteúdo e, em seguida, cole-o para o **certificado** caixa de texto.

    c. Clique em **Guardar configuração**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** campo introduza **BrittaSimon**.
  
    b. Na **nome de utilizador** tipo de campo `brittasimon@yourcompanydomain.extension`  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para StatusPage.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **StatusPage**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **StatusPage**.

    ![A ligação de StatusPage na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-statuspage-test-user"></a>Criar utilizador de teste StatusPage

O objetivo desta secção é criar um usuário chamado Eduarda Almeida no StatusPage.

StatusPage suporta o aprovisionamento de just-in-time. Já ativou-lo na [configurar o Azure AD Single Sign-On](#configure-azure-ad-single-sign-on).

**Para criar um usuário chamado Eduarda Almeida no StatusPage, execute os seguintes passos:**

1. Início de sessão no site da sua empresa StatusPage como administrador.

1. No menu na parte superior, clique em **gerir conta**.

    ![Configurar o início de sessão único](./media/statuspage-tutorial/tutorial_statuspage_06.png)

1. Clique nas **os integrantes da Equipe** separador.
  
    ![Criar um utilizador de teste do Azure AD](./media/statuspage-tutorial/tutorial_statuspage_10.png) 

1. Clique em **Adicionar membro da EQUIPE**.
  
    ![Criar um utilizador de teste do Azure AD](./media/statuspage-tutorial/tutorial_statuspage_11.png) 

1. Tipo de **endereço de E-Mail**, **nome próprio**, e **Apelido** de um utilizador válido que pretende aprovisionar em caixas de texto relacionadas. 

    ![Criar um utilizador de teste do Azure AD](./media/statuspage-tutorial/tutorial_statuspage_12.png) 

1. Como **função**, escolha **administrador Client**.

1. Clique em **criar conta**.

### <a name="test-single-sign-on"></a>Testar início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico StatusPage no painel de acesso, deve ser automaticamente sessão iniciada no StatusPage para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
