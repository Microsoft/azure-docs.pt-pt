---
title: Criar dispositivo de gateway transparente - Azure IoT Edge | Documentos da Microsoft
description: Utilizar um dispositivo Azure IoT Edge como gateway transparente que pode processar informações a partir de dispositivos de downstream
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/30/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: bf60bfb41e48220845e9aa26dc26f20e6ed60d16
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76510690"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>Configurar um dispositivo IoT Edge para atuar como gateway transparente

Este artigo fornece instruções detalhadas para configurar um IoT Edge dispositivo para funcionar como um gateway transparente para outros dispositivos se comunicarem com o Hub IoT. Este artigo usa o termo *IOT Edge gateway* para se referir a um dispositivo de IOT Edge configurado como um gateway transparente. Para obter mais informações, consulte [como um dispositivo de IOT Edge pode ser usado como um gateway](./iot-edge-as-gateway.md).

>[!NOTE]
>Atualmente:
>
> * Dispositivos com capacidade de borda não é possível ligar aos gateways de IoT Edge.
> * Dispositivos jusante não é possível utilizar o carregamento de ficheiros.

Há três etapas gerais para configurar uma conexão de gateway transparente bem-sucedida. Este artigo aborda a primeira etapa:

1. **O dispositivo de gateway precisa ser capaz de se conectar com segurança a dispositivos downstream, receber comunicações de dispositivos downstream e rotear mensagens para o destino adequado.**
2. O dispositivo downstream precisa ter uma identidade de dispositivo para ser capaz de autenticar com o Hub IoT e saber se comunicar por meio de seu dispositivo de gateway. Para obter mais informações, consulte [autenticar um dispositivo downstream no Hub IOT do Azure](how-to-authenticate-downstream-device.md).
3. O dispositivo downstream precisa se conectar ao seu dispositivo de gateway com segurança. Para obter mais informações, consulte [ligar um dispositivo jusante a um gateway do Azure IoT Edge](how-to-connect-downstream-device.md).

Para que um dispositivo funcione como um gateway, ele precisa ser capaz de se conectar com segurança a seus dispositivos downstream. O Azure IoT Edge permite-lhe utilizar uma infraestrutura de chaves públicas (PKI) para configurar ligações seguras entre dispositivos. Neste caso, estamos está a permitir que um dispositivo downstream ligar a um dispositivo IoT Edge que atua como um gateway transparente. Para manter a segurança razoável, o dispositivo downstream deve confirmar a identidade do dispositivo de gateway. Essa verificação de identidade impede que os dispositivos se conectem a gateways potencialmente mal-intencionados.

