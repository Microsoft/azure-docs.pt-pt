---
title: 'Tutorial: Integração do Active Directory do Azure com OpsGenie | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e OpsGenie.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 41b59b22-a61d-4fe6-ab0d-6c3991d1375f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 48922e0165aa8f5f418c222aab29ff8c100f2745
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58076433"
---
# <a name="tutorial-azure-active-directory-integration-with-opsgenie"></a>Tutorial: Integração do Active Directory do Azure com OpsGenie

Neste tutorial, saiba como integrar OpsGenie com o Azure Active Directory (Azure AD).
Integrar OpsGenie no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao OpsGenie.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para OpsGenie (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com OpsGenie, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* OpsGenie logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta OpsGenie **SP** iniciada SSO

## <a name="adding-opsgenie-from-the-gallery"></a>Adicionando OpsGenie da Galeria

Para configurar a integração do OpsGenie com o Azure AD, terá de adicionar OpsGenie a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar OpsGenie a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **OpsGenie**, selecione **OpsGenie** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![OpsGenie na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com OpsGenie com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no OpsGenie deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com OpsGenie, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar OpsGenie Single Sign-On](#configure-opsgenie-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste OpsGenie](#create-opsgenie-test-user)**  - para ter um equivalente da Eduarda Almeida na OpsGenie que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com OpsGenie, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **OpsGenie** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![OpsGenie domínio e URLs únicas início de sessão em informações](common/sp-signonurl.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL:  `https://app.opsgenie.com/auth/login`

5. No **definir a segurança de início de sessão único com o SAML** na página a **certificado de assinatura SAML** secção, clique em botão Copiar para copiar **Url de metadados de Federação de aplicação** e guarde-o no seu computador.

    ![O link de download de certificado](common/copy-metadataurl.png)

6. Sobre o **configurar OpsGenie** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-opsgenie-single-sign-on"></a>Configurar OpsGenie Single Sign-On

1. Abra outra instância do browser e, em seguida, iniciar sessão na OpsGenie como administrador.

2. Clique em **configurações**e, em seguida, clique nas **início de sessão único** separador.
   
    ![OpsGenie Single Sign-On](./media/opsgenie-tutorial/tutorial_opsgenie_06.png)

3. Para ativar o SSO, selecione **ativado**.
   
    ![Definições de OpsGenie](./media/opsgenie-tutorial/tutorial_opsgenie_07.png) 

4. Na **fornecedor** secção, clique nas **Azure Active Directory** separador.
   
    ![Definições de OpsGenie](./media/opsgenie-tutorial/tutorial_opsgenie_08.png) 

5. Na página de diálogo do Azure Active Directory, execute os seguintes passos:
   
    ![Definições de OpsGenie](./media/opsgenie-tutorial/tutorial_opsgenie_09.png)
    
    a. Na **SAML 2.0 Endpoint** caixa de texto, colar **URL de início de sessão**valor que copiou do portal do Azure.
    
    b. Na **Url de metadados:** caixa de texto, colar **Url de metadados de Federação de aplicação** valor que copiou do portal do Azure.
    
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
  
    b. Na **nome de utilizador** tipo de campo **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para OpsGenie.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **OpsGenie**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **OpsGenie**.

    ![A ligação de OpsGenie na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-opsgenie-test-user"></a>Criar utilizador de teste OpsGenie

O objetivo desta secção é criar um usuário chamado Eduarda Almeida no OpsGenie. 

1. Numa janela do browser web, inicie sessão no seu inquilino OpsGenie como administrador.

2. Navegue até à lista de utilizadores clicando **utilizadores** no painel esquerdo.
   
    ![Definições de OpsGenie](./media/opsgenie-tutorial/tutorial_opsgenie_10.png) 

3. Clique em **adicionar utilizador**.

4. Sobre o **adicionar utilizador** caixa de diálogo, execute os seguintes passos:
   
    ![Definições de OpsGenie](./media/opsgenie-tutorial/tutorial_opsgenie_11.png)
   
    a. Na **E-Mail** caixa de texto, o endereço de e-mail typu BrittaSimon resolvidos no Azure Active Directory.
   
    b. Na **FullName** caixa de texto, tipo **Eduarda Almeida**.
   
    c. Clique em **Guardar**. 

>[!NOTE]
>Eduarda obtém uma mensagem de e-mail com instruções sobre como configurar o seu perfil.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico OpsGenie no painel de acesso, deve ser automaticamente sessão iniciada no OpsGenie para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [ Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

