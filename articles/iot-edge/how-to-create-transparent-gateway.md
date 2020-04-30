---
title: Criar dispositivo de gateway transparente - Azure IoT Edge [ Azure IoT Edge ] Microsoft Docs
description: Utilize um dispositivo Azure IoT Edge como um portal transparente que pode processar informações a partir de dispositivos a jusante
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/03/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: e563e67b5e951b43e5782f8c845c8ec46ff3e9bb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81687167"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>Configurar um dispositivo IoT Edge para atuar como um gateway transparente

Este artigo fornece instruções detalhadas para configurar um dispositivo IoT Edge para funcionar como uma porta de entrada transparente para outros dispositivos comunicarem com o IoT Hub. Este artigo utiliza o termo *Gateway IoT Edge* para se referir a um dispositivo IoT Edge configurado como um gateway transparente. Para mais informações, consulte [como um dispositivo IoT Edge pode ser usado como porta de entrada](./iot-edge-as-gateway.md).

>[!NOTE]
>Atualmente:
>
> * Os dispositivos com borda não podem ligar-se aos gateways IoT Edge.
> * Os dispositivos a jusante não podem usar o upload de ficheiros.

Existem três passos gerais para estabelecer uma ligação transparente e bem sucedida. Este artigo cobre o primeiro passo:

1. **O dispositivo gateway precisa de ser capaz de se ligar de forma segura a dispositivos a jusante, receber comunicações de dispositivos a jusante e direcionar mensagens para o destino adequado.**
2. O dispositivo a jusante precisa de ter uma identidade de dispositivo para poder autenticar com o IoT Hub, e saber comunicar através do seu dispositivo gateway. Para mais informações, consulte [Authenticate um dispositivo a jusante para o Hub Azure IoT](how-to-authenticate-downstream-device.md).
3. O dispositivo a jusante tem de se ligar ao seu dispositivo de gateway de forma segura. Para mais informações, consulte [Ligue um dispositivo a jusante a um gateway Azure IoT Edge](how-to-connect-downstream-device.md).

Para que um dispositivo funcione como porta de entrada, tem de ser capaz de se ligar de forma segura aos seus dispositivos a jusante. O Azure IoT Edge permite-lhe utilizar uma infraestrutura de chaves públicas (PKI) para estabelecer ligações seguras entre dispositivos. Neste caso, estamos a permitir que um dispositivo a jusante se conectem a um dispositivo IoT Edge, atuando como uma porta de entrada transparente. Para manter uma segurança razoável, o dispositivo a jusante deve confirmar a identidade do dispositivo gateway. Esta verificação de identidade impede que os seus dispositivos se conectem a gateways potencialmente maliciosos.

