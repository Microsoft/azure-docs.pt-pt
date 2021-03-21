---
title: Gémeos do módulo monitor - Azure IoT Edge
description: Como interpretar gémeos de dispositivos e gémeos módulos para determinar a conectividade e a saúde.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 05/29/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 0b7013979199eefa873a651d99e87dc8b2c47856
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103201605"
---
# <a name="monitor-module-twins"></a>Monitorizar módulos duplos

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Os gémeos módulos no Azure IoT Hub permitem monitorizar a conectividade e a saúde das suas implementações IoT Edge. Os gémeos módulos armazenam informações úteis no seu hub IoT sobre o desempenho dos seus módulos de funcionamento. O [agente IoT Edge](iot-edge-runtime.md#iot-edge-agent) e os módulos de execução do hub [IoT Edge](iot-edge-runtime.md#iot-edge-hub) mantêm cada um os seus gémeos módulos `$edgeAgent` `$edgeHub` e, respectivamente:

* `$edgeAgent` contém dados de saúde e conectividade sobre o agente IoT Edge e os módulos de tempo de funcionação do hub IoT Edge, e os seus módulos personalizados. O agente IoT Edge é responsável por implantar os módulos, monitorizá-los e reportar o estado de ligação ao seu hub Azure IoT.
* `$edgeHub` contém dados sobre comunicações entre o hub IoT Edge em execução num dispositivo e o seu hub Azure IoT. Isto inclui o processamento de mensagens recebidas de dispositivos a jusante. O hub IoT Edge é responsável pelo processamento das comunicações entre o Azure IoT Hub e os dispositivos e módulos IoT Edge.

Os dados são organizados em metadados, tags, juntamente com conjuntos de propriedade desejados e reportados nas estruturas JSON dos gémeos do módulo. As propriedades desejadas especificadas no seu deployment.jsem ficheiro são copiadas para os gémeos do módulo. O agente IoT Edge e o hub IoT Edge atualizam cada uma as propriedades reportadas para os seus módulos.

Da mesma forma, as propriedades desejadas especificadas para os seus módulos personalizados no deployment.jsem ficheiro são copiadas para o seu módulo twin, mas a sua solução é responsável por fornecer os seus valores de propriedade reportados.

Este artigo descreve como rever os gémeos módulos no portal Azure, Azure CLI, e no Código do Estúdio Visual. Para obter informações sobre a monitorização da forma como os seus dispositivos recebem as implementações, consulte as [implementações do Monitor IoT Edge](how-to-monitor-iot-edge-deployments.md). Para uma visão geral sobre o conceito de gémeos módulos, consulte [Compreender e usar gémeos módulos no IoT Hub.](../iot-hub/iot-hub-devguide-module-twins.md)

> [!TIP]
> As propriedades reportadas de um módulo de tempo de execução podem ficar em mau estado se um dispositivo IoT Edge for desligado do seu hub IoT. Pode [verificar](how-to-edgeagent-direct-method.md#ping) o `$edgeAgent` módulo para determinar se a ligação foi perdida.

## <a name="monitor-runtime-module-twins"></a>Monitor gémeos módulos de tempo de execução

Para resolver problemas de conectividade de implementação, reveja o agente IoT Edge e os gémeos do módulo de tempo de execução do hub IoT Edge e, em seguida, abave para outros módulos.

### <a name="monitor-iot-edge-agent-module-twin"></a>Monitor IoT Edge agent módulo twin

O JSON seguinte mostra o `$edgeAgent` módulo twin em Visual Studio Code com a maioria das secções JSON colapsadas.

```json
{
  "deviceId": "Windows109",
  "moduleId": "$edgeAgent",
  "etag": "AAAAAAAAAAU=",
  "deviceEtag": "NzgwNjA1MDUz",
  "status": "enabled",
  "statusUpdateTime": "0001-01-01T00:00:00Z",
  "connectionState": "Disconnected",
  "lastActivityTime": "0001-01-01T00:00:00Z",
  "cloudToDeviceMessageCount": 0,
  "authenticationType": "sas",
  "x509Thumbprint": {
    "primaryThumbprint": null,
    "secondaryThumbprint": null
  },
  "version": 53,
  "properties": {
    "desired": { "···" },
    "reported": {
      "schemaVersion": "1.0",
      "version": { "···" },
      "lastDesiredStatus": { "···" },
      "runtime": { "···" },
      "systemModules": {
        "edgeAgent": { "···" },
        "edgeHub": { "···" }
      },
      "lastDesiredVersion": 5,
      "modules": {
        "SimulatedTemperatureSensor": { "···" }
      },
      "$metadata": { "···" },
      "$version": 48
    }
  }
}
```

O JSON pode ser descrito nas seguintes secções, a partir do topo:

* Metadados - Contém dados de conectividade. Curiosamente, o estado de ligação do agente IoT Edge está sempre num estado desligado: `"connectionState": "Disconnected"` . A razão pela qual o estado de ligação diz respeito a mensagens de dispositivo-a-nuvem (D2C) e o agente IoT Edge não envia mensagens D2C.
* Propriedades - Contém as `desired` `reported` subsecções e subsecções.
* Propriedades.desejadas - (mostrado colapso) Valores de propriedade esperados definidos pelo operador no deployment.jsem arquivo.
* Propriedades.reportados - Os valores de propriedade mais recentes reportados pelo agente IoT Edge.

Tanto as `properties.desired` `properties.reported` secções têm uma estrutura semelhante e contêm metadados adicionais para o esquema, versão e informação de tempo de execução. Também está incluída a `modules` secção para quaisquer módulos personalizados (tais `SimulatedTemperatureSensor` como), e a secção para `systemModules` e `$edgeAgent` os `$edgeHub` módulos de tempo de execução.

Ao comparar os valores de propriedade reportados com os valores pretendidos, pode determinar discrepâncias e identificar desconexões que podem ajudá-lo a resolver problemas. Ao fazer estas comparações, verifique o `$lastUpdated` valor reportado na secção da propriedade que está a `metadata` investigar.

As seguintes propriedades são importantes para examinar para a resolução de problemas:

* **código de saída** - Qualquer valor que não seja zero indica que o módulo parou com uma falha. No entanto, os códigos de erro 137 ou 143 são utilizados se um módulo tiver sido intencionalmente definido para um estado de paragem.

* **lastStartTimeUtc** - Mostra a **DataTime** que o recipiente foi iniciado pela última vez. Este valor é 0001-01-01T00:00:00Z se o contentor não foi iniciado.

* **lastExitTimeUtc** - Mostra a **DataTime** que o recipiente terminou pela última vez. Este valor é 0001-01-01T00:00:00Z se o contentor estiver em funcionamento e nunca tiver sido parado.

* **runtimeStatus** - Pode ser um dos seguintes valores:

    | Valor | Descrição |
    | --- | --- |
    | desconhecido | Estado predefinido até que a implementação seja criada. |
    | backoff | O módulo está programado para começar, mas não está em execução. Este valor é útil para um módulo que sofre alterações de estado no reinício. Quando um módulo de falha aguarda o reinício durante o período de arrefecimento, o módulo estará em estado de recuo. |
    | correndo | Indica que o módulo está em funcionamento. |
    | insalubre | Indica que uma verificação da sonda de saúde falhou ou foi cronometrada. |
    | parou | Indica que o módulo saiu com sucesso (com um código de saída zero). |
    | falhou | Indica que o módulo saiu com um código de saída de falha (não zero). O módulo pode transitar de volta para o recuo deste estado dependendo da política de reinício em vigor. Este estado pode indicar que o módulo sofreu um erro irrecuperável. A falha ocorre quando o Agente de Monitorização da Microsoft (MMA) já não consegue reanimar o módulo, requerendo uma nova implementação. |

Consulte [as propriedades reportadas pela EdgeAgent](module-edgeagent-edgehub.md#edgeagent-reported-properties) para mais detalhes.

### <a name="monitor-iot-edge-hub-module-twin"></a>Monitor IoT Edge hub módulo twin

O JSON seguinte mostra o `$edgeHub` módulo twin em Visual Studio Code com a maioria das secções JSON colapsadas.

```json
{
  "deviceId": "Windows109",
  "moduleId": "$edgeHub",
  "etag": "AAAAAAAAAAU=",
  "deviceEtag": "NzgwNjA1MDU2",
  "status": "enabled",
  "statusUpdateTime": "0001-01-01T00:00:00Z",
  "connectionState": "Connected",
  "lastActivityTime": "0001-01-01T00:00:00Z",
  "cloudToDeviceMessageCount": 0,
  "authenticationType": "sas",
  "x509Thumbprint": {
    "primaryThumbprint": null,
    "secondaryThumbprint": null
  },
  "version": 102,
    "properties": {
      "desired": { "···" },
      "reported": {
        "schemaVersion": "1.0",
        "version": { "···" },
      "lastDesiredVersion": 5,
      "lastDesiredStatus": { "···" },
      "clients": {
        "Windows109/SimulatedTemperatureSensor": {
          "status": "Disconnected",
          "lastConnectedTimeUtc": "2020-04-08T21:42:42.1743956Z",
          "lastDisconnectedTimeUtc": "2020-04-09T07:02:42.1398325Z"
        }
      },
      "$metadata": { "···" },
      "$version": 97
    }
  }
}

```

O JSON pode ser descrito nas seguintes secções, a partir do topo:

* Metadados - Contém dados de conectividade.

* Propriedades - Contém as `desired` `reported` subsecções e subsecções.
* Propriedades.desejadas - (mostrado colapso) Valores de propriedade esperados definidos pelo operador no deployment.jsem arquivo.
* Propriedades.reportados - Os valores mais recentes de propriedade reportados pelo ioT Edge hub.

Se estiver a ter problemas com os seus dispositivos a jusante, examinar estes dados seria um bom ponto de partida.

## <a name="monitor-custom-module-twins"></a>Monitorize gémeos de módulo personalizado

As informações sobre a conectividade dos seus módulos personalizados são mantidas no módulo de agente IoT Edge twin. O módulo twin para o seu módulo personalizado é usado principalmente para manter dados para a sua solução. As propriedades desejadas que definiu no seu deployment.jsno ficheiro refletem-se no módulo twin, e o seu módulo pode atualizar os valores de propriedade reportados conforme necessário.

Pode utilizar a sua linguagem de programação preferida com os [SDKs de dispositivo de hub Azure IoT](../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks) para atualizar os valores de propriedade reportados no módulo twin, com base no código de aplicação do seu módulo. O seguinte procedimento utiliza o Azure SDK para .NET para o fazer, utilizando código a partir do módulo [SimulaçãoTemperatureSensor:](https://github.com/Azure/iotedge/blob/dd5be125df165783e4e1800f393be18e6a8275a3/edge-modules/SimulatedTemperatureSensor/src/Program.cs)

1. Crie uma instância do [MóduloClient](/dotnet/api/microsoft.azure.devices.client.moduleclient) com o método [CreateFromEnvironmentAysnc.](/dotnet/api/microsoft.azure.devices.client.moduleclient.createfromenvironmentasync)

1. Obtenha uma coleção das propriedades do módulo twin com o método [GetTwinAsync.](/dotnet/api/microsoft.azure.devices.client.moduleclient.gettwinasync)

1. Crie um ouvinte (passando uma chamada) para capturar alterações nas propriedades desejadas com o método [SetDesiredPropertyUpdateCallbackAsync.](/dotnet/api/microsoft.azure.devices.client.deviceclient.setdesiredpropertyupdatecallbackasync)

1. No seu método de retorno, atualize as propriedades relatadas no módulo twin com o método [UpdateReportedPropertiesAsync,](/dotnet/api/microsoft.azure.devices.client.moduleclient) passando uma [TwinCollection](/dotnet/api/microsoft.azure.devices.shared.twincollection) dos valores de propriedade que pretende definir.

## <a name="access-the-module-twins"></a>Aceda aos gémeos módulos

Você pode rever o JSON para gémeos módulos no Azure IoT Hub, em Visual Studio Code, e com Azure CLI.

### <a name="monitor-in-azure-iot-hub"></a>Monitor em Azure IoT Hub

Para ver o JSON para o módulo twin:

1. Inscreva-se no [portal Azure](https://portal.azure.com) e navegue até ao seu hub IoT.
1. Selecione **IoT Edge** no menu do painel esquerdo.
1. No **separador dispositivos IoT Edge,** selecione o **ID** do dispositivo com os módulos que pretende monitorizar.
1. Selecione o nome do módulo no **separador Módulos** e, em seguida, selecione **Módulo Identidade Twin** da barra de menu superior.

  ![Selecione um módulo twin para ver no portal Azure](./media/how-to-monitor-module-twins/select-module-twin.png)

Se vir a mensagem "Uma identidade de módulo não existe para este módulo", este erro indica que a solução back-end já não está disponível que originalmente criou a identidade.

### <a name="monitor-module-twins-in-visual-studio-code"></a>Monitor gémeos módulos no Código do Estúdio Visual

Para rever e editar um módulo twin:

1. Se ainda não estiver instalado, instale a extensão de [ferramentas Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) para código de estúdio visual.
1. No **Explorer**, expanda o **Azure IoT Hub** e, em seguida, expanda o dispositivo com o módulo que pretende monitorizar.
1. Clique com o botão direito no módulo e **selecione Editar Módulo Twin**. Um ficheiro temporário do módulo twin é descarregado para o seu computador e exibido no Código do Estúdio Visual.

  ![Obtenha um módulo twin para editar no Código do Estúdio Visual](./media/how-to-monitor-module-twins/edit-module-twin-vscode.png)

Se escoar alterações, selecione **Update Module Twin** acima do código no editor para guardar alterações no seu hub IoT.

  ![Atualize um módulo twin no Código do Estúdio Visual](./media/how-to-monitor-module-twins/update-module-twin-vscode.png)

### <a name="monitor-module-twins-in-azure-cli"></a>Monitor gémeos módulos em Azure CLI

Para ver se o IoT Edge está em funcionamento, use o [hub az iot invocando](how-to-edgeagent-direct-method.md#ping) o método do módulo para pingar o agente IoT Edge.

A estrutura [az iot hub módulo-twin](/cli/azure/ext/azure-iot/iot/hub/module-twin) fornece estes comandos:

* **az iot hub módulo-twin show** - Mostre uma definição de módulo twin.
* **az iot hub module-twin update** - Atualize uma definição de módulo twin.
* **az iot hub módulo-twin substitua** - Substitua uma definição de módulo twin por um JSON alvo.

## <a name="next-steps"></a>Passos seguintes

Aprenda a [comunicar com o EdgeAgent utilizando métodos diretos incorporados.](how-to-edgeagent-direct-method.md)
