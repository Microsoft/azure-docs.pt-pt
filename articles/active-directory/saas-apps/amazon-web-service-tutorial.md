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
ms.openlocfilehash: f346c995cbc8be6e609020db799959d873ce89b3
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/10/2019
ms.locfileid: "68944963"
---
# <a name="tutorial-integrate-amazon-web-services-aws-with-azure-active-directory"></a>Tutorial: Integrar o Amazon Web Services (AWS) com Azure Active Directory

Neste tutorial, você aprenderá a integrar o Amazon Web Services (AWS) ao Azure Active Directory (Azure AD). Ao integrar o AWS ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao AWS.
* Habilite seus usuários a serem conectados automaticamente ao AWS com suas contas do Azure AD.
* Gerencie suas contas em um local central, o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

![Diagrama do relacionamento do Azure AD e do AWS](./media/amazon-web-service-tutorial/tutorial_amazonwebservices_image.png)

Você pode configurar vários identificadores para várias instâncias. Por exemplo:

* `https://signin.aws.amazon.com/saml#1`

* `https://signin.aws.amazon.com/saml#2`

Com esses valores, o Azure ad remove o valor **#** de e envia o valor `https://signin.aws.amazon.com/saml` correto como a URL do público no token SAML.

Recomendamos essa abordagem pelos seguintes motivos:

- Cada aplicativo fornece um certificado X509 exclusivo. Cada instância de uma instância de aplicativo AWS pode ter uma data de expiração de certificado diferente, que pode ser gerenciada em uma base de conta de AWS individual. A substituição geral do certificado é mais fácil nesse caso.

- Você pode habilitar o provisionamento de usuário com um aplicativo AWS no Azure AD e, em seguida, nosso serviço busca todas as funções dessa conta do AWS. Você não precisa adicionar ou atualizar manualmente as funções AWS no aplicativo.

- Você pode atribuir o proprietário do aplicativo individualmente para o aplicativo. Essa pessoa pode gerenciar o aplicativo diretamente no Azure AD.

> [!Note]
> Certifique-se de usar apenas um aplicativo de galeria.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do AWS habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste. O AWS dá suporte ao **SP e** ao SSO iniciado pelo IDP.

## <a name="add-aws-from-the-gallery"></a>Adicionar o AWS da Galeria

Para configurar a integração do AWS ao Azure AD, você precisará adicionar o AWS da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta Microsoft pessoal.
1. No painel esquerdo, selecione o serviço **Azure Active Directory** .
1. Vá para **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **Amazon Web Services (AWS)** na caixa de pesquisa.
1. Selecione **Amazon Web Services (AWS)** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Configure e teste o SSO do Azure AD com o AWS usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação vinculada entre um usuário do Azure AD e o usuário relacionado no AWS.

Para configurar e testar o SSO do Azure AD com o AWS, conclua os seguintes blocos de construção:

