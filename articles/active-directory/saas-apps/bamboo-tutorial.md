---
title: 'Tutorial: Integração do Active Directory do Azure com o SAML SSO para Bamboo pela resolução GmbH | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o SAML SSO para Bamboo pela resolução GmbH.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f00160c7-f4cc-43bf-af18-f04168d3767c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97c13469c9c70aec31314048b8971c66bae5cd49
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "65858853"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-bamboo-by-resolution-gmbh"></a>Tutorial: Integração do Active Directory do Azure com o SAML SSO para Bamboo pela resolução GmbH

Neste tutorial, saiba como integrar o SSO SAML para Bamboo pela resolução GmbH com o Azure Active Directory (Azure AD).
Integrar o SSO SAML para Bamboo pela resolução GmbH com o Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao SAML SSO para Bamboo pela resolução GmbH.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para SAML SSO para Bamboo pela resolução GmbH (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o SAML SSO para Bamboo pela resolução GmbH, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter um [conta gratuita](https://azure.microsoft.com/free/)
* SAML SSO para Bamboo pela resolução GmbH logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* SAML SSO para Bamboo pela resolução suporta GmbH **SP e IDP** iniciada SSO
* SAML SSO para Bamboo pela resolução suporta GmbH **Just In Time** aprovisionamento de utilizadores

## <a name="adding-saml-sso-for-bamboo-by-resolution-gmbh-from-the-gallery"></a>Adicionando SAML SSO para Bamboo pela resolução GmbH da Galeria

Para configurar a integração de SAML SSO para Bamboo pela resolução GmbH com o Azure AD, terá de adicionar SAML SSO para Bamboo pela resolução GmbH a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Adicionar SAML SSO para Bamboo através da resolução GmbH a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **SAML SSO para Bamboo pela resolução GmbH**, selecione **SAML SSO para Bamboo pela resolução GmbH** no painel de resultados, em seguida, clique em **Add** botão para adicionar o aplicação.

    ![SAML SSO para Bamboo pela resolução GmbH, na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com o SAML SSO para Bamboo pela resolução GmbH com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no SAML SSO para Bamboo pela resolução GmbH deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com o SAML SSO para Bamboo pela resolução GmbH, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar o SSO SAML para Bamboo através da resolução GmbH Single Sign-On](#configure-saml-sso-for-bamboo-by-resolution-gmbh-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar SAML SSO para Bamboo por utilizador de teste de GmbH de resolução](#create-saml-sso-for-bamboo-by-resolution-gmbh-test-user)**  - para ter um equivalente da Eduarda Almeida no SAML SSO para Bamboo pela resolução GmbH que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com o SAML SSO para Bamboo pela resolução GmbH, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **SAML SSO para Bamboo pela resolução GmbH** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, se desejar configurar a aplicação no **IDP** iniciada pelo modo, execute os seguintes passos:

    ![Informações de início de sessão de único SAML SSO para Bamboo pela resolução GmbH domínio e URLs](common/idp-intiated.png)

    a. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://<server-base-url>/plugins/servlet/samlsso`

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://<server-base-url>/plugins/servlet/samlsso`

5. Clique em **definir URLs adicionais** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    ![Informações de início de sessão de único SAML SSO para Bamboo pela resolução GmbH domínio e URLs](common/metadata-upload-additional-signon.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão:  `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificador, o URL de resposta e início de sessão real. Contacte [equipa de suporte de SAML SSO para Bamboo pela resolução GmbH cliente](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bamboo/server/support) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

6. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **XML de metadados de Federação**  entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

7. Sobre o **configurar o SSO de SAML para Bamboo pela resolução GmbH** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-saml-sso-for-bamboo-by-resolution-gmbh-single-sign-on"></a>Configurar o SSO SAML para Bamboo através da resolução GmbH Single Sign-On

1. Início de sessão na sua SAML SSO para Bamboo pelo site de empresa resolução GmbH como administrador.

1. No lado direito da barra de ferramentas principal, clique em **configurações** > **suplementos**.

    ![As definições](./media/bamboo-tutorial/tutorial_bamboo_setings.png)

1. Aceda à secção de segurança, clique em **SAML SingleSignOn** na barra de menus.

    ![O Samlsingle](./media/bamboo-tutorial/tutorial_bamboo_samlsingle.png)

1. Sobre o **página de configuração de plug-in SAML SIngleSignOn**, clique em **adicionar idp**.

    ![A adicionar o idp](./media/bamboo-tutorial/tutorial_bamboo_addidp.png)

1. Sobre o **escolha o fornecedor de identidade de SAML** página, execute os seguintes passos:

    ![O fornecedor de identidade](./media/bamboo-tutorial/tutorial_bamboo_identityprovider.png)

    a. Selecione **tipo de Idp** como **do AZURE AD**.

    b. Na **nome** caixa de texto, escreva o nome.

    c. Na **Descrição** caixa de texto, digite a descrição.

    d. Clique em **Seguinte**.

1. Sobre o **configuração do fornecedor de identidade** página clique **próxima**.

    ![A configuração de identidade](./media/bamboo-tutorial/tutorial_bamboo_identityconfig.png)

1. Sobre o **importação SAML Idp metadados** página, clique em **carregar ficheiro** para carregar o **XML de METADADOS** ficheiro que transferiu a partir do portal do Azure.

    ![O idpmetadata](./media/bamboo-tutorial/tutorial_bamboo_idpmetadata.png)

1. Clique em **Seguinte**.

1. Clique em **Guardar definições**.

    ![A guardar](./media/bamboo-tutorial/tutorial_bamboo_save.png)

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso para SAML SSO para Bamboo pela resolução GmbH.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **SAML SSO para Bamboo pela resolução GmbH**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **SAML SSO para Bamboo pela resolução GmbH**.

    ![O SSO SAML para Bamboo pela ligação de GmbH resolução na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-saml-sso-for-bamboo-by-resolution-gmbh-test-user"></a>Criar SAML SSO para Bamboo por utilizador de teste de GmbH de resolução

O objetivo desta secção é criar um usuário chamado Eduarda Almeida no SAML SSO para Bamboo pela resolução GmbH. SAML SSO para Bamboo pela resolução GmbH suporta o aprovisionamento de just-in-time e também os utilizadores podem ser criados manualmente, contacte [equipa de suporte de SAML SSO para Bamboo pela resolução GmbH cliente](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bamboo/server/support) de acordo com seus requisitos.

### <a name="test-single-sign-on"></a>Testar início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar o SAML SSO para Bamboo por mosaico de GmbH resolução no painel de acesso, deve ser automaticamente sessão iniciada no SAML SSO para Bamboo por GmbH de resolução para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
