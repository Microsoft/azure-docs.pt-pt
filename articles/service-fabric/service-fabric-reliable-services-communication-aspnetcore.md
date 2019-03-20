---
title: Serviço de comunicação com o ASP.NET Core | Documentos da Microsoft
description: Saiba como utilizar o ASP.NET Core no Reliable Services com e sem estado.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: 8aa4668d-cbb6-4225-bd2d-ab5925a868f2
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 10/12/2018
ms.author: vturecek
ms.openlocfilehash: d74cee712b33f8d8d9924b9b8906ccd97e0b1756
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57902999"
---
# <a name="aspnet-core-in-service-fabric-reliable-services"></a>ASP.NET Core no Service Fabric Reliable Services

O ASP.NET Core é uma nova estrutura de código-fonte aberto e Multiplataforma para a criação de modernas aplicações baseadas na nuvem ligada à Internet, como aplicações web, aplicações de IoT e back-ends móveis. 

Este artigo é um guia detalhado para hospedagem de serviços do ASP.NET Core no Service Fabric Reliable Services com o **Microsoft.ServiceFabric.AspNetCore.** * conjunto de pacotes NuGet.

Para obter um tutorial introdutório sobre o ASP.NET Core no Service Fabric e instruções sobre como obter a configuração do ambiente de desenvolvimento, consulte [criar uma aplicação .NET](service-fabric-tutorial-create-dotnet-app.md).

O restante deste artigo pressupõe que já estiver familiarizado com o ASP.NET Core. Se não, é recomendável ler o [conceitos básicos do ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/index).

## <a name="aspnet-core-in-the-service-fabric-environment"></a>ASP.NET Core no ambiente do Service Fabric

Aplicações ASP.NET Core e do Service Fabric podem executar no .NET Core, bem como o .NET Framework completo. ASP.NET Core podem ser utilizado de duas formas diferentes no Service Fabric:
 - **Hospedado como um executável convidado**. Isso é usado principalmente para executar aplicativos existentes do ASP.NET Core no Service Fabric sem alterações de código.
 - **Executar dentro de um serviço fiável**. Isso permite uma melhor integração com o tempo de execução do Service Fabric e permite com monitoração de estado ASP.NET Core serviços.

O restante deste artigo explica como usar o ASP.NET Core dentro de um serviço fiável com os componentes de integração do ASP.NET Core que vêm com o SDK do Service Fabric. 

## <a name="service-fabric-service-hosting"></a>Hospedagem de serviços do Service Fabric

No Service Fabric, um ou mais instâncias de e/ou réplicas do seu serviço execute *processo de anfitrião do serviço*, um ficheiro executável que executa o código do serviço. Que, como um autor de serviço, possui o processo de host de serviço e o Service Fabric é ativado e monitoriza-a para.

ASP.NET tradicional (até MVC 5) está intimamente ligado ao IIS por meio da dll. ASP.NET Core fornece uma separação entre o servidor web e a sua aplicação web. Isso permite que os aplicativos da web seja portátil entre servidores web diferentes e também permite que servidores web estejam *autoalojado*, que significa que pode iniciar um servidor web no seu próprio processo, em vez de um processo que é propriedade de web dedicados software de servidor como o IIS. 

Para combinar um serviço do Service Fabric e ASP.NET, como um executável de convidado ou num serviço fiável, tem de ser possível iniciar o ASP.NET dentro do processo de host de serviço. Hospedagem interna do ASP.NET Core permite-lhe fazê-lo.

## <a name="hosting-aspnet-core-in-a-reliable-service"></a>ASP.NET Core num serviço fiável de alojamento
Normalmente, aplicativos de hospedagem interna ASP.NET Core de criar um WebHost no ponto de entrada de uma aplicação, tal como o `static void Main()` método na `Program.cs`. Neste caso, o ciclo de vida do WebHost está vinculado ao ciclo de vida do processo.

![Num processo de hospedagem ASP.NET Core][0]