Um dispositivo a jusante num cenário transparente de gateway pode ser qualquer aplicação ou plataforma que tenha uma identidade criada com o serviço de nuvem [Azure IoT Hub.](https://docs.microsoft.com/azure/iot-hub) Em muitos casos, estas aplicações utilizam o [dispositivo Azure IoT SDK](../iot-hub/iot-hub-devguide-sdks.md). Para todos os efeitos práticos, um dispositivo a jusante pode mesmo ser uma aplicação em execução no próprio dispositivo ioT Edge gateway. No entanto, um dispositivo IoT Edge não pode estar a jusante de um gateway IoT Edge.

Pode criar qualquer infraestrutura de certificado que permita a confiança necessária para a sua topologia de gateway de dispositivos. Neste artigo, assumimos a mesma configuração de certificado que utilizaria para permitir a [segurança X.509 CA](../iot-hub/iot-hub-x509ca-overview.md) no IoT Hub, que envolve um certificado De CA X.509 associado a um hub IoT específico (a raiz de ioT ca), uma série de certificados assinados com este CA, e um CA para o dispositivo IoT Edge.

![Configuração do certificado gateway](./media/how-to-create-transparent-gateway/gateway-setup.png)

>[!NOTE]
>O termo "root CA" utilizado ao longo deste artigo refere-se ao certificado público de autoridade mais alta da cadeia de certificados PKI, e não necessariamente à raiz de certificado de uma autoridade sindical. Em muitos casos, trata-se, na verdade, de um certificado público intermédio da AC.

O daemon de segurança IoT Edge utiliza o certificado CA do dispositivo IoT Edge para assinar um certificado CA de carga de trabalho, que por sua vez assina um certificado de servidor para o hub IoT Edge. O portal apresenta o seu certificado de servidor ao dispositivo a jusante durante o início da ligação. O dispositivo a jusante verifica para se certificar de que o certificado de servidor faz parte de uma cadeia de certificados que se encontra no certificado CA raiz. Este processo permite que o dispositivo a jusante confirme que o portal provém de uma fonte fidedigna. Para mais informações, consulte [entenda como o Azure IoT Edge utiliza certificados](iot-edge-certs.md).

Os seguintes passos passam pelo processo de criação dos certificados e instalação nos locais certos na porta de entrada. Pode utilizar qualquer máquina para gerar os certificados e, em seguida, copiá-los para o seu dispositivo IoT Edge.

## <a name="prerequisites"></a>Pré-requisitos

Um dispositivo Azure IoT Edge, configurado com certificados de [produção.](how-to-manage-device-certificates.md)

## <a name="deploy-edgehub-to-the-gateway"></a>Desloque o EdgeHub para o portal

Quando instala o IoT Edge num dispositivo, apenas um módulo de sistema começa automaticamente: o agente IoT Edge. Assim que criar a primeira implementação mais um dispositivo, o segundo módulo de sistema, o hub IoT Edge, também é iniciado.

O hub IoT Edge é responsável por receber mensagens de entrada de dispositivos a jusante e encaminhar-as para o próximo destino. Se o módulo **EdgeHub** não estiver em funcionamento no seu dispositivo, crie uma implementação inicial para o seu dispositivo. A implementação parecerá vazia porque não adiciona nenhum módulo, mas irá certificar-se de que ambos os módulos do sistema estão em funcionamento.

Pode verificar quais os módulos que estão a ser reparados num dispositivo, verificando os detalhes do dispositivo `iotedge list` no portal Azure, visualizando o estado do dispositivo no Visual Studio ou Visual Studio Code, ou executando o comando no próprio dispositivo.

Se o módulo **EdgeAgent** estiver a funcionar sem o módulo **EdgeHub,** utilize os seguintes passos:

1. No portal do Azure, navegue para o seu hub IoT.

2. Vá ao **IoT Edge** e selecione o seu dispositivo IoT Edge que pretende utilizar como porta de entrada.

3. Selecione **Módulos de Conjunto**.

4. Selecione **Seguinte**.

5. Na página de **rotas Especte,** deve ter uma rota predefinida que envia todas as mensagens de todos os módulos para o IoT Hub. Caso contrário, adicione o seguinte código e, em seguida, selecione **Seguinte**.

   ```JSON
   {
       "routes": {
           "route": "FROM /messages/* INTO $upstream"
       }
   }
   ```

6. Na página do **modelo de revisão,** selecione **Submeter**.

## <a name="open-ports-on-gateway-device"></a>Portas abertas no dispositivo gateway

Os dispositivos Standard IoT Edge não precisam de qualquer conectividade de entrada para funcionar, porque toda a comunicação com o IoT Hub é feita através de ligações de saída. Os dispositivos gateway são diferentes porque precisam de receber mensagens dos seus dispositivos a jusante. Se uma firewall estiver entre os dispositivos a jusante e o dispositivo gateway, então a comunicação também tem de ser possível através da firewall.

Para que um cenário de portal funcione, pelo menos um dos protocolos suportados pelo hub IoT Edge deve estar aberto para o tráfego de entrada a partir de dispositivos a jusante. Os protocolos suportados são MQTT, AMQP, HTTPS, MQTT sobre WebSockets e AMQP sobre WebSockets.

| Porta | Protocolo |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> Mqtt+WS <br> AMQP+WS |

## <a name="route-messages-from-downstream-devices"></a>Rotas de mensagens de dispositivos a jusante

O tempo de funcionamento do IoT Edge pode enviar mensagens enviadas a partir de dispositivos a jusante, tal como as mensagens enviadas por módulos. Esta funcionalidade permite-lhe realizar análises num módulo que corre no portal antes de enviar quaisquer dados para a nuvem.

Atualmente, a forma como envia mensagens enviadas por dispositivos a jusante é diferenciando-as das mensagens enviadas por módulos. As mensagens enviadas por módulos contêm uma propriedade do sistema chamada **connectionModuleId,** mas as mensagens enviadas por dispositivos a jusante não. Pode utilizar a cláusula WHERE da rota para excluir quaisquer mensagens que contenham essa propriedade do sistema.

A rota abaixo é um exemplo que enviaria mensagens de qualquer dispositivo a jusante para um módulo chamado `ai_insights`, e depois de `ai_insights` ioT Hub.

```json
{
    "routes":{
        "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")",
        "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream"
    }
}
```

Para obter mais informações sobre o encaminhamento de mensagens, consulte [módulos de implantação e estabeleça rotas](./module-composition.md#declare-routes).

## <a name="enable-extended-offline-operation"></a>Ativar o funcionamento offline prolongado

Começando com o [lançamento v1.0.4](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4) do tempo de funcionamento do IoT Edge, o dispositivo gateway e os dispositivos a jusante que lhe ligam podem ser configurados para um funcionamento offline prolongado.

Com esta capacidade, os módulos locais ou dispositivos a jusante podem reautenticar com o dispositivo IoT Edge conforme necessário e comunicar entre si usando mensagens e métodos mesmo quando desligados do hub IoT. Para mais informações, consulte [Compreender capacidades offline estendidas para dispositivos, módulos e dispositivos infantis IoT Edge](offline-capabilities.md).

Para permitir capacidades offline estendidas, estabelece uma relação pai-filho entre um dispositivo ioT Edge gateway e dispositivos a jusante que se ligarão a ele. Esses passos são explicados mais detalhadamente em [Authenticate um dispositivo a jusante para o Hub Azure IoT](how-to-authenticate-downstream-device.md).

## <a name="next-steps"></a>Passos seguintes

Agora que tem um dispositivo IoT Edge a funcionar como uma porta de entrada transparente, precisa de configurar os seus dispositivos a jusante para confiar no portal e enviar-lhe mensagens. Continue a [autenticar um dispositivo a jusante para o Azure IoT Hub](how-to-authenticate-downstream-device.md) para os próximos passos na configuração do seu cenário transparente de gateway.
