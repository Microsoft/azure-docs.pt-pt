---
title: Como utilizar comandos de dispositivos numa solução Azure IoT Central
description: Como utilizar comandos de dispositivos em solução Azure IoT Central. Este tutorial mostra-lhe como, como desenvolvedor de dispositivos, utilizar comandos de dispositivos na aplicação do cliente para a sua aplicação Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 01/07/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: dd05a4880bdf077f63dc58575828e6180e415260
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98122601"
---
# <a name="how-to-use-commands-in-an-azure-iot-central-solution"></a>Como utilizar comandos numa solução Azure IoT Central

Este guia de como fazer mostra-lhe como, como desenvolvedor de dispositivos, utilizar comandos que são definidos num modelo de dispositivo.

Um operador pode usar a UI Central IoT para chamar um comando num dispositivo. Os comandos controlam o comportamento de um dispositivo. Por exemplo, um operador pode chamar um comando para reiniciar um dispositivo ou recolher dados de diagnóstico.

Um dispositivo pode:

* Responda a um comando imediatamente.
* Responda à IoT Central quando receber o comando e, em seguida, notifique a IoT Central quando o *comando de longa duração* estiver completo.

Por predefinição, os comandos esperam que um dispositivo seja ligado e falhe se o dispositivo não for alcançado. Se selecionar a fila se a opção **offline** no modelo do dispositivo UI um comando pode ser feito em fila até que um dispositivo esteja online. Estes *comandos offline* são descritos numa secção separada mais tarde neste artigo.

## <a name="define-your-commands"></a>Defina os seus comandos

Os comandos padrão são enviados para um dispositivo para instruir o dispositivo a fazer algo. Um comando pode incluir parâmetros com informações adicionais. Por exemplo, um comando para abrir uma válvula num dispositivo pode ter um parâmetro que especifica quanto abrir a válvula. Os comandos também podem receber um valor de retorno quando o dispositivo completa o comando. Por exemplo, um comando que pede a um dispositivo para executar alguns diagnósticos pode receber um relatório de diagnóstico como um valor de retorno.

Os comandos são definidos como parte de um modelo de dispositivo. A imagem que se segue mostra a definição de comando **do relatório Get Max-Min** no modelo do dispositivo do **termóstato.** Este comando tem parâmetros de pedido e resposta: 

:::image type="content" source="media/howto-use-commands/command-definition.png" alt-text="Screenshot mostrando O comando do Relatório Max Min no modelo do dispositivo do termóstato":::

A tabela a seguir mostra as definições de configuração para uma capacidade de comando:

| Campo             |Descrição|
|-------------------|-----------|
|Nome a Apresentar       |O valor de comando utilizado nos painéis e formulários.|
| Name            | O nome do comando. O IoT Central gera um valor para este campo a partir do nome do visor, mas pode escolher o seu próprio valor se necessário. Este campo tem de ser alfanumérico. O código do dispositivo utiliza este valor **Nome.**|
| Tipo de Capacidade | O comando.|
| Fila se offline | Se fazer deste comando um comando *offline.* |
| Descrição     | Uma descrição da capacidade de comando.|
| Comentário     | Qualquer comentário sobre a capacidade de comando.|
| Pedir     | A carga útil para o comando do dispositivo.|
| Resposta     | A carga útil da resposta do comando do dispositivo.|

O seguinte corte mostra a representação JSON do comando no modelo do dispositivo. Neste exemplo, o valor de resposta é um tipo complexo **de objetos** com vários campos:

```json
{
  "@type": "Command",
  "name": "getMaxMinReport",
  "displayName": "Get Max-Min report.",
  "description": "This command returns the max, min and average temperature from the specified time to the current time.",
  "request": {
    "name": "since",
    "displayName": "Since",
    "description": "Period to return the max-min report.",
    "schema": "dateTime"
  },
  "response": {
    "name" : "tempReport",
    "displayName": "Temperature Report",
    "schema": {
      "@type": "Object",
      "fields": [
        {
          "name": "maxTemp",
          "displayName": "Max temperature",
          "schema": "double"
        },
        {
          "name": "minTemp",
          "displayName": "Min temperature",
          "schema": "double"
        },
        {
          "name" : "avgTemp",
          "displayName": "Average Temperature",
          "schema": "double"
        },
        {
          "name" : "startTime",
          "displayName": "Start Time",
          "schema": "dateTime"
        },
        {
          "name" : "endTime",
          "displayName": "End Time",
          "schema": "dateTime"
        }
      ]
    }
  }
}
```

> [!TIP]
> Pode exportar um modelo de dispositivo a partir da página do modelo do dispositivo.

Pode relacionar esta definição de comando com a imagem da UI utilizando os seguintes campos:

* `@type` para especificar o tipo de capacidade: `Command`
* `name` para o valor de comando.

Os campos opcionais, como o nome do ecrã e a descrição, permitem adicionar mais detalhes à interface e às capacidades.

## <a name="standard-commands"></a>Comandos padrão

Esta secção mostra como um dispositivo envia um valor de resposta assim que recebe o comando.

O seguinte corte de código mostra como um dispositivo pode responder a um comando enviando imediatamente um código de sucesso:

> [!NOTE]
> Este artigo usa Node.js para a simplicidade. Para outros exemplos linguísticos, consulte a [Configuração e conecte uma aplicação do cliente ao seu tutorial de aplicação Azure IoT Central.](tutorial-connect-device.md)

