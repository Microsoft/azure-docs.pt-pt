---
title: Desdobre a ponte do dispositivo Central Azure IoT | Microsoft Docs
description: Coloque a ponte do dispositivo IoT Central para ligar outras nuvens IoT à sua aplicação IoT Central. Outras nuvens IoT incluem Sigfox, Particle Device Cloud e The Things Network.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 04/19/2021
ms.topic: how-to
ms.openlocfilehash: 6b535ecb80fae9f55eb6ab11751c26e0c6d0e9e5
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107713745"
---
# <a name="use-the-iot-central-device-bridge-to-connect-other-iot-clouds-to-iot-central"></a>Use a ponte do dispositivo IoT Central para ligar outras nuvens IoT à IoT Central

*Este artigo aplica-se aos administradores.*

## <a name="azure-iot-central-device-bridge"></a>Ponte do dispositivo Azure IoT Central

A ponte do dispositivo IoT Central é uma solução de código aberto que liga outras nuvens IoT à sua aplicação IoT Central. Exemplos de outras nuvens IoT incluem [Sigfox,](https://www.sigfox.com/) [Particle Device Cloud](https://www.particle.io/)e The Things [Network](https://www.thethingsnetwork.org/). A ponte do dispositivo funciona reencaminhando dados de dispositivos ligados a outras nuvens IoT até à sua aplicação IoT Central. A ponte do dispositivo apenas encaminha dados para a IoT Central, não envia comandos ou atualizações de propriedade da IoT Central de volta para os dispositivos.

A ponte do dispositivo permite combinar a potência da IoT Central com dispositivos como dispositivos de rastreio de ativos ligados à rede de área de baixa potência da Sigfox, dispositivos de monitorização da qualidade do ar na Nuvem de Dispositivos de Partículas ou dispositivos de monitorização da humidade do solo na Rede de Coisas. Pode utilizar funcionalidades de aplicação IoT Central, tais como regras e análises sobre os dados, criar fluxos de trabalho em aplicações Power Automamate e Azure Logic ou exportar os dados.

A solução de ponte do dispositivo fornece vários recursos Azure na sua subscrição Azure que trabalham em conjunto para transformar e encaminhar mensagens de dispositivo para a IoT Central.

## <a name="prerequisites"></a>Pré-requisitos

Para completar os passos neste guia de como fazer, precisa de uma subscrição ativa do Azure.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Complete o quickstart [da aplicação Create azure IoT Central](./quick-deploy-iot-central.md) para criar uma aplicação IoT Central utilizando o modelo **de aplicação Custom > Custom.**

## <a name="overview"></a>Descrição Geral

A ponte de dispositivos IoT Central é uma solução de código aberto no GitHub. Utiliza um modelo personalizado do Azure Resource Manager para implementar vários recursos para a sua subscrição Azure, incluindo uma aplicação de função Azure.

A aplicação de função é a peça central da ponte do dispositivo. Recebe pedidos HTTP POST de outras plataformas IoT através de um webhook simples. O repositório [da Ponte Central de Dispositivos Azure IoT](https://github.com/Azure/iotc-device-bridge) inclui exemplos que mostram como ligar nuvens de Sigfox, Particle e The Things Network. Pode estender esta solução para ligar à sua nuvem IoT personalizada se a sua plataforma puder enviar pedidos HTTP POST para a sua aplicação de função.

A aplicação de função transforma os dados num formato aceite pela IoT Central e encaminha-os utilizando o serviço de fornecimento de dispositivos e apis do cliente do dispositivo:

:::image type="content" source="media/howto-build-iotc-device-bridge/azure-function.png" alt-text="Screenshot de Azure funções screenshot.":::

Se a sua aplicação IoT Central reconhecer o ID do dispositivo na mensagem reencaminhada, a telemetria do dispositivo aparece na IoT Central. Se o ID do dispositivo não for reconhecido pela sua aplicação IoT Central, a aplicação de função tenta registar um novo dispositivo com o ID do dispositivo. O novo dispositivo aparece como um **dispositivo não associado** na página **dispositivos** na sua aplicação IoT Central. A partir da página **Dispositivos,** pode associar o novo dispositivo a um modelo de dispositivo e, em seguida, ver a telemetria.

## <a name="deploy-the-device-bridge"></a>Desdobre a ponte do dispositivo

Para implantar a ponte do dispositivo para a sua subscrição:

1. Na sua aplicação IoT Central, navegue para a página **de Ligação do Dispositivo > Administração.**

    1. Tome nota do **ID Scope**. Utilize este valor quando se implanta a ponte do dispositivo.

    1. Na mesma página, abra o grupo de inscrição **SAS-IoT-Devices.** Na página do grupo **SAS-IoT-Devices,** copie a **chave principal**. Utilize este valor quando se implanta a ponte do dispositivo.

1. Utilize o botão **Implementar para Azure** abaixo para abrir o modelo personalizado de Gestor de Recursos que implementa a aplicação de função para a sua subscrição. Utilize o **âmbito de id e** a chave **primária** do passo anterior:

    [![Implementar no Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fiotc-device-bridge%2Fmaster%2Fazuredeploy.json)

Após a implementação concluída, é necessário instalar os pacotes NPM que a função requer:

1. No portal Azure, abra a app de função que foi implementada para a sua subscrição. Em seguida, navegue para **ferramentas de desenvolvimento > consola.** Na consola, executar os seguintes comandos para instalar os pacotes:

    ```bash
    cd IoTCIntegration
    npm install
    ```

    Estes comandos podem demorar vários minutos a ser executados. Pode ignorar com segurança quaisquer mensagens de aviso.

1. Após o fim da instalação do pacote, **selecione Reiniciar** na página **de visão geral** da aplicação de funções:

    :::image type="content" source="media/howto-build-iotc-device-bridge/restart.png" alt-text="Screenshot de Restart.":::

1. A função está agora pronta a ser utilizada. Os sistemas externos podem utilizar pedidos HTTP POST para enviar dados do dispositivo através da ponte do dispositivo para a sua aplicação IoT Central. Para obter o URL de função, navegue para **Funções > IoTCIntegration > Código + Teste > Obter URL de função**:

    :::image type="content" source="media/howto-build-iotc-device-bridge/get-function-url.png" alt-text="Screenshot de Get Function URL.":::

Os corpos de mensagens enviados para a ponte do dispositivo devem ter o seguinte formato:

```json
"device": {
  "deviceId": "my-cloud-device"
},
"measurements": {
  "temp": 20.31,
  "pressure": 50,
  "humidity": 8.5,
  "ledColor": "blue"
}
```

Cada tecla do `measurements` objeto deve corresponder ao nome de um tipo de telemetria no modelo do dispositivo na aplicação IoT Central. Esta solução não suporta especificar o ID da interface no corpo da mensagem. Assim, se duas interfaces diferentes tiverem um tipo de telemetria com o mesmo nome, a medição aparece em ambos os fluxos de telemetria na sua aplicação IoT Central.

Pode incluir um `timestamp` campo no corpo para especificar a data e hora da mensagem utc. Este campo deve estar no formato ISO 8601. Por exemplo, `2020-06-08T20:16:54.602Z`. Se não incluir uma hora marcada, a data e a hora atuais são usadas.

Pode incluir um `modelId` campo no corpo. Utilize este campo para associar o dispositivo a um modelo de dispositivo durante o provisionamento. Esta funcionalidade é suportada apenas por [aplicações V3.](howto-get-app-info.md)

O `deviceId` deve ser alfanumérico, minúsculo, e pode conter hífenes.

Se não incluir o campo, ou se a `modelId` IoT Central não reconhecer o ID do modelo, então uma mensagem com um não reconhecido `deviceId` cria um novo dispositivo não _associado_ na IoT Central. Um operador pode migrar manualmente o dispositivo para o modelo correto do dispositivo. Para saber mais, consulte [Gerir os dispositivos na sua aplicação Azure IoT Central > dispositivos migratórios para um modelo](howto-manage-devices.md).

Nas [aplicações V2,](howto-get-app-info.md)o novo dispositivo aparece na página de **dispositivos > dispositivos não-associados.** **Selecione Associate** e escolha um modelo de dispositivo para começar a receber telemetria de entrada a partir do dispositivo.

> [!NOTE]
> Até que o dispositivo esteja associado a um modelo, todas as chamadas HTTP para a função retornam um estado de erro 403.

Para ligar o registo da aplicação de função com o Application Insights, navegue para **monitorizar > registar registos** na sua aplicação de função no portal Azure. Selecione **Ligue as informações de aplicação**.

## <a name="provisioned-resources"></a>Recursos provisões

O modelo do Gestor de Recursos fornece os seguintes recursos na sua subscrição Azure:

* Aplicação de Funções
* Plano do Serviço de Aplicações
* Conta de armazenamento
* Key Vault

O cofre-chave armazena a chave do grupo SAS para a sua aplicação IoT Central.

A Aplicação de Função funciona num [plano de consumo.](https://azure.microsoft.com/pricing/details/functions/) Embora esta opção não ofereça recursos computacional dedicados, permite que a ponte do dispositivo manuseie centenas de mensagens de dispositivos por minuto, adequadas para frotas mais pequenas de dispositivos ou dispositivos que enviam mensagens com menos frequência. Se a sua aplicação depender do streaming de um grande número de mensagens de dispositivo, substitua o plano de consumo por um plano de serviço de [Aplicação](https://azure.microsoft.com/pricing/details/app-service/windows/)dedicado. Este plano oferece recursos compute dedicados, que dão tempos de resposta mais rápidos ao servidor. Utilizando um Plano de Serviço de Aplicações padrão, o desempenho máximo observado da função Azure neste repositório foi de cerca de 1.500 mensagens de dispositivo por minuto. Para saber mais, consulte [as opções de hospedagem da Função Azure](../../azure-functions/functions-scale.md).

Para utilizar um Plano de Serviço de Aplicações dedicado em vez de um plano de consumo, edite o modelo personalizado antes de implementar. Selecione  **o modelo de edição**.

:::image type="content" source="media/howto-build-iotc-device-bridge/edit-template.png" alt-text="Screenshot do modelo de edição.":::

Substitua o seguinte segmento:

```json
{
  "type": "Microsoft.Web/serverfarms",
  "apiVersion": "2015-04-01",
  "name": "[variables('planName')]",
  "location": "[resourceGroup().location]",
  "properties": {
    "name": "[variables('planName')]",
    "computeMode": "Dynamic",
    "sku": "Dynamic"
  }
},
```

com o

```json
{
  "type": "Microsoft.Web/serverfarms",
  "sku": {
      "name": "S1",
      "tier": "Standard",
      "size": "S1",
      "family": "S",
      "capacity": 1
  },
  "kind": "app",
  "name": "[variables('planName')]",
  "apiVersion": "2016-09-01",
  "location": "[resourceGroup().location]",
  "tags": {
      "iotCentral": "device-bridge",
      "iotCentralDeviceBridge": "app-service-plan"
  },
  "properties": {
      "name": "[variables('planName')]"
  }
},
```

Em seguida, edite o modelo para incluir `"alwaysOn": true` na configuração para o `functionapp` recurso sob a `"properties": {"SiteConfig": {...}}` [configuração AlwaysOn](https://github.com/Azure/Azure-Functions/wiki/Enable-Always-On-when-running-on-dedicated-App-Service-Plan) garante que a aplicação de função está sempre em execução.

## <a name="examples"></a>Exemplos

Os exemplos a seguir descrevem como configurar a ponte do dispositivo para várias nuvens IoT:

### <a name="example-1-connecting-particle-devices-through-the-device-bridge"></a>Exemplo 1: Ligar dispositivos de partículas através da ponte do dispositivo

Para ligar um dispositivo de partícula através da ponte do dispositivo à IoT Central, vá à consola De Partículas e crie uma nova integração webhook. Desa estada o **Formato de Pedido** para **JSON**.  Em **Definições Avançadas,** utilize o seguinte formato personalizado do corpo:

```json
{
  "device": {
    "deviceId": "{{{PARTICLE_DEVICE_ID}}}"
  },
  "measurements": {
    "{{{PARTICLE_EVENT_NAME}}}": {{{PARTICLE_EVENT_VALUE}}}
  }
}
```

Cole no URL de **função** a partir da sua aplicação de função Azure e vê dispositivos de partículas aparecerem como dispositivos não subordinados no IoT Central. Para saber mais, veja o [Here's How to Integrate your Particle-powered projects with Azure IoT Central](https://blog.particle.io/2019/09/26/integrate-particle-with-azure-iot-central/) blog post.

### <a name="example-2-connecting-sigfox-devices-through-the-device-bridge"></a>Exemplo 2: Ligar dispositivos Sigfox através da ponte do dispositivo

Algumas plataformas podem não permitir especificar o formato das mensagens de dispositivo enviadas através de um webhook. Para estes sistemas, deve converter a carga útil da mensagem para o formato corporal esperado antes que a ponte do dispositivo o processe. Pode fazer a conversão na mesma função Azure que executa a ponte do dispositivo.

Esta secção mostra como converter a carga útil de uma integração webhook Sigfox para o formato corporal esperado pela ponte do dispositivo. A nuvem Sigfox transmite dados do dispositivo num formato de corda hexadecimal. Por conveniência, a ponte do dispositivo inclui uma função de conversão para este formato, que aceita um subconjunto dos possíveis tipos de campo numa carga útil do dispositivo Sigfox: `int` e `uint` de 8, 16, 32 ou 64 bits; `float` de 32 bits ou 64 bits; pequeno-endian e big-endian. Para processar mensagens a partir de uma integração webhook Sigfox, faça as seguintes alterações ao ficheiro _IoTCIntegration/index.js_ na aplicação de função.

Para converter a carga útil da mensagem, adicione o seguinte código antes da chamada para a `handleMessage` linha 21, substituindo `payloadDefinition` pela definição de carga útil Sigfox:

```javascript
const payloadDefinition = 'gforce::uint:8 lat::uint:8 lon::uint:16'; // Replace this with your payload definition

req.body = {
    device: {
        deviceId: req.body.device
    },
    measurements: require('./converters/sigfox')(payloadDefinition, req.body.data)
};
```

Os dispositivos Sigfox esperam um `204` código de resposta. Adicione o seguinte código após a chamada para a `handleMessage` linha 21:

```javascript
context.res = {
    status: 204
};
```

### <a name="example-3-connecting-devices-from-the-things-network-through-the-device-bridge"></a>Exemplo 3: Dispositivos de ligação da Rede de Coisas através da ponte do dispositivo

Para ligar os dispositivos da Rede coisas à IoT Central:

* Adicione uma nova integração HTTP à sua aplicação na Rede de Coisas: **Integrações > aplicações > adicionar integração > integração HTTP**.
* Certifique-se de que a sua aplicação inclui uma função de descodificador que converte automaticamente a carga útil das mensagens do seu dispositivo para JSON antes de ser enviada para a Função Azure: **Aplicação > Funções de carga útil > descodificador**.

A amostra que se segue mostra uma função de descodificador JavaScript que pode utilizar para descodificar tipos numéricos comuns a partir de dados binários:

```javascript
function Decoder(bytes, port) {
  function bytesToFloat(bytes, decimalPlaces) {
    var bits = (bytes[3] << 24) | (bytes[2] << 16) | (bytes[1] << 8) | bytes[0];
    var sign = (bits >>> 31 === 0) ? 1.0 : -1.0;
    var e = bits >>> 23 & 0xff;
    var m = (e === 0) ? (bits & 0x7fffff) << 1 : (bits & 0x7fffff) | 0x800000;
    var f = Math.round((sign * m * Math.pow(2, e - 150)) * Math.pow(10, decimalPlaces)) / Math.pow(10, decimalPlaces);
    return f;
  }

  function bytesToInt32(bytes, signed) {
    var bits = bytes[0] | (bytes[1] << 8) | (bytes[2] << 16) | (bytes[3] << 24);
    var sign = 1;

    if (signed && bits >>> 31 === 1) {
      sign = -1;
      bits = bits & 0x7FFFFFFF;
    }

    return bits * sign;
  }

  function bytesToShort(bytes, signed) {
    var bits = bytes[0] | (bytes[1] << 8);
    var sign = 1;

    if (signed && bits >>> 15 === 1) {
      sign = -1;
      bits = bits & 0x7FFF;
    }

    return bits * sign;
  }

  return {
    temperature: bytesToFloat(bytes.slice(0, 4), 2),
    presscounter: bytesToInt32(bytes.slice(4, 8), true),
    blueLux: bytesToShort(bytes.slice(8, 10), false)
  };
}
```

Depois de definir a integração, adicione o seguinte código antes da chamada para `handleMessage` a linha 21 do ficheiro *IoTCIntegration/index.js* da sua aplicação de função Azure. Este código traduz o corpo da sua integração HTTP para o formato esperado.

```javascript
device: {
    deviceId: req.body.hardware_serial.toLowerCase()
},
measurements: req.body.payload_fields
};
```

## <a name="limitations"></a>Limitações

A ponte do dispositivo apenas envia mensagens para a IoT Central, e não envia mensagens de volta para os dispositivos. É por isso que as propriedades e comandos não funcionam para dispositivos que se ligam à IoT Central através desta ponte do dispositivo. Como as operações gémeas do dispositivo não são suportadas, não é possível atualizar as propriedades do dispositivo através da ponte do dispositivo. Para utilizar estas funcionalidades, um dispositivo deve ligar-se diretamente à IoT Central utilizando um dos [SDKs do dispositivo Azure IoT](../../iot-hub/iot-hub-devguide-sdks.md).

## <a name="next-steps"></a>Passos seguintes
Agora que aprendeu a implantar a ponte do dispositivo IoT Central, eis o próximo passo sugerido:

> [!div class="nextstepaction"]
> [Faça a gestão dos seus dispositivos](howto-manage-devices.md)