No entanto, o ponto de entrada do aplicativo não é o local indicado para criar um WebHost num serviço fiável, porque o ponto de entrada de aplicativo só é utilizado para registar um tipo de serviço com o tempo de execução do Service Fabric, para que ele pode criar instâncias desse tipo de serviço. O WebHost deve ser criada num serviço fiável o próprio. Dentro do processo de host de serviço, instâncias de serviço e/ou de réplicas podem passar por vários ciclos de vida. 

Uma instância de Reliable Service é representada por sua classe de serviço derivar `StatelessService` ou `StatefulService`. A pilha de comunicações para um serviço está contida num `ICommunicationListener` implementação na sua classe de serviço. O `Microsoft.ServiceFabric.AspNetCore.*` pacotes de NuGet contenham implementações de `ICommunicationListener` que iniciar e gerir o WebHost do ASP.NET Core para Kestrel ou HttpSys num serviço fiável.

![ASP.NET Core num serviço fiável de alojamento][1]

## <a name="aspnet-core-icommunicationlisteners"></a>ICommunicationListeners do ASP.NET Core
O `ICommunicationListener` implementações para Kestrel e HttpSys no `Microsoft.ServiceFabric.AspNetCore.*` pacotes NuGet têm padrões de utilização semelhante, mas executar ligeiramente diferentes ações específicas para cada servidor web. 

Ambos os serviços de escuta de comunicação de fornecer um construtor que aceita os seguintes argumentos:
 - **`ServiceContext serviceContext`**: O `ServiceContext` objeto que contém informações sobre o serviço em execução.
 - **`string endpointName`**: o nome de um `Endpoint` configuração no servicemanifest. XML. Isto é principalmente em que os serviços de escuta de dois comunicação são diferentes: HttpSys **requer** um `Endpoint` configuração, enquanto o Kestrel não faz.
 - **`Func<string, AspNetCoreCommunicationListener, IWebHost> build`**: um lambda que implementar em que cria e retorna um `IWebHost`. Isto permite-lhe configurar `IWebHost` a forma como faria normalmente num aplicativo do ASP.NET Core. O lambda fornece um URL que é gerado para consoante a integração do Service Fabric opções utilizar e o `Endpoint` configuração fornecer. Que URL, em seguida, pode ser modificada ou utilizada como-é iniciar o servidor web.

## <a name="service-fabric-integration-middleware"></a>Middleware de integração do Service Fabric
O `Microsoft.ServiceFabric.AspNetCore` inclui o pacote NuGet do `UseServiceFabricIntegration` método de extensão no `IWebHostBuilder` que adiciona suporte para o Service Fabric middleware. Este middleware configura o Kestrel ou HttpSys `ICommunicationListener` para registar um URL de serviço único com o serviço de nomenclatura do Service Fabric e, em seguida, valida os pedidos de cliente para garantir que os clientes estão a ligar para o serviço correto. Isso é necessário num ambiente de host partilhados, tais como o Service Fabric, em que várias aplicações web podem ser executados no mesmo físico ou máquina virtual, mas não utilize nomes de host único, para impedir que os clientes por engano, conexão com o serviço de errado. Este cenário é descrito mais detalhadamente na secção seguinte.

### <a name="a-case-of-mistaken-identity"></a>Um caso de identidade errada
Réplicas de serviço, independentemente do protocolo, ouça numa combinação de IP: porta exclusiva. Depois de uma réplica de serviço foi iniciado um ponto de extremidade IP: porta a escutar, relatórios esse endereço de ponto final para o serviço Service Fabric Naming onde possam ser detetado por clientes ou outros serviços. Se os serviços utilizam portas da aplicação atribuído de forma dinâmica, uma réplica de serviço por coincidência pode utilizar o mesmo ponto de final de IP: porta de outro serviço que foi anteriormente no mesmo físico ou máquina virtual. Isto pode fazer com que um cliente por engano se ligue ao serviço errado. Isto pode acontecer se a seguinte sequência de eventos ocorre:

 1. O serviço escuta na 10.0.0.1:30000 através de HTTP. 
 2. Resolve o serviço de cliente e obtém o endereço 10.0.0.1:30000
 3. Serviço A move para um nó diferente.
 4. Serviço B for colocada em 10.0.0.1 e por coincidência utiliza a mesma porta 30000.
 5. Cliente tenta estabelecer ligação ao serviço A com 10.0.0.1:30000 endereço em cache.
 6. Cliente está agora ligado com êxito para o serviço B não percebendo que ele está ligado ao serviço errado.

