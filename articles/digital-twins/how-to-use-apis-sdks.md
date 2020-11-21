---
title: Utilizar as APIs e SDKs do Azure Digital Twins
titleSuffix: Azure Digital Twins
description: Veja como trabalhar com as APIs das Gémeas Digitais Azure, incluindo via SDK.
author: baanders
ms.author: baanders
ms.date: 06/04/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: d349d07a66b21766ea529661c2f27d0c76ea4d3b
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024726"
---
# <a name="use-the-azure-digital-twins-apis-and-sdks"></a>Utilizar as APIs e SDKs do Azure Digital Twins

A Azure Digital Twins vem equipado com **APIs de avião de controlo** e **APIs de plano de dados** para gerir a sua instância e seus elementos. 
* As APIs do plano de controlo são APIs [Azure Resource Manager (ARM)](../azure-resource-manager/management/overview.md) e abrangem operações de gestão de recursos como criar e eliminar o seu caso. 
* As APIs do plano de dados são APIs Azure Digital Twins, e são usadas para operações de gestão de dados como a gestão de modelos, gémeos e o gráfico.

Este artigo apresenta uma visão geral das APIs disponíveis e os métodos para interagir com elas. Pode utilizar as APIs REST diretamente com os seus Swaggers associados, ou através de um SDK.

## <a name="overview-control-plane-apis"></a>Visão geral: APIs do plano de controlo

As APIs do plano de controlo são APIs [ARM](../azure-resource-manager/management/overview.md) usadas para gerir a sua instância Azure Digital Twins como um todo, por isso cobrem operações como criar ou apagar toda a sua instância. Também os utilizará para criar e eliminar pontos finais.

A versão API do plano de controlo mais atual é _**2020-10-31**_.

