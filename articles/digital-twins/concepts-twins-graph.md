---
title: Digital Twins e o grafo duplo
titleSuffix: Azure Digital Twins
description: Entenda o conceito de gémeo digital, e como as suas relações fazem um gráfico.
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: a1fc5be93e2b9729838aa9fb3a777936003c5f45
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93356404"
---
# <a name="understand-digital-twins-and-their-twin-graph"></a>Compreenda os gémeos digitais e o seu gráfico gémeo

Numa solução Azure Digital Twins, as entidades do seu ambiente são representadas por **gémeos digitais** Azure. Um gémeo digital é um exemplo de um dos seus [modelos personalizados.](concepts-models.md) Pode ser conectado a outros gémeos digitais através de **relações** para formar um **gráfico gémeo** : este gráfico gémeo é a representação de todo o seu ambiente.

> [!TIP]
> "Azure Digital Twins" refere-se a este serviço Azure como um todo. "Digital twin(s)" ou apenas "twin(s)" refere-se a nódoas gémeas individuais dentro do seu caso do serviço.

## <a name="digital-twins"></a>Digital Twins

Antes de criar um gémeo digital na sua instância Azure Digital Twins, precisa de ter um *modelo* carregado para o serviço. Um modelo descreve o conjunto de propriedades, mensagens de telemetria e relacionamentos que um gémeo em particular pode ter, entre outras coisas. Para os tipos de informação que são definidos num modelo, consulte [*Conceitos: Modelos personalizados.*](concepts-models.md)

Depois de criar e carregar um modelo, a sua aplicação de clientes pode criar uma instância do tipo; este é um gémeo digital. Por exemplo, depois de criar um modelo de *Floor,* pode criar um ou vários gémeos digitais que usam este tipo (como um twin tipo *chão* chamado *GroundFloor* , outro chamado *Floor2* , etc.). 

## <a name="relationships-a-graph-of-digital-twins"></a>Relacionamentos: um gráfico de gémeos digitais

Os gémeos estão ligados a um gráfico gémeo pelas suas relações. As relações que um gémeo pode ter são definidas como parte do seu modelo.  

Por exemplo, o modelo *Floor* pode definir uma relação *de contenção* que visa gémeos de *tipo quarto*. Com esta definição, a Azure Digital Twins permitirá criar relações *de* qualquer *nó* gémeo do Chão a *qualquer* quarto gémeo (incluindo gémeos que são de subtipos *de Quarto).* 

O resultado deste processo é um conjunto de nós (os gémeos digitais) ligados através de bordas (suas relações) num gráfico.

[!INCLUDE [visualizing with Azure Digital Twins explorer](../../includes/digital-twins-visualization.md)]

## <a name="create-with-the-apis"></a>Criar com as APIs

Esta secção mostra o que parece criar gémeos digitais e relacionamentos a partir de uma aplicação de cliente. Contém exemplos de código .NET que utilizam as [APIs DigitalTwins,](/rest/api/digital-twins/dataplane/twins)para fornecer contexto adicional sobre o que se passa dentro de cada um destes conceitos.

### <a name="create-digital-twins"></a>Criar gémeos digitais

Abaixo está um corte de código de cliente que usa as [APIs DigitalTwins](/rest/api/digital-twins/dataplane/twins) para instantaneaizar um twin de *tipo Room*.

Pode inicializar as propriedades de um gémeo quando é criado, ou defini-las mais tarde. Para criar um gémeo com propriedades inicializadas, crie um documento JSON que forneça os valores de inicialização necessários.

[!INCLUDE [Azure Digital Twins code: create twin](../../includes/digital-twins-code-create-twin.md)]