Isso pode causar bugs em momentos aleatórios que podem ser difíceis de diagnosticar. 

### <a name="using-unique-service-urls"></a>Utilizando os URLs de serviço único
Para evitar esta situação, os serviços podem publicar um ponto final para o serviço de nomenclatura de mensagens em fila com um identificador exclusivo e, em seguida, validar esse identificador exclusivo durante os pedidos de cliente. Esta é uma ação cooperativo entre os serviços num ambiente confiável de não inquilino hipóteses, semi-hostil. Isso não fornece autenticação do serviço segura num ambiente de inquilino de hipóteses, semi-hostil.

Num ambiente fidedigno, o middleware adicionado o `UseServiceFabricIntegration` método acrescenta automaticamente um identificador exclusivo para o endereço que é publicado para o serviço de nomenclatura e valida esse identificador em cada pedido. Se o identificador não corresponderem, o middleware devolve imediatamente uma resposta de HTTP 410 ficaram no passado.

Serviços que utilizam uma porta dinamicamente atribuída deve tornar a utilização deste middleware.

Os serviços que utilizam uma porta única fixa não têm esse problema num ambiente cooperativo. Uma porta única fixa é normalmente utilizada para pontos finais direcionados para serviços que precisam de uma porta bem conhecida para aplicativos de cliente ligar a. Por exemplo, a maioria dos aplicativos da web Internet utilizará a porta 80 ou 443 para ligações de navegador da web. Neste caso, o identificador exclusivo não deve ser ativado.

O diagrama seguinte mostra o fluxo de pedido com o middleware ativado:

![Integração do ASP.NET Core do Service Fabric][2]

Tanto o Kestrel HttpSys `ICommunicationListener` implementações usarem esse mecanismo exatamente da mesma forma. Embora HttpSys internamente pode diferenciar pedidos com base nos caminhos de URL exclusivos com subjacente *HTTP. sys* recurso, o que é a funcionalidade de compartilhamento de porta *não* utilizado pelo HttpSys `ICommunicationListener` implementação de uma vez que irá resultar em 503 de HTTP e HTTP 404 códigos de estado de erro no cenário descrito anteriormente. Que por sua vez torna difícil para os clientes determinar a intenção do erro, como HTTP 503 e HTTP 404 já normalmente são usadas para indicar outros erros. Portanto, o Kestrel e HttpSys `ICommunicationListener` implementações padronizar o middleware fornecido pelo `UseServiceFabricIntegration` método de extensão, para que os clientes só tem de efetuar um ponto de extremidade de serviço voltar a resolver ação sobre respostas de HTTP 410.

## <a name="httpsys-in-reliable-services"></a>HttpSys no Reliable Services
HttpSys pode ser usado num serviço fiável através da importação de **Microsoft.ServiceFabric.AspNetCore.HttpSys** pacote NuGet. Este pacote contém `HttpSysCommunicationListener`, uma implementação de `ICommunicationListener`, que permite-lhe criar um WebHost do ASP.NET Core dentro de um serviço fiável com HttpSys que o servidor web.

