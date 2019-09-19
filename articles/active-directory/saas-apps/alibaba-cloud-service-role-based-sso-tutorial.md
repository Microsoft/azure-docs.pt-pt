---
title: 'Tutorial: Azure Active Directory integração de SSO (logon único) com o serviço de nuvem do alibaba (SSO baseado em função) | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o serviço de nuvem alibaba (SSO baseado em função).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3667841e-acfc-4490-acf5-80d9ca3e71e8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/13/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 99b9173c817cc3ecf4b9a34ec6906af0b4de70e6
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71120776"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-alibaba-cloud-service-role-based-sso"></a>Tutorial: Azure Active Directory integração de SSO (logon único) com o serviço de nuvem do alibaba (SSO baseado em função)

Neste tutorial, você aprenderá a integrar o serviço de nuvem do alibaba (SSO baseado em função) com o Azure Active Directory (Azure AD). Ao integrar o serviço de nuvem do alibaba (SSO baseado em função) ao Azure AD, você pode:

* Controle no Azure AD que tem acesso ao serviço de nuvem do alibaba (SSO baseado em função).
* Habilite seus usuários a serem conectados automaticamente ao serviço de nuvem do alibaba (SSO baseado em função) com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada do serviço de nuvem do alibaba (SSO baseado em função) com SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* Serviço de nuvem alibaba (SSO baseado em função) dá suporte ao SSO iniciado pelo **IDP**

## <a name="adding-alibaba-cloud-service-role-based-sso-from-the-gallery"></a>Adicionando o serviço de nuvem alibaba (SSO baseado em função) por meio da Galeria

Para configurar a integração do serviço de nuvem do alibaba (SSO baseado em função) ao Azure AD, você precisa adicionar o serviço de nuvem do alibaba (SSO baseado em função) da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite serviço de **nuvem alibaba (SSO baseado em função)** na caixa de pesquisa.
1. Selecione **serviço de nuvem alibaba (SSO baseado em função)** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.
5. Na página **serviço de nuvem do alibaba (SSO baseado em função)** , clique em **Propriedades** no painel de navegação do lado esquerdo e copie a **ID do objeto** e salve-a no computador para uso posterior.

    ![Configuração de propriedades](./media/alibaba-cloud-service-role-based-sso-tutorial/Properties.png)


## <a name="configure-and-test-azure-ad-single-sign-on-for-alibaba-cloud-service-role-based-sso"></a>Configurar e testar o logon único do Azure AD para o serviço de nuvem do alibaba (SSO baseado em função)

Configure e teste o SSO do Azure AD com o serviço de nuvem do alibaba (SSO baseado em função) usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no serviço de nuvem alibaba (SSO baseado em função).

Para configurar e testar o SSO do Azure AD com o serviço de nuvem do alibaba (SSO baseado em função), conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
    1. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
