---
title: Comunicação de serviço com o ASP.NET Core | Microsoft Docs
description: Saiba como usar ASP.NET Core em Reliable Services com e sem estado.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 8aa4668d-cbb6-4225-bd2d-ab5925a868f2
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 10/12/2018
ms.author: vturecek
ms.openlocfilehash: 9648307bb7278f36686d8a53be90c2d9ef7159e1
ms.sourcegitcommit: fe50db9c686d14eec75819f52a8e8d30d8ea725b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/14/2019
ms.locfileid: "69016698"
---
# <a name="aspnet-core-in-azure-service-fabric-reliable-services"></a>ASP.NET Core no Service Fabric do Azure Reliable Services

ASP.NET Core é uma estrutura de software livre e de plataforma cruzada. Essa estrutura foi projetada para a criação de aplicativos conectados à Internet, baseados em nuvem, como aplicativos Web, aplicativos de IoT e back-ends móveis.

Este artigo é um guia detalhado para hospedar serviços de ASP.NET Core no Service Fabric Reliable Services usando o **Microsoft. AspNetCore** . conjunto de pacotes NuGet.

Para obter um tutorial introdutório sobre ASP.NET Core em Service Fabric e instruções sobre como configurar seu ambiente de desenvolvimento, [consulte Tutorial: Crie e implante um aplicativo com um serviço de front-end de API Web ASP.NET Core e um serviço](service-fabric-tutorial-create-dotnet-app.md)de back-end com estado.

O restante deste artigo pressupõe que você já esteja familiarizado com ASP.NET Core. Caso contrário, leia os [conceitos básicos do ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/index).

## <a name="aspnet-core-in-the-service-fabric-environment"></a>ASP.NET Core no ambiente de Service Fabric

Os aplicativos ASP.NET Core e Service Fabric podem ser executados no .NET Core ou no .NET Framework completo. Você pode usar ASP.NET Core de duas maneiras diferentes no Service Fabric:
 - **Hospedado como um executável convidado**. Dessa forma, é usada principalmente para executar aplicativos ASP.NET Core existentes no Service Fabric sem alterações de código.
 - **Execute dentro de um serviço confiável**. Dessa forma, permite uma melhor integração com o tempo de execução do Service Fabric e permite serviços de ASP.NET Core com estado.

O restante deste artigo explica como usar ASP.NET Core dentro de um serviço confiável, por meio dos componentes de integração do ASP.NET Core que acompanham o SDK do Service Fabric.

## <a name="service-fabric-service-hosting"></a>Hospedagem de serviço Service Fabric

No Service Fabric, uma ou mais instâncias e/ou réplicas do serviço são executadas em um *processo de host de serviço*: um arquivo executável que executa o código de serviço. Você, como autor de um serviço, possui o processo de host de serviço e Service Fabric ativa e monitora para você.

O ASP.NET tradicional (até MVC 5) está rigidamente acoplado ao IIS por meio de System. Web. dll. ASP.NET Core fornece uma separação entre o servidor Web e o aplicativo Web. Essa separação permite que os aplicativos Web sejam portáteis entre diferentes servidores Web. Ele também permite que os servidores Web sejam *hospedados automaticamente*. Isso significa que você pode iniciar um servidor Web em seu próprio processo, em oposição a um processo que é de propriedade de um software de servidor Web dedicado, como o IIS.

Para combinar um serviço de Service Fabric e o ASP.NET, como um executável convidado ou em um serviço confiável, você deve ser capaz de iniciar o ASP.NET dentro do processo de host do serviço. ASP.NET Core hospedagem interna permite que você faça isso.

## <a name="hosting-aspnet-core-in-a-reliable-service"></a>Hospedando ASP.NET Core em um serviço confiável
Normalmente, os aplicativos de ASP.NET Core auto-hospedados criam um Webhost no ponto de entrada de um aplicativo `static void Main()` , como `Program.cs`o método em. Nesse caso, o ciclo de vida do Webhost está associado ao ciclo de vida do processo.

![Hospedando ASP.NET Core em um processo][0]

Mas o ponto de entrada do aplicativo não é o lugar certo para criar um Webhost em um serviço confiável. Isso ocorre porque o ponto de entrada do aplicativo é usado apenas para registrar um tipo de serviço com o tempo de execução Service Fabric, para que ele possa criar instâncias desse tipo de serviço. O Webhost deve ser criado em um serviço confiável em si. Dentro do processo de host de serviço, as instâncias de serviço e/ou réplicas podem passar por vários ciclos de vida. 

