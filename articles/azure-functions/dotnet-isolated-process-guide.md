---
title: .NET guia de processo isolado para .NET 5.0 em Funções Azure
description: Aprenda a utilizar um processo isolado .NET para executar as suas funções C# em .NET 5.0 fora de processo em Azure.
ms.service: azure-functions
ms.topic: conceptual
ms.date: 03/01/2021
ms.custom: template-concept
ms.openlocfilehash: b13c19aea3c3d36bd1cb4237278e5f0edbb8ed54
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102181725"
---
# <a name="guide-for-running-functions-on-net-50-in-azure"></a>Guia para executar funções em .NET 5.0 em Azure

_O suporte .NET 5.0 está atualmente em pré-visualização._

Este artigo é uma introdução à utilização de C# para desenvolver funções de processo isolado .NET, que estão a ficar sem processo em Funções Azure. O processo esgotado permite-lhe dissociar o seu código de função do tempo de funcionamento das Funções Azure. Também fornece uma forma de criar e executar funções que visam a versão atual .NET 5.0. 

Se não precisar de suportar .NET 5.0 ou executar as suas funções fora do processo, poderá, em vez disso, desenvolver funções de [biblioteca de classe C#](functions-dotnet-class-library.md).

## <a name="why-net-isolated-process"></a>Por que.PROCESSO isolado DA NET?

Anteriormente, a Azure Functions apenas apoiou um modo fortemente integrado para as funções .NET, que funcionam [como uma biblioteca de classes](functions-dotnet-class-library.md) no mesmo processo que o anfitrião. Este modo proporciona uma integração profunda entre o processo de anfitrião e as funções. Por exemplo, as funções de biblioteca de classe .NET podem partilhar APIs e tipos de ligação. No entanto, esta integração também requer um acoplamento mais apertado entre o processo de anfitrião e a função .NET. Por exemplo, as funções .NET em execução em processo são necessárias para funcionar na mesma versão de .NET que o tempo de execução das Funções. Para que possa correr fora destes constrangimentos, pode agora optar por correr num processo isolado. Este isolamento de processo também permite desenvolver funções que utilizem as atuais versões .NET (como .NET 5.0), não suportadas de forma nativa pelo tempo de funcionamento das Funções.

Como estas funções são executadas num processo separado, [existem algumas diferenças de funcionalidades e funcionalidades](#differences-with-net-class-library-functions) entre aplicações de função isoladas .NET e aplicações de função de biblioteca de classe .NET.

### <a name="benefits-of-running-out-of-process"></a>Benefícios do esgotar do processo

Ao esgotar o processo, as suas funções .NET podem tirar partido dos seguintes benefícios: 

+ Menos conflitos: como as funções funcionam num processo separado, os conjuntos utilizados na sua app não entrarão em conflito com diferentes versões dos mesmos conjuntos utilizados pelo processo de anfitrião.  
+ Controlo total do processo: controla o arranque da app e pode controlar as configurações utilizadas e o middleware iniciado.
+ Injeção de dependência: como tem o controlo total do processo, pode utilizar comportamentos atuais .NET para injeção de dependência e incorporar middleware na sua aplicação de função. 

## <a name="supported-versions"></a>Versões suportadas

A única versão de .NET que é atualmente suportada para ficar sem processo é .NET 5.0.

## <a name="net-isolated-project"></a>.NET projeto isolado

Um projeto de função isolada .NET é basicamente um projeto de aplicação de consola .NET que visa .NET 5.0. Seguem-se os ficheiros básicos necessários em qualquer projeto isolado .NET:

+ [host.jsarquivado.](functions-host-json.md)
+ [local.settings.jsarquivado.](functions-run-local.md#local-settings-file)
+ Arquivo de projeto C# (.csproj) que define o projeto e dependências.
+ Program.cs ficheiro que é o ponto de entrada para a aplicação.

## <a name="package-references"></a>Referências do pacote

Ao esgotar-se, o seu projeto .NET utiliza um conjunto único de pacotes, que implementam tanto a funcionalidade principal como as extensões de ligação. 

### <a name="core-packages"></a>Pacotes centrais 

As seguintes embalagens são necessárias para executar as suas funções .NET num processo isolado:

+ [Microsoft.Azure.Functions.Worker](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker/)
+ [Microsoft.Azure.Functions.Worker.Sdk](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker.Sdk/)

### <a name="extension-packages"></a>Pacotes de extensão

Porque as funções que funcionam num processo isolado .NET utilizam diferentes tipos de encadernação, requerem um conjunto único de pacotes de extensão de ligação. 

Encontrará estes pacotes de extensão ao abrigo [do Microsoft.Azure.Functions.Worker.Extensions](https://www.nuget.org/packages?q=Microsoft.Azure.Functions.Worker.Extensions).
 
## <a name="start-up-and-configuration"></a>Arranque e configuração 

Ao utilizar funções isoladas .NET, tem acesso ao arranque da sua aplicação de função, que normalmente está em Program.cs. É responsável por criar e começar o seu próprio caso de anfitrião. Como tal, também tem acesso direto ao pipeline de configuração da sua aplicação. Você pode muito mais facilmente injetar dependências e executar middleware quando está a ficar sem processo. 

O seguinte código mostra um exemplo de um `HostBuilder` gasoduto:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" range="20-33":::

A `HostBuilder` é usado para construir e devolver uma instância totalmente inicializada, `IHost` que você corre assíncronamente para iniciar a sua aplicação de função. 

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" range="35":::

### <a name="configuration"></a>Configuração

Ter acesso ao pipeline do construtor anfitrião significa que pode definir quaisquer configurações específicas da aplicação durante a inicialização. Estas configurações aplicam-se à aplicação da sua função em execução num processo separado. Para escamar as alterações no anfitrião das funções ou configuração de gatilho e de ligação, ainda terá de utilizar a [host.jsno ficheiro](functions-host-json.md).      

O exemplo a seguir mostra como adicionar `args` configuração, que são lidas como argumentos de linha de comando: 
 
:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" range="21-24" :::

O `ConfigureAppConfiguration` método é usado para configurar o resto do processo de construção e aplicação. Este exemplo também utiliza um [IConfigurationBuilder](/dotnet/api/microsoft.extensions.configuration.iconfigurationbuilder?view=dotnet-plat-ext-5.0&preserve-view=true), o que facilita a adição de vários itens de configuração. Como `ConfigureAppConfiguration` devolve a mesma instância de , também pode [`IConfiguration `](/dotnet/api/microsoft.extensions.configuration.iconfiguration?view=dotnet-plat-ext-5.0&preserve-view=true) chamá-lo várias vezes para adicionar vários itens de configuração. Pode aceder ao conjunto completo de configurações de ambos [`HostBuilderContext.Configuration`](/dotnet/api/microsoft.extensions.hosting.hostbuildercontext.configuration?view=dotnet-plat-ext-5.0&preserve-view=true) e [`IHost.Services`](/dotnet/api/microsoft.extensions.hosting.ihost.services?view=dotnet-plat-ext-5.0&preserve-view=true) .

Para saber mais sobre a configuração, consulte [a Configuração no ASP.NET Core](/aspnet/core/fundamentals/configuration/?view=aspnetcore-5.0&preserve-view=true). 

### <a name="dependency-injection"></a>Injeção de dependência

A injeção de dependência é simplificada, em comparação com as bibliotecas de classe .NET. Em vez de ter de criar uma classe de startups para registar serviços, basta recorrer `ConfigureServices` ao construtor anfitrião e utilizar os métodos de extensão [`IServiceCollection`](/dotnet/api/microsoft.extensions.dependencyinjection.iservicecollection?view=dotnet-plat-ext-5.0&preserve-view=true) para injetar serviços específicos. 

O exemplo a seguir injeta uma dependência de serviço singleton:  
 
:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" range="29-32" :::

Para saber mais, consulte [a injeção de dependência no ASP.NET Core](/aspnet/core/fundamentals/dependency-injection?view=aspnetcore-5.0&preserve-view=true).

### <a name="middleware"></a>Middleware

.NET isolado também suporta o registo de middleware, mais uma vez usando um modelo semelhante ao que existe em ASP.NET. Este modelo dá-lhe a capacidade de injetar lógica no pipeline de invocação, e antes e depois das funções executadas.

Embora o conjunto completo de registos de APIs de middleware ainda não esteja exposto, o registo de middleware é suportado e adicionámos um exemplo à aplicação da amostra sob a pasta Middleware.

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" range="25-28" :::

## <a name="execution-context"></a>Contexto de execução

.NET isolado passa um `FunctionContext` objeto para os seus métodos de função. Este objeto permite obter um [`ILogger`](/dotnet/api/microsoft.extensions.logging.ilogger?view=dotnet-plat-ext-5.0&preserve-view=true) caso para escrever para os registos, chamando o método e `GetLogger` fornecendo uma `categoryName` cadeia. Para saber mais, consulte [O Registo.](#logging) 

## <a name="bindings"></a>Enlaces 

As ligações são definidas utilizando atributos em métodos, parâmetros e tipos de devolução. Um método de função é um método com um `Function` atributo de gatilho aplicado a um parâmetro de entrada, como mostra o seguinte exemplo:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/SampleApp/Queue/QueueFunction.cs" range="11-14" :::

O atributo gatilho especifica o tipo de gatilho e liga os dados de entrada a um parâmetro do método. A função exemplo anterior é desencadeada por uma mensagem de fila, e a mensagem de fila é passada para o método no `myQueueItem` parâmetro.

O `Function` atributo marca o método como ponto de entrada de função. O nome deve ser único dentro de um projeto, começar com uma letra e conter apenas letras, números `_` e `-` até 127 caracteres de comprimento. Os modelos de projeto muitas vezes criam um método chamado `Run` , mas o nome do método pode ser qualquer nome de método C# válido.

Como os projetos isolados .NET funcionam num processo de trabalho separado, as ligações não podem tirar partido de classes de encadernação ricas, `ICollector<T>` `IAsyncCollector<T>` como, `CloudBlockBlob` e. Também não há suporte direto para tipos herdados de SDKs de serviço subjacente, tais como [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient) e [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). Em vez disso, as ligações dependem de cordas, matrizes e tipos serializáveis, tais como objetos de classe antiga simples (POCOs). 

Para os detonadores HTTP, deve utilizar `HttpRequestData` e `HttpResponseData` aceder aos dados de pedido e resposta. Isto porque não tem acesso aos objetos de pedido e resposta HTTP originais quando está a ficar sem processo. 

### <a name="input-bindings"></a>Enlaces de entrada

Uma função pode ter ligações de entrada zero ou mais que podem passar dados para uma função. Tal como os gatilhos, as ligações de entrada são definidas aplicando um atributo de ligação a um parâmetro de entrada. Quando a função executa, o tempo de execução tenta obter dados especificados na ligação. Os dados solicitados dependem frequentemente da informação fornecida pelo gatilho utilizando parâmetros vinculativos.  

### <a name="output-bindings"></a>Enlaces de saída

Para escrever para uma ligação de saída, deve aplicar um atributo de ligação de saída ao método de função, que definiu como escrever ao serviço vinculado. O valor devolvido pelo método é escrito para a ligação de saída. Por exemplo, o exemplo a seguir escreve um valor de corda para uma fila de mensagens nomeada `functiontesting2` utilizando uma ligação de saída:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/SampleApp/Queue/QueueFunction.cs" range="11-21" :::

### <a name="multiple-output-bindings"></a>Encadernações múltiplas de saída

Os dados escritos para uma ligação de saída é sempre o valor de devolução da função. Se precisar de escrever para mais de uma ligação de saída, tem de criar um tipo de devolução personalizado. Este tipo de devolução deve ter o atributo de ligação de saída aplicado a uma ou mais propriedades da classe. O exemplo a seguir escreve para uma resposta HTTP e uma ligação de saída de fila:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Function1/Function1.cs" range="14-33":::

### <a name="http-trigger"></a>Acionador HTTP

HTTP triggers traduz a mensagem de pedido HTTP recebida em um `HttpRequestData` objeto que é passado para a função. Este objeto fornece dados do pedido, `Headers` incluindo, , , e opcional uma `Cookies` `Identities` `URL` mensagem `Body` . Este objeto é uma representação do objeto de pedido HTTP e não do próprio pedido. 

Da mesma forma, a função devolve um `HttpReponseData` objeto, que fornece dados utilizados para criar a resposta HTTP, incluindo mensagem, `StatusCode` e `Headers` opcionalmente uma mensagem `Body` .  

O seguinte código é um gatilho HTTP 

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/SampleApp/Http/HttpFunction.cs" range="13-27" :::

## <a name="logging"></a>Registo

Em .NET isolado, pode escrever para registos utilizando uma [`ILogger`](/dotnet/api/microsoft.extensions.logging.ilogger?view=dotnet-plat-ext-5.0&preserve-view=true) instância obtida a partir de um objeto passado para a sua `FunctionContext` função. Ligue para o `GetLogger` método, passando um valor de cadeia que é o nome para a categoria em que os registos são escritos. A categoria é geralmente o nome da função específica a partir da qual os registos são escritos. Para saber mais sobre categorias, consulte o [artigo de monitorização.](functions-monitoring.md#log-levels-and-categories) 

O exemplo a seguir mostra como obter um `ILogger` e escrever registos dentro de uma função:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/SampleApp/Http/HttpFunction.cs" range="17-18" ::: 

Utilize vários métodos `ILogger` para escrever vários níveis de registo, tais como `LogWarning` ou `LogError` . Para saber mais sobre os níveis de registo, consulte o [artigo de monitorização](functions-monitoring.md#log-levels-and-categories).

Um [`ILogger`](/dotnet/api/microsoft.extensions.logging.ilogger?view=dotnet-plat-ext-5.0&preserve-view=true) também é fornecido quando se utiliza a injeção de [dependência](#dependency-injection).

## <a name="differences-with-net-class-library-functions"></a>Diferenças com as funções da biblioteca da classe .NET

Esta secção descreve o estado atual das diferenças funcionais e comportamentais em .NET 5.0 fora do processo em comparação com as funções da biblioteca da classe .NET em execução no processo:

| Recurso/comportamento |  Processo em curso (.NET Core 3.1) | Fora de processo (.NET 5.0) |
| ---- | ---- | ---- |
| Versões .NET | LTS (.NET Core 3.1) | Corrente (.NET 5.0) |
| Pacotes centrais | [Microsoft.NET.Sdk.Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) | [Microsoft.Azure.Functions.Worker](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker/)<br/>[Microsoft.Azure.Functions.Worker](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker.Sdk) | 
| Pacotes de extensão vinculativa | [`Microsoft.Azure.WebJobs.Extensions.*`](https://www.nuget.org/packages?q=Microsoft.Azure.WebJobs.Extensions)  | Sob [`Microsoft.Azure.Functions.Worker.Extensions.*`](https://www.nuget.org/packages?q=Microsoft.Azure.Functions.Worker.Extensions) | 
| Registo | [`ILogger`](/dotnet/api/microsoft.extensions.logging.ilogger?view=dotnet-plat-ext-5.0&preserve-view=true) passou para a função | [`ILogger`](/dotnet/api/microsoft.extensions.logging.ilogger?view=dotnet-plat-ext-5.0&preserve-view=true) obtido a partir de `FunctionContext` |
| Fichas de cancelamento | [Suportado](functions-dotnet-class-library.md#cancellation-tokens) | Não suportado |
| Enlaces de saída | Parâmetros fora | Valores de retorno |
| Tipos de enlace de saída |  `IAsyncCollector`, [DocumentoClient,](/dotnet/api/microsoft.azure.documents.client.documentclient?view=azure-dotnet&preserve-view=true) [BrokeredMessage,](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet&preserve-view=true)e outros tipos específicos do cliente | Tipos simples, tipos serializáveis JSON e matrizes. |
| Encadernações múltiplas de saída | Suportado | [Suportado](#multiple-output-bindings) |
| Acionador HTTP | [`HttpRequest`](/dotnet/api/microsoft.aspnetcore.http.httprequest?view=aspnetcore-5.0&preserve-view=true)/[`ObjectResult`](/dotnet/api/microsoft.aspnetcore.mvc.objectresult?view=aspnetcore-5.0&preserve-view=true) | `HttpRequestData`/`HttpResponseData` |
| Funções Duráveis | [Suportado](durable/durable-functions-overview.md) | Não suportado | 
| Obrigações imperativas | [Suportado](functions-dotnet-class-library.md#binding-at-runtime) | Não suportado |
| function.jsno artefacto | Gerado | Não gerado |
| Configuração | [host.js](functions-host-json.md) | [host.jse](functions-host-json.md) [a inicialização personalizada](#configuration) |
| Injeção de dependência | [Suportado](functions-dotnet-dependency-injection.md)  | [Suportado](#dependency-injection) |
| Middleware | Não suportado | [Suportado](#middleware) |
| Tempos de início a frio | Típico | Mais tempo, por causa do arranque a tempo. Corra no Linux em vez do Windows para reduzir potenciais atrasos. |
| ReadyToRun | [Suportado](functions-dotnet-class-library.md#readytorun) | _TBD_ |


## <a name="next-steps"></a>Passos seguintes

+ [Saiba mais sobre gatilhos e encadernações](functions-triggers-bindings.md)
+ [Saiba mais sobre as melhores práticas para funções Azure](functions-best-practices.md)
