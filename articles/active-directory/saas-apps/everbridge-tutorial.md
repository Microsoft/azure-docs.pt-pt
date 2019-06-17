---
title: 'Tutorial: Integração do Active Directory do Azure com Everbridge | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Everbridge.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 58d7cd22-98c0-4606-9ce5-8bdb22ee8b3e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/18/2019
ms.author: jeedes
ms.openlocfilehash: 60463a00c6864bed7b3a18e816ef0143d3573782
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67103260"
---
# <a name="tutorial-azure-active-directory-integration-with-everbridge"></a>Tutorial: Integração do Active Directory do Azure com Everbridge

Neste tutorial, saiba como integrar Everbridge com o Azure Active Directory (Azure AD).
Quando integrar Everbridge com o Azure AD, pode:

* Controlar no Azure AD que tenha acesso ao Everbridge.
* Permitir que os utilizadores ter automaticamente sessão iniciada no Everbridge com as suas contas do Azure AD. Este controlo de acesso é chamado de início de sessão único (SSO).
* Gira as suas contas num local central, utilizando o portal do Azure.
Para obter mais informações sobre o software como uma integração de aplicação de serviço (SaaS) com o Azure AD, consulte [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Everbridge, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter um [conta gratuita](https://azure.microsoft.com/free/).
* Uma subscrição de Everbridge que utiliza o início de sessão único.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Everbridge suporta SSO iniciado por IDP.

## <a name="add-everbridge-from-the-azure-marketplace"></a>Adicionar Everbridge no Azure Marketplace

Para configurar a integração do Everbridge com o Azure AD, adicione Everbridge no Azure Marketplace à sua lista de aplicações de SaaS geridas.

Para adicionar Everbridge no Azure Marketplace, siga estes passos.

1. Na [portal do Azure](https://portal.azure.com), no painel de navegação esquerdo, selecione **Azure Active Directory**.

    ![Botão de Active Directory do Azure](common/select-azuread.png)

2. Aceda a **aplicações empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione **nova aplicação** na parte superior da caixa de diálogo.

    ![Botão novo da aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **Everbridge**. Selecione **Everbridge** do painel de resultados e selecione **Add**.

     ![Everbridge na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Everbridge com base no utilizador de teste Eduarda Almeida.
Para o início de sessão único trabalhar, estabeleça uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Everbridge.

Para configurar e testar o Azure AD início de sessão único com Everbridge, conclua os seguintes blocos de construção:

- [Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on) para permitir aos utilizadores utilizar esta funcionalidade.
- [Configurar Everbridge como Everbridge manager portal início de sessão único](#configure-everbridge-as-everbridge-manager-portal-single-sign-on) para configurar as definições de início de sessão únicas no lado do aplicativo.
- [Configurar Everbridge como Everbridge membro portal início de sessão único](#configure-everbridge-as-everbridge-member-portal-single-sign-on) para configurar as definições de início de sessão únicas no lado do aplicativo.
- [Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user) para testar o Azure AD início de sessão único com Eduarda Almeida.
- [Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user) para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
- [Criar um utilizador de teste Everbridge](#create-an-everbridge-test-user) ter um equivalente da Eduarda Almeida na Everbridge que está ligado a representação do Azure AD do utilizador.
- [Testar início de sessão único](#test-single-sign-on) para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com Everbridge, siga estes passos.

1. Na [portal do Azure](https://portal.azure.com/), na **Everbridge** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Na **selecionar um método de início de sessão único** caixa de diálogo, selecione a **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definido no início de sessão único com o SAML** página, selecione **editar** para abrir o **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

    >[!NOTE]
    >Configurar a aplicação como o portal de gestor *ou* como o portal de membro no portal do Azure e o portal de Everbridge.

4. Para configurar o **Everbridge** como o **Everbridge manager portal**, no **configuração básica de SAML** secção, siga estes passos:

    ![Informações de início de sessão de único Everbridge domínio e URLs](common/idp-intiated.png)

    a. Na **identificador** , introduza um URL que segue o padrão `https://sso.everbridge.net/<API_Name>`

    b. Na **URL de resposta** , introduza um URL que segue o padrão `https://manager.everbridge.net/saml/SSO/<API_Name>/alias/defaultAlias`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com os valores de identificador e o URL de resposta reais. Para obter estes valores, entre em contato com o [equipa de suporte de Everbridge](mailto:support@everbridge.com). Também pode consultar os padrões que mostra a **configuração básica de SAML** secção no portal do Azure.

5. Para configurar o **Everbridge** como o **portal de membro Everbridge**, no **configuração básica de SAML** secção, siga estes passos:

  * Se quiser configurar a aplicação no modo de iniciado o IDP, siga estes passos:

     ![Everbridge domínio e URLs de informações de início de sessão únicas para o modo de iniciado o IDP](common/idp-intiated.png)

    a. Na **identificador** , introduza um URL que segue o padrão `https://sso.everbridge.net/<API_Name>/<Organization_ID>`

    b. Na **URL de resposta** , introduza um URL que segue o padrão `https://member.everbridge.net/saml/SSO/<API_Name>/<Organization_ID>/alias/defaultAlias`

   * Se quiser configurar a aplicação no modo iniciado por SP, selecione **definir URLs adicionais** e siga este passo:

     ![Everbridge domínio e URLs de informações de início de sessão únicas para o modo iniciado por SP](common/both-signonurl.png)

     a. Na **iniciar sessão no URL** , introduza um URL que segue o padrão `https://member.everbridge.net/saml/login/<API_Name>/<Organization_ID>/alias/defaultAlias?disco=true`

     > [!NOTE]
     > Estes valores não são reais. Atualize estes valores com o identificador real, o URL de resposta e inicie sessão em valores de URL. Para obter estes valores, entre em contato com o [equipa de suporte de Everbridge](mailto:support@everbridge.com). Também pode consultar os padrões que mostra a **configuração básica de SAML** secção no portal do Azure.

6. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, selecione **transferir** para transferir o **XML de metadados de Federação** . Guarde-o no seu computador.

    ![Ligação de transferência do certificado](common/metadataxml.png)

7. Na **configurar Everbridge** secção, copie os URLs que precisa para os seus requisitos:

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    - URL de início de sessão
    - Identificador do Azure AD
    - URL de fim de sessão

### <a name="configure-everbridge-as-everbridge-manager-portal-single-sign-on"></a>Configurar Everbridge como Everbridge manager portal início de sessão único

Configurar o SSO **Everbridge** como um **Everbridge manager portal** aplicação, siga estes passos.
 
1. Numa janela do browser web diferente, inicie sessão no Everbridge como um administrador.

1. No menu na parte superior, selecione o **definições** separador. Sob **Security**, selecione **Single Sign-On**.
   
     ![Configurar o início de sessão único](./media/everbridge-tutorial/tutorial_everbridge_002.png)
   
     a. Na **nome** , introduza o nome do fornecedor de identificador. Um exemplo é o nome da sua empresa.
   
     b. Na **nome da API** , introduza o nome da API.
   
     c. Selecione **Escolher ficheiro** para carregar o ficheiro de metadados que transferiu a partir do portal do Azure.
   
     d. Para **localização de identidade de SAML**, selecione **identidade é no elemento NameIdentifier da declaração de assunto**.
   
     e. Na **URL de início de sessão do fornecedor de identidade** caixa, cole a **URL de início de sessão** valor que copiou do portal do Azure.
   
     f. Para **fornecedor de serviços de iniciado o pedido de ligação**, selecione **redirecionamento de HTTP**.

     g. Selecione **Guardar**.

### <a name="configure-everbridge-as-everbridge-member-portal-single-sign-on"></a>Configurar Everbridge como Everbridge membro portal início de sessão único

Para configurar o início de sessão único num **Everbridge** como um **portal de membro Everbridge**, enviar a transferido **XML de metadados de Federação** para o [Everbridge equipa de suporte](mailto:support@everbridge.com). Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD 

Para criar o utilizador de teste Eduarda Almeida no portal do Azure, siga estes passos.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory** > **utilizadores** > **todos os utilizadores**.

    ![Os utilizadores e todas as ligações de utilizadores](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Botão de novo utilizador](common/new-user.png)

3. Na **utilizador** diálogo caixa, siga estes passos.

    ![Caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** , introduza **BrittaSimon**.
  
    b. Na **nome de utilizador** , introduza `brittasimon@yourcompanydomain.extension`. Um exemplo é BrittaSimon@contoso.com.

    c. Selecione o **mostrar palavra-passe** caixa de verificação. Anote o valor que é apresentado na **palavra-passe** caixa.

    d. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Ative a Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Everbridge.

1. No portal do Azure, selecione **aplicações empresariais** > **todas as aplicações** >**Everbridge**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Everbridge**.

    ![Ligação de Everbridge na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![Ligação de utilizadores e grupos](common/users-groups-blade.png)

4. Selecione **adicionar utilizador**. Na **adicionar atribuição** caixa de diálogo, selecione **utilizadores e grupos**.

    ![Adicionar a caixa de diálogo de atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores. Escolher **selecione** na parte inferior do ecrã.

6. Se qualquer valor de função na asserção de SAML, que espera do **selecionar função** diálogo caixa, selecione a função adequada para o utilizador a partir da lista. Escolher **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** caixa de diálogo, selecione **atribuir**.

### <a name="create-an-everbridge-test-user"></a>Criar um utilizador de teste Everbridge

Nesta secção, vai criar o utilizador de teste Eduarda Almeida na Everbridge. Para adicionar os utilizadores na plataforma Everbridge, trabalhar com o [equipa de suporte de Everbridge](mailto:support@everbridge.com). Os utilizadores tem de ser criados e ativados no Everbridge antes de utilizar o início de sessão único. 

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Teste a configuração do Azure AD única início de sessão com o painel de acesso.

Ao selecionar o mosaico Everbridge no painel de acesso, deve ser automaticamente conectado para a conta de Everbridge para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

