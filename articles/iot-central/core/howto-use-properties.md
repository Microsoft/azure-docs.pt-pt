---
title: Como utilizar propriedades numa solução Azure IoT Central
description: Como utilizar propriedades só de leitura e writable na solução Azure IoT Central
author: v-krghan
ms.author: v-krghan
ms.date: 08/12/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: fa9b07d80c34ec26ca920fe147ada8f8ef7f2fd7
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91346963"
---
# <a name="how-to-use-properties-in-an-azure-iot-central-solution"></a>Como utilizar propriedades numa solução Azure IoT Central

Este artigo mostra-lhe como usar propriedades do dispositivo que são definidas num modelo de dispositivo na sua aplicação Azure IoT Central.

As propriedades representam valores pontuais. Por exemplo, um dispositivo pode usar uma propriedade para reportar a temperatura-alvo que está a tentar alcançar. As propriedades também permitem sincronizar o estado entre o seu dispositivo e a sua aplicação IoT Central.  Você pode definir propriedades writable a partir de IoT Central.

Também pode definir propriedades em nuvem numa aplicação IoT Central. Os valores de propriedade em nuvem nunca são trocados com um dispositivo e estão fora de alcance para este artigo.

## <a name="define-your-properties"></a>Defina as suas propriedades

As propriedades são campos de dados que representam o estado do seu dispositivo. Utilize propriedades para representar o estado duradouro do dispositivo, como o estado de saída de um dispositivo. As propriedades também podem representar propriedades básicas do dispositivo, como a versão de software do dispositivo. Pode declarar propriedades apenas para leitura ou legitável.

A imagem seguinte mostra uma definição de propriedade na Aplicação Central Azure IoT

![Definir Propriedade](./media/howto-use-properties/property-definition.png)

A tabela a seguir mostra as definições de configuração para uma capacidade de propriedade:

| Campo           | Descrição                                                                                                                                                                                                                        |
|-----------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Nome a Apresentar    | O nome de exibição para o valor da propriedade usado em dashboards e formulários.                                                                                                                                                              |
| Name            | O nome da propriedade. O IoT Central gera um valor para este campo a partir do nome do visor, mas pode escolher o seu próprio valor se necessário. Este campo tem de ser alfanumérico.                                                 |
| Tipo de Capacidade | Propriedade.                                                                                                                                                                                                                          |
| Tipo semântico   | O tipo semântico da propriedade, como temperatura, estado ou evento. A escolha do tipo semântico determina quais dos seguintes campos estão disponíveis.                                                                       |
| Esquema          | O tipo de dados da propriedade, como duplo, string ou vetor. As escolhas disponíveis são determinadas pelo tipo semântico. Schema não está disponível para o evento e tipos semânticos do estado.                                               |
| Gravável       | Se a propriedade não for escrita, o dispositivo pode reportar valores de propriedade à IoT Central. Se a propriedade for escrita, o dispositivo pode reportar valores de propriedade à IoT Central e ioT Central pode enviar atualizações de propriedade para o dispositivo. |
| Gravidade        | Disponível apenas para o tipo semântico do evento. As severidades são **Erro,** **Informação**ou **Aviso**.                                                                                                                         |
| Valores do Estado    | Disponível apenas para o tipo semântico do estado. Defina os valores de estado possíveis, cada um dos quais tem nome de exibição, nome, tipo de enumeração e valor.                                                                                   |
| Unidade            | Uma unidade para o valor da propriedade, como **mph,** **%** ou ** &deg; C**.                                                                                                                                                              |
| Unidade de Exibição    | Uma unidade de visualização para utilização em painéis e formulários.                                                                                                                                                                                    |
| Comentário         | Quaisquer comentários sobre a capacidade da propriedade.                                                                                                                                                                                        |
| Description     | Uma descrição da capacidade da propriedade.                                                                                                                                                                                          |

As propriedades também podem ser definidas numa interface num modelo de dispositivo como abaixo:

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

Este exemplo mostra cinco propriedades, estas podem estar relacionadas com a definição de propriedade na UI como abaixo:

* `@type` para especificar o tipo de capacidade: `Property`
* `name` para o valor da propriedade.
* `schema` especificar o tipo de dados para a propriedade. Este valor pode ser um tipo primitivo, como duplo, inteiro, booleano ou corda. Os tipos complexos de objetos, matrizes e mapas também são suportados.
* `writable` Por padrão, as propriedades são apenas de leitura. Você pode marcar uma propriedade como escrever, usando este campo

Os campos opcionais, como o nome do ecrã e a descrição, permitem adicionar mais detalhes à interface e às capacidades.

