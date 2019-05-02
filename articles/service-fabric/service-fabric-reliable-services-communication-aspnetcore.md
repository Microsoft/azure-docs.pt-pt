---
title: Serviço de comunicação com o ASP.NET Core | Documentos da Microsoft
description: Saiba como utilizar o ASP.NET Core no Reliable Services com e sem estado.
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
ms.openlocfilehash: 638c06e1854504dcb7ff34b1d9df56694556c421
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64939782"
---
# <a name="aspnet-core-in-azure-service-fabric-reliable-services"></a>ASP.NET Core no Azure Service Fabric Reliable Services

ASP.NET Core é uma estrutura de código-fonte aberto e Multiplataforma. Essa estrutura foi concebida para criar aplicativos com base na cloud, ligados à internet, tais como aplicações web, aplicações de IoT, e o back-ends móveis.

Este artigo é um guia detalhado para alojar serviços do ASP.NET Core no Service Fabric Reliable Services utilizando o **Microsoft.ServiceFabric.AspNetCore.** conjunto de pacotes NuGet.

Para um tutorial introdutório sobre o ASP.NET Core no Service Fabric e instruções sobre como obter o seu ambiente de desenvolvimento, configurar, consulte [Tutorial: Criar e implementar uma aplicação com um serviço de front-end de ASP.NET Core Web API e um serviço de back-end com monitorização de estado](service-fabric-tutorial-create-dotnet-app.md).

O restante deste artigo pressupõe que já estiver familiarizado com o ASP.NET Core. Se não for, leia os [conceitos básicos do ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/index).

## <a name="aspnet-core-in-the-service-fabric-environment"></a>ASP.NET Core no ambiente do Service Fabric

Aplicações ASP.NET Core e do Service Fabric podem executar no .NET Core ou .NET Framework completo. Pode usar o ASP.NET Core de duas formas diferentes no Service Fabric:
 - **Hospedado como um executável convidado**. Dessa maneira é usada principalmente para executar aplicativos existentes do ASP.NET Core no Service Fabric sem alterações de código.
 - **Executar dentro de um serviço fiável**. Dessa forma permite uma melhor integração com o tempo de execução do Service Fabric e permite com monitoração de estado ASP.NET Core serviços.

O restante deste artigo explica como usar o ASP.NET Core dentro de um serviço confiável por meio dos componentes de integração do ASP.NET Core que vêm com o SDK do Service Fabric.

## <a name="service-fabric-service-hosting"></a>Hospedagem de serviços do Service Fabric

No Service Fabric, um ou mais instâncias de e/ou réplicas do seu serviço execute *processo de anfitrião do serviço*: um ficheiro executável que executa o código do serviço. Que, como um autor de serviço, possui o processo de host de serviço e o Service Fabric ativa e monitoriza-a para.

ASP.NET tradicional (até MVC 5) está intimamente ligado ao IIS por meio da dll. ASP.NET Core fornece uma separação entre o servidor web e a sua aplicação web. Essa separação permite que os aplicativos da web seja portátil entre servidores web diferente. Ele também permite que servidores web estejam *autoalojado*. Isso significa que pode iniciar um servidor web no seu próprio processo, em vez de um processo que é propriedade de software de servidor web dedicado, como o IIS.

Combinar um serviço do Service Fabric e ASP.NET, como um executável de convidado ou num serviço fiável, tem de ser possível iniciar o ASP.NET dentro do processo de host de serviço. Hospedagem interna do ASP.NET Core permite-lhe fazê-lo.

## <a name="hosting-aspnet-core-in-a-reliable-service"></a>Hospedagem ASP.NET Core num reliable service
Normalmente, aplicativos de hospedagem interna ASP.NET Core de criar um WebHost no ponto de entrada de uma aplicação, tal como o `static void Main()` método na `Program.cs`. Neste caso, o ciclo de vida do WebHost está vinculado ao ciclo de vida do processo.

![Num processo de hospedagem ASP.NET Core][0]

Mas o ponto de entrada do aplicativo não é o local indicado para criar um WebHost num serviço fiável. Isso ocorre porque o ponto de entrada de aplicativo só é utilizado para registar um tipo de serviço com o tempo de execução do Service Fabric, para que ele possa criar instâncias desse tipo de serviço. O WebHost deve ser criada num serviço fiável em si. Dentro do processo de host de serviço, instâncias de serviço e/ou de réplicas podem passar por vários ciclos de vida. 

