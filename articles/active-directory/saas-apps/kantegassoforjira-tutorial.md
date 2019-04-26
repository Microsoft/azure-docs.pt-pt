---
title: 'Tutorial: Integração do Active Directory do Azure com o SSO Kantega para JIRA | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Kantega SSO para JIRA.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e2af4891-e3c8-43b3-bdcb-0500c493e9b4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 59c46e30f0f1c4ce3cf8cbe06808945ec0d9b5e2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60267705"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-jira"></a>Tutorial: Integração do Active Directory do Azure com o SSO Kantega para JIRA

Neste tutorial, saiba como integrar o SSO Kantega para JIRA com o Azure Active Directory (Azure AD).
Integrar o SSO Kantega para JIRA com o Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Kantega SSO para JIRA.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para Kantega SSO para JIRA (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Kantega SSO para JIRA, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter um [conta gratuita](https://azure.microsoft.com/free/)
* Subscrição de ativado Kantega SSO para JIRA início de sessão único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Kantega SSO para suporta JIRA **SP e IDP** iniciada SSO

## <a name="adding-kantega-sso-for-jira-from-the-gallery"></a>Adicionar Kantega SSO para JIRA a partir da Galeria

Para configurar a integração do SSO de Kantega para JIRA com o Azure AD, terá de adicionar Kantega SSO para JIRA a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Kantega SSO para JIRA a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Kantega SSO para JIRA**, selecione **Kantega SSO para JIRA** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Kantega SSO para JIRA na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com o Kantega SSO para JIRA com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Kantega SSO para JIRA deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com o SSO Kantega para JIRA, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar o SSO Kantega para JIRA início de sessão único](#configure-kantega-sso-for-jira-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar Kantega SSO para o utilizador de teste JIRA](#create-kantega-sso-for-jira-test-user)**  - para ter um equivalente da Eduarda Almeida na Kantega SSO para JIRA que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com o SSO Kantega para JIRA, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Kantega SSO para JIRA** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, se desejar configurar a aplicação no **IDP** iniciada pelo modo, execute os seguintes passos:

    ![Kantega SSO para JIRA domínio e URLs únicas início de sessão em informações](common/idp-intiated.png)

    a. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

5. Clique em **definir URLs adicionais** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    ![Kantega SSO para JIRA domínio e URLs únicas início de sessão em informações](common/metadata-upload-additional-signon.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão:  `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador de real, a URL de resposta e o URL de início de sessão. Estes valores são recebidos durante a configuração do Jira Plug-in do, que é explicado mais tarde no tutorial.

6. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **XML de metadados de Federação**  entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

7. Sobre o **configurar o SSO Kantega para JIRA** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-kantega-sso-for-jira-single-sign-on"></a>Configurar o SSO Kantega para JIRA início de sessão único

1. Numa janela do browser web diferente, inicie sessão no seu servidor no local do JIRA como um administrador.

1. Paire o rato sobre o ícone de roda dentada e clique nas **suplementos**.

    ![Configurar o início de sessão único](./media/kantegassoforjira-tutorial/addon1.png)

1. Na secção do separador de suplementos, clique em **encontrar novos suplementos**. Pesquisa **Kantega SSO para JIRA (SAML e Kerberos)** e clique em **instalar** botão para instalar o novo plug-in SAML.

    ![Configurar o início de sessão único](./media/kantegassoforjira-tutorial/addon2.png)

1. Inicia a instalação de plug-in.

    ![Configurar o início de sessão único](./media/kantegassoforjira-tutorial/addon3.png)

1. Assim que a instalação estiver concluída. Clique em **Fechar**.

    ![Configurar o início de sessão único](./media/kantegassoforjira-tutorial/addon33.png)

1.  Clique em **Gerir**.

    ![Configurar o início de sessão único](./media/kantegassoforjira-tutorial/addon34.png)
    
1. Novo plug-in está listado em **INTEGRAÇÕES**. Clique em **configurar** para configurar o plug-in de novo.

    ![Configurar o início de sessão único](./media/kantegassoforjira-tutorial/addon35.png)

1. Na **SAML** secção. Selecione **Azure Active Directory (Azure AD)** partir a **fornecedor de identidade de adicionar** lista pendente.

    ![Configurar o início de sessão único](./media/kantegassoforjira-tutorial/addon4.png)

1. Selecione o nível de assinatura como **básica**.

    ![Configurar o início de sessão único](./media/kantegassoforjira-tutorial/addon5.png)       

1. Sobre o **propriedades da aplicação** secção, execute os seguintes passos: 

    ![Configurar o início de sessão único](./media/kantegassoforjira-tutorial/addon6.png)

    a. Copiar o **URI de ID de aplicação** valor e usá-la como **identificador, o URL de resposta e o URL de início de sessão** no **configuração básica de SAML** secção no portal do Azure.

    b. Clique em **Seguinte**.

1. Sobre o **importação de metadados** secção, execute os seguintes passos: 

    ![Configurar o início de sessão único](./media/kantegassoforjira-tutorial/addon7.png)

    a. Selecione **ficheiro de metadados no meu computador**e o ficheiro de metadados de carregamento, que transferiu a partir do portal do Azure.

    b. Clique em **Seguinte**.

1. Sobre o **localização de nome e o SSO** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/kantegassoforjira-tutorial/addon8.png)

    a. Adicionar o nome do fornecedor de identidade no **nome do fornecedor de identidade** caixa de texto (por exemplo, do Azure AD).

    b. Clique em **Seguinte**.

1. Verifique se o certificado de assinatura e clique em **seguinte**.

    ![Configurar o início de sessão único](./media/kantegassoforjira-tutorial/addon9.png)

1. Sobre o **contas de utilizador do JIRA** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/kantegassoforjira-tutorial/addon10.png)

    a. Selecione **criar utilizadores no diretório de interno do JIRA, se for necessário** e introduza o nome adequado do grupo de utilizadores (pode ser não várias. de grupos separados por vírgula).

    b. Clique em **Seguinte**.

1. Clique em **Concluir**.

    ![Configurar o início de sessão único](./media/kantegassoforjira-tutorial/addon11.png)

1. Sobre o **conhecido domínios para o Azure AD** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/kantegassoforjira-tutorial/addon12.png)

    a. Selecione **conhecido domínios** do painel esquerdo da página.

    b. Introduza o nome de domínio a **conhecido domínios** caixa de texto.

    c. Clique em **Guardar**.

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Kantega SSO para JIRA.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Kantega SSO para JIRA**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Kantega SSO para JIRA**.

    ![O SSO Kantega para ligação JIRA na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-kantega-sso-for-jira-test-user"></a>Criar Kantega SSO para o utilizador de teste do JIRA

Para ativar a utilizadores do Azure AD iniciar sessão no JIRA, tem de ser aprovisionados no JIRA. Kantega SSO para JIRA, aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no seu servidor no local do JIRA como um administrador.

1. Paire o rato sobre o ícone de roda dentada e clique nas **gestão de utilizadores**.

    ![Adicionar o funcionário](./media/kantegassoforjira-tutorial/user1.png) 

1. Sob **gestão de utilizadores** secção, clique em **criar utilizador**.

    ![Adicionar o funcionário](./media/kantegassoforjira-tutorial/user2.png) 

1. Sobre o **"Criar novo usuário"** caixa de diálogo página, execute os seguintes passos:

    ![Adicionar o funcionário](./media/kantegassoforjira-tutorial/user3.png) 

    a. Na **endereço de E-Mail** caixa de texto, como o tipo de endereço de e-mail do utilizador Brittasimon@contoso.com.

    b. Na **FullName** caixa de texto, nome completo do tipo do utilizador, como a Eduarda Almeida.

    c. Na **nome de utilizador** caixa de texto, como o tipo de e-mail do utilizador Brittasimon@contoso.com.

    d. Na **palavra-passe** caixa de texto, escreva a palavra-passe do utilizador.

    e. Clique em **criar utilizador**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar o SSO Kantega para mosaico JIRA no painel de acesso, deve ser automaticamente sessão iniciada no SSO Kantega para JIRA para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
