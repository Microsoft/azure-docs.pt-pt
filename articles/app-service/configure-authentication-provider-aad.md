---
title: Configurar a autenticação do Azure AD
description: Saiba como configurar a autenticação do Diretório Ativo Azure como fornecedor de identidade para o seu App Service ou aplicação Funções Azure.
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.topic: article
ms.date: 04/14/2020
ms.custom: seodec18, fasttrack-edit, has-adal-ref
ms.openlocfilehash: c03a7b89fee188d8a22cfb8ddcd73920ce43f43a
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83649151"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-azure-ad-login"></a>Configure o seu app service ou app Funções Azure para usar login Azure AD

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Este artigo mostra-lhe como configurar o Azure App Service ou as Funções Azure para utilizar o Azure Ative Directory (Azure AD) como fornecedor de autenticação.

> [!NOTE]
> O fluxo de definições expressas configura um registo de aplicação AAD V1. Se desejar utilizar o [Diretório Ativo Azure v2.0](../active-directory/develop/v2-overview.md) (incluindo o [MSAL),](../active-directory/develop/msal-overview.md)siga as [instruções avançadas](#advanced)de configuração .

Siga estas boas práticas ao configurar a sua app e autenticação:

- Dê a cada app do App Service as suas próprias permissões e consentimento.
- Configure cada app do App Service com o seu próprio registo.
- Evite a partilha de permissões entre ambientes utilizando registos de aplicações separados para slots de implementação separados. Ao testar um novo código, esta prática pode ajudar a evitar que problemas afetem a aplicação de produção.

> [!NOTE]
> Esta funcionalidade não está atualmente disponível no plano de consumo linux para funções azure

## <a name="configure-with-express-settings"></a><a name="express"> </a>Configure com configurações expressas

> [!NOTE]
> A opção **Express** não está disponível para nuvens governamentais.

1. No [portal Azure,]procure e selecione **Serviços de Aplicações**e, em seguida, selecione a sua aplicação.
2. A partir da navegação à esquerda, **selecione Autenticação / Autorização**  >  **On**.
3. Selecione **Azure Ative Directory**  >  **Express**.

   Se quiser escolher um registo de aplicação existente:

   1. Escolha **a aplicação AD existente**e, em seguida, clique na **aplicação AD Azure**.
   2. Escolha o registo de uma aplicação existente e clique **em OK**.

3. Selecione **OK** para registar a app Service no Diretório Ativo Azure. É criado um novo registo de aplicações.

    ![Configurações expressas em Diretório Ativo Azure](./media/configure-authentication-provider-aad/express-settings.png)

4. (Opcional) Por padrão, o Serviço de Aplicações fornece a autenticação, mas não restringe o acesso autorizado aos conteúdos e APIs do seu site. Tem de autorizar os utilizadores no seu código de aplicações. Para restringir o acesso à aplicação apenas aos utilizadores autenticados pelo Diretório Ativo do Azure, deteteto **a Ação a tomar quando o pedido não for autenticado** para iniciar sessão com o **Diretório Ativo do Azure**. Ao definir esta funcionalidade, a sua aplicação requer que todos os pedidos sejam autenticados. Também redireciona todos os não autenticados para o Diretório Ativo Azure para autenticação.

    > [!CAUTION]
    > Restringir o acesso desta forma aplica-se a todas as chamadas para a sua app, o que pode não ser desejável para aplicações que tenham uma página inicial disponível ao público, como em muitas aplicações de página única. Para tais aplicações, **permitir pedidos anónimos (nenhuma ação)** pode ser preferido, com a aplicação a iniciar o login manualmente em si. Para mais informações, consulte o [fluxo de autenticação](overview-authentication-authorization.md#authentication-flow).
5. Selecione **Guardar**.

## <a name="configure-with-advanced-settings"></a><a name="advanced"> </a>Configurar com configurações avançadas

Pode configurar manualmente as definições de aplicações se pretender utilizar um registo de aplicações de um inquilino Azure AD diferente. Para completar esta configuração personalizada:

1. Crie uma inscrição em Azure AD.
2. Forneça alguns dos dados de registo ao Serviço de Aplicações.

### <a name="create-an-app-registration-in-azure-ad-for-your-app-service-app"></a><a name="register"> </a>Crie um registo de aplicativos no Azure AD para a sua app App Service

Necessitará das seguintes informações quando configurar a sua aplicação de Serviço de Aplicações:

- ID de Cliente
- ID do inquilino
- Segredo do cliente (opcional)
- ID de aplicação URI

Execute os seguintes passos:

1. Inscreva-se no [portal Azure,]procure e selecione **Serviços de Aplicações,** e depois selecione a sua aplicação. Tenha em anotao o **URL**da sua aplicação. Irá usá-lo para configurar o registo da sua aplicação Azure Ative Directory.
1. Selecione Registos de Aplicações **de Diretório Ativo Azure**Novas  >  **App registrations**  >  **inscrições**.
1. Na página **do Registo de inscrição,** insira um **Nome** para o registo da sua aplicação.
1. Em **Redirecione URI,** selecione **Web** e escreva `<app-url>/.auth/login/aad/callback` . Por exemplo, `https://contoso.azurewebsites.net/.auth/login/aad/callback`.
1. Selecione **Criar**.
1. Após a criação do registo da aplicação, copie o ID da **Aplicação (cliente)** e o **ID do Diretório (inquilino)** para mais tarde.
1. **Selecione Autenticação**. Sob **a subvenção Implícita,** ative **fichas de ID** para permitir o openID Connect user sign-ins do Serviço de Aplicações.
1. (Opcional) Selecione **Branding**. No URL da **página inicial,** introduza o URL da sua aplicação App Service e selecione **Guardar**.
1. Selecione Expor um conjunto **API**  >  **Set**. Para aplicativo de inquilino único, colhe no URL da sua app App Service e selecione **Save** e para aplicação multi-inquilino, cola no URL que é baseado em um dos domínios verificados pelo inquilino e, em seguida, selecione **Save**.

   > [!NOTE]
   > Este valor é o **ID DE Aplicação URI** do registo da aplicação. Se a sua aplicação web necessitar de acesso a uma API na nuvem, precisa do **ID URI** da aplicação web quando configurar o recurso cloud App Service. Pode usá-lo, por exemplo, se quiser que o serviço de nuvem conceda explicitamente acesso à aplicação web.

1. Selecione **Adicionar âmbito**.
   1. No **nome Scope,** *introduza user_impersonation*.
   1. Nas caixas de texto, introduza o nome e descrição do âmbito de consentimento que pretende que os utilizadores vejam na página de consentimento. Por exemplo, insira *Aceder à minha aplicação*.
   1. **Selecione Adicionar âmbito**.
1. (Opcional) Para criar um segredo de cliente, selecione **Certificados & segredos**  >  **Novo segredo do cliente**  >  **Adicionar**. Copie o valor secreto do cliente mostrado na página. Não voltará a ser mostrado.
1. (Opcional) Para adicionar **vários URLs**de Resposta, selecione **Autenticação**.

### <a name="enable-azure-active-directory-in-your-app-service-app"></a><a name="secrets"> </a>Ativar o Diretório Ativo Azure na sua app App Service

1. No [portal Azure,]procure e selecione **Serviços de Aplicações**e, em seguida, selecione a sua aplicação.
1. No painel esquerdo, em **Definições,** **selecione Autenticação / Autorização**  >  **Ligado**.
1. (Opcional) Por defeito, a autenticação do Serviço de Aplicações permite o acesso não autenticado à sua aplicação. Para impor a autenticação do utilizador, detete a **Ação a tomar quando o pedido não for autenticado** para iniciar sessão com o **Diretório Ativo Azure**.
1. No âmbito dos **Fornecedores de Autenticação,** selecione **Diretório Ativo Azure**.
1. No **modo de Gestão,** selecione autenticação **avançada** e configure o Serviço de Aplicações de acordo com a tabela seguinte:

    |Campo|Descrição|
    |-|-|
    |ID de Cliente| Utilize a identificação da **Aplicação (cliente)** do registo da aplicação. |
    |Url de emitente| Utilize , e substitua o `<authentication-endpoint>/<tenant-id>/v2.0` ponto final da * \< autenticação>* pelo ponto final de [autenticação para](../active-directory/develop/authentication-national-cloud.md#azure-ad-authentication-endpoints) o seu ambiente em nuvem (por exemplo, https://login.microsoft.com " para o Global Azure), substituindo também * \<>de identificação de inquilinos* pelo ID do **Diretório (inquilino)** no qual foi criado o registo da aplicação. Este valor é utilizado para redirecionar os utilizadores para o inquilino da AD Azure correto, bem como para descarregar os metadados apropriados para determinar as chaves de assinatura simbólicas apropriadas e o valor de reclamação de emitente simbólico, por exemplo. A `/v2.0` secção pode ser omitida para aplicações utilizando AAD v1. |
    |Segredo de Cliente (Opcional)| Use o segredo do cliente gerado no registo da aplicação.|
    |Audiências simbólicas permitidas| Se se trata de uma aplicação de nuvem ou servidor e pretende permitir fichas de autenticação a partir de uma aplicação web, adicione o **ID URI** da aplicação web aqui. O ID do **cliente** configurado é *sempre* considerado implicitamente como um público permitido. |

2. Selecione **OK**, e, em seguida, selecione **Guardar**.

Está agora pronto para utilizar o Diretório Ativo Azure para autenticação na sua aplicação App Service.

## <a name="configure-a-native-client-application"></a>Configurar uma aplicação de cliente nativo

Pode registar clientes nativos para permitir a autenticação na Web API's hospedada na sua aplicação utilizando uma biblioteca de clientes como a Biblioteca de Autenticação de **DirectórioAtivo Ativo.**

1. No [portal Azure,]selecione Registos de Aplicações **de Diretório Ativo**  >  **App registrations**  >  **Novo registo.**
1. Na página **do Registo de inscrição,** insira um **Nome** para o registo da sua aplicação.
1. No **Redirect URI,** selecione **cliente público (mobile & desktop)** e escreva o URL `<app-url>/.auth/login/aad/callback` . Por exemplo, `https://contoso.azurewebsites.net/.auth/login/aad/callback`.

    > [!NOTE]
    > Para uma aplicação da Microsoft Store, utilize o [pacote SID](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) como URI.
1. Selecione **Criar**.
1. Após a criação do registo da aplicação, copie o valor do ID da **Aplicação (cliente).**
1. Selecione **permissões API**  >  **Adicione uma permissão**  >  **As minhas APIs**.
1. Selecione o registo de aplicações que criou anteriormente para a sua aplicação App Service. Se não vir o registo da aplicação, certifique-se de que adicionou o âmbito **de user_impersonation** em [Criar uma inscrição de aplicação no Azure AD para](#register)a sua app App Service .
1. Sob **permissões delegadas,** selecione **user_impersonation**, e, em seguida, selecione **Adicionar permissões**.

Já configurou uma aplicação de cliente nativo que pode aceder à sua aplicação de Serviço de Aplicações em nome de um utilizador.

## <a name="configure-a-daemon-client-application-for-service-to-service-calls"></a>Configure uma aplicação de cliente daemon para chamadas de serviço a serviço

A sua aplicação pode adquirir um símbolo para chamar uma API Web hospedada na sua app De Serviço ou Função de Aplicação (não em nome de um utilizador). Este cenário é útil para aplicações daemon não interativas que executam tarefas sem um registo registado no utilizador. Usa a bolsa padrão de [credenciais](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md) de clientes OAuth 2.0.

1. No [portal Azure,]selecione Registos de Aplicações **de Diretório Ativo**  >  **App registrations**  >  **Novo registo.**
1. Na página do Registo de **uma aplicação,** insira um **Nome** para o registo da sua aplicação daemon.
1. Para uma aplicação daemon, você não precisa de um Uri Redirecion para que possa manter isso vazio.
1. Selecione **Criar**.
1. Após a criação do registo da aplicação, copie o valor do ID da **Aplicação (cliente).**
1. Selecione **Certificados & segredos**  >  **Novo segredo do cliente**  >  **Adicionar**. Copie o valor secreto do cliente mostrado na página. Não voltará a ser mostrado.

Pode agora [solicitar um sinal de acesso utilizando o ID do cliente e o segredo](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md#first-case-access-token-request-with-a-shared-secret) do cliente, definindo o parâmetro para o ID DE `resource` **Aplicação URI** da aplicação alvo. O token de acesso resultante pode então ser apresentado à app-alvo utilizando o cabeçalho de autorização padrão [OAuth 2.0](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md#use-the-access-token-to-access-the-secured-resource), e a Autenticação/Autorização do Serviço de Aplicações validará e utilizará o símbolo como de costume para indicar agora que o chamador (uma aplicação neste caso, não um utilizador) é autenticado.

Atualmente, isto permite que _qualquer_ aplicação de cliente no seu inquilino Azure AD solicite um token de acesso e autenticar a app alvo. Se também pretende impor _autorização_ para permitir apenas determinadas aplicações de clientes, deve realizar alguma configuração adicional.

1. [Defina uma Função de Aplicação](../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md) no manifesto do registo da aplicação que representa o Serviço de Aplicações ou app 'Função' que pretende proteger.
1. No registo da aplicação que representa o cliente que precisa de ser autorizado, selecione **permissões API**  >  **Adicione uma permissão**  >  **As minhas APIs**.
1. Selecione o registo da aplicação que criou anteriormente. Se não vir o registo da aplicação, certifique-se de que [adicionou uma Função de Aplicação](../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md).
1. Sob **permissões de Aplicação,** selecione a Função app que criou anteriormente e, em seguida, selecione **Adicionar permissões**.
1. Certifique-se de clicar no **consentimento do administrador da Grant** para autorizar o pedido do cliente para solicitar a permissão.
1. À semelhança do cenário anterior (antes de serem adicionadas quaisquer funções), pode agora [solicitar um sinal](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md#first-case-access-token-request-with-a-shared-secret) de acesso para o mesmo alvo , e o sinal de acesso incluirá uma `resource` reclamação contendo as Funções de `roles` Aplicação que foram autorizadas para a aplicação do cliente.
1. Dentro do código de aplicação target App Service ou Função, pode agora validar que as funções esperadas estão presentes no token (isto não é executado por Autenticação/Autorização do Serviço de Aplicação). Para mais informações, consulte [as reclamações dos utilizadores](app-service-authentication-how-to.md#access-user-claims)do Access.

Já configurou uma aplicação de cliente daemon que pode aceder à sua aplicação App Service usando a sua própria identidade.

## <a name="next-steps"></a><a name="related-content"> </a>Passos seguintes

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[Portal do Azure]: https://portal.azure.com/