Uma instância de Reliable Service é representada por sua classe de serviço derivar `StatelessService` ou `StatefulService`. A pilha de comunicações para um serviço está contida num `ICommunicationListener` implementação na sua classe de serviço. O `Microsoft.ServiceFabric.AspNetCore.*` pacotes de NuGet contenham implementações de `ICommunicationListener` que iniciar e gerir o WebHost do ASP.NET Core para Kestrel ou HTTP. sys num serviço fiável.

![Diagrama de hospedagem ASP.NET Core num reliable service][1]

## <a name="aspnet-core-icommunicationlisteners"></a>ICommunicationListeners do ASP.NET Core
O `ICommunicationListener` implementações para Kestrel e HTTP. sys no `Microsoft.ServiceFabric.AspNetCore.*` pacotes NuGet têm padrões de utilização semelhante. Mas eles realizar um pouco diferentes ações específicas para cada servidor web. 

Ambos os serviços de escuta de comunicação de fornecer um construtor que aceita os seguintes argumentos:
 - **`ServiceContext serviceContext`**: Este é o `ServiceContext` objeto que contém informações sobre o serviço em execução.
 - **`string endpointName`**: Este é o nome de um `Endpoint` configuração no servicemanifest. XML. É principalmente em que os serviços de escuta de dois comunicação são diferentes. O HTTP. sys *requer* um `Endpoint` configuração, enquanto o Kestrel não.
 - **`Func<string, AspNetCoreCommunicationListener, IWebHost> build`**: Este é um lambda que implementar, na qual cria e retorna um `IWebHost`. Permite-lhe configurar `IWebHost` a forma como faria normalmente num aplicativo do ASP.NET Core. O lambda fornece um URL que é gerado para, dependendo das opções de integração do Service Fabric utiliza e o `Endpoint` configuração fornecer. Em seguida, pode modificar ou usar esse URL para iniciar o servidor web.

## <a name="service-fabric-integration-middleware"></a>Middleware de integração do Service Fabric
O `Microsoft.ServiceFabric.AspNetCore` inclui o pacote NuGet do `UseServiceFabricIntegration` método de extensão no `IWebHostBuilder` que adiciona o middleware de reconhecimento de Service Fabric. Este middleware configura o Kestrel ou HTTP. sys `ICommunicationListener` para registar um URL de serviço único com o serviço de nomenclatura do Service Fabric. Em seguida, valida os pedidos de cliente para garantir que os clientes estão a ligar para o serviço correto. 

Este passo é necessário para impedir que os clientes por engano, conexão com o serviço de errado. Isso ocorre porque, num ambiente de host partilhados, tais como o Service Fabric, várias aplicações web podem ser executados no mesmo físico ou máquina virtual, mas não utilizam nomes de anfitrião exclusivo. Este cenário é descrito mais detalhadamente na secção seguinte.

### <a name="a-case-of-mistaken-identity"></a>Um caso de identidade errada
Réplicas de serviço, independentemente do protocolo, ouça numa combinação de IP: porta exclusiva. Depois de uma réplica de serviço foi iniciado um ponto de extremidade IP: porta a escutar, relatórios esse endereço de ponto final para o serviço de nomenclatura do Service Fabric. Lá, os clientes ou outros serviços podem detetá-los. Se os serviços utilizam portas da aplicação atribuído de forma dinâmica, uma réplica de serviço por coincidência poderá utilizar o mesmo ponto de final de IP: porta de outro serviço anteriormente sobre o mesmo físico ou máquina virtual. Isto pode fazer com que um cliente por engano se ligue ao serviço errado. Este cenário pode resultar se a seguinte sequência de eventos ocorre:

 1. O serviço escuta na 10.0.0.1:30000 através de HTTP. 
 2. Cliente resolve o serviço e obtém o endereço 10.0.0.1:30000.
 3. Serviço A move para um nó diferente.
 4. Serviço B for colocada em 10.0.0.1 e por coincidência utiliza a mesma porta 30000.
 5. Cliente tenta estabelecer ligação ao serviço A com 10.0.0.1:30000 endereço em cache.
 6. Cliente está agora ligado com êxito para o serviço B, não percebendo que ele está ligado ao serviço errado.

Isso pode causar bugs em momentos aleatórios que podem ser difíceis de diagnosticar.

