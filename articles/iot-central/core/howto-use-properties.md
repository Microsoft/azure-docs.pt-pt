---
title: Utilize propriedades numa solução Azure IoT Central
description: Aprenda a usar propriedades só de leitura e writable numa solução Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 11/06/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 36329987e510372ff286a10584a115ea259afc60
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98119089"
---
# <a name="use-properties-in-an-azure-iot-central-solution"></a>Utilize propriedades numa solução Azure IoT Central

Este guia de como fazer mostra-lhe como, como desenvolvedor de dispositivos, utilizar propriedades do dispositivo que são definidas num modelo de dispositivo na sua aplicação Azure IoT Central.

As propriedades representam valores pontuais. Por exemplo, um dispositivo pode usar uma propriedade para reportar a temperatura-alvo que está a tentar alcançar. Por predefinição, as propriedades do dispositivo são apenas de leitura na IoT Central. Propriedades writable permitem sincronizar o estado entre o seu dispositivo e a sua aplicação Azure IoT Central.

Também pode definir propriedades em nuvem numa aplicação Azure IoT Central. Os valores de propriedade em nuvem nunca são trocados com um dispositivo e estão fora de alcance para este artigo.

## <a name="define-your-properties"></a>Defina as suas propriedades

As propriedades são campos de dados que representam o estado do seu dispositivo. Utilize propriedades para representar o estado duradouro do dispositivo, como o estado de ligação/desligação de um dispositivo. As propriedades também podem representar propriedades básicas do dispositivo, como a versão de software do dispositivo. Declara propriedades apenas para leitura ou legitável.

A imagem que se segue mostra uma definição de propriedade numa aplicação Azure IoT Central.

:::image type="content" source="media/howto-use-properties/property-definition.png" alt-text="Screenshot que mostra uma definição de propriedade em uma aplicação Azure IoT Central.":::

A tabela seguinte mostra as definições de configuração para uma capacidade de propriedade.

| Campo           | Descrição                                                                                                                                                                                                                        |
|-----------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Nome a apresentar    | O nome de exibição para o valor da propriedade usado em dashboards e formulários.                                                                                                                                                              |
| Name            | O nome da propriedade. O Azure IoT Central gera um valor para este campo a partir do nome do visor, mas pode escolher o seu próprio valor se necessário. Este campo deve ser alfanumérico.  O código do dispositivo utiliza este valor **Nome.**           |
| Tipo de capacidade | Propriedade.                                                                                                                                                                                                                          |
| Semantic type (Tipo de semântica)   | O tipo semântico da propriedade, como temperatura, estado ou evento. A escolha do tipo semântico determina quais dos seguintes campos estão disponíveis.                                                                       |
| Esquema          | O tipo de dados da propriedade, como duplo, string ou vetor. As escolhas disponíveis são determinadas pelo tipo semântico. Schema não está disponível para o evento e tipos semânticos do estado.                                               |
| Writable (Gravável)       | Se a propriedade não for escrita, o dispositivo pode reportar valores de propriedade à Azure IoT Central. Se a propriedade for escrita, o dispositivo pode reportar valores de propriedade à Azure IoT Central. Em seguida, a Azure IoT Central pode enviar atualizações de propriedade para o dispositivo. |
| Gravidade        | Disponível apenas para o tipo semântico do evento. As severidades são **Erro,** **Informação** ou **Aviso**.                                                                                                                         |
| Valores do Estado    | Disponível apenas para o tipo semântico do estado. Defina os valores de estado possíveis, cada um dos quais tem nome de exibição, nome, tipo de enumeração e valor.                                                                                   |
| Unidade            | Uma unidade para o valor da propriedade, como **mph,** **%** ou **&deg; C**.                                                                                                                                                              |
| Unidade de exibição    | Uma unidade de visualização para utilização em painéis e formulários.                                                                                                                                                                                    |
| Comentário         | Quaisquer comentários sobre a capacidade da propriedade.                                                                                                                                                                                        |
| Description     | Uma descrição da capacidade da propriedade.                                                                                                                                                                                          |

As propriedades também podem ser definidas numa interface num modelo de dispositivo, como mostrado aqui:

``` json
{
  "@type": [
    "Property",
    "Temperature"
  ],
  "name": "targetTemperature",
  "schema": "double",
  "displayName": "Target Temperature",
  "description": "Allows to remotely specify the desired target temperature.",
  "unit" : "degreeCelsius",
  "writable": true
},
{
  "@type": [
    "Property",
    "Temperature"
  ],
  "name": "maxTempSinceLastReboot",
  "schema": "double",
  "unit" : "degreeCelsius",
  "displayName": "Max temperature since last reboot.",
  "description": "Returns the max temperature since last device reboot."
}
```

Este exemplo mostra duas propriedades. Estas propriedades dizem respeito à definição de propriedade na UI:

* `@type` especifica o tipo de capacidade: `Property` . O exemplo anterior também mostra o tipo semântico `Temperature` para ambas as propriedades.
* `name` para a propriedade.
* `schema` especifica o tipo de dados para a propriedade. Este valor pode ser um tipo primitivo, como duplo, inteiro, booleano ou corda. Os tipos e mapas complexos de objetos também são suportados.
* `writable` Por padrão, as propriedades são apenas de leitura. Você pode marcar uma propriedade como escrita usando este campo.

