---
title: 'Tutorial: Utilizadores autenticados E2E (Linux)'
description: Saiba como usar a autenticação e autorização do App Service para garantir as suas aplicações de serviço de aplicação Linux de ponta a ponta, incluindo acesso a APIs remotos.
keywords: serviço de aplicações, serviço de aplicações do azure, authN, authZ, proteger, segurança, do azure, multicamadas, azure active directory, azure ad
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 08/14/2019
ms.custom: seodec18
ms.openlocfilehash: 71aec33d5afe1a909f460ddae2d5cb0552857fee
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "74688947"
---
# <a name="tutorial-authenticate-and-authorize-users-end-to-end-in-azure-app-service-on-linux"></a>Tutorial: Autenticar e autorizar utilizadores ponto a ponto no Serviço de Aplicações do Azure no Linux

[O Serviço de Aplicações no Linux](app-service-linux-intro.md) fornece um serviço de hospedagem web altamente escalável e auto-remendado utilizando o sistema operativo Linux. Além disso, o Serviço de Aplicações tem suporte incorporado para [autenticação e autorização de utilizadores](../overview-authentication-authorization.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json). Este tutorial mostra como proteger as suas aplicações com a autenticação e autorização do Serviço de Aplicações. Utiliza uma aplicação ASP.NET Core com um front-end Angular.js, mas é apenas para exemplo. A autenticação e autorização do Serviço de Aplicações suportam todos os runtimes de linguagens e pode seguir o tutorial para aprender a aplicá-las na sua linguagem preferida.