Uma instância de serviço confiável é representada por sua classe de serviço derivada `StatelessService` de `StatefulService`ou. A pilha de comunicação para um serviço está contida em `ICommunicationListener` uma implementação em sua classe de serviço. Os `Microsoft.ServiceFabric.AspNetCore.*` pacotes NuGet contêm implementações `ICommunicationListener` do que iniciam e gerenciam o ASP.NET Core Webhost para Kestrel ou http. sys em um serviço confiável.

![Diagrama para hospedagem de ASP.NET Core em um serviço confiável][1]

## <a name="aspnet-core-icommunicationlisteners"></a>ASP.NET Core ICommunicationListeners
As `ICommunicationListener` implementações para Kestrel e http. sys `Microsoft.ServiceFabric.AspNetCore.*` nos pacotes NuGet têm padrões de uso semelhantes. Mas eles executam ações um pouco diferentes específicas para cada servidor Web. 

Ambos os ouvintes de comunicação fornecem um construtor que usa os seguintes argumentos:
 - **`ServiceContext serviceContext`** : Esse é o `ServiceContext` objeto que contém informações sobre o serviço em execução.
 - **`string endpointName`** : Este é o nome de uma `Endpoint` configuração no Service manifest. xml. É principalmente onde os dois ouvintes de comunicação diferem. O http. sys requer `Endpoint` uma configuração, enquanto Kestrel não.
 - **`Func<string, AspNetCoreCommunicationListener, IWebHost> build`** : Esse é um lambda que você implementa, no qual você cria e retorna um `IWebHost`. Ele permite que você configure `IWebHost` a maneira que normalmente faria em um aplicativo ASP.NET Core. O lambda fornece uma URL que é gerada para você, dependendo das opções de integração de Service Fabric que você usa e `Endpoint` a configuração que você fornece. Em seguida, você pode modificar ou usar essa URL para iniciar o servidor Web.

## <a name="service-fabric-integration-middleware"></a>Middleware de integração Service Fabric
O `Microsoft.ServiceFabric.AspNetCore` pacote NuGet inclui o `UseServiceFabricIntegration` método de extensão `IWebHostBuilder` no, que adiciona o middleware com reconhecimento Service Fabric. Esse middleware configura o Kestrel ou o http. sys `ICommunicationListener` para registrar uma URL de serviço exclusiva com o serviço de nomenclatura de Service Fabric. Em seguida, ele valida as solicitações do cliente para garantir que os clientes estejam se conectando ao serviço certo. 

Essa etapa é necessária para impedir que os clientes se conectem erroneamente ao serviço errado. Isso ocorre porque, em um ambiente de host compartilhado, como Service Fabric, vários aplicativos Web podem ser executados na mesma máquina física ou virtual, mas não usam nomes de host exclusivos. Esse cenário é descrito mais detalhadamente na próxima seção.

### <a name="a-case-of-mistaken-identity"></a>Um caso de identidade equivocada
Réplicas de serviço, independentemente do protocolo, escutam em uma combinação de IP: porta exclusiva. Depois que uma réplica de serviço começa a escutar em um ponto de extremidade de IP: porta, ela relata esse endereço de ponto de extremidade para o Service Fabric Serviço de Nomenclatura. Lá, os clientes ou outros serviços podem descobri-lo. Se os serviços usarem portas de aplicativo atribuídas dinamicamente, uma réplica de serviço poderá usar coincidentemente o mesmo ponto de extremidade de IP: porta de outro serviço anteriormente na mesma máquina física ou virtual. Isso pode fazer com que um cliente se conecte erroneamente ao serviço errado. Esse cenário pode resultar se ocorrer a seguinte sequência de eventos:

 1. O serviço A escuta em 10.0.0.1:30000 por HTTP. 
 2. O cliente resolve o serviço A e Obtém o endereço 10.0.0.1:30000.
 3. O serviço A se move para um nó diferente.
 4. O serviço B é colocado em 10.0.0.1 e usa coincidentemente a mesma porta 30000.
 5. O cliente tenta se conectar ao serviço A com o endereço em cache 10.0.0.1:30000.
 6. O cliente agora está conectado com êxito ao serviço B, não percebendo que ele está conectado ao serviço errado.

