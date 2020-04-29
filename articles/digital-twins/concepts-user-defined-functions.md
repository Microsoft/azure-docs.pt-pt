---
title: Processamento de dados e funções definidas pelo utilizador - Gémeas Digitais Azure Microsoft Docs
description: Visão geral do processamento de dados, matchers e funções definidas pelo utilizador com As Gémeas Digitais Azure.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/03/2020
ms.openlocfilehash: 75ed2029582438ede43687addfd54c0a187e0120
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79265184"
---
# <a name="data-processing-and-user-defined-functions"></a>Processamento de dados e funções definidas pelo utilizador

A Azure Digital Twins oferece capacidades avançadas de computação. Os desenvolvedores podem definir e executar funções personalizadas contra mensagens de telemetria para enviar eventos para pontos finais pré-definidos.

## <a name="data-processing-flow"></a>Fluxo de processamento de dados

Depois de os dispositivos enviarem dados de telemetria para as Gémeas Digitais Azure, os desenvolvedores podem processar dados em quatro fases: *validar,* *combinar,* *compute*e *despachar*.

[![Fluxo de processamento de dados de Gémeos Digitais Azure](media/concepts/digital-twins-data-processing-flow.png)](media/concepts/digital-twins-data-processing-flow.png#lightbox)

1. A fase de validação transforma a mensagem de telemetria de entrada num formato de objeto de transferência de [dados](https://docs.microsoft.com/aspnet/web-api/overview/data/using-web-api-with-entity-framework/part-5) comumente compreendido. Esta fase também executa a validação do dispositivo e do sensor.
1. A fase de jogo encontra as funções definidas pelo utilizador adequadas para serem executadas. Os matchers predefinidos encontram as funções definidas pelo utilizador com base no dispositivo, sensor e informações espaciais a partir da mensagem de telemetria que está a chegar.
1. A fase do cálculo executa as funções definidas pelo utilizador correspondidas na fase anterior. Estas funções podem ler e atualizar valores computorizados em nódos de gráficos espaciais e podem emitir notificações personalizadas.
1. A fase de despacho direciona quaisquer notificações personalizadas da fase do cálculo para pontos finais definidos no gráfico.

## <a name="data-processing-objects"></a>Objetos de processamento de dados

O processamento de dados em Gémeos Digitais Azure consiste na definição de três objetos: *matchers,* *funções definidas pelo utilizador*e atribuições de *papéis.*

[![Objetos de processamento de dados de Gémeos Digitais Azure](media/concepts/digital-twins-user-defined-functions.png)](media/concepts/digital-twins-user-defined-functions.png#lightbox)

### <a name="matchers"></a>Matchers

Os matchers definem um conjunto de condições que avaliam que ações ocorrem com base na telemetria do sensor de entrada. As condições para determinar a correspondência podem incluir propriedades do sensor, do dispositivo principal do sensor e do espaço dos pais do sensor. As condições são expressas como comparações com um [caminho JSON,](https://jsonpath.com/) tal como descrito neste exemplo:

- Todos os sensores de temperatura do **tipo** de dados representados pelo valor de corda escapado`\"Temperature\"`
- Ter `01` no seu porto
- Que pertencem a dispositivos com a chave de propriedade estendida **Fabricante** definido para o valor string escapado`\"Contoso\"`
- Que pertencem a espaços do tipo especificado pela Corda escapou`\"Venue\"`
- Que são descendentes do pai **SpaceId**`DE8F06CA-1138-4AD7-89F4-F782CC6F69FD`

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
> - Os caminhos da JSON são sensíveis ao caso.
> - A carga útil da JSON é a mesma que a carga útil que é devolvida por:
>   - `/sensors/{id}?includes=properties,types`para o sensor.
>   - `/devices/{id}?includes=properties,types,sensors,sensorsproperties,sensorstypes`para o dispositivo principal do sensor.
>   - `/spaces/{id}?includes=properties,types,location,timezone`para o espaço dos pais do sensor.
> - As comparações são insensíveis.

### <a name="user-defined-functions"></a>Funções definidas pelo utilizador

Uma função definida pelo utilizador é uma função personalizada executada dentro de um ambiente isolado das Gémeas Digitais Azure. As funções definidas pelo utilizador têm acesso à mensagem de telemetria de sensores brutos à medida que são recebidas. As funções definidas pelo utilizador também têm acesso ao serviço de gráfico sonante e deexpedidor. Depois de a função definida pelo utilizador estar registada dentro de um gráfico, deve ser criado um fósforo [(detalhado acima)](#matchers)para especificar quando a função é executada. Por exemplo, quando o Azure Digital Twins recebe uma nova telemetria a partir de um determinado sensor, a função definida pelo utilizador pode calcular uma média móvel das últimas leituras de sensores.

As funções definidas pelo utilizador podem ser escritas no JavaScript. Os métodos auxiliares interagem com o gráfico no ambiente de execução definido pelo utilizador. Os desenvolvedores podem executar fragmentos de código personalizados contra mensagens de telemetria de sensores. Os exemplos incluem:

- Coloque a leitura do sensor diretamente no objeto do sensor dentro do gráfico.
- Execute uma ação baseada em diferentes leituras de sensores dentro de um espaço no gráfico.
- Crie uma notificação quando determinadas condições forem satisfeitas para uma leitura do sensor de entrada.
- Fixe os metadados gráficos à leitura do sensor antes de enviar uma notificação.

Para mais informações, leia [Como utilizar funções definidas pelo utilizador](./how-to-user-defined-functions.md).

#### <a name="examples"></a>Exemplos

O [repo GitHub para a amostra Digital Twins C#](https://github.com/Azure-Samples/digital-twins-samples-csharp/) contém alguns exemplos das funções definidas pelo utilizador:
- [Esta função](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availabilityForTutorial.js) procura valores de dióxido de carbono, movimento e temperatura para determinar se uma sala está disponível com estes valores ao alcance. Os [tutoriais para Gémeos Digitais](tutorial-facilities-udf.md) exploram esta função em mais detalhes. 
- [Esta função](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/multiplemotionsensors.js) procura dados de múltiplos sensores de movimento, e determina que o espaço está disponível se nenhum deles detetar qualquer movimento. Pode substituir facilmente a função definida pelo utilizador utilizada no [quickstart](quickstart-view-occupancy-dotnet.md), ou nos [tutoriais,](tutorial-facilities-setup.md)fazendo as alterações mencionadas na secção de comentários do ficheiro. 

### <a name="role-assignment"></a>Atribuição de função

As ações de uma função definida pelo utilizador estão sujeitas ao [controlo de acesso baseado em funções](./security-role-based-access-control.md) da Azure Digital Twins para obter dados seguros dentro do serviço. As atribuições de funções definem quais as funções definidas pelo utilizador têm as permissões adequadas para interagir com o gráfico espacial e as suas entidades. Por exemplo, uma função definida pelo utilizador pode ter a capacidade e permissão para *CRIAR,* *LER,* *ATUALIZAR*ou *ELIMINAR* dados de gráficos num determinado espaço. O nível de acesso de uma função definida pelo utilizador é verificado quando a função definida pelo utilizador pede ao gráfico dados ou tenta uma ação. Para mais informações, leia o [controlo de acesso baseado em papéis.](./security-create-manage-role-assignments.md)

É possível que um matcher desencadeie uma função definida pelo utilizador que não tenha atribuições de funções. Neste caso, a função definida pelo utilizador não lê quaisquer dados do gráfico.

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre como encaminhar eventos e mensagens de telemetria para outros serviços do Azure, leia [eventos e mensagens da Route.](./concepts-events-routing.md)

- Para saber mais sobre como criar matchers, funções definidas pelo utilizador e atribuições de papéis, leia [Guia para utilizar funções definidas pelo utilizador](./how-to-user-defined-functions.md).

- Reveja a documentação de referência da biblioteca do [cliente de função definida pelo utilizador.](./reference-user-defined-functions-client-library.md)