Para utilizar as APIs do plano de controlo:
* Pode ligar diretamente para as APIs, referindo-se à mais recente swagger na [pasta do plano de controlo Swagger](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/resource-manager/Microsoft.DigitalTwins). Este repo também inclui uma pasta de exemplos que mostram o uso.
* Atualmente você pode aceder ADKs para controlar APIs em...
  - [**.NET (C#)**](https://www.nuget.org/packages/Microsoft.Azure.Management.DigitalTwins/) [(referência [auto-gerada]](/dotnet/api/overview/azure/digitaltwins/management?view=azure-dotnet&preserve-view=true)([fonte)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Microsoft.Azure.Management.DigitalTwins)
  - [**Java**](https://search.maven.org/artifact/com.microsoft.azure.digitaltwins.v2020_10_31/azure-mgmt-digitaltwins/1.0.0/jar) [(referência [autogerada]](/java/api/overview/azure/digitaltwins?view=azure-java-stable)) ([fonte)](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins/mgmt-v2020_10_31)
  - [**JavaScript**](https://www.npmjs.com/package/@azure/arm-digitaltwins) [(fonte)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/arm-digitaltwins)
  - [**Python**](https://pypi.org/project/azure-mgmt-digitaltwins/) ([fonte)](https://github.com/Azure/azure-sdk-for-python/tree/release/v3/sdk/digitaltwins/azure-mgmt-digitaltwins)
  - [**Ir**](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/digitaltwins/mgmt/2020-10-31/digitaltwins) ([fonte)](https://github.com/Azure/azure-sdk-for-go/tree/master/services/digitaltwins/mgmt/2020-10-31/digitaltwins)

Também pode exercer APIs de controlo do plano interagindo com as Gémeas Digitais Azure através do [portal Azure](https://portal.azure.com) e [CLI](how-to-use-cli.md).

## <a name="overview-data-plane-apis"></a>Visão geral: APIs do plano de dados

As APIs do plano de dados são as APIs Azure Digital Twins usadas para gerir os elementos dentro da sua instância Azure Digital Twins. Incluem operações como criar rotas, carregar modelos, criar relacionamentos e gerir gémeos. Podem ser amplamente divididos nas seguintes categorias:
* **DigitalTwinModels** - A categoria DigitalTwinModels contém APIs para gerir os [modelos](concepts-models.md) num caso Azure Digital Twins. As atividades de gestão incluem upload, validação, recuperação e eliminação de modelos da autoria em DTDL.
* **DigitalTwins** - A categoria DigitalTwins contém as APIs que permitem aos desenvolvedores criar, modificar e eliminar [gémeos digitais](concepts-twins-graph.md) e as suas relações num exemplo de Azure Digital Twins.
* **Consulta** - A categoria de Consulta permite que os desenvolvedores [encontrem conjuntos de gémeos digitais no gráfico gémeo](how-to-query-graph.md) entre relacionamentos.
* **Rotas de Eventos** - A categoria Rotas de Eventos contém APIs para [encaminhar dados,](concepts-route-events.md)através do sistema e para serviços a jusante.

A versão API do plano de dados mais atual é _**2020-10-31**_.

Para utilizar as APIs do plano de dados:
* Pode ligar diretamente para as APIs, por...
   - referindo-se à mais recente Swagger na [pasta Swagger do plano de dados](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins). Este repo também inclui uma pasta de exemplos que mostram o uso. 
   - visualização da [documentação de referência](/rest/api/azure-digitaltwins/)da API .
* Pode utilizar o **.NET (C#) SDK**. Para utilizar o .NET SDK...
   - pode ver e adicionar o pacote do NuGet: [Azure.DigitalTwins.Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core). 
   - pode ver a [documentação de referência do SDK.](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true)
   - você pode encontrar a fonte SDK, incluindo uma pasta de amostras, em GitHub: [Azure IoT Digital Twins biblioteca cliente para .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core). 
   - pode ver informações detalhadas e exemplos de utilização continuando na secção [*.NET (C#) SDK (plano de dados)*](#net-c-sdk-data-plane) deste artigo.
* Pode utilizar o **Java SDK.** Para usar o Java SDK...
   - pode ver e instalar a embalagem a partir de Maven: [`com.azure:azure-digitaltwins-core`](https://search.maven.org/artifact/com.azure/azure-digitaltwins-core/1.0.0/jar)
   - você pode ver a [documentação de referência SDK](/java/api/overview/azure/digitaltwins/client?preserve-view=true&view=azure-java-stable)
   - você pode encontrar a fonte SDK em GitHub: [Azure IoT Digital Twins biblioteca de clientes para Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins/azure-digitaltwins-core)
* Pode utilizar o **JavaScript SDK**. Para utilizar o JavaScript SDK...
   - pode ver e instalar o pacote a partir de npm: [Azure Azure Digital Twins Core biblioteca de clientes para JavaScript](https://www.npmjs.com/package/@azure/digital-twins-core).
   - pode ver a [documentação de referência do SDK.](/javascript/api/@azure/digital-twins-core/?branch=master&view=azure-node-latest&preserve-view=true)
   - você pode encontrar a fonte SDK em GitHub: [Azure Azure Digital Twins Core biblioteca de clientes para JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/digital-twins-core)
* Pode utilizar o **Python SDK.** Para usar o Python SDK...
   - você pode ver e instalar o pacote a partir de PyPi: [Azure Azure Digital Twins Core biblioteca de clientes para Python](https://pypi.org/project/azure-digitaltwins-core/).
   - pode ver a [documentação de referência do SDK.](/python/api/azure-digitaltwins-core/azure.digitaltwins.core?view=azure-python&preserve-view=true)
   - você pode encontrar a fonte SDK em GitHub: [Azure Azure Digital Twins Core biblioteca de clientes para Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/digitaltwins/azure-digitaltwins-core)
* Pode gerar um SDK para outro idioma utilizando o AutoRest. Siga as instruções em [*Como-a-: Crie SDKs personalizados para Gémeos Digitais Azure com AutoRest*](how-to-create-custom-sdks.md).

Também pode exercitar APIs de plano de data interagindo com a Azure Digital Twins através do [CLI](how-to-use-cli.md).

## <a name="net-c-sdk-data-plane"></a>.NET (C#) SDK (plano de dados)

O Azure Digital Twins .NET (C#) SDK faz parte do Azure SDK para .NET. É de código aberto, e é baseado no plano de dados Azure Digital Twins APIs.

> [!NOTE]
> Para obter mais informações sobre o design da SDK, consulte os [princípios gerais de design dos SDKs Azure](https://azure.github.io/azure-sdk/general_introduction.html) e as diretrizes específicas de [design .NET](https://azure.github.io/azure-sdk/dotnet_introduction.html).

Para utilizar o SDK, inclua o pacote NuGet **Azure.DigitalTwins.Core** com o seu projeto. Também vai precisar da versão mais recente do pacote **Azure.Identity.**

* No Visual Studio, pode adicionar pacotes com o Gestor de Pacotes NuGet (acedido através *de Ferramentas > NuGet Package Manager > Gerir pacotes nuget para solução).* 
* Utilizando a ferramenta linha de comando .NET, pode executar:

    ```cmd/sh
    dotnet add package Azure.DigitalTwins.Core --version 1.0.0-preview.3
    dotnet add package Azure.identity
    ```

Para obter um walk-through detalhado da utilização das APIs na prática, consulte o [*Tutorial: Código de uma aplicação para clientes*](tutorial-code.md). 

### <a name="net-sdk-usage-examples"></a>.NET SDK exemplos de utilização

Aqui estão algumas amostras de código que ilustram a utilização do .NET SDK.

Autenticar contra o serviço:

```csharp
// Authenticate against the service and create a client
string adtInstanceUrl = "https://<your-Azure-Digital-Twins-instance-hostName>";
var credential = new DefaultAzureCredential();
DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credential);
```

[!INCLUDE [Azure Digital Twins: local credentials note](../../includes/digital-twins-local-credentials-note.md)] 

Faça upload de um modelo e modelos de lista:

```csharp
// Upload a model
var typeList = new List<string>();
string dtdl = File.ReadAllText("SampleModel.json");
typeList.Add(dtdl);
try {
    await client.CreateModelsAsync(typeList);
} catch (RequestFailedException rex) {
    Console.WriteLine($"Load model: {rex.Status}:{rex.Message}");
}
// Read a list of models back from the service
AsyncPageable<DigitalTwinsModelData> modelDataList = client.GetModelsAsync();
await foreach (DigitalTwinsModelData md in modelDataList)
{
    Console.WriteLine($"Type name: {md.DisplayName}: {md.Id}");
}
```

Criar e consultar gémeos:

```csharp
// Initialize twin metadata
BasicDigitalTwin updateTwinData = new BasicDigitalTwin();

twinData.Id = $"firstTwin";
twinData.Metadata.ModelId = "dtmi:com:contoso:SampleModel;1";
twinData.Contents.Add("data", "Hello World!");
try {
    await client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>("firstTwin", updateTwinData);
} catch(RequestFailedException rex) {
    Console.WriteLine($"Create twin error: {rex.Status}:{rex.Message}");  
}
 
// Run a query    
AsyncPageable<string> result = client.QueryAsync("Select * From DigitalTwins");
await foreach (string twin in result)
{
    // Use JSON deserialization to pretty-print
    object jsonObj = JsonSerializer.Deserialize<object>(twin);
    string prettyTwin = JsonSerializer.Serialize(jsonObj, new JsonSerializerOptions { WriteIndented = true });
    Console.WriteLine(prettyTwin);
    // Or use BasicDigitalTwin for convenient property access
    BasicDigitalTwin btwin = JsonSerializer.Deserialize<BasicDigitalTwin>(twin);
}
```

Consulte o [*Tutorial: Código de um aplicativo*](tutorial-code.md) de cliente para uma passagem deste código de aplicação de amostra. 

Também pode encontrar amostras adicionais no [repo GitHub para o .NET (C#) SDK](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core/samples).

#### <a name="serialization-helpers"></a>Ajudantes de serialização

Os ajudantes de serialização são funções de ajuda disponíveis dentro do SDK para criar ou deserizar rapidamente dados gémeos para acesso a informações básicas. Uma vez que os métodos SDK principais devolvem dados gémeos como JSON por padrão, pode ser útil usar estas classes de ajudantes para quebrar ainda mais os dados gémeos.

As aulas de ajudante disponível são:
* `BasicDigitalTwin`: Representa os dados fundamentais de um gémeo digital
* `BasicRelationship`: Representa os dados fundamentais de uma relação
* `UpdateOperationUtility`: Representa as informações do Patch JSON utilizadas nas chamadas de atualização
* `WriteableProperty`: Representa metadados de propriedade

##### <a name="deserialize-a-digital-twin"></a>Deserializar um gémeo digital

Pode sempre desseeciar dados gémeos utilizando a biblioteca JSON à sua escolha, como `System.Test.Json` ou `Newtonsoft.Json` . Para o acesso básico a um gémeo, as aulas de ajudante tornam isto um pouco mais conveniente.

```csharp
Response<BasicDigitalTwin> twin = client.GetDigitalTwin(twin_id);
Console.WriteLine($"Model id: {twin.Metadata.ModelId}");
```

A `BasicDigitalTwin` classe de ajudante também lhe dá acesso a propriedades definidas no gémeo, através de um `Dictionary<string, object>` . Para listar as propriedades do gémeo, pode utilizar:

```csharp
Response<BasicDigitalTwin> twin = client.GetDigitalTwin(twin_id);
Console.WriteLine($"Model id: {twin.Metadata.ModelId}");
foreach (string prop in twin.Contents.Keys)
{
    if (twin.Contents.TryGetValue(prop, out object value))
        Console.WriteLine($"Property '{prop}': {value}");
}
```

##### <a name="create-a-digital-twin"></a>Criar um gémeo digital

Utilizando a `BasicDigitalTwin` classe, pode preparar dados para criar uma instância dupla:

```csharp
BasicDigitalTwin twin = new BasicDigitalTwin();
twin.Metadata = new DigitalTwinMetadata();
twin.Metadata.ModelId = "dtmi:example:Room;1";
// Initialize properties
Dictionary<string, object> props = new Dictionary<string, object>();
props.Add("Temperature", 25.0);
twin.Contents = props;

client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>("myNewRoomID", twin);
```

O código acima é equivalente à seguinte variante "manual":

```csharp
Dictionary<string, object> meta = new Dictionary<string, object>()
{
    { "$model", "dtmi:example:Room;1"}
};
Dictionary<string, object> twin = new Dictionary<string, object>()
{
    { "$metadata", meta },
    { "Temperature", 25.0 }
};
client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>("myNewRoomID", twin);
```

##### <a name="deserialize-a-relationship"></a>Deserializar uma relação

Pode sempre desseeciar dados de relacionamento a um tipo de escolha. Para acesso básico a uma relação, utilize o tipo `BasicRelationship` .

```csharp
BasicRelationship res = client.GetRelationship<BasicRelationship>(twin_id, rel_id);
Console.WriteLine($"Relationship Name: {rel.Name}");
```

A `BasicRelationship` classe de ajudante também lhe dá acesso a propriedades definidas na relação, através de um `IDictionary<string, object>` . Para listar propriedades, pode utilizar:

```csharp
BasicRelationship res = client.GetRelationship<BasicRelationship>(twin_id, rel_id);
Console.WriteLine($"Relationship Name: {rel.Name}");
foreach (string prop in rel.Contents.Keys)
{
    if (twin.Contents.TryGetValue(prop, out object value))
        Console.WriteLine($"Property '{prop}': {value}");
}
```

##### <a name="create-a-relationship"></a>Criar uma relação

Utilizando a `BasicRelationship` classe, também pode preparar dados para criar relacionamentos num caso duplo:

```csharp
BasicRelationship rel = new BasicRelationship();
rel.TargetId = "myTargetTwin";
rel.Name = "contains"; // a relationship with this name must be defined in the model
// Initialize properties
Dictionary<string, object> props = new Dictionary<string, object>();
props.Add("active", true);
rel.Properties = props;
client.CreateOrReplaceRelationshipAsync("mySourceTwin", "rel001", rel);
```

##### <a name="create-a-patch-for-twin-update"></a>Crie um patch para atualização twin

A atualização de chamadas para gémeos e relacionamentos usam a estrutura [do JSON Patch.](http://jsonpatch.com/) Para criar listas de operações do JSON Patch, pode utilizar o `JsonPatchDocument` como mostrado abaixo.

```csharp
var updateTwinData = new JsonPatchDocument();
updateTwinData.AppendAddOp("/Temperature", 25.0);
updateTwinData.AppendAddOp("/myComponent/Property", "Hello");
// Un-set a property
updateTwinData.AppendRemoveOp("/Humidity");

client.UpdateDigitalTwin("myTwin", updateTwinData);
```

## <a name="general-apisdk-usage-notes"></a>Notas gerais de utilização da API/SDK

> [!NOTE]
> Por favor, note que a Azure Digital Twins não suporta atualmente **a Partilha de Recursos cross-Origin (CORS)**. Para obter mais informações sobre as estratégias de impacto e resolução, consulte a secção [*de Partilha de Recursos de Origem Cruzada (CORS)*](concepts-security.md#cross-origin-resource-sharing-cors) de *Conceitos: Segurança para as soluções Azure Digital Twins*.

A lista a seguir fornece detalhes adicionais e orientações gerais para a utilização das APIs e dos SDKs.

* Para usar o SDK, instantânea a `DigitalTwinsClient` aula. O construtor requer credenciais que podem ser obtidas com uma variedade de métodos de autenticação na `Azure.Identity` embalagem. Para mais `Azure.Identity` informações, consulte a [documentação do espaço de nome.](/dotnet/api/azure.identity?preserve-view=true&view=azure-dotnet) 
* Pode encontrar o útil durante o `InteractiveBrowserCredential` arranque, mas existem várias outras opções, incluindo credenciais para [identidade gerida](/dotnet/api/azure.identity.interactivebrowsercredential?preserve-view=true&view=azure-dotnet), que provavelmente utilizará para autenticar [funções Azure configuradas com MSI](../app-service/overview-managed-identity.md?tabs=dotnet) contra Azure Digital Twins. Para mais informações `InteractiveBrowserCredential` sobre , consulte a sua [documentação de classe.](/dotnet/api/azure.identity.interactivebrowsercredential?preserve-view=true&view=azure-dotnet)
* Todas as chamadas de API de serviço são expostas como funções de membro na `DigitalTwinsClient` classe.
* Todas as funções de serviço existem em versões sincronizadas e assíncronos.
* Todas as funções de serviço lançam uma exceção para qualquer estado de devolução de 400 ou mais. Certifique-se de embrulhar as chamadas numa `try` secção e apanhar pelo `RequestFailedExceptions` menos. Para saber mais sobre este tipo de exceção, consulte [aqui.](/dotnet/api/azure.requestfailedexception?preserve-view=true&view=azure-dotnet)
* A maioria dos métodos de serviço regressam `Response<T>` ou `Task<Response<T>>` (para as chamadas assíncronas), onde `T` está a classe de objeto de retorno para a chamada de serviço. A [`Response`](/dotnet/api/azure.response-1?preserve-view=true&view=azure-dotnet) classe encapsula a devolução do serviço e apresenta valores de retorno no seu `Value` campo.  
* Métodos de serviço com retorno de resultados paged `Pageable<T>` ou `AsyncPageable<T>` como resultados. Para saber mais sobre a `Pageable<T>` classe, consulte [aqui;](/dotnet/api/azure.pageable-1?preserve-view=true&view=azure-dotnet-preview)para mais `AsyncPageable<T>` informações, consulte [aqui.](/dotnet/api/azure.asyncpageable-1?preserve-view=true&view=azure-dotnet-preview)
* Pode iterar os resultados paged utilizando um `await foreach` loop. Para saber mais sobre este processo, consulte [aqui.](/archive/msdn-magazine/2019/november/csharp-iterating-with-async-enumerables-in-csharp-8)
* O SDK subjacente `Azure.Core` é. Consulte a documentação do [espaço de nomeS Azure](/dotnet/api/azure?preserve-view=true&view=azure-dotnet-preview) para obter referência sobre a infraestrutura e tipos SDK.

Os métodos de serviço retornam objetos fortemente digitado sempre que possível. No entanto, uma vez que o Azure Digital Twins se baseia em modelos configurados pelo utilizador em tempo de execução (através de modelos DTDL enviados para o serviço), muitas APIs de serviço pegam e devolvem dados gémeos em formato JSON.

## <a name="monitor-api-metrics"></a>Monitorizar métricas de API

Métricas de API como pedidos, latência e taxa de insucesso podem ser vistas no [portal Azure](https://portal.azure.com/). 

A partir da página inicial do portal, procure a sua instância Azure Digital Twins para obter os seus detalhes. Selecione a opção **Métricas** no menu da instância Azure Digital Twins para apresentar a página *Métricas.*

:::image type="content" source="media/troubleshoot-metrics/azure-digital-twins-metrics.png" alt-text="Screenshot mostrando a página métrica para Azure Digital Twins":::

A partir daqui, você pode ver as métricas para o seu exemplo e criar vistas personalizadas.

## <a name="next-steps"></a>Próximos passos

Veja como usar as APIs para configurar uma instância e autenticação de Gémeos Digitais Azure:
* [*Como fazer: Configurar um caso e autenticação*](how-to-set-up-instance-cli.md)

Ou, caminhe pelos degraus para criar uma aplicação de cliente como a usada neste como:
* [*Tutorial: Código de uma aplicação de cliente*](tutorial-code.md)