Isso pode causar bugs em momentos aleatórios que podem ser difíceis de diagnosticar.

### <a name="using-unique-service-urls"></a>Usando URLs de serviço exclusivas
Para evitar esses bugs, os serviços podem postar um ponto de extremidade no Serviço de Nomenclatura com um identificador exclusivo e, em seguida, validar esse identificador exclusivo durante as solicitações do cliente. Essa é uma ação cooperativa entre serviços em um ambiente confiável que não é hostil pelo locatário. Ele não fornece autenticação de serviço segura em um ambiente de locatário hostil.

Em um ambiente confiável, o middleware adicionado pelo `UseServiceFabricIntegration` método acrescenta automaticamente um identificador exclusivo ao endereço Postado para o serviço de nomenclatura. Ele valida esse identificador em cada solicitação. Se o identificador não corresponder, o middleware retornará imediatamente uma resposta HTTP 410 out.

Os serviços que usam uma porta atribuída dinamicamente devem fazer uso desse middleware.

Os serviços que usam uma porta exclusiva fixa não têm esse problema em um ambiente cooperativo. Uma porta exclusiva fixa é normalmente usada para serviços voltados externamente que precisam de uma porta conhecida para que os aplicativos cliente se conectem ao. Por exemplo, a maioria dos aplicativos da Web voltados para a Internet usará a porta 80 ou 443 para conexões de navegador da Web. Nesse caso, o identificador exclusivo não deve ser habilitado.

O diagrama a seguir mostra o fluxo de solicitação com o middleware habilitado:

![Integração do Service Fabric ASP.NET Core][2]

As implementações Kestrel e http `ICommunicationListener` . sys usam esse mecanismo exatamente da mesma maneira. Embora o http. sys possa diferenciar internamente as solicitações com base em caminhos de URL exclusivos usando o recurso subjacente de compartilhamento de porta **http. sys** , essa funcionalidade *não* é usada `ICommunicationListener` pela implementação de http. sys. Isso ocorre porque resulta em códigos de status de erro HTTP 503 e HTTP 404 no cenário descrito anteriormente. Isso, por sua vez, dificulta para os clientes determinarem a intenção do erro, pois HTTP 503 e HTTP 404 são comumente usados para indicar outros erros. 

Portanto, as implementações Kestrel e http `ICommunicationListener` . sys padronizam o middleware fornecido pelo `UseServiceFabricIntegration` método de extensão. Portanto, os clientes precisam apenas executar uma ação de reresolução de ponto de extremidade de serviço em respostas HTTP 410.

## <a name="httpsys-in-reliable-services"></a>HTTP. sys em Reliable Services
Você pode usar o HTTP. sys em Reliable Services importando o pacote NuGet **Microsoft. AspNetCore. https** . Este pacote contém `HttpSysCommunicationListener`, uma implementação do `ICommunicationListener`. `HttpSysCommunicationListener`permite que você crie um Webhost ASP.NET Core dentro de um serviço confiável usando HTTP. sys como o servidor Web.