Ao criar uma propriedade, pode especificar os tipos de **esquemas** complexos como Objeto, Enum, etc.

![Adicionar uma capacidade](./media/howto-use-properties/property.png)

Ao selecionar o esquema complexo como **o Object,** também precisa de definir o objeto.

![Definir objeto](./media/howto-use-properties/object.png)

O código que se segue mostra a definição de um tipo de propriedade Object. Este objeto tem dois campos com tipos de corda e inteiro:

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

Por padrão, as propriedades são apenas de leitura. Propriedades apenas de leitura significam que o dispositivo reporta atualizações do valor da propriedade para a sua aplicação IoT Central. Sua aplicação IoT Central não pode definir o valor de uma propriedade apenas de leitura.

A IoT Central utiliza gémeos de dispositivos para sincronizar os valores de propriedade entre o dispositivo e a aplicação IoT Central. Os valores de propriedade do dispositivo usam propriedades reportadas pelo dispositivo twin. Para mais informações, consulte [os gémeos do dispositivo](https://docs.microsoft.com/azure/iot-hub/tutorial-device-twins)

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

As propriedades apenas de leitura são enviadas pelo dispositivo para a IoT Central. As propriedades são enviadas como carga útil JSON, para mais informações, ver [cargas.](./concepts-telemetry-properties-commands.md)

Pode utilizar o dispositivo Azure IoT SDK para enviar uma atualização de propriedade para a sua aplicação IoT Central.

As propriedades gémeas do dispositivo podem ser enviadas para a sua aplicação Azure IoT Central utilizando a função abaixo:

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

Este artigo utiliza Node.js para simplicidade, para obter informações completas sobre exemplos de aplicações do dispositivo ver a [Configuração e ligar uma aplicação do cliente à sua aplicação Azure IoT Central (Node.js)](tutorial-connect-device-nodejs.md) e Criar e ligar uma aplicação de cliente aos seus tutoriais [de aplicação Azure IoT Central (Python).](tutorial-connect-device-python.md)

A seguinte vista na aplicação Azure IoT Central mostra as propriedades, você pode ver a vista automaticamente faz do modelo do dispositivo propriedade uma _propriedade de dispositivo apenas de leitura_.

![Vista da propriedade apenas de leitura](./media/howto-use-properties/read-only.png)

## <a name="implement-writable-properties"></a>Implementar propriedades writable

As propriedades writable são definidas por um operador na aplicação IoT Central num formulário. A IoT Central envia a propriedade para o dispositivo. A IoT Central espera um reconhecimento do dispositivo. 

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

Para definir e manusear as propriedades escritas a que o seu dispositivo responde, pode utilizar o seguinte código.

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

A mensagem de resposta deve incluir os `ac` campos e `av` campos. O campo `ad` é opcional. Veja os seguintes excertos, por exemplo.

* `ac` é um campo numérico que utiliza os valores na tabela seguinte:

* `av` é o número de versão enviado para o dispositivo.

* `ad` é uma descrição do string de opção.

| Valor | Etiqueta | Description |
| ----- | ----- | ----------- |
| `'ac': 200` | Concluído | A operação de mudança de propriedade foi concluída com sucesso. |
| `'ac': 202`  ou `'ac': 201` | Pendente | A operação de mudança de propriedade está pendente ou em curso |
| `'ac': 4xx` | Erro | A alteração de propriedade solicitada não foi válida ou teve um erro |
| `'ac': 5xx` | Erro | O dispositivo sofreu um erro inesperado ao processar a alteração solicitada. |


Para mais informações, consulte [os gémeos do dispositivo.](https://docs.microsoft.com/azure/iot-hub/tutorial-device-twins)

Quando o operador define uma propriedade escrita na aplicação IoT Central, a aplicação utiliza um dispositivo que dois propriedades desejadas para enviar o valor para o dispositivo. Em seguida, o dispositivo responde utilizando uma propriedade reportada por gémeos do dispositivo. Quando a IoT Central recebe o valor da propriedade reportada, atualiza a vista da propriedade com um estado de **Aceito.**

A seguinte vista mostra as propriedades writable. Quando introduz o valor e **guarda,** o estado inicial está **pendente,** quando o dispositivo aceita a alteração, o estado muda para **Aceito**.

![Estado pendente](./media/howto-use-properties/status-pending.png)

![Propriedade aceite](./media/howto-use-properties/accepted.png)

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a usar propriedades na sua aplicação Azure IoT Central, pode ver [Payloads](concepts-telemetry-properties-commands.md) e [Criar e ligar uma aplicação do cliente à sua aplicação Azure IoT Central (Node.js)](tutorial-connect-device-nodejs.md).
