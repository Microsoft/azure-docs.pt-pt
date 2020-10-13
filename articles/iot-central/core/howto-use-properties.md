---
title: Utilize propriedades numa solução Azure IoT Central
description: Aprenda a usar propriedades só de leitura e writable numa solução Azure IoT Central.
author: v-krghan
ms.author: v-krghan
ms.date: 08/12/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: eb949f6f0895743250ead0276692497432bfeed5
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2020
ms.locfileid: "91940570"
---
# <a name="use-properties-in-an-azure-iot-central-solution"></a>Utilize propriedades numa solução Azure IoT Central

Este artigo mostra-lhe como usar propriedades do dispositivo que são definidas num modelo de dispositivo na sua aplicação Azure IoT Central.

As propriedades representam valores pontuais. Por exemplo, um dispositivo pode usar uma propriedade para reportar a temperatura-alvo que está a tentar alcançar. As propriedades também permitem sincronizar o estado entre o seu dispositivo e a sua aplicação Azure IoT Central. Você pode definir propriedades writable a partir de Azure IoT Central.

Também pode definir propriedades em nuvem numa aplicação Azure IoT Central. Os valores de propriedade em nuvem nunca são trocados com um dispositivo e estão fora de alcance para este artigo.

## <a name="define-your-properties"></a>Defina as suas propriedades

As propriedades são campos de dados que representam o estado do seu dispositivo. Utilize propriedades para representar o estado duradouro do dispositivo, como o estado de ligação/desligação de um dispositivo. As propriedades também podem representar propriedades básicas do dispositivo, como a versão de software do dispositivo. Pode declarar propriedades apenas para leitura ou legitável.

A imagem que se segue mostra uma definição de propriedade numa aplicação Azure IoT Central.

![Screenshot que mostra uma definição de propriedade em uma aplicação Azure IoT Central.](./media/howto-use-properties/property-definition.png)

A tabela seguinte mostra as definições de configuração para uma capacidade de propriedade.

| Campo           | Descrição                                                                                                                                                                                                                        |
|-----------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Nome a apresentar    | O nome de exibição para o valor da propriedade usado em dashboards e formulários.                                                                                                                                                              |
| Nome            | O nome da propriedade. O Azure IoT Central gera um valor para este campo a partir do nome do visor, mas pode escolher o seu próprio valor se necessário. Este campo deve ser alfanumérico.                                                 |
| Tipo de capacidade | Propriedade.                                                                                                                                                                                                                          |
| Semantic type (Tipo de semântica)   | O tipo semântico da propriedade, como temperatura, estado ou evento. A escolha do tipo semântico determina quais dos seguintes campos estão disponíveis.                                                                       |
| Esquema          | O tipo de dados da propriedade, como duplo, string ou vetor. As escolhas disponíveis são determinadas pelo tipo semântico. Schema não está disponível para o evento e tipos semânticos do estado.                                               |
| Gravável       | Se a propriedade não for escrita, o dispositivo pode reportar valores de propriedade à Azure IoT Central. Se a propriedade for escrita, o dispositivo pode reportar valores de propriedade à Azure IoT Central. Em seguida, a Azure IoT Central pode enviar atualizações de propriedade para o dispositivo. |
| Gravidade        | Disponível apenas para o tipo semântico do evento. As severidades são **Erro,** **Informação**ou **Aviso**.                                                                                                                         |
| Valores do Estado    | Disponível apenas para o tipo semântico do estado. Defina os valores de estado possíveis, cada um dos quais tem nome de exibição, nome, tipo de enumeração e valor.                                                                                   |
| Unidade            | Uma unidade para o valor da propriedade, como **mph,** **%** ou ** &deg; C**.                                                                                                                                                              |
| Unidade de exibição    | Uma unidade de visualização para utilização em painéis e formulários.                                                                                                                                                                                    |
| Comentário         | Quaisquer comentários sobre a capacidade da propriedade.                                                                                                                                                                                        |
| Descrição     | Uma descrição da capacidade da propriedade.                                                                                                                                                                                          |

As propriedades também podem ser definidas numa interface num modelo de dispositivo, como mostrado aqui:

