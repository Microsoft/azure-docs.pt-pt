---
title: 'Tutorial: Integração do Active Directory do Azure com o controlo de Workspot | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único para o Azure Active Directory e o controle de Workspot.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3ea8e4e9-f61f-4f45-b635-b0e306eda3d1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 3/11/2019
ms.author: jeedes
ms.openlocfilehash: 41d8d05cf5f900c7fcd5640f8896c715640ebcab
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65772805"
---
# <a name="tutorial-azure-active-directory-integration-with-workspot-control"></a>Tutorial: Integração do Active Directory do Azure com o controlo de Workspot

Neste tutorial, saiba como integrar o controle de Workspot com o Azure Active Directory (Azure AD). Quando integrar o controle de Workspot com o Azure AD, pode:

* Utilize o Azure AD para controlar quem tem acesso para controlo de Workspot.
* Permita aos utilizadores iniciar sessão automaticamente no controle de Workspot (início de sessão único [SSO]) através das respetivas contas do Azure AD.
* Gira as suas contas num local central: portal do Azure.

Para obter mais informações sobre a integração de aplicações SaaS com o Azure AD, consulte [início de sessão único para aplicações no Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o controlo de Workspot, precisa do seguinte:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter um [conta gratuita](https://azure.microsoft.com/free/).

* Uma controle Workspot única início de sessão no ativada subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

> [!Note]
> O controlo de Workspot suporta SSO iniciado por SP e iniciado o IDP.


## <a name="adding-workspot-control-from-the-gallery"></a>Adicionando Workspot controlo da Galeria

Para configurar a integração de controlo de Workspot com o Azure AD, tem de adicionar Workspot controlo da Galeria à sua lista de aplicações de SaaS geridas.

**Adicionar controlo de Workspot a partir da galeria, siga estes passos:**

1. No painel esquerdo dos [portal do Azure](https://portal.azure.com), selecione **Azure Active Directory**.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Aceda a **aplicações empresariais** e selecione **todos os aplicativos**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Selecione **nova aplicação** na parte superior da janela.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **controle Workspot**, selecione **Workspot controle** no painel de resultados e, em seguida, selecione **Add**.

     ![Janela "Adicionar a partir da Galeria"](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com o controlo de Workspot para um utilizador de teste, a Eduarda Almeida.
Para o início de sessão único funcione, tem de estabelecer uma ligação entre um utilizador do Azure AD e o utilizador relacionado no controle de Workspot.

Para configurar e testar o Azure AD início de sessão único com o controlo de Workspot, tem de concluir as seguintes tarefas:

1. [Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on) para permitir aos utilizadores utilizar a funcionalidade.
2. [Configurar controlo de Workspot início de sessão único](#configure-workspot-control-single-sign-on) para configurar as definições de início de sessão únicas no lado do aplicativo.
3. [Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user) para testar o Azure AD início de sessão único para Eduarda Almeida.
4. [Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user) para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. [Criar um utilizador de teste de controle de Workspot](#create-a-workspot-control-test-user) para estabelecer um equivalente da Eduarda Almeida no controle de Workspot que está ligado a representação do Azure AD do utilizador.
6. [Testar início de sessão único](#test-single-sign-on) para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com o controlo de Workspot, siga estes passos:

1. Sobre o **Workspot controlo** página de integração de aplicativo no [portal do Azure](https://portal.azure.com/), selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Na **selecionar um método de início de sessão único** janela, selecione **SAML** modo para ativar o início de sessão único.

    ![Selecione uma janela de início de sessão selecione método único](common/select-saml-option.png)

3. Na **definir a segurança de início de sessão único com o SAML** página, selecione a **editar** ícone (lápis) para acessar **configuração básica de SAML**.

    ![Editar ícone realçado na "Configuração básica de SAML"](common/edit-urls.png)

4. Na **configuração básica de SAML** secção, se quiser configurar a aplicação no modo de iniciado o IDP, siga estes passos:

    ![Controlo de Workspot domínio e URLs único informações de início de sessão](common/idp-intiated.png)

    1. Na **identificador** texto, introduza um URL no seguinte padrão:<br/>
    ***https://<<i></i>INSTANCENAME>-saml.workspot.com/saml/metadata***

    1. Na **URL de resposta** texto, introduza um URL no seguinte padrão:<br/>
    ***https://<<i></i>INSTANCENAME>-saml.workspot.com/saml/assertion***

5. Se quiser configurar a aplicação no modo iniciado por SP, selecione **definir URLs adicionais**.

    ![Controlo de Workspot domínio e URLs único informações de início de sessão](common/metadata-upload-additional-signon.png)

    Na **URL de início de sessão** texto, introduza um URL no seguinte padrão:<br/>
    ***https://<<i></i>INSTANCENAME>-saml.workspot.com/***

    > [!NOTE]
    > Estes valores não são reais. Substitua estes valores com o identificador real, URL de resposta e início de sessão no URL. Contacte os [equipa de suporte de cliente de controlo de Workspot](mailto:support@workspot.com) obter esses valores. Ou também pode consultar os padrões de **configuração básica de SAML** seção do portal do Azure.

6. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, selecione **transferir** para transferir **certificado (Base64)** nas opções disponíveis de acordo com os seus requisitos. Guarde-o para o seu computador.

    ![O link de download de certificado (Base64)](common/certificatebase64.png)

7. Na **configurar o controlo de Workspot** secção, copie os URLs apropriados de acordo com os seus requisitos:

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    - **URL de início de sessão**

    - **Azure AD Identifier**

    - **URL de fim de sessão**

### <a name="configure-workspot-control-single-sign-on"></a>Configurar controlo de Workspot início de sessão único

1. Numa janela do browser web diferente, inicie sessão no controle de Workspot como um administrador de segurança.

2. Na barra de ferramentas na parte superior da página, selecione **programa de configuração** e, em seguida **SAML**.

    ![Opções de configuração](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_setup.png)

3. Na **configuração de linguagem de marcação de asserção de segurança** janela, siga estes passos:
 
    ![Janela de configuração de linguagem de marcação de asserção de segurança](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_saml.png)

    1. Na **ID de entidade** caixa, cole a **Azure Ad identificador** que copiou do portal do Azure.

    1. Na **URL do serviço de início de sessão** caixa, cole a **URL de início de sessão** que copiou do portal do Azure.

    1. Na **URL de serviço de fim de sessão** caixa, cole a **URL de fim de sessão** que copiou do portal do Azure.

    1. Selecione **ficheiro de atualização** carregar para o certificado X.509 base 64 codificada certificado que transferiu a partir do portal do Azure.

    1. Selecione **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta secção, vai criar um utilizador de teste no portal do Azure.

1. No painel esquerdo do portal do Azure, selecione **do Azure Active Directory**, **utilizadores**e, em seguida **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior da janela.

    ![O utilizador"novo" botão](common/new-user.png)

3. Nas propriedades para o utilizador, siga estes passos:

    ![A janela de propriedades do utilizador](common/user-properties.png)

    1. Na **Name** , insira **BrittaSimon**.
  
    1. Na **nome de utilizador** campo, introduza **brittasimon @* yourcompanydomain.extension***. Por exemplo, introduza  **BrittaSimon@contoso.<i> </i>com**.

    1. Selecione o **palavra-passe de Show** caixa de verificação. Em seguida, anote o valor que é apresentado na **palavra-passe** caixa.

    1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, concede acesso de Eduarda Almeida a Workspot controle para permitir-lhe utilizar o Azure início de sessão único.

1. No portal do Azure, selecione **aplicações empresariais**, **todos os aplicativos**e, em seguida **Workspot controle**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Workspot controle**.

    ![A ligação de controlo de Workspot na lista de aplicações](common/all-applications.png)

3. No menu no lado esquerdo, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Selecione o **adicionar utilizador** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** janela.

    ![A janela de "Adicionar atribuição"](common/add-assign-user.png)

5. Na **utilizadores e grupos** janela, selecione **Eduarda Almeida** do **utilizadores** lista. Em seguida, clique em **Selecionar**.

6. Se necessitar de qualquer valor de função na asserção de SAML, selecione a função adequada para o utilizador na lista os **selecionar função** janela. Em seguida, clique em **selecione** na parte inferior.

7. Na **adicionar atribuição** janela, selecione **atribuir**.

### <a name="create-a-workspot-control-test-user"></a>Criar um utilizador de teste de controle de Workspot

Para ativar a utilizadores do Azure AD iniciar sessão no controle de Workspot, tem de ser aprovisionados no controle de Workspot. O aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, siga estes passos:**

1. Inicie sessão no controle de Workspot como um administrador de segurança.

2. Na barra de ferramentas na parte superior da página, selecione **usuários** e, em seguida **adicionar utilizador**.

    ![Opções de "Utilizadores"](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_adduser.png)

3. Na **adicionar um novo utilizador** janela, siga estes passos:

    ![Janela "Adicionar um novo utilizador"](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_addnewuser.png)

    1. Na **nome próprio** , introduza o nome de um utilizador, tal como **Eduarda**.

    1. Na **sobrenome** texto, digite o apelido do utilizador, tal como **Simon**.

    1. Na **E-Mail** , introduza o endereço de e-mail do utilizador, tal como  **Brittasimon@contoso.<i> </i>com**.

    1. Selecione a função de utilizador adequada do **função** na lista pendente.

    1. Selecione o grupo de utilizadores adequado partir da **grupo** na lista pendente.

    1. Selecione **adicionar utilizador**.

### <a name="test-single-sign-on"></a>Testar início de sessão único

Nesta secção, vamos testar nosso única início de sessão em configuração do Azure AD através de *painel de acesso*.

Quando clica no **Workspot controle** mosaico no painel de acesso, deve estar automaticamente conectado ao controle Workspot para o qual configura o SSO. Para mais informações, consulte [Introduction to the Access Panel (Introdução ao Painel de Acesso)](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access).

## <a name="additional-resources"></a>Recursos adicionais

- [Tutoriais para integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)

- [Início de sessão único para aplicações no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
