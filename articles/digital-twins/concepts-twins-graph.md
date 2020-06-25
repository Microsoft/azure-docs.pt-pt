---
title: Digital Twins e o grafo duplo
titleSuffix: Azure Digital Twins
description: Entenda o conceito de gémeo digital, e como as suas relações fazem um gráfico.
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: conceptual
ms.service: digital-twins
ROBOTS: NOINDEX, NOFOLLOW
ms.openlocfilehash: 79035a2fa2f7f3bed4890faa749661f025cea312
ms.sourcegitcommit: f98ab5af0fa17a9bba575286c588af36ff075615
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/25/2020
ms.locfileid: "85362871"
---
# <a name="understand-digital-twins-and-their-twin-graph"></a>Compreenda os gémeos digitais e o seu gráfico gémeo

[!INCLUDE [Azure Digital Twins current preview status](../../includes/digital-twins-preview-status.md)]

Numa solução Azure Digital Twins, as entidades do seu ambiente são representadas por **gémeos digitais**Azure. Um gémeo digital é um exemplo de um dos seus [modelos personalizados.](concepts-models.md) Pode ser conectado a outros gémeos digitais através de **relações** para formar um **gráfico gémeo**: este gráfico gémeo é a representação de todo o seu ambiente.

> [!TIP]
> "Azure Digital Twins" refere-se a este serviço Azure como um todo. "Digital twin(s)" ou apenas "twin(s)" refere-se a nódoas gémeas individuais dentro do seu caso do serviço.

## <a name="creating-digital-twins"></a>Criação de gémeos digitais

Antes de criar um gémeo digital na sua instância Azure Digital Twins, precisa de ter um *modelo* carregado para o serviço. Um modelo descreve o conjunto de propriedades, mensagens de telemetria e relacionamentos que um gémeo em particular pode ter, entre outras coisas. Para os tipos de informação que são definidos num modelo, consulte [Conceitos: Modelos personalizados.](concepts-models.md)

Depois de criar e carregar um modelo, a sua aplicação de clientes pode criar uma instância do tipo; este é um gémeo digital. Por exemplo, depois de criar um modelo de *Floor,* pode criar um ou vários gémeos digitais que usam este tipo (como um twin tipo *chão*chamado *GroundFloor*, outro chamado *Floor2*, etc.). 

Abaixo está um corte de código de cliente que usa as [APIs DigitalTwins](how-to-use-apis-sdks.md) para instantaneaizar um twin de *tipo Room*.

Na pré-visualização atual do Azure Digital Twins, todas as propriedades de um gémeo devem ser inicializadas antes de o gémeo poder ser criado. Isto é feito através da criação de um documento JSON que fornece os valores de inicialização necessários.

```csharp
public Task<boolean> CreateRoom(string id, double temperature, double humidity) 
{
    // Define the model for the twin to be created
    Dictionary<string, object> meta = new Dictionary<string, object>()
    {
      { "$model", "dtmi:com:contoso:Room;2" }
    };
    // Initialize the twin properties
    Dictionary<string, object> initData = new Dictionary<string, object>()
    {
      { "$metadata", meta },
      { "Temperature", temperature},
      { "Humidity", humidity},
    };
    try
    {
      await client.DigitalTwins.AddAsync(id, initData);
      return true;
    }
    catch (ErrorResponseException e)
    {
      Console.WriteLine($"*** Error creating twin {id}: {e.Response.StatusCode}");
      return false;
    }
}
```

## <a name="relationships-creating-a-graph-of-digital-twins"></a>Relacionamentos: Criar um gráfico de gémeos digitais

Os gémeos estão ligados a um gráfico gémeo pelas suas relações. As relações que um gémeo pode ter são definidas como parte do seu modelo.  

Por exemplo, o modelo *Floor* pode definir uma relação *de contenção* que visa gémeos de *tipo quarto*. Com esta definição, a Azure Digital Twins permitirá criar relações *de* qualquer *nó* gémeo do Chão a *qualquer* quarto gémeo (incluindo gémeos que são de subtipos *de Quarto).* 

Aqui está um código de cliente exemplo que usa as [APIs DigitalTwins](how-to-use-apis-sdks.md) para construir uma relação entre um twin digital tipo *Floor*chamado *GroundFloor* e um twin digital *tipo Quarto*chamado *Café.*

```csharp
// Create Twins, using functions similar to the previous sample
await CreateRoom("Cafe", 70, 66);
await CreateFloor("GroundFloor", averageTemperature=70);
// Create relationships
Dictionary<string, object> targetrec = new Dictionary<string, object>()
{
    { "$targetId", "Cafe" }
};
try
{
    await client.DigitalTwins.AddEdgeAsync("GroundFloor", "contains", "GF-to-Cafe", targetrec);
} catch(ErrorResponseException e)
{
    Console.WriteLine($"*** Error creating relationship: {e.Response.StatusCode}");
}
```