Os campos opcionais, como o nome do ecrã e a descrição, permitem adicionar mais detalhes à interface e às capacidades.

Ao criar uma propriedade, pode especificar tipos de esquemas complexos como **Object** e **Enum**.

![Screenshot que mostra como adicionar uma capacidade.](./media/howto-use-properties/property.png)

Ao selecionar o **esquema** complexo , como **o Object,** também precisa de definir o objeto.

:::image type="content" source="media/howto-use-properties/object.png" alt-text="Screenshot que mostra como definir um objeto":::

O código que se segue mostra a definição de um tipo de propriedade Object. Este objeto tem dois campos com tipos de corda e inteiro.

``` json
{
  "@type": "Property",
  "displayName": {
    "en": "ObjectProperty"
  },
  "name": "ObjectProperty",
  "schema": {
    "@type": "Object",
    "displayName": {
      "en": "Object"
    },
    "fields": [
      {
        "displayName": {
          "en": "Field1"
        },
        "name": "Field1",
        "schema": "integer"
      },
      {
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

Por padrão, as propriedades são apenas de leitura. Propriedades apenas de leitura permitem que um dispositivo reporte atualizações do valor da propriedade para a sua aplicação Azure IoT Central. A sua aplicação Azure IoT Central não pode definir o valor de uma propriedade só de leitura.

A Azure IoT Central utiliza gémeos de dispositivos para sincronizar os valores de propriedade entre o dispositivo e a aplicação Azure IoT Central. Os valores de propriedade do dispositivo usam propriedades reportadas pelo dispositivo twin. Para mais informações, consulte [os gémeos do dispositivo.](../../iot-hub/tutorial-device-twins.md)

O seguinte corte de um modelo de dispositivo mostra a definição de um tipo de propriedade apenas de leitura:

``` json
{
  "name": "model",
  "displayName": "Device model",
  "schema": "string",
  "comment": "Device model name or ID. Ex. Surface Book 2."
}
```

As atualizações de propriedade são enviadas por um dispositivo como uma carga útil JSON. Para obter mais informações, consulte [as cargas.](./concepts-telemetry-properties-commands.md)

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

Este artigo usa Node.js para a simplicidade. Para outros exemplos linguísticos, consulte a [Configuração e conecte uma aplicação do cliente ao seu tutorial de aplicação Azure IoT Central.](tutorial-connect-device.md)

A seguinte vista na aplicação Azure IoT Central mostra as propriedades que você pode ver. A vista torna automaticamente a propriedade do **modelo do dispositivo** uma propriedade _apenas de leitura_.

:::image type="content" source="media/howto-use-properties/read-only.png" alt-text="Screenshot que mostra a vista de uma propriedade só de leitura":::

## <a name="implement-writable-properties"></a>Implementar propriedades writable

As propriedades escrituradas são definidas por um operador na aplicação Azure IoT Central num formulário. A Azure IoT Central envia a propriedade para o dispositivo. A Azure IoT Central espera um reconhecimento do dispositivo.

O seguinte corte de um modelo de dispositivo mostra a definição de um tipo de propriedade writable:

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

| Valor | Etiqueta | Description |
| ----- | ----- | ----------- |
| `'ac': 200` | Concluído | A operação de mudança de propriedade foi concluída com sucesso. |
| `'ac': 202` ou `'ac': 201` | Pendente | A operação de mudança de propriedade está pendente ou em curso. |
| `'ac': 4xx` | Erro | A alteração de propriedade solicitada não era válida ou tinha um erro. |
| `'ac': 5xx` | Erro | O dispositivo sofreu um erro inesperado ao processar a alteração solicitada. |

Para obter mais informações sobre os gémeos do dispositivo, consulte [configurar os seus dispositivos a partir de um serviço de back-end](../../iot-hub/tutorial-device-twins.md).

Quando o operador define uma propriedade escrita na aplicação Azure IoT Central, a aplicação utiliza um dispositivo de propriedade dupla desejada para enviar o valor para o dispositivo. Em seguida, o dispositivo responde utilizando uma propriedade reportada por gémeos do dispositivo. Quando a Azure IoT Central recebe o valor da propriedade reportada, atualiza a vista da propriedade com um estado de **Aceito.**

A seguinte vista mostra as propriedades writable. Quando introduz o valor e selecione **Guardar,** o estado inicial está **pendente**. Quando o dispositivo aceita a alteração, o estado muda para **Accepted**.

![Screenshot que mostra estado pendente.](./media/howto-use-properties/status-pending.png)

![Screenshot que mostra propriedade aceite.](./media/howto-use-properties/accepted.png)

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a usar propriedades na sua aplicação Azure IoT Central, consulte:

* [Cargas](concepts-telemetry-properties-commands.md)
* [Crie e conecte uma aplicação de cliente à sua aplicação Azure IoT Central](tutorial-connect-device.md)