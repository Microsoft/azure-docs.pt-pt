---
title: 'Tutorial: Integração do Active Directory do Azure com Promapp | Documentos da Microsoft'
description: Neste tutorial, irá aprender como configurar o início de sessão único entre o Azure Active Directory e Promapp.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 418d0601-6e7a-4997-a683-73fa30a2cfb5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: e91351d4571eaa084865c5a179ed05e6c773b952
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66240417"
---
# <a name="tutorial-azure-active-directory-integration-with-promapp"></a>Tutorial: Integração do Active Directory do Azure com Promapp

Neste tutorial, irá aprender como integrar Promapp com o Azure Active Directory (Azure AD).
Esta integração oferece estes benefícios:

* Pode utilizar o Azure AD para controlar quem tem acesso ao Promapp.
* Pode permitir que os utilizadores ter automaticamente sessão iniciada no Promapp (início de sessão único) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central: portal do Azure.

Para saber mais sobre a integração de aplicações SaaS com o Azure AD, veja [início de sessão único para aplicações no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Promapp, tem de ter:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode inscrever-se para obter um [versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).
* Uma subscrição de Promapp que tem início de sessão único ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, irá configurar e testar o Azure AD início de sessão único num ambiente de teste.

* Promapp suporta SSO iniciado por SP e iniciado o IdP.

* Promapp suporta o aprovisionamento de utilizador de just-in-time.

## <a name="add-promapp-from-the-gallery"></a>Adicionar Promapp a partir da Galeria

Para configurar a integração do Promapp com o Azure AD, terá de adicionar Promapp a partir da Galeria à sua lista de aplicações de SaaS geridas.

1. Na [portal do Azure](https://portal.azure.com), no painel esquerdo, selecione **Azure Active Directory**:

    ![Selecione Azure Active Directory](common/select-azuread.png)

2. Aceda a **aplicações empresariais** > **todas as aplicações**:

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar uma aplicação, selecione **nova aplicação** na parte superior da janela:

    ![Selecionar novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **Promapp**. Selecione **Promapp** nos resultados da pesquisa e, em seguida, selecione **Add**.

     ![Resultados da pesquisa](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, irá configurar e testar o Azure AD início de sessão único com Promapp com um utilizador de teste com o nome Eduarda Almeida.
Para ativar o início de sessão único, tem de estabelecer uma relação entre um utilizador do Azure AD e o utilizador correspondente no Promapp.

Para configurar e testar o Azure AD início de sessão único com Promapp, tem de concluir estes passos:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  para ativar a funcionalidade para os seus utilizadores.
2. **[Configurar Promapp início de sessão único](#configure-promapp-single-sign-on)**  no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  para testar o Azure AD início de sessão único.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  para ativar o Azure AD início de sessão único para o utilizador.
5. **[Testar início de sessão único](#test-single-sign-on)**  para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, irá ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com Promapp, siga estes passos:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicações do Promapp, selecione **início de sessão único**:

    ![Selecione início de sessão único](common/select-sso.png)

2. Na **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único:

    ![Selecione um método de início de sessão único](common/select-saml-option.png)

3. Na **definir a segurança de início de sessão único com o SAML** página, selecione a **editar** ícone para abrir o **configuração básica de SAML** caixa de diálogo:

    ![Ícone editar](common/edit-urls.png)

4. Na **configuração básica de SAML** caixa de diálogo, se quiser configurar a aplicação no modo de iniciado o IdP, conclua os seguintes passos.

    ![Caixa de diálogo de configuração de SAML básica](common/idp-intiated.png)

    1. Na **identificador** , introduza um URL neste padrão:

       | |
        |--|
        | `https://go.promapp.com/TENANTNAME/`|
        | `https://au.promapp.com/TENANTNAME/`|
        | `https://us.promapp.com/TENANTNAME/`|
        | `https://eu.promapp.com/TENANTNAME/`|
        | `https://ca.promapp.com/TENANTNAME/`|
        |   |

       > [!NOTE]
       > A integração do Azure AD com Promapp está atualmente configurada apenas para autenticação iniciadas pelo serviço. (Ou seja, vai para um URL de Promapp inicia o processo de autenticação.) Mas o **URL de resposta** é um campo obrigatório.

    1. Na **URL de resposta** , introduza um URL neste padrão:

       `https://<DOMAINNAME>.promapp.com/TENANTNAME/saml/authenticate.aspx`

5. Se quiser configurar a aplicação no modo iniciado por SP, selecione **definir URLs adicionais**. Na **iniciar sessão no URL** , introduza um URL neste padrão:

      `https://<DOMAINNAME>.promapp.com/TENANTNAME/saml/authenticate`

    ![Promapp domínio e URLs únicas início de sessão em informações](common/metadata-upload-additional-signon.png)

   

    > [!NOTE]
    > Estes valores são marcadores de posição. Tem de utilizar o identificador real, URL de resposta e início de sessão no URL. Contacte os [equipa de suporte de Promapp](https://www.promapp.com/about-us/contact-us/) para obter os valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** caixa de diálogo no portal do Azure.

6. No **definir a segurança de início de sessão único com o SAML** na página a **certificado de assinatura SAML** secção, selecione o **transferir** ligação junto a **certificado (Base64)** acordo com suas necessidades e guarde o certificado no seu computador:

    ![Ligação de transferência do certificado](common/certificatebase64.png)

7. Na **configurar Promapp** secção, copie os URLs adequados, com base nos seus requisitos:

    ![Copie os URLs de configuração](common/copy-configuration-urls.png)

    1. **URL de início de sessão**.

    1. **Azure AD Identifier**.

    1. **URL de fim de sessão**.

### <a name="configure-promapp-single-sign-on"></a>Configurar Promapp início de sessão único

1. Inicie sessão no site da sua empresa Promapp como um administrador.

2. No menu na parte superior da janela, selecione **administrador**:
   
    ![Selecione o administrador][12]

3. Selecione **configurar**:
   
    ![Selecione configurar][13]

4. Na **segurança** diálogo caixa, siga os passos seguintes.
   
    ![Caixa de diálogo de segurança][14]
    
    1. Colar o **URL de início de sessão** que copiou do portal do Azure para o **URL de início de sessão de SSO** caixa.
    
    1. Na **SSO - modo de início de sessão único** lista, selecione **opcional**. Selecione **Guardar**.

       > [!NOTE]
       > Modo de opcional é apenas para teste. Depois que estiver satisfeito com a configuração, selecione **necessários** no **SSO - modo de início de sessão único** lista para forçar todos os utilizadores para se autenticar com o Azure AD.

    1. No bloco de notas, abra o certificado que transferiu na secção anterior. Copie o conteúdo do certificado sem a primeira linha ( **---BEGIN CERTIFICATE---** ) ou a última linha ( **---END CERTIFICATE---** ). Cole o certificado de conteúdo para o **certificado x.509 de SSO** caixa e, em seguida, selecione **guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta secção, irá criar um utilizador de teste com o nome Eduarda Almeida no portal do Azure.

1. No portal do Azure, selecione **do Azure Active Directory** no painel esquerdo, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**:

    ![Selecione todos os utilizadores](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã:

    ![Selecione o novo utilizador](common/new-user.png)

3. Na **utilizador** diálogo caixa, siga os passos seguintes.

    ![Caixa de diálogo de utilizador](common/user-properties.png)

    1. Na **Name** , introduza **BrittaSimon**.
  
    1. Na **nome de utilizador** , introduza **BrittaSimon @\<yourcompanydomain >.\< extensão >** . (Por exemplo, BrittaSimon@contoso.com.)

    1. Selecione **mostrar palavra-passe**e, em seguida, anote o valor que está a **palavra-passe** caixa.

    1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, irá ativar a Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Promapp.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **Promapp**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Promapp**.

    ![Lista de aplicações](common/all-applications.png)

3. No painel esquerdo, selecione **utilizadores e grupos**:

    ![Selecionar utilizadores e grupos](common/users-groups-blade.png)

4. Selecione **adicionar utilizador**e, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Selecione adicionar utilizador](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista de utilizadores e, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se um valor de função na asserção de SAML, que espera do **selecionar função** diálogo caixa, selecione a função adequada para o utilizador a partir da lista. Clique nas **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** caixa de diálogo, selecione **atribuir**.

### <a name="just-in-time-user-provisioning"></a>Aprovisionamento de utilizadores de just-in-time

Promapp suporta o aprovisionamento de utilizador de just-in-time. Esta funcionalidade está ativada por predefinição. Se um utilizador já não existir no Promapp, é criado um novo após a autenticação.

### <a name="test-single-sign-on"></a>Testar início de sessão único

Agora precisa testar a configuração do Azure AD única início de sessão utilizando o painel de acesso.

Ao selecionar o mosaico Promapp no painel de acesso, deve ser automaticamente conectado à instância Promapp para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [aplicações de acesso e a utilização no portal minhas aplicações](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Tutoriais para integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[12]: ./media/promapp-tutorial/tutorial_promapp_05.png
[13]: ./media/promapp-tutorial/tutorial_promapp_06.png
[14]: ./media/promapp-tutorial/tutorial_promapp_07.png
