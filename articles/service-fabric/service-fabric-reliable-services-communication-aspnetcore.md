---
title: Comunicação de serviço com o núcleo ASP.NET
description: Saiba como usar ASP.NET Core em aplicações apátridas e stateful Azure Service Fabric Reliable Services.
ms.topic: conceptual
ms.date: 10/12/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: a125c6a1972b51f518175a4c69248119f71ada7c
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98791599"
---
# <a name="aspnet-core-in-azure-service-fabric-reliable-services"></a>ASP.NET Core em Serviço Azure Fabric Reliable Services

ASP.NET Core é um quadro de código aberto e de plataforma cruzada. Esta estrutura é projetada para a construção de aplicações baseadas na nuvem, conectadas à Internet, tais como aplicações web, aplicações IoT e extremidades traseiras móveis.

Este artigo é um guia aprofundado para hospedar ASP.NET serviços Core em Serviços Fiáveis de Tecido de Serviço, utilizando o **Microsoft.ServiceFabric.AspNetCore.** conjunto de pacotes NuGet.

Para um tutorial introdutório sobre ASP.NET Core in Service Fabric e instruções para configurar o seu ambiente de desenvolvimento, consulte [Tutorial: Crie e implemente uma aplicação com um serviço frontal ASP.NET Core Web E um serviço de back-end stateful](service-fabric-tutorial-create-dotnet-app.md).

O resto deste artigo assume que já está familiarizado com ASP.NET Core. Caso contrário, por favor leia os fundamentos do [núcleo ASP.NET.](/aspnet/core/fundamentals/index)

## <a name="aspnet-core-in-the-service-fabric-environment"></a>ASP.NET Core no ambiente do tecido de serviço

Tanto ASP.NET aplicações Core e Service Fabric podem funcionar em .NET Core ou full .NET Framework. Você pode usar ASP.NET Core de duas maneiras diferentes em Tecido de Serviço:
 - **Hospedado como um hóspede executável**. Desta forma é usado principalmente para executar aplicações core ASP.NET existentes no Tecido de Serviço sem alterações de código.
 - **Corra dentro de um serviço de confiança.** Desta forma permite uma melhor integração com o tempo de funcionamento do Service Fabric e permite serviços ASP.NET Core.

O resto deste artigo explica como usar ASP.NET Core dentro de um serviço confiável, através dos componentes de integração ASP.NET Core que enviam com o Service Fabric SDK.

## <a name="service-fabric-service-hosting"></a>Hospedagem de serviço de tecido de serviço

No Tecido de Serviço, uma ou mais instâncias e/ou réplicas do seu serviço funcionam num processo de anfitrião de *serviço:* um ficheiro executável que executa o seu código de serviço. Você, como autor de serviço, é dono do processo de anfitrião de serviço, e o Service Fabric ativa-o e monitoriza-o por si.

O ASP.NET tradicional (até MVC 5) está bem acoplado ao IIS através de System.Web.dll. ASP.NET Core fornece uma separação entre o servidor web e a sua aplicação web. Esta separação permite que as aplicações web sejam portáteis entre diferentes servidores web. Também permite que os servidores web sejam *auto-hospedados.* Isto significa que pode iniciar um servidor web no seu próprio processo, em oposição a um processo que é propriedade de software de servidor web dedicado, como o IIS.

Para combinar um serviço de Tecido de Serviço e ASP.NET, seja como um hóspede executável ou num serviço confiável, você deve ser capaz de começar ASP.NET dentro do seu processo de anfitrião de serviço. ASP.NET Core auto-hospedagem permite-lhe fazer isso.

## <a name="hosting-aspnet-core-in-a-reliable-service"></a>Hospedar ASP.NET Core num serviço confiável
Normalmente, aplicações core ASP.NET auto-acolô criam um WebHost no ponto de entrada de uma aplicação, como o `static void Main()` método em `Program.cs` . Neste caso, o ciclo de vida do WebHost está ligado ao ciclo de vida do processo.

![Hospedar ASP.NET Core em um processo][0]

Mas o ponto de entrada da aplicação não é o local certo para criar um WebHost num serviço confiável. Isto porque o ponto de entrada da aplicação é utilizado apenas para registar um tipo de serviço com o tempo de funcionamento do Tecido de Serviço, para que possa criar instâncias desse tipo de serviço. O WebHost deve ser criado num serviço fiável em si. Dentro do processo de anfitrião de serviço, as instâncias de serviço e/ou réplicas podem passar por vários ciclos de vida. 