``` json
{
  "@type": "Property",
  "displayName": "Device State",
  "description": "The state of the device. Two states online/offline are available.",
  "name": "state",
  "schema": "boolean"
},
{
  "@type": "Property",
  "displayName": "Customer Name",
  "description": "The name of the customer currently operating the device.",
  "name": "name",
  "schema": "string",
  "writable": true
},
{
 "@type": "Property",
 "displayName": "Date ",
 "description": "The date on which the device is currently operating",
 "name": "date",
 "writable": true,
 "schema": "date"
},
{ 
 "@type": "Property",
 "displayName": "Location",
 "description": "The current location of the device",
 "name": "location",
 "writable": true,
 "schema": "geopoint"
},
{
 "@type": "Property",
 "displayName": "Vector Level",
 "description": "The Vector level of the device",
 "name": "vector",
 "writable": true,
 "schema": "vector"
}
```

Este exemplo mostra cinco propriedades. Estas propriedades podem estar relacionadas com a definição de propriedade na UI como mostrado aqui:

* `@type` para especificar o tipo de capacidade: `Property`
* `name` para o valor da propriedade.
* `schema` especificar o tipo de dados para a propriedade. Este valor pode ser um tipo primitivo, como duplo, inteiro, booleano ou corda. Os tipos complexos de objetos, matrizes e mapas também são suportados.
* `writable` Por padrão, as propriedades são apenas de leitura. Você pode marcar uma propriedade como escrita usando este campo.

Os campos opcionais, como o nome do ecrã e a descrição, permitem adicionar mais detalhes à interface e às capacidades.

Ao criar uma propriedade, pode especificar os tipos **complexos de Schema** como Object e Enum.

![Screenshot que mostra como adicionar uma capacidade.](./media/howto-use-properties/property.png)

Ao selecionar o **esquema**complexo , como **o Object,** também precisa de definir o objeto.

![Screenshot que mostra como definir um objeto.](./media/howto-use-properties/object.png)

O código que se segue mostra a definição de um tipo de propriedade Object. Este objeto tem dois campos com tipos de corda e inteiro.

``` json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "ObjectProperty"
  },
  "name": "ObjectProperty",
  "schema": {
    "@id": "<element id>",
    "@type": "Object",
    "displayName": {
      "en": "Object"
    },
    "fields": [
      {
        "@id": "<element id>",
        "@type": "SchemaField",
        "displayName": {
          "en": "Field1"
        },
        "name": "Field1",
        "schema": "integer"
      },
      {
        "@id": "<element id>",
        "@type": "SchemaField",
        "displayName": {
          "en": "Field2"
        },
        "name": "Field2",
        "schema": "string"
      }
    ]
  }
}
```

## <a name="implement-read-only-properties"></a>Implementar propriedades apenas de leitura

Por padrão, as propriedades são apenas de leitura. Propriedades apenas de leitura significam que o dispositivo reporta atualizações do valor da propriedade para a sua aplicação Azure IoT Central. A sua aplicação Azure IoT Central não pode definir o valor de uma propriedade só de leitura.