O HTTP. sys é criado na [API do servidor http do Windows](https://msdn.microsoft.com/library/windows/desktop/aa364510(v=vs.85).aspx). Essa API usa o driver de kernel **http. sys** para processar solicitações HTTP e roteá-las para processos que executam aplicativos Web. Isso permite que vários processos na mesma máquina física ou virtual hospedem aplicativos Web na mesma porta, sem ambigüidade por um caminho de URL exclusivo ou nome de host. Esses recursos são úteis em Service Fabric para hospedar vários sites no mesmo cluster.

>[!NOTE]
>A implementação de HTTP. sys funciona apenas na plataforma Windows.

O diagrama a seguir ilustra como o HTTP. sys usa o driver de kernel **http. sys** no Windows para compartilhamento de porta:

![Diagrama de HTTP. sys][3]

### <a name="httpsys-in-a-stateless-service"></a>HTTP. sys em um serviço sem estado
Para usar `HttpSys` em um serviço sem estado, substitua `CreateServiceInstanceListeners` o método e retorne uma `HttpSysCommunicationListener` instância:

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(serviceContext =>
            new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
                new WebHostBuilder()
                    .UseHttpSys()
                    .ConfigureServices(
                        services => services
                            .AddSingleton<StatelessServiceContext>(serviceContext))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build()))
    };
}
```

### <a name="httpsys-in-a-stateful-service"></a>HTTP. sys em um serviço com estado

`HttpSysCommunicationListener`Não está projetado atualmente para uso em serviços com estado devido a complicações com o recurso subjacente de compartilhamento de porta **http. sys** . Para obter mais informações, consulte a seção a seguir sobre alocação de porta dinâmica com HTTP. sys. Para serviços com estado, Kestrel é o servidor Web sugerido.

### <a name="endpoint-configuration"></a>Configuração do ponto de extremidade

Uma `Endpoint` configuração é necessária para servidores Web que usam a API do servidor http do Windows, incluindo http. sys. Os servidores Web que usam a API do servidor HTTP do Windows devem primeiro reservar sua URL com HTTP. sys (normalmente, isso é feito com a ferramenta [netsh](https://msdn.microsoft.com/library/windows/desktop/cc307236(v=vs.85).aspx) ). 

Essa ação requer privilégios elevados que os serviços não têm, por padrão. As opções "http" ou "https" para a `Protocol` propriedade `Endpoint` da configuração em manifestar. XML são usadas especificamente para instruir o tempo de execução de Service Fabric para registrar uma URL com http. sys em seu nome. Ele faz isso usando o prefixo de URL de [*curinga forte*](https://msdn.microsoft.com/library/windows/desktop/aa364698(v=vs.85).aspx) .

Por exemplo, para reservar `http://+:80` um serviço, use a seguinte configuração no Service manifest. xml:

```xml
<ServiceManifest ... >
    ...
    <Resources>
        <Endpoints>
            <Endpoint Name="ServiceEndpoint" Protocol="http" Port="80" />
        </Endpoints>
    </Resources>

</ServiceManifest>
```

E o nome do ponto de extremidade deve ser `HttpSysCommunicationListener` passado para o construtor:

```csharp
 new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
 {
     return new WebHostBuilder()
         .UseHttpSys()
         .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
         .UseUrls(url)
         .Build();
 })
```

#### <a name="use-httpsys-with-a-static-port"></a>Usar HTTP. sys com uma porta estática
Para usar uma porta estática com http. sys, forneça o número da porta na `Endpoint` configuração:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

#### <a name="use-httpsys-with-a-dynamic-port"></a>Usar HTTP. sys com uma porta dinâmica
Para usar uma porta atribuída dinamicamente com http. sys, omita a `Port` propriedade `Endpoint` na configuração:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
```

Uma porta dinâmica alocada por `Endpoint` uma configuração fornece apenas uma porta *por processo de host*. O modelo de Hospedagem de Service Fabric atual permite que várias instâncias de serviço e/ou réplicas sejam hospedadas no mesmo processo. Isso significa que cada uma delas compartilhará a mesma porta quando alocada por meio da `Endpoint` configuração. Várias instâncias de **http. sys** podem compartilhar uma porta usando o recurso subjacente de compartilhamento de porta **http. sys** . Mas não há suporte `HttpSysCommunicationListener` para isso devido às complicações que ele introduz para solicitações de cliente. Para o uso de porta dinâmica, Kestrel é o servidor Web sugerido.

## <a name="kestrel-in-reliable-services"></a>KESTREL em Reliable Services
Você pode usar o Kestrel em Reliable Services importando o pacote NuGet **Microsoft. Microsoft. AspNetCore. Kestrel** . Este pacote contém `KestrelCommunicationListener`, uma implementação do `ICommunicationListener`. `KestrelCommunicationListener`permite que você crie um Webhost ASP.NET Core dentro de um serviço confiável usando o Kestrel como o servidor Web.

O Kestrel é um servidor Web de plataforma cruzada para ASP.NET Core com base no libuv, uma biblioteca de e/s assíncrona de plataforma cruzada. Ao contrário de HTTP. sys, Kestrel não usa um Gerenciador de ponto de extremidade centralizado. Além disso, ao contrário de HTTP. sys, o Kestrel não dá suporte ao compartilhamento de porta entre vários processos. Cada instância de Kestrel deve usar uma porta exclusiva.

![Diagrama de Kestrel][4]

### <a name="kestrel-in-a-stateless-service"></a>KESTREL em um serviço sem estado
Para usar `Kestrel` em um serviço sem estado, substitua `CreateServiceInstanceListeners` o método e retorne uma `KestrelCommunicationListener` instância:

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(serviceContext =>
            new KestrelCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
                new WebHostBuilder()
                    .UseKestrel()
                    .ConfigureServices(
                        services => services
                            .AddSingleton<StatelessServiceContext>(serviceContext))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build();
            ))
    };
}
```