Uma instância de Serviço Fiável é representada pela sua classe de serviço derivada de `StatelessService` ou `StatefulService` . A pilha de comunicação para um serviço está contida numa `ICommunicationListener` implementação na sua classe de serviço. Os `Microsoft.ServiceFabric.AspNetCore.*` pacotes NuGet contêm implementações `ICommunicationListener` desse início e gerem o Core WebHost ASP.NET para a Kestrel ou HTTP.sys num serviço fiável.

![Diagrama para hospedar ASP.NET Core em um serviço confiável][1]

## <a name="aspnet-core-icommunicationlisteners"></a>ASP.NET Núcleo ICommunicationIseners
As `ICommunicationListener` implementações para Kestrel e HTTP.sys nos  `Microsoft.ServiceFabric.AspNetCore.*` pacotes NuGet têm padrões de utilização semelhantes. Mas realizam ações ligeiramente diferentes específicas de cada servidor web. 

Ambos os ouvintes de comunicação fornecem um construtor que toma os seguintes argumentos:
 - **`ServiceContext serviceContext`**: Este é o `ServiceContext` objeto que contém informações sobre o serviço de funcionamento.
 - **`string endpointName`**: Este é o nome de uma `Endpoint` configuração em ServiceManifest.xml. É principalmente onde os dois ouvintes de comunicação diferem. HTTP.sys *requer* uma `Endpoint` configuração, enquanto Kestrel não.
 - **`Func<string, AspNetCoreCommunicationListener, IWebHost> build`**: Esta é uma lambda que implementa, na qual cria e devolve um `IWebHost` . Permite-lhe configurar como `IWebHost` normalmente faria numa aplicação core ASP.NET. O lambda fornece um URL que é gerado para si, dependendo das opções de integração do Tecido de Serviço que utiliza e da `Endpoint` configuração que fornece. Em seguida, pode modificar ou utilizar esse URL para iniciar o servidor web.

## <a name="service-fabric-integration-middleware"></a>Middleware de integração de tecido de serviço
O `Microsoft.ServiceFabric.AspNetCore` pacote NuGet inclui o `UseServiceFabricIntegration` método de extensão que adiciona `IWebHostBuilder` middleware consciente do Tecido de Serviço. Este middleware configura o Kestrel ou HTTP.sys `ICommunicationListener` para registar um URL de serviço único com o Serviço de Nomeação de Tecido de Serviço. Em seguida, valida os pedidos do cliente para garantir que os clientes estão ligados ao serviço certo. 

Este passo é necessário para evitar que os clientes se conectem erradamente ao serviço errado. Isto porque, num ambiente partilhado como o Service Fabric, várias aplicações web podem funcionar na mesma máquina física ou virtual, mas não usam nomes de anfitriões únicos. Este cenário é descrito com mais detalhes na secção seguinte.

### <a name="a-case-of-mistaken-identity"></a>Um caso de identidade trocada
Réplicas de serviço, independentemente do protocolo, ouçam uma combinação IP:port única. Uma vez que uma réplica de serviço tenha começado a ouvir num ponto final IP:port, informa o endereço final para o Serviço de Nomeação de Tecido de Serviço. Lá, os clientes ou outros serviços podem descobri-lo. Se os serviços utilizarem portas de aplicação atribuídas dinamicamente, uma réplica de serviço pode, por coincidência, utilizar o mesmo ponto final IP:port de outro serviço anteriormente na mesma máquina física ou virtual. Isto pode fazer com que um cliente se ligue erradamente ao serviço errado. Este cenário pode resultar se ocorrer a seguinte sequência de eventos:

 1. Serviço A escuta em 10.0.0.1:30000 em HTTP. 
 2. O cliente resolve o Serviço A e obtém o endereço 10.0.0.1:30000.
 3. O serviço A move-se para um nó diferente.
 4. O serviço B é colocado em 10.0.0.1 e, por coincidência, utiliza a mesma porta 30000.
 5. O cliente tenta ligar-se ao serviço A com o endereço em cache 10.0.0.1:30000.
 6. O cliente está agora ligado com sucesso ao serviço B, não percebendo que está ligado ao serviço errado.

Isto pode causar insetos em momentos aleatórios que podem ser difíceis de diagnosticar.

