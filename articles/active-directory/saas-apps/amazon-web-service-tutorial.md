---
title: 'Tutorial: Integração do Azure Active Directory com o Amazon Web Services (AWS) | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Amazon Web Services (AWS).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/30/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f6640708905abc266b07b7b66f5da09aeb890f01
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68823894"
---
# <a name="tutorial-integrate-amazon-web-services-aws-with-azure-active-directory"></a>Tutorial: Integrar o Amazon Web Services (AWS) com Azure Active Directory

Neste tutorial, você aprenderá a integrar o Amazon Web Services (AWS) ao Azure Active Directory (Azure AD). Ao integrar o Amazon Web Services (AWS) ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao Amazon Web Services (AWS).
* Habilite seus usuários a entrar automaticamente no Amazon Web Services (AWS) com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

![Amazon Web Services (AWS)](./media/amazon-web-service-tutorial/tutorial_amazonwebservices_image.png)

Você pode configurar vários identificadores para várias instâncias, como abaixo.

* `https://signin.aws.amazon.com/saml#1`

* `https://signin.aws.amazon.com/saml#2`

Com esses valores, o Azure ad removerá o valor **#** de e enviará o `https://signin.aws.amazon.com/saml` valor correto como a URL do público no token SAML.

**É recomendável usar essa abordagem pelos seguintes motivos:**

a. Cada aplicativo fornecerá um certificado X509 exclusivo. Cada instância da instância do aplicativo AWS pode ter uma data de expiração de certificado diferente que pode ser gerenciada em uma base de conta de AWS individual. A substituição geral do certificado será mais fácil nesse caso.

b. Você pode habilitar o provisionamento de usuário com o aplicativo AWS no Azure AD e, em seguida, nosso serviço buscará todas as funções dessa conta do AWS. Você não precisa adicionar ou atualizar manualmente as funções AWS no aplicativo.

c. Você pode atribuir o proprietário do aplicativo individualmente para o aplicativo que pode gerenciar o aplicativo diretamente no Azure AD.

> [!Note]
> Certifique-se de usar somente o aplicativo da Galeria

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Amazon Web Services (AWS).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste. Amazon Web Services (AWS) dá suporte ao **SP e** ao SSO iniciado pelo IDP.

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Adição de Amazon Web Services (AWS) da Galeria

Para configurar a integração do Amazon Web Services (AWS) ao Azure AD, você precisa adicionar o Amazon Web Services (AWS) da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **Amazon Web Services (AWS)** na caixa de pesquisa.
1. Selecione **Amazon Web Services (AWS)** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Configure e teste o SSO do Azure AD com o Amazon Web Services (AWS) usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Amazon Web Services (AWS).

Para configurar e testar o SSO do Azure AD com o Amazon Web Services (AWS), conclua os seguintes blocos de construção:

