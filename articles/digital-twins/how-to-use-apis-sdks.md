---
title: Utilizar as APIs e SDKs do Azure Digital Twins
titleSuffix: Azure Digital Twins
description: Veja como trabalhar com as APIs das Gémeas Digitais Azure, incluindo via SDK.
author: baanders
ms.author: baanders
ms.date: 06/04/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 21247f6b396cb1f7016c74cbec528149c0583724
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587209"
---
# <a name="use-the-azure-digital-twins-apis-and-sdks"></a>Utilizar as APIs e SDKs do Azure Digital Twins

A Azure Digital Twins vem equipado com **APIs de avião de controlo** e **APIs de plano de dados** para gerir a sua instância e seus elementos. 
* As APIs do plano de controlo são APIs [Azure Resource Manager (ARM)](../azure-resource-manager/management/overview.md) e abrangem operações de gestão de recursos como criar e eliminar o seu caso. 
* As APIs do plano de dados são APIs Azure Digital Twins, e são usadas para operações de gestão de dados como a gestão de modelos, gémeos e o gráfico.

Este artigo apresenta uma visão geral das APIs disponíveis e os métodos para interagir com elas. Pode utilizar as APIs REST diretamente com os seus Swaggers associados (através de uma ferramenta como [o Carteiro),](how-to-use-postman.md)ou através de um SDK.

## <a name="overview-control-plane-apis"></a>Visão geral: APIs do plano de controlo

As APIs do plano de controlo são APIs [ARM](../azure-resource-manager/management/overview.md) usadas para gerir a sua instância Azure Digital Twins como um todo, por isso cobrem operações como criar ou apagar toda a sua instância. Também os utilizará para criar e eliminar pontos finais.

A versão API do plano de controlo mais atual é _**2020-12-01**_.

