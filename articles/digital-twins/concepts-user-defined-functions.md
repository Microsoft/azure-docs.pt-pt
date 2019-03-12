---
title: Processamento de dados e funções definidas pelo utilizador duplos Digital do Azure | Documentos da Microsoft
description: Descrição geral do processamento de dados, matchers e funções definidas pelo utilizador duplos Digital do Azure.
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: alinast
ms.openlocfilehash: 4db515a931bc7f423eb11ae31b7304a602f0da46
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57531747"
---
# <a name="data-processing-and-user-defined-functions"></a>Processamento de dados e funções definidas pelo utilizador

Duplos Digital do Azure oferece capacidades de computação avançada. Os desenvolvedores podem definir e executar funções personalizadas em mensagens de telemetria de entrada para enviar eventos para pontos finais predefinidos.

## <a name="data-processing-flow"></a>Fluxo de processamento de dados

Depois dos dispositivos enviam dados telemétricos para Twins Digital do Azure, os desenvolvedores podem processar os dados em quatro fases: *validar*, *corresponde ao*, *computação*, e *expedição* .

![Fluxo de processamento de dados digitais duplos do Azure][1]

1. A fase de validar transforma a mensagem de telemetria de entrada para um normalmente compreendida [objeto de transferência de dados](https://docs.microsoft.com/aspnet/web-api/overview/data/using-web-api-with-entity-framework/part-5) formato. Esta fase também executa a validação de dispositivos e sensores.
1. A fase de correspondência localiza as funções definidas pelo utilizador apropriadas para executar. Matchers predefinidos encontrar as funções definidas pelo utilizador com base em informações de espaço de mensagem de telemetria de entrada, sensores e dispositivos.
1. A fase de computação executa as funções definidas pelo utilizador correspondidas na fase anterior. Estas funções podem ler e atualizar valores computados geográficos em nós de gráfico e pode emitir notificações personalizadas.
1. A fase de expedição encaminha qualquer notificações personalizadas da fase de computação para pontos finais definidos no gráfico.

## <a name="data-processing-objects"></a>Objetos de processamento de dados

Processamento de dados no duplos Digital do Azure consiste em definir três objetos: *matchers*, *funções definidas pelo utilizador*, e *atribuições de funções*.

![Objetos de processamento de dados digitais duplos do Azure][2]

<div id="matcher"></div>

### <a name="matchers"></a>Matchers

Matchers definir um conjunto de condições que avaliar quais ações com base na telemetria de sensores de entrada. Condições para determinar a correspondência podem incluir as propriedades do sensor, o dispositivo de principal do sensor e o espaço de principal do sensor. As condições são expressas como comparações contra um [caminho JSON](https://jsonpath.com/) conforme descrito neste exemplo:

- Todos os sensores do tipo de dados **temperatura** representado pelo valor de cadeia de caracteres de escape `\"Temperature\"`
- Ter `01` na respetiva porta
- Que pertencem a dispositivos com a chave da propriedade expandida **fabricante** definido para o valor de cadeia de caracteres de escape `\"GoodCorp\"`
- Que pertencem aos espaços do tipo especificado pela cadeia de caracteres de escape `\"Venue\"`
- Quais são os descendentes de principal **SpaceId** `DE8F06CA-1138-4AD7-89F4-F782CC6F69FD`

```JSON
{
  "SpaceId": "DE8F06CA-1138-4AD7-89F4-F782CC6F69FD",
  "Name": "My custom matcher",
  "Description": "All sensors of datatype Temperature with 01 in their port that belong to devices with the extended property key Manufacturer set to the value GoodCorp and that belong to spaces of type Venue that are somewhere below space Id DE8F06CA-1138-4AD7-89F4-F782CC6F69FD",
  "Conditions": [
    {
      "target": "Sensor",
      "path": "$.dataType",
      "value": "\"Temperature\"",
      "comparison": "Equals"
    },
    {
      "target": "Sensor",
      "path": "$.port",
      "value": "01",
      "comparison": "Contains"
    },
    {
      "target": "SensorDevice",
      "path": "$.properties[?(@.name == 'Manufacturer')].value",
      "value": "\"GoodCorp\"",
      "comparison": "Equals"
    },
    {
      "target": "SensorSpace",
      "path": "$.type",
      "value": "\"Venue\"",
      "comparison": "Equals"
    }
  ]
}
```

> [!IMPORTANT]
> - Caminhos JSON são maiúsculas de minúsculas.
> - O payload JSON é o mesmo que o payload devolvido pelo:
>   - `/sensors/{id}?includes=properties,types` para o sensor.
>   - `/devices/{id}?includes=properties,types,sensors,sensorsproperties,sensorstypes` para o dispositivo de principal do sensor.
>   - `/spaces/{id}?includes=properties,types,location,timezone` para o espaço de principal do sensor.
> - As comparações diferenciam maiúsculas de minúsculas.

### <a name="user-defined-functions"></a>Funções definidas pelo utilizador

Uma função definida pelo usuário é uma função personalizada executada dentro de um ambiente isolado de duplos Digital do Azure. Funções definidas pelo utilizador têm acesso a mensagem de telemetria não processados do sensor que obtém recebida. Funções definidas pelo utilizador também tem acesso para o serviço graph e o dispatcher espacial. Depois da função definida pelo utilizador está registrada dentro de um gráfico, na ferramenta de correspondência (detalhadas [acima](#matcher)) tem de ser criada para especificar quando a função é executada. Por exemplo, quando duplos Digital do Azure recebe telemetria nova de um determinado sensor, a função definida pelo utilizador correspondente pode calcular uma média móvel das último alguns as leituras dos sensores.

Funções definidas pelo utilizador podem ser escritas em JavaScript. Métodos auxiliares interagirem com o gráfico no ambiente de execução definido pelo utilizador. Os desenvolvedores podem executar fragmentos personalizados de código em relação a mensagens de telemetria de sensores. Alguns exemplos:

- Defina o diretamente para o objeto de sensor em gráfico de leitura de sensor.
- Execute uma ação baseada nas leituras de sensores diferentes dentro de um espaço no gráfico.
- Crie uma notificação quando forem cumpridas determinadas condições para um entrada de leitura de sensor.
- Anexe os metadados de gráfico para o sensor ler antes de enviar uma notificação.

Para obter mais informações, consulte [como utilizar as funções definidas pelo utilizador](./how-to-user-defined-functions.md).


#### <a name="examples"></a>Exemplos

O [repositório do GitHub para o Digital duplos C# exemplo](https://github.com/Azure-Samples/digital-twins-samples-csharp/) contém alguns exemplos das funções definidas pelo utilizador:
- [Esta função](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availabilityForTutorial.js) procura por emissões de dióxido de carbono, movimento e os valores de temperatura determinar se uma sala está disponível com estes valores no intervalo. O [tutoriais duplos Digital](tutorial-facilities-udf.md) explorar essa função em mais detalhes. 
- [Esta função](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/multiplemotionsensors.js) procura dados de vários sensores de movimento e determina que o espaço está disponível se nenhum deles detetar qualquer movimento. É fácil substituir a função definida pelo utilizador utilizada em qualquer um a [início rápido](quickstart-view-occupancy-dotnet.md), ou o [tutoriais](tutorial-facilities-setup.md), fazendo as alterações mencionadas na seção de comentários do arquivo. 



### <a name="role-assignment"></a>Atribuição de função

Ações de uma função definida pelo utilizador estão sujeitos a duplos Digital do Azure [controlo de acesso baseado em funções](./security-role-based-access-control.md) para proteger os dados no serviço. Atribuições de funções definem quais funções definidas pelo utilizador tem as permissões adequadas para interagir com o gráfico geográfico e respetivas entidades. Por exemplo, uma função definida pelo usuário pode ter a capacidade e permissão para *CREATE*, *ler*, *ATUALIZAÇÃO*, ou *eliminar* dados de gráfico num determinado espaço. Nível de uma função definida pelo utilizador de acesso é verificado quando a função definida pelo utilizador pede-lhe o gráfico de dados ou tenta uma ação. Para obter mais informações, consulte [controlo de acesso baseado em funções](./security-create-manage-role-assignments.md).

É possível que na ferramenta de correspondência acionar uma função definida pelo utilizador que tenha não existem atribuições de função. Neste caso, a função definida pelo utilizador não consegue ler todos os dados do gráfico.

## <a name="next-steps"></a>Passos Seguintes

- Para saber mais sobre como rotear mensagens de telemetria e de eventos para outros serviços do Azure, leia [encaminhar eventos e mensagens](./concepts-events-routing.md).

- Para saber mais sobre como criar matchers, funções definidas pelo utilizador e as atribuições de funções, leia [guia para utilizar as funções definidas pelo utilizador](./how-to-user-defined-functions.md).

- Reveja os [documentação de referência da biblioteca de cliente de função definida pelo utilizador](./reference-user-defined-functions-client-library.md).

<!-- Images -->
[1]: media/concepts/digital-twins-data-processing-flow.png
[2]: media/concepts/digital-twins-user-defined-functions.png