### <a name="using-unique-service-urls"></a>Usando URLs de serviço exclusivos
Para prevenir estes bugs, os serviços podem colocar um ponto final no Serviço de Nomeação com um identificador único e, em seguida, validar esse identificador único durante os pedidos do cliente. Esta é uma ação cooperativa entre serviços num ambiente de confiança não hostil. Não fornece autenticação de serviço seguro num ambiente hostil de inquilinos.

Num ambiente de confiança, o middleware que é adicionado pelo `UseServiceFabricIntegration` método anexa automaticamente um identificador único ao endereço publicado no Serviço de Nomeação. Valida o identificador em cada pedido. Se o identificador não corresponder, o middleware devolve imediatamente uma resposta HTTP 410 Gone.

Os serviços que utilizam uma porta atribuída dinamicamente devem utilizar este middleware.

Os serviços que utilizam uma porta fixa e única não têm este problema num ambiente cooperativo. Uma porta fixa e única é normalmente utilizada para serviços de frente externa que precisam de uma porta bem conhecida para as aplicações do cliente se conectarem. Por exemplo, a maioria das aplicações web viradas para a Internet usarão a porta 80 ou 443 para ligações ao navegador web. Neste caso, o identificador único não deve ser ativado.

O diagrama que se segue mostra o fluxo de pedido com o middleware ativado:

![Tecido de Serviço ASP.NET Integração core][2]

Tanto a Kestrel como a HTTP.sys `ICommunicationListener` implementações utilizam este mecanismo exatamente da mesma forma. Embora HTTP.sys possa diferenciar internamente os pedidos com base em caminhos DE URL únicos utilizando a funcionalidade de partilha **de portaHTTP.sys** subjacente, essa funcionalidade *não* é utilizada pela `ICommunicationListener` implementação HTTP.sys. Isto porque resulta em códigos de estado de erro HTTP 503 e HTTP 404 no cenário descrito anteriormente. Isto por sua vez dificulta que os clientes determinem a intenção do erro, uma vez que HTTP 503 e HTTP 404 são normalmente utilizados para indicar outros erros. 

Assim, tanto as implementações da Kestrel como HTTP.sys `ICommunicationListener` normalizam o middleware fornecido pelo método de `UseServiceFabricIntegration` extensão. Por isso, os clientes apenas precisam de realizar um ponto final de serviço para re-resolver a ação em respostas HTTP 410.

## <a name="httpsys-in-reliable-services"></a>HTTP.sys em Serviços Fiáveis
Pode utilizar HTTP.sys em Serviços Fiáveis importando o pacote **Microsoft.ServiceFabric.AspNetCore.HttpSys** NuGet. Este pacote `HttpSysCommunicationListener` contém, uma implementação de `ICommunicationListener` . `HttpSysCommunicationListener` permite-lhe criar um Core WebHost ASP.NET dentro de um serviço confiável, utilizando HTTP.sys como servidor web.

HTTP.sys é construído na [API](/windows/win32/http/http-api-start-page)do Servidor HTTP do Windows HTTP . Esta API utiliza o **HTTP.sys** controlador de núcleo para processar pedidos HTTP e encaminhá-los para processos que executam aplicações web. Isto permite que vários processos na mesma máquina física ou virtual aloquem aplicações web na mesma porta, desambiguadas por um caminho URL único ou nome de hospedeiro. Estas funcionalidades são úteis no Service Fabric para hospedar vários websites no mesmo cluster.

>[!NOTE]
>HTTP.sys implementação funciona apenas na plataforma Windows.

O seguinte diagrama ilustra como HTTP.sys utiliza **o** HTTP.syscondutor de núcleo no Windows para partilha de porta:

![diagrama de HTTP.sys][3]

### <a name="httpsys-in-a-stateless-service"></a>HTTP.sys num serviço apátrida
Para utilizar `HttpSys` num serviço apátrida, sobreponha o `CreateServiceInstanceListeners` método e devolva uma `HttpSysCommunicationListener` instância:

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

### <a name="httpsys-in-a-stateful-service"></a>HTTP.sys num serviço imponente

`HttpSysCommunicationListener` não é projetado atualmente para uso em serviços estatais devido a complicações com a funcionalidade de partilha **deHTTP.sys** portuária subjacente. Para mais informações, consulte a secção seguinte sobre a atribuição dinâmica do porto com HTTP.sys. Para serviços estatais, Kestrel é o servidor web sugerido.