Para utilizar as APIs do plano de controlo:
* Pode ligar diretamente para as APIs, referindo-se à mais recente pasta Swagger no [plano de controlo Swagger repo](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/resource-manager/Microsoft.DigitalTwins/stable). Esta pasta também inclui uma pasta de exemplos que mostram a utilização.
* Atualmente você pode aceder ADKs para controlar APIs em...
  - [**.NET (C#)**](https://www.nuget.org/packages/Microsoft.Azure.Management.DigitalTwins/) [(referência [auto-gerada]](/dotnet/api/overview/azure/digitaltwins/management)([fonte)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Microsoft.Azure.Management.DigitalTwins)
  - [**Java**](https://search.maven.org/search?q=a:azure-mgmt-digitaltwins) [(referência [autogerada]](/java/api/overview/azure/digitaltwins)) ([fonte)](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins)
  - [**JavaScript**](https://www.npmjs.com/package/@azure/arm-digitaltwins) [(fonte)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/arm-digitaltwins)
  - [**Python**](https://pypi.org/project/azure-mgmt-digitaltwins/) ([fonte)](https://github.com/Azure/azure-sdk-for-python/tree/release/v3/sdk/digitaltwins/azure-mgmt-digitaltwins)
  - [**Ir**](https://pkg.go.dev/github.com/Azure/azure-sdk-for-go/services/digitaltwins/mgmt) ([fonte)](https://github.com/Azure/azure-sdk-for-go/tree/master/services/digitaltwins/mgmt)

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
   - referindo-se à mais recente pasta Swagger no [plano de dados Swagger repo](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins). Esta pasta também inclui uma pasta de exemplos que mostram a utilização. 
   - visualização da [documentação de referência](/rest/api/azure-digitaltwins/)da API .
* Pode utilizar o **.NET (C#) SDK**. Para utilizar o .NET SDK...
   - pode ver e adicionar o pacote do NuGet: [Azure.DigitalTwins.Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core). 
   - pode ver a [documentação de referência do SDK.](/dotnet/api/overview/azure/digitaltwins/client)
   - você pode encontrar a fonte SDK, incluindo uma pasta de amostras, em GitHub: [Azure IoT Digital Twins biblioteca cliente para .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core). 
   - pode ver informações detalhadas e exemplos de utilização continuando na secção [*.NET (C#) SDK (plano de dados)*](#net-c-sdk-data-plane) deste artigo.
* Pode utilizar o **Java SDK.** Para usar o Java SDK...
   - pode ver e instalar a embalagem a partir de Maven: [`com.azure:azure-digitaltwins-core`](https://search.maven.org/artifact/com.azure/azure-digitaltwins-core/1.0.0/jar)
   - você pode ver a [documentação de referência SDK](/java/api/overview/azure/digitaltwins/client)
   - você pode encontrar a fonte SDK em GitHub: [Azure IoT Digital Twins biblioteca de clientes para Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins/azure-digitaltwins-core)
* Pode utilizar o **JavaScript SDK**. Para utilizar o JavaScript SDK...
   - pode ver e instalar o pacote a partir de npm: [Azure Azure Digital Twins Core biblioteca de clientes para JavaScript](https://www.npmjs.com/package/@azure/digital-twins-core).
   - pode ver a [documentação de referência do SDK.](/javascript/api/@azure/digital-twins-core/)
   - você pode encontrar a fonte SDK em GitHub: [Azure Azure Digital Twins Core biblioteca de clientes para JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/digital-twins-core)
* Pode utilizar o **Python SDK.** Para usar o Python SDK...
   - você pode ver e instalar o pacote a partir de PyPi: [Azure Azure Digital Twins Core biblioteca de clientes para Python](https://pypi.org/project/azure-digitaltwins-core/).
   - pode ver a [documentação de referência do SDK.](/python/api/azure-digitaltwins-core/azure.digitaltwins.core)
   - você pode encontrar a fonte SDK em GitHub: [Azure Azure Digital Twins Core biblioteca de clientes para Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/digitaltwins/azure-digitaltwins-core)
* Pode gerar um SDK para outro idioma utilizando o AutoRest. Siga as instruções em [*Como-a-: Crie SDKs personalizados para Gémeos Digitais Azure com AutoRest*](how-to-create-custom-sdks.md).

Também pode exercitar APIs de plano de data interagindo com a Azure Digital Twins através do [CLI](how-to-use-cli.md).

## <a name="net-c-sdk-data-plane"></a>.NET (C#) SDK (plano de dados)

O Azure Digital Twins .NET (C#) SDK faz parte do Azure SDK para .NET. É de código aberto, e é baseado no plano de dados Azure Digital Twins APIs.

> [!NOTE]
> Para obter mais informações sobre o design da SDK, consulte os [princípios gerais de design dos SDKs Azure](https://azure.github.io/azure-sdk/general_introduction.html) e as diretrizes específicas de [design .NET](https://azure.github.io/azure-sdk/dotnet_introduction.html).

Para utilizar o SDK, inclua o pacote NuGet **Azure.DigitalTwins.Core** com o seu projeto. Também vai precisar da versão mais recente do pacote **Azure.Identity.** No Visual Studio, pode adicionar estes pacotes utilizando o NuGet Package Manager (acedido através *de Ferramentas > NuGet Package Manager > Gerir pacotes nuget para solução).* Em alternativa, pode utilizar a ferramenta de linha de comando .NET com os comandos encontrados nos links de pacote NuGet abaixo para adicioná-los ao seu projeto:
* [**Azure.DigitalTwins.Core**](https://www.nuget.org/packages/Azure.DigitalTwins.Core). Este é o pacote para o [Azure Digital Twins SDK para .NET](/dotnet/api/overview/azure/digitaltwins/client). 
* [**Azure.Identidade.**](https://www.nuget.org/packages/Azure.Identity) Esta biblioteca fornece ferramentas para ajudar na autenticação contra o Azure.

Para obter um walk-through detalhado da utilização das APIs na prática, consulte o [*Tutorial: Código de uma aplicação para clientes*](tutorial-code.md). 

### <a name="net-sdk-usage-examples"></a>.NET SDK exemplos de utilização

Aqui estão algumas amostras de código que ilustram a utilização do .NET SDK.

Autenticar contra o serviço:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="DefaultAzureCredential_basic":::

[!INCLUDE [Azure Digital Twins: local credentials note](../../includes/digital-twins-local-credentials-note.md)] 

Faça o upload de um modelo:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="CreateModel":::

Modelos de listas:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="GetModels":::

Criar gémeos:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="CreateTwin_withHelper":::

Consultas gémeas e loop através de resultados:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/queries.cs" id="FullQuerySample":::

Consulte o [*Tutorial: Código de um aplicativo*](tutorial-code.md) de cliente para uma passagem deste código de aplicação de amostra. 

Também pode encontrar amostras adicionais no [repo GitHub para o .NET (C#) SDK](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core/samples).

#### <a name="serialization-helpers"></a>Ajudantes de serialização

Os ajudantes de serialização são funções de ajuda disponíveis dentro do SDK para criar ou deserizar rapidamente dados gémeos para acesso a informações básicas. Uma vez que os métodos SDK principais devolvem dados gémeos como JSON por padrão, pode ser útil usar estas classes de ajudantes para quebrar ainda mais os dados gémeos.

As aulas de ajudante disponível são:
* `BasicDigitalTwin`: Genericamente representa os dados fundamentais de um gémeo digital
* `BasicDigitalTwinComponent`: Genericamente representa um componente nas `Contents` propriedades de um `BasicDigitalTwin`
* `BasicRelationship`: Genericamente representa os dados fundamentais de uma relação
* `DigitalTwinsJsonPropertyName`: Contém as constantes de corda para utilização na serialização e deserialização de JSON para tipos de gémeos digitais personalizados

##### <a name="deserialize-a-digital-twin"></a>Deserializar um gémeo digital

Pode sempre desseeciar dados gémeos utilizando a biblioteca JSON à sua escolha, como `System.Text.Json` ou `Newtonsoft.Json` . Para o acesso básico a um gémeo, as aulas de ajudante podem tornar isto mais conveniente.

A `BasicDigitalTwin` classe de ajudante também lhe dá acesso a propriedades definidas no gémeo, através de um `Dictionary<string, object>` . Para listar as propriedades do gémeo, pode utilizar:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="GetTwin":::

> [!NOTE]
> `BasicDigitalTwin` usa `System.Text.Json` atributos. Para utilizar `BasicDigitalTwin` com o seu [DigitalTwinsClient,](/dotnet/api/azure.digitaltwins.core.digitaltwinsclient?view=azure-dotnet&preserve-view=true)deve rubricar o cliente com o construtor predefinido, ou, se quiser personalizar a opção serializadora, utilize o [JsonObjectSerializador](/dotnet/api/azure.core.serialization.jsonobjectserializer?view=azure-dotnet&preserve-view=true).

##### <a name="create-a-digital-twin"></a>Criar um gémeo digital

Utilizando a `BasicDigitalTwin` classe, pode preparar dados para criar uma instância dupla:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="CreateTwin_withHelper":::

O código acima é equivalente à seguinte variante "manual":

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="CreateTwin_noHelper":::

##### <a name="deserialize-a-relationship"></a>Deserializar uma relação

Pode sempre desseeciar dados de relacionamento a um tipo de escolha. Para acesso básico a uma relação, utilize o tipo `BasicRelationship` .

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="GetRelationshipsCall":::

A `BasicRelationship` classe de ajudante também lhe dá acesso a propriedades definidas na relação, através de um `IDictionary<string, object>` . Para listar propriedades, pode utilizar:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_other.cs" id="ListRelationshipProperties":::

##### <a name="create-a-relationship"></a>Criar uma relação

Utilizando a `BasicRelationship` classe, também pode preparar dados para criar relacionamentos num caso duplo:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_other.cs" id="CreateRelationship_short":::

##### <a name="create-a-patch-for-twin-update"></a>Crie um patch para atualização twin

A atualização de chamadas para gémeos e relacionamentos usam a estrutura [do JSON Patch.](http://jsonpatch.com/) Para criar listas de operações do JSON Patch, pode utilizar o `JsonPatchDocument` como mostrado abaixo.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="UpdateTwin":::

## <a name="general-apisdk-usage-notes"></a>Notas gerais de utilização da API/SDK

> [!NOTE]
> Por favor, note que a Azure Digital Twins não suporta atualmente **a Partilha de Recursos cross-Origin (CORS)**. Para obter mais informações sobre as estratégias de impacto e resolução, consulte a secção [*de Partilha de Recursos de Origem Cruzada (CORS)*](concepts-security.md#cross-origin-resource-sharing-cors) de *Conceitos: Segurança para as soluções Azure Digital Twins*.

A lista a seguir fornece detalhes adicionais e orientações gerais para a utilização das APIs e dos SDKs.

* Pode utilizar uma ferramenta de teste HTTP REST como o Carteiro para fazer chamadas diretas para as APIs de Gémeos Digitais Azure. Para obter mais informações sobre este processo, consulte [*Como fazer: Fazer pedidos com o Carteiro.*](how-to-use-postman.md)
* Para usar o SDK, instantânea a `DigitalTwinsClient` aula. O construtor requer credenciais que podem ser obtidas com uma variedade de métodos de autenticação na `Azure.Identity` embalagem. Para mais `Azure.Identity` informações, consulte a [documentação do espaço de nome.](/dotnet/api/azure.identity) 
* Pode encontrar o útil durante o `InteractiveBrowserCredential` arranque, mas existem várias outras opções, incluindo credenciais para [identidade gerida](/dotnet/api/azure.identity.interactivebrowsercredential), que provavelmente utilizará para autenticar [funções Azure configuradas com MSI](../app-service/overview-managed-identity.md?tabs=dotnet) contra Azure Digital Twins. Para mais informações `InteractiveBrowserCredential` sobre , consulte a sua [documentação de classe.](/dotnet/api/azure.identity.interactivebrowsercredential)
* Os pedidos às APIs das Gémeas Digitais Azure requerem um utilizador ou diretor de serviço que faça parte do mesmo inquilino do [Azure Ative Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) onde reside a instância Azure Digital Twins. Para evitar a digitalização maliciosa dos pontos finais da Azure Digital Twins, os pedidos com fichas de acesso de fora do inquilino originado serão devolvidos uma mensagem de erro "404 Sub-Domain não encontrada". Este erro será devolvido *mesmo que* o utilizador ou o diretor de serviço tenha recebido um papel de Azure Digital Twins Data Owner ou Azure Digital Twins Data Reader através da colaboração [Azure AD B2B.](../active-directory/external-identities/what-is-b2b.md) Para obter informações sobre como obter acesso a vários inquilinos, consulte [*Como escrever: Escrever código de autenticação de aplicações*](how-to-authenticate-client.md#authenticate-across-tenants).
* Todas as chamadas de API de serviço são expostas como funções de membro na `DigitalTwinsClient` classe.
* Todas as funções de serviço existem em versões sincronizadas e assíncronos.
* Todas as funções de serviço lançam uma exceção para qualquer estado de devolução de 400 ou mais. Certifique-se de embrulhar as chamadas numa `try` secção e apanhar pelo `RequestFailedExceptions` menos. Para saber mais sobre este tipo de exceção, consulte [aqui.](/dotnet/api/azure.requestfailedexception)
* A maioria dos métodos de serviço regressam `Response<T>` ou `Task<Response<T>>` (para as chamadas assíncronas), onde `T` está a classe de objeto de retorno para a chamada de serviço. A [`Response`](/dotnet/api/azure.response-1) classe encapsula a devolução do serviço e apresenta valores de retorno no seu `Value` campo.  
* Métodos de serviço com retorno de resultados paged `Pageable<T>` ou `AsyncPageable<T>` como resultados. Para saber mais sobre a `Pageable<T>` classe, consulte [aqui;](/dotnet/api/azure.pageable-1)para mais `AsyncPageable<T>` informações, consulte [aqui.](/dotnet/api/azure.asyncpageable-1)
* Pode iterar os resultados paged utilizando um `await foreach` loop. Para saber mais sobre este processo, consulte [aqui.](/archive/msdn-magazine/2019/november/csharp-iterating-with-async-enumerables-in-csharp-8)
* O SDK subjacente `Azure.Core` é. Consulte a documentação do [espaço de nomeS Azure](/dotnet/api/azure) para obter referência sobre a infraestrutura e tipos SDK.


Os métodos de serviço retornam objetos fortemente digitado sempre que possível. No entanto, uma vez que o Azure Digital Twins se baseia em modelos configurados pelo utilizador em tempo de execução (através de modelos DTDL enviados para o serviço), muitas APIs de serviço pegam e devolvem dados gémeos em formato JSON.

## <a name="monitor-api-metrics"></a>Monitorizar métricas de API

Métricas de API como pedidos, latência e taxa de insucesso podem ser vistas no [portal Azure](https://portal.azure.com/). 

A partir da página inicial do portal, procure a sua instância Azure Digital Twins para obter os seus detalhes. Selecione a opção **Métricas** no menu da instância Azure Digital Twins para apresentar a página *Métricas.*

:::image type="content" source="media/troubleshoot-metrics/azure-digital-twins-metrics.png" alt-text="Screenshot mostrando a página métrica para Azure Digital Twins":::

A partir daqui, você pode ver as métricas para o seu exemplo e criar vistas personalizadas.

## <a name="next-steps"></a>Passos seguintes

Veja como fazer pedidos diretos às APIs usando o Carteiro:
* [*Como fazer: Fazer pedidos com o Carteiro*](how-to-use-postman.md)

Ou, pratique utilizando o .NET SDK criando uma aplicação para clientes com este tutorial:
* [*Tutorial: Código de uma aplicação de cliente*](tutorial-code.md)