A Azure IoT Central utiliza gémeos de dispositivos para sincronizar os valores de propriedade entre o dispositivo e a aplicação Azure IoT Central. Os valores de propriedade do dispositivo usam propriedades reportadas pelo dispositivo twin. Para mais informações, consulte [os gémeos do dispositivo.](https://docs.microsoft.com/azure/iot-hub/tutorial-device-twins)

O seguinte corte de um modelo de capacidade do dispositivo mostra a definição de um tipo de propriedade apenas de leitura:

``` json
{
  "@type": "Property",
  "name": "model",
  "displayName": "Device model",
  "schema": "string",
  "comment": "Device model name or ID. Ex. Surface Book 2."
}
```

As propriedades apenas de leitura são enviadas pelo dispositivo para a Azure IoT Central. As propriedades são enviadas como carga útil JSON. Para obter mais informações, consulte [as cargas.](./concepts-telemetry-properties-commands.md)

Pode utilizar o dispositivo Azure IoT SDK para enviar uma atualização de propriedade para a sua aplicação Azure IoT Central.

As propriedades gémeas do dispositivo podem ser enviadas para a sua aplicação Azure IoT Central utilizando a seguinte função:

``` javascript
hubClient.getTwin((err, twin) => {
    const properties = {
        model: 'environmentalSensor1.0'
    };
    twin.properties.reported.update(properties, (err) => {
        console.log(err ? `error: ${err.toString()}` : `status: success` )
    });
});
```

Este artigo usa Node.js para a simplicidade. Para obter informações completas sobre exemplos de aplicações do dispositivo, consulte os seguintes tutoriais:

* [Crie e conecte uma aplicação do cliente à sua aplicação Azure IoT Central (Node.js)](tutorial-connect-device-nodejs.md)
* [Crie e conecte uma aplicação de cliente à sua aplicação Azure IoT Central (Python)](tutorial-connect-device-python.md)

A seguinte vista na aplicação Azure IoT Central mostra as propriedades que você pode ver. A vista torna automaticamente a propriedade do **modelo do dispositivo** uma propriedade _apenas de leitura_.

![Screenshot que mostra a vista de uma propriedade só de leitura.](./media/howto-use-properties/read-only.png)

## <a name="implement-writable-properties"></a>Implementar propriedades writable

As propriedades escrituradas são definidas por um operador na aplicação Azure IoT Central num formulário. A Azure IoT Central envia a propriedade para o dispositivo. A Azure IoT Central espera um reconhecimento do dispositivo.

O seguinte corte de um modelo de capacidade do dispositivo mostra a definição de um tipo de propriedade writable:

``` json
{
  "@type": "Property",
  "displayName": "Brightness Level",
  "description": "The brightness level for the light on the device. Can be specified as 1 (high), 2 (medium), 3 (low)",
  "name": "brightness",
  "writable": true,
  "schema": "long"
}
```

Um cliente do dispositivo deve enviar uma carga útil JSON que se pareça com o seguinte exemplo como uma propriedade reportada no dispositivo twin:

``` json
{ "Brightness Level": 2 }
```

Para definir e manusear as propriedades escritas a que o seu dispositivo responde, pode utilizar o seguinte código:

``` javascript
hubClient.getTwin((err, twin) => {
    twin.on('properties.desired.brightness', function(desiredBrightness) {
        console.log( `Received setting: ${desiredBrightness.value}` );
        var patch = {
            brightness: {
                value: desiredBrightness.value,
                ad: 'success',
                ac: 200,
                av: desiredBrightness.$version
            }
        }
        twin.properties.reported.update(patch, (err) => {
            console.log(err ? `error: ${err.toString()}` : `status: success` )
        });
    });
});
```

A mensagem de resposta deve incluir os `ac` campos e `av` campos. O campo `ad` é opcional. Veja os seguintes excertos, por exemplo:

* `ac` é um campo numérico que utiliza os valores na tabela seguinte.
* `av` é o número de versão enviado para o dispositivo.
* `ad` é uma descrição do string de opção.

| Valor | Etiqueta | Descrição |
| ----- | ----- | ----------- |
| `'ac': 200` | Concluído | A operação de mudança de propriedade foi concluída com sucesso. |
| `'ac': 202` ou `'ac': 201` | Pendente | A operação de mudança de propriedade está pendente ou em curso. |
| `'ac': 4xx` | Erro | A alteração de propriedade solicitada não era válida ou tinha um erro. |
| `'ac': 5xx` | Erro | O dispositivo sofreu um erro inesperado ao processar a alteração solicitada. |


Para obter mais informações sobre os gémeos do dispositivo, consulte [configurar os seus dispositivos a partir de um serviço de back-end](https://docs.microsoft.com/azure/iot-hub/tutorial-device-twins).

Quando o operador define uma propriedade escrita na aplicação Azure IoT Central, a aplicação utiliza um dispositivo de propriedade dupla desejada para enviar o valor para o dispositivo. Em seguida, o dispositivo responde utilizando uma propriedade reportada por gémeos do dispositivo. Quando a Azure IoT Central recebe o valor da propriedade reportada, atualiza a vista da propriedade com um estado de **Aceito.**

A seguinte vista mostra as propriedades writable. Quando introduz o valor e selecione **Guardar,** o estado inicial está **pendente**. Quando o dispositivo aceita a alteração, o estado muda para **Accepted**.

![Screenshot que mostra estado pendente.](./media/howto-use-properties/status-pending.png)

![Screenshot que mostra propriedade aceite.](./media/howto-use-properties/accepted.png)

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a usar propriedades na sua aplicação Azure IoT Central, consulte:

* [Cargas](concepts-telemetry-properties-commands.md)
* [Crie e conecte uma aplicação do cliente à sua aplicação Azure IoT Central (Node.js)](tutorial-connect-device-nodejs.md)