```javascript
client.onDeviceMethod('getMaxMinReport', commandHandler);

// ...

const commandHandler = async (request, response) => {
  switch (request.methodName) {
  case 'getMaxMinReport': {
    console.log('MaxMinReport ' + request.payload);
    await sendCommandResponse(request, response, 200, deviceTemperatureSensor.getMaxMinReportObject());
    break;
  }
  default:
    await sendCommandResponse(request, response, 404, 'unknown method');
    break;
  }
};

const sendCommandResponse = async (request, response, status, payload) => {
  try {
    await response.send(status, payload);
    console.log('Response to method \'' + request.methodName +
              '\' sent successfully.' );
  } catch (err) {
    console.error('An error ocurred when sending a method response:\n' +
              err.toString());
  }
};
```

A chamada para `onDeviceMethod` definir o `commandHandler` método. Este encarregado de comando:

1. Verifica o nome do comando.
1. Para o `getMaxMinReport` comando, chama `getMaxMinReportObject` para recuperar os valores a incluir no objeto de retorno.
1. Chamadas `sendCommandResponse` para enviar a resposta de volta para a IoT Central. Esta resposta inclui o `200` código de resposta para indicar o sucesso.

A imagem a seguir mostra como a resposta de comando bem sucedida aparece na UI Central IoT:

:::image type="content" source="media/howto-use-commands/simple-command-ui.png" alt-text="Screenshot mostrando como ver a carga útil do comando para um comando padrão":::

## <a name="long-running-commands"></a>Comandos de execução longa

Esta secção mostra como um dispositivo pode atrasar o envio de uma confirmação de que o comando competiu.

O seguinte corte de código mostra como um dispositivo pode implementar um comando de longa duração:

> [!NOTE]
> Este artigo usa Node.js para a simplicidade. Para outros exemplos linguísticos, consulte a [Configuração e conecte uma aplicação do cliente ao seu tutorial de aplicação Azure IoT Central.](tutorial-connect-device.md)

```javascript
client.onDeviceMethod('rundiagnostics', commandHandler);

// ...

const commandHandler = async (request, response) => {
  switch (request.methodName) {
  case 'rundiagnostics': {
    console.log('Starting long-running diagnostics run ' + request.payload);
    await sendCommandResponse(request, response, 202, 'Diagnostics run started');

    // Long-running operation here
    // ...

    const patch = {
      rundiagnostics: {
        value: 'Diagnostics run complete at ' + new Date().toLocaleString()
      }
    };

    deviceTwin.properties.reported.update(patch, function (err) {
      if (err) throw err;
      console.log('Properties have been reported for component');
    });
    break;
  }
  default:
    await sendCommandResponse(request, response, 404, 'unknown method');
    break;
  }
};
```

A chamada para `onDeviceMethod` definir o `commandHandler` método. Este encarregado de comando:

1. Verifica o nome do comando.
1. Chamadas `sendCommandResponse` para enviar a resposta de volta para a IoT Central. Esta resposta inclui o `202` código de resposta para indicar resultados pendentes.
1. Completa a operação de longa duração.
1. Usa uma propriedade relatada com o mesmo nome que o comando para dizer à IoT Central que o comando completou.

A imagem a seguir mostra como a resposta do comando aparece na UI Central IoT quando recebe o código de resposta 202:

:::image type="content" source="media/howto-use-commands/long-running-start.png" alt-text="Screenshot que mostra resposta imediata do dispositivo":::

A imagem que se segue mostra a UI Central IoT quando recebe a atualização da propriedade que indica que o comando está completo:

:::image type="content" source="media/howto-use-commands/long-running-finish.png" alt-text="Screenshot que mostra comando de longa duração terminado":::

## <a name="offline-commands"></a>Comandos offline

Esta secção mostra como um dispositivo lida com um comando offline. Se um dispositivo estiver online, pode manusear o comando offline assim que for recebido. Se um dispositivo estiver desligado, manuseia o comando offline quando se liga a seguir à IoT Central. Os dispositivos não podem enviar um valor de retorno em resposta a um comando offline.

O seguinte corte de código mostra como um dispositivo pode implementar um comando offline:

> [!NOTE]
> Este artigo usa Node.js para a simplicidade. Para outros exemplos linguísticos, consulte a [Configuração e conecte uma aplicação do cliente ao seu tutorial de aplicação Azure IoT Central.](tutorial-connect-device.md)

A imagem que se segue mostra um comando offline chamado **GenerateDiagnostics**. O parâmetro de pedido é um objeto com propriedade datetime chamada **StartTime** e uma propriedade de enumeração inteiro chamada **Banco**:

:::image type="content" source="media/howto-use-commands/offline-command.png" alt-text="Screenshot que mostra a UI para um comando offline":::

O seguinte corte de código mostra como um cliente pode ouvir comandos offline e exibir o conteúdo da mensagem:

```javascript
client.on('message', function (msg) {
  console.log('Body: ' + msg.data);
  console.log('Properties: ' + JSON.stringify(msg.properties));
  client.complete(msg, function (err) {
    if (err) {
      console.error('complete error: ' + err.toString());
    } else {
      console.log('complete sent');
    }
  });
});
```

A saída do corte de código anterior mostra a carga útil com os valores **StartTime** e **Bank.** A lista de propriedades inclui o nome de comando no item da lista **de nomes de método:**

```output
Body: {"StartTime":"2021-01-06T06:00:00.000Z","Bank":2}
Properties: {"propertyList":[{"key":"iothub-ack","value":"none"},{"key":"method-name","value":"GenerateDiagnostics"}]}
```

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a usar comandos na sua aplicação Azure IoT Central, consulte [Payloads](concepts-telemetry-properties-commands.md) para saber mais sobre parâmetros de comando e [Crie e conecte uma aplicação do cliente à sua aplicação Azure IoT Central](tutorial-connect-device.md) para ver amostras de código completas em diferentes idiomas.