Um dispositivo downstream em um cenário de gateway transparente pode ser qualquer aplicativo ou plataforma que tenha uma identidade criada com o serviço de nuvem do [Hub IOT do Azure](https://docs.microsoft.com/azure/iot-hub) . Em muitos casos, estas aplicações se utilizar o [do Azure IoT device SDK](../iot-hub/iot-hub-devguide-sdks.md). Para todos os fins práticos, um dispositivo de downstream ainda pode ser uma aplicação em execução no dispositivo de gateway do IoT Edge em si. No entanto, um dispositivo de IoT Edge não pode ser downstream de um gateway de IoT Edge.

Pode criar qualquer infraestrutura de certificado que permite a confiança necessária para a sua topologia de gateway de dispositivo. Neste artigo, assumimos a mesma configuração de certificado que você usaria para habilitar a [segurança de autoridade de certificação x. 509](../iot-hub/iot-hub-x509ca-overview.md) no Hub IOT, que envolve um certificado de autoridade de certificação x. 509 associado a um hub IOT específico (a AC raiz do Hub IOT), uma série de certificados assinados com essa AC e uma AC para o dispositivo IOT Edge.

![Configurar o certificado do gateway](./media/how-to-create-transparent-gateway/gateway-setup.png)

>[!NOTE]
>O termo "CA raiz" usado em todo este artigo refere-se ao certificado público mais alto da autoridade da cadeia de certificados PKI e não necessariamente à raiz do certificado de uma autoridade de certificação agregada. Em muitos casos, é, na verdade, um certificado público de CA intermediário.

O gateway apresenta seu IoT Edge certificado de autoridade de certificação do dispositivo ao dispositivo downstream durante o início da conexão. O dispositivo downstream verifica se o certificado de autoridade de certificação do dispositivo IoT Edge está assinado pelo certificado de autoridade de certificação raiz. Esse processo permite que o dispositivo downstream confirme se o gateway vem de uma fonte confiável.

As etapas a seguir orientam você pelo processo de criação dos certificados e sua instalação nos locais certos no gateway. Pode utilizar qualquer máquina para gerar os certificados e, em seguida, copie os mesmos para o seu dispositivo IoT Edge.

## <a name="prerequisites"></a>Pré-requisitos

Um dispositivo Azure IoT Edge, configurado com [certificados de produção](how-to-install-production-certificates.md).

## <a name="deploy-edgehub-to-the-gateway"></a>Implantar edgeHub no gateway

Quando você instala o IoT Edge pela primeira vez em um dispositivo, somente um módulo do sistema é iniciado automaticamente: o agente do IoT Edge. Depois de criar a primeira implantação mais um dispositivo, o segundo módulo do sistema, o Hub de IoT Edge, também será iniciado.

O Hub de IoT Edge é responsável por receber mensagens de entrada de dispositivos downstream e roteá-los para o próximo destino. Se o módulo **edgeHub** não estiver em execução no seu dispositivo, crie uma implantação inicial para seu dispositivo. A implantação ficará vazia porque você não adiciona nenhum módulo, mas verificará se ambos os módulos do sistema estão em execução.

Você pode verificar quais módulos estão sendo executados em um dispositivo verificando os detalhes do dispositivo na portal do Azure, exibindo o status do dispositivo no Visual Studio ou Visual Studio Code, ou executando o comando `iotedge list` no próprio dispositivo.

Se o módulo **edgeAgent** estiver em execução sem o módulo **edgeHub** , use as seguintes etapas:

1. No portal do Azure, navegue para o seu hub IoT.

2. Aceda a **IoT Edge** e selecione o seu dispositivo IoT Edge que pretende utilizar como um gateway.

3. Selecione **Definir Módulos**.

4. Selecione **Seguinte**.

5. Na **especificar rotas** página, deve ter uma rota envia todas as mensagens de todos os módulos para o IoT Hub. Caso contrário, adicione o seguinte código e, em seguida, selecione **Seguinte**.

   ```JSON
   {
       "routes": {
           "route": "FROM /messages/* INTO $upstream"
       }
   }
   ```

6. Na **rever modelo** página, selecione **submeter**.

## <a name="open-ports-on-gateway-device"></a>Abrir portas no dispositivo de gateway

Os dispositivos IoT Edge padrão não precisam de nenhuma conectividade de entrada para funcionar, pois toda a comunicação com o Hub IoT é feita por meio de conexões de saída. Os dispositivos de gateway são diferentes porque precisam receber mensagens de seus dispositivos downstream. Se um firewall estiver entre os dispositivos downstream e o dispositivo de gateway, a comunicação também precisará ser possível por meio do firewall.

Para que um cenário de gateway funcione, pelo menos um dos protocolos com suporte do hub de IoT Edge deve estar aberto para o tráfego de entrada de dispositivos downstream. Os protocolos com suporte são MQTT, AMQP, HTTPS, MQTT sobre WebSockets e AMQP sobre WebSockets.

| Porta | Protocolo |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT+WS <br> AMQP+WS |

## <a name="route-messages-from-downstream-devices"></a>Encaminhar mensagens a partir de dispositivos de downstream

O runtime do IoT Edge pode encaminhar mensagens enviadas a partir de dispositivos downstream, assim como as mensagens enviadas por módulos. Esse recurso permite que você execute análises em um módulo em execução no gateway antes de enviar qualquer dado para a nuvem.

Atualmente, a maneira que rotear as mensagens enviadas pelos dispositivos downstream é diferenciá-los de mensagens enviadas por módulos. As mensagens enviadas por todos os módulos de contenham uma propriedade de sistema chamada **connectionModuleId** mas não as mensagens enviadas pelos dispositivos downstream. Pode utilizar a cláusula WHERE da rota para excluir todas as mensagens que contêm essa propriedade de sistema.

A rota abaixo é um exemplo que enviaria mensagens de qualquer dispositivo downstream para um módulo chamado `ai_insights`e, em seguida, de `ai_insights` para o Hub IoT.

```json
{
    "routes":{
        "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")",
        "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream"
    }
}
```

Para obter mais informações sobre o roteamento de mensagens, consulte [implementar módulos e rotas](./module-composition.md#declare-routes).

## <a name="enable-extended-offline-operation"></a>Habilitar operação offline estendida

A partir da [versão v 1.0.4](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4) do tempo de execução do IOT Edge, o dispositivo de gateway e os dispositivos downstream que se conectam a ele podem ser configurados para operação estendida offline.

Com esse recurso, os módulos locais ou dispositivos downstream podem se autenticar novamente com o dispositivo IoT Edge conforme necessário e se comunicarem entre si usando mensagens e métodos mesmo quando estiverem desconectados do Hub IoT. Para obter mais informações, consulte [compreender estendido recursos offline para o IoT Edge, dispositivos, módulos e dispositivos de subordinados](offline-capabilities.md).

Para habilitar recursos offline estendidos, você estabelece uma relação pai-filho entre um dispositivo IoT Edge gateway e dispositivos downstream que se conectarão a ele. Essas etapas são explicadas em mais detalhes em [autenticar um dispositivo downstream no Hub IOT do Azure](how-to-authenticate-downstream-device.md).

## <a name="next-steps"></a>Passos seguintes

Agora que tem um dispositivo IoT Edge como gateway transparente a funcionar, terá de configurar os seus dispositivos jusante para o gateway de confiança e enviar mensagens ao mesmo. Continue em para [autenticar um dispositivo downstream no Hub IOT do Azure](how-to-authenticate-downstream-device.md) para as próximas etapas em Configurando seu cenário de gateway transparente.