### <a name="using-unique-service-urls"></a>Utilizando os URLs de serviço único
Para impedir que esses bugs, serviços podem publicar um ponto final para o serviço de nomenclatura de mensagens em fila com um identificador exclusivo e, em seguida, validar esse identificador exclusivo durante os pedidos de cliente. Esta é uma ação cooperativo entre os serviços num ambiente confiável de não inquilino hipóteses, semi-hostil. Ela não fornece autenticação do serviço segura num ambiente de inquilino de hipóteses, semi-hostil.

Num ambiente fidedigno, o middleware adicionado o `UseServiceFabricIntegration` método acrescenta automaticamente um identificador exclusivo para o endereço publicado para o serviço de nomenclatura. Ele valida a esse identificador em cada pedido. Se o identificador não corresponder, o middleware devolve imediatamente uma resposta de HTTP 410 ficaram no passado.

Serviços que utilizam uma porta dinamicamente atribuída deve tornar a utilização deste middleware.

Serviços que utilizam uma porta única fixa não tem esse problema num ambiente cooperativo. Uma porta única fixa é normalmente utilizada para pontos finais direcionados para serviços que precisam de uma porta bem conhecida para aplicativos de cliente ligar a. Por exemplo, a maioria dos aplicativos da web Internet utilizará a porta 80 ou 443 para ligações de navegador da web. Neste caso, o identificador exclusivo não deve ser ativado.

O diagrama seguinte mostra o fluxo de pedido com o middleware ativado:

![Integração do ASP.NET Core do Service Fabric][2]

Tanto o Kestrel como o HTTP. sys `ICommunicationListener` implementações usarem esse mecanismo exatamente da mesma forma. Embora o HTTP. sys internamente pode diferenciar pedidos com base em caminhos de URL exclusivos com subjacente **HTTP. sys** recurso, o que é a funcionalidade de compartilhamento de porta *não* utilizado pelo HTTP. sys `ICommunicationListener`implementação. Isso ocorre porque o que resulta em 503 de HTTP e HTTP 404 códigos de estado de erro no cenário descrito anteriormente. Que por sua vez torna difícil para os clientes determinar a intenção do erro, como HTTP 503 e HTTP 404 são frequentemente utilizadas para indicar outros erros. 

Portanto, o Kestrel e HTTP. sys `ICommunicationListener` implementações padronizar em middleware fornecida pelo `UseServiceFabricIntegration` método de extensão. Por conseguinte, os clientes só precisam de realizar uma ação de voltar a resolver de ponto final de serviço sobre respostas de HTTP 410.

## <a name="httpsys-in-reliable-services"></a>Http. sys no Reliable Services
Pode usar o HTTP. sys no Reliable Services através da importação de **Microsoft.ServiceFabric.AspNetCore.HttpSys** pacote NuGet. Este pacote contém `HttpSysCommunicationListener`, uma implementação de `ICommunicationListener`. `HttpSysCommunicationListener` Pode criar um WebHost do ASP.NET Core dentro de um serviço fiável com HTTP. sys, que o servidor web.

