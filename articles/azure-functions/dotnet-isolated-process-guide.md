---
title: .NET guia de processo isolado para .NET 5.0 em Funções Azure
description: Aprenda a utilizar um processo isolado .NET para executar as suas funções C# em .NET 5.0 fora de processo em Azure.
ms.service: azure-functions
ms.topic: conceptual
ms.date: 03/01/2021
ms.custom: template-concept
ms.openlocfilehash: 4da685c247427e78297df1753779ee9b5c7866b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105023202"
---
# <a name="guide-for-running-functions-on-net-50-in-azure"></a>Guia para executar funções em .NET 5.0 em Azure

Este artigo é uma introdução à utilização de C# para desenvolver funções de processo isolado .NET, que estão a ficar sem processo em Funções Azure. O processo esgotado permite-lhe dissociar o seu código de função do tempo de funcionamento das Funções Azure. Também fornece uma forma de criar e executar funções que visam a versão atual .NET 5.0. 

| Introdução | Conceitos| Amostras |
|--|--|--| 
| <ul><li>[Utilizar o Visual Studio Code](dotnet-isolated-process-developer-howtos.md?pivots=development-environment-vscode)</li><li>[Usando ferramentas de linha de comando](dotnet-isolated-process-developer-howtos.md?pivots=development-environment-cli)</li><li>[Com o Visual Studio](dotnet-isolated-process-developer-howtos.md?pivots=development-environment-vs)</li></ul> | <ul><li>[Opções de alojamento](functions-scale.md)</li><li>[Monitorização](functions-monitoring.md)</li> | <ul><li>[Amostras de referência](https://github.com/Azure/azure-functions-dotnet-worker/tree/main/samples)</li></ul> |

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
+ Programa.cs ficheiro que é o ponto de entrada para a aplicação.

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

Ao utilizar funções isoladas .NET, tem acesso ao arranque da sua aplicação de função, que normalmente está no Programa.cs. É responsável por criar e começar o seu próprio caso de anfitrião. Como tal, também tem acesso direto ao pipeline de configuração da sua aplicação. Ao esgotar o processo, pode adicionar configurações, injetar dependências e executar o seu próprio middleware. 

O seguinte código mostra um exemplo de um oleoduto [HostBuilder:]

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_startup":::

Um [HostBuilder] é usado para construir e devolver uma instância [IHost] totalmente inicializada, que você corre assíncronamente para iniciar a sua aplicação de função. 

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_host_run":::

### <a name="configuration"></a>Configuração

O método [ConfigureFunctionsWorkerDefaults] é utilizado para adicionar as definições necessárias para que a aplicação de função fique sem processo, o que inclui a seguinte funcionalidade:

+ Conjunto padrão de conversores.
+ Desa estade as [Opições JsonSerializer] padrão para ignorar o invólucro nos nomes de propriedade.
+ Integre-se com o registo de funções Azure.
+ Middleware e funcionalidades de ligação de saída.
+ Função execução middleware.
+ Suporte gRPC predefinido. 

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_configure_defaults" :::   

Ter acesso ao pipeline do construtor anfitrião significa que também pode definir quaisquer configurações específicas da aplicação durante a inicialização. Pode ligar para o método [ConfigurarAppConfiguration] no [HostBuilder] uma ou mais vezes para adicionar as configurações necessárias pela sua aplicação de função. Para saber mais sobre a configuração da aplicação, consulte [Configuração no ASP.NET Core](/aspnet/core/fundamentals/configuration/?view=aspnetcore-5.0&preserve-view=true). 

Estas configurações aplicam-se à aplicação da sua função em execução num processo separado. Para escamar as alterações no anfitrião das funções ou configuração de gatilho e de ligação, ainda terá de utilizar a [host.jsno ficheiro](functions-host-json.md).   

### <a name="dependency-injection"></a>Injeção de dependência

A injeção de dependência é simplificada, em comparação com as bibliotecas de classe .NET. Em vez de ter de criar uma classe de startups para registar serviços, basta ligar para a [ConfigureServices] no construtor anfitrião e utilizar os métodos de extensão no [IServiceCollection] para injetar serviços específicos. 

O exemplo a seguir injeta uma dependência de serviço singleton:  
 
:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_dependency_injection" :::

Para saber mais, consulte [a injeção de dependência no ASP.NET Core](/aspnet/core/fundamentals/dependency-injection?view=aspnetcore-5.0&preserve-view=true).

### <a name="middleware"></a>Middleware

.NET isolado também suporta o registo de middleware, mais uma vez usando um modelo semelhante ao que existe em ASP.NET. Este modelo dá-lhe a capacidade de injetar lógica no pipeline de invocação, e antes e depois das funções executadas.

O método de extensão [ConfigureFunctionsWorkerDefaults] tem uma sobrecarga que permite registar o seu próprio middleware, como pode ver no exemplo seguinte.  

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/CustomMiddleware/Program.cs" id="docsnippet_middleware_register" :::

Para obter um exemplo mais completo de utilização de middleware personalizado na sua aplicação de função, consulte a [amostra de referência de middleware personalizada](https://github.com/Azure/azure-functions-dotnet-worker/blob/main/samples/CustomMiddleware).

## <a name="execution-context"></a>Contexto de execução

.NET isolado passa um objeto [FunctionContext] para os seus métodos de função. Este objeto permite-lhe obter uma instância [ILogger] para escrever para os registos, chamando o método [GetLogger] e fornecendo uma `categoryName` corda. Para saber mais, consulte [O Registo.](#logging) 

## <a name="bindings"></a>Enlaces 

As ligações são definidas utilizando atributos em métodos, parâmetros e tipos de devolução. Um método de função é um método com um `Function` atributo e um atributo de gatilho aplicado a um parâmetro de entrada, como mostra o exemplo seguinte:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/Queue/QueueFunction.cs" id="docsnippet_queue_trigger" :::

O atributo gatilho especifica o tipo de gatilho e liga os dados de entrada a um parâmetro do método. A função exemplo anterior é desencadeada por uma mensagem de fila, e a mensagem de fila é passada para o método no `myQueueItem` parâmetro.

O `Function` atributo marca o método como ponto de entrada de função. O nome deve ser único dentro de um projeto, começar com uma letra e conter apenas letras, números `_` e `-` até 127 caracteres de comprimento. Os modelos de projeto muitas vezes criam um método chamado `Run` , mas o nome do método pode ser qualquer nome de método C# válido.

Como os projetos isolados .NET funcionam num processo de trabalho separado, as ligações não podem tirar partido de classes de encadernação ricas, `ICollector<T>` `IAsyncCollector<T>` como, `CloudBlockBlob` e. Também não há suporte direto para tipos herdados de SDKs de serviço subjacente, tais como [DocumentClient] e [BrokeredMessage]. Em vez disso, as ligações dependem de cordas, matrizes e tipos serializáveis, tais como objetos de classe antiga simples (POCOs). 

Para os detonadores HTTP, tem de utilizar [httpRequestData] e [HttpResponseData] para aceder aos dados de pedido e resposta. Isto porque não tem acesso aos objetos de pedido e resposta HTTP originais quando está a ficar sem processo.

Para obter um conjunto completo de amostras de referência para a utilização de gatilhos e encadernações durante o escoramento, consulte a amostra de referência das [extensões de ligação](https://github.com/Azure/azure-functions-dotnet-worker/blob/main/samples/Extensions). 

### <a name="input-bindings"></a>Enlaces de entrada

Uma função pode ter ligações de entrada zero ou mais que podem passar dados para uma função. Tal como os gatilhos, as ligações de entrada são definidas aplicando um atributo de ligação a um parâmetro de entrada. Quando a função executa, o tempo de execução tenta obter dados especificados na ligação. Os dados solicitados dependem frequentemente da informação fornecida pelo gatilho utilizando parâmetros vinculativos.  

### <a name="output-bindings"></a>Enlaces de saída

Para escrever para uma ligação de saída, deve aplicar um atributo de ligação de saída ao método de função, que definiu como escrever ao serviço vinculado. O valor devolvido pelo método é escrito para a ligação de saída. Por exemplo, o exemplo a seguir escreve um valor de corda para uma fila de mensagens nomeada `functiontesting2` utilizando uma ligação de saída:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/Queue/QueueFunction.cs" id="docsnippet_queue_output_binding" :::

### <a name="multiple-output-bindings"></a>Encadernações múltiplas de saída

Os dados escritos para uma ligação de saída é sempre o valor de devolução da função. Se precisar de escrever para mais de uma ligação de saída, tem de criar um tipo de devolução personalizado. Este tipo de devolução deve ter o atributo de ligação de saída aplicado a uma ou mais propriedades da classe. O exemplo a seguir de um gatilho HTTP escreve tanto para a resposta HTTP como para uma ligação de saída de fila:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/MultiOutput/MultiOutput.cs" id="docsnippet_multiple_outputs":::

A resposta de um gatilho HTTP é sempre considerada uma saída, pelo que não é necessário um atributo de valor de devolução.

### <a name="http-trigger"></a>Acionador HTTP

HTTP triggers traduz a mensagem de pedido HTTP incoming num objeto [HttpRequestData] que é passado para a função. Este objeto fornece dados do pedido, `Headers` incluindo, , , e opcional uma `Cookies` `Identities` `URL` mensagem `Body` . Este objeto é uma representação do objeto de pedido HTTP e não do próprio pedido. 

Da mesma forma, a função devolve um objeto [HttpResponseData,] que fornece dados utilizados para criar a resposta HTTP, incluindo `StatusCode` mensagem, e `Headers` opcionalmente uma mensagem `Body` .  

O seguinte código é um gatilho HTTP 

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/Http/HttpFunction.cs" id="docsnippet_http_trigger" :::

## <a name="logging"></a>Registo

Em .NET isolado, pode escrever para registos utilizando uma instância [ILogger] obtida a partir de um objeto [FunctionContext] transmitido para a sua função. Ligue para o método [GetLogger,] passando um valor de cadeia que é o nome para a categoria em que os registos são escritos. A categoria é geralmente o nome da função específica a partir da qual os registos são escritos. Para saber mais sobre categorias, consulte o [artigo de monitorização.](functions-monitoring.md#log-levels-and-categories) 

O exemplo a seguir mostra como obter um [ILogger] e escrever registos dentro de uma função:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/Http/HttpFunction.cs" id="docsnippet_logging" ::: 

Utilize vários métodos de [ILogger] para escrever vários níveis de log, tais como `LogWarning` ou `LogError` . Para saber mais sobre os níveis de registo, consulte o [artigo de monitorização](functions-monitoring.md#log-levels-and-categories).

Um [ILogger] também é fornecido quando se utiliza [uma injeção de dependência](#dependency-injection).

## <a name="differences-with-net-class-library-functions"></a>Diferenças com as funções da biblioteca da classe .NET

Esta secção descreve o estado atual das diferenças funcionais e comportamentais em .NET 5.0 fora do processo em comparação com as funções da biblioteca da classe .NET em execução no processo:

| Recurso/comportamento |  Processo em curso (.NET Core 3.1) | Fora de processo (.NET 5.0) |
| ---- | ---- | ---- |
| Versões .NET | LTS (.NET Core 3.1) | Corrente (.NET 5.0) |
| Pacotes centrais | [Microsoft.NET.Sdk.Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) | [Microsoft.Azure.Functions.Worker](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker/)<br/>[Microsoft.Azure.Functions.Worker.Sdk](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker.Sdk) | 
| Pacotes de extensão vinculativa | [Microsoft.Azure.WebJobs.Extensions.*](https://www.nuget.org/packages?q=Microsoft.Azure.WebJobs.Extensions)  | Sob [microsoft.Azure.Functions.Worker.Extensions.*](https://www.nuget.org/packages?q=Microsoft.Azure.Functions.Worker.Extensions) | 
| Registo | [ILogger] passou para a função | [ILogger] obtido a partir de [FunctionContext] |
| Fichas de cancelamento | [Suportado](functions-dotnet-class-library.md#cancellation-tokens) | Não suportado |
| Enlaces de saída | Parâmetros fora | Valores de retorno |
| Tipos de enlace de saída |  `IAsyncCollector`, [DocumentoClient,] [BrokeredMessage,]e outros tipos específicos do cliente | Tipos simples, tipos serializáveis JSON e matrizes. |
| Encadernações múltiplas de saída | Suportado | [Suportado](#multiple-output-bindings) |
| Acionador HTTP | [HttpRequest] / [ObjectResult] | [HttpRequestData] / [HttpResponseData] |
| Funções Duráveis | [Suportado](durable/durable-functions-overview.md) | Não suportado | 
| Obrigações imperativas | [Suportado](functions-dotnet-class-library.md#binding-at-runtime) | Não suportado |
| function.jsno artefacto | Gerado | Não gerado |
| Configuração | [host.js](functions-host-json.md) | [host.jse](functions-host-json.md) a inicialização personalizada |
| Injeção de dependência | [Suportado](functions-dotnet-dependency-injection.md)  | [Suportado](#dependency-injection) |
| Middleware | Não suportado | Suportado |
| Tempos de início a frio | Típico | Mais tempo, por causa do arranque a tempo. Corra no Linux em vez do Windows para reduzir potenciais atrasos. |
| ReadyToRun | [Suportado](functions-dotnet-class-library.md#readytorun) | _TBD_ |

## <a name="known-issues"></a>Problemas conhecidos

Para obter informações sobre soluções alternativas para saber os problemas em execução .NET funções de processo isolado, consulte [esta página de assuntos conhecidos](https://aka.ms/AAbh18e). Para relatar problemas, [crie um problema neste repositório gitHub](https://github.com/Azure/azure-functions-dotnet-worker/issues/new/choose).  

## <a name="next-steps"></a>Passos seguintes

+ [Saiba mais sobre gatilhos e encadernações](functions-triggers-bindings.md)
+ [Saiba mais sobre as melhores práticas para funções Azure](functions-best-practices.md)


[HostBuilder]: /dotnet/api/microsoft.extensions.hosting.hostbuilder?view=dotnet-plat-ext-5.0&preserve-view=true
[IHost]: /dotnet/api/microsoft.extensions.hosting.ihost?view=dotnet-plat-ext-5.0&preserve-view=true
[ConfigureFunctionsWorkerDefaults]: /dotnet/api/microsoft.extensions.hosting.workerhostbuilderextensions.configurefunctionsworkerdefaults?view=azure-dotnet&preserve-view=true#Microsoft_Extensions_Hosting_WorkerHostBuilderExtensions_ConfigureFunctionsWorkerDefaults_Microsoft_Extensions_Hosting_IHostBuilder_
[Configuração DoAppConfiguration]: /dotnet/api/microsoft.extensions.hosting.hostbuilder.configureappconfiguration?view=dotnet-plat-ext-5.0&preserve-view=true
[IServiceCollection]: /dotnet/api/microsoft.extensions.dependencyinjection.iservicecollection?view=dotnet-plat-ext-5.0&preserve-view=true
[ConfigureServices]: /dotnet/api/microsoft.extensions.hosting.hostbuilder.configureservices?view=dotnet-plat-ext-5.0&preserve-view=true
[FunçãoContexto]: /dotnet/api/microsoft.azure.functions.worker.functioncontext?view=azure-dotnet&preserve-view=true
[ILogger]: /dotnet/api/microsoft.extensions.logging.ilogger?view=dotnet-plat-ext-5.0&preserve-view=true
[GetLogger]: /dotnet/api/microsoft.azure.functions.worker.functioncontextloggerextensions.getlogger?view=azure-dotnet&preserve-view=true
[DocumentoClient]: /dotnet/api/microsoft.azure.documents.client.documentclient
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[HttpRequestData]: /dotnet/api/microsoft.azure.functions.worker.http.httprequestdata?view=azure-dotnet&preserve-view=true
[HttpResponseData]: /dotnet/api/microsoft.azure.functions.worker.http.httpresponsedata?view=azure-dotnet&preserve-view=true
[HttpRequest]: /dotnet/api/microsoft.aspnetcore.http.httprequest?view=aspnetcore-5.0&preserve-view=true
[ObjectResult]: /dotnet/api/microsoft.aspnetcore.mvc.objectresult?view=aspnetcore-5.0&preserve-view=true
[Opções JsonSerializer]: /api/system.text.json.jsonserializeroptions?view=net-5.0&preserve-view=true