O tutorial utiliza a aplicação de exemplo para lhe mostrar como proteger uma aplicação autónoma (em [Ativar a autenticação e autorização em aplicações de back-end](#enable-authentication-and-authorization-for-back-end-app)).

![Autenticação e autorização simples](./media/tutorial-auth-aad/simple-auth.png)

Também lhe mostra como proteger uma aplicação multicamadas ao aceder a uma API de back-end protegida em nome do utilizador autenticado, a partir de [código do servidor](#call-api-securely-from-server-code) e de [código do browser](#call-api-securely-from-browser-code).

![Autenticação e autorização avançadas](./media/tutorial-auth-aad/advanced-auth.png)

Estes são apenas alguns dos cenários possíveis de autenticação e autorização no Serviço de Aplicações. 

Eis uma lista mais completa do que vai aprender no tutorial:

> [!div class="checklist"]
> * Ativar a autenticação e autorização incorporadas
> * Proteger aplicações contra pedidos não autenticados
> * Utilizar o Azure Active Directory como o fornecedor de identidade
> * Aceder a uma aplicação remota em nome do utilizador com sessão iniciada
> * Proteger chamadas de serviço para serviço com a autenticação de tokens
> * Utilizar tokens de acesso a partir do código de servidor
> * Utilizar tokens de acesso a partir do código de cliente (browser)

Pode seguir os passos neste tutorial em macOS, Linux e Windows.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

* [Instale Git](https://git-scm.com/).
* [Instale o .NET Core](https://www.microsoft.com/net/core/).

## <a name="create-local-net-core-app"></a>Criar uma aplicação .NET Core local

Neste passo, vai configurar o projeto .NET Core local. Vai utilizar o mesmo projeto para implementar uma aplicação API de back-end e uma aplicação Web de front-end.

### <a name="clone-and-run-the-sample-application"></a>Clonar e executar a aplicação de exemplo

Execute os seguintes comandos para clonar o repositório de exemplo e executá-lo.

```bash
git clone https://github.com/Azure-Samples/dotnet-core-api
cd dotnet-core-api
dotnet run
```

Navegue para `http://localhost:5000` e experimente adicionar, editar e remover itens de afazeres. 

![API ASP.NET Core em execução localmente](./media/tutorial-auth-aad/local-run.png)

Para parar o ASP.NET Core em qualquer altura, prima `Ctrl+C` no terminal.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="deploy-apps-to-azure"></a>Implementar aplicações no Azure

Neste passo, vai implementar o projeto em duas aplicações do Serviço de Aplicações. Uma é a aplicação de front-end e a outra é a de back-end.

### <a name="create-azure-resources"></a>Criar recursos do Azure

No Cloud Shell, execute os seguintes comandos para criar duas aplicações Web. Substitua `a-z` `0-9` `-` _ \<_ o nome da aplicação frontal>e _ \<o nome da aplicação de back-end>_ por dois nomes de aplicativos globalmente únicos (caracteres válidos são , e ). Para obter mais informações sobre cada comando, consulte [Criar uma aplicação .NET Core no Serviço de Aplicações Azure no Linux](quickstart-dotnetcore.md).

```azurecli-interactive
az group create --name myAuthResourceGroup --location "West Europe"
az appservice plan create --name myAuthAppServicePlan --resource-group myAuthResourceGroup --sku B1 --is-linux
az webapp create --resource-group myAuthResourceGroup --plan myAuthAppServicePlan --name <front-end-app-name> --runtime "dotnetcore|2.0" --deployment-local-git --query deploymentLocalGitUrl
az webapp create --resource-group myAuthResourceGroup --plan myAuthAppServicePlan --name <back-end-app-name> --runtime "dotnetcore|2.0" --deployment-local-git --query deploymentLocalGitUrl
```

> [!NOTE]
> Guarde os URLs das instâncias remotas do Git para as aplicações de front-end e back-end, que são apresentados na saída de `az webapp create`.
>

### <a name="push-to-azure-from-git"></a>Enviar para o Azure a partir do Git

Novamente na _janela de terminal local_, execute os seguintes comandos do Git para implementar na aplicação de back-end. Substitua _ \<a implementaçãoLocalGitUrl-of-back-end-app>_ pelo URL do comando Git que guardou dos [recursos Create Azure](#create-azure-resources). Quando solicitado para obter credenciais por Git Credential Manager, certifique-se de que introduz as [suas credenciais](../deploy-configure-credentials.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)de implantação , e não as credenciais que usa para iniciar sessão no portal Azure.

```bash
git remote add backend <deploymentLocalGitUrl-of-back-end-app>
git push backend master
```

Na janela de terminal do local, execute os seguintes comandos do Git para implementar o mesmo código na aplicação de front-end. Substitua _ \<a implementaçãoLocalGitUrl-of-front-end-app>_ com o URL do comando Git que guardou dos [recursos Create Azure](#create-azure-resources).

```bash
git remote add frontend <deploymentLocalGitUrl-of-front-end-app>
git push frontend master
```

### <a name="browse-to-the-apps"></a>Navegue nas aplicações

Navegue para os seguintes URLs num browser e veja as duas aplicações a funcionar.

```
http://<back-end-app-name>.azurewebsites.net
http://<front-end-app-name>.azurewebsites.net
```

![API ASP.NET Core em execução no Serviço de Aplicações do Azure](./media/tutorial-auth-aad/azure-run.png)

> [!NOTE]
> Se a aplicação se reiniciar, poderá ter reparado que os dados novos foram apagados. Este comportamento é predefinido, porque a aplicação ASP.NET Core de exemplo utiliza uma base de dados dentro da memória.
>
>

## <a name="call-back-end-api-from-front-end"></a>Chamar a API de back-end a partir do front-end

Neste passo, vai apontar o código do servidor da aplicação de front-end para aceder à API de back-end. Posteriormente, vai ativar o acesso autenticado do front-end para o back-end.

### <a name="modify-front-end-code"></a>Modificar o código de front-end

No repositório local, abra _Controllers/TodoController.cs_. No início da `TodoController` aula, adicione as seguintes linhas e substitua _ \<o nome_ da aplicação de back-end>com o nome da sua aplicação de back-end:

```cs
private static readonly HttpClient _client = new HttpClient();
private static readonly string _remoteUrl = "https://<back-end-app-name>.azurewebsites.net";
```

Localize o método `GetAll()` e substitua o código dentro das chavetas por:

```cs
var data = _client.GetStringAsync($"{_remoteUrl}/api/Todo").Result;
return JsonConvert.DeserializeObject<List<TodoItem>>(data);
```

A primeira linha faz uma chamada de `GET /api/Todo` para a aplicação API de back-end.

Em seguida, localize o método `GetById(long id)` e substitua o código dentro das chavetas por:

```cs
var data = _client.GetStringAsync($"{_remoteUrl}/api/Todo/{id}").Result;
return Content(data, "application/json");
```

A primeira linha faz uma chamada de `GET /api/Todo/{id}` para a aplicação API de back-end.

Em seguida, localize o método `Create([FromBody] TodoItem item)` e substitua o código dentro das chavetas por:

```cs
var response = _client.PostAsJsonAsync($"{_remoteUrl}/api/Todo", item).Result;
var data = response.Content.ReadAsStringAsync().Result;
return Content(data, "application/json");
```

A primeira linha faz uma chamada de `POST /api/Todo` para a aplicação API de back-end.

Em seguida, localize o método `Update(long id, [FromBody] TodoItem item)` e substitua o código dentro das chavetas por:

```cs
var res = _client.PutAsJsonAsync($"{_remoteUrl}/api/Todo/{id}", item).Result;
return new NoContentResult();
```

A primeira linha faz uma chamada de `PUT /api/Todo/{id}` para a aplicação API de back-end.

Em seguida, localize o método `Delete(long id)` e substitua o código dentro das chavetas por:

```cs
var res = _client.DeleteAsync($"{_remoteUrl}/api/Todo/{id}").Result;
return new NoContentResult();
```

A primeira linha faz uma chamada de `DELETE /api/Todo/{id}` para a aplicação API de back-end.

Guarde todas as alterações. Na janela de terminal do local, implemente as alterações na aplicação de front-end com os seguintes comandos do Git:

```bash
git add .
git commit -m "call back-end API"
git push frontend master
```

### <a name="check-your-changes"></a>Verificar as alterações

Navegue para `http://<front-end-app-name>.azurewebsites.net` e adicione alguns itens, como `from front end 1` e `from front end 2`.

Navegue para `http://<back-end-app-name>.azurewebsites.net` para ver os itens adicionados a partir da aplicação de front-end. Além disso, adicione alguns itens, como `from back end 1` e `from back end 2` e, depois, atualize a aplicação de front-end para ver se reflete as alterações.

![API ASP.NET Core em execução no Serviço de Aplicações do Azure](./media/tutorial-auth-aad/remote-api-call-run.png)

## <a name="configure-auth"></a>Configurar a autenticação

Neste passo, vai ativar a autenticação e autorização para as duas aplicações. Também vai configurar a aplicação de front-end para gerar um token de acesso que pode utilizar para fazer chamadas autenticadas para a aplicação de back-end.

Vai utilizar o Azure Active Directory como o fornecedor de identidade. Para obter mais informações, veja [Configure Azure Active Directory authentication for your App Services application](../configure-authentication-provider-aad.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) (Configurar a autenticação do Azure Active Directory na sua aplicação dos Serviços de Aplicação).

### <a name="enable-authentication-and-authorization-for-back-end-app"></a>Ativar a autenticação e autorização na aplicação de back-end

No [portal Azure,](https://portal.azure.com)abra a página de gestão da sua aplicação back-end clicando a partir do menu esquerdo: **Grupos** > de recursos**myAuthResourceGroup** > **_\<back-end-app-name>_**.

![API ASP.NET Core em execução no Serviço de Aplicações do Azure](./media/tutorial-auth-aad/portal-navigate-back-end.png)

No menu da esquerda da sua aplicação de back-end, clique em **Autenticação / Autorização** e clique em **Ativado** para ativar a Autenticação do Serviço de Aplicações.

Em **Ação a tomar quando o pedido não é autenticado**, selecione **Iniciar sessão com o Azure Active Directory**.

Em **Fornecedores de Autenticação**, clique em **Azure Active Directory**. 

![API ASP.NET Core em execução no Serviço de Aplicações do Azure](./media/tutorial-auth-aad/configure-auth-back-end.png)

Clique em **Expresso**, aceite as predefinições para criar uma aplicação do AD nova e clique em **OK**.

Na página **Autenticação / Autorização**, clique em **Guardar**. 

Quando vir a notificação com a mensagem `Successfully saved the Auth Settings for <back-end-app-name> App`, atualize a página.

Clique novamente no **Diretório Ativo azure** e, em seguida, clique na **app Azure AD**.

Copie a **identificação** do cliente da aplicação Azure AD para um bloco de notas. Vai precisar deste valor mais à frente.

![API ASP.NET Core em execução no Serviço de Aplicações do Azure](./media/tutorial-auth-aad/get-application-id-back-end.png)

### <a name="enable-authentication-and-authorization-for-front-end-app"></a>Ativar a autenticação e autorização na aplicação de front-end

Siga os mesmos passos para a aplicação de front-end, mas ignore o último. Não precisa da identificação do cliente para a aplicação frontal.

Se preferir, navegue para `http://<front-end-app-name>.azurewebsites.net`. Deve ser agora direcionado para uma página de início de sessão segura. Depois de iniciar sessão, continua sem poder aceder aos dados da aplicação de back-end, porque ainda tem de fazer três coisas:

- Conceder ao front-end o acesso ao back-end
- Configurar o Serviço de Aplicações para devolver um token utilizável
- Utilizar o token no seu código

> [!TIP]
> Se se deparar com erros e reconfigurar as definições de autenticação/autorização da sua aplicação, os tokens no arquivo de tokens poderão não ser regenerados a partir das definições novas. Para garantir que os tokens são regenerados, tem de terminar sessão e reiniciá-la na aplicação. Uma forma fácil de fazê-lo é utilizar o browser no modo privado e fechá-lo e reabri-lo no modo privado depois de alterar as definições nas suas aplicações.

### <a name="grant-front-end-app-access-to-back-end"></a>Conceder ao front-end acesso ao back-end

Agora que ativou a autenticação e autorização em ambas as suas aplicações, cada uma destas está associada a uma aplicação do AD. Neste passo, vai conceder à aplicação de front-end as permissões para aceder ao back-end em nome do utilizador. (Tecnicamente, vai conceder à _aplicação do AD_ de front-end as permissões para aceder à _aplicação do AD_ de back-end em nome do utilizador.)

A partir do menu esquerdo no portal, selecione**Registos** > de Aplicações de **Diretório** > Ativo Azure As aplicações de propriedade**aplicações** > **\<>**  >  **permissões API**.

![API ASP.NET Core em execução no Serviço de Aplicações do Azure](./media/tutorial-auth-aad/add-api-access-front-end.png)

Selecione **Adicionar uma permissão**e, em seguida, selecione **my APIs** > **\<back-end-app-name>**.

Na página de **permissões do Request API** para a aplicação back-end, selecione **permissões delegadas** e **user_impersonation,** em seguida, selecione **Adicionar permissões**.

![API ASP.NET Core em execução no Serviço de Aplicações do Azure](./media/tutorial-auth-aad/select-permission-front-end.png)

### <a name="configure-app-service-to-return-a-usable-access-token"></a>Configurar o Serviço de Aplicações para devolver um token de acesso utilizável

A aplicação frontal tem agora as permissões necessárias para aceder à aplicação back-end como utilizador inscrito. Neste passo, vai configurar a autenticação e autorização do Serviço de Aplicações para lhe dar um token de acesso utilizável para aceder ao back-end. Para este passo, precisa do ID do cliente do back end, que copiou da [autenticação Enable e autorização para aplicação back-end](#enable-authentication-and-authorization-for-back-end-app).

Inicie sessão no [Azure Resource Explorer](https://resources.azure.com). Na parte superior da página, clique em **Leitura/Escrita** para ativar a edição dos seus recursos do Azure.

![API ASP.NET Core em execução no Serviço de Aplicações do Azure](./media/tutorial-auth-aad/resources-enable-write.png)

No navegador esquerdo, clique em **subscrições** > **_\<da sua subscrição>_**  >  **recursosGroup** > **myAuthResourceGroup** > **fornecedores** > **Microsoft.Web** > **sites** > **_\<front-end-app-name>_**  >  **config** > **authsettings**.

Na vista **authsettings**, clique em **Editar**. Desloque-se `additionalLoginParams` para a seguinte corda JSON, utilizando o ID do cliente copiado. 

```json
"additionalLoginParams": ["response_type=code id_token","resource=<back-end-client-id>"],
```

![API ASP.NET Core em execução no Serviço de Aplicações do Azure](./media/tutorial-auth-aad/additional-login-params-front-end.png)

Clique em **PUT** para guardar as definições.

As suas aplicações estão agora configuradas. O front-end está agora pronto para aceder ao back-end com um token de acesso correto.

Para obter informações sobre como configurar o sinal de acesso para outros fornecedores, consulte [fichas](../app-service-authentication-how-to.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#refresh-identity-provider-tokens)do fornecedor de identidade Refresh .

## <a name="call-api-securely-from-server-code"></a>Chamar a API de forma segura a partir do código de servidor

Neste passo, vai ativar o código de servidor modificado anteriormente para fazer chamadas autenticadas para a API de back-end.

A sua aplicação frontal tem agora a permissão necessária e também adiciona o ID do cliente da parte de trás aos parâmetros de login. Por conseguinte, pode obter um token de acesso para a autenticação na aplicação de back-end. O Serviço de Aplicações fornece este token ao código de servidor mediante a injeção de um cabeçalho `X-MS-TOKEN-AAD-ACCESS-TOKEN` em cada pedido autenticado (veja [Retrieve tokens in app code](../app-service-authentication-how-to.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#retrieve-tokens-in-app-code) [Obter tokens no código da aplicação]).

> [!NOTE]
> Estes cabeçalhos são injetados em todas as linguagens suportadas. Pode aceder aos mesmos através da norma padrão de cada linguagem.

No repositório local, abra novamente _Controllers/TodoController.cs_. No construtor `TodoController(TodoContext context)`, adicione o seguinte código:

```cs
public override void OnActionExecuting(ActionExecutingContext context)
{
    base.OnActionExecuting(context);

    _client.DefaultRequestHeaders.Accept.Clear();
    _client.DefaultRequestHeaders.Authorization =
        new AuthenticationHeaderValue("Bearer", Request.Headers["X-MS-TOKEN-AAD-ACCESS-TOKEN"]);
}
```

Este código adiciona o cabeçalho HTTP padrão `Authorization: Bearer <access-token>` a todas as chamadas à API remotas. No pipeline de execução do pedido ASP.NET Core MVC, `OnActionExecuting` é executado imediatamente antes do respetivo método de ação (como `GetAll()`), pelo que, agora, cada uma das chamadas à API apresenta o token de acesso.

Guarde todas as alterações. Na janela de terminal do local, implemente as alterações na aplicação de front-end com os seguintes comandos do Git:

```bash
git add .
git commit -m "add authorization header for server code"
git push frontend master
```

Volte a iniciar sessão em `https://<front-end-app-name>.azurewebsites.net`. Na página do contrato de utilização de dados do utilizador, clique em **Aceitar**.

Deverá ser agora capaz de criar, ler, atualizar e eliminar os dados da aplicação de back-end como antes. A única diferença é que agora ambas as aplicações estão protegidas através da autenticação e autorização do Serviço de Aplicações, incluindo as chamadas de serviço para serviço.

Parabéns! O código de servidor está agora a aceder aos dados do back-end em nome do utilizador autenticado.

## <a name="call-api-securely-from-browser-code"></a>Chamar a API de forma segura a partir do código do browser

Neste passo, vai apontar a aplicação Angular.js de front-end para a API de back-end. Desta forma, aprenderá a obter o token de acesso e a fazer chamadas à API para a aplicação de back-end com o mesmo.

Embora o código do servido tenha acesso aos cabeçalhos dos pedidos, o código do cliente pode aceder a `GET /.auth/me` para obter os mesmos tokens de acesso (veja [Retrieve tokens in app code](../app-service-authentication-how-to.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#retrieve-tokens-in-app-code) [Obter tokens no código da aplicação]).

> [!TIP]
> Esta secção utiliza os métodos HTTP padrão para demonstrar as chamadas HTTP seguras. No entanto, pode utilizar a [Biblioteca de Autenticação do Active Directory (ADAL) para JavaScript](https://github.com/AzureAD/azure-activedirectory-library-for-js) para ajudar a simplificar o padrão da aplicação Angular.js.
>

### <a name="configure-cors"></a>Configurar o CORS

Na Cloud Shell, ative o CORS no URL [`az resource update`](/cli/azure/resource#az-resource-update) do seu cliente utilizando o comando. Substitua _ \<_ o nome da aplicação de trás para a frente>e _ \<_ o nome da aplicação frontal>espaços reservados.

```azurecli-interactive
az resource update --name web --resource-group myAuthResourceGroup --namespace Microsoft.Web --resource-type config --parent sites/<back-end-app-name> --set properties.cors.allowedOrigins="['https://<front-end-app-name>.azurewebsites.net']" --api-version 2015-06-01
```

Este passo não está relacionado com a autenticação e autorização. No entanto, tem de segui-lo para que o browser possa permitir as chamadas à API em vários domínios a partir da aplicação Angular.js. Para obter mais informações, veja [Adicionar a funcionalidade CORS](../app-service-web-tutorial-rest-api.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#add-cors-functionality).

### <a name="point-angularjs-app-to-back-end-api"></a>Apontar a aplicação Angular.js para a API de back-end

No repositório local, abra _wwwroot/index.html_.

Na Linha 51, defina a variável `apiEndpoint` como o URL da aplicação de back-end (`https://<back-end-app-name>.azurewebsites.net`). Substitua>de nome de aplicação de _ \<back-end_ com o nome da aplicação no Serviço de Aplicações.

No repositório local, abra _wwwroot/app/scripts/todoListSvc.js_ e verifique se `apiEndpoint` está anexado a todas as chamadas à API. A sua aplicação Angular.js está agora a chamar as APIs de back-end. 

### <a name="add-access-token-to-api-calls"></a>Adicionar o token de acesso às chamadas à API

Em _wwwroot/app/scripts/todoListSvc.js_, acima da lista de chamadas à API (em cima da linha `getItems : function(){`), adicione a seguinte função à lista:

```javascript
setAuth: function (token) {
    $http.defaults.headers.common['Authorization'] = 'Bearer ' + token;
},
```

Esta função é chamada para definir o cabeçalho `Authorization` predefinido com o token de acesso. Vai chamá-la no próximo passo.

No repositório local, abra _wwwroot/app/scripts/app.js_ e localize o seguinte código:

```javascript
$routeProvider.when("/Home", {
    controller: "todoListCtrl",
    templateUrl: "/App/Views/TodoList.html",
}).otherwise({ redirectTo: "/Home" });
```

Substitua o bloco de código inteiro pelo código abaixo:

```javascript
$routeProvider.when("/Home", {
    controller: "todoListCtrl",
    templateUrl: "/App/Views/TodoList.html",
    resolve: {
        token: ['$http', 'todoListSvc', function ($http, todoListSvc) {
            return $http.get('/.auth/me').then(function (response) {
                todoListSvc.setAuth(response.data[0].access_token);
                return response.data[0].access_token;
            });
        }]
    },
}).otherwise({ redirectTo: "/Home" });
```

A nova alteração adiciona o mapeamento `revolve` que chama `/.auth/me` e define o token de acesso. Confirma que tem o token de acesso antes de instanciar o controlador `todoListCtrl`. Dessa forma, todas as chamadas à API pelo controlador incluem o token.

### <a name="deploy-updates-and-test"></a>Implementar as atualizações e testar

Guarde todas as alterações. Na janela de terminal do local, implemente as alterações na aplicação de front-end com os seguintes comandos do Git:

```bash
git add .
git commit -m "add authorization header for Angular"
git push frontend master
```

Navegue novamente até `https://<front-end-app-name>.azurewebsites.net`. Deverá ser agora capaz de criar, ler, atualizar e eliminar os dados da aplicação de back-end, diretamente na aplicação Angular.js.

Parabéns! O código do cliente está agora a aceder aos dados do back-end em nome do utilizador autenticado.

## <a name="when-access-tokens-expire"></a>Quando o token de acesso expira

O token de acesso expira ao fim de algum tempo. Para obter informações sobre como atualizar os seus tokens de acesso sem exigir que os utilizadores se reatentam com a sua aplicação, consulte [fichas](../app-service-authentication-how-to.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#refresh-identity-provider-tokens)do fornecedor de identidade Refresh .

## <a name="clean-up-resources"></a>Limpar recursos

Nos passos anteriores, criou os recursos do Azure num grupo de recursos. Se achar que não vai precisar destes recursos no futuro, execute o seguinte comando no Cloud Shell para eliminar o grupo de recursos:

```azurecli-interactive
az group delete --name myAuthResourceGroup
```

Este comando pode demorar alguns minutos a ser executado.

<a name="next"></a>
## <a name="next-steps"></a>Passos seguintes

O que aprendeu:

> [!div class="checklist"]
> * Ativar a autenticação e autorização incorporadas
> * Proteger aplicações contra pedidos não autenticados
> * Utilizar o Azure Active Directory como o fornecedor de identidade
> * Aceder a uma aplicação remota em nome do utilizador com sessão iniciada
> * Proteger chamadas de serviço para serviço com a autenticação de tokens
> * Utilizar tokens de acesso a partir do código de servidor
> * Utilizar tokens de acesso a partir do código de cliente (browser)

Avance para o próximo tutorial para aprender a mapear um nome DNS personalizado para a sua aplicação.

> [!div class="nextstepaction"]
> [Mapeie um nome dNS personalizado existente para o Serviço de Aplicações Azure](../app-service-web-tutorial-custom-domain.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
