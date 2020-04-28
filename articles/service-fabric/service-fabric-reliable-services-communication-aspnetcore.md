---
title: Comunicação de serviço com o Núcleo ASP.NET
description: Saiba como utilizar ASP.NET Core em aplicações apátridas e apátridas de Serviço Azure Fidedignade de Serviços.
author: vturecek
ms.topic: conceptual
ms.date: 10/12/2018
ms.author: vturecek
ms.openlocfilehash: 0d432bd19d0689ef508fca0bf24eed4406929f82
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75639637"
---
# <a name="aspnet-core-in-azure-service-fabric-reliable-services"></a>ASP.NET Núcleo em Serviço Azure Tecido Serviço Fiável Serviços Fiáveis

ASP.NET Core é uma estrutura de código aberto e cross-platform. Esta estrutura foi concebida para construir aplicações baseadas na nuvem e ligadas à Internet, tais como aplicações web, aplicações IoT e back ends móveis.

Este artigo é um guia aprofundado para hospedar ASP.NET serviços Core em Serviços Fiáveis de Tecidos, utilizando o **Microsoft.ServiceFabric.AspNetCore.** conjunto de pacotes NuGet.

Para um tutorial introdutório sobre ASP.NET Core em Tecido de Serviço e instruções para configurar o seu ambiente de desenvolvimento, consulte [Tutorial: Crie e implemente uma aplicação com um serviço frontal de API core core aPI ASP.NET e um serviço de back-end imponente.](service-fabric-tutorial-create-dotnet-app.md)

