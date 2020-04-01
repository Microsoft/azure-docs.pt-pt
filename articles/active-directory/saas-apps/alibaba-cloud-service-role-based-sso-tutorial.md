---
title: 'Tutorial: Azure Ative Directory integração individual (SSO) com alibaba Cloud Service (SSO baseado em funções) [ SSO) [ SSO) [ SSO) [ SSO) [ SSO) [ SSO) [ SSO) [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Alibaba Cloud Service (SSO baseado em papéis).
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
ms.openlocfilehash: e22bec224d185d0306f2b0032aef929f627c910e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "77367933"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-alibaba-cloud-service-role-based-sso"></a>Tutorial: Azure Ative Directory integração de um único sign-on (SSO) com o Alibaba Cloud Service (SSO baseado em funções)

Neste tutorial, você aprenderá a integrar o Alibaba Cloud Service (SSO baseado em funções) com o Azure Ative Directory (Azure AD). Quando integrar o Alibaba Cloud Service (SSO baseado em funções) com a Azure AD, pode:

* Controlo em Azure AD que tem acesso ao Alibaba Cloud Service (SSO baseado em funções).
* Ative que os seus utilizadores sejam automaticamente inscritos no Alibaba Cloud Service (SSO baseado em funções) com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Alibaba Cloud Service (SSO baseado em funções) única subscrição ativada por SSO.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Alibaba Cloud Service (SSO baseado em funções) suporta **IDP** iniciado SSO

## <a name="adding-alibaba-cloud-service-role-based-sso-from-the-gallery"></a>Adicionar o Alibaba Cloud Service (SSO baseado em papéis) na galeria

Para configurar a integração do Alibaba Cloud Service (SSO baseado em role) no Azure AD, você precisa adicionar Alibaba Cloud Service (SSO baseado em role) da galeria para a sua lista de aplicações SaaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **de galeria,** digite o **Alibaba Cloud Service (SSO baseado em papéis)** na caixa de pesquisa.
1. Selecione **Alibaba Cloud Service (SSO baseado em funções)** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.
5. Na página **Alibaba Cloud Service (SSO baseado em funções),** clique em **Propriedades** no painel de navegação do lado esquerdo e copie o ID do **objeto** e guarde-o no seu computador para posterior utilização.

    ![Propriedades config](./media/alibaba-cloud-service-role-based-sso-tutorial/Properties.png)


## <a name="configure-and-test-azure-ad-single-sign-on-for-alibaba-cloud-service-role-based-sso"></a>Configure e teste Azure AD single sign-on para Alibaba Cloud Service (SSO baseado em funções)

Configure e teste Azure AD SSO com Alibaba Cloud Service (SSO baseado em funções) utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Alibaba Cloud Service (SSO baseado em funções).