HttpSys foi criado sobre o [Windows HTTP Server API](https://msdn.microsoft.com/library/windows/desktop/aa364510(v=vs.85).aspx). Esta opção utiliza a *HTTP. sys* driver do kernel utilizado pelo IIS para processar pedidos HTTP e roteá-los para processos de execução de aplicativos web. Isso permite que vários processos no mesmo físico ou máquina virtual para alojar aplicações web na mesma porta, a ambigüidade removida por um caminho de URL exclusivo ou o nome de anfitrião. Esses recursos são úteis no Service Fabric para alojar vários Web sites no mesmo cluster.

>[!NOTE]
>Implementação de HttpSys funciona apenas na plataforma Windows.

O diagrama seguinte ilustra como HttpSys utiliza a *HTTP. sys* driver do kernel no Windows para a partilha de portas:

![http.sys][3]

### <a name="httpsys-in-a-stateless-service"></a>HttpSys num serviço sem estado
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

### <a name="httpsys-in-a-stateful-service"></a>HttpSys num serviço com estado

`HttpSysCommunicationListener` Atualmente não foi concebido para utilização em serviços com estado devido a complicações com subjacente *HTTP. sys* recurso de compartilhamento de porta. Para obter mais informações, consulte a secção seguinte na alocação de porta dinâmica com HttpSys. Para serviços com estado, o Kestrel é o servidor web recomendada.

### <a name="endpoint-configuration"></a>Configuração do ponto final

Um `Endpoint` configuração é necessária para servidores web que utilizam a API de servidor Windows HTTP, incluindo HttpSys. Servidores Web que utilizam o Windows HTTP Server API primeiro tem de reservar o URL com *HTTP. sys* (isso normalmente é feito com o [netsh](https://msdn.microsoft.com/library/windows/desktop/cc307236(v=vs.85).aspx) ferramenta). Esta ação requer privilégios elevados que não têm os seus serviços por predefinição. As opções de "http" ou "https" para o `Protocol` propriedade do `Endpoint` configuração no *servicemanifest. XML* servem especificamente para instruir o runtime do Service Fabric para registar um URL com  *o HTTP. sys* no seu nome com o [ *curinga forte* ](https://msdn.microsoft.com/library/windows/desktop/aa364698(v=vs.85).aspx) prefixo de URL.

Por exemplo, para reservar `http://+:80` para um serviço, a seguinte configuração deve ser utilizada no servicemanifest. XML:

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

#### <a name="use-httpsys-with-a-static-port"></a>Utilizar HttpSys com uma porta estática
Para utilizar uma porta estática com HttpSys, indique o número de porta no `Endpoint` configuração:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

#### <a name="use-httpsys-with-a-dynamic-port"></a>Utilizar HttpSys com uma porta dinâmica
Para utilizar uma porta dinamicamente atribuída com HttpSys, omita o `Port` propriedade o `Endpoint` configuração:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
```

Uma porta dinâmica alocada por um `Endpoint` configuração fornece apenas uma porta *por processo de host*. O modelo de alojamento atual do Service Fabric permite que várias instâncias de serviço e/ou as réplicas sejam hospedados no mesmo processo, o que significa que cada um deles irá partilhar a mesma porta quando alocados através do `Endpoint` configuração. Várias instâncias de HttpSys podem partilhar uma porta utilizando subjacente *HTTP. sys* porta partilha funcionalidade, mas que não é suportada pelo `HttpSysCommunicationListener` devido a complicações introduz para pedidos de cliente. Uso de porta dinâmica, o Kestrel é o servidor web recomendada.

## <a name="kestrel-in-reliable-services"></a>Kestrel no Reliable Services
Kestrel pode ser usado num serviço fiável através da importação de **Microsoft.ServiceFabric.AspNetCore.Kestrel** pacote NuGet. Este pacote contém `KestrelCommunicationListener`, uma implementação de `ICommunicationListener`, que permite-lhe criar um WebHost do ASP.NET Core dentro de um serviço fiável com Kestrel que o servidor web.

Kestrel é que um servidor web para várias plataformas para o ASP.NET Core com base no libuv, uma biblioteca de e/s assíncrona de várias plataformas. Ao contrário de HttpSys, Kestrel não utiliza um Gestor de ponto de extremidade centralizado, como *HTTP. sys*. E, ao contrário de HttpSys, Kestrel não suporta a partilha de portas entre vários processos. Cada instância de Kestrel tem de utilizar uma porta única.

![kestrel][4]

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

Uma `Endpoint` é o nome de configuração **não** fornecido para `KestrelCommunicationListener` num serviço com estado. Isso é explicado mais detalhadamente na secção seguinte.

### <a name="configure-kestrel-to-use-https"></a>Configurar o Kestrel para utilizar HTTPS
Ao ativar os HTTPS com o Kestrel no seu serviço, terá de definir várias opções de escuta.  Atualização do `ServiceInstanceListener` para utilizar um ponto de extremidade EndpointHttps e escutar numa porta específica (por exemplo, a porta 443). Quando a configuração do host da web para utilizar o Kestrel server, tem de configurar o Kestrel para escutar os endereços IPv6 em todas as interfaces de rede: 

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

Para obter um exemplo completo utilizado um tutorial, veja [configurar o Kestrel para utilizar HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#configure-kestrel-to-use-https).


### <a name="endpoint-configuration"></a>Configuração do ponto final
Um `Endpoint` configuração não é necessária para utilizar o Kestrel. 

Kestrel é um servidor de web autónoma simples; ao contrário HttpSys (ou HttpListener), não é necessário um `Endpoint` configuração no *servicemanifest. XML* porque ele não necessita de registo de URL antes de iniciar. 

#### <a name="use-kestrel-with-a-static-port"></a>Utilizar o Kestrel com uma porta estática
É possível configurar uma porta estática no `Endpoint` configuração do servicemanifest. XML para uso com o Kestrel. Embora não seja estritamente necessário, ele fornece dois benefícios potenciais:
 1. Se a porta não se encontra no intervalo de porta da aplicação, é aberto através da firewall do SO ao Service Fabric.
 2. O URL fornecido para por meio de `KestrelCommunicationListener` irá utilizar esta porta.

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

Se um `Endpoint` configuração não é utilizada, omitir o nome no `KestrelCommunicationListener` construtor. Neste caso, será utilizada uma porta dinâmica. Veja a secção seguinte para obter mais informações.

#### <a name="use-kestrel-with-a-dynamic-port"></a>Utilizar o Kestrel com uma porta dinâmica
Kestrel não é possível utilizar a atribuição automática de porta do `Endpoint` configuração no servicemanifest. XML, porque a atribuição automática de porta de uma `Endpoint` configuração atribui uma porta única por *alojar processo*, e um processo de host único pode conter várias instâncias de Kestrel. Uma vez que o Kestrel não suporta a partilha de portas, isso não funcionar conforme cada instância de Kestrel tem de ser aberta numa porta única.

Para utilizar a atribuição de porta dinâmica com Kestrel, omita o `Endpoint` configuração no servicemanifest. XML e não passar um nome de ponto final para o `KestrelCommunicationListener` construtor:

```csharp
new KestrelCommunicationListener(serviceContext, (url, listener) => ...
```

Nesta configuração, `KestrelCommunicationListener` selecionará automaticamente uma porta não utilizada do intervalo de porta da aplicação.

## <a name="service-fabric-configuration-provider"></a>Fornecedor de configuração do Service Fabric
Configuração de aplicações em ASP.NET Core se baseia em pares chave-valor estabelecidas pelos fornecedores de configuração, leia [configuração no ASP.NET Core](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/) compreender mais no geral ASP.NET Core suporte de configuração.

Esta secção descreve o fornecedor de configuração de recursos de infraestrutura do serviço para integrar com a configuração do ASP.NET Core, ao importar o `Microsoft.ServiceFabric.AspNetCore.Configuration` pacote NuGet.

### <a name="addservicefabricconfiguration-startup-extensions"></a>Extensões de inicialização de AddServiceFabricConfiguration
Depois que importação `Microsoft.ServiceFabric.AspNetCore.Configuration` pacote NuGet, terá de registar a origem de configuração do Service Fabric com a configuração do ASP.NET Core API pelo **AddServiceFabricConfiguration** extensões no `Microsoft.ServiceFabric.AspNetCore.Configuration` espaço de nomes em relação a `IConfigurationBuilder`

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

Agora o serviço ASP.NET Core pode acessar as definições de configuração do Service Fabric assim como qualquer outra configuração de aplicativo. Por exemplo, pode utilizar o padrão de opções ao carregar as definições em objetos com rigidez de tipos.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<MyOptions>(Configuration);  // Strongly typed configuration object.
    services.AddMvc();
}
```
### <a name="default-key-mapping"></a>Chave predefinida de mapeamento
Por predefinição, o fornecedor do Service Fabric configuraiton inclui nome do pacote, o nome de secção e o nome da propriedade para formar a configuração do asp.net core com os seguintes função da chave:
```csharp
$"{this.PackageName}{ConfigurationPath.KeyDelimiter}{section.Name}{ConfigurationPath.KeyDelimiter}{property.Name}"
```

Por exemplo, se tiver um pacotes de configuração com o nome `MyConfigPackage` com abaixo conteúdo, em seguida, o valor de configuração vai estar disponível no ASP.NET Core `IConfiguration` por meio da chave *MyConfigPackage:MyConfigSection:MyParameter*
```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">  
  <Section Name="MyConfigSection">
    <Parameter Name="MyParameter" Value="Value1" />
  </Section>  
</Settings>
```
### <a name="service-fabric-configuration-options"></a>Opções de configuração do Service Fabric
Fornecedor de configuração do Service Fabric também suporta `ServiceFabricConfigurationOptions` para alterar o comportamento padrão do mapeamento de chave.

#### <a name="encrypted-settings"></a>Definições encriptadas
O Service Fabric suporta para encriptar as definições, o fornecedor de configuração do Service Fabric também oferece suporte a isso. A seguir segura ao princípio da predefinição, o descrypted de are't as definições encriptadas por predefinição para ASP.NET Core `IConfiguration`, o valor encriptado são armazenadas em vez disso. No entanto, se quiser desencriptar o valor para armazenar em ASP.NET Core IConfiguration foi possível definir o sinalizador de DecryptValue como false no `AddServiceFabricConfiguration` extensões da seguinte forma:

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
O Service Fabric suporta vários pacotes de configuração. Por predefinição, o nome do pacote está incluído na configuração da chave. É possível definir o `IncludePackageName` sinalizador para alterar o comportamento predefinido.
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
Além de acima 2 sinalizadores para alterar o comportamento predefinido, fornecedor de configuração do Service Fabric também suporta a mais cenários avançados como personalizado o mapeamento de chave via `ExtractKeyFunc` e como personalizado, extraia os valores por meio de `ExtractValueFunc`. Poderia até alterar o processo inteiro para preencher dados de configuração do Service Fabric para configuração do ASP.NET Core via `ConfigAction`.

Os exemplos a seguir ilustram a utilizar `ConfigAction` para personalizar a população de dados.
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
### <a name="configuration-update"></a>Atualização de configuração
Fornecedor de configuração do Service Fabric também suporta a atualização de configuração e pode usar o ASP.NET Core `IOptionsMonitor` para receber notificações de alteração e também com `IOptionsSnapshot` recarregar os dados de configuração. Para obter mais informações, consulte [opções do ASP.NET Core](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/options).

Isto é suportado por predefinição e não são necessárias mais codificação são necessárias para ativar a atualização de configuração.

## <a name="scenarios-and-configurations"></a>Cenários e configurações
Esta secção descreve os seguintes cenários e fornece a combinação recomendada de servidor web, configuração da porta, opções de integração do Service Fabric e outras configurações para alcançar um serviço está a funcionar corretamente:
 - Externamente expostos serviço sem estado ASP.NET Core
 - Só de interna serviço sem estado ASP.NET Core
 - Só de interna serviço com estado ASP.NET Core

Uma **externamente expostos** serviço é aquele que expõe um ponto final acessível a partir de fora do cluster, normalmente por meio de um balanceador de carga.

Uma **apenas internos** service é uma cujo ponto final só está acessível a partir do cluster.

> [!NOTE]
> Pontos finais de serviço com estado, geralmente, não devem ser expostos à Internet. Clusters que estão por trás de balanceadores de carga que não estão cientes de resolução de serviço do Service Fabric, como o Balanceador de carga do Azure, será possível expor serviços com estado, uma vez que o Balanceador de carga não será capaz de localizar e encaminhar o tráfego para o adequado réplica de serviço com estado. 

### <a name="externally-exposed-aspnet-core-stateless-services"></a>Expostos externamente os serviços sem estado ASP.NET Core
Kestrel é o servidor de web recomendada para os serviços front-end que expõem pontos finais HTTP externos, o acesso à Internet. No Windows, HttpSys podem ser utilizadas para fornecer a capacidade de compartilhamento de porta que permite-lhe alojar vários serviços web no mesmo conjunto de nós com a mesma porta, diferenciada por nome de anfitrião ou o caminho, sem depender de um gateway ou proxy front-end para fornecer encaminhamento HTTP.
 
Quando exposto à Internet, um serviço sem estado deve utilizar um ponto final bem conhecido e estável que está acessível através de um balanceador de carga. Este é o URL que irá fornecer aos utilizadores da sua aplicação. Recomenda-se a seguinte configuração:

|  |  | **Notas** |
| --- | --- | --- |
| Servidor Web | Kestrel | Kestrel é o servidor web preferencial, dado que é suportada em Windows e Linux. |
| Configuração da porta | estático | Uma porta estática bem conhecida deve ser configurada no `Endpoints` configuração do servicemanifest. XML, por exemplo 80 para HTTP ou 443 para HTTPS. |
| ServiceFabricIntegrationOptions | Nenhuma | O `ServiceFabricIntegrationOptions.None` opção deve ser utilizada quando configurar o middleware de integração do Service Fabric, para que o serviço não tenta validar pedidos recebidos para um identificador exclusivo. Utilizadores externos do seu aplicativo não saberá que as informações de identificação exclusivas usadas pelo middleware. |
| Contagem de Instâncias | -1 | Em casos de utilização típicos, a definição da contagem de instância deve ser definida como "-1" para que uma instância está disponível em todos os nós que recebem o tráfego de um balanceador de carga. |

Se vários serviços expostos externamente partilharem o mesmo conjunto de nós, HttpSys pode ser utilizado com um caminho de URL exclusivo, mas estável. Isso pode ser feito modificando o URL fornecido ao configurar IWebHost. Tenha em atenção de que isso se aplica a HttpSys apenas.

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
Serviços sem estado que são chamados apenas de dentro do cluster devem utilizar URLs exclusivas e atribuídos dinamicamente portas para garantir a cooperação entre vários serviços. Recomenda-se a seguinte configuração:

|  |  | **Notas** |
| --- | --- | --- |
| Servidor Web | Kestrel | Embora HttpSys podem ser utilizadas para serviços sem estado internos, o Kestrel é o servidor recomendado para permitir que várias instâncias do serviço compartilhar um host.  |
| Configuração da porta | dinamicamente atribuído | Várias réplicas de um serviço com monitorização de estado podem partilhar um processo de anfitrião ou o sistema operativo anfitrião e, portanto, terá de portas exclusivas. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | Com a atribuição de porta dinâmica, esta definição impede que o problema de identidade errada descrito anteriormente. |
| InstanceCount | qualquer | A contagem de instâncias definição pode ser definida como qualquer valor necessária para operar o serviço. |

### <a name="internal-only-stateful-aspnet-core-service"></a>Serviço ASP.NET Core com monitorização de estado só de interno
Serviços com estado que são chamados apenas de dentro do cluster devem utilizar portas dinamicamente atribuídas para garantir a cooperação entre vários serviços. Recomenda-se a seguinte configuração:

|  |  | **Notas** |
| --- | --- | --- |
| Servidor Web | Kestrel | O `HttpSysCommunicationListener` não foi concebido para utilização pelos serviços com estado, na qual as réplicas compartilham um processo de host. |
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