Você também pode usar uma classe de ajudante chamada `BasicDigitalTwin` para armazenar campos de propriedade em um objeto "twin" mais diretamente, como uma alternativa para usar um dicionário. Para obter mais informações sobre a classe de ajudante e exemplos da sua utilização, consulte a secção [*Digital Twin*](how-to-manage-twin.md#create-a-digital-twin) de *How-to: Manage digital twins*.

>[!NOTE]
>Enquanto as propriedades gémeas são tratadas como opcionais e, portanto, não têm de ser inicializadas, quaisquer componentes no **twin** precisam de ser [definidos](concepts-models.md#elements-of-a-model) quando o gémeo é criado. Podem ser objetos vazios, mas os próprios componentes devem existir.

### <a name="create-relationships"></a>Criar relacionamentos

Aqui está um código de cliente exemplo que usa as [APIs DigitalTwins](/rest/api/digital-twins/dataplane/twins) para construir uma relação entre um twin digital tipo *Floor* chamado *GroundFloor* e um twin digital *tipo Quarto* chamado *Café.*

```csharp
// Create Twins, using functions similar to the previous sample
await CreateRoom("Cafe", 70, 66);
await CreateFloor("GroundFloor", averageTemperature=70);
// Create relationships
var relationship = new BasicRelationship
{
    TargetId = "Cafe",
    Name = "contains"
};
try
{
    string relId = $"GroundFloor-contains-Cafe";
    await client.CreateOrReplaceRelationshipAsync<BasicRelationship>("GroundFloor", relId, relationship);
} catch(ErrorResponseException e)
{
    Console.WriteLine($"*** Error creating relationship: {e.Response.StatusCode}");
}
```

## <a name="json-representations-of-graph-elements"></a>Representações de JSON de elementos gráficos

Os dados digitais de gémeos e de relacionamento são armazenados no formato JSON. Isto significa que quando consultar [o gráfico gémeo](how-to-query-graph.md) no seu exemplo de Azure Digital Twins, o resultado será uma representação JSON de gémeos digitais e relações que criou.

### <a name="digital-twin-json-format"></a>Formato JSON twin digital

Quando representado como um objeto JSON, um gémeo digital apresentará os seguintes campos:

| Nome do campo | Descrição |
| --- | --- |
| `$dtId` | Uma cadeia fornecida pelo utilizador que representa o ID do gémeo digital |
| `$etag` | Campo HTTP padrão atribuído pelo servidor web |
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
| `{componentName}.$metadata` | A informação de metadados para o componente, semelhante ao nível de raiz `$metadata` |

Aqui está um exemplo de um gémeo digital formatado como um objeto JSON:

```json
{
  "$dtId": "Cafe",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "Temperature": 72,
  "Location": {
    "x": 101,
    "y": 33
  },
  "component": {
    "TableOccupancy": 1,
    "$metadata": {
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
| `$relationshipId` | Uma cadeia fornecida pelo utilizador que representa a identificação desta relação. Esta cadeia é única no contexto da fonte digital twin, o que também significa que `sourceId`  +  `relationshipId` é única no contexto da instância Azure Digital Twins. |
| `$etag` | Campo HTTP padrão atribuído pelo servidor web |
| `$sourceId` | O ID da fonte digital twin |
| `$targetId` | A ID do gémeo digital alvo |
| `$relationshipName` | O nome da relação |
| `{propertyName}` | [Opcional] O valor de uma propriedade desta relação, em JSON `string` (, tipo de número, ou objeto) |

Aqui está um exemplo de uma relação formatada como um objeto JSON:

```json
{
  "$relationshipId": "relationship-01",
  "$etag": "W/\"506e8391-2b21-4ac9-bca3-53e6620f6a90\"",
  "$sourceId": "GroundFloor",
  "$targetId": "Cafe",
  "$relationshipName": "contains",
  "startDate": "2020-02-04"
}
```

## <a name="next-steps"></a>Próximos passos

Veja como gerir elementos gráficos com APIs Azure Digital Twin:
* [*Como fazer: Gerir gémeos digitais*](how-to-manage-twin.md)
* [*Como fazer: Gerir o gráfico gémeo com relacionamentos*](how-to-manage-graph.md)

Ou, saiba se consulta o gráfico gémeo Azure Digital Twins para obter informações:
* [*Conceitos: Linguagem de consulta*](concepts-query-language.md)