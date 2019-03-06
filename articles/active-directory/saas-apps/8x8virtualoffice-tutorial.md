---
title: 'Tutorial: Integração do Active Directory do Azure com o Office de Virtual de 8 x 8 | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory diretório e 8 x 8 Virtual Office.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: b34a6edf-e745-4aec-b0b2-7337473d64c5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 70a8704fc27aad114d9a1d251cca773d9d0b767a
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57454886"
---
# <a name="tutorial-azure-active-directory-integration-with-8x8-virtual-office"></a>Tutorial: Integração do Active Directory do Azure com o Office de Virtual de 8 x 8

Neste tutorial, saiba como integrar o Office de Virtual de 8 x 8 com o Azure Active Directory (Azure AD).
Integração de 8 x 8 Office Virtual com o Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Office de Virtual de 8 x 8.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para o Office de Virtual de 8 x 8 (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Office de Virtual de 8 x 8, precisa do seguinte:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* 8 x 8 Virtual Office-início de sessão único de subscrição ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.


* suporta o escritório Virtual 8 x 8 **IDP** iniciada SSO

* suporta o escritório Virtual 8 x 8 **Just In Time** aprovisionamento de utilizadores

## <a name="adding-8x8-virtual-office-from-the-gallery"></a>Adicionar 8 x 8 Virtual Office a partir da Galeria

Para configurar a integração do Office de Virtual de 8 x 8 com o Azure AD, terá de adicionar 8 x 8 Virtual Office a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar 8 x 8 Virtual Office a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **8 x 8 Virtual Office**, selecione **8 x 8 Virtual Office** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Office de Virtual de 8 x 8 na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configurar e testar o Azure AD início de sessão único com o escritório Virtual com base num utilizador de teste de 8 x 8 **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado 8 x 8 Virtual Office deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com o Office de Virtual de 8 x 8, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar 8 x 8 Virtual Office início de sessão único](#configure-8x8-virtual-office-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste do Virtual Office 8 x 8](#create-8x8-virtual-office-test-user)**  - para ter um equivalente da Eduarda Almeida no escritório de Virtual de 8 x 8 que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com o Office de Virtual de 8 x 8, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **8 x 8 Virtual Office** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** caixa de diálogo, execute os seguintes passos:

    ![8 x 8 URLs de domínio Virtual do Office e únicas início de sessão em informações](common/idp-intiated.png)

    a. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://sso.8x8.com/saml2`

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://sso.8x8.com/saml2`

4. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (bruto)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificateraw.png)

6. Sobre o **configurar o Office de Virtual de 8 x 8** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Azure Ad Identifier

    c. URL de fim de sessão

### <a name="configure-8x8-virtual-office-single-sign-on"></a>Configurar 8 x 8 Virtual Office início de sessão único

7. Início de sessão no seu inquilino do Virtual Office 8 x 8 como administrador.

8. Selecione **Gestor de conta do Virtual Office** no painel de aplicação.

    ![Configurar no lado de aplicação](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_001.png)

9. Selecione **Business** conta para gerir e clique em **sessão** botão.

    ![Configurar no lado de aplicação](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_002.png)

10. Clique em **contas** guia na lista de menu.

    ![Configurar no lado de aplicação](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_003.png)

11. Clique em **início de sessão único** na lista de contas.
  
    ![Configurar no lado de aplicação](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_004.png)

12. Selecione **início de sessão único** sob métodos de autenticação e clique em **SAML**.

    ![Configurar no lado de aplicação](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_005.png)

13. Na **SAML início de sessão único** secção, execute os seguintes passos:

    ![Configurar no lado de aplicação](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_006.png)

    a. Na **URL de início de sessão** caixa de texto, colar **URL de início de sessão** valor que copiou do portal do Azure.

    b. Na **URL de fim de sessão** caixa de texto, colar **URL de fim de sessão** valor que copiou do portal do Azure.

    c. Na **URL de emissor** caixa de texto, colar **do Azure AD identificador** valor que copiou do portal do Azure.

    d. Clique em **procurar** botão para carregar o certificado que transferiu a partir do portal do Azure.

    e. Clique no botão **Guardar**.

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso ao Office de Virtual de 8 x 8.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **8 x 8 Virtual Office**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, escreva e selecione **8 x 8 Virtual Office**.

    ![A ligação de escritório Virtual 8 x 8 na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-8x8-virtual-office-test-user"></a>Criar utilizador de teste do Virtual Office 8 x 8

Nesta secção, um usuário chamado Eduarda Almeida é criado no escritório de Virtual de 8 x 8. suporta o escritório Virtual 8 x 8 **aprovisionamento de utilizadores de just-in-time**, que está ativado por predefinição. Não existe nenhum item de ação para nesta secção. Se um utilizador já não existir no escritório de Virtual de 8 x 8, é criado um novo após a autenticação.

> [!NOTE]
> Se precisar de criar manualmente um utilizador, terá de contactar o [equipa de suporte do Virtual Office 8 x 8](https://www.8x8.com/about-us/contact-us).

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico de escritório Virtual 8 x 8 no painel de acesso, deve ser automaticamente conectado para o escritório de Virtual de 8 x 8 para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