1. **[Configure o SSO do Azure ad](#configure-azure-ad-sso)** para permitir que seus usuários usem esse recurso.
2. **[Configure o Amazon Web Services (AWS)](#configure-amazon-web-services-aws)** para definir as configurações de SSO no lado do aplicativo.
3. **[Crie um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B. Simon.
4. **[Atribua o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** para habilitar B. Simon para usar o logon único do Azure AD.
5. **[Crie um usuário de teste do Amazon Web Services (AWS)](#create-amazon-web-services-aws-test-user)** para ter um equivalente de B. Simon em Amazon Web Services (AWS) que esteja vinculado à representação do usuário no Azure AD.
6. **[Teste o SSO](#test-sso)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Amazon Web Services (AWS)** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , o aplicativo é pré-configurado e as URLs necessárias já foram preenchidas previamente com o Azure. O usuário precisa salvar a configuração clicando no botão **salvar** .

5. Quando você estiver configurando mais de uma instância, forneça o valor do identificador. Da segunda instância em diante, forneça o valor do identificador no formato a seguir. Use um **#** sinal para especificar um valor SPN exclusivo.

    `https://signin.aws.amazon.com/saml#2`

6. Amazon Web Services aplicativo (AWS) espera as asserções SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão. Clique em **Editar** ícone para abrir a caixa de diálogo atributos de usuário.

    ![image](common/edit-attribute.png)

7. Além disso, o aplicativo Amazon Web Services (AWS) espera que mais alguns atributos sejam passados de volta na resposta SAML. Na seção **declarações do usuário** , na caixa de diálogo **atributos de usuário** , execute as seguintes etapas para adicionar o atributo de token SAML, conforme mostrado na tabela abaixo:

    | Nome  | Atributo de origem  | Espaço de Nomes |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | Role            | user.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    | SessionDuration             | "forneça um valor entre 900 segundos (15 minutos) a 43200 segundos (12 horas)" |  https://aws.amazon.com/SAML/Attributes |

    a. Clique em **Adicionar nova declaração** para abrir a caixa de diálogo **gerenciar declarações do usuário** .

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na caixa de texto **nome** , digite o nome do atributo mostrado para essa linha.

    c. Na caixa de texto **namespace** , digite o valor do namespace mostrado para essa linha.

    d. Selecione origem como **atributo**.

    e. Na lista **atributo de origem** , digite o valor do atributo mostrado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Guardar**.

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **XML de metadados de Federação** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

   ![O link de download de certificado](common/metadataxml.png)

1. Na seção **configurar Amazon Web Services (AWS)** , copie as URLs apropriadas com base em seu requisito.

   ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="configure-amazon-web-services-aws"></a>Configurar Amazon Web Services (AWS)

1. Em uma janela de navegador diferente, faça logon no site de sua empresa do Amazon Web Services (AWS) como administrador.

2. Clique em **página inicial do AWS**.

    ![Configurar página inicial de logon único][11]

3. Clique em **Gerenciamento de identidade e acesso**.

    ![Configurar identidade de logon único][12]

4. Clique em **provedores de identidade**e, em seguida, clique em **criar provedor**.

    ![Configurar o provedor de logon único][13]

5. Na página de diálogo **Configurar provedor** , execute as seguintes etapas:

    ![Caixa de diálogo Configurar logon único][14]

    a. Como **tipo de provedor**, selecione **SAML**.

    b. Na caixa de texto **nome do provedor** , digite um nome de provedor (por exemplo: *WAAD*).

    c. Para carregar o **arquivo de metadados** baixado do portal do Azure, clique em **escolher arquivo**.

    d. Clique em **próxima etapa**.

6. Na página de diálogo **verificar informações do provedor** , clique em **criar**.

    ![Configurar verificação de logon único][15]

7. Clique em **funções**e, em seguida, clique em **criar função**.

    ![Configurar funções de logon único][16]

8. Na página **criar função** , execute as seguintes etapas:  

    ![Configurar confiança de logon único][19]

    a. Selecione **Federação SAML 2,0** em **Selecionar tipo de entidade confiável**.

    b. Na **seção escolher um provedor saml 2,0**, selecione o **provedor SAML** que você criou anteriormente (por exemplo: *WAAD*)

    c. Selecione **permitir acesso de console de gerenciamento programático e AWS**.
  
    d. Clique **em Avançar: Permissões**.

9. Na caixa de diálogo **anexar políticas de permissões** , anexe a política apropriada de acordo com sua organização. Clique **em Avançar: Examine**.  

    ![Configurar política de logon único][33]

10. Na caixa de diálogo revisar, execute as seguintes etapas:

    ![Configurar análise de logon único][34]

    a. Na caixa de texto **nome da função** , insira o nome da função.

    b. Na caixa de texto **Descrição da função** , insira a descrição.

    c. Clique em **criar função**.

    d. Crie quantas funções forem necessárias e mapeie-as para o provedor de identidade.

11. Use as credenciais de conta de serviço do AWS para buscar as funções da conta AWS no provisionamento de usuário do Azure AD. Para isso, abra a página inicial do console do AWS.

12. Clique em **Serviços** -> **segurança, identidade & conformidade** -> **iam**.

    ![buscando as funções da conta AWS](./media/amazon-web-service-tutorial/fetchingrole1.png)

13. Selecione a guia **políticas** na seção iam.

    ![buscando as funções da conta AWS](./media/amazon-web-service-tutorial/fetchingrole2.png)

14. Crie uma nova política clicando em **criar política** para buscar as funções da conta AWS no provisionamento de usuário do Azure AD.

    ![Criando nova política](./media/amazon-web-service-tutorial/fetchingrole3.png)

15. Crie sua própria política para buscar todas as funções de contas do AWS executando as seguintes etapas:

    ![Criando nova política](./media/amazon-web-service-tutorial/policy1.png)

    a. Na seção **"criar política"** , clique na guia **"JSON"** .

    b. No documento de política, adicione o JSON abaixo.

    ```json
    {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Effect": "Allow",
                "Action": [
                "iam:ListRoles"
                ],
                "Resource": "*"
            }
        ]
    }
    ```

    c. Clique no **botão examinar política** para validar a política.

    ![Definir a nova política](./media/amazon-web-service-tutorial/policy5.png)

16. Defina a **nova política** executando as seguintes etapas:

    ![Definir a nova política](./media/amazon-web-service-tutorial/policy2.png)

    a. Forneça o **nome da política** como **AzureAD_SSOUserRole_Policy**.

    b. Você pode fornecer uma **Descrição** à política, pois **essa política permitirá buscar as funções de contas AWS**.

    c. Clique no botão **"criar política"** .

17. Crie uma nova conta de usuário no serviço IAM AWS executando as seguintes etapas:

    a. Clique na navegação de **usuários** no console iam AWS.

    ![Definir a nova política](./media/amazon-web-service-tutorial/policy3.png)

    b. Clique no botão **Adicionar usuário** para criar um novo usuário.

    ![Adicionar utilizador](./media/amazon-web-service-tutorial/policy4.png)

    c. Na seção **Adicionar usuário** , execute as seguintes etapas:

    ![Adicionar utilizador](./media/amazon-web-service-tutorial/adduser1.png)

    * Insira o nome de usuário como **AzureADRoleManager**.

    * No tipo de acesso, selecione a opção **acesso programático** . Dessa forma, o usuário pode invocar as APIs e buscar as funções da conta AWS.

    * Clique no botão **próximas permissões** no canto inferior direito.

18. Agora, crie uma nova política para esse usuário executando as seguintes etapas:

    ![Adicionar utilizador](./media/amazon-web-service-tutorial/adduser2.png)

    a. Clique no botão **anexar políticas existentes diretamente** .

    b. Procure a política recém-criada na seção de filtro **AzureAD_SSOUserRole_Policy**.

    c. Selecione a **política** e clique no **seguinte: Botão** examinar.

19. Examine a política para o usuário anexado executando as seguintes etapas:

    ![Adicionar utilizador](./media/amazon-web-service-tutorial/adduser3.png)

    a. Examine o nome de usuário, o tipo de acesso e a política mapeados para o usuário.

    b. Clique no botão **criar usuário** no canto inferior direito para criar o usuário.

20. Baixe as credenciais de usuário de um usuário executando as seguintes etapas:

    ![Adicionar utilizador](./media/amazon-web-service-tutorial/adduser4.png)

    a. Copie a **ID de chave de acesso** do usuário e a chave de acesso de **segredo**.

    b. Insira essas credenciais na seção provisionamento de usuário do Azure AD para buscar as funções do console do AWS.

    c. Clique no botão **fechar** na parte inferior.

21. Navegue até a seção **provisionamento de usuário** do Amazon Web Services aplicativo no Azure ad portal de gerenciamento.

    ![Adicionar utilizador](./media/amazon-web-service-tutorial/provisioning.png)

22. Insira a **chave de acesso** e o **segredo** no campo **segredo do cliente** e **token secreto** , respectivamente.

    ![Adicionar utilizador](./media/amazon-web-service-tutorial/provisioning1.png)

    a. Insira a chave de acesso do usuário AWS no campo **clientsecret** .

    b. Insira o segredo de usuário AWS no campo **token secreto** .

    c. Clique no botão **testar conexão** e você poderá testar com êxito essa conexão.

    d. Salve a configuração clicando no botão **salvar** na parte superior.

23. Agora, verifique se você habilitou o status de provisionamento **em** na seção Configurações, ativando a opção e clicando no botão **salvar** na parte superior.

    ![Adicionar utilizador](./media/amazon-web-service-tutorial/provisioning2.png)

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso ao Amazon Web Services (AWS).

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **Amazon Web Services (AWS)** .
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-amazon-web-services-aws-test-user"></a>Criar usuário de teste do Amazon Web Services (AWS)

O objetivo desta seção é criar um usuário chamado B. Simon em Amazon Web Services (AWS). Amazon Web Services (AWS) não precisa que um usuário seja criado em seu sistema para SSO, portanto, você não precisa executar nenhuma ação aqui.

### <a name="test-sso"></a>Testar SSO

Ao selecionar o bloco Amazon Web Services (AWS) no painel de acesso, você deve ser conectado automaticamente ao Amazon Web Services (AWS) para o qual você configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="known-issues"></a>Problemas conhecidos

 * Na seção **provisionamento** , a subseção **mapeamentos** mostrará um "carregando..." e nunca exibirá os mapeamentos de atributo. O único fluxo de trabalho de provisionamento com suporte hoje é a importação de funções do AWS para o Azure AD para seleção durante a atribuição de usuário/grupo. Os mapeamentos de atributo para isso são predeterminados e não configuráveis.
 
 * A seção de **provisionamento** só dá suporte à inserção de um conjunto de credenciais para um locatário AWS por vez. Todas as funções importadas são gravadas na propriedade appRoles do [objeto de entidade de segurança](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta) do Azure ad para o locatário AWS. Vários locatários AWS (representados por entidades de segurança) podem ser adicionados ao Azure AD da galeria para provisionamento; no entanto, há um problema conhecido sem ser capaz de gravar automaticamente todas as funções importadas de várias entidades de segurança do AWS usadas para provisionamento em uma única entidade de segurança usada para logon único. Como alternativa, a [API Microsoft Graph](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta) pode ser usada para extrair todas as appRoles importadas para cada AWS de entidade de segurança em que o provisionamento está configurado. Essas cadeias de caracteres de função podem ser adicionadas subsequentemente ao AWS servicePrincipalName onde o logon único está configurado.

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[11]: ./media/amazon-web-service-tutorial/ic795031.png
[12]: ./media/amazon-web-service-tutorial/ic795032.png
[13]: ./media/amazon-web-service-tutorial/ic795033.png
[14]: ./media/amazon-web-service-tutorial/ic795034.png
[15]: ./media/amazon-web-service-tutorial/ic795035.png
[16]: ./media/amazon-web-service-tutorial/ic795022.png
[17]: ./media/amazon-web-service-tutorial/ic795023.png
[18]: ./media/amazon-web-service-tutorial/ic795024.png
[19]: ./media/amazon-web-service-tutorial/ic795025.png
[32]: ./media/amazon-web-service-tutorial/ic7950251.png
[33]: ./media/amazon-web-service-tutorial/ic7950252.png
[35]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/amazon-web-service-tutorial/ic7950253.png
[36]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_on.png
