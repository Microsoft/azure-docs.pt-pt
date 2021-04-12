---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com o Alibaba Cloud Service (SSO baseado em funções) | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Alibaba Cloud Service (SSO baseado em funções).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/08/2020
ms.author: jeedes
ms.openlocfilehash: 6e4cb3c372b696b203d2441c74a6cafff175af47
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98736157"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-alibaba-cloud-service-role-based-sso"></a>Tutorial: Azure Ative Directory integração única de sign-on (SSO) com o Alibaba Cloud Service (SSO baseado em funções)

Neste tutorial, você vai aprender a integrar o Alibaba Cloud Service (SSO baseado em funções) com O Diretório Ativo Azure (Azure AD). Quando integrar o Alibaba Cloud Service (SSO baseado em funções) com Azure AD, pode:

* Control em Azure AD que tem acesso ao Alibaba Cloud Service (SSO baseado em funções).
* Capacitar os seus utilizadores a serem automaticamente inscritos no Alibaba Cloud Service (SSO baseado em funções) com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Alibaba Cloud Service (SSO baseado em funções) subscrição única (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Alibaba Cloud Service (SSO baseado em funções) suporta SSO iniciado no **IDP**

## <a name="adding-alibaba-cloud-service-role-based-sso-from-the-gallery"></a>Adicionar o Alibaba Cloud Service (SSO baseado em papéis) da galeria

Para configurar a integração do Alibaba Cloud Service (SSO baseado em funções) no Azure AD, é necessário adicionar o Alibaba Cloud Service (SSO baseado em funções) da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Add a partir da secção **de galeria,** **digite o Serviço de Nuvem Alibaba (SSO baseado em funções)** na caixa de pesquisa.
1. Selecione **o Alibaba Cloud Service (SSO baseado em funções)** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.
5. Na página **Alibaba Cloud Service (SSO baseada em funções),** clique em **Propriedades** no painel de navegação do lado esquerdo e copie o **ID** do objeto e guarde-o no seu computador para posterior utilização.

    ![Propriedades config](./media/alibaba-cloud-service-role-based-sso-tutorial/Properties.png)


## <a name="configure-and-test-azure-ad-sso-for-alibaba-cloud-service-role-based-sso"></a>Configure e teste Azure AD SSO para o Alibaba Cloud Service (SSO baseado em funções)

Configure e teste Azure AD SSO com Alibaba Cloud Service (SSO baseado em função) usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no Alibaba Cloud Service (SSO baseado em funções).

Para configurar e testar o Azure AD SSO com o Alibaba Cloud Service (SSO baseado em funções), execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
2. **[Configurar Role-Based Sign-On único no Serviço de Nuvem Alibaba](#configure-role-based-single-sign-on-in-alibaba-cloud-service)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Configure o Serviço de Nuvem Alibaba (SSO baseado em funções) SSO](#configure-alibaba-cloud-service-role-based-sso-sso)** - para configurar as definições de Sign-On únicas no lado da aplicação.
    1. **[Create Alibaba Cloud Service (Role-based SSO) user](#create-alibaba-cloud-service-role-based-sso-test-user)** - para ter uma contraparte de Britta Simon no Alibaba Cloud Service (SSO baseado em funções) que está ligada à representação AD Ad do utilizador.
3. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração de aplicações **do Alibaba Cloud Service (SSO baseado em funções),** encontre a secção **Gerir** e selecione um único sinal de **sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** se tiver **um ficheiro de metadados do Fornecedor de Serviços,** execute os seguintes passos:

    a. Clique **em Carregar o ficheiro de metadados**.

    b. Clique no **logotipo da pasta** para selecionar o ficheiro de metadados e clique em **Upload**.


    >[!NOTE]
    >1. Para o Site Internacional da Alibaba Cloud, descarregue os metadados do Fornecedor de [Serviços a](https://signin.alibabacloud.com/saml-role/sp-metadata.xml) partir deste link.
    > 1. Para o Site do Serviço de Nuvem alibaba (CN), descarregue os metadados do Fornecedor de [Serviços a](https://signin.aliyun.com/saml-role/sp-metadata.xml) partir deste link.

    c. Uma vez que o ficheiro de metadados é carregado com sucesso, os valores de URL **de identificação** e **resposta** são auto-povoados na caixa de texto da secção SSO (SSO baseada em funções) da Alibaba Cloud Service:

    > [!Note]
    > Se os valores de URL **de identificação** e **resposta** não forem preenchidos automaticamente, preencha os valores manualmente de acordo com o seu requisito.

1. O Alibaba Cloud Service (SSO baseado em funções) requer que os papéis sejam configurados em Azure AD. A alegação de papel é pré-configurada para que não tenha que configurá-la, mas ainda precisa criá-las em AD Azure usando este [artigo](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview).

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregue** para descarregar o certificado e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção Configurar o **Serviço de Nuvem Alibaba (SSO baseado em funções),** copie os URL(s) apropriados com base no seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao Alibaba Cloud Service (SSO baseado em funções).

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Alibaba Cloud Service (SSO baseado em funções)**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No **separador Utilizadores e grupos,** selecione u2 da lista de utilizadores e clique em **Select**. Em seguida, clique em **Atribuir**.

    ![Atribuir o utilizador de teste AZure AD1](./media/alibaba-cloud-service-role-based-sso-tutorial/test01.png)

1. Ver a função atribuída e testar o Serviço de Nuvem Alibaba (SSO baseado em funções).

    ![Atribuir o utilizador de teste AZure AD2](./media/alibaba-cloud-service-role-based-sso-tutorial/test02.png)

    >[!NOTE]
    >Depois de atribuir o utilizador (u2), a função criada é automaticamente anexada ao utilizador. Se criou várias funções, tem de anexar a função adequada ao utilizador, se necessário. Se pretender implementar SSO baseado em funções a partir de Azure AD para várias contas da Alibaba Cloud, repita os passos anteriores.

## <a name="configure-role-based-single-sign-on-in-alibaba-cloud-service"></a>Configurar Role-Based Sign-On única no Serviço de Nuvem Alibaba

1. Inscreva-se na consola Alibaba Cloud [RAM](https://account.alibabacloud.com/login/login.htm?oauth_callback=https%3A%2F%2Fram.console.aliyun.com%2F%3Fspm%3Da2c63.p38356.879954.8.7d904e167h6Yg9) utilizando a Conta1.

2. No painel de navegação do lado esquerdo, selecione **SSO**.

3. No **separador SSO baseado em funções,** clique em **Criar IdP**.

4. Na página apresentada, insira `AAD` no campo IdP Name, introduza uma descrição no campo **Note,** clique em **Upload** para carregar o ficheiro de metadados da federação que descarregou anteriormente e clique em **OK**.

5. Depois de o IdP ser criado com sucesso, clique em **Criar Papel RAM**.

6. No campo **RAM Role Name** , `AADrole` selecione `AAD` a partir da lista de drop-down **Select IdP** e clique em OK.

    >[!NOTE]
    >Pode conceder permissão para o papel que for necessário. Depois de criar o IdP e o papel correspondente, recomendamos que guarde as ARNs do IdP e o papel para posterior utilização. Pode obter os ARNs na página de informações do IdP e na página de informações de funções.

7. Associar o papel de Alibaba Cloud RAM (AADrole) ao utilizador Azure AD (u2): Para associar o papel da RAM ao utilizador AZure AD, deve criar uma função na Azure AD seguindo estes passos:

    a. Inscreva-se no [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer).

    b. Clique **em modificar permissões** para obter permissões necessárias para criar uma função.

    ![Gráfico config1](./media/alibaba-cloud-service-role-based-sso-tutorial/graph01.png)

    c. Selecione as seguintes permissões da lista e clique em **Modificar Permissões**, como mostrado na figura seguinte.

    ![Gráfico config2](./media/alibaba-cloud-service-role-based-sso-tutorial/graph02.png)

    >[!NOTE]
    >Após a concessão das permissões, inicie sessão no Graph Explorer novamente.

    d. Na página Graph Explorer, selecione **GET** da primeira lista de drop-down e **beta** da segunda lista de drop-down. Em seguida, insira `https://graph.microsoft.com/beta/servicePrincipals` no campo ao lado das listas de drop-down e clique em ' Executar **Consulta**.

    ![Gráfico config3](./media/alibaba-cloud-service-role-based-sso-tutorial/graph03.png)

    >[!NOTE]
    >Se estiver a utilizar vários diretórios, pode entrar `https://graph.microsoft.com/beta/contoso.com/servicePrincipals` no campo da consulta.

    e. Na secção **Pré-visualização** de resposta, extrai a propriedade appRoles do 'Service Principal' para utilização posterior.

    ![Gráfico config4](./media/alibaba-cloud-service-role-based-sso-tutorial/graph05.png)

    >[!NOTE]
    >Pode localizar a propriedade appRoles entrando `https://graph.microsoft.com/beta/servicePrincipals/<objectID>` no campo da consulta. Note que `objectID` este é o ID do objeto que copiou da página Azure AD **Properties.**

    f. Volte para o Graph Explorer, altere o método de **GET** para **PATCH,** cole o seguinte conteúdo na secção **'Corpo pedido'** e clique em **''' 'Executar's' (')**
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
    > São `value` as ARNs do IdP e o papel que criaste na consola RAM. Aqui, pode adicionar vários papéis conforme necessário. A Azure AD enviará o valor destas funções como valor de reclamação na resposta SAML. No entanto, só é possível adicionar novas funções após a `msiam_access` peça para a operação do patch. Para suavizar o processo de criação, recomendamos que utilize um gerador de identificação, como o Gerador GUID, para gerar IDs em tempo real.

    exemplo, Depois de o 'Service Principal' ser corrigido com a função exigida, fixe a função com o utilizador Azure AD (u2) seguindo os passos da secção de utilizador de **teste Azure AD** do tutorial.

### <a name="configure-alibaba-cloud-service-role-based-sso-sso"></a>Configure o Serviço de Nuvem Alibaba (SSO baseado em funções) SSO

Para configurar um único sign-on no lado **do Alibaba Cloud Service (SSO baseado em funções),** você precisa enviar o **metdata XML da Federação** descarregado e URLs copiados apropriados do portal Azure para a equipa de [suporte do Alibaba Cloud Service (SSO baseado em papéis).](https://www.aliyun.com/service/) Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-alibaba-cloud-service-role-based-sso-test-user"></a>Criar o Alibaba Cloud Service (SSO baseado em funções) utilizador de teste

Nesta secção, cria-se um utilizador chamado Britta Simon in Alibaba Cloud Service (SSO baseado em funções). Trabalhe com a [equipa de suporte da Alibaba Cloud Service (SSO baseada em funções)](https://www.aliyun.com/service/) para adicionar os utilizadores na plataforma Alibaba Cloud Service (SSO baseada em papéis). Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

## <a name="test-sso"></a>Teste SSO 

Após a conclusão das configurações anteriores, teste o Serviço de Nuvem Alibaba (SSO baseado em funções) seguindo estes passos:

1. No portal Azure, aceda à página **Do Serviço de Nuvem Alibaba (SSO baseado em funções),** selecione Single **sign-on** e clique em **Teste**.

    ![Teste config1](./media/alibaba-cloud-service-role-based-sso-tutorial/test03.png)

2. Clique em **Iniciar sessão como o utilizador atual**.

    ![Teste config2](./media/alibaba-cloud-service-role-based-sso-tutorial/test04.png)

3. Na página de seleção de conta, selecione u2.

    ![Teste config3](./media/alibaba-cloud-service-role-based-sso-tutorial/test05.png)

4. É apresentada a página seguinte, indicando que o SSO baseado em funções é bem sucedido.

    ![Teste config4](./media/alibaba-cloud-service-role-based-sso-tutorial/test06.png)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado o Alibaba Cloud Service (SSO baseado em funções) pode impor o controlo de sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