### <a name="endpoint-configuration"></a>Configuração do ponto final

`Endpoint`É necessária uma configuração para servidores web que utilizem a API do Servidor HTTP do Windows, incluindo HTTP.sys. Os servidores web que utilizam a API do Servidor HTTP do Windows devem primeiro reservar o seu URL com HTTP.sys (isto é normalmente realizado com a ferramenta [netsh).](/windows/win32/http/netsh-commands-for-http) 

Esta ação requer privilégios elevados que os seus serviços não têm por defeito. As opções "http" ou "https" para a `Protocol` propriedade da `Endpoint` configuração em ServiceManifest.xml são usadas especificamente para instruir o tempo de execução do Tecido de Serviço para registar um URL com HTTP.sys em seu nome. Fá-lo utilizando o prefixo URL [*wildcard forte.*](/windows/win32/http/urlprefix-strings)

Por exemplo, para reservar `http://+:80` para um serviço, utilize a seguinte configuração em ServiceManifest.xml:

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

E o nome do ponto final deve ser passado para o `HttpSysCommunicationListener` construtor:

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

#### <a name="use-httpsys-with-a-static-port"></a>Utilize HTTP.sys com uma porta estática
Para utilizar uma porta estática com HTTP.sys, forneça o número da porta na `Endpoint` configuração:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

