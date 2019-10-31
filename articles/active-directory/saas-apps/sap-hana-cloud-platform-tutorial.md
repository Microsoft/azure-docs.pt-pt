---
title: 'Tutorial: integração do Azure Active Directory com a plataforma de nuvem do SAP | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e a plataforma de nuvem SAP.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: bd398225-8bd8-4697-9a44-af6e6679113a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/17/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 89ea2c45e16dfeb63801f70fa4480c0d865a890f
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73160081"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform"></a>Tutorial: integração do Azure Active Directory com a plataforma de nuvem SAP

Neste tutorial, você aprenderá a integrar a plataforma de nuvem SAP com o Azure Active Directory (Azure AD).
A integração da plataforma de nuvem SAP ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao SAP Cloud Platform.
* Você pode permitir que seus usuários façam logon automaticamente no SAP Cloud Platform (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um local central-a portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com a plataforma de nuvem do SAP, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do SAP Cloud Platform

Depois de concluir este tutorial, os usuários do AD do Azure atribuídos à plataforma de nuvem SAP poderão fazer logon único no aplicativo usando a [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md).

>[!IMPORTANT]
>Você precisa implantar seu próprio aplicativo ou assinar um aplicativo em sua conta da plataforma de nuvem SAP para testar o logon único. Neste tutorial, um aplicativo é implantado na conta.
> 

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O SAP Cloud Platform dá suporte ao SSO iniciado por **SP**

## <a name="adding-sap-cloud-platform-from-the-gallery"></a>Adicionando a SAP Cloud Platform por meio da Galeria

Para configurar a integração do SAP Cloud Platform ao Azure AD, você precisa adicionar a SAP Cloud Platform da Galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar a SAP Cloud Platform por meio da galeria, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique no ícone de **Azure Active Directory** .

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **aplicativos empresariais** e, em seguida, selecione a opção **todos os aplicativos** .

    ![A folha aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, clique no botão **novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **plataforma de nuvem SAP**, selecione **plataforma de nuvem SAP** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Plataforma de nuvem SAP na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com a plataforma de nuvem do SAP, com base em um usuário de teste chamado **Brenda Simon**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do SAP Cloud Platform.

Para configurar e testar o logon único do Azure AD com a plataforma de nuvem do SAP, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure ad](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar logon único da SAP Cloud Platform](#configure-sap-cloud-platform-single-sign-on)** -para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com o Brenda Simon.
4. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para permitir que Brenda Simon use o logon único do Azure AD.
5. **[Criar usuário de teste do SAP Cloud Platform](#create-sap-cloud-platform-test-user)** – para ter um equivalente de Brenda Simon na SAP Cloud Platform que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com a plataforma de nuvem do SAP, execute as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos da **SAP Cloud Platform** , selecione **logon único**.

    ![Link configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-enalimentado** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com SAML** , clique no ícone **Editar** para abrir a caixa de diálogo **configuração básica de SAML** .

    ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , execute as seguintes etapas:

    ![Informações de logon único de domínio e URLs da SAP Cloud Platform](common/sp-identifier-reply.png)

    a. Na caixa de texto **URL de logon** , digite a URL usada pelos usuários para entrar no aplicativo **SAP Cloud Platform** . Esta é a URL específica da conta de um recurso protegido em seu aplicativo da plataforma de nuvem do SAP. A URL é baseada no seguinte padrão: `https://<applicationName><accountName>.<landscape host>.ondemand.com/<path_to_protected_resource>`
      
     >[!NOTE]
     >Essa é a URL em seu aplicativo de plataforma de nuvem do SAP que exige que o usuário se autentique.
     > 

    | |
    |--|
    | `https://<subdomain>.hanatrial.ondemand.com/<instancename>` |
    | `https://<subdomain>.hana.ondemand.com/<instancename>` |

    b. Na caixa de texto **identificador** , você fornecerá o tipo de uma URL da plataforma de nuvem do SAP usando um dos seguintes padrões: 

    | |
    |--|
    | `https://hanatrial.ondemand.com/<instancename>` |
    | `https://hana.ondemand.com/<instancename>` |
    | `https://us1.hana.ondemand.com/<instancename>` |
    | `https://ap1.hana.ondemand.com/<instancename>` |

    c. Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão:

    | |
    |--|
    | `https://<subdomain>.hanatrial.ondemand.com/<instancename>` |
    | `https://<subdomain>.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.us1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.us1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.ap1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.ap1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.hana.ondemand.com/<instancename>` |

    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com a URL de logon, o identificador e a URL de resposta reais. Contate a [equipe de suporte ao cliente do SAP Cloud Platform](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/5dd739823b824b539eee47b7860a00be.html) para obter a URL de logon e o identificador. URL de resposta que você pode obter da seção de gerenciamento de confiança, que é explicada posteriormente no tutorial.
    > 
4. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o XML de **metadados de Federação** das opções determinadas de acordo com seu requisito e salvá-lo em seu computador.

    ![O link de download do certificado](common/metadataxml.png)

### <a name="configure-sap-cloud-platform-single-sign-on"></a>Configurar o logon único da plataforma de nuvem SAP

1. Em uma janela diferente do navegador da Web, faça logon na ferramenta cockpit da SAP Cloud Platform em `https://account.<landscape host>.ondemand.com/cockpit`(por exemplo: https://account.hanatrial.ondemand.com/cockpit).

2. Clique na guia **confiança** .
   
    ![Bidirecional](./media/sap-hana-cloud-platform-tutorial/ic790800.png "Confiança")

3. Na seção Gerenciamento de confiança, em **provedor de serviço local**, execute as seguintes etapas:

    ![Gerenciamento de confiança](./media/sap-hana-cloud-platform-tutorial/ic793931.png "Gerenciamento de confiança")
   
    a. Clique em **Editar**.

    b. Como **tipo de configuração**, selecione **personalizado**.

    c. Como **nome do provedor local**, deixe o valor padrão. Copie esse valor e cole-o no campo **identificador** na configuração do Azure ad para a plataforma de nuvem do SAP.

    d. Para gerar uma **chave de assinatura** e um par de chaves de **certificado de assinatura** , clique em **gerar par de chaves**.

    e. Como **propagação de principal**, selecione **desabilitado**.

    f. Em **forçar autenticação**, selecione **desabilitado**.

    g. Clique em **Guardar**.

4. Depois de salvar as configurações do **provedor de serviço local** , execute o seguinte para obter a URL de resposta:
   
    ![Obter metadados](./media/sap-hana-cloud-platform-tutorial/ic793930.png "Obter metadados")

    a. Baixe o arquivo de metadados da SAP Cloud Platform clicando em **obter metadados**.

    b. Abra o arquivo XML de metadados da SAP Cloud Platform baixado e localize a marca **marca NS3: AssertionConsumerService** .
 
    c. Copie o valor do atributo **Location** e cole-o no campo URL de **resposta** na configuração do Azure ad para a plataforma de nuvem do SAP.

5. Clique na guia **provedor de identidade confiável** e, em seguida, clique em **Adicionar provedor de identidade confiável**.
   
    ![Gerenciamento de confiança](./media/sap-hana-cloud-platform-tutorial/ic790802.png "Gerenciamento de confiança")
   
    >[!NOTE]
    >Para gerenciar a lista de provedores de identidade confiáveis, você precisa ter escolhido o tipo de configuração personalizada na seção provedor de serviço local. Para o tipo de configuração padrão, você tem uma relação de confiança não editável e implícita para o serviço de ID do SAP. Para nenhum, você não tem nenhuma configuração de confiança.
    > 
    > 

6. Clique na guia **geral** e, em seguida, clique em **procurar** para carregar o arquivo de metadados baixado.
    
    ![Gerenciamento de confiança](./media/sap-hana-cloud-platform-tutorial/ic793932.png "Gerenciamento de confiança")
    
    >[!NOTE]
    >Depois de carregar o arquivo de metadados, os valores para **URL de logon único**, **URL de logoff único**e **certificado de assinatura** são preenchidos automaticamente.
    > 
     
7. Clique no separador **Atributos**.

8. Na guia **atributos** , execute a seguinte etapa:
    
    ![Atributos](./media/sap-hana-cloud-platform-tutorial/ic790804.png "Atributos") 

    a. Clique em **Adicionar atributo baseado em asserção**e adicione os seguintes atributos baseados em asserção:
       
    | Atributo de asserção | Atributo principal |
    | --- | --- |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` |FirstName |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` |LastName |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |e-mail |
   
     >[!NOTE]
     >A configuração dos atributos depende de como os aplicativos no SCP são desenvolvidos, ou seja, quais atributos eles esperam na resposta SAML e sob qual nome (atributo principal) eles acessam esse atributo no código.
     > 
    
    b. O **atributo padrão** na captura de tela é apenas para fins de ilustração. Não é necessário fazer o cenário funcionar.  
 
    c. Os nomes e valores do **atributo principal** mostrados na captura de tela dependem de como o aplicativo é desenvolvido. É possível que seu aplicativo exija mapeamentos diferentes.

### <a name="assertion-based-groups"></a>Grupos baseados em asserção

Como uma etapa opcional, você pode configurar grupos baseados em asserção para seu provedor de identidade Azure Active Directory.

O uso de grupos na plataforma de nuvem SAP permite atribuir dinamicamente um ou mais usuários a uma ou mais funções em seus aplicativos da plataforma de nuvem SAP, determinados pelos valores de atributos na Asserção SAML 2,0. 

Por exemplo, se a asserção contiver o atributo "*contrato = temporário*", talvez você queira que todos os usuários afetados sejam adicionados ao grupo "*temporário*". O grupo "*temporário*" pode conter uma ou mais funções de um ou mais aplicativos implantados em sua conta da plataforma de nuvem do SAP.
 
Use grupos baseados em asserção quando desejar atribuir simultaneamente muitos usuários a uma ou mais funções de aplicativos em sua conta da SAP Cloud Platform. Se você quiser atribuir apenas um único ou um pequeno número de usuários a funções específicas, é recomendável atribuí-los diretamente na guia "**autorizações**" da ferramenta cockpit da plataforma de nuvem do SAP.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

O objetivo desta seção é criar um usuário de teste no portal do Azure chamado Brenda Simon.

1. No portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **usuários**e, em seguida, selecione **todos os usuários**.

    ![Os links "usuários e grupos" e "todos os usuários"](common/users.png)

2. Selecione **novo usuário** na parte superior da tela.

    ![Botão novo usuário](common/new-user.png)

3. Nas propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo usuário](common/user-properties.png)

    a. No campo **nome** , insira **brendafernandes**.
  
    b. No campo **nome de usuário** , digite **brendafernandes\@yourcompanydomain. Extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e anote o valor exibido na caixa senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo acesso à SAP Cloud Platform.

1. Na portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **plataforma de nuvem SAP**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, digite e selecione **plataforma de nuvem SAP**.

    ![O link da SAP Cloud Platform na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![O link "usuários e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar usuário** e selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **usuários e grupos** , selecione **Brenda Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.

6. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-sap-cloud-platform-test-user"></a>Criar usuário de teste do SAP Cloud Platform

Para permitir que os usuários do AD do Azure façam logon na plataforma de nuvem SAP, você deve atribuir funções na plataforma de nuvem SAP a eles.

**Para atribuir uma função a um usuário, execute as seguintes etapas:**

1. Faça logon na ferramenta cockpit da **SAP Cloud Platform** .

2. Execute o seguinte:
   
    ![Autorizações](./media/sap-hana-cloud-platform-tutorial/ic790805.png "Autorizações")
   
    a. Clique em **autorização**.

    b. Clique na guia **usuários** .

    c. Na caixa de texto **usuário** , digite o endereço de email do usuário.

    d. Clique em **atribuir** para atribuir o usuário a uma função.

    e. Clique em **Guardar**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao clicar no bloco plataforma de nuvem do SAP no painel de acesso, você deve entrar automaticamente na plataforma de nuvem SAP para a qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

