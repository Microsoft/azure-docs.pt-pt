---
title: Gerir duplos digitais
titleSuffix: Azure Digital Twins
description: Veja como recuperar, atualizar e eliminar gémeos e relacionamentos individuais.
author: baanders
ms.author: baanders
ms.date: 10/21/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 98b50673b464044af2a038fa93c3b6a022fa2899
ms.sourcegitcommit: 6776f0a27e2000fb1acb34a8dddc67af01ac14ac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103149708"
---
# <a name="manage-digital-twins"></a>Gerir duplos digitais

As entidades do seu ambiente são representadas por [gémeos digitais.](concepts-twins-graph.md) Gerir os seus gémeos digitais pode incluir criação, modificação e remoção. Para estas operações, pode utilizar as [**APIs DigitalTwins,**](/rest/api/digital-twins/dataplane/twins)a [.NET (C#) SDK,](/dotnet/api/overview/azure/digitaltwins/client)ou o [Azure Digital Twins CLI](how-to-use-cli.md).

Este artigo centra-se na gestão de gémeos digitais; para trabalhar com relacionamentos e o [gráfico gémeo](concepts-twins-graph.md) como um todo, ver [*Como-a- Gerir o gráfico gémeo com relacionamentos*](how-to-manage-graph.md).

> [!TIP]
> Todas as funções SDK vêm em versões sincronizadas e assíncronos.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="ways-to-manage-twins"></a>Formas de gerir gémeos

[!INCLUDE [digital-twins-ways-to-manage.md](../../includes/digital-twins-ways-to-manage.md)]

## <a name="create-a-digital-twin"></a>Criar um gémeo digital

Para criar um gémeo, utiliza-se o `CreateOrReplaceDigitalTwinAsync()` método no cliente de serviço como este:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="CreateTwinCall":::

Para criar um gémeo digital, é necessário fornecer:
* O iD desejado para o gémeo digital
* O [modelo](concepts-models.md) que quer usar

Opcionalmente, pode fornecer valores iniciais para todas as propriedades do twin digital. As propriedades são tratadas como opcionais e podem ser definidas mais tarde, mas **não aparecerão como parte de um gémeo até estarem definidas.**

>[!NOTE]
>Embora as propriedades gémeas não tenham de ser inicializadas, quaisquer componentes no **gémeo** precisam de ser [definidos](concepts-models.md#elements-of-a-model) quando o gémeo é criado. Podem ser objetos vazios, mas os próprios componentes devem existir.

O modelo e quaisquer valores de propriedade iniciais são fornecidos através do `initData` parâmetro, que é uma cadeia JSON contendo os dados relevantes. Para obter mais informações sobre a estruturação deste objeto, continue para a secção seguinte.

> [!TIP]
> Após a criação ou atualização de um gémeo, pode haver uma latência de até 10 segundos antes de as alterações se refletirem em [consultas](how-to-query-graph.md). A `GetDigitalTwin` API (descrita [mais tarde neste artigo)](#get-data-for-a-digital-twin)não experimenta este atraso, por isso, se precisar de uma resposta imediata, use a chamada da API em vez de consultar os seus gémeos recém-criados. 

### <a name="initialize-model-and-properties"></a>Inicializar modelo e propriedades

Pode inicializar as propriedades de um gémeo no momento em que o gémeo é criado. 

A API de criação gémea aceita um objeto que é serializado numa descrição válida do JSON das propriedades gémeas. Ver [*Conceitos: Gémeos digitais e o gráfico gémeo*](concepts-twins-graph.md) para uma descrição do formato JSON para um gémeo. 

Em primeiro lugar, pode criar um objeto de dados para representar o gémeo e os seus dados de propriedade. Pode criar um objeto de parâmetro manualmente ou utilizando uma classe de ajudante fornecida. Aqui está um exemplo de cada um.

#### <a name="create-twins-using-manually-created-data"></a>Criar gémeos usando dados criados manualmente

Sem o uso de quaisquer classes de ajudantes personalizadas, pode representar as propriedades de um gémeo `Dictionary<string, object>` num, onde `string` o nome da propriedade e o é um objeto que representa a propriedade e o seu `object` valor.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="CreateTwin_noHelper":::

#### <a name="create-twins-with-the-helper-class"></a>Criar gémeos com a classe de ajudante

A classe de ajudante `BasicDigitalTwin` permite-lhe armazenar campos de propriedade num objeto "twin" diretamente. Pode ainda querer construir a lista de propriedades utilizando um `Dictionary<string, object>` , que pode ser adicionado ao objeto gémeo como seu `CustomProperties` diretamente.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="CreateTwin_withHelper":::

>[!NOTE]
> `BasicDigitalTwin` objetos vêm com um `Id` campo. Pode deixar este campo vazio, mas se adicionar um valor de ID, tem de corresponder ao parâmetro de ID passado à `CreateOrReplaceDigitalTwinAsync()` chamada. Por exemplo:
>
>```csharp
>twin.Id = "myRoomId";
>```

## <a name="get-data-for-a-digital-twin"></a>Obtenha dados para um gémeo digital

Pode aceder aos detalhes de qualquer gémeo digital chamando o `GetDigitalTwin()` método como este:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="GetTwinCall":::

Esta chamada devolve dados gémeos como um tipo de objeto fortemente digitado, como `BasicDigitalTwin` . `BasicDigitalTwin` é uma classe de ajudante de serialização incluída com o SDK, que irá devolver os metadados e propriedades gémeos principais na forma pré-parsed. Aqui está um exemplo de como usar isto para ver detalhes gémeos:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="GetTwin" highlight="2":::

Apenas as propriedades que foram definidas pelo menos uma vez são devolvidas quando se recupera um gémeo com o `GetDigitalTwin()` método.

>[!TIP]
>O `displayName` para um gémeo faz parte dos seus metadados-modelo, pelo que não vai mostrar quando se obter dados para a instância gémea. Para ver este valor, pode [recuperá-lo do modelo.](how-to-manage-model.md#retrieve-models)

Para recuperar vários gémeos utilizando uma única chamada de API, consulte os exemplos de consulta da API em [*Como-a-: Consultar o gráfico gémeo*](how-to-query-graph.md).

Considere o seguinte modelo (escrito em [Linguagem de Definição de Gémeos Digitais (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/tree/master/DTDL)que define uma *Lua*:

:::code language="json" source="~/digital-twins-docs-samples/models/Moon.json":::

O resultado de chamar `object result = await client.GetDigitalTwinAsync("my-moon");` um gémeo do tipo *Lua* pode ser assim:

```json
{
  "$dtId": "myMoon-001",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "radius": 1737.1,
  "mass": 0.0734,
  "$metadata": {
    "$model": "dtmi:example:Moon;1",
    "radius": {
      "desiredValue": 1737.1,
      "desiredVersion": 5,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "OK"
    },
    "mass": {
      "desiredValue": 0.0734,
      "desiredVersion": 8,
      "ackVersion": 8,
      "ackCode": 200,
      "ackDescription": "OK"
    }
  }
}
```

As propriedades definidas do gémeo digital são devolvidas como propriedades de alto nível no twin digital. Os metadados ou informações do sistema que não fazem parte da definição DTDL são devolvidos com um `$` prefixo. As propriedades dos metadados incluem os seguintes valores:
* `$dtId`: A ID do gémeo digital neste exemplo de Azure Digital Twins
* `$etag`: Um campo HTTP padrão atribuído pelo servidor web. Isto é atualizado para um novo valor cada vez que o twin é atualizado, o que pode ser útil para determinar se os dados do twin foram atualizados no servidor desde uma verificação anterior. Também pode ser usado em cabeçalhos HTTP desta forma:
  - com operações de leitura para evitar o conteúdo que não mudou
  - com operações de escrita para apoiar a concordância otimista
* `$metadata`: Um conjunto de outras propriedades, incluindo:
  - O DTMI do modelo do gémeo digital.
  - Estado de sincronização para cada propriedade escrita. Isto é mais útil para dispositivos, onde é possível que o serviço e o dispositivo tenham estatutos divergentes (por exemplo, quando um dispositivo está offline). Atualmente, esta propriedade aplica-se apenas a dispositivos físicos ligados ao IoT Hub. Com os dados na secção de metadados, é possível compreender o estado total de uma propriedade, bem como os últimos timetamps modificados. Para obter mais informações sobre o estado de sincronização, consulte [este tutorial do IoT Hub](../iot-hub/tutorial-device-twins.md) sobre o estado do dispositivo sincronizado.
  - Metadados específicos do serviço, como do IoT Hub ou da Azure Digital Twins. 

Pode ler mais sobre as aulas de ajudantes de serialização, como `BasicDigitalTwin` em [*How-to: Use as APIs e SDKs das Gémeas Digitais Azure*](how-to-use-apis-sdks.md).

## <a name="view-all-digital-twins"></a>Ver todos os gémeos digitais

Para ver todos os gémeos digitais no seu caso, use uma [consulta.](how-to-query-graph.md) Pode fazer uma consulta com as [APIs de Consulta](/rest/api/digital-twins/dataplane/query) ou os [comandos CLI](how-to-use-cli.md).

Aqui está o corpo da consulta básica que devolverá uma lista de todos os gémeos digitais no caso:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="GetAllTwins":::

## <a name="update-a-digital-twin"></a>Atualize um gémeo digital

Para atualizar as propriedades de um gémeo digital, escreve as informações que pretende substituir no formato [JSON Patch.](http://jsonpatch.com/) Desta forma, pode substituir várias propriedades ao mesmo tempo. Em seguida, passe o documento do Patch JSON para um `UpdateDigitalTwin()` método:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="UpdateTwinCall":::

Uma chamada de patch pode atualizar todas as propriedades de um único gémeo quanto você gostaria (mesmo todas elas). Se precisar de atualizar propriedades em vários gémeos, precisará de uma chamada de atualização separada para cada gémeo.

> [!TIP]
> Após a criação ou atualização de um gémeo, pode haver uma latência de até 10 segundos antes de as alterações se refletirem em [consultas](how-to-query-graph.md). A `GetDigitalTwin` API (descrita [anteriormente neste artigo)](#get-data-for-a-digital-twin)não experimenta este atraso, por isso use a chamada da API em vez de consultar os seus gémeos recém-atualizados se precisar de uma resposta imediata. 

Aqui está um exemplo do código de patch JSON. Este documento substitui os *valores* de propriedade em massa e *raio* do gémeo digital a que é aplicado.

:::code language="json" source="~/digital-twins-docs-samples/models/patch.json":::

Pode criar patches utilizando o [JsonPatchDocument](/dotnet/api/azure.jsonpatchdocument)do Azure .NET SDK . Eis um exemplo.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="UpdateTwin":::

### <a name="update-properties-in-digital-twin-components"></a>Atualizar propriedades em componentes gémeos digitais

Recorde-se que um modelo pode conter componentes, permitindo que seja composto por outros modelos. 

Para corrigir propriedades nos componentes de um gémeo digital, pode utilizar a sintaxe do caminho no JSON Patch:

:::code language="json" source="~/digital-twins-docs-samples/models/patch-component.json":::

### <a name="update-a-digital-twins-model"></a>Atualize o modelo de um gémeo digital

A `UpdateDigitalTwin()` função também pode ser usada para migrar um gémeo digital para um modelo diferente. 

Por exemplo, considere o seguinte documento JSON Patch que substitui o campo de metadados do gémeo `$model` digital:

:::code language="json" source="~/digital-twins-docs-samples/models/patch-model-1.json":::

Esta operação só será bem sucedida se o gémeo digital for modificado pelo patch em conformidade com o novo modelo. 

Considere o exemplo seguinte:
1. Imagine um gémeo digital com um modelo de *foo_old.* *foo_old* define uma *massa* de propriedade necessária.
2. O novo modelo *foo_new* define uma massa de propriedade, e adiciona uma nova *temperatura* de propriedade necessária.
3. Após o patch, o gémeo digital deve ter uma propriedade de massa e temperatura. 

O patch para esta situação precisa atualizar tanto o modelo como a propriedade de temperatura do gémeo, como este:

:::code language="json" source="~/digital-twins-docs-samples/models/patch-model-2.json":::

### <a name="handle-conflicting-update-calls"></a>Lidar com chamadas de atualização conflituosas

A Azure Digital Twins garante que todos os pedidos de entrada são processados um após o outro. Isto significa que mesmo que várias funções tentem atualizar a mesma propriedade em um gémeo ao mesmo tempo, não há **necessidade de** escrever código de bloqueio explícito para lidar com o conflito.

Este comportamento é por gémeo. 

Como exemplo, imagine um cenário em que estas três chamadas cheguem ao mesmo tempo: 
*   Escreva propriedade A em *Twin1*
*   Escreva propriedade B em *Twin1*
*   Escreva propriedade A em *Twin2*

As duas chamadas que modificam *o Twin1* são executadas uma após a outra e as mensagens de alteração são geradas para cada mudança. O apelo para modificar *o Twin2* pode ser executado simultaneamente sem qualquer conflito, assim que chegar.

## <a name="delete-a-digital-twin"></a>Excluir um gémeo digital

Pode eliminar gémeos utilizando o `DeleteDigitalTwin()` método. No entanto, só se pode apagar um gémeo quando não tem mais relacionamentos. Então, apaguem primeiro as relações de entrada e saída do gémeo.

Aqui está um exemplo do código para apagar gémeos e suas relações. A `DeleteDigitalTwin` chamada SDK é destacada para clarificar onde se enquadra no contexto de exemplo mais amplo.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="DeleteTwin" highlight="7":::

### <a name="delete-all-digital-twins"></a>Apagar todos os gémeos digitais

Para um exemplo de como eliminar todos os gémeos de uma só vez, descarregue a aplicação de amostras utilizada no [*Tutorial: Explore o básico com uma aplicação de cliente de amostra.*](tutorial-command-line-app.md) O ficheiro *.cs CommandLoop* faz isto numa `CommandDeleteAllTwins()` função.

## <a name="runnable-digital-twin-code-sample"></a>Amostra de código duplo digital runnable

Pode utilizar a amostra de código runable abaixo para criar um gémeo, atualizar os seus dados e eliminar o gémeo. 

### <a name="set-up-the-runnable-sample"></a>Configurar a amostra runnable

O snippet usa o [Room.jsna](https://github.com/Azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Room.json) definição de modelo a partir de [*Tutorial: Explore Azure Digital Twins com uma aplicação de cliente de amostra.*](tutorial-command-line-app.md) Pode utilizar este link para ir diretamente ao ficheiro ou descarregá-lo como parte do projeto de amostra completa de ponta a [ponta aqui](/samples/azure-samples/digital-twins-samples/digital-twins-samples/).

Antes de executar a amostra, faça o seguinte:
1. Descarregue o ficheiro do modelo, coloque-o no seu projeto e substitua o `<path-to>` espaço reservado no código abaixo para dizer ao seu programa onde encontrá-lo.
2. Substitua o espaço reservado `<your-instance-hostname>` pelo nome de anfitrião da sua instância Azure Digital Twins.
3. Adicione duas dependências ao seu projeto que serão necessárias para trabalhar com a Azure Digital Twins. O primeiro é o pacote para o [Azure Digital Twins SDK para .NET](/dotnet/api/overview/azure/digitaltwins/client), o segundo fornece ferramentas para ajudar na autenticação contra o Azure.

      ```cmd/sh
      dotnet add package Azure.DigitalTwins.Core
      dotnet add package Azure.Identity
      ```

Também terá de configurar credenciais locais se quiser executar a amostra diretamente. A próxima secção passa por isto.
[!INCLUDE [Azure Digital Twins: local credentials prereq (outer)](../../includes/digital-twins-local-credentials-outer.md)]

### <a name="run-the-sample"></a>Executar o exemplo

Depois de completar os passos acima, pode executar diretamente o seguinte código de amostra.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs":::

Aqui está a saída da consola do programa acima: 

:::image type="content" source="./media/how-to-manage-twin/console-output-manage-twins.png" alt-text="Saída da consola mostrando que o gémeo é criado, atualizado e eliminado" lightbox="./media/how-to-manage-twin/console-output-manage-twins.png":::

## <a name="next-steps"></a>Passos seguintes

Veja como criar e gerir relações entre os seus gémeos digitais:
* [*Como fazer: Gerir o gráfico gémeo com relacionamentos*](how-to-manage-graph.md)