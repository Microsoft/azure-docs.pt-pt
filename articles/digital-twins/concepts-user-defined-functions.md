---
title: Processamento de dados e funções definidas pelo usuário – gêmeos digital do Azure | Microsoft Docs
description: Visão geral do processamento de dados, correspondências e funções definidas pelo usuário com o gêmeos digital do Azure.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/03/2020
ms.openlocfilehash: 5b1c3e6dcd106e16ab07f707729f31ee1e269461
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/04/2020
ms.locfileid: "75660312"
---
# <a name="data-processing-and-user-defined-functions"></a>Processamento de dados e funções definidas pelo utilizador

O Azure digital gêmeos oferece recursos avançados de computação. Os desenvolvedores podem definir e executar funções personalizadas em mensagens de telemetria de entrada para enviar eventos para pontos de extremidade predefinidos.

## <a name="data-processing-flow"></a>Fluxo de processamento de dados

Depois que os dispositivos enviam dados de telemetria para o Azure digital gêmeos, os desenvolvedores podem processar dados em quatro fases: *validar*, *corresponder*, *computar*e *distribuir*.

[![o fluxo de processamento de dados do Azure digital gêmeos](media/concepts/digital-twins-data-processing-flow.png)](media/concepts/digital-twins-data-processing-flow.png#lightbox)

1. A fase validar transforma a mensagem de telemetria de entrada em um formato de [objeto de transferência de dados](https://docs.microsoft.com/aspnet/web-api/overview/data/using-web-api-with-entity-framework/part-5) comumente compreendido. Essa fase também executa a validação do dispositivo e do sensor.
1. A fase de correspondência localiza as funções apropriadas definidas pelo usuário a serem executadas. Os correspondentes predefinidos localizam as funções definidas pelo usuário com base no dispositivo, no sensor e nas informações de espaço da mensagem de telemetria de entrada.
1. A fase de computação executa as funções definidas pelo usuário correspondentes na fase anterior. Essas funções podem ler e atualizar valores computados em nós de grafo espaciais e podem emitir notificações personalizadas.
1. A fase de expedição roteia todas as notificações personalizadas da fase de computação para os pontos de extremidade definidos no grafo.

## <a name="data-processing-objects"></a>Objetos de processamento de dados

O processamento de dados no Azure digital gêmeos consiste em definir três objetos: *correspondências*, *funções definidas pelo usuário*e *atribuições de função*.

[![objetos de processamento de dados do Azure digital gêmeos](media/concepts/digital-twins-user-defined-functions.png)](media/concepts/digital-twins-user-defined-functions.png#lightbox)

### <a name="matchers"></a>Correspondências

Os correspondentes definem um conjunto de condições que avaliam as ações que ocorrem com base na telemetria do sensor de entrada. As condições para determinar a correspondência podem incluir propriedades do sensor, do dispositivo pai do sensor e do espaço pai do sensor. As condições são expressas como comparações em relação a um [caminho JSON](https://jsonpath.com/) , conforme descrito neste exemplo:

- Todos os sensores de **temperatura** de DataType representados pelo valor de cadeia de caracteres de escape `\"Temperature\"`
- Tendo `01` em sua porta
- Que pertencem a dispositivos com o **fabricante** da chave de propriedade estendida definido como o valor da cadeia de caracteres de escape `\"Contoso\"`
- Que pertencem a espaços do tipo especificado pela cadeia de caracteres de escape `\"Venue\"`
- Que são descendentes do **spaceid** pai `DE8F06CA-1138-4AD7-89F4-F782CC6F69FD`

```JSON
{
  "id": "23535afafd-f39b-46c0-9b0c-0dd3892a1c30",
  "name": "My custom matcher",
  "spaceId": "DE8F06CA-1138-4AD7-89F4-F782CC6F69FD",
  "description": "All sensors of datatype Temperature with 01 in their port that belong to devices with the extended property key Manufacturer set to the value Contoso and that belong to spaces of type Venue that are somewhere below space Id DE8F06CA-1138-4AD7-89F4-F782CC6F69FD",
  "conditions": [
    {
      "id": "43898sg43-e15a-4e9c-abb8-2gw464364",
      "target": "Sensor",
      "path": "$.dataType",
      "value": "\"Temperature\"",
      "comparison": "Equals"
    },
    {
      "id": "wt3th44-e15a-35sg-seg3-235wf3ga463",
      "target": "Sensor",
      "path": "$.port",
      "value": "01",
      "comparison": "Contains"
    },
    {
      "id": "735hs33-e15a-37jj-23532-db901d550af5",
      "target": "SensorDevice",
      "path": "$.properties[?(@.name == 'Manufacturer')].value",
      "value": "\"Contoso\"",
      "comparison": "Equals"
    },
    {
      "id": "222325-e15a-49fg-5744-463643644",
      "target": "SensorSpace",
      "path": "$.type",
      "value": "\"Venue\"",
      "comparison": "Equals"
    }
  ]
}
```

> [!IMPORTANT]
> - Os caminhos JSON diferenciam maiúsculas de minúsculas.
> - A carga JSON é a mesma que a carga retornada por:
>   - `/sensors/{id}?includes=properties,types` para o sensor.
>   - `/devices/{id}?includes=properties,types,sensors,sensorsproperties,sensorstypes` para o dispositivo pai do sensor.
>   - `/spaces/{id}?includes=properties,types,location,timezone` para o espaço pai do sensor.
> - As comparações diferenciam maiúsculas de minúsculas.

### <a name="user-defined-functions"></a>Funções definidas pelo utilizador

Uma função definida pelo usuário é uma função personalizada executada em um ambiente isolado do Azure digital gêmeos. As funções definidas pelo usuário têm acesso à mensagem de telemetria do sensor bruto, pois ela é recebida. As funções definidas pelo usuário também têm acesso ao grafo espacial e ao serviço de Dispatcher. Depois que a função definida pelo usuário é registrada em um grafo, uma correspondência (detalhada [acima](#matchers)) deve ser criada para especificar quando a função é executada. Por exemplo, quando o Azure digital gêmeos recebe uma nova telemetria de um determinado sensor, a função correspondente definida pelo usuário pode calcular uma média móvel das últimas leituras do sensor.

As funções definidas pelo usuário podem ser escritas em JavaScript. Os métodos auxiliares interagem com o grafo no ambiente de execução definido pelo usuário. Os desenvolvedores podem executar trechos de código personalizados em relação a mensagens de telemetria do sensor. Alguns exemplos:

- Defina o sensor lendo diretamente no objeto sensor dentro do grafo.
- Execute uma ação com base em leituras de sensor diferentes dentro de um espaço no grafo.
- Crie uma notificação quando determinadas condições forem atendidas para uma leitura de sensor de entrada.
- Anexe metadados do grafo à leitura do sensor antes de enviar uma notificação.

Para obter mais informações, consulte [como usar funções definidas pelo usuário](./how-to-user-defined-functions.md).

#### <a name="examples"></a>Exemplos

O [repositório do GitHub para o exemplo C# de gêmeos digital](https://github.com/Azure-Samples/digital-twins-samples-csharp/) contém alguns exemplos das funções definidas pelo usuário:
- [Essa função](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availabilityForTutorial.js) procura valores de dióxido de carbono, movimento e temperatura para determinar se um espaço está disponível com esses valores no intervalo. Os [tutoriais para o digital gêmeos](tutorial-facilities-udf.md) exploram essa função em mais detalhes. 
- [Essa função](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/multiplemotionsensors.js) procura dados de vários sensores de movimento e determina que o espaço estará disponível se nenhum deles detectar nenhum movimento. Você pode substituir facilmente a função definida pelo usuário usada no [início rápido](quickstart-view-occupancy-dotnet.md)ou nos [tutoriais](tutorial-facilities-setup.md), fazendo as alterações mencionadas na seção comentários do arquivo. 

### <a name="role-assignment"></a>Atribuição de função

As ações de uma função definida pelo usuário estão sujeitas ao [controle de acesso baseado em função](./security-role-based-access-control.md) do Azure digital gêmeos para proteger os dados no serviço. As atribuições de função definem quais funções definidas pelo usuário têm as permissões adequadas para interagir com o grafo espacial e suas entidades. Por exemplo, uma função definida pelo usuário pode ter a capacidade e permissão para *criar*, *ler*, *Atualizar*ou *excluir* dados do grafo em um determinado espaço. O nível de acesso de uma função definida pelo usuário é verificado quando a função definida pelo usuário solicita dados no grafo ou tenta uma ação. Para obter mais informações, consulte [controle de acesso baseado em função](./security-create-manage-role-assignments.md).

É possível que um correspondente dispare uma função definida pelo usuário que não tenha nenhuma atribuição de função. Nesse caso, a função definida pelo usuário não lê nenhum dado do grafo.

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre como rotear eventos e mensagens de telemetria para outros serviços do Azure, leia [eventos e mensagens de rota](./concepts-events-routing.md).

- Para saber mais sobre como criar correspondências, funções definidas pelo usuário e atribuições de função, leia o [guia para usar funções definidas pelo usuário](./how-to-user-defined-functions.md).

- Examine a [documentação de referência da biblioteca de cliente da função definida pelo usuário](./reference-user-defined-functions-client-library.md).
