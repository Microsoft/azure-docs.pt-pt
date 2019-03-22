---
title: 'Tutorial: Integração do Active Directory do Azure com o controlo de Workspot | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o controle Workspot.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3ea8e4e9-f61f-4f45-b635-b0e306eda3d1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97f375c6f48d3dc497eb59e76f19fc64cf906b56
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57886512"
---
# <a name="tutorial-azure-active-directory-integration-with-workspot-control"></a>Tutorial: Integração do Active Directory do Azure com o controlo de Workspot

Neste tutorial, saiba como integrar o controle de Workspot com o Azure Active Directory (Azure AD).

Integração de controlo de Workspot com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao controle Workspot.
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para controle de Workspot (Single Sign-On) com as suas contas do Azure AD.
- Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o controlo de Workspot, terá dos seguintes itens:

- Uma subscrição do Azure
- Um controle de Workspot logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Workspot controlo da Galeria
2. Configuração e teste do Azure AD início de sessão único

## <a name="adding-workspot-control-from-the-gallery"></a>Adicionando Workspot controlo da Galeria
Para configurar a integração de controlo de Workspot com o Azure AD, terá de adicionar Workspot controlo a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Adicionar controlo de Workspot a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![image](./media/workspotcontrol-tutorial/selectazuread.png)

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![image](./media/workspotcontrol-tutorial/a_select_app.png)
    
3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![image](./media/workspotcontrol-tutorial/a_new_app.png)

4. Na caixa de pesquisa, escreva **controle Workspot**, selecione **Workspot controle** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com o controlo de Workspot com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no controle de Workspot a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no controle de Workspot deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com o controlo de Workspot, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
3. **[Criar um utilizador de teste de controle de Workspot](#create-a-workspot-control-test-user)**  - para ter um equivalente da Eduarda Almeida no controle de Workspot que está ligada à representação de utilizador do Azure AD.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo de controle de Workspot.

**Para configurar o Azure AD início de sessão único com o controlo de Workspot, execute os seguintes passos:**

1. Na [portal do Azure](https://portal.azure.com/), na **Workspot controle** página de integração de aplicações, selecione **início de sessão único**.

    ![image](./media/workspotcontrol-tutorial/B1_B2_Select_SSO.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML** modo para ativar o início de sessão único.

    ![image](./media/workspotcontrol-tutorial/b1_b2_saml_sso.png)

3. Sobre o **definido no início de sessão único com o SAML** página, clique em **editar** botão para abrir **configuração básica de SAML** caixa de diálogo.

    ![image](./media/workspotcontrol-tutorial/b1-domains_and_urlsedit.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos, se desejar configurar a aplicação na **IDP** intiated modo:

    ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_url.png)

    a. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://<INSTANCENAME>-saml.workspot.com/saml/metadata`

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://<INSTANCENAME>-saml.workspot.com/saml/assertion`

    c. Clique em **definir URLs adicionais** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

     ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_url1.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão:  `https://<INSTANCENAME>-saml.workspot.com/`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificador, o URL de resposta e início de sessão real. Contacte [equipa de suporte de cliente de controlo de Workspot](mailto:support@workspot.com) obter esses valores. 

5. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** e guarde-o no seu computador.

    ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_certficate.png) 

6. Sobre o **configurar o controlo de Workspot** secção, copie o URL adequado, de acordo com seus requisitos.

    Tenha em atenção que o URL pode dizer o seguinte:

    a. URL de início de sessão

    b. Azure Ad Identifier

    c. URL de fim de sessão

    ![image](./media/workspotcontrol-tutorial/d1_samlsonfigure.png) 

7. Numa janela do browser web diferente, início de sessão de controlo de Workspot como um administrador de segurança.

8. Na barra de ferramentas na parte superior da página, clique em **programa de configuração**, em seguida, navegue até à **SAML**.

    ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_setup.png)

9. Sobre o **configuração de linguagem de marcação de asserção de segurança** página, execute os seguintes passos:
 
    ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_saml.png)

    a. Na **ID de entidade** caixa de texto, cole o valor de **do Azure Ad identificador** que copiou do portal do Azure.   

    b.In a **URL do serviço de início de sessão** caixa de texto, cole o valor de **URL de início de sessão** que copiou do portal do Azure.

    c. Na **URL de serviço de fim de sessão** caixa de texto, cole o valor de **URL de fim de sessão** que copiou do portal do Azure. 

    d. Clique em **ficheiro de atualização** certificado que transferiu do portal do Azure, para o certificado X.509 com codificação de botão para carregar a base 64.

    e. Clique em **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    ![image](./media/workspotcontrol-tutorial/d_users_and_groups.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![image](./media/workspotcontrol-tutorial/d_adduser.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![image](./media/workspotcontrol-tutorial/d_userproperties.png)

    a. Na **Name** campo introduza **BrittaSimon**.
  
    b. Na **nome de utilizador** tipo de campo **brittasimon\@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **propriedades**, selecione a **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Selecione **Criar**.
 
### <a name="create-a-workspot-control-test-user"></a>Criar um utilizador de teste de controle de Workspot

Para ativar a utilizadores do Azure AD iniciar sessão no controle de Workspot, tem de ser aprovisionados no controle de Workspot. No controlo de Workspot, aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no controle de Workspot como um administrador de segurança.

2. Na barra de ferramentas na parte superior da página, clique em **usuários**, em seguida, navegue até à **adicionar utilizador**.

    ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_adduser.png)

3. Sobre o **adicionar um novo utilizador** página, execute os seguintes passos:

    ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_addnewuser.png)

    a. Na **nome próprio** texto, introduza o nome de utilizador, como **Eduarda**.

    b. Na **sobrenome** texto, digite o apelido do utilizador, como **simon**.

    c. Na **E-Mail** texto, introduza o e-mail do utilizador, como **Brittasimon\@contoso.com**.

    d. Selecione a função de utilizador adequada do **função** lista pendente.

    e. Selecione o grupo de utilizadores adequado partir do **grupo** lista pendente.

    f. Clique em **adicionar utilizador**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para controlo de Workspot.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**.

    ![image](./media/workspotcontrol-tutorial/d_all_applications.png)

2. Na lista de aplicações, selecione **Workspot controle**.

    ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_app.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![image](./media/workspotcontrol-tutorial/d_leftpaneusers.png)

4. Selecione o **Add** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![image](./media/workspotcontrol-tutorial/d_assign_user.png)

4. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

5. Na **adicionar atribuição** caixa de diálogo select a **atribuir** botão.
    
### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico de controle de Workspot no painel de acesso, deve obter automaticamente sessão iniciada em seu aplicativo de controle de Workspot.
Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