O resto deste artigo assume que já está familiarizado com ASP.NET Core. Caso contrário, leia os fundamentos do [Núcleo de ASP.NET.](https://docs.microsoft.com/aspnet/core/fundamentals/index)

## <a name="aspnet-core-in-the-service-fabric-environment"></a>núcleo ASP.NET no ambiente de tecido de serviço

Tanto ASP.NET aplicações Core como Service Fabric podem ser executadas em .NET Core ou full .NET Framework. Pode utilizar ASP.NET Core de duas formas diferentes no Tecido de Serviço:
 - **Hospedado como um hóspede executável.** Desta forma é usada principalmente para executar aplicações de ASP.NET Core existentes no Tecido de Serviço sem alterações de código.
 - **Corra dentro de um serviço fiável.** Desta forma permite uma melhor integração com o tempo de execução do Tecido de Serviço e permite serviços de ASP.NET Core.

O resto deste artigo explica como usar ASP.NET Core dentro de um serviço fiável, através dos componentes de integração ASP.NET Core que enviam com o SDK de Tecido de Serviço.

## <a name="service-fabric-service-hosting"></a>Hospedagem de serviço de tecido de serviço

No Tecido de Serviço, uma ou mais instâncias e/ou réplicas do seu serviço funcionam num processo de hospedagem de *serviço:* um ficheiro executável que executa o seu código de serviço. Você, como autor de serviço, é dono do processo de hospedamento de serviço, e o Service Fabric ativa-o e monitoriza-o para si.

A ASP.NET tradicional (até MVC 5) está bem acopnada ao IIS através do System.Web.dll. ASP.NET Core fornece uma separação entre o servidor web e a sua aplicação web. Esta separação permite que as aplicações web sejam portáteis entre diferentes servidores web. Também permite que os servidores web sejam *auto-hospedados.* Isto significa que pode iniciar um servidor web no seu próprio processo, em oposição a um processo que é propriedade de software de servidor web dedicado, como o IIS.

Para combinar um serviço de Tecido de Serviço e ASP.NET, seja como um hóspede executável ou num serviço fiável, deve ser capaz de iniciar ASP.NET dentro do seu processo de hospedamento de serviço. ASP.NET auto-hospedagem core permite-lhe fazer isto.

## <a name="hosting-aspnet-core-in-a-reliable-service"></a>Hospedar ASP.NET Core em um serviço confiável
Tipicamente, as aplicações auto-hospedadas ASP.NET Core criam um WebHost no `static void Main()` ponto `Program.cs`de entrada de uma aplicação, como o método em . Neste caso, o ciclo de vida do WebHost está ligado ao ciclo de vida do processo.

![Hospedar ASP.NET Core num processo][0]

Mas o ponto de entrada da aplicação não é o lugar certo para criar um WebHost num serviço confiável. Isto porque o ponto de entrada da aplicação só é utilizado para registar um tipo de serviço com o tempo de funcionamento do Tecido de Serviço, para que possa criar instâncias desse tipo de serviço. O WebHost deve ser criado num serviço fiável. Dentro do processo de hospedamento de serviço, as instâncias de serviço e/ou réplicas podem passar por vários ciclos de vida. 

Uma instância de Serviço Fiável é representada pela `StatelessService` `StatefulService`sua classe de serviço proveniente de ou . A pilha de comunicação para `ICommunicationListener` um serviço está contida numa implementação na sua classe de serviço. Os `Microsoft.ServiceFabric.AspNetCore.*` pacotes NuGet contêm `ICommunicationListener` implementações desse início e gerem o ASP.NET Core WebHost para Kestrel ou HTTP.sys num serviço fiável.

![Diagrama para hospedar ASP.NET Core em um serviço confiável][1]

## <a name="aspnet-core-icommunicationlisteners"></a>ASP.NET Core ICommunicationListeners
As `ICommunicationListener` implementações para Kestrel e HTTP.sys nos pacotes `Microsoft.ServiceFabric.AspNetCore.*` NuGet têm padrões de utilização semelhantes. Mas realizam ações ligeiramente diferentes específicas de cada servidor web. 

Ambos os ouvintes de comunicação fornecem um construtor que toma os seguintes argumentos:
 - **`ServiceContext serviceContext`**: Este `ServiceContext` é o objeto que contém informações sobre o serviço de funcionamento.
 - **`string endpointName`**: Este é o `Endpoint` nome de uma configuração em ServiceManifest.xml. É principalmente onde os dois ouvintes de comunicação diferem. HTTP.sys *requer* `Endpoint` uma configuração, enquanto Kestrel não.
 - **`Func<string, AspNetCoreCommunicationListener, IWebHost> build`**: Trata-se de uma lambda que se `IWebHost`implementa, na qual se cria e devolve um . Permite-lhe configurar `IWebHost` como normalmente faria numa aplicação ASP.NET Core. A lambda fornece um URL que é gerado para si, dependendo das `Endpoint` opções de integração do Tecido de Serviço que utiliza e da configuração que fornece. Em seguida, pode modificar ou utilizar esse URL para iniciar o servidor web.

## <a name="service-fabric-integration-middleware"></a>Middleware de integração de tecido de serviço
O `Microsoft.ServiceFabric.AspNetCore` pacote NuGet `UseServiceFabricIntegration` inclui `IWebHostBuilder` o método de extensão que adiciona o utensílio de meio consciente do Tecido de Serviço. Este middleware confunde os Kestrel ou `ICommunicationListener` HTTP.sys para registar um URL de serviço único com o Serviço de Nomeação de Tecidos de Serviço. Em seguida, valida os pedidos do cliente para garantir que os clientes estão conectados ao serviço certo. 

Este passo é necessário para evitar que os clientes se conectem erradamente ao serviço errado. Isto porque, num ambiente de hospedagem partilhada como o Service Fabric, várias aplicações web podem ser executadas na mesma máquina física ou virtual, mas não usam nomes únicos de anfitriões. Este cenário é descrito mais detalhadamente na secção seguinte.

### <a name="a-case-of-mistaken-identity"></a>Um caso de identidade equivocada
Réplicas de serviço, independentemente do protocolo, ouçam uma combinação IP:porta única. Uma vez que uma réplica de serviço tenha começado a ouvir num ponto final IP:porta, reporta o endereço final do Serviço de Nomeação de Tecidos de Serviço. Lá, clientes ou outros serviços podem descobri-lo. Se os serviços utilizarem portas de aplicação dinamicamente atribuídas, uma réplica de serviço poderá, por coincidência, utilizar o mesmo ponto final IP:porta de outro serviço anteriormente na mesma máquina física ou virtual. Isto pode fazer com que um cliente se ligue erradamente ao serviço errado. Este cenário pode resultar se ocorrer a seguinte sequência de eventos:

 1. Serviço A ouve em 10.0.0.1:30000 sobre HTTP. 
 2. Cliente resolve serviço A e recebe endereço 10.0.0.1.30000.
 3. Serviço A muda para um nó diferente.
 4. O serviço B é colocado em 10.0.0.1 e, por coincidência, utiliza a mesma porta 30000.
 5. O cliente tenta ligar-se ao serviço A com o endereço 10.0.0.1.30000.
 6. O cliente está agora ligado com sucesso ao serviço B, não percebendo que está ligado ao serviço errado.

Isto pode causar insetos em momentos aleatórios que podem ser difíceis de diagnosticar.

### <a name="using-unique-service-urls"></a>Usando URLs de serviço únicos
Para prevenir estes bugs, os serviços podem colocar um ponto final no Serviço de Nomeação com um identificador único e, em seguida, validar esse identificador único durante os pedidos do cliente. Trata-se de uma ação cooperativa entre serviços num ambiente de confiança não hostil. Não fornece autenticação de serviço seguro num ambiente hostil-inquilino.

Num ambiente de confiança, o middleware `UseServiceFabricIntegration` que é adicionado pelo método automaticamente anexa um identificador único ao endereço publicado no Serviço de Nomeação. Valida esse identificador em cada pedido. Se o identificador não corresponder, o middleware devolve imediatamente uma resposta HTTP 410 Gone.

Os serviços que utilizam uma porta dinamicamente atribuída devem utilizar este middleware.

Os serviços que utilizam um porto único fixo não têm este problema num ambiente cooperativo. Uma porta única fixa é normalmente usada para serviços virados externamente que precisam de uma porta bem conhecida para aplicações de clientes para se conectarem. Por exemplo, a maioria das aplicações web viradas para a Internet usarão a porta 80 ou 443 para ligações ao navegador web. Neste caso, o identificador único não deve ser ativado.

O diagrama seguinte mostra o fluxo de pedido com o middleware ativado:

![Fabricação de tecido de serviço ASP.NET integração core][2]

Tanto as implementações da `ICommunicationListener` Kestrel como da HTTP.sys utilizam este mecanismo exatamente da mesma forma. Embora http.sys possa diferenciar internamente pedidos com base em caminhos de URL únicos utilizando *not* a funcionalidade de partilha de `ICommunicationListener` portas **HTTP.sys** subjacente, essa funcionalidade não é utilizada pela implementação http.sys. Isto porque resulta em códigos de estado de erro HTTP 503 e HTTP 404 no cenário descrito anteriormente. Isso, por sua vez, dificulta que os clientes determinem a intenção do erro, uma vez que http 503 e HTTP 404 são comumente usados para indicar outros erros. 

Assim, tanto as implementações de `ICommunicationListener` Kestrel como HTTP.sys normalizam o middleware fornecido pelo método de `UseServiceFabricIntegration` extensão. Por isso, os clientes apenas precisam de realizar uma redeterminação do ponto final de serviço em respostas HTTP 410.

## <a name="httpsys-in-reliable-services"></a>HTTP.sys em Serviços Fiáveis
Pode utilizar http.sys em Serviços Fiáveis importando o pacote **Microsoft.ServiceFabric.AspNetCore.HttpSys** NuGet. Este pacote `HttpSysCommunicationListener`contém, `ICommunicationListener`uma implementação de . `HttpSysCommunicationListener`permite-lhe criar um ASP.NET Core WebHost dentro de um serviço fiável, utilizando http.sys como servidor web.

HTTP.sys é construído no [Windows HTTP Server API](https://msdn.microsoft.com/library/windows/desktop/aa364510(v=vs.85).aspx). Esta API utiliza o controlador **kernel HTTP.sys** para processar pedidos http e encaminhá-los para processos que executam aplicações web. Isto permite que vários processos na mesma máquina física ou virtual acolhem aplicações web na mesma porta, desambiguadas por um caminho único de URL ou nome anfitrião. Estas funcionalidades são úteis no Service Fabric para hospedar vários websites no mesmo cluster.

>[!NOTE]
>A implementação http.sys funciona apenas na plataforma Windows.

O diagrama que se segue ilustra como http.sys utiliza o controlador kernel **HTTP.sys** no Windows para partilha de portas:

![Diagrama HTTP.sys][3]

### <a name="httpsys-in-a-stateless-service"></a>HTTP.sys em um serviço apátrida
Para `HttpSys` utilizar num serviço apátrida, sobrepor-se ao `CreateServiceInstanceListeners` método e devolver uma `HttpSysCommunicationListener` instância:

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

### <a name="httpsys-in-a-stateful-service"></a>HTTP.sys em um serviço imponente

`HttpSysCommunicationListener`Atualmente não é projetado para ser usado em serviços imponentes devido a complicações com a funcionalidade de partilha de portas **HTTP.sys** subjacente. Para mais informações, consulte a secção seguinte sobre alocação dinâmica de portas com HTTP.sys. Para serviços estatais, Kestrel é o servidor web sugerido.

### <a name="endpoint-configuration"></a>Configuração de ponto final

É `Endpoint` necessária uma configuração para servidores web que utilizem o API do Servidor Windows HTTP, incluindo http.sys. Os servidores web que utilizam o Windows HTTP Server API devem primeiro reservar o seu URL com HTTP.sys (isto é normalmente realizado com a ferramenta [netsh).](https://msdn.microsoft.com/library/windows/desktop/cc307236(v=vs.85).aspx) 

Esta ação requer privilégios elevados que os seus serviços não têm por defeito. As opções "http" ou `Protocol` "https" `Endpoint` para a propriedade da configuração em ServiceManifest.xml são utilizadas especificamente para instruir o tempo de execução do Tecido de Serviço para registar um URL com HTTP.sys em seu nome. Fá-lo utilizando o [*prefixo de URL wildcard forte.*](https://msdn.microsoft.com/library/windows/desktop/aa364698(v=vs.85).aspx)

Por exemplo, `http://+:80` para reservar para um serviço, utilize a seguinte configuração em ServiceManifest.xml:

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

E o nome final deve `HttpSysCommunicationListener` ser passado para o construtor:

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

#### <a name="use-httpsys-with-a-static-port"></a>Utilize http.sys com uma porta estática
Para utilizar uma porta estática com HTTP.sys, forneça o número da porta na `Endpoint` configuração:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

#### <a name="use-httpsys-with-a-dynamic-port"></a>Utilize http.sys com uma porta dinâmica
Para utilizar uma porta dinamicamente atribuída com HTTP.sys, omita a `Port` propriedade na `Endpoint` configuração:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
```

Uma porta dinâmica atribuída `Endpoint` por uma configuração fornece apenas uma porta *por processo de acolhimento.* O atual modelo de hospedagem do Tecido de Serviço permite que várias instâncias de serviço e/ou réplicas sejam hospedadas no mesmo processo. Isto significa que cada um partilhará a `Endpoint` mesma porta quando atribuído através da configuração. Vários **casos http.sys** podem partilhar uma porta utilizando a funcionalidade de partilha de portas **HTTP.sys** subjacente. Mas não é suportado `HttpSysCommunicationListener` devido às complicações que introduz para pedidos de clientes. Para uma utilização dinâmica da porta, Kestrel é o servidor web sugerido.

## <a name="kestrel-in-reliable-services"></a>Kestrel em Serviços Fiáveis
Pode utilizar o Kestrel em Serviços Fiáveis importando o pacote **Microsoft.ServiceFabric.AspNetCore.Kestrel** NuGet. Este pacote `KestrelCommunicationListener`contém, `ICommunicationListener`uma implementação de . `KestrelCommunicationListener`permite-lhe criar um ASP.NET Core WebHost dentro de um serviço fiável, utilizando o Kestrel como servidor web.

Kestrel é um servidor web de plataforma cruzada para ASP.NET Core. Ao contrário do HTTP.sys, o Kestrel não usa um gestor de pontofinal centralizado. Também ao contrário de HTTP.sys, Kestrel não suporta a partilha de portas entre vários processos. Cada instância de Kestrel deve usar uma porta única. Para mais informações sobre o Kestrel, consulte os Detalhes da [Implementação.](https://docs.microsoft.com/aspnet/core/fundamentals/servers/kestrel?view=aspnetcore-2.2)

![Diagrama de Kestrel][4]

### <a name="kestrel-in-a-stateless-service"></a>Kestrel em um serviço apátrida
Para `Kestrel` utilizar num serviço apátrida, sobrepor-se ao `CreateServiceInstanceListeners` método e devolver uma `KestrelCommunicationListener` instância:

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

### <a name="kestrel-in-a-stateful-service"></a>Kestrel em um serviço imponente
Para `Kestrel` utilizar num serviço imponente, `CreateServiceReplicaListeners` sobrepor-se `KestrelCommunicationListener` ao método e devolver uma instância:

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

Neste exemplo, um caso `IReliableStateManager` singleton é fornecido ao recipiente de injeção de dependência webHost. Isto não é estritamente necessário, mas `IReliableStateManager` permite-lhe usar e Coleções Fiáveis nos seus métodos de ação do controlador MVC.

Um `Endpoint` nome de configuração *não* é fornecido `KestrelCommunicationListener` num serviço imponente. Isto é explicado mais detalhadamente na secção seguinte.

### <a name="configure-kestrel-to-use-https"></a>Configurar o Kestrel para utilizar HTTPS
Ao ativar HTTPS com Kestrel no seu serviço, terá de definir várias opções de audição. Atualize `ServiceInstanceListener` o ponto final para utilizar um ponto final *EndpointHttps* e ouça uma porta específica (como a porta 443). Ao configurar o web host para utilizar o servidor web Kestrel, deve configurar o Kestrel para ouvir os endereços IPv6 em todas as interfaces de rede: 

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

Para um exemplo completo num tutorial, consulte [Configure Kestrel para utilizar HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#configure-kestrel-to-use-https).


### <a name="endpoint-configuration"></a>Configuração de ponto final
Uma `Endpoint` configuração não é necessária para usar kestrel. 

Kestrel é um simples servidor web autónomo. Ao contrário de HTTP.sys (ou HttpListener), não precisa de uma `Endpoint` configuração em ServiceManifest.xml porque não requer registo de URL antes de começar. 

#### <a name="use-kestrel-with-a-static-port"></a>Use Kestrel com uma porta estática
Pode configurar uma porta `Endpoint` estática na configuração do ServiceManifest.xml para utilização com kestrel. Embora isto não seja estritamente necessário, oferece dois potenciais benefícios:
 - Se a porta não cair na gama da porta de aplicação, é aberta através da firewall ossa por Service Fabric.
 - O URL que `KestrelCommunicationListener` lhe foi fornecido utilizará esta porta.

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

Se `Endpoint` um estiver configurado, o seu `KestrelCommunicationListener` nome deve ser passado para o construtor: 

```csharp
new KestrelCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) => ...
```

Se serviceManifest.xml não utilizar `Endpoint` uma configuração, omita o nome no `KestrelCommunicationListener` construtor. Neste caso, utilizará uma porta dinâmica. Consulte a secção seguinte para mais informações sobre este facto.

#### <a name="use-kestrel-with-a-dynamic-port"></a>Use Kestrel com um porto dinâmico
Kestrel não pode usar a atribuição `Endpoint` automática da porta a partir da configuração em ServiceManifest.xml. Isto porque a atribuição automática `Endpoint` de portas a partir de uma configuração atribui uma porta única por *processo de anfitrião,* e um único processo de anfitrião pode conter várias instâncias kestrel. Isto não funciona com o Kestrel porque não suporta a partilha de portos. Portanto, cada instância kestrel deve ser aberta em um porto único.

Para utilizar a atribuição dinâmica da porta `Endpoint` com kestrel, omita totalmente a configuração em `KestrelCommunicationListener` ServiceManifest.xml e não passe um nome final ao construtor, da seguinte forma:

```csharp
new KestrelCommunicationListener(serviceContext, (url, listener) => ...
```

Nesta configuração, `KestrelCommunicationListener` selecionará automaticamente uma porta não utilizada a partir da gama da porta de aplicação.

Para HTTPS, deve ter o Ponto Final configurado com protocolo HTTPS sem uma porta especificada em ServiceManifest.xml e passar o nome final para o construtor KestrelCommunicationListener.


## <a name="service-fabric-configuration-provider"></a>Prestador de configuração de tecido de serviço
A configuração da aplicação em ASP.NET Core baseia-se em pares de valor-chave estabelecidos pelo fornecedor de configuração. Leia [a Configuração no Núcleo ASP.NET](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/) para entender mais sobre o suporte de configuração do Núcleo de ASP.NET geral.

Esta secção descreve como o fornecedor de configuração do `Microsoft.ServiceFabric.AspNetCore.Configuration` Tecido de Serviço se integra com ASP.NET configuração Core importando o pacote NuGet.

### <a name="addservicefabricconfiguration-startup-extensions"></a>Extensões de arranque AddServiceFabricConfiguration
Depois de `Microsoft.ServiceFabric.AspNetCore.Configuration` importar o pacote NuGet, tem de registar a fonte de configuração do tecido de serviço com ASP.NET API de configuração Core. Faça-o verificando as extensões **AddServiceFabricConfiguration** no espaço de `Microsoft.ServiceFabric.AspNetCore.Configuration` nome contra `IConfigurationBuilder`.

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

Agora, o serviço ASP.NET Core pode aceder às definições de configuração do Tecido de Serviço, tal como quaisquer outras definições de aplicação. Por exemplo, pode utilizar o padrão de opções para carregar as definições em objetos fortemente digitados.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<MyOptions>(Configuration);  // Strongly typed configuration object.
    services.AddMvc();
}
```
### <a name="default-key-mapping"></a>Mapeamento de chave padrão
Por predefinição, o fornecedor de configuração do Tecido de Serviço inclui o nome do pacote, o nome da secção e o nome da propriedade. Juntos, estes formam a chave de configuração ASP.NET Core, da seguinte forma:
```csharp
$"{this.PackageName}{ConfigurationPath.KeyDelimiter}{section.Name}{ConfigurationPath.KeyDelimiter}{property.Name}"
```

Por exemplo, se tiver um `MyConfigPackage` pacote de configuração nomeado com o seguinte `IConfiguration` conteúdo, então o valor de configuração estará disponível no Núcleo ASP.NET através do *MyConfigPackage:MyConfigSection:MyParameter*.
```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">  
  <Section Name="MyConfigSection">
    <Parameter Name="MyParameter" Value="Value1" />
  </Section>  
</Settings>
```
### <a name="service-fabric-configuration-options"></a>Opções de configuração de tecido de serviço
O fornecedor de configuração `ServiceFabricConfigurationOptions` do Tecido de Serviço também suporta alterar o comportamento padrão do mapeamento da chave.

#### <a name="encrypted-settings"></a>Definições encriptadas
O Serviço Fabric suporta configurações encriptadas, assim como o fornecedor de configuração do Tecido de Serviço. As definições encriptadas não são desencriptadas para ASP.NET Core `IConfiguration` por padrão. Em vez disso, os valores encriptados são armazenados lá. Mas se quiser desencriptar o valor para armazenar em ASP.NET Core IConfiguration, `AddServiceFabricConfiguration` pode definir a bandeira *DecryptValue* como falsa na extensão, da seguinte forma:

```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    var builder = new ConfigurationBuilder()        
        .AddServiceFabricConfiguration(activationContext, (options) => options.DecryptValue = false); // set flag to decrypt the value
    Configuration = builder.Build();
}
```
#### <a name="multiple-configuration-packages"></a>Pacotes de configuração múltipla
O Tecido de Serviço suporta vários pacotes de configuração. Por predefinição, o nome do pacote está incluído na chave de configuração. Mas pode colocar `IncludePackageName` a bandeira em falso, da seguinte forma:
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
#### <a name="custom-key-mapping-value-extraction-and-data-population"></a>Mapeamento de chaves personalizadas, extração de valor e população de dados
O fornecedor de configuração de Tecido de Serviço também `ExtractKeyFunc` suporta cenários mais `ExtractValueFunc`avançados para personalizar o mapeamento chave com e extrair os valores com . Pode até alterar todo o processo de povoação de dados `ConfigAction`da configuração do Tecido de Serviço para ASP.NET configuração Core utilizando .

Os seguintes exemplos `ConfigAction` ilustram como usar para personalizar a população de dados:
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
O fornecedor de configuração do Tecido de Serviço também suporta atualizações de configuração. Pode utilizar ASP.NET `IOptionsMonitor` Core para receber notificações `IOptionsSnapshot` de alteração e, em seguida, utilizar para recarregar dados de configuração. Para mais informações, consulte [ASP.NET opções Core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options).

Estas opções são suportadas por defeito. Não é necessário mais codificação para permitir atualizações de configuração.

## <a name="scenarios-and-configurations"></a>Cenários e configurações
Esta secção fornece a combinação de servidor web, configuração de porta, opções de integração de tecido de serviço e configurações diversas recomendamos para resolver os seguintes cenários:
 - Serviços apátridas ASP.NET Núcleo de ASP.NET
 - Serviços apátridas ASP.NET Apátridas de ASP.NET Interno
 - Serviços estatais ASP.NET apenas internos

Um **serviço externo exposto** é aquele que expõe um ponto final que é chamado de fora do cluster, geralmente através de um equilibrista de carga.

Um serviço **interno** é aquele cujo ponto final só é chamado de dentro do cluster.

> [!NOTE]
> Os pontos finais de serviço audais geralmente não devem ser expostos à internet. Os clusters por trás de equilibradores de carga que desconheçam a resolução de serviços da Service Fabric, como o Azure Load Balancer, não poderão expor serviços estatais. Isso porque o equilibrista de carga não será capaz de localizar e direcionar o tráfego para a réplica de serviço apropriado. 

### <a name="externally-exposed-aspnet-core-stateless-services"></a>Serviços apátridas ASP.NET Núcleo de ASP.NET
Kestrel é o servidor web sugerido para serviços front-end que expõem pontos finais HTTP externos e virados para a Internet. No Windows, http.sys pode fornecer capacidade de partilha de portas, o que lhe permite hospedar vários serviços web no mesmo conjunto de nós usando a mesma porta. Neste cenário, os serviços web são diferenciados pelo nome ou caminho do anfitrião, sem depender de um proxy frontal ou porta de entrada para fornecer o encaminhamento HTTP.
 
Quando exposto à internet, um serviço apátrida deve usar um ponto final bem conhecido e estável que seja acessível através de um equilibrista de carga. Irá fornecer este URL aos utilizadores da sua aplicação. Recomendamos a seguinte configuração:

|  |  | **Notas** |
| --- | --- | --- |
| Servidor Web | Rio Kestrel | Kestrel é o servidor web preferido, uma vez que é suportado através do Windows e Linux. |
| Configuração da porta | static | Uma porta estática bem conhecida deve `Endpoints` ser configurada na configuração do ServiceManifest.xml, tal como 80 para HTTP ou 443 para HTTPS. |
| Opções de Integração de Tecidos de Serviço | Nenhuma | Utilize `ServiceFabricIntegrationOptions.None` a opção ao configurar o middleware de integração do Tecido de Serviço, para que o serviço não tente validar os pedidos de entrada de um identificador único. Os utilizadores externos da sua aplicação não saberão a informação de identificação única que o middleware utiliza. |
| Contagem de Instâncias | -1 | Em casos de utilização típica, a definição de contagem de casos deve ser definida para *-1*. Isto é feito de modo a que uma instância esteja disponível em todos os nós que recebem tráfego de um equilibrista de carga. |

Se vários serviços expostos externamente partilharem o mesmo conjunto de nós, pode utilizar http.sys com um caminho URL único mas estável. Pode fazê-lo modificando o URL fornecido ao configurar o IWebHost. Note que isto se aplica apenas ao HTTP.sys.

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

### <a name="internal-only-stateless-aspnet-core-service"></a>Serviço central de ASP.NET apátrida interna
Os serviços apátridas que só são chamados de dentro do cluster devem utilizar URLs únicos e portos dinamicamente atribuídos para garantir a cooperação entre vários serviços. Recomendamos a seguinte configuração:

|  |  | **Notas** |
| --- | --- | --- |
| Servidor Web | Rio Kestrel | Embora possa utilizar http.sys para serviços internos apátridas, o Kestrel é o melhor servidor para permitir que várias instâncias de serviço partilhem um anfitrião.  |
| Configuração da porta | dinamicamente atribuído | Várias réplicas de um serviço imponente podem partilhar um processo de hospedagem ou sistema operativo anfitrião e, portanto, precisarão de portas únicas. |
| Opções de Integração de Tecidos de Serviço | UseUniqueServiceUrl | Com a atribuição dinâmica do porto, esta definição impede a questão de identidade errada descrita anteriormente. |
| Contagem de casos | qualquer | A definição de contagem de instâncias pode ser definida para qualquer valor necessário para operar o serviço. |

### <a name="internal-only-stateful-aspnet-core-service"></a>Serviço central de ASP.NET apenas interno
Os serviços estatais que só são chamados de dentro do cluster devem utilizar portos dinamicamente atribuídos para garantir a cooperação entre vários serviços. Recomendamos a seguinte configuração:

|  |  | **Notas** |
| --- | --- | --- |
| Servidor Web | Rio Kestrel | O `HttpSysCommunicationListener` não foi concebido para ser usado por serviços imponentes em que as réplicas partilham um processo de acolhimento. |
| Configuração da porta | dinamicamente atribuído | Várias réplicas de um serviço imponente podem partilhar um processo de hospedagem ou sistema operativo anfitrião e, portanto, precisarão de portas únicas. |
| Opções de Integração de Tecidos de Serviço | UseUniqueServiceUrl | Com a atribuição dinâmica do porto, esta definição impede a questão de identidade errada descrita anteriormente. |

## <a name="next-steps"></a>Passos seguintes
[Depurar a sua aplicação do Service Fabric com o Visual Studio](service-fabric-debugging-your-application.md)


<!--Image references-->
[0]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-standalone.png
[1]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-servicefabric.png
[2]:./media/service-fabric-reliable-services-communication-aspnetcore/integration.png
[3]:./media/service-fabric-reliable-services-communication-aspnetcore/httpsys.png
[4]:./media/service-fabric-reliable-services-communication-aspnetcore/kestrel.png