1. **Configure o SSO do Azure ad** para permitir que seus usuários usem esse recurso.
2. **Configure o AWS** para definir as configurações de SSO no lado do aplicativo.
3. **Crie um usuário de teste do Azure ad** para testar o SSO do Azure AD com B. Simon.
4. **Atribua o usuário de teste do Azure ad** para habilitar B. Simon para usar o SSO do Azure AD.
5. **Crie um usuário de teste do AWS** para ter um equivalente de B. Simon em AWS que esteja vinculado à representação do usuário no Azure AD.
6. **Teste o SSO** para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Amazon Web Services (AWS)** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , selecione o ícone de caneta para a **configuração básica do SAML** para editar as configurações.

   ![Captura de tela da página Configurar logon único com SAML, com o ícone de caneta realçado](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , o aplicativo é pré-configurado e as URLs necessárias já foram preenchidas previamente com o Azure. O usuário precisa salvar a configuração selecionando **salvar**.

5. Quando você estiver configurando mais de uma instância, forneça um valor de identificador. Da segunda instância em diante, use o formato a seguir, incluindo um **#** sinal para especificar um valor SPN exclusivo.

    `https://signin.aws.amazon.com/saml#2`

6. O aplicativo AWS espera as asserções SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão. Selecione o ícone de caneta para abrir a caixa de diálogo atributos de usuário.

    ![Captura de tela da caixa de diálogo atributos de usuário, com o ícone de caneta realçado](common/edit-attribute.png)

7. Além dos atributos anteriores, o aplicativo AWS espera que mais alguns atributos sejam passados de volta na resposta SAML. Na seção **declarações do usuário** na caixa de diálogo **atributos de usuário** , execute as etapas a seguir para adicionar o atributo de token SAML.

    | Nome  | Atributo de origem  | Espaço de Nomes |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | Role            | user.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    | SessionDuration             | "forneça um valor entre 900 segundos (15 minutos) a 43200 segundos (12 horas)" |  https://aws.amazon.com/SAML/Attributes |

    a. Selecione **Adicionar nova declaração** para abrir a caixa de diálogo **gerenciar declarações do usuário** .

    ![Captura de tela da seção declarações do usuário, com Adicionar nova declaração e salvar realçado](common/new-save-attribute.png)

    ![Captura de tela da caixa de diálogo Gerenciar declarações do usuário](common/new-attribute-details.png)

    b. Em **nome**, digite o nome do atributo mostrado para essa linha.

    c. Em **namespace**, digite o valor do namespace mostrado para essa linha.

    d. Em **origem**, selecione **atributo**.

    e. Na lista **atributo de origem** , digite o valor do atributo mostrado para essa linha.

    f. Selecione **Ok**.

    g. Selecione **Guardar**.

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , encontre XML de metadados de **Federação**. Selecione **baixar** para baixar o certificado e salvá-lo no computador.

   ![Captura de tela da seção certificado de autenticação SAML, com download realçado](common/metadataxml.png)

1. Na seção **configurar Amazon Web Services (AWS)** , copie as URLs apropriadas com base em seu requisito.

   ![Captura de tela da seção configurar Amazon Web Services (AWS), com URLs de configuração realçadas](common/copy-configuration-urls.png)

### <a name="configure-aws"></a>Configurar o AWS

1. Em uma janela de navegador diferente, faça logon em seu site de empresa do AWS como administrador.

2. Selecione **AWS página inicial**.

    ![Captura de tela do site da empresa AWS, com o ícone Home do AWS realçado][11]

3. Selecione **Gerenciamento de identidade e acesso**.

    ![Captura de tela da página de serviços AWSs, com IAM realçado][12]

4. Selecione **provedores** > de identidade**criar provedor**.

    ![Captura de tela da página IAM, com provedores de identidade e para criar provedor realçado][13]

5. Na página **Configurar provedor** , execute as seguintes etapas:

    ![Captura de tela do provedor de configuração][14]

    a. Para **tipo de provedor**, selecione **SAML**.

    b. Para **nome do provedor**, digite um nome de provedor (por exemplo: *WAAD*).

    c. Para carregar o **arquivo de metadados** baixado do portal do Azure, selecione **escolher arquivo**.

    d. Selecione a **próxima etapa**.

6. Na página **verificar informações do provedor** , selecione **criar**.

    ![Captura de tela de verificar informações do provedor, com criar realçado][15]

7. Selecione **funções** > **criar função**.

    ![Captura de tela da página funções][16]

8. Na página **criar função** , execute as seguintes etapas:  

    ![Captura de tela da página Criar função][19]

    a. Em **Selecionar tipo de entidade confiável**, selecione **Federação SAML 2,0**.

    b. Em **escolher um provedor saml 2,0**, selecione o **provedor SAML** que você criou anteriormente (por exemplo: *WAAD*).

    c. Selecione **permitir acesso de console de gerenciamento programático e AWS**.
  
    d. Selecione **avançar: Permissões**.

9. Na caixa de diálogo **anexar políticas de permissões** , anexe a política apropriada, de acordo com a sua organização. Em seguida **, selecione Avançar: Examine**.  

    ![Captura de tela da caixa de diálogo política de permissões de anexação][33]

10. Na caixa de diálogo revisar, execute as seguintes etapas:

    ![Captura de tela da caixa de diálogo de revisão][34]

    a. Em **nome da função**, insira o nome da função.

    b. Em **Descrição da função**, insira a descrição.

    c. Selecione **criar função**.

    d. Crie quantas funções forem necessárias e mapeie-as para o provedor de identidade.

11. Use as credenciais de conta de serviço do AWS para buscar as funções da conta AWS no provisionamento de usuário do Azure AD. Para isso, abra a página inicial do console do AWS.

12. Selecione **Serviços**. Em **segurança, identidade & conformidade**, selecione **iam**.

    ![Captura de tela da página inicial do console do AWS, com serviços e IAM realçados](./media/amazon-web-service-tutorial/fetchingrole1.png)

13. Na seção IAM, selecione **políticas**.

    ![Captura de tela da seção IAM, com políticas realçadas](./media/amazon-web-service-tutorial/fetchingrole2.png)

14. Crie uma nova política selecionando **criar política** para buscar as funções da conta AWS no provisionamento de usuário do Azure AD.

    ![Captura de tela da página Criar função, com criar política realçada](./media/amazon-web-service-tutorial/fetchingrole3.png)

15. Crie sua própria política para buscar todas as funções de contas do AWS.

    ![Captura de tela da página Criar política, com JSON realçado](./media/amazon-web-service-tutorial/policy1.png)

    a. Em **criar política**, selecione a guia **JSON** .

    b. No documento de política, adicione o seguinte JSON:

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

    c. Selecione **examinar política** para validar a política.

    ![Captura de tela da página Criar política](./media/amazon-web-service-tutorial/policy5.png)

16. Defina a nova política.

    ![Captura de tela da página Criar política, com campos nome e descrição realçados](./media/amazon-web-service-tutorial/policy2.png)

    a. Para **nome**, insira **AzureAD_SSOUserRole_Policy**.

    b. Para **Descrição**, insira **essa política permitirá buscar as funções de contas AWS**.

    c. Selecione **criar política**.

17. Crie uma nova conta de usuário no serviço IAM AWS.

    a. No console AWS IAM, selecione **usuários**.

    ![Captura de tela do console IAM AWS, com usuários realçados](./media/amazon-web-service-tutorial/policy3.png)

    b. Para criar um novo usuário, selecione **Adicionar usuário**.

    ![Captura de tela do botão Adicionar usuário](./media/amazon-web-service-tutorial/policy4.png)

    c. Na seção **Adicionar usuário** :

    ![Captura de tela da página Adicionar usuário, com o nome de usuário e o tipo de acesso realçados](./media/amazon-web-service-tutorial/adduser1.png)

    * Insira o nome de usuário como **AzureADRoleManager**.

    * Para o tipo de acesso, selecione **acesso programático**. Dessa forma, o usuário pode invocar as APIs e buscar as funções da conta AWS.

    * Selecione **próximas permissões**.

18. Crie uma nova política para este usuário.

    ![Captura de tela de Adicionar usuário](./media/amazon-web-service-tutorial/adduser2.png)

    a. Selecione **anexar políticas existentes diretamente**.

    b. Procure a política recém-criada na seção de filtro **AzureAD_SSOUserRole_Policy**.

    c. Selecione a política e, em seguida **, selecione Avançar: Examine**.

19. Examine a política para o usuário anexado.

    ![Captura de tela da página Adicionar usuário, com Create User realçado](./media/amazon-web-service-tutorial/adduser3.png)

    a. Examine o nome de usuário, o tipo de acesso e a política mapeados para o usuário.

    b. Selecione **criar usuário**.

20. Baixe as credenciais de usuário de um usuário.

    ![Captura de tela de Adicionar usuário](./media/amazon-web-service-tutorial/adduser4.png)

    a. Copie a **ID de chave de acesso** do usuário e a chave de acesso de **segredo**.

    b. Insira essas credenciais na seção provisionamento de usuário do Azure AD para buscar as funções no console do AWS.

    c. Selecione **fechar**.

21. No portal de gerenciamento do Azure AD, no aplicativo AWS, acesse **provisionamento**.

    ![Captura de tela do aplicativo AWS, com provisionamento realçado](./media/amazon-web-service-tutorial/provisioning.png)

22. Insira a chave de acesso e o segredo nos campos **clientsecret** e **token secreto** , respectivamente.

    ![Captura de tela da caixa de diálogo credenciais de administrador](./media/amazon-web-service-tutorial/provisioning1.png)

    a. Insira a chave de acesso do usuário AWS no campo **clientsecret** .

    b. Insira o segredo de usuário AWS no campo **token secreto** .

    c. Selecione **testar conexão**.

    d. Salve a configuração selecionando **salvar**.

23. Na seção **configurações** , para **status de provisionamento**, selecione **ativado**. Em seguida, selecione **Guardar**.

    ![Captura de tela da seção Configurações, com em realçado](./media/amazon-web-service-tutorial/provisioning2.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta seção, você cria um usuário de teste, B. Simon, no portal do Azure.

1. No painel esquerdo na portal do Azure, selecione **Azure Active Directory** > **usuários** > **todos os usuários**.
1. Selecione **novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **usuário** , siga estas etapas:
   
   a. No campo **Nome**, introduza `B.Simon`.  
   b. No campo **nome de usuário** , insira o username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.   
   c. Selecione **Mostrar senha**e anote-a. Em seguida, selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta seção, você habilitará B. Simon para usar o SSO do Azure concedendo acesso ao AWS.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **Amazon Web Services (AWS)** .
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![Captura de tela da seção Gerenciar, com usuários e grupos realçados](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**. Na caixa de diálogo **Adicionar atribuição** , selecione **usuários e grupos**.

    ![Captura de tela de Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon**. Em seguida, escolha **selecionar**.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista. Em seguida, escolha **selecionar**.
1. Na caixa de diálogo **Adicionar atribuição** , selecione **atribuir**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Ao selecionar o bloco AWS no painel de acesso, você deverá entrar automaticamente no AWS para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="known-issues"></a>Problemas conhecidos

 * Na seção **provisionamento** , a subseção **mapeamentos** mostra um "carregando..." Message e nunca exibe os mapeamentos de atributo. O único fluxo de trabalho de provisionamento com suporte hoje é a importação de funções do AWS para o Azure AD para seleção durante uma atribuição de usuário ou de grupo. Os mapeamentos de atributo para isso são predeterminados e não são configuráveis.
 
 * A seção de **provisionamento** só dá suporte à inserção de um conjunto de credenciais para um locatário AWS por vez. Todas as funções importadas são `appRoles` gravadas na Propriedade do [ `servicePrincipal` objeto](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta) do Azure ad para o locatário AWS. 
 
   Vários locatários AWS (representados `servicePrincipals`por) podem ser adicionados ao Azure ad da galeria para provisionamento. Há um problema conhecido, no entanto, sem ser capaz de gravar automaticamente todas as funções importadas de vários AWS `servicePrincipals` usados para provisionamento em um único `servicePrincipal` usado para sso. 
   
   Como alternativa, você pode usar a [API Microsoft Graph](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta) para extrair todas as importadas em cada `appRoles` AWS `servicePrincipal` em que o provisionamento está configurado. Posteriormente, você pode adicionar essas cadeias de caracteres `servicePrincipal` de função ao AWS em que o SSO está configurado.

## <a name="additional-resources"></a>Recursos adicionais

- [Tutoriais para integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

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
