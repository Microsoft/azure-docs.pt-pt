---
title: 'Tutorial: Integração do Active Directory do Azure com o Comm100 Live Chat | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Comm100, Chat ao vivo.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 0340d7f3-ab54-49ef-b77c-62a0efd5d49c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/22/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8bfebab81e1c0105bb42cf2853a2f042f128c1fd
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2019
ms.locfileid: "57731116"
---
# <a name="tutorial-azure-active-directory-integration-with-comm100-live-chat"></a>Tutorial: Integração do Active Directory do Azure com Comm100, Chat ao vivo

Neste tutorial, saiba como integrar o Comm100, Chat ao vivo com o Azure Active Directory (Azure AD).
Integrar o Comm100, Chat ao vivo no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Comm100, Chat ao vivo.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para Comm100 Chat ao vivo (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Comm100, Chat ao vivo, precisa do seguinte:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Comm100, Chat ao vivo logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Oferece suporte a Comm100, Chat ao vivo **SP** iniciada SSO

## <a name="adding-comm100-live-chat-from-the-gallery"></a>Adicionando Comm100, Chat ao vivo da Galeria

Para configurar a integração do Comm100, Chat ao vivo com o Azure AD, terá de adicionar Comm100, Chat ao vivo da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar a Comm100, Chat ao vivo a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Comm100, Chat ao vivo**, selecione **Comm100, Chat ao vivo** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Comm100 Chat ao vivo na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Comm100 Chat ao vivo com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Comm100 Live Chat deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Comm100, Chat ao vivo, precisa concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Comm100 Live Chat início de sessão único](#configure-comm100-live-chat-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste Comm100, Chat ao vivo](#create-comm100-live-chat-test-user)**  - para ter um equivalente da Eduarda Almeida na Comm100 Chat ao vivo que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com Comm100, Chat ao vivo, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Comm100, Chat ao vivo** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![Domínio de Chat ao vivo da Comm100 e URLs únicas início de sessão em informações](common/sp-signonurl.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão:  `https://<SUBDOMAIN>.comm100.com/AdminManage/LoginSSO.aspx?siteId=<SITEID>`

    > [!NOTE] 
    > O valor de URL de início de sessão não é real. Atualizar o valor de URL de início de sessão com o início de sessão no URL real, que é explicado mais tarde no tutorial.

5. Aplicação Comm100, Chat ao vivo espera que as asserções SAML num formato específico. Configure as seguintes declarações para esta aplicação. Pode gerir os valores destes atributos do **atributos de utilizador** secção na página de integração de aplicações. Sobre o **definido no início de sessão único com o SAML** página, clique em **editar** botão para abrir **atributos de utilizador** caixa de diálogo.

    ![image](common/edit-attribute.png)

6. No **afirmações de utilizador** secção sobre o **atributos de utilizador** caixa de diálogo, editar as afirmações utilizando **ícone de edição** ou adicionar as afirmações utilizando **Adicionar nova afirmação**para configurar o atributo de token SAML conforme mostrado na imagem acima e execute os seguintes passos: 

    | Name |  Atributo de origem|
    | ---------------| --------------- |
    |   e-mail    | user.mail |

    a. Clique em **Adicionar nova afirmação** para abrir o **afirmações de utilizador de gerir** caixa de diálogo.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.

    c. Deixe o **espaço de nomes** em branco.

    d. Selecione a origem de dado **atributo**.

    e. Partir do **atributo de origem** lista, digite o valor de atributo apresentado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Guardar**.

4. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

6. Sobre o **configurar Comm100, Chat ao vivo** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Azure Ad Identifier

    c. URL de fim de sessão

### <a name="configure-comm100-live-chat-single-sign-on"></a>Configurar Comm100 Chat ao vivo início de sessão único

9. Numa janela do browser web diferente, início de sessão para Comm100 Chat ao vivo como um administrador de segurança.

10. No canto superior direito da página, clique em **minha conta**.

    ![Myaccount Comm100, Chat ao vivo](./media/comm100livechat-tutorial/tutorial_comm100livechat_account.png)

11. Do lado esquerdo do menu, clique em **Security** e, em seguida, clique em **agente Single Sign-On**.

    ![Segurança Comm100, Chat ao vivo](./media/comm100livechat-tutorial/tutorial_comm100livechat_security.png)

12. Sobre o **agente Single Sign-On** página, execute os seguintes passos:

    ![Segurança Comm100, Chat ao vivo](./media/comm100livechat-tutorial/tutorial_comm100livechat_singlesignon.png)

    a. Copie a primeira ligação realçada e cole-a no **URL de início de sessão** na caixa de texto **Comm100 Live Chat domínio e URLs** secção no portal do Azure.

    b. Na **URL de SSO SAML** caixa de texto, cole o valor de **URL de início de sessão**, que copiou do portal do Azure.

    c. Na **URL de fim de sessão remoto** caixa de texto, cole o valor de **URL de fim de sessão**, que copiou do portal do Azure.

    d. Clique em **escolher um ficheiro** carregar a base 64 codificada certificado que transferiu do portal do Azure, para o **certificado**.

    e. Clique em **guardar alterações**

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Comm100, Chat ao vivo.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Comm100, Chat ao vivo**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Comm100, Chat ao vivo**.

    ![A ligação Comm100, Chat ao vivo, na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-comm100-live-chat-test-user"></a>Criar utilizador de teste Comm100, Chat ao vivo

Para ativar a utilizadores do Azure AD iniciar sessão no Comm100 Live Chat, tem de ser aprovisionados no Comm100 Live Chat. No Comm100 Live Chat, aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no Chat ao vivo de Comm100 como um administrador de segurança.

2. No canto superior direito da página, clique em **minha conta**.

    ![Myaccount Comm100, Chat ao vivo](./media/comm100livechat-tutorial/tutorial_comm100livechat_account.png)

3. Do lado esquerdo do menu, clique em **agentes** e, em seguida, clique em **novo agente**.

    ![Agente Comm100, Chat ao vivo](./media/comm100livechat-tutorial/tutorial_comm100livechat_agent.png)

4. Sobre o **novo agente** página, execute os seguintes passos:

    ![Comm100, Chat ao vivo novo agente](./media/comm100livechat-tutorial/tutorial_comm100livechat_newagent.png)

    a. a. Na **E-Mail** texto, introduza o e-mail do utilizador, como **Brittasimon@contoso.com**.

    b. Na **nome próprio** texto, introduza o nome de utilizador, como **Eduarda**.

    c. Na **sobrenome** texto, digite o apelido do utilizador, como **simon**.

    d. Na **nome a apresentar** caixa de texto, introduza o nome a apresentar do utilizador, como **Eduarda Almeida**

    e. Na **palavra-passe** caixa de texto, escreva a palavra-passe.

    f. Clique em **Guardar**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Comm100, Chat ao vivo no painel de acesso, deve ser automaticamente sessão iniciada no Comm100 Chat ao vivo para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

