---
title: Configurar a autenticação do Azure AD
description: Saiba como configurar a autenticação do Azure Ative Directory como fornecedor de identidade para o seu Serviço de Aplicações ou aplicação Azure Functions.
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.topic: article
ms.date: 04/14/2020
ms.custom: seodec18, fasttrack-edit, has-adal-ref
ms.openlocfilehash: 377b7fd44b4f5afa2fd3892d9cb920484bc11c0b
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102509443"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-azure-ad-login"></a>Configure o seu Serviço de Aplicações ou app Azure Functions para usar o login AZure AD

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Este artigo mostra-lhe como configurar a autenticação para o Azure App Service ou para as Funções Azure para que a sua aplicação assine nos utilizadores com o Azure Ative Directory (Azure AD) como fornecedor de autenticação.

Esta funcionalidade não se encontra disponível no plano de Consumo linux para funções Azure.

## <a name="configure-with-express-settings"></a><a name="express"> </a>Configure com configurações expressas

A opção **Express** foi concebida para tornar a autenticação ativada simples e requer apenas alguns cliques.

As definições expressas criarão automaticamente um registo de aplicação que utiliza o ponto final do Azure Ative Directory V1. Para utilizar [o Azure Ative Directory v2.0](../active-directory/develop/v2-overview.md) (incluindo [o MSAL),](../active-directory/develop/msal-overview.md)siga as [instruções avançadas de configuração](#advanced).

> [!NOTE]
> A opção **Express** não está disponível para nuvens governamentais.

Para ativar a autenticação utilizando a opção **Express,** siga estes passos:

1. No [portal Azure,]procure e selecione **Serviços de Aplicações** e, em seguida, selecione a sua aplicação.
2. A partir da navegação à esquerda, selecione **Autenticação / Autorização**  >  **Em**.
3. Selecione **Azure Ative Directory**  >  **Express**.

   Se quiser escolher um registo de aplicações existente:

   1. Escolha **selecionar a aplicação AD existente** e, em seguida, clique na App **AD AZure**.
   2. Escolha um registo de aplicações existente e clique **em OK.**

4. Selecione **OK** para registar a aplicação Serviço de Aplicações no Diretório Ativo Azure. É criado um novo registo de aplicações.

    ![Definições expressas no Diretório Ativo Azure](./media/configure-authentication-provider-aad/express-settings.png)

5. (Opcional) Por predefinição, o Serviço de Aplicações fornece autenticação mas não restringe o acesso autorizado ao conteúdo do seu site e APIs. Tem de autorizar os utilizadores no seu código de aplicação. Para restringir o acesso da aplicação apenas aos utilizadores autenticados pelo Azure Ative Directory, desconfiem **de Ação a tomar quando o pedido não for autenticado** para **iniciar sessão com o Azure Ative Directory**. Ao definir esta funcionalidade, a sua aplicação requer que todos os pedidos sejam autenticados. Também redireciona todos os não autenticados para o Azure Ative Directory para a autenticação.

    > [!CAUTION]
    > Restringir o acesso desta forma aplica-se a todas as chamadas para a sua app, o que pode não ser desejável para aplicações que tenham uma página inicial disponível ao público, como em muitas aplicações de página única. Para tais aplicações, poderá ser preferido **solicitar pedidos anónimos (nenhuma ação),** com a aplicação a iniciar manualmente o próprio login. Para mais informações, consulte [o fluxo de autenticação.](overview-authentication-authorization.md#authentication-flow)
6. Selecione **Guardar**.

Para um exemplo de configurar o login Azure AD para uma aplicação web que acede ao Azure Storage e ao Microsoft Graph, consulte [este tutorial](scenario-secure-app-authentication-app-service.md).

## <a name="configure-with-advanced-settings"></a><a name="advanced"> </a>Configure com configurações avançadas

Para que a Azure AD atue como o fornecedor de autenticação da sua aplicação, tem de registar a sua aplicação com ela. A opção Express faz isto automaticamente. A opção Advanced permite-lhe registar manualmente a sua aplicação, personalizando o registo e introduzindo manualmente os dados de registo no Serviço de Aplicações. Isto é útil, por exemplo, se você quiser usar um registo de aplicação de um inquilino AD Azure diferente daquele em que o seu Serviço de Aplicações está.

### <a name="create-an-app-registration-in-azure-ad-for-your-app-service-app"></a><a name="register"> </a>Crie um registo de aplicações em Azure AD para a sua app App Service

Primeiro, irá criar o seu registo de aplicações. Ao fazê-lo, recolha as seguintes informações de que necessitará mais tarde quando configurar a autenticação na aplicação Serviço de Aplicações:

- ID de Cliente
- ID do inquilino
- Segredo do cliente (opcional)
- ID uri de aplicação

Para registar a aplicação, execute os seguintes passos:

1. Inscreva-se no [portal Azure,]procure e selecione **Serviços de Aplicações** e, em seguida, selecione a sua aplicação. Note o **URL** da sua aplicação. Você vai usá-lo para configurar o seu registo de aplicativo Azure Ative Directory.
1. A partir do menu do portal, selecione **Azure Ative Directory,** em seguida, vá ao separador **registos** da App e selecione **Novo registo**.
1. Na página **registar uma candidatura,** insira um **Nome** para o registo da sua aplicação.
1. In **Redirect URI**, selecione **Web** e type `<app-url>/.auth/login/aad/callback` . Por exemplo, `https://contoso.azurewebsites.net/.auth/login/aad/callback`.
1. Selecione **Registar**.
1. Após a criação do registo da aplicação, copie o **ID da Aplicação (cliente)** e o ID do **Diretório (inquilino)** para mais tarde.
1. Selecione **Autenticação**. Ao abrigo **da concessão implícita**, permita **que os tokens de ID** permitam iniciar ins ins de utilizador OpenID Connect a partir do Serviço de Aplicações.
1. (Opcional) **Selecione Branding**. No **URL da página inicial,** insira o URL da sua aplicação de Serviço de Aplicações e selecione **Save**.
1. Selecione **Expor um** conjunto de  >  **API**. Para uma aplicação de inquilino único, cole no URL da sua app App Service e selecione **Save** and for multi-tenant app, pasta no URL que se baseia num dos domínios verificados pelo inquilino e, em seguida, selecione **Save.**

   > [!NOTE]
   > Este valor é o **ID URI da aplicação.** Se a sua aplicação web necessitar de acesso a uma API na nuvem, precisa do **ID URI** da aplicação web quando configurar o recurso cloud App Service. Pode usar isto, por exemplo, se quiser que o serviço de cloud conceda explicitamente acesso à aplicação web.

1. Selecione **Adicionar âmbito**.
   1. No **nome Scope**, insira *user_impersonation*.
   1. Nas caixas de texto, insira o nome do âmbito de consentimento e a descrição que pretende que os utilizadores vejam na página de consentimento. Por exemplo, insira *o Access my app*.
   1. Selecione **Adicionar âmbito**.
1. (Opcional) Para criar um segredo de cliente, selecione **Certificados & segredos**  >  **Novo cliente Secreto**  >  **Add**. Copie o valor secreto do cliente mostrado na página. Não voltará a ser mostrado.
1. (Opcional) Para adicionar **URLs de resposta múltiplas,** selecione **Autenticação**.

### <a name="enable-azure-active-directory-in-your-app-service-app"></a><a name="secrets"> </a>Ativar o Azure Ative Directory na sua aplicação De Serviço de Aplicações

1. No [portal Azure,]procure e selecione **Serviços de Aplicações** e, em seguida, selecione a sua aplicação.
1. No painel esquerdo, em **Definições,** selecione **Autenticação / Autorização**  >  **Em**.
1. (Opcional) Por predefinição, a autenticação do Serviço de Aplicações permite o acesso não autenticado à sua aplicação. Para impor a autenticação do utilizador, detenha **a ação a tomar quando o pedido não for autenticado** para **iniciar sessão com o Azure Ative Directory**.
1. No âmbito **dos Fornecedores de Autenticação**, selecione **Azure Ative Directory**.
1. No **modo Gestão,** selecione **Autenticação avançada** e configurada do Serviço de Aplicações de acordo com a seguinte tabela:

    |Campo|Descrição|
    |-|-|
    |ID de Cliente| Utilize o ID de **Aplicação (cliente)** do registo da aplicação. |
    |Url emitente| Use `<authentication-endpoint>/<tenant-id>/v2.0` , e *\<authentication-endpoint>* substitua-o pelo ponto final de [autenticação para o seu ambiente em nuvem](../active-directory/develop/authentication-national-cloud.md#azure-ad-authentication-endpoints) (por exemplo, " para a https://login.microsoftonline.com Azure global), substituindo também *\<tenant-id>* pelo ID do **Diretório (inquilino)** no qual foi criado o registo da aplicação. Este valor é usado para redirecionar os utilizadores para o inquilino AZure AD correto, bem como para baixar os metadados apropriados para determinar as chaves de assinatura de token apropriadas e o valor de reclamação do emitente simbólico, por exemplo. Para aplicações que usam Azure AD v1 e para aplicações Azure Functions, `/v2.0` omite no URL.|
    |Segredo do Cliente (Opcional)| Use o segredo do cliente que gerou no registo da aplicação.|
    |Audiências token permitidas| Se se trata de uma aplicação de cloud ou servidor e pretender permitir fichas de autenticação a partir de uma aplicação web, adicione aqui o **ID URI** da aplicação web. O **ID do Cliente** configurado é *sempre* implicitamente considerado como um público permitido. |

2. Selecione **OK** e, em seguida, selecione **Guardar**.

Está agora pronto para usar o Azure Ative Directory para autenticação na sua aplicação de Serviço de Aplicações.

## <a name="configure-client-apps-to-access-your-app-service"></a>Configure as aplicações do cliente para aceder ao seu Serviço de Aplicações

Na secção anterior, registou o seu Serviço de Aplicações ou Função Azure para autenticar os utilizadores. Esta secção explica como registar aplicações de clientes nativos ou daemon para que possam solicitar o acesso às APIs expostas pelo seu Serviço de Aplicações em nome dos utilizadores ou de si próprios. Não é necessário completar os passos nesta secção se pretender apenas autenticar os utilizadores.

### <a name="native-client-application"></a>Aplicação de cliente nativo

Pode registar-se clientes nativos para solicitar acesso às APIs da sua app app em nome de um utilizador assinado.

1. No [portal Azure,]selecione **Ative Directory**  >  **App registrs**  >  **Novo registo**.
1. Na página **registar uma candidatura,** insira um **Nome** para o registo da sua aplicação.
1. Em **Redirecionamento URI**, selecione **cliente público (mobile & desktop)** e digite o URL `<app-url>/.auth/login/aad/callback` . Por exemplo, `https://contoso.azurewebsites.net/.auth/login/aad/callback`.

    > [!NOTE]
    > Para uma aplicação da Microsoft Store, utilize o [pacote SID](/previous-versions/azure/app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library#package-sid) como URI.
1. Selecione **Criar**.
1. Após a criação do registo da aplicação, copie o valor do ID da **Aplicação (cliente).**
1. Selecione **permissões API**  >  **Adicione uma permissão**  >  **As minhas APIs**.
1. Selecione o registo de aplicações que criou anteriormente para a sua aplicação App Service. Se não vir o registo da aplicação, certifique-se de que adicionou o âmbito **user_impersonation** na [Criação de um registo de aplicações em Azure AD para a sua aplicação de Serviço de Aplicações.](#register)
1. Sob **permissões delegadas**, selecione **user_impersonation** e, em seguida, selecione **Permissões de adicionar**.

Agora configura uma aplicação de cliente nativo que pode solicitar o acesso à sua aplicação de Serviço de Aplicações em nome de um utilizador.

### <a name="daemon-client-application-service-to-service-calls"></a>Aplicação do cliente Daemon (chamadas de serviço para serviço)

A sua aplicação pode adquirir um token para chamar uma API web hospedada na sua app De serviço de aplicação ou função em nome de si mesma (não em nome de um utilizador). Este cenário é útil para aplicações daemon não interativas que executam tarefas sem uma sessão registada no utilizador. Utiliza a concessão padrão de [credenciais](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md) de cliente 2.0 da OAuth.

1. No [portal Azure,]selecione **Ative Directory**  >  **App registrs**  >  **Novo registo**.
1. Na página **de inscrição,** insira um **Nome** para o seu registo da aplicação Daemon.
1. Para uma aplicação daemon, você não precisa de um URI redirecionado para que possa mantê-lo vazio.
1. Selecione **Criar**.
1. Após a criação do registo da aplicação, copie o valor do ID da **Aplicação (cliente).**
1. Selecione **Certificados & segredos**  >  **Novo cliente Secreto**  >  **Adicionar**. Copie o valor secreto do cliente mostrado na página. Não voltará a ser mostrado.

Agora pode solicitar um token de [acesso usando o ID do cliente e o segredo](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md#first-case-access-token-request-with-a-shared-secret) do cliente, definindo o parâmetro para o `resource` **ID URI** da aplicação-alvo. O token de acesso resultante pode então ser apresentado à aplicação-alvo utilizando o cabeçalho de [autorização padrão OAuth 2.0](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md#use-the-access-token-to-access-the-secured-resource), e a Autenticação/Autorização do Serviço de Aplicações validará e utilizará o token como de costume para agora indicar que o chamador (uma aplicação neste caso, não um utilizador) é autenticado.

Neste momento, isto permite que _qualquer_ aplicação do cliente no seu inquilino Azure AD solicite um token de acesso e autente para a aplicação-alvo. Se também pretender impor _a autorização_ para permitir apenas determinadas aplicações ao cliente, tem de realizar alguma configuração adicional.

1. [Defina uma Função de Aplicação](../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md) no manifesto do registo da aplicação que representa o Serviço de Aplicações ou aplicação de função que pretende proteger.
1. No registo da aplicação que representa o cliente que precisa de ser autorizado, selecione **permissões API**  >  **Adicione uma permissão**  >  **As Minhas APIs**.
1. Selecione o registo de aplicações que criou anteriormente. Se não vir o registo da aplicação, certifique-se de que [adicionou uma App Role](../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md).
1. Sob **permissões de aplicação**, selecione a Função de Aplicação que criou anteriormente e, em seguida, selecione **permissões Adicionar**.
1. Certifique-se de clicar no **consentimento de administração grant** para autorizar o pedido do cliente para solicitar a permissão.
1. À semelhança do cenário anterior (antes de serem adicionadas quaisquer funções), pode agora [solicitar um token](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md#first-case-access-token-request-with-a-shared-secret) de acesso para o mesmo alvo – e o token de `resource` acesso incluirá uma `roles` reclamação contendo as Funções de Aplicação que foram autorizadas para a aplicação do cliente.
1. Dentro do código de aplicação target App Service ou Function, pode agora validar que as funções esperadas estão presentes no token (isto não é realizado por Autenticação/Autorização do Serviço de Aplicações). Para obter mais informações, consulte [as reclamações do utilizador do Access.](app-service-authentication-how-to.md#access-user-claims)

Agora configuraste uma aplicação para clientes daemon que pode aceder à tua aplicação de Serviço de Aplicações usando a sua própria identidade.

## <a name="best-practices"></a>Melhores práticas

Independentemente da configuração que utiliza para configurar a autenticação, as seguintes boas práticas manterão o seu inquilino e aplicações mais seguras:

- Dê a cada app de Serviço de Aplicações as suas próprias permissões e consentimento.
- Configure cada app do Serviço de Aplicações com o seu próprio registo.
- Evite a partilha de permissão entre ambientes utilizando registos de aplicações separados para slots de implantação separados. Ao testar um novo código, esta prática pode ajudar a evitar que os problemas afetem a aplicação de produção.

## <a name="next-steps"></a><a name="related-content"> </a>Passos seguintes

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]
* [Tutorial: Autenticar e autorizar utilizadores numa aplicação web que acede ao Azure Storage e ao Microsoft Graph](scenario-secure-app-authentication-app-service.md)
* [Tutorial: Autenticar e autorizar utilizadores ponto a ponto no Serviço de Aplicações do Azure](tutorial-auth-aad.md)
<!-- URLs. -->

[Portal do Azure]: https://portal.azure.com/
