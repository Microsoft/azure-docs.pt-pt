---
title: 'Tutorial: Integração do Active Directory do Azure com sistemas de Pega | Documentos da Microsoft'
description: Neste tutorial, irá aprender como configurar o início de sessão único entre o Azure Active Directory e sistemas de Pega.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 31acf80f-1f4b-41f1-956f-a9fbae77ee69
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: 013e477b66d2772698ce5c9cc61a59f8a5a04a5a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67094888"
---
# <a name="tutorial-azure-active-directory-integration-with-pega-systems"></a>Tutorial: Integração do Active Directory do Azure com sistemas de Pega

Neste tutorial, irá aprender como integrar Pega sistemas com o Azure Active Directory (Azure AD).

Esta integração oferece estes benefícios:

* Pode utilizar o Azure AD para controlar quem tem acesso aos sistemas de Pega.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para sistemas de Pega (início de sessão único) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central: portal do Azure.

Para saber mais sobre a integração de aplicações SaaS com o Azure AD, veja [início de sessão único para aplicações no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Se não tiver uma subscrição do Azure, [criar uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com sistemas de Pega, tem de ter:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode inscrever-se para obter um [versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).
* Uma subscrição de sistemas de Pega que tem início de sessão único ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, irá configurar e testar o Azure AD início de sessão único num ambiente de teste.

* Sistemas de pega suporta SSO iniciado por SP e iniciado o IdP.

## <a name="add-pega-systems-from-the-gallery"></a>Adicionar sistemas Pega a partir da Galeria

Para configurar a integração de sistemas de Pega com o Azure AD, terá de adicionar sistemas Pega a partir da Galeria à sua lista de aplicações de SaaS geridas.

1. Na [portal do Azure](https://portal.azure.com), no painel esquerdo, selecione **Azure Active Directory**:

    ![Selecione Azure Active Directory](common/select-azuread.png)

2. Aceda a **aplicações empresariais** > **todas as aplicações**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar uma aplicação, selecione **nova aplicação** na parte superior da janela:

    ![Selecionar novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **Pega sistemas**. Selecione **Pega sistemas** nos resultados da pesquisa e, em seguida, selecione **Add**.

     ![Resultados da pesquisa](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, irá configurar e testar o Azure AD início de sessão único com sistemas de Pega com um utilizador de teste com o nome Eduarda Almeida.
Para ativar o início de sessão único, tem de estabelecer uma relação entre um utilizador do Azure AD e o utilizador correspondente em sistemas de Pega.

Para configurar e testar o Azure AD início de sessão único com sistemas de Pega, tem de concluir estes passos:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  para ativar a funcionalidade para os seus utilizadores.
2. **[Configurar sistemas de Pega início de sessão único](#configure-pega-systems-single-sign-on)**  no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  para testar o Azure AD início de sessão único.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  para ativar o Azure AD início de sessão único para o utilizador.
5. **[Criar um utilizador de teste de sistemas de Pega](#create-a-pega-systems-test-user)**  associado a representação do Azure AD do utilizador.
6. **[Testar início de sessão único](#test-single-sign-on)**  para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, irá ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com sistemas de Pega, siga estes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Pega sistemas** página de integração de aplicações, selecione **início de sessão único**:

    ![Selecione início de sessão único](common/select-sso.png)

2. Na **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único:

    ![Selecionar um método de início de sessão único](common/select-saml-option.png)

3. Na **definir a segurança de início de sessão único com o SAML** página, selecione a **editar** ícone para abrir o **configuração básica de SAML** caixa de diálogo:

    ![Ícone editar](common/edit-urls.png)

4. Na **configuração básica de SAML** caixa de diálogo, se quiser configurar a aplicação no modo de iniciado o IdP, conclua os seguintes passos.

    ![Caixa de diálogo de configuração de SAML básica](common/idp-intiated.png)

    1. Na **identificador** , introduza um URL neste padrão:

       `https://<customername>.pegacloud.io:443/prweb/sp/<instanceID>`

    1. Na **URL de resposta** , introduza um URL neste padrão:

       `https://<customername>.pegacloud.io:443/prweb/PRRestService/WebSSO/SAML/AssertionConsumerService`

5. Se quiser configurar a aplicação no modo iniciado por SP, selecione **definir URLs adicionais** e conclua os passos seguintes.

    ![Pega sistemas de domínio e URLs únicas início de sessão em informações](common/both-advanced-urls.png)

    1. Na **iniciar sessão no URL** , introduza o início de sessão no valor de URL.

    1. Na **estado de reencaminhamento** , introduza um URL neste padrão: `https://<customername>.pegacloud.io/prweb/sso`

    > [!NOTE]
    > Os valores fornecidos aqui são marcadores de posição. Tem de utilizar o identificador real, URL de resposta, iniciar sessão no URL e o URL de estado de reencaminhamento. Pode obter o identificador e os valores de URL de resposta de um aplicativo de Pega, conforme explicado posteriormente neste tutorial. Para obter o valor de estado de reencaminhamento, entre em contato com o [equipa de suporte de sistemas de Pega](https://www.pega.com/contact-us). Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

6. A aplicação de sistemas de Pega tem as asserções SAML para estar num formato específico. Para obtê-los no formato correto, terá de adicionar mapeamentos de atributos personalizado à sua configuração de atributos de token SAML. Captura de ecrã seguinte mostra os atributos de predefinição. Selecione o **edite** ícone para abrir o **atributos do utilizador** caixa de diálogo:

    ![Atributos de utilizador](common/edit-attribute.png)

7. Além dos atributos mostrados na captura de ecrã anterior, a aplicação de sistemas de Pega requer mais alguns atributos a serem passados de volta na resposta SAML. Na **afirmações de utilizador** secção a **atributos de utilizador** caixa de diálogo caixa, conclua os seguintes passos para adicionar esses atributos de token SAML:

    
   - `uid`
   - `cn`
   - `mail`
   - `accessgroup`  
   - `organization`  
   - `orgdivision`
   - `orgunit`
   - `workgroup`  
   - `Phone`

    > [!NOTE]
    > Estes valores são específicos para sua organização. Forneça os valores adequados.

    1. Selecione **Adicionar nova afirmação** para abrir o **afirmações de utilizador de gerir** caixa de diálogo:

    ![Selecione Adicionar nova afirmação](common/new-save-attribute.png)

    ![Gerir a caixa de diálogo de afirmações de utilizador](common/new-attribute-details.png)

    1. Na **nome** , introduza o nome de atributo apresentado para essa linha.

    1. Deixe o **espaço de nomes** caixa vazia.

    1. Para o **origem**, selecione **atributo**.

    1. Na **atributo de origem** , selecione o valor do atributo apresentado para essa linha.

    1. Selecione **Ok**.

    1. Selecione **Guardar**.

8. No **definir a segurança de início de sessão único com o SAML** na página a **certificado de assinatura SAML** secção, selecione o **transferir** ligação junto a **XML de metadados de Federação** acordo com suas necessidades e guarde o certificado no seu computador:

    ![Ligação de transferência do certificado](common/metadataxml.png)

9. Na **configurar sistemas de Pega** secção, copie os URLs adequados, com base nos seus requisitos.

    ![Copie os URLs de configuração](common/copy-configuration-urls.png)

    1. **URL de início de sessão**.

    1. **Azure AD Identifier**.

    1. **URL de fim de sessão**.

### <a name="configure-pega-systems-single-sign-on"></a>Configurar sistemas de Pega início de sessão único

1. Para configurar o início de sessão único no **Pega sistemas** lado, inicie sessão no Portal do Pega com uma conta de administrador na outra janela do browser.

2. Selecione **crie** > **SysAdmin** > **serviço de autenticação**:

    ![Selecione o serviço de autenticação](./media/pegasystems-tutorial/tutorial_pegasystems_admin.png)
    
3. Conclua os seguintes passos no **criar serviço de autenticação** ecrã.

    ![Criar o ecrã do serviço de autenticação](./media/pegasystems-tutorial/tutorial_pegasystems_admin1.png)

    1. Na **tipo** lista, selecione **SAML 2.0**.

    1. Na **Name** , introduza qualquer nome (por exemplo, **SSO do Azure AD**).

    1. Na **descrição breve** , introduza uma descrição.  

    1. Selecione **criar e abra**.
    
4. Na **informações do fornecedor de identidade (IdP)** secção, selecione **IdP importar metadados** e procure o ficheiro de metadados que transferiu a partir do portal do Azure. Clique em **submeter** ao carregar os metadados:

    ![Secção de informações do fornecedor (IdP) de identidade](./media/pegasystems-tutorial/tutorial_pegasystems_admin2.png)
    
    A importação irá preencher os dados de IdP, conforme mostrado aqui:

    ![Dados importados do IdP](./media/pegasystems-tutorial/tutorial_pegasystems_admin3.png)
    
6. Concluir os passos seguintes na **definições do fornecedor de serviços (SP)** secção.

    ![Definições do fornecedor de serviço](./media/pegasystems-tutorial/tutorial_pegasystems_admin4.png)

    1. Cópia a **identificação de entidade** valor e cole-o no **identificador** caixa o **configuração básica de SAML** secção no portal do Azure.

    1. Cópia a **localização do serviço de consumidor de asserção (ACS)** valor e cole-o no **URL de resposta** caixa o **configuração básica de SAML** secção no portal do Azure.

    1. Selecione **desativar a solicitação de assinatura**.

7. Selecione **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta secção, irá criar um utilizador de teste com o nome Eduarda Almeida no portal do Azure.

1. No portal do Azure, selecione **do Azure Active Directory** no painel esquerdo, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**:

    ![Selecione todos os utilizadores](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã:

    ![Selecione o novo utilizador](common/new-user.png)

3. Na **utilizador** diálogo caixa, conclua os passos seguintes.

    ![Caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** , introduza **BrittaSimon**.
  
    b. Na **nome de utilizador** , introduza **brittasimon @\<yourcompanydomain >.\< extensão >** . (Por exemplo, BrittaSimon@contoso.com.)

    c. Selecione **palavra-passe de Show**e, em seguida, anote o valor no **palavra-passe** caixa.

    d. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, irá ativar a Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso aos sistemas de Pega.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **Pega sistemas**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Pega sistemas**.

    ![Lista de aplicações](common/all-applications.png)

3. No painel esquerdo, selecione **utilizadores e grupos**:

    ![Selecionar utilizadores e grupos](common/users-groups-blade.png)

4. Selecione **adicionar utilizador**e, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Selecione adicionar utilizador](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista de utilizadores e, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se um valor de função na asserção de SAML, que espera do **selecionar função** diálogo caixa, selecione a função adequada para o utilizador a partir da lista. Clique nas **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** caixa de diálogo, selecione **atribuir**.

### <a name="create-a-pega-systems-test-user"></a>Criar um utilizador de teste de sistemas de Pega

Em seguida, terá de criar um utilizador com o nome Eduarda Almeida em sistemas de Pega. Trabalhar com o [equipa de suporte de sistemas de Pega](https://www.pega.com/contact-us) para criar utilizadores.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Agora precisa testar a configuração do Azure AD única início de sessão utilizando o painel de acesso.

Ao selecionar o mosaico de sistemas de Pega no painel de acesso, deve ser automaticamente conectado para a instância de sistemas de Pega para o qual configura o SSO. Para obter mais informações, consulte [aplicações de acesso e a utilização no portal minhas aplicações](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Tutoriais para integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)