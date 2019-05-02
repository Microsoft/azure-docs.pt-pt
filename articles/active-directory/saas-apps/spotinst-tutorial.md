---
title: 'Tutorial: Integração do Active Directory do Azure com Spotinst | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Spotinst.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2f6dbd70-c2db-4ae9-99ee-976c3090d214
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3f0d42562b1c927fe0f87ddc87b5e097022d2be2
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64922081"
---
# <a name="tutorial-azure-active-directory-integration-with-spotinst"></a>Tutorial: Integração do Active Directory do Azure com Spotinst

Neste tutorial, saiba como integrar Spotinst com o Azure Active Directory (Azure AD).
Integrar Spotinst no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Spotinst.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para Spotinst (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Spotinst, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter um [conta gratuita](https://azure.microsoft.com/free/)
* Spotinst logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta Spotinst **SP e IDP** iniciada SSO

## <a name="adding-spotinst-from-the-gallery"></a>Adicionando Spotinst da Galeria

Para configurar a integração do Spotinst com o Azure AD, terá de adicionar Spotinst a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Spotinst a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Spotinst**, selecione **Spotinst** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Spotinst na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Spotinst com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Spotinst deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Spotinst, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Spotinst Single Sign-On](#configure-spotinst-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste Spotinst](#create-spotinst-test-user)**  - para ter um equivalente da Eduarda Almeida na Spotinst que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com Spotinst, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Spotinst** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, se desejar configurar a aplicação no **IDP** iniciada pelo modo, execute os seguintes passos:

    ![Spotinst domínio e URLs únicas início de sessão em informações](common/idp-preintegrated-relay.png)

    a. Verifique **definir URLs adicionais**.

    b. Na **estado de reencaminhamento** caixa de texto, escreva um valor: `<ID>`

5. Clique em **definir URLs adicionais** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    ![Spotinst domínio e URLs únicas início de sessão em informações](common/metadata-upload-additional-signon.png)

    Na **URL de início de sessão** caixa de texto, escreva o URL:  `https://console.spotinst.com`

    > [!NOTE]
    > O valor de estado de reencaminhamento não é real. Atualizar o valor de estado de reencaminhamento com o valor de estado de reencaminhamento real, o que é explicado mais tarde no tutorial.

6. Aplicação de Spotinst espera que as asserções SAML num formato específico. Configure as seguintes declarações para esta aplicação. Pode gerir os valores destes atributos do **atributos de utilizador** secção na página de integração de aplicações. Sobre o **definido no início de sessão único com o SAML** página, clique em **editar** botão para abrir **atributos de utilizador** caixa de diálogo.

    ![image](common/edit-attribute.png)

7. No **afirmações de utilizador** secção sobre o **atributos de utilizador** caixa de diálogo, editar as afirmações utilizando **ícone de edição** ou adicionar as afirmações utilizando **Adicionar nova afirmação**para configurar o atributo de token SAML conforme mostrado na imagem acima e execute os seguintes passos: 

    | Name | Atributo de origem|
    | -----| --------------- |
    | Email | user.mail |
    | FirstName | user.givenname |
    | LastName | user.surname |

    a. Clique em **Adicionar nova afirmação** para abrir o **afirmações de utilizador de gerir** caixa de diálogo.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.

    c. Deixe o **espaço de nomes** em branco.

    d. Selecione a origem de dado **atributo**.

    e. Partir do **atributo de origem** lista, digite o valor de atributo apresentado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Guardar**.

8. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **XML de metadados de Federação**  entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

9. Sobre o **configurar Spotinst** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-spotinst-single-sign-on"></a>Configurar Spotinst Single Sign-On

1. Numa janela do browser web diferente, inicie sessão no Spotinst como um administrador de segurança.

2. Clique nas **ícone de utilizador** no canto superior direito do ecrã e clique em **definições**.

    ![Definições de Spotinst](./media/spotinst-tutorial/tutorial_spotinst_settings.png)

3. Clique nas **SECURITY** separador na parte superior e, em seguida, selecione **fornecedores de identidade** e execute os seguintes passos:

    ![Segurança de Spotinst](./media/spotinst-tutorial/tutorial_spotinst_security.png)

    a. Cópia a **estado de reencaminhamento** para a sua instância de valor e cole-o na **estado de reencaminhamento** caixa de texto no **configuração básica de SAML** secção no portal do Azure.

    b. Clique em **procurar** para carregar o ficheiro xml de metadados que transferiu a partir do portal do Azure

    c. Clique em **GUARDAR**.

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Spotinst.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Spotinst**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Spotinst**.

    ![A ligação de Spotinst na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-spotinst-test-user"></a>Criar utilizador de teste Spotinst

O objetivo desta secção é criar um usuário chamado Eduarda Almeida no Spotinst.

1. Se tiver configurado o aplicativo no **SP** iniciada pelo modo, execute os seguintes passos:

   a. Numa janela do browser web diferente, inicie sessão no Spotinst como um administrador de segurança.

   b. Clique nas **ícone de utilizador** no canto superior direito do ecrã e clique em **definições**.

    ![Definições de Spotinst](./media/spotinst-tutorial/tutorial_spotinst_settings.png)

    c. Clique em **usuários** e selecione **adicionar utilizador**.

    ![Definições de Spotinst](./media/spotinst-tutorial/adduser1.png)

    d. Na secção Adicionar utilizador, execute os seguintes passos:

    ![Definições de Spotinst](./media/spotinst-tutorial/adduser2.png)

    * Na **FullName** caixa de texto, introduza o nome completo do utilizador, como **BrittaSimon**.

    * Na **E-Mail** caixa de texto, introduza o endereço de e-mail do utilizador, como `brittasimon\@contoso.com`.

    * Selecione os detalhes da sua organização específicas para o **função de organização, a função de conta e contas**.

2. Se tiver configurado o aplicativo no **IDP** modo iniciado, aí não é nenhum item de ação para nesta secção. Spotinst suporta o aprovisionamento de just-in-time, que está por predefinição, ativada. Um novo utilizador é criado durante uma tentativa de aceder Spotinst se não existir ainda.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Spotinst no painel de acesso, deve ser automaticamente sessão iniciada no Spotinst para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