#### <a name="use-httpsys-with-a-dynamic-port"></a>Utilize HTTP.sys com uma porta dinâmica
Para utilizar uma porta atribuída dinamicamente com HTTP.sys, omita a `Port` propriedade na `Endpoint` configuração:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
```

Uma porta dinâmica atribuída por uma `Endpoint` configuração fornece apenas uma porta *por processo de hospedeiro*. O modelo de hospedagem de tecido de serviço atual permite que várias instâncias de serviço e/ou réplicas sejam hospedadas no mesmo processo. Isto significa que cada um partilhará a mesma porta quando atribuída através da `Endpoint` configuração. **VáriasHTTP.sys** casos podem partilhar uma porta utilizando a funcionalidade de partilha **de portaHTTP.sys** subjacente. Mas não é suportado `HttpSysCommunicationListener` devido às complicações que introduz para os pedidos dos clientes. Para uma utilização dinâmica da porta, o Kestrel é o servidor web sugerido.

## <a name="kestrel-in-reliable-services"></a>Kestrel em Serviços Fiáveis
Pode utilizar o Kestrel em Serviços Fiáveis importando o pacote **Microsoft.ServiceFabric.AspNetCore.Kestrel** NuGet. Este pacote `KestrelCommunicationListener` contém, uma implementação de `ICommunicationListener` . `KestrelCommunicationListener` permite-lhe criar um Core WebHost ASP.NET dentro de um serviço confiável, utilizando o Kestrel como servidor web.

Kestrel é um servidor web de plataforma cruzada para ASP.NET Core. Ao contrário HTTP.sys, o Kestrel não usa um gestor centralizado de pontos finais. Também ao contrário HTTP.sys, Kestrel não suporta a partilha de portas entre vários processos. Cada instância de Kestrel deve usar uma porta única. Para mais informações sobre kestrel, consulte os Detalhes de [Implementação.](/aspnet/core/fundamentals/servers/kestrel)

![Diagrama de Kestrel][4]

### <a name="kestrel-in-a-stateless-service"></a>Kestrel em um serviço apátrida
Para utilizar `Kestrel` num serviço apátrida, sobreponha o `CreateServiceInstanceListeners` método e devolva uma `KestrelCommunicationListener` instância:

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

### <a name="kestrel-in-a-stateful-service"></a>Kestrel em um serviço estatal
Para utilizar `Kestrel` num serviço imponente, anular o `CreateServiceReplicaListeners` método e devolver uma `KestrelCommunicationListener` instância:

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

Neste exemplo, é fornecida uma instância singleton de ser fornecida ao recipiente de `IReliableStateManager` injeção de dependência WebHost. Isto não é estritamente necessário, mas permite-lhe usar `IReliableStateManager` e Coleções Fiáveis nos seus métodos de ação do controlador MVC.

Um `Endpoint` nome de configuração *não* é fornecido num `KestrelCommunicationListener` serviço imponente. Isto é explicado mais detalhadamente na secção seguinte.

### <a name="configure-kestrel-to-use-https"></a>Configurar o Kestrel para utilizar HTTPS
Ao ativar HTTPS com Kestrel no seu serviço, terá de definir várias opções de audição. Atualize `ServiceInstanceListener` a utilização de um ponto final *EndpointHttps* e ouça uma porta específica (como a porta 443). Ao configurar o web host para utilizar o servidor web Kestrel, tem de configurar o Kestrel para ouvir endereços IPv6 em todas as interfaces de rede: 

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

Para obter um exemplo completo num tutorial, consulte [Configure Kestrel para utilizar HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#configure-kestrel-to-use-https).


### <a name="endpoint-configuration"></a>Configuração do ponto final
Uma `Endpoint` configuração não é necessária para usar Kestrel. 

Kestrel é um simples servidor web autónomo. Ao contrário HTTP.sys (ou HttpListener), não precisa de uma `Endpoint` configuração em ServiceManifest.xml porque não requer registo de URL antes de começar. 

#### <a name="use-kestrel-with-a-static-port"></a>Use Kestrel com uma porta estática
Pode configurar uma porta estática na `Endpoint` configuração de ServiceManifest.xml para utilização com Kestrel. Embora isto não seja estritamente necessário, oferece dois benefícios potenciais:
 - Se a porta não cair na gama de portas de aplicação, é aberta através da firewall oss por Service Fabric.
 - A URL fornecida através `KestrelCommunicationListener` da utilização desta porta.

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

Se um `Endpoint` estiver configurado, o seu nome deve ser passado para o `KestrelCommunicationListener` construtor: 

```csharp
new KestrelCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) => ...
```

Se ServiceManifest.xml não usar uma `Endpoint` configuração, omita o nome no `KestrelCommunicationListener` construtor. Neste caso, utilizará uma porta dinâmica. Consulte a secção seguinte para mais informações sobre isto.

#### <a name="use-kestrel-with-a-dynamic-port"></a>Use Kestrel com uma porta dinâmica
A Kestrel não pode utilizar a atribuição automática da porta a partir da `Endpoint` configuração em ServiceManifest.xml. Isto porque a atribuição automática da porta a partir de uma `Endpoint` configuração atribui uma porta única por *processo de hospedeiro*, e um único processo de hospedeiro pode conter várias instâncias Kestrel. Isto não funciona com o Kestrel porque não suporta a partilha do porto. Portanto, cada instância Kestrel deve ser aberta num porto único.

Para utilizar uma atribuição de porta dinâmica com a Kestrel, omita `Endpoint` a configuração em ServiceManifest.xml completamente, e não passe um nome de ponto final para o `KestrelCommunicationListener` construtor, da seguinte forma:

```csharp
new KestrelCommunicationListener(serviceContext, (url, listener) => ...
```

Nesta configuração, `KestrelCommunicationListener` selecionará automaticamente uma porta não-usada da gama de portas de aplicação.

Para HTTPS, deve ter o Ponto final configurado com o protocolo HTTPS sem uma porta especificada em ServiceManifest.xml e passar o nome de ponto final para o construtor KestrelCommunicationListener.


## <a name="service-fabric-configuration-provider"></a>Fornecedor de configuração de tecido de serviço
A configuração da aplicação em ASP.NET Core baseia-se em pares de valor-chave estabelecidos pelo fornecedor de configuração. Leia [a configuração no núcleo ASP.NET](/aspnet/core/fundamentals/configuration/) para saber mais sobre o suporte de configuração geral ASP.NET Core.

Esta secção descreve como o fornecedor de configuração de Tecido de Serviço se integra com ASP.NET configuração Core importando o `Microsoft.ServiceFabric.AspNetCore.Configuration` pacote NuGet.

### <a name="addservicefabricconfiguration-startup-extensions"></a>Extensões de arranque AddServiceFabricConfiguration
Depois de importar o `Microsoft.ServiceFabric.AspNetCore.Configuration` pacote NuGet, tem de registar a fonte de Configuração do Tecido de Serviço com ASP.NET API de configuração core. Fá-lo verificando as extensões **de Configuração AddServiceFabric** no espaço de `Microsoft.ServiceFabric.AspNetCore.Configuration` nome contra `IConfigurationBuilder` .

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

Agora o serviço Core ASP.NET pode aceder às definições de configuração do Tecido de Serviço, tal como qualquer outra definição de aplicação. Por exemplo, pode utilizar o padrão de opções para carregar as definições em objetos fortemente digitados.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<MyOptions>(Configuration);  // Strongly typed configuration object.
    services.AddMvc();
}
```
### <a name="default-key-mapping"></a>Mapeamento de chave padrão
Por predefinição, o fornecedor de configuração do Tecido de Serviço inclui o nome do pacote, nome da secção e nome da propriedade. Juntos, estes formam a chave de configuração do núcleo ASP.NET, da seguinte forma:
```csharp
$"{this.PackageName}{ConfigurationPath.KeyDelimiter}{section.Name}{ConfigurationPath.KeyDelimiter}{property.Name}"
```

Por exemplo, se tiver um pacote de configuração nomeado `MyConfigPackage` com o seguinte conteúdo, então o valor de configuração estará disponível no ASP.NET Core `IConfiguration` através do *MyConfigPackage:MyConfigSection:MyParameter*.
```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">  
  <Section Name="MyConfigSection">
    <Parameter Name="MyParameter" Value="Value1" />
  </Section>  
</Settings>
```
### <a name="service-fabric-configuration-options"></a>Opções de configuração do tecido de serviço
O fornecedor de configuração de Tecido de Serviço também suporta `ServiceFabricConfigurationOptions` alterar o comportamento padrão do mapeamento de chaves.

#### <a name="encrypted-settings"></a>Definições encriptadas
O Service Fabric suporta definições encriptadas, assim como o fornecedor de configuração de Tecido de Serviço. As definições encriptadas não são desencriptadas para ASP.NET Core `IConfiguration` por padrão. Os valores encriptados são armazenados lá em vez disso. Mas se quiser desencriptar o valor a armazenar em ASP.NET Core IConfiguration, pode definir a bandeira *de DecriptValue* em falso na `AddServiceFabricConfiguration` extensão, da seguinte forma:

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
O Service Fabric suporta pacotes de configuração múltiplas. Por predefinição, o nome do pacote está incluído na chave de configuração. Mas pode colocar a `IncludePackageName` bandeira em falso, da seguinte forma:
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
O fornecedor de configuração de Tecido de Serviço também suporta cenários mais avançados para personalizar o mapeamento da chave com `ExtractKeyFunc` e extrair os valores com `ExtractValueFunc` . Pode até alterar todo o processo de povoamento de dados da configuração do Tecido de Serviço para ASP.NET configuração core utilizando `ConfigAction` .

Os seguintes exemplos ilustram como usar `ConfigAction` para personalizar a população de dados:
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
O fornecedor de configuração do Tecido de Serviço também suporta atualizações de configuração. Pode utilizar ASP.NET Core `IOptionsMonitor` para receber notificações de alteração e, em seguida, utilizar `IOptionsSnapshot` para recarregar dados de configuração. Para obter mais informações, consulte [ASP.NET opções Core](/aspnet/core/fundamentals/configuration/options).

Estas opções são suportadas por padrão. Não é necessário codificar mais para permitir atualizações de configuração.

## <a name="scenarios-and-configurations"></a>Cenários e configurações
Esta secção fornece a combinação de servidor web, configuração de porta, opções de integração de tecido de serviço e configurações diversas que recomendamos para resolver os seguintes cenários:
 - Serviços ASP.NET apátridas core expostos externamente
 - Serviços apátridas do núcleo ASP.NET internos
 - Serviços estatais do Núcleo de ASP.NET internos

Um **serviço exposto externamente** é aquele que expõe um ponto final chamado de fora do cluster, geralmente através de um equilibrador de carga.

Um serviço **interno** é aquele cujo ponto final é chamado apenas de dentro do cluster.

> [!NOTE]
> Os pontos finais de serviços imponentes geralmente não devem ser expostos à internet. Os clusters por trás de equilibradores de carga que desconhecem a resolução do serviço service Fabric, como o Azure Load Balancer, não poderão expor serviços estatais. Isso porque o equilibrador de carga não será capaz de localizar e encaminhar o tráfego para a réplica de serviço estatal apropriada. 

### <a name="externally-exposed-aspnet-core-stateless-services"></a>Serviços ASP.NET apátridas core expostos externamente
Kestrel é o servidor web sugerido para serviços frontais que expõem pontos finais HTTP externos e virados para a Internet. No Windows, HTTP.sys pode fornecer capacidade de partilha de portas, o que permite hospedar vários serviços web no mesmo conjunto de nós usando a mesma porta. Neste cenário, os serviços web são diferenciados pelo nome ou caminho do anfitrião, sem depender de um proxy frontal ou gateway para fornecer encaminhamento HTTP.
 
Quando exposto à internet, um serviço apátrida deve usar um ponto final bem conhecido e estável que seja acessível através de um equilibrador de carga. Irá fornecer este URL aos utilizadores da sua aplicação. Recomendamos a seguinte configuração:

| Tipo | Recomendação | Notas |
| ---- | -------------- | ----- |
| Servidor Web | Rio Kestrel | Kestrel é o servidor web preferido, já que é suportado através do Windows e Linux. |
| Configuração da porta | static | Uma porta estática bem conhecida deve ser configurada na `Endpoints` configuração de ServiceManifest.xml, tais como 80 para HTTP ou 443 para HTTPS. |
| Opções de Integração de ServiçosFabric | Nenhum | Utilize a `ServiceFabricIntegrationOptions.None` opção ao configurar o middleware de integração do Tecido de Serviço, para que o serviço não tente validar pedidos de entrada para um identificador único. Os utilizadores externos da sua aplicação não saberão a informação de identificação única que o middleware utiliza. |
| Contagem de Instâncias | -1 | Nos casos de utilização típica, a definição da contagem de casos deve ser definida *como -1*. Isto é feito para que esteja disponível um caso em todos os nós que recebem tráfego de um equilibrador de carga. |

Se vários serviços expostos externamente partilharem o mesmo conjunto de nós, pode utilizar HTTP.sys com um caminho URL único mas estável. Pode fazê-lo modificando o URL fornecido ao configurar o IWebHost. Note que isto se aplica apenas a HTTP.sys.

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
Os serviços apátridas que só são chamados de dentro do cluster devem utilizar URLs únicos e portos dinamicamente atribuídos para garantir a cooperação entre múltiplos serviços. Recomendamos a seguinte configuração:

| Tipo | Recomendação | Notas |
| ---- | -------------- | ----- |
| Servidor Web | Rio Kestrel | Embora possa utilizar HTTP.sys para serviços apátridas internos, a Kestrel é o melhor servidor para permitir que várias instâncias de serviço partilhem um hospedeiro.  |
| Configuração da porta | atribuído dinamicamente | Várias réplicas de um serviço imponente podem partilhar um processo de anfitrião ou um sistema operativo anfitrião e, portanto, precisarão de portas únicas. |
| Opções de Integração de ServiçosFabric | UseUniqueServiceUrl | Com uma atribuição dinâmica da porta, esta definição impede o problema de identidade errado descrito anteriormente. |
| InstânciaCount | qualquer | A definição de contagem de casos pode ser definida em qualquer valor necessário para operar o serviço. |

### <a name="internal-only-stateful-aspnet-core-service"></a>Serviço central de ASP.NET só interno
Os serviços estatais que são chamados apenas de dentro do cluster devem utilizar portos dinamicamente atribuídos para garantir a cooperação entre múltiplos serviços. Recomendamos a seguinte configuração:

| Tipo | Recomendação | Notas |
| ---- | -------------- | ----- |
| Servidor Web | Rio Kestrel | O `HttpSysCommunicationListener` não é projetado para ser usado por serviços estatais em que réplicas compartilham um processo de anfitrião. |
| Configuração da porta | atribuído dinamicamente | Várias réplicas de um serviço imponente podem partilhar um processo de anfitrião ou um sistema operativo anfitrião e, portanto, precisarão de portas únicas. |
| Opções de Integração de ServiçosFabric | UseUniqueServiceUrl | Com uma atribuição dinâmica da porta, esta definição impede o problema de identidade errado descrito anteriormente. |

## <a name="next-steps"></a>Próximos passos
[Depurar a sua aplicação do Service Fabric com o Visual Studio](service-fabric-debugging-your-application.md)


<!--Image references-->
[0]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-standalone.png
[1]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-servicefabric.png
[2]:./media/service-fabric-reliable-services-communication-aspnetcore/integration.png
[3]:./media/service-fabric-reliable-services-communication-aspnetcore/httpsys.png
[4]:./media/service-fabric-reliable-services-communication-aspnetcore/kestrel.png