Http. sys foi criado sobre o [Windows HTTP Server API](https://msdn.microsoft.com/library/windows/desktop/aa364510(v=vs.85).aspx). Esta API utiliza o **HTTP. sys** driver do kernel para processar pedidos HTTP e roteá-los para processos que executam aplicações web. Isso permite que vários processos no mesmo físico ou máquina virtual para alojar aplicações web na mesma porta, a ambigüidade removida por qualquer um de um único URL caminho ou nome de anfitrião. Esses recursos são úteis no Service Fabric para alojar vários Web sites no mesmo cluster.

>[!NOTE]
>Implementação de HTTP. sys funciona apenas na plataforma Windows.

O diagrama seguinte ilustra como o HTTP. sys utiliza o **HTTP. sys** driver do kernel no Windows para a partilha de portas:

![Diagrama de HTTP. sys][3]

### <a name="httpsys-in-a-stateless-service"></a>Http. sys num serviço sem estado
Para utilizar `HttpSys` num serviço sem estado, substituir o `CreateServiceInstanceListeners` método e retornar um `HttpSysCommunicationListener` instância:

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

### <a name="httpsys-in-a-stateful-service"></a>Http. sys num serviço com estado

`HttpSysCommunicationListener` Atualmente não é projetado para uso em serviços com estado devido a complicações com subjacente **HTTP. sys** recurso de compartilhamento de porta. Para obter mais informações, consulte a secção seguinte na alocação de porta dinâmica com HTTP. sys. Para serviços com estado, o Kestrel é o servidor web sugerida.

### <a name="endpoint-configuration"></a>Configuração do ponto final

Um `Endpoint` configuração é necessária para servidores web que utilizam a API de servidor Windows HTTP, incluindo HTTP. sys. Servidores Web que utilizam o Windows HTTP Server API primeiro tem de reservar o URL do HTTP. sys (isso normalmente é feito com o [netsh](https://msdn.microsoft.com/library/windows/desktop/cc307236(v=vs.85).aspx) ferramenta). 

Esta ação requer privilégios elevados que seus serviços não têm por predefinição. As opções de "http" ou "https" para o `Protocol` propriedade o `Endpoint` configuração no servicemanifest. XML são usados especificamente para instruir o runtime do Service Fabric para registar um URL com HTTP. sys em seu nome. Fá-lo ao utilizar o [ *curinga forte* ](https://msdn.microsoft.com/library/windows/desktop/aa364698(v=vs.85).aspx) prefixo de URL.

Por exemplo, para reservar `http://+:80` para um serviço, utilize a seguinte configuração no servicemanifest. XML:

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

E o nome do ponto de extremidade deve ser passado para o `HttpSysCommunicationListener` construtor:

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

#### <a name="use-httpsys-with-a-static-port"></a>Utilizar o HTTP. sys com uma porta estática
Para utilizar uma porta estática com HTTP. sys, indique o número de porta no `Endpoint` configuração:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

#### <a name="use-httpsys-with-a-dynamic-port"></a>Utilizar o HTTP. sys com uma porta dinâmica
Para utilizar uma porta dinamicamente atribuída com HTTP. sys, omita o `Port` propriedade o `Endpoint` configuração:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
```

Uma porta dinâmica alocada por um `Endpoint` configuração fornece apenas uma porta *por processo de host*. O modelo de alojamento atual do Service Fabric permite que várias instâncias de serviço e/ou as réplicas sejam hospedados no mesmo processo. Isso significa que cada um irá partilhar a mesma porta quando alocados através do `Endpoint` configuração. Várias **HTTP. sys** instâncias podem partilhar uma porta com o subjacentes **HTTP. sys** recurso de compartilhamento de porta. Mas não é suportado pelo `HttpSysCommunicationListener` devido a complicações introduz para pedidos de cliente. Uso de porta dinâmica, o Kestrel é o servidor web sugerida.

## <a name="kestrel-in-reliable-services"></a>Kestrel no Reliable Services
Pode usar o Kestrel no Reliable Services através da importação de **Microsoft.ServiceFabric.AspNetCore.Kestrel** pacote NuGet. Este pacote contém `KestrelCommunicationListener`, uma implementação de `ICommunicationListener`. `KestrelCommunicationListener` permite-lhe criar um WebHost do ASP.NET Core dentro de um serviço fiável com Kestrel que o servidor web.

Kestrel é que um servidor web para várias plataformas para o ASP.NET Core com base no libuv, uma biblioteca de e/s assíncrona de várias plataformas. Ao contrário de HTTP. sys, Kestrel não utiliza um Gestor de ponto de extremidade centralizado. Também ao contrário de HTTP. sys, Kestrel não suporta a partilha de portas entre vários processos. Cada instância de Kestrel tem de utilizar uma porta única.

![Diagrama de kestrel][4]

### <a name="kestrel-in-a-stateless-service"></a>Kestrel num serviço sem estado
Para utilizar `Kestrel` num serviço sem estado, substituir o `CreateServiceInstanceListeners` método e retornar um `KestrelCommunicationListener` instância:

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

### <a name="kestrel-in-a-stateful-service"></a>Kestrel num serviço com estado
Para utilizar `Kestrel` no serviço com estado, substituir o `CreateServiceReplicaListeners` método e retornar um `KestrelCommunicationListener` instância:

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

Neste exemplo, uma instância singleton do `IReliableStateManager` é fornecido para o contêiner de injeção de dependência WebHost. Isso não é estritamente necessário, mas permite-lhe utilizar `IReliableStateManager` e coleções fiáveis no seus métodos de ação de controlador MVC.

Uma `Endpoint` é o nome de configuração *não* fornecido para `KestrelCommunicationListener` num serviço com estado. Isso é explicado mais detalhadamente na secção seguinte.

### <a name="configure-kestrel-to-use-https"></a>Configurar o Kestrel para utilizar HTTPS
Ao ativar os HTTPS com o Kestrel no seu serviço, precisará definir várias opções de escuta. Atualização do `ServiceInstanceListener` para utilizar um *EndpointHttps* ponto final e escutar numa porta específica (por exemplo, a porta 443). Quando a configuração do host da web para utilizar o servidor de web Kestrel, tem de configurar o Kestrel para escutar os endereços IPv6 em todas as interfaces de rede: 

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

Para obter um exemplo completo num tutorial, veja [configurar o Kestrel para utilizar HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#configure-kestrel-to-use-https).


### <a name="endpoint-configuration"></a>Configuração do ponto final
Um `Endpoint` não é necessária configuração para utilizar o Kestrel. 

Kestrel é um servidor de web autónoma simples. Ao contrário de HTTP. sys (ou HttpListener), ele não precisa uma `Endpoint` configuração no servicemanifest. XML porque ele não requer o registo de URL antes de iniciar. 

#### <a name="use-kestrel-with-a-static-port"></a>Utilizar o Kestrel com uma porta estática
Pode configurar uma porta estática no `Endpoint` configuração do servicemanifest. XML para uso com o Kestrel. Embora não seja estritamente necessário, oferece dois benefícios potenciais:
 - Se a porta não se enquadra no intervalo de porta da aplicação, é aberto através da firewall do SO ao Service Fabric.
 - O URL fornecido para por meio de `KestrelCommunicationListener` irá utilizar esta porta.

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

Se um `Endpoint` está configurado, o nome deve ser passado para o `KestrelCommunicationListener` construtor: 

```csharp
new KestrelCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) => ...
```

Se o servicemanifest. xml não usa um `Endpoint` configuração, omitir o nome no `KestrelCommunicationListener` construtor. Neste caso, irá utilizar uma porta dinâmica. Veja a secção seguinte para obter mais informações sobre isso.

#### <a name="use-kestrel-with-a-dynamic-port"></a>Utilizar o Kestrel com uma porta dinâmica
Kestrel não é possível utilizar a atribuição automática de porta do `Endpoint` configuração no servicemanifest. XML. Isso ocorre porque automática a porta de atribuição de um `Endpoint` configuração atribui uma porta única por *alojar processo*, e um processo de host único pode conter várias instâncias de Kestrel. Isso não funciona com o Kestrel porque ele não dá suporte a partilha de portas. Por conseguinte, cada instância de Kestrel tem de ser aberta numa porta única.

Para utilizar a atribuição de porta dinâmica com Kestrel, omita o `Endpoint` configuração no servicemanifest. XML e não passar um nome de ponto final para o `KestrelCommunicationListener` construtor, da seguinte forma:

```csharp
new KestrelCommunicationListener(serviceContext, (url, listener) => ...
```

Nesta configuração, `KestrelCommunicationListener` selecionará automaticamente uma porta não utilizada do intervalo de porta da aplicação.

## <a name="service-fabric-configuration-provider"></a>Fornecedor de configuração do Service Fabric
Configuração de aplicações em ASP.NET Core se baseia em pares chave-valor estabelecidos pelo fornecedor de configuração. Leia [configuração no ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/) compreender mais no geral ASP.NET Core suporte de configuração.

Esta secção descreve como o fornecedor de configuração do Service Fabric se integra com a configuração do ASP.NET Core importando o `Microsoft.ServiceFabric.AspNetCore.Configuration` pacote NuGet.

### <a name="addservicefabricconfiguration-startup-extensions"></a>Extensões de inicialização de AddServiceFabricConfiguration
Depois de importar o `Microsoft.ServiceFabric.AspNetCore.Configuration` pacote NuGet, terá de registar a origem de configuração do Service Fabric com a API de configuração do ASP.NET Core. Fazer isso marcando **AddServiceFabricConfiguration** extensões no `Microsoft.ServiceFabric.AspNetCore.Configuration` espaço de nomes em relação a `IConfigurationBuilder`.

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

Agora o serviço ASP.NET Core pode acessar as definições de configuração do Service Fabric, assim como qualquer outra configuração de aplicativo. Por exemplo, pode utilizar o padrão de opções ao carregar as definições em objetos com rigidez de tipos.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<MyOptions>(Configuration);  // Strongly typed configuration object.
    services.AddMvc();
}
```
### <a name="default-key-mapping"></a>Mapeamento de chave padrão
Por predefinição, o fornecedor de configuração do Service Fabric inclui o nome do pacote, o nome da seção e o nome de propriedade. Juntos, eles formam a chave de configuração do ASP.NET Core, da seguinte forma:
```csharp
$"{this.PackageName}{ConfigurationPath.KeyDelimiter}{section.Name}{ConfigurationPath.KeyDelimiter}{property.Name}"
```

Por exemplo, se tiver um pacote de configuração com o nome `MyConfigPackage` com o seguinte conteúdo, em seguida, o valor de configuração vai estar disponível no ASP.NET Core `IConfiguration` através de *MyConfigPackage:MyConfigSection:MyParameter*.
```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">  
  <Section Name="MyConfigSection">
    <Parameter Name="MyParameter" Value="Value1" />
  </Section>  
</Settings>
```
### <a name="service-fabric-configuration-options"></a>Opções de configuração do Service Fabric
O fornecedor de configuração do Service Fabric também suporta `ServiceFabricConfigurationOptions` para alterar o comportamento padrão do mapeamento de chave.

#### <a name="encrypted-settings"></a>Definições encriptadas
O Service Fabric suporta as definições encriptadas, assim como o fornecedor de configuração do Service Fabric. As definições encriptadas não são desencriptadas para o ASP.NET Core `IConfiguration` por predefinição. Os valores criptografados são armazenados em vez disso. Mas se quiser desencriptar o valor para armazenar em ASP.NET Core IConfiguration, pode definir o *DecryptValue* sinalizador como falso no `AddServiceFabricConfiguration` extensão, da seguinte forma:

```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    var builder = new ConfigurationBuilder()        
        .AddServiceFabricConfiguration(activationContext, (options) => options.DecryptValue = true); // set flag to decrypt the value
    Configuration = builder.Build();
}
```
#### <a name="multiple-configuration-packages"></a>Vários pacotes de configuração
O Service Fabric suporta vários pacotes de configuração. Por predefinição, o nome do pacote está incluído na chave de configuração. Mas pode definir o `IncludePackageName` sinalizador como false, da seguinte forma:
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
#### <a name="custom-key-mapping-value-extraction-and-data-population"></a>Mapeamento de chave personalizado, extração de valor e a população de dados
O fornecedor de configuração do Service Fabric também suporta cenários mais avançados para personalizar o mapeamento de chave com `ExtractKeyFunc` e personalizada-extrair os valores com `ExtractValueFunc`. Pode até mesmo alterar todo o processo de população de dados de configuração do Service Fabric para a configuração do ASP.NET Core, utilizando `ConfigAction`.

Os exemplos a seguir ilustram como utilizar `ConfigAction` para personalizar a população de dados:
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
O fornecedor de configuração do Service Fabric também suporta atualizações de configuração. Pode usar o ASP.NET Core `IOptionsMonitor` para receber notificações de alteração e, em seguida, utilizar `IOptionsSnapshot` recarregar os dados de configuração. Para obter mais informações, consulte [opções do ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options).

Estas opções são suportadas por predefinição. Não são necessárias mais codificação é necessária para ativar as atualizações de configuração.

## <a name="scenarios-and-configurations"></a>Cenários e configurações
Esta seção fornece a combinação de servidor web, configuração da porta, opções de integração do Service Fabric e outras configurações que recomendamos para resolver os seguintes cenários:
 - Expostos externamente os serviços sem estado ASP.NET Core
 - Só de interno serviços sem estado ASP.NET Core
 - Só de interno serviços com estado ASP.NET Core

Uma **externamente expostos serviço** é aquele que expõe um ponto final que é chamado a partir de fora do cluster, normalmente por meio de um balanceador de carga.

Uma **apenas internos** serviço é um ponto final de cujo apenas é chamado de dentro do cluster.

> [!NOTE]
> Pontos finais de serviço com estado, geralmente, não devem ser expostos à internet. Clusters por trás de balanceadores de carga que não estão cientes da resolução de serviço do Service Fabric, por exemplo, o Balanceador de carga do Azure, que não poderá expor serviços com estado. Isso ocorre porque o Balanceador de carga não será capaz de localizar e encaminhar o tráfego para a réplica de serviço com estado apropriado. 

### <a name="externally-exposed-aspnet-core-stateless-services"></a>Expostos externamente os serviços sem estado ASP.NET Core
Kestrel é o servidor de web sugeridas para os serviços front-end que expõem pontos finais HTTP externos, o acesso à internet. No Windows, o HTTP. sys pode fornecer capacidade compartilhamento de porta, o que permite-lhe alojar vários serviços web no mesmo conjunto de nós com a mesma porta. Neste cenário, os serviços web são diferenciados por nome de anfitrião ou o caminho, sem depender de um gateway ou proxy front-end para fornecer encaminhamento HTTP.
 
Quando exposto à internet, um serviço sem estado deve utilizar um ponto final bem conhecido e estável que está acessível através de um balanceador de carga. Irá fornecer este URL aos utilizadores da sua aplicação. Recomendamos a seguinte configuração:

|  |  | **Notas** |
| --- | --- | --- |
| Servidor Web | Kestrel | Kestrel é o servidor de web preferencial, dado que é suportada em Windows e Linux. |
| Configuração da porta | estático | Uma porta estática bem conhecida deve ser configurada no `Endpoints` configuração do servicemanifest. XML, por exemplo 80 para HTTP ou 443 para HTTPS. |
| ServiceFabricIntegrationOptions | Nenhuma | Utilize o `ServiceFabricIntegrationOptions.None` opção quando configurar o middleware de integração do Service Fabric, para que o serviço não tenta validar pedidos recebidos para um identificador exclusivo. Utilizadores externos do seu aplicativo não saberá as informações de identificação exclusivas que utiliza o middleware. |
| Contagem de Instâncias | -1 | Em casos de utilização típicos, a definição da contagem de instância deve ser definida como *-1*. Isso é feito para que uma instância está disponível em todos os nós que recebem o tráfego de um balanceador de carga. |

Se vários serviços expostos externamente partilharem o mesmo conjunto de nós, pode usar o HTTP. sys com um caminho de URL exclusivo, mas estável. Isso pode ser feito modificando o URL fornecido ao configurar IWebHost. Tenha em atenção que isto se aplica ao HTTP. sys apenas.

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

### <a name="internal-only-stateless-aspnet-core-service"></a>Serviço ASP.NET Core sem monitorização de estado só de interno
Serviços sem estado que são chamados apenas de dentro do cluster devem utilizar URLs exclusivas e atribuídos dinamicamente portas para garantir a cooperação entre vários serviços. Recomendamos a seguinte configuração:

|  |  | **Notas** |
| --- | --- | --- |
| Servidor Web | Kestrel | Embora seja possível usar o HTTP. sys para serviços sem estado internos, o Kestrel é o melhor servidor para permitir que várias instâncias do serviço compartilhar um host.  |
| Configuração da porta | dinamicamente atribuído | Várias réplicas de um serviço com monitorização de estado podem partilhar um processo de anfitrião ou o sistema operativo anfitrião e, portanto, terá de portas exclusivas. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | Com a atribuição de porta dinâmica, esta definição impede que o problema de identidade errada descrito anteriormente. |
| InstanceCount | qualquer | A contagem de instâncias definição pode ser definida como qualquer valor necessária para operar o serviço. |

### <a name="internal-only-stateful-aspnet-core-service"></a>Serviço ASP.NET Core com monitorização de estado só de interno
Serviços com estado que são chamados apenas de dentro do cluster devem utilizar portas dinamicamente atribuídas para garantir a cooperação entre vários serviços. Recomendamos a seguinte configuração:

|  |  | **Notas** |
| --- | --- | --- |
| Servidor Web | Kestrel | O `HttpSysCommunicationListener` não é projetado para ser usado por serviços com estado, na qual as réplicas compartilham um processo de host. |
| Configuração da porta | dinamicamente atribuído | Várias réplicas de um serviço com monitorização de estado podem partilhar um processo de anfitrião ou o sistema operativo anfitrião e, portanto, terá de portas exclusivas. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | Com a atribuição de porta dinâmica, esta definição impede que o problema de identidade errada descrito anteriormente. |

## <a name="next-steps"></a>Passos Seguintes
[Depurar a sua aplicação do Service Fabric com o Visual Studio](service-fabric-debugging-your-application.md)


<!--Image references-->
[0]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-standalone.png
[1]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-servicefabric.png
[2]:./media/service-fabric-reliable-services-communication-aspnetcore/integration.png
[3]:./media/service-fabric-reliable-services-communication-aspnetcore/httpsys.png
[4]:./media/service-fabric-reliable-services-communication-aspnetcore/kestrel.png