2. **[Configurar o logon único baseado em função no serviço de nuvem do alibaba](#configure-role-based-single-sign-on-in-alibaba-cloud-service)** – para permitir que os usuários usem esse recurso.
    1. **[Configurar o SSO do serviço de nuvem alibaba (SSO baseado em função)](#configure-alibaba-cloud-service-role-based-sso-sso)** -para configurar as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste do serviço de nuvem alibaba (SSO baseado em função)](#create-alibaba-cloud-service-role-based-sso-test-user)** – para ter um equivalente de Brenda Simon no serviço de nuvem alibaba (SSO baseado em função) que esteja vinculado à representação de usuário do Azure AD.
3. **[Testar SSO único](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **serviço de nuvem do alibaba (SSO baseado em função)** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , se você tiver um **arquivo de metadados do provedor de serviços**, execute as seguintes etapas:

    >[!NOTE]
    >Você obterá os metadados do provedor de serviços desta [URL](https://signin.alibabacloud.com/saml-role/sp-metadata.xml)

    a. Clique em **carregamento de ficheiro de metadados**.

    b. Clique em **logótipo da pasta** para selecionar o ficheiro de metadados e clique em **carregar**.

    c. Depois que o arquivo de metadados for carregado com êxito, os valores do **identificador** e da **URL de resposta** serão preenchidos automaticamente na caixa de texto da seção serviço de nuvem alibaba (SSO baseado em função):

    > [!Note]
    > Se os valores do **identificador** e da **URL de resposta** não forem preenchidos automaticamente, preencha os valores manualmente de acordo com seu requisito.

1. O serviço de nuvem alibaba (SSO baseado em função) requer que as funções sejam configuradas no Azure AD. A declaração de função é pré-configurada para que você não precise configurá-la, mas ainda precisa criá-las no Azure AD usando este [artigo](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management).

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **XML de metadados de Federação** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/metadataxml.png)

1. Na seção **Configurar o serviço de nuvem do alibaba (SSO baseado em função)** , copie as URLs apropriadas com base em seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B. Simon.

1. No painel esquerdo na portal do Azure, selecione **Azure Active Directory**, selecione **usuários**e, em seguida, selecione **todos os usuários**.
1. Selecione **novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **usuário** , siga estas etapas:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome de usuário** , insira o username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **senha** .
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso ao serviço de nuvem do alibaba (SSO baseado em função).

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **serviço de nuvem alibaba (SSO baseado em função)** .
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na guia **usuários e grupos** , selecione U2 na lista de usuários e clique em **selecionar**. Em seguida, clique em **atribuir**.

    ![Configuração de teste](./media/alibaba-cloud-service-role-based-sso-tutorial/test01.png)

1. Exiba a função atribuída e teste serviço de nuvem alibaba (SSO baseado em função).

    ![Configuração de teste](./media/alibaba-cloud-service-role-based-sso-tutorial/test02.png)

    >[!NOTE]
    >Depois de atribuir o usuário (U2), a função criada é anexada automaticamente ao usuário. Se você tiver criado várias funções, será necessário anexar a função apropriada ao usuário, conforme necessário. Se você quiser implementar o SSO baseado em função do Azure AD para várias contas de nuvem do alibaba, repita as etapas anteriores.

## <a name="configure-role-based-single-sign-on-in-alibaba-cloud-service"></a>Configurar o logon único baseado em função no serviço de nuvem do alibaba

1. Entre no console do alibaba Cloud [RAM](https://account.alibabacloud.com/login/login.htm?oauth_callback=https%3A%2F%2Fram.console.aliyun.com%2F%3Fspm%3Da2c63.p38356.879954.8.7d904e167h6Yg9) usando o Account1.

2. No painel de navegação do lado esquerdo, selecione **SSO**.

3. Na guia **SSO baseado em função** , clique em **criar IDP**.

4. Na página `AAD` exibida, digite no campo nome do IDP, insira uma descrição no campo **Observação** , clique em **carregar** para carregar o arquivo de metadados de Federação que você baixou antes e clique em **OK**.

5. Depois que o IdP for criado com êxito, clique em **criar função de RAM**.

6. No campo **nome da função** de RAM `AADrole`, insira `AAD` , selecione na lista suspensa **selecionar IDP** e clique em OK.

    >[!NOTE]
    >Você pode conceder permissão para a função, conforme necessário. Depois de criar o IdP e a função correspondente, recomendamos que você salve o ARNs do IdP e a função para uso posterior. Você pode obter o ARNs na página informações do IdP e na página informações da função.

7. Associe a função de RAM de nuvem do alibaba (AADrole) ao usuário do Azure AD (U2): Para associar a função de RAM ao usuário do Azure AD, você deve criar uma função no Azure AD seguindo estas etapas:

    a. Entre no explorador do [Graph do Azure ad](https://developer.microsoft.com/graph/graph-explorer?spm=a2c63.p38356.879954.9.7d904e167h6Yg9).

    b. Clique em **Modificar permissões** para obter as permissões necessárias para criar uma função.

    ![Configuração do grafo](./media/alibaba-cloud-service-role-based-sso-tutorial/graph01.png)

    c. Selecione as permissões a seguir na lista e clique em **Modificar permissões**, conforme mostrado na figura a seguir.

    ![Configuração do grafo](./media/alibaba-cloud-service-role-based-sso-tutorial/graph02.png)

    >[!NOTE]
    >Depois que as permissões forem concedidas, faça logon novamente no Graph Explorer.

    d. Na página Gerenciador de gráficos, selecione **obter** na primeira lista suspensa e **beta** na segunda lista suspensa. Em seguida `https://graph.microsoft.com/beta/servicePrincipals` , insira no campo ao lado das listas suspensas e clique em **Executar consulta**.

    ![Configuração do grafo](./media/alibaba-cloud-service-role-based-sso-tutorial/graph03.png)

    >[!NOTE]
    >Se você estiver usando vários diretórios, poderá inserir `https://graph.microsoft.com/beta/contoso.com/servicePrincipals` no campo da consulta.

    e. Na seção **visualização de resposta** , extraia a propriedade appRoles da ' entidade de serviço ' para uso subsequente.

    ![Configuração do grafo](./media/alibaba-cloud-service-role-based-sso-tutorial/graph05.png)

    >[!NOTE]
    >Você pode localizar a propriedade appRoles inserindo `https://graph.microsoft.com/beta/servicePrincipals/<objectID>` no campo da consulta. Observe que o `objectID` é a ID de objeto que você copiou da página de **Propriedades** do Azure AD.

    f. Volte para o Gerenciador de gráficos, altere o método de **Get** para **patch**, Cole o conteúdo a seguir na seção **corpo da solicitação** e clique em **Executar consulta**:
    ```
    { 
    "appRoles": [
        { 
        "allowedMemberTypes":[
            "User"
        ],
        "description": "msiam_access",
        "displayName": "msiam_access",
        "id": "41be2db8-48d9-4277-8e86-f6d22d35****",
        "isEnabled": true,
        "origin": "Application",
        "value": null
        },
        { "allowedMemberTypes": [
            "User"
        ],
        "description": "Admin,AzureADProd",
        "displayName": "Admin,AzureADProd",
        "id": "68adae10-8b6b-47e6-9142-6476078cdbce",
        "isEnabled": true,
        "origin": "ServicePrincipal",
        "value": "acs:ram::187125022722****:role/aadrole,acs:ram::187125022722****:saml-provider/AAD"
        }
    ]
    }
    ```
    > [!NOTE]
    > O `value` é o Arns do IDP e a função que você criou no console de RAM. Aqui, você pode adicionar várias funções, conforme necessário. O Azure AD enviará o valor dessas funções como o valor de declaração na resposta SAML. No entanto, você só pode adicionar novas funções `msiam_access` após a parte da operação de patch. Para suavizar o processo de criação, recomendamos que você use um gerador de ID, como o gerador de GUID, para gerar IDs em tempo real.

    g. Depois que a ' entidade de serviço ' for corrigida com a função necessária, anexe a função com o usuário do Azure AD (U2) seguindo as etapas de **atribuir a seção usuário de teste do Azure ad** do tutorial.

### <a name="configure-alibaba-cloud-service-role-based-sso-sso"></a>Configurar o SSO do serviço de nuvem alibaba (SSO baseado em função)

Para configurar o logon único no lado do **serviço alibaba Cloud (SSO baseado em função)** , você precisa enviar o XML de **metadados de Federação** baixado e as URLs copiadas apropriadas de portal do Azure para a [equipe de suporte do serviço de nuvem alibaba (SSO baseado em função)](https://www.aliyun.com/service/) . Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.

### <a name="create-alibaba-cloud-service-role-based-sso-test-user"></a>Criar usuário de teste do serviço de nuvem alibaba (SSO baseado em função)

Nesta seção, você criará um usuário chamado Brenda Simon no serviço de nuvem do alibaba (SSO baseado em função). Trabalhe com a [equipe de suporte do serviço de nuvem alibaba (SSO baseado em função)](https://www.aliyun.com/service/) para adicionar os usuários na plataforma do serviço de nuvem do alibaba (SSO baseado em função). Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

## <a name="test-sso"></a>Testar SSO 

Depois que as configurações anteriores forem concluídas, teste o serviço de nuvem alibaba (SSO baseado em função) seguindo estas etapas:

1. Na portal do Azure, vá para a página **serviço de nuvem do alibaba (SSO baseado em função)** , selecione **logon único**e clique em **testar**.

    ![Configuração de teste](./media/alibaba-cloud-service-role-based-sso-tutorial/test03.png)

2. Clique em **Iniciar sessão como o utilizador atual**.

    ![Configuração de teste](./media/alibaba-cloud-service-role-based-sso-tutorial/test04.png)

3. Na página seleção de conta, selecione U2.

    ![Configuração de teste](./media/alibaba-cloud-service-role-based-sso-tutorial/test05.png)

4. A página a seguir é exibida, indicando que o SSO baseado em função foi bem-sucedido.

    ![Configuração de teste](./media/alibaba-cloud-service-role-based-sso-tutorial/test06.png)

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o serviço de nuvem do alibaba (SSO baseado em função) com o Azure AD](https://aad.portal.azure.com/)

