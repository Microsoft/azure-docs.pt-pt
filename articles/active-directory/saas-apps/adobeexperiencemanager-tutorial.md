---
title: 'Tutorial: Integração do Active Directory do Azure com o Adobe Experience Manager | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o Adobe Experience Manager.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 88a95bb5-c17c-474f-bb92-1f80f5344b5a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 70096267a151971e9fb3b56a217da58ef9750264
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57405849"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-experience-manager"></a>Tutorial: Integração do Active Directory do Azure com o Adobe Experience Manager

Neste tutorial, saiba como integrar o Adobe Experience Manager com o Azure Active Directory (Azure AD).
Integrar o Adobe Experience Manager no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso para o Adobe Experience Manager.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para o Adobe Experience Manager (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Adobe Experience Manager, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* O Adobe Experience Manager logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* O Adobe Experience Manager suporta **SP e IDP** iniciada SSO

* O Adobe Experience Manager suporta **Just In Time** aprovisionamento de utilizadores

## <a name="adding-adobe-experience-manager-from-the-gallery"></a>Adicionar o Adobe Experience Manager a partir da Galeria

Para configurar a integração do Adobe Experience Manager com o Azure AD, terá de adicionar o Adobe Experience Manager a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar o Adobe Experience Manager a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Adobe Experience Manager**, selecione **Adobe Experience Manager** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![O Adobe Experience Manager na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configurar e testar o Azure AD início de sessão único com [nome da aplicação] com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado em [nome da aplicação] deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com [nome da aplicação], tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar o Adobe Experience Manager Single Sign-On](#configure-adobe-experience-manager-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste do Adobe Experience Manager](#create-adobe-experience-manager-test-user)**  - para ter um equivalente da Eduarda Almeida no Adobe Experience Manager que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com [nome da aplicação], execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Adobe Experience Manager** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, se desejar configurar a aplicação no **IDP** iniciada pelo modo, execute os seguintes passos:

    ![O Adobe Experience Manager domínio e URLs únicas início de sessão em informações](common/idp-intiated.png)

    a. Na **identificador** texto, escreva um valor exclusivo que definir no servidor do AEM.

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://<AEM Server Url>/saml_login`

    > [!NOTE]
    > O valor de URL de resposta não é real. Atualize o valor de URL de resposta com o URL de resposta real. Para obter este valor, entre em contato com o [equipa de suporte de cliente do Adobe Experience Manager](https://helpx.adobe.com/support/experience-manager.html) para obter este valor. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

5. Clique em **definir URLs adicionais** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    ![O Adobe Experience Manager domínio e URLs únicas início de sessão em informações](common/metadata-upload-additional-signon.png)

    Na **URL de início de sessão** caixa de texto, escreva o URL do servidor Adobe Experience Manager.

6. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

7. Sobre o **configure o Gestor de experiência do Adobe** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Azure Ad Identifier

    c. URL de fim de sessão

### <a name="configure-adobe-experience-manager-single-sign-on"></a>Configurar o Adobe Experience Manager Single Sign-On

1. Na outra janela do browser, abra a **Adobe Experience Manager** portal de administração.

2. Selecione **configurações** > **segurança** > **utilizadores**.

    ![Configurar o início de sessão único botão save](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user.png)

3. Selecione **administrador** ou qualquer outro utilizador relevante.

    ![Configurar o botão único início de sessão em Guardar](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin6.png)

4. Selecione **definições da conta** > **gerir TrustStore**.

    ![Configurar o botão único início de sessão em Guardar](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_managetrust.png)

5. Sob **adicionar certificados a partir do ficheiro CER**, clique em **selecionar ficheiro de certificado**. Procure e selecione o ficheiro de certificado, que já transferiu a partir do portal do Azure.

    ![Configurar o início de sessão único botão save](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user2.png)

6. O certificado é adicionado ao TrustStore. Tenha em atenção o alias do certificado.

    ![Configurar o botão único início de sessão em Guardar](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin7.png)

7. Sobre o **usuários** página, selecione **serviço de autenticação**.

    ![Configurar o botão único início de sessão em Guardar](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin8.png)

8. Selecione **definições da conta** > **KeyStore de criar/gerir**. Crie KeyStore ao fornecer uma palavra-passe.

    ![Configurar o botão único início de sessão em Guardar](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin9.png)

9. Volte ao ecrã de administrador. Em seguida, selecione **configurações** > **operações** > **consola Web**.

    ![Configurar o botão único início de sessão em Guardar](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin1.png)

    Esta ação abre a página de configuração.

    ![Configurar o início de sessão único botão save](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin2.png)

10. Encontrar **Adobe Granite SAML 2.0 autenticação manipulador**. Em seguida, selecione o **adicionar** ícone.

    ![Configurar o botão único início de sessão em Guardar](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin3.png)

11. Efetue as seguintes ações nesta página.

    ![Configurar o botão único início de sessão em Guardar](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin4.png)

    a. Na **caminho** , introduza **/**.

    b. Na **URL de IDP** , introduza o **URL de início de sessão** valor que copiou do portal do Azure.

    c. Na **Alias de certificado do IDP** , introduza o **Alias de certificado** valor que adicionou no TrustStore.

    d. Na **segurança fornecido ID de entidade** , introduza o exclusivo **do Azure Ad identificador** valor que configurou no portal do Azure.

    e. Na **URL do serviço de consumidor de asserção** , introduza o **URL de resposta** valor que configurou no portal do Azure.

    f. Na **Store de palavra-passe de chave** , introduza o **palavra-passe** definida no KeyStore.

    g. Na **ID de atributo do utilizador** , introduza o **ID de nome** ou outro ID de utilizador que seja relevante no seu caso.

    h. Selecione **Autocreate CRX utilizadores**.

    i. Na **URL de fim de sessão** , introduza o exclusivo **URL de fim de sessão** valor que recebeu do portal do Azure.

    j. Selecione **Guardar**.

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso para o Adobe Experience Manager.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Adobe Experience Manager**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Adobe Experience Manager**.

    ![A ligação do Adobe Experience Manager na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-adobe-experience-manager-test-user"></a>Criar utilizador de teste do Adobe Experience Manager

Nesta secção, vai criar um usuário chamado Eduarda Almeida no Adobe Experience Manager. Se tiver selecionado o **Autocreate CRX utilizadores** opção, os utilizadores são criados automaticamente após a autenticação com êxito.

Se quiser criar manualmente os utilizadores, trabalhar com o [equipa de suporte do Adobe Experience Manager](https://helpx.adobe.com/support/experience-manager.html) para adicionar os utilizadores na plataforma do Adobe Experience Manager.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico da Adobe Experience Manager no painel de acesso, deve ser automaticamente conectado para o Adobe Experience Manager para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