O resultado deste processo é um conjunto de nós (os gémeos digitais) ligados através de bordas (suas relações) num gráfico.

## <a name="json-representations-of-graph-elements"></a>Representações de JSON de elementos gráficos

Os dados digitais de gémeos e de relacionamento são armazenados no formato JSON. Isto significa que quando consultar [o gráfico gémeo](how-to-query-graph.md) no seu exemplo de Azure Digital Twins, o resultado será uma representação JSON de gémeos digitais e relações que criou.

### <a name="digital-twin-json-format"></a>Formato JSON twin digital

Quando representado como um objeto JSON, um gémeo digital apresentará os seguintes campos:

| Nome do campo | Descrição |
| --- | --- |
| `$dtId` | Uma cadeia fornecida pelo utilizador que representa o ID do gémeo digital |
| `$conformance` | Um enum que contém o estado de conformidade deste gémeo digital *(conformante,* *não-conformante,* *desconhecido)* |
| `{propertyName}` | O valor de um imóvel em JSON `string` (, tipo de número ou objeto) |
| `$relationships` | A URL do caminho para a coleção de relacionamentos. Este campo está ausente se o gémeo digital não tiver limites de relacionamento. |
| `$metadata.$model` | [Opcional] O ID da interface de modelo que caracteriza este gémeo digital |
| `$metadata.{propertyName}.desiredValue` | [Apenas para propriedades writable] O valor desejado do imóvel especificado |
| `$metadata.{propertyName}.desiredVersion` | [Apenas para propriedades writable] A versão do valor desejado |
| `$metadata.{propertyName}.ackVersion` | A versão reconhecida pela aplicação do dispositivo implementando o twin digital |
| `$metadata.{propertyName}.ackCode` | [Apenas para propriedades writable] O `ack` código devolvido pela aplicação do dispositivo implementando o twin digital |
| `$metadata.{propertyName}.ackDescription` | [Apenas para propriedades writable] A `ack` descrição devolvida pela aplicação do dispositivo implementando o twin digital |
| `{componentName}` | Um objeto JSON contendo os valores de propriedade e metadados do componente, semelhantes aos do objeto raiz. Este objeto existe mesmo que o componente não tenha propriedades. |
| `{componentName}.{propertyName}` | O valor da propriedade do componente em JSON `string` (, tipo de número ou objeto) |
| `{componentName}.$metadata` | A informação de metadados para o componente, semelhante ao nível de raiz`$metadata` |

Aqui está um exemplo de um gémeo digital formatado como um objeto JSON:

```json
{
  "$dtId": "Cafe",
  "Temperature": 72,
  "Location": {
    "x": 101,
    "y": 33
  },
  "component": {
    "TableOccupancy": 1,
    "$metadata": {
      "$model": "dtmi:com:contoso:Table;1",
      "TableOccupancy": {
        "desiredValue": 1,
        "desiredVersion": 3,
        "ackVersion": 2,
        "ackCode": 200,
        "ackDescription": "OK"
      }
    }
  },
  "$metadata": {
    "$model": "dtmi:com:contoso:Room;1",
    "Temperature": {
      "desiredValue": 72,
      "desiredVersion": 5,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "OK"
    },
    "Location": {
      "desiredValue": {
        "x": 101,
        "y": 33,
      },
      "desiredVersion": 8,
      "ackVersion": 8,
      "ackCode": 200,
      "ackDescription": "OK"
    }
  }
}
```

### <a name="relationship-json-format"></a>Relacionamento JSON formato

Quando representado como um objeto JSON, uma relação de um gémeo digital mostrará os seguintes campos:

| Nome do campo | Descrição |
| --- | --- |
| `$edgeId` | Uma cadeia fornecida pelo utilizador que representa o ID desta borda de relacionamento. Esta cadeia é única no contexto da fonte digital twin, o que também significa que `sourceId`  +  `edgeId` é única no contexto da instância Azure Digital Twins. |
| `$sourceId` | O ID da fonte digital twin |
| `$targetId` | A ID do gémeo digital alvo |
| `$relationshipName` | O nome da relação |
| `{propertyName}` | [Opcional] O valor de uma propriedade desta relação, em JSON `string` (, tipo de número, ou objeto) |

Aqui está um exemplo de uma relação formatada como um objeto JSON:

```json
{
  "$edgeId": "Edge-01",
  "$sourceId": "GroundFloor",
  "$relationship": "contains",
  "$targetId": "Cafe",
  "startDate": "2020-02-04"
}
```

## <a name="next-steps"></a>Passos seguintes

Veja como gerir elementos gráficos com APIs Azure Digital Twin:
* [Como fazer: Gerir gémeos digitais](how-to-manage-twin.md)
* [Como fazer: Gerir o gráfico gémeo com relacionamentos](how-to-manage-graph.md)

Ou, saiba se consulta o gráfico gémeo Azure Digital Twins para obter informações:
* [Conceitos: Linguagem de consulta](concepts-query-language.md)