Para configurar e testar o Azure AD SSO com o Alibaba Cloud Service (SSO baseado em funções), complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
2. **[Configure o single sign-on baseado em funções no Alibaba Cloud Service](#configure-role-based-single-sign-on-in-alibaba-cloud-service)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Configure o Alibaba Cloud Service (SSO baseado em funções)](#configure-alibaba-cloud-service-role-based-sso-sso)** - para configurar as definições de início de sessão individuais no lado da aplicação.
    1. Crie o Alibaba Cloud Service (Utilizador de teste Baseado **[em Funções)](#create-alibaba-cloud-service-role-based-sso-test-user)** - para ter uma contrapartida da Britta Simon no Alibaba Cloud Service (SSO baseado em funções) que está ligada à representação do utilizador da AD Azure.
3. **[Teste o único SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações do **Alibaba Cloud Service (SSO baseado em funções),** encontre a secção **Gerir** e selecione um **único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** se tiver ficheiro de **metadados do Prestador**de Serviços, execute os seguintes passos:

    >[!NOTE]
    >Você receberá os metadados do Fornecedor de Serviços a partir deste [URL](https://signin.alibabacloud.com/saml-role/sp-metadata.xml)

    a. Clique no **ficheiro de metadados de upload**.

    b. Clique no logotipo da **pasta** para selecionar o ficheiro de metadados e clicar em **Carregar**.

    c. Uma vez que o ficheiro de metadados é carregado com sucesso, os valores **DE URL** do **Identificador** e resposta são preenchidos automaticamente na secção Alibaba Cloud Service (SSO) secção:

    > [!Note]
    > Se os valores de URL do **Identificador** e **da Resposta** não ficarem povoados automaticamente, preencha os valores manualmente de acordo com a sua exigência.

1. O Alibaba Cloud Service (SSO baseado em funções) exige que as funções sejam configuradas em Azure AD. A alegação de funções é pré-configurada para que não tenha de configurá-la, mas ainda precisa criá-las em Azure AD usando este [artigo](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management).

1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **metadados da Federação XML** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

1. Na secção Configurar o **Alibaba Cloud Service (SSO baseado em funções),** copie os URL(s) adequados com base no seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, você vai criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, **selecione Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo de nome username@companydomain.extensiondo **Utilizador,** introduza o . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permitirá que b.Simon utilize um único sign-on Azure, concedendo acesso ao Alibaba Cloud Service (SSO baseado em funções).

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Alibaba Cloud Service (SSO baseado em funções)**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No separador **Utilizadores e grupos,** selecione u2 da lista de utilizadores e clique em **Selecionar**. Em seguida, clique em **Atribuir**.

    ![Config de teste](./media/alibaba-cloud-service-role-based-sso-tutorial/test01.png)

1. Veja o papel atribuído e teste o Alibaba Cloud Service (SSO baseado em funções).

    ![Config de teste](./media/alibaba-cloud-service-role-based-sso-tutorial/test02.png)

    >[!NOTE]
    >Depois de atribuir o utilizador (u2), a função criada é automaticamente anexada ao utilizador. Se criou várias funções, tem de atribuir o papel adequado ao utilizador, se necessário. Se quiser implementar o SSO baseado em papéis a partir de Azure AD para várias contas da Alibaba Cloud, repita os passos anteriores.

## <a name="configure-role-based-single-sign-on-in-alibaba-cloud-service"></a>Configure o single sign-on baseado em funções no serviço de nuvem da Alibaba

1. Inscreva-se na [consola](https://account.alibabacloud.com/login/login.htm?oauth_callback=https%3A%2F%2Fram.console.aliyun.com%2F%3Fspm%3Da2c63.p38356.879954.8.7d904e167h6Yg9) Alibaba Cloud RAM utilizando a Conta1.

2. No painel de navegação do lado esquerdo, selecione **SSO**.

3. No separador **SSO baseado em Funções,** clique em **Criar IdP**.

4. Na página exibida, `AAD` introduza no campo IdP Name, introduza uma descrição no campo **Note,** clique em **Carregar** o ficheiro de metadados da federação que descarregou antes e clique em **OK**.

5. Depois de o IdP ser criado com sucesso, clique em **Criar Papel RAM**.

6. No campo **RAM Role Name** introduza `AADrole`, selecione `AAD` na lista de drop-down select **IdP** e clique EM OK.

    >[!NOTE]
    >Pode conceder permissão para o papel, conforme necessário. Após a criação do IDP e da função correspondente, recomendamos que guarde as ARNs do IDP e o papel para posterior utilização. Pode obter as ARNs na página de informação do IDP e na página de informação de funções.

7. Associar a função Alibaba Cloud RAM (AADrole) ao utilizador Azure AD (u2): Para associar a função RAM ao utilizador da AD Azure, deve criar um papel no Azure AD seguindo estes passos:

    a. Inscreva-se no [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer).

    b. Clique em **modificar permissões** para obter permissões necessárias para criar uma função.

    ![Config gráfico](./media/alibaba-cloud-service-role-based-sso-tutorial/graph01.png)

    c. Selecione as seguintes permissões da lista e clique em **Modificar Permissões,** como mostrado na figura seguinte.

    ![Config gráfico](./media/alibaba-cloud-service-role-based-sso-tutorial/graph02.png)

    >[!NOTE]
    >Depois de concedidas permissões, inicie sessão no Graph Explorer novamente.

    d. Na página do Graph Explorer, selecione **GET** a partir da primeira lista de lançamentos e **beta** da segunda lista de lançamentos. Em `https://graph.microsoft.com/beta/servicePrincipals` seguida, introduza no campo ao lado das listas de drop-down e clique em **Executar Consulta**.

    ![Config gráfico](./media/alibaba-cloud-service-role-based-sso-tutorial/graph03.png)

    >[!NOTE]
    >Se estiver a usar vários `https://graph.microsoft.com/beta/contoso.com/servicePrincipals` diretórios, pode entrar no campo da consulta.

    e. Na secção **De Visualização** de Resposta, extraia a propriedade appRoles do 'Diretor de Serviço' para posterior utilização.

    ![Config gráfico](./media/alibaba-cloud-service-role-based-sso-tutorial/graph05.png)

    >[!NOTE]
    >Pode localizar a propriedade appRoles `https://graph.microsoft.com/beta/servicePrincipals/<objectID>` entrando no campo da consulta. Note que `objectID` este é o ID do objeto que copiou da página Azure AD **Properties.**

    f. Volte ao Graph Explorer, altere o método de **GET** para **PATCH,** colhe o seguinte conteúdo na secção **Request Body** e clique em **Executar Consulta:**
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
    > São `value` as ARNs do IDP e o papel que criou na consola RAM. Aqui, pode adicionar múltiplos papéis conforme necessário. A Azure AD enviará o valor destas funções como valor de reclamação na resposta da SAML. No entanto, só pode `msiam_access` adicionar novas funções após a função do patch. Para suavizar o processo de criação, recomendamos que utilize um gerador de ID, como o GERADOR GUID, para gerar IDs em tempo real.

    g. Depois de o 'Diretor de Serviço' ser remendado com a função exigida, fixe a função ao utilizador Azure AD (u2) seguindo os passos de atribuir a secção de utilizador de **teste Azure AD** do tutorial.

### <a name="configure-alibaba-cloud-service-role-based-sso-sso"></a>Configure Alibaba Cloud Service (SSO baseado em funções) SSO

Para configurar um único sign-on no lado do **Alibaba Cloud Service (SSO baseado em papéis),** você precisa enviar a equipa de suporte de Metadados da Federação **XML** descarregada e URLs copiados apropriados do portal Azure para a equipa de suporte do [Alibaba Cloud Service (SSO baseado em role).](https://www.aliyun.com/service/) Eles definiram esta definição para ter a ligação SAML SSO corretamente definida em ambos os lados.

### <a name="create-alibaba-cloud-service-role-based-sso-test-user"></a>Criar o Alibaba Cloud Service (SSO baseado em funções)

Nesta secção, cria-se um utilizador chamado Britta Simon no Alibaba Cloud Service (SSO baseado em funções). Trabalhe com a equipa de suporte do [Alibaba Cloud Service (SSO baseado em papéis)](https://www.aliyun.com/service/) para adicionar os utilizadores na plataforma Alibaba Cloud Service (SSO baseada em papéis). Os utilizadores devem ser criados e ativados antes de utilizar um único sinal.

## <a name="test-sso"></a>Teste SSO 

Após a conclusão das configurações anteriores, teste o Alibaba Cloud Service (SSO baseado em funções) seguindo estes passos:

1. No portal Azure, vá à página **alibaba Cloud Service (SSO baseado em funções),** selecione **single sign-on**, e clique em **Test**.

    ![Config de teste](./media/alibaba-cloud-service-role-based-sso-tutorial/test03.png)

2. Clique em **Iniciar sessão como o utilizador atual**.

    ![Config de teste](./media/alibaba-cloud-service-role-based-sso-tutorial/test04.png)

3. Na página de seleção de conta, selecione u2.

    ![Config de teste](./media/alibaba-cloud-service-role-based-sso-tutorial/test05.png)

4. A página seguinte é apresentada, indicando que o SSO baseado em funções é bem sucedido.

    ![Config de teste](./media/alibaba-cloud-service-role-based-sso-tutorial/test06.png)

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o Alibaba Cloud Service (SSO baseado em funções) com a Azure AD](https://aad.portal.azure.com/)