### <a name="kestrel-in-a-stateful-service"></a>KESTREL em um serviço com estado
Para usar `Kestrel` em um serviço com estado, substitua `CreateServiceReplicaListeners` o método e retorne uma `KestrelCommunicationListener` instância:

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new ServiceReplicaListener[]
    {
        new ServiceReplicaListener(serviceContext =>
            new KestrelCommunicationListener(serviceContext, (url, listener) =>
                new WebHostBuilder()
                    .UseKestrel()
                    .ConfigureServices(
                         services => services
                             .AddSingleton<StatefulServiceContext>(serviceContext)
                             .AddSingleton<IReliableStateManager>(this.StateManager))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build();
            ))
    };
}
```

Neste exemplo, uma instância singleton do `IReliableStateManager` é fornecida para o contêiner de injeção de dependência webhost. Isso não é estritamente necessário, mas permite que você use `IReliableStateManager` e Reliable Collections em seus métodos de ação do controlador MVC.

Um `Endpoint` nome de configuração *não* é fornecido `KestrelCommunicationListener` para em um serviço com estado. Isso é explicado mais detalhadamente na seção a seguir.

### <a name="configure-kestrel-to-use-https"></a>Configurar o Kestrel para utilizar HTTPS
Ao habilitar HTTPS com Kestrel em seu serviço, você precisará definir várias opções de escuta. Atualize o `ServiceInstanceListener` para usar um ponto de extremidade *EndpointHttps* e escute em uma porta específica (como a porta 443). Ao configurar o host Web para usar o servidor Web Kestrel, você deve configurar o Kestrel para escutar endereços IPv6 em todas as interfaces de rede: 

```csharp
new ServiceInstanceListener(
serviceContext =>
    new KestrelCommunicationListener(
        serviceContext,
        "EndpointHttps",
        (url, listener) =>
        {
            ServiceEventSource.Current.ServiceMessage(serviceContext, $"Starting Kestrel on {url}");

            return new WebHostBuilder()
                .UseKestrel(opt =>
                {
                    int port = serviceContext.CodePackageActivationContext.GetEndpoint("EndpointHttps").Port;
                    opt.Listen(IPAddress.IPv6Any, port, listenOptions =>
                    {
                        listenOptions.UseHttps(GetCertificateFromStore());
                        listenOptions.NoDelay = true;
                    });
                })
                .ConfigureAppConfiguration((builderContext, config) =>
                {
                    config.AddJsonFile("appsettings.json", optional: false, reloadOnChange: true);
                })

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
```

Para obter um exemplo completo em um tutorial, consulte [Configurar Kestrel para usar HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#configure-kestrel-to-use-https).


### <a name="endpoint-configuration"></a>Configuração do ponto de extremidade
Uma `Endpoint` configuração não é necessária para usar o Kestrel. 

Kestrel é um servidor Web autônomo simples. Diferentemente de http. sys (ou HttpListener), ele não `Endpoint` precisa de uma configuração em um manifesto. XML porque ele não requer o registro de URL antes de iniciar. 

#### <a name="use-kestrel-with-a-static-port"></a>Usar o Kestrel com uma porta estática
Você pode configurar uma porta estática na `Endpoint` configuração de immanifest. xml para uso com Kestrel. Embora isso não seja estritamente necessário, ele oferece dois benefícios potenciais:
 - Se a porta não cair no intervalo de portas do aplicativo, ela será aberta por meio do firewall do sistema operacional pelo Service Fabric.
 - A URL fornecida por `KestrelCommunicationListener` você usará essa porta.

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

Se um `Endpoint` for configurado, seu nome deverá ser passado para o `KestrelCommunicationListener` Construtor: 

```csharp
new KestrelCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) => ...
```

Se o Service manifest. xml não usar `Endpoint` uma configuração, omita o nome `KestrelCommunicationListener` no construtor. Nesse caso, ele usará uma porta dinâmica. Consulte a próxima seção para obter mais informações sobre isso.

#### <a name="use-kestrel-with-a-dynamic-port"></a>Usar o Kestrel com uma porta dinâmica
O Kestrel não pode usar a atribuição de porta `Endpoint` automática da configuração em um manifesto. xml. Isso ocorre porque a atribuição automática de porta `Endpoint` de uma configuração atribui uma porta exclusiva por *processo de host*, e um único processo de host pode conter várias instâncias de Kestrel. Isso não funciona com Kestrel porque não dá suporte ao compartilhamento de porta. Portanto, cada instância de Kestrel deve ser aberta em uma porta exclusiva.

Para usar a atribuição de porta dinâmica com Kestrel, `Endpoint` omita totalmente a configuração no Service manifest. xml e não passe um nome de ponto `KestrelCommunicationListener` de extremidade para o construtor, da seguinte maneira:

```csharp
new KestrelCommunicationListener(serviceContext, (url, listener) => ...
```

Nessa configuração, `KestrelCommunicationListener` o selecionará automaticamente uma porta não usada do intervalo de portas do aplicativo.

## <a name="service-fabric-configuration-provider"></a>Provedor de configuração de Service Fabric
A configuração do aplicativo no ASP.NET Core é baseada em pares chave-valor estabelecidos pelo provedor de configuração. Leia [configuração no ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/) para saber mais sobre o suporte geral ASP.NET Core configuração.

Esta seção descreve como o provedor de configuração do Service Fabric se integra com a configuração do `Microsoft.ServiceFabric.AspNetCore.Configuration` ASP.NET Core importando o pacote NuGet.

### <a name="addservicefabricconfiguration-startup-extensions"></a>Extensões de inicialização do AddServiceFabricConfiguration
Depois de importar o `Microsoft.ServiceFabric.AspNetCore.Configuration` pacote NuGet, você precisa registrar a fonte de configuração Service Fabric com ASP.NET Core API de configuração. Faça isso verificando extensões **AddServiceFabricConfiguration** no namespace em `Microsoft.ServiceFabric.AspNetCore.Configuration` relação `IConfigurationBuilder`a.

```csharp
using Microsoft.ServiceFabric.AspNetCore.Configuration;

public Startup(IHostingEnvironment env)
{
    var builder = new ConfigurationBuilder()
        .SetBasePath(env.ContentRootPath)
        .AddJsonFile("appsettings.json", optional: false, reloadOnChange: true)
        .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true)
        .AddServiceFabricConfiguration() // Add Service Fabric configuration settings.
        .AddEnvironmentVariables();
    Configuration = builder.Build();
}

public IConfigurationRoot Configuration { get; }
```

Agora, o serviço de ASP.NET Core pode acessar as definições de configuração de Service Fabric, assim como qualquer outra configuração de aplicativo. Por exemplo, você pode usar o padrão de opções para carregar as configurações em objetos com rigidez de tipos.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<MyOptions>(Configuration);  // Strongly typed configuration object.
    services.AddMvc();
}
```
### <a name="default-key-mapping"></a>Mapeamento de chave padrão
Por padrão, o provedor de configuração Service Fabric inclui o nome do pacote, o nome da seção e o nome da propriedade. Juntos, eles formam a chave de configuração ASP.NET Core, da seguinte maneira:
```csharp
$"{this.PackageName}{ConfigurationPath.KeyDelimiter}{section.Name}{ConfigurationPath.KeyDelimiter}{property.Name}"
```

Por exemplo, se você tiver um pacote de configuração `MyConfigPackage` chamado com o conteúdo a seguir, o valor de configuração estará disponível em `IConfiguration` ASP.NET Core por meio de *MyConfigPackage: myconfigsection: MyParameter*.
```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">  
  <Section Name="MyConfigSection">
    <Parameter Name="MyParameter" Value="Value1" />
  </Section>  
</Settings>
```
### <a name="service-fabric-configuration-options"></a>Opções de configuração do Service Fabric
O provedor de configuração de Service Fabric `ServiceFabricConfigurationOptions` também dá suporte para alterar o comportamento padrão do mapeamento de chave.

#### <a name="encrypted-settings"></a>Configurações criptografadas
O Service Fabric dá suporte a configurações criptografadas, assim como o provedor de configuração de Service Fabric. As configurações criptografadas não são descriptografadas para ASP.NET Core `IConfiguration` por padrão. Em vez disso, os valores criptografados são armazenados nesse local. Mas se você quiser descriptografar o valor a ser armazenado no ASP.NET Core IConfiguration, poderá definir o sinalizador decryptvalue como false na `AddServiceFabricConfiguration` extensão, da seguinte maneira:

```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    var builder = new ConfigurationBuilder()        
        .AddServiceFabricConfiguration(activationContext, (options) => options.DecryptValue = false); // set flag to decrypt the value
    Configuration = builder.Build();
}
```
#### <a name="multiple-configuration-packages"></a>Vários pacotes de configuração
O Service Fabric dá suporte a vários pacotes de configuração. Por padrão, o nome do pacote é incluído na chave de configuração. Mas você pode definir o `IncludePackageName` sinalizador como false, da seguinte maneira:
```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    var builder = new ConfigurationBuilder()        
        // exclude package name from key.
        .AddServiceFabricConfiguration(activationContext, (options) => options.IncludePackageName = false); 
    Configuration = builder.Build();
}
```
#### <a name="custom-key-mapping-value-extraction-and-data-population"></a>Mapeamento de chave personalizada, extração de valor e população de dados
O provedor de configuração de Service Fabric também dá suporte a cenários mais avançados para personalizar `ExtractKeyFunc` o mapeamento de chave com e extrair `ExtractValueFunc`os valores personalizados com. Você pode até mesmo alterar todo o processo de preenchimento de dados da configuração Service Fabric para ASP.NET Core configuração `ConfigAction`usando o.

Os exemplos a seguir ilustram como `ConfigAction` usar o para personalizar a população de dados:
```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    
    this.valueCount = 0;
    this.sectionCount = 0;
    var builder = new ConfigurationBuilder();
    builder.AddServiceFabricConfiguration(activationContext, (options) =>
        {
            options.ConfigAction = (package, configData) =>
            {
                ILogger logger = new ConsoleLogger("Test", null, false);
                logger.LogInformation($"Config Update for package {package.Path} started");

                foreach (var section in package.Settings.Sections)
                {
                    this.sectionCount++;

                    foreach (var param in section.Parameters)
                    {
                        configData[options.ExtractKeyFunc(section, param)] = options.ExtractValueFunc(section, param);
                        this.valueCount++;
                    }
                }

                logger.LogInformation($"Config Update for package {package.Path} finished");
            };
        });
  Configuration = builder.Build();
}
```

### <a name="configuration-updates"></a>Atualizações de configuração
O provedor de configuração de Service Fabric também dá suporte a atualizações de configuração. Você pode usar ASP.NET Core `IOptionsMonitor` para receber notificações de alteração e, em `IOptionsSnapshot` seguida, usar para recarregar os dados de configuração. Para obter mais informações, consulte [Opções de ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options).

Essas opções têm suporte por padrão. Nenhuma codificação adicional é necessária para habilitar atualizações de configuração.

## <a name="scenarios-and-configurations"></a>Cenários e configurações
Esta seção fornece a combinação de servidor Web, configuração de porta, opções de integração de Service Fabric e configurações diversas que recomendamos para solucionar os seguintes cenários:
 - Serviços sem estado ASP.NET Core e expostos externamente
 - Somente interno ASP.NET Core serviços sem monitoração de estado
 - Somente interno ASP.NET Core serviços com estado

Um **serviço exposto externamente** é aquele que expõe um ponto de extremidade chamado de fora do cluster, geralmente por meio de um balanceador de carga.

Um serviço **somente interno** é aquele cujo ponto de extremidade é chamado apenas de dentro do cluster.

> [!NOTE]
> Os pontos de extremidade de serviço com estado geralmente não devem ser expostos à Internet. Os clusters por trás de balanceadores de carga que não reconhecem Service Fabric resolução de serviço, como Azure Load Balancer, não poderão expor serviços com estado. Isso ocorre porque o balanceador de carga não poderá localizar e rotear o tráfego para a réplica de serviço com estado apropriada. 

### <a name="externally-exposed-aspnet-core-stateless-services"></a>Serviços sem estado ASP.NET Core e expostos externamente
Kestrel é o servidor Web sugerido para serviços de front-end que expõem pontos de extremidade HTTP externos para a Internet. No Windows, o HTTP. sys pode fornecer a capacidade de compartilhamento de porta, que permite hospedar vários serviços Web no mesmo conjunto de nós usando a mesma porta. Nesse cenário, os serviços Web são diferenciados por nome de host ou caminho, sem depender de um proxy de front-end ou gateway para fornecer roteamento HTTP.
 
Quando exposto à Internet, um serviço sem estado deve usar um ponto de extremidade bem conhecido e estável acessível por meio de um balanceador de carga. Você fornecerá essa URL para os usuários do seu aplicativo. Recomendamos a seguinte configuração:

|  |  | **Notas** |
| --- | --- | --- |
| Servidor Web | Kestrel | Kestrel é o servidor Web preferencial, pois tem suporte no Windows e no Linux. |
| Configuração de porta | estático | Uma porta estática conhecida deve ser configurada na `Endpoints` configuração de manifestar. xml, como 80 para http ou 443 para https. |
| ServiceFabricIntegrationOptions | Nenhum | Use a `ServiceFabricIntegrationOptions.None` opção ao configurar o middleware de integração Service Fabric, para que o serviço não tente validar solicitações de entrada para um identificador exclusivo. Os usuários externos do seu aplicativo não saberão as informações exclusivas de identificação que o middleware usa. |
| Contagem de Instâncias | -1 | Em casos de uso típicos, a configuração de contagem de instâncias deve ser definida como *-1*. Isso é feito para que uma instância esteja disponível em todos os nós que recebem o tráfego de um balanceador de carga. |

Se vários serviços expostos externamente compartilharem o mesmo conjunto de nós, você poderá usar o HTTP. sys com um caminho de URL exclusivo, mas estável. Você pode fazer isso modificando a URL fornecida ao configurar o IWebHost. Observe que isso se aplica somente a HTTP. sys.

 ```csharp
 new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
 {
     url += "/MyUniqueServicePath";
 
     return new WebHostBuilder()
         .UseHttpSys()
         ...
         .UseUrls(url)
         .Build();
 })
 ```

### <a name="internal-only-stateless-aspnet-core-service"></a>Serviço de ASP.NET Core sem estado somente interno
Os serviços sem estado que são chamados apenas de dentro do cluster devem usar URLs exclusivas e portas atribuídas dinamicamente para garantir a cooperação entre vários serviços. Recomendamos a seguinte configuração:

|  |  | **Notas** |
| --- | --- | --- |
| Servidor Web | Kestrel | Embora você possa usar HTTP. sys para serviços sem estado internos, o Kestrel é o melhor servidor para permitir que várias instâncias de serviço compartilhem um host.  |
| Configuração de porta | atribuído dinamicamente | Várias réplicas de um serviço com estado podem compartilhar um processo de host ou sistema operacional do host e, portanto, precisarão de portas exclusivas. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | Com a atribuição de porta dinâmica, essa configuração impede o problema de identidade equivocado descrito anteriormente. |
| InstanceCount | qualquer | A configuração contagem de instâncias pode ser definida como qualquer valor necessário para operar o serviço. |

### <a name="internal-only-stateful-aspnet-core-service"></a>Serviço de ASP.NET Core com estado somente interno
Serviços com estado que são chamados apenas de dentro do cluster devem usar portas atribuídas dinamicamente para garantir a cooperação entre vários serviços. Recomendamos a seguinte configuração:

|  |  | **Notas** |
| --- | --- | --- |
| Servidor Web | Kestrel | O `HttpSysCommunicationListener` não é projetado para uso por serviços com estado em que as réplicas compartilham um processo de host. |
| Configuração de porta | atribuído dinamicamente | Várias réplicas de um serviço com estado podem compartilhar um processo de host ou sistema operacional do host e, portanto, precisarão de portas exclusivas. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | Com a atribuição de porta dinâmica, essa configuração impede o problema de identidade equivocado descrito anteriormente. |

## <a name="next-steps"></a>Passos Seguintes
[Depurar a sua aplicação do Service Fabric com o Visual Studio](service-fabric-debugging-your-application.md)


<!--Image references-->
[0]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-standalone.png
[1]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-servicefabric.png
[2]:./media/service-fabric-reliable-services-communication-aspnetcore/integration.png
[3]:./media/service-fabric-reliable-services-communication-aspnetcore/httpsys.png
[4]:./media/service-fabric-reliable-services-communication-aspnetcore/kestrel.png
