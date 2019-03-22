---
title: Criar uma aplicação .NET no Service Fabric no Azure | Microsoft Docs
description: Neste tutorial, irá aprender a criar uma aplicação com um front-end do ASP.NET Core e um back-end com monitorização de estado de Reliable Service, e como implementar a aplicação num cluster.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/14/2019
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: ff6475637b07f50aaea93caac85e65356bc9b96b
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58317209"
---
# <a name="tutorial-create-and-deploy-an-application-with-an-aspnet-core-web-api-front-end-service-and-a-stateful-back-end-service"></a>Tutorial: Criar e implementar uma aplicação com um serviço de front-end de API Web do ASP.NET Core e um serviço de back-end com monitorização de estado

Este tutorial é a primeira parte de uma série.  Ficará a saber como criar uma aplicação do Azure Service Fabric com um front-end de API Web do ASP.NET Core e um serviço de back-end com monitorização de estado para armazenar dados. Quando tiver terminado, terá uma aplicação de votações com um front-end da Web ASP.NET que guarda os resultados das votações num serviço de back-end com estado no cluster. Se não quiser criar manualmente a aplicação de voto, pode [transferir o código de origem](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/) da aplicação concluída e avançar diretamente para o [Guia do exemplo de aplicação de voto](#walkthrough_anchor).  Se preferir, também pode ver um [vídeo passo a passo](https://channel9.msdn.com/Events/Connect/2017/E100) deste tutorial.

![Diagrama da aplicação](./media/service-fabric-tutorial-create-dotnet-app/application-diagram.png)

Na primeira parte da série, saiba como:

> [!div class="checklist"]
> * Criar um serviço de API Web do ASP.NET Core como um Reliable Service com monitorização de estado
> * Criar um serviço de Aplicação Web do ASP.NET Core como um serviço Web sem monitorização de estado
> * Utilizar o proxy inverso para comunicar com o serviço com estado

Nesta série de tutoriais, ficará a saber como:
> [!div class="checklist"]
> * Criar uma aplicação .NET do Service Fabric
> * [Implementar a aplicação num cluster remoto](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * [Adicionar um ponto final HTTPS a um serviço de front-end ASP.NET Core](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
> * [Configurar CI/CD com o Azure Pipelines](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * [Configurar a monitorização e os diagnósticos da aplicação](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial:
* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Instale o Visual Studio 2017](https://www.visualstudio.com/), versão 15.5 ou posterior, com as cargas de trabalho de **desenvolvimento no Azure** e de **desenvolvimento Web e em ASP.NET**.
* [Instale o SDK do Service Fabric](service-fabric-get-started.md)

## <a name="create-an-aspnet-web-api-service-as-a-reliable-service"></a>Criar um serviço de API Web do ASP.NET como um Reliable Service

Em primeiro lugar, crie o front-end Web da aplicação de voto com o ASP.NET Core. O ASP.NET Core é uma arquitetura de desenvolvimento Web simples para várias plataformas que pode utilizar para criar uma IU Web e APIs Web modernas. Para obter uma compreensão abrangente de como o ASP.NET Core se integra com o Service Fabric, recomendamos vivamente que leia o artigo [ASP.NET Core no Reliable Services do Service Fabric](service-fabric-reliable-services-communication-aspnetcore.md). Por agora, pode seguir este tutorial para começar a trabalhar rapidamente. Para saber mais sobre o ASP.NET Core, veja a [Documentação do ASP.NET Core](https://docs.microsoft.com/aspnet/core/).

1. Inicie o Visual Studio como **administrador**.

2. Crie um projeto em **Ficheiro**->**Novo**->**Projeto**.

3. Na caixa de diálogo **Novo Projeto**, escolha **Cloud > Aplicação do Service Fabric**.

4. Dê o nome **Voto** à aplicação e clique em **OK**.

   ![Caixa de diálogo de novo projeto no Visual Studio](./media/service-fabric-tutorial-create-dotnet-app/new-project-dialog.png)

5. Na página **Novo Serviço do Service Fabric**, escolha **ASP.NET Core Sem Monitorização de Estado** e dê o nome **VotingWeb** ao seu serviço e clique em **OK**.
   
   ![Escolher o serviço Web do ASP.NET na caixa de diálogo do novo serviço](./media/service-fabric-tutorial-create-dotnet-app/new-project-dialog-2.png) 

6. A página seguinte fornece um conjunto de modelos de projeto do ASP.NET Core. Para este tutorial, escolha **Aplicação Web (Model-View-Controller)** e, em seguida, clique em **OK**.
   
   ![Escolher o tipo de projeto ASP.NET](./media/service-fabric-tutorial-create-dotnet-app/vs-new-aspnet-project-dialog.png)

   O Visual Studio cria uma aplicação e um projeto de serviço e apresenta-os no Explorador de Soluções.

   ![Explorador de Soluções após a criação da aplicação com o serviço de API Web do ASP.NET Core]( ./media/service-fabric-tutorial-create-dotnet-app/solution-explorer-aspnetcore-service.png)

### <a name="update-the-sitejs-file"></a>Atualizar o ficheiro site.js
Abra **wwwroot/js/site.js**.  Substitua o respetivo conteúdo pelo seguinte JavaScript utilizado pelas vistas de Início e guarde as alterações.

```javascript
var app = angular.module('VotingApp', ['ui.bootstrap']);
app.run(function () { });

app.controller('VotingAppController', ['$rootScope', '$scope', '$http', '$timeout', function ($rootScope, $scope, $http, $timeout) {

    $scope.refresh = function () {
        $http.get('api/Votes?c=' + new Date().getTime())
            .then(function (data, status) {
                $scope.votes = data;
            }, function (data, status) {
                $scope.votes = undefined;
            });
    };

    $scope.remove = function (item) {
        $http.delete('api/Votes/' + item)
            .then(function (data, status) {
                $scope.refresh();
            })
    };

    $scope.add = function (item) {
        var fd = new FormData();
        fd.append('item', item);
        $http.put('api/Votes/' + item, fd, {
            transformRequest: angular.identity,
            headers: { 'Content-Type': undefined }
        })
            .then(function (data, status) {
                $scope.refresh();
                $scope.item = undefined;
            })
    };
}]);
```

### <a name="update-the-indexcshtml-file"></a>Atualizar o ficheiro Index.cshtml

Abra **Views/Home/Index.cshtml**, a vista específica do controlador Home Page.  Substitua o respetivo conteúdo pelo seguinte e, em seguida, guarde as alterações.

```html
@{
    ViewData["Title"] = "Service Fabric Voting Sample";
}

<div ng-controller="VotingAppController" ng-init="refresh()">
    <div class="container-fluid">
        <div class="row">
            <div class="col-xs-8 col-xs-offset-2 text-center">
                <h2>Service Fabric Voting Sample</h2>
            </div>
        </div>

        <div class="row">
            <div class="col-xs-8 col-xs-offset-2">
                <form class="col-xs-12 center-block">
                    <div class="col-xs-6 form-group">
                        <input id="txtAdd" type="text" class="form-control" placeholder="Add voting option" ng-model="item"/>
                    </div>
                    <button id="btnAdd" class="btn btn-default" ng-click="add(item)">
                        <span class="glyphicon glyphicon-plus" aria-hidden="true"></span>
                        Add
                    </button>
                </form>
            </div>
        </div>

        <hr/>

        <div class="row">
            <div class="col-xs-8 col-xs-offset-2">
                <div class="row">
                    <div class="col-xs-4">
                        Click to vote
                    </div>
                </div>
                <div class="row top-buffer" ng-repeat="vote in votes.data">
                    <div class="col-xs-8">
                        <button class="btn btn-success text-left btn-block" ng-click="add(vote.key)">
                            <span class="pull-left">
                                {{vote.key}}
                            </span>
                            <span class="badge pull-right">
                                {{vote.value}} Votes
                            </span>
                        </button>
                    </div>
                    <div class="col-xs-4">
                        <button class="btn btn-danger pull-right btn-block" ng-click="remove(vote.key)">
                            <span class="glyphicon glyphicon-remove" aria-hidden="true"></span>
                            Remove
                        </button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>
```

### <a name="update-the-layoutcshtml-file"></a>Atualizar o ficheiro _Layout.cshtml

Abra **Views/Shared/_Layout.cshtml**, o esquema predefinido da aplicação ASP.NET.  Substitua o respetivo conteúdo pelo seguinte e, em seguida, guarde as alterações.


```html
<!DOCTYPE html>
<html ng-app="VotingApp" xmlns:ng="https://angularjs.org">
<head>
    <meta charset="utf-8"/>
    <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
    <title>@ViewData["Title"]</title>

    <link href="~/lib/bootstrap/dist/css/bootstrap.css" rel="stylesheet"/>
    <link href="~/css/site.css" rel="stylesheet"/>

</head>
<body>
<div class="container body-content">
    @RenderBody()
</div>

<script src="~/lib/jquery/dist/jquery.js"></script>
<script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/angular.js/1.7.2/angular.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/angular-ui-bootstrap/2.5.0/ui-bootstrap-tpls.js"></script>
<script src="~/js/site.js"></script>

@RenderSection("Scripts", required: false)
</body>
</html>
```

### <a name="update-the-votingwebcs-file"></a>Atualizar o ficheiro VotingWeb.cs

Abra o ficheiro *VotingWeb.cs*, o qual cria o WebHost do ASP.NET Core dentro do serviço sem estado através do servidor Web WebListener.

Adicione a diretiva `using System.Net.Http;` à parte superior do ficheiro.

Substitua a função `CreateServiceInstanceListeners()` pelo código seguinte e, em seguida, guarde as alterações.

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(
            serviceContext =>
                new KestrelCommunicationListener(
                    serviceContext,
                    "ServiceEndpoint",
                    (url, listener) =>
                    {
                        ServiceEventSource.Current.ServiceMessage(serviceContext, $"Starting Kestrel on {url}");

                        return new WebHostBuilder()
                            .UseKestrel()
                            .ConfigureServices(
                                services => services
                                    .AddSingleton<HttpClient>(new HttpClient())
                                    .AddSingleton<FabricClient>(new FabricClient())
                                    .AddSingleton<StatelessServiceContext>(serviceContext))
                            .UseContentRoot(Directory.GetCurrentDirectory())
                            .UseStartup<Startup>()
                            .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                            .UseUrls(url)
                            .Build();
                    }))
    };
}
```

Adicione também o seguinte método `GetVotingDataServiceName` abaixo `CreateServiceInstanceListeners()` e, em seguida, guarde as alterações. `GetVotingDataServiceName` devolve o nome do serviço quando consultado.

```csharp
internal static Uri GetVotingDataServiceName(ServiceContext context)
{
    return new Uri($"{context.CodePackageActivationContext.ApplicationName}/VotingData");
}
```

### <a name="add-the-votescontrollercs-file"></a>Adicionar o ficheiro VotesController.cs

Adicione um controlador, o que define as ações de voto. Clique com o botão direito do rato na pasta **Controladores** e, em seguida, selecione **Adicionar->Novo item->Visual C#->ASP.NET Core->Classe**. Dê ao ficheiro o nome **VotesController.cs** e clique em **Adicionar**.  

Substitua o conteúdo do ficheiro *VotesController.cs* pelo seguinte e, em seguida, guarde as alterações.  Mais adiante, em [Atualizar o ficheiro VotesController.cs](#updatevotecontroller_anchor), este ficheiro é modificado para ler e escrever dados de voto do serviço de back-end.  Por agora, o controlador devolve dados de cadeia estática à vista.

```csharp
namespace VotingWeb.Controllers
{
    using System;
    using System.Collections.Generic;
    using System.Fabric;
    using System.Fabric.Query;
    using System.Linq;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc;
    using Newtonsoft.Json;

    [Produces("application/json")]
    [Route("api/Votes")]
    public class VotesController : Controller
    {
        private readonly HttpClient httpClient;

        public VotesController(HttpClient httpClient)
        {
            this.httpClient = httpClient;
        }

        // GET: api/Votes
        [HttpGet]
        public async Task<IActionResult> Get()
        {
            List<KeyValuePair<string, int>> votes= new List<KeyValuePair<string, int>>();
            votes.Add(new KeyValuePair<string, int>("Pizza", 3));
            votes.Add(new KeyValuePair<string, int>("Ice cream", 4));

            return Json(votes);
        }
     }
}
```

### <a name="configure-the-listening-port"></a>Configurar a porta de escuta

Quando o serviço de front-end VotingWeb é criado, o Visual Studio seleciona aleatoriamente uma porta na qual o serviço escuta.  O serviço VotingWeb atua como front-end desta aplicação e aceita tráfego externo, pelo que vamos vincular este serviço a uma porta fixa e bem conhecida.  O [manifesto do serviço](service-fabric-application-and-service-manifests.md) declara os pontos finais de serviço.

No Explorador de Soluções, abra *VotingWeb/PackageRoot/ServiceManifest.xml*.  Localize o elemento **Ponto Final** na secção **Recursos** e altere o valor de **Porta** para **8080**. Para implementar e executar a aplicação localmente, a porta de escuta da aplicação tem de estar aberta e disponível no seu computador.

```xml
<Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" Port="8080" />
    </Endpoints>
  </Resources>
```

Também tem de atualizar o valor da propriedade de URL da Aplicação no projeto Voto para que um browser se abra para a porta correta quando depura a sua aplicação.  No Explorador de Soluções, selecione o projeto **Voto** e atualize a propriedade **URL da Aplicação** para **8080**.

### <a name="deploy-and-run-the-voting-application-locally"></a>Implementar e executar a aplicação de Voto localmente
Agora, já pode executar a aplicação de Voto para depuração. No Visual Studio, prima **F5** para implementar a aplicação para o seu cluster do Service Fabric local no modo de depuração. A aplicação irá falhar se, anteriormente, não tiver aberto o Visual Studio como **administrador**.

> [!NOTE]
> Da primeira vez que executar e implementar a aplicação localmente, o Visual Studio cria um cluster do Service Fabric local para depuração.  A criação do cluster pode demorar algum tempo. O estado da criação do cluster aparece na janela de saída do Visual Studio.

Depois de implementar a aplicação de Voto no seu cluster do Service Fabric local, a sua aplicação Web irá abrir automaticamente num separador do browser e deve ser semelhante ao seguinte:

![Front-end do ASP.NET Core](./media/service-fabric-tutorial-create-dotnet-app/debug-front-end.png)

Para parar a depuração da aplicação, volte ao Visual Studio e prima **Shift+F5**.

## <a name="add-a-stateful-back-end-service-to-your-application"></a>Adicionar um serviço de back-end com monitorização de estado à sua aplicação

Agora que tem um serviço de API Web do ASP.NET em execução na aplicação, adicione um Reliable Service com monitorização de estado para armazenar alguns dados na aplicação.

O Service Fabric permite-lhe armazenar de forma consistente e fiável os seus dados diretamente dentro do seu serviço através das Reliable Collections. As Reliable Collections são um conjunto de classes de Reliable Collections de elevada disponibilidade que qualquer pessoa que tenha utilizado coleções C# conhecerá.

Neste tutorial, irá criar um serviço que armazena um valor de contador numa Reliable Collection.

1. No Explorador de Soluções, clique com o botão direito do rato em **Serviços** no projeto da aplicação de Voto e escolha **Adicionar -> Novo Serviço do Service Fabric...**.
    
2. Na caixa de diálogo **Novo Serviço do Service Fabric**, escolha **ASP.NET Core com Monitorização de Estado** e dê ao serviço o nome **VotingData** e prima **OK**.

    Uma vez criado o projeto de serviço, terá dois serviços na sua aplicação. À medida que continua a criar a sua aplicação, pode adicionar mais serviços com o mesmo método. Pode dar uma versão e atualizar cada serviço de forma independente.

3. A página seguinte fornece um conjunto de modelos de projeto do ASP.NET Core. Para este tutorial, escolha **API**.

    O Visual Studio cria um projeto de serviço VotingData e apresenta-o no Explorador de Soluções.

    ![Explorador de Soluções](./media/service-fabric-tutorial-create-dotnet-app/solution-explorer-aspnetcore-webapi-service.png)

### <a name="add-the-votedatacontrollercs-file"></a>Adicionar o ficheiro VoteDataController.cs

No projeto **VotingData**, clique com o botão direito do rato na pasta **Controladores** e, em seguida, selecione **Adicionar->Novo item->Classe**. Dê ao ficheiro o nome **VoteDataController.cs** e clique em **Adicionar**. Substitua o conteúdo do ficheiro pelo seguinte e, em seguida, guarde as alterações.

```csharp
namespace VotingData.Controllers
{
    using System.Collections.Generic;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.ServiceFabric.Data;
    using Microsoft.ServiceFabric.Data.Collections;

    [Route("api/[controller]")]
    public class VoteDataController : Controller
    {
        private readonly IReliableStateManager stateManager;

        public VoteDataController(IReliableStateManager stateManager)
        {
            this.stateManager = stateManager;
        }

        // GET api/VoteData
        [HttpGet]
        public async Task<IActionResult> Get()
        {
            CancellationToken ct = new CancellationToken();

            IReliableDictionary<string, int> votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

            using (ITransaction tx = this.stateManager.CreateTransaction())
            {
                Microsoft.ServiceFabric.Data.IAsyncEnumerable<KeyValuePair<string, int>> list = await votesDictionary.CreateEnumerableAsync(tx);

                Microsoft.ServiceFabric.Data.IAsyncEnumerator<KeyValuePair<string, int>> enumerator = list.GetAsyncEnumerator();

                List<KeyValuePair<string, int>> result = new List<KeyValuePair<string, int>>();

                while (await enumerator.MoveNextAsync(ct))
                {
                    result.Add(enumerator.Current);
                }

                return this.Json(result);
            }
        }

        // PUT api/VoteData/name
        [HttpPut("{name}")]
        public async Task<IActionResult> Put(string name)
        {
            IReliableDictionary<string, int> votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

            using (ITransaction tx = this.stateManager.CreateTransaction())
            {
                await votesDictionary.AddOrUpdateAsync(tx, name, 1, (key, oldvalue) => oldvalue + 1);
                await tx.CommitAsync();
            }

            return new OkResult();
        }

        // DELETE api/VoteData/name
        [HttpDelete("{name}")]
        public async Task<IActionResult> Delete(string name)
        {
            IReliableDictionary<string, int> votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

            using (ITransaction tx = this.stateManager.CreateTransaction())
            {
                if (await votesDictionary.ContainsKeyAsync(tx, name))
                {
                    await votesDictionary.TryRemoveAsync(tx, name);
                    await tx.CommitAsync();
                    return new OkResult();
                }
                else
                {
                    return new NotFoundResult();
                }
            }
        }
    }
}
```

## <a name="connect-the-services"></a>Ligar os serviços

Neste passo, irá ligar os dois serviços e preparar a aplicação Web do front-end para receber e definir informações de voto do serviço de back-end.

O Service Fabric garante flexibilidade total na como comunica com o Reliable Services. Dentro de uma única aplicação, poderá ter serviços acessíveis através de TCP. Outros serviços poderão ser acessíveis através de uma API REST HTTP e outros serviços ainda poderão ser acessíveis através de sockets Web. Para obter informações gerais sobre as opções disponíveis e sobre as desvantagens existentes, veja [Comunicar com os serviços](service-fabric-connect-and-communicate-with-services.md).

Este tutorial utiliza a [API Web do ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md) e o [proxy inverso do Service Fabric](service-fabric-reverseproxy.md), para que o serviço Web de front-end VotingWeb possa comunicar com o serviço VotingData de back-end. O proxy inverso está configurado por predefinição para utilizar a porta 19081 e deve funcionar para este tutorial. A porta de proxy inverso está definida no modelo do Azure Resource Manager utilizado para configurar o cluster. Para encontrar a porta utilizada, procure no modelo de clusters, no recurso **Microsoft.ServiceFabric/clusters**: 

```json
"nodeTypes": [
          {
            ...
            "httpGatewayEndpointPort": "[variables('nt0fabricHttpGatewayPort')]",
            "isPrimary": true,
            "vmInstanceCount": "[parameters('nt0InstanceCount')]",
            "reverseProxyEndpointPort": "[parameters('SFReverseProxyPort')]"
          }
        ],
```
Para localizar a porta de proxy inverso utilizada no seu cluster de desenvolvimento local, veja a **HttpApplicationGatewayEndpoint** elemento no manifesto do cluster de Service Fabric local:
1. Abra uma janela do browser e navegue para o http:\//localhost:19080 para abrir a ferramenta de Service Fabric Explorer.
2. Selecione **Cluster -> manifesto**.
3. Anote a porta do elemento HttpApplicationGatewayEndpoint. Por predefinição, esta deve ser 19081. Se não for 19081, terá de alterar a porta no método GetProxyAddress do código VotesController.cs seguinte.

<a id="updatevotecontroller" name="updatevotecontroller_anchor"></a>

### <a name="update-the-votescontrollercs-file"></a>Atualizar o ficheiro VotesController.cs

No projeto **VotingWeb**, abra o ficheiro *Controllers/VotesController.cs*.  Substitua o conteúdo da definição de classe `VotesController` pelo seguinte e, em seguida, guarde as alterações. Se a porta do proxy inverso que detetou no passo anterior não for 19081, altere a porta utilizada no método GetProxyAddress de 19081 para a porta que detetou.

```csharp
public class VotesController : Controller
{
    private readonly HttpClient httpClient;
    private readonly FabricClient fabricClient;
    private readonly StatelessServiceContext serviceContext;

    public VotesController(HttpClient httpClient, StatelessServiceContext context, FabricClient fabricClient)
    {
        this.fabricClient = fabricClient;
        this.httpClient = httpClient;
        this.serviceContext = context;
    }

    // GET: api/Votes
    [HttpGet("")]
    public async Task<IActionResult> Get()
    {
        Uri serviceName = VotingWeb.GetVotingDataServiceName(this.serviceContext);
        Uri proxyAddress = this.GetProxyAddress(serviceName);

        ServicePartitionList partitions = await this.fabricClient.QueryManager.GetPartitionListAsync(serviceName);

        List<KeyValuePair<string, int>> result = new List<KeyValuePair<string, int>>();

        foreach (Partition partition in partitions)
        {
            string proxyUrl =
                $"{proxyAddress}/api/VoteData?PartitionKey={((Int64RangePartitionInformation) partition.PartitionInformation).LowKey}&PartitionKind=Int64Range";

            using (HttpResponseMessage response = await this.httpClient.GetAsync(proxyUrl))
            {
                if (response.StatusCode != System.Net.HttpStatusCode.OK)
                {
                    continue;
                }

                result.AddRange(JsonConvert.DeserializeObject<List<KeyValuePair<string, int>>>(await response.Content.ReadAsStringAsync()));
            }
        }

        return this.Json(result);
    }

    // PUT: api/Votes/name
    [HttpPut("{name}")]
    public async Task<IActionResult> Put(string name)
    {
        Uri serviceName = VotingWeb.GetVotingDataServiceName(this.serviceContext);
        Uri proxyAddress = this.GetProxyAddress(serviceName);
        long partitionKey = this.GetPartitionKey(name);
        string proxyUrl = $"{proxyAddress}/api/VoteData/{name}?PartitionKey={partitionKey}&PartitionKind=Int64Range";

        StringContent putContent = new StringContent($"{{ 'name' : '{name}' }}", Encoding.UTF8, "application/json");
        putContent.Headers.ContentType = new MediaTypeHeaderValue("application/json");

        using (HttpResponseMessage response = await this.httpClient.PutAsync(proxyUrl, putContent))
        {
            return new ContentResult()
            {
                StatusCode = (int) response.StatusCode,
                Content = await response.Content.ReadAsStringAsync()
            };
        }
    }

    // DELETE: api/Votes/name
    [HttpDelete("{name}")]
    public async Task<IActionResult> Delete(string name)
    {
        Uri serviceName = VotingWeb.GetVotingDataServiceName(this.serviceContext);
        Uri proxyAddress = this.GetProxyAddress(serviceName);
        long partitionKey = this.GetPartitionKey(name);
        string proxyUrl = $"{proxyAddress}/api/VoteData/{name}?PartitionKey={partitionKey}&PartitionKind=Int64Range";

        using (HttpResponseMessage response = await this.httpClient.DeleteAsync(proxyUrl))
        {
            if (response.StatusCode != System.Net.HttpStatusCode.OK)
            {
                return this.StatusCode((int) response.StatusCode);
            }
        }

        return new OkResult();
    }


    /// <summary>
    /// Constructs a reverse proxy URL for a given service.
    /// Example: http://localhost:19081/VotingApplication/VotingData/
    /// </summary>
    /// <param name="serviceName"></param>
    /// <returns></returns>
    private Uri GetProxyAddress(Uri serviceName)
    {
        return new Uri($"http://localhost:19081{serviceName.AbsolutePath}");
    }

    /// <summary>
    /// Creates a partition key from the given name.
    /// Uses the zero-based numeric position in the alphabet of the first letter of the name (0-25).
    /// </summary>
    /// <param name="name"></param>
    /// <returns></returns>
    private long GetPartitionKey(string name)
    {
        return Char.ToUpper(name.First()) - 'A';
    }
}
```

<a id="walkthrough" name="walkthrough_anchor"></a>

## <a name="walk-through-the-voting-sample-application"></a>Percorrer a aplicação de votação de exemplo

A aplicação de votação é composta por dois serviços:

* Serviço de front-end da Web (VotingWeb) - um serviço de front-end da Web ASP.NET Core, que serve a página Web e expõe as APIs da Web para comunicar com o serviço de back-end.
* Serviço de back-end (VotingData) - um serviço Web ASP.NET Core, que expõe uma API para armazenar os resultados da votação num dicionário fiável que é persistido no disco.

![Diagrama da aplicação](./media/service-fabric-tutorial-create-dotnet-app/application-diagram.png)

Quando vota na aplicação, ocorrem os seguintes eventos:

1. Um JavaScript envia o pedido de voto para a API Web no serviço de front-end Web como pedido HTTP PUT.

2. O serviço de front-end da Web utiliza um proxy para localizar e reencaminhar um pedido HTTP PUT para o serviço de back-end.

3. O serviço de back-end recebe o pedido de entrada e armazena os resultados atualizados num dicionário fiável, que é replicado para vários nós dentro do cluster e persistido no disco. Todos os dados da aplicação são armazenados no cluster, pelo que não é necessária uma base de dados.

## <a name="debug-in-visual-studio"></a>Depurar no Visual Studio

Ao depurar a aplicação no Visual Studio, vai utilizar um cluster de desenvolvimento local do Service Fabric. Tem a opção de ajustar a sua experiência de depuração para o seu cenário. Nesta aplicação, armazene dados no serviço de back-end através de um dicionário fiável. O Visual Studio remove a aplicação por predefinição, quando para o depurador. Remover a aplicação faz com que os dados no serviço de back-end sejam também removidos. Para persistir os dados entre as sessões de depuração, pode alterar o **Modo de Depuração da Aplicação** como propriedade no projeto **Voting** no Visual Studio.

Para ver o que acontece no código, conclua os passos seguintes:

1. Abra o **VotingWeb\VotesController.cs** de ficheiros e defina um ponto de interrupção da API web **colocar** método (linha 72).

2. Abra o **VotingData\VoteDataController.cs** de ficheiros e definir um ponto de interrupção desta web API **colocar** método (linha 54).

3. Prima **F5** para iniciar a aplicação no modo de depuração.

4. Regresse ao browser e clique numa opção de votação ou adicione uma opção nova. Atingiu o primeiro ponto de interrupção no controlador de API do front-end da Web.
    

   1. É aqui que o JavaScript no browser envia um pedido para o controlador de API Web no serviço do front-end.

      ![Adicionar Serviço de Front-End de Votação](./media/service-fabric-tutorial-create-dotnet-app/addvote-frontend.png)

   2. Primeiro, construa o URL para o ReverseProxy para o serviço de back-end **(1)**.
   3. Em seguida, envie o Pedido HTTP PUT para ReverseProxy **(2)**.
   4. Por último, devolva a resposta do serviço de back-end ao cliente **(3)**.

5. Prima **F5** para continuar.
   1. Está agora no ponto de interrupção do serviço de back-end.

      ![Adicionar Serviço de Back-End de Votação](./media/service-fabric-tutorial-create-dotnet-app/addvote-backend.png)

   2. Na primeira linha no método **(1)**, utilize o `StateManager` para obter ou adicionar um dicionário fiável chamado `counts`.
   3. Todas as interações com os valores num dicionário fiável requer uma transação; a utilização da declaração **(2)** cria essa transação.
   4. Na transação, atualize o valor da chave relevante para a opção de voto e consolide a operação **(3)**. Quando é devolvido o método de consolidação, os dados são atualizados no dicionário e replicados para outros nós do cluster. Os dados estão agora armazenados em segurança no cluster e o serviço de back-end pode fazer a ativação pós-falha para outros nós, mantendo os dados disponíveis.
6. Prima **F5** para continuar.

Para parar a sessão de depuração, prima **Shift + F5**.

## <a name="next-steps"></a>Passos seguintes

Nesta parte do tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar um serviço de API Web do ASP.NET Core como um Reliable Service com monitorização de estado
> * Criar um serviço de Aplicação Web do ASP.NET Core como um serviço Web sem monitorização de estado
> * Utilizar o proxy inverso para comunicar com o serviço com estado

Avance para o tutorial seguinte:
> [!div class="nextstepaction"]
> [Implementar a aplicação no Azure](service-fabric-tutorial-deploy-app-to-party-cluster.md)
