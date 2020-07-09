---
title: Criar dispositivo de gateway transparente - Azure IoT Edge / Microsoft Docs
description: Use um dispositivo Azure IoT Edge como um gateway transparente que pode processar informações a partir de dispositivos a jusante
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/02/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 0155294777e1d732e5ff3874102b90049d9a123d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84782590"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>Configurar um dispositivo IoT Edge para atuar como um gateway transparente

Este artigo fornece instruções detalhadas para configurar um dispositivo IoT Edge para funcionar como um gateway transparente para outros dispositivos comunicarem com o IoT Hub. Este artigo usa o termo *gateway IoT Edge* para se referir a um dispositivo IoT Edge configurado como um gateway transparente. Para obter mais informações, consulte [como um dispositivo IoT Edge pode ser usado como porta de entrada](./iot-edge-as-gateway.md).

>[!NOTE]
>Atualmente:
>
> * Os dispositivos ativados por arestas não conseguem ligar-se aos gateways IoT Edge.
> * Os dispositivos a jusante não podem usar o upload de ficheiros.

Existem três passos gerais para estabelecer uma ligação transparente de gateway bem sucedida. Este artigo abrange o primeiro passo:

1. **Configure o dispositivo gateway como um servidor para que os dispositivos a jusante possam ligar-se a ele de forma segura. Configurar o portal para receber mensagens de dispositivos a jusante e encaminhá-las para o destino adequado.**
2. Crie uma identidade do dispositivo para o dispositivo a jusante para que possa autenticar com o IoT Hub. Configure o dispositivo a jusante para enviar mensagens através do dispositivo gateway. Para obter mais informações, consulte [Autenticar um dispositivo a jusante para o Azure IoT Hub](how-to-authenticate-downstream-device.md).
3. Ligue o dispositivo a jusante ao dispositivo gateway e comece a enviar mensagens. Para obter mais informações, consulte [ligar um dispositivo a jusante a um gateway Azure IoT Edge](how-to-connect-downstream-device.md).

Para que um dispositivo atue como um portal, tem de se ligar de forma segura aos seus dispositivos a jusante. O Azure IoT Edge permite-lhe utilizar uma infraestrutura de chaves públicas (PKI) para estabelecer ligações seguras entre dispositivos. Neste caso, estamos a permitir que um dispositivo a jusante se conecte a um dispositivo IoT Edge agindo como um gateway transparente. Para manter uma segurança razoável, o dispositivo a jusante deve confirmar a identidade do dispositivo gateway. Esta verificação de identidade impede que os seus dispositivos se conectem a gateways potencialmente maliciosos.

Um dispositivo a jusante pode ser qualquer aplicação ou plataforma que tenha uma identidade criada com o serviço de nuvem [Azure IoT Hub.](https://docs.microsoft.com/azure/iot-hub) Estas aplicações utilizam frequentemente o [dispositivo Azure IoT SDK](../iot-hub/iot-hub-devguide-sdks.md). Um dispositivo a jusante pode até ser uma aplicação em execução no próprio dispositivo de gateway IoT Edge. No entanto, um dispositivo IoT Edge não pode ser a jusante de um gateway IoT Edge.

Pode criar qualquer infraestrutura de certificado que permita a confiança necessária para a sua topologia de porta de dispositivos. Neste artigo, assumimos a mesma configuração de certificado que você usaria para permitir a [segurança X.509 CA](../iot-hub/iot-hub-x509ca-overview.md) no IoT Hub, que envolve um certificado DE CA X.509 associado a um hub IoT específico (o hub IoT raiz CA), uma série de certificados assinados com este CA, e um CA para o dispositivo IoT Edge.

>[!NOTE]
>O *certificado de CA de raiz* utilizado ao longo destes artigos refere-se ao certificado público de autoridade mais alta da cadeia de certificados PKI, e não necessariamente à raiz do certificado de uma autoridade de certificados sindicado. Em muitos casos, trata-se, na verdade, de um certificado público intermédio da AC.

Os passos seguintes acompanham-no através do processo de criação dos certificados e da sua instalação nos locais certos no gateway. Pode utilizar qualquer máquina para gerar os certificados e copiá-los para o seu dispositivo IoT Edge.

## <a name="prerequisites"></a>Pré-requisitos

Um dispositivo Linux ou Windows com IoT Edge instalado.

## <a name="set-up-the-device-ca-certificate"></a>Configurar o certificado ca do dispositivo

Todos os gateways IoT Edge precisam de um certificado de CA do dispositivo instalado neles. O daemon de segurança IoT Edge utiliza o certificado CA do dispositivo IoT Edge para assinar um certificado de CA de carga de trabalho, que por sua vez assina um certificado de servidor para o hub IoT Edge. O gateway apresenta o seu certificado de servidor ao dispositivo a jusante durante o início da ligação. O dispositivo a jusante verifica se o certificado do servidor faz parte de uma cadeia de certificados que chega ao certificado de CA raiz. Este processo permite que o dispositivo a jusante confirme que o gateway provém de uma fonte fidedigna. Para mais informações, consulte [como a Azure IoT Edge utiliza certificados.](iot-edge-certs.md)

![Configuração do certificado gateway](./media/how-to-create-transparent-gateway/gateway-setup.png)

O certificado de CA raiz e o certificado CA do dispositivo (com a sua chave privada) têm de estar presentes no dispositivo de gateway IoT Edge e configurados no ficheiro IoT Edge config.yaml. Lembre-se que, neste *caso, o certificado de CA raiz* significa a autoridade de certificado mais alta para este cenário IoT Edge. O certificado de CA do dispositivo de gateway e os certificados do dispositivo a jusante têm de chegar ao mesmo certificado de CA raiz.

>[!TIP]
>O processo de instalação do certificado de CA raiz e do certificado ca do dispositivo num dispositivo IoT Edge também é explicado mais detalhadamente em [certificados De gestão num dispositivo IoT Edge](how-to-manage-device-certificates.md).

Tenha os seguintes ficheiros prontos:

* Certificado root CA
* Certificado ca dispositivo
* Chave privada do dispositivo CA

Para cenários de produção, deve gerar estes ficheiros com a sua própria autoridade de certificados. Para cenários de desenvolvimento e teste, pode utilizar certificados de demonstração.

1. Se estiver a utilizar certificados de demonstração, utilize o seguinte conjunto de passos para criar os seus ficheiros:
   1. [Crie o certificado de CA raiz](how-to-create-test-certificates.md#create-root-ca-certificate). No final destas instruções, terá um ficheiro de certificado de CA raiz:
      * `<path>/certs/azure-iot-test-only.root.ca.cert.pem`.

   2. [Criar certificado CA do dispositivo IoT Edge](how-to-create-test-certificates.md#create-iot-edge-device-ca-certificates). No final destas instruções, terá dois ficheiros, um certificado de AC do dispositivo e a sua chave privada:
      * `<path>/certs/iot-edge-device-<cert name>-full-chain.cert.pem`e
      * `<path>/private/iot-edge-device-<cert name>.key.pem`

2. Se criou estes ficheiros numa máquina diferente, copie-os para o seu dispositivo IoT Edge.

3. No seu dispositivo IoT Edge, abra o ficheiro de segurança daemon config.
   * Janelas:`C:\ProgramData\iotedge\config.yaml`
   * Linux:`/etc/iotedge/config.yaml`

4. Encontre a secção de certificados do ficheiro e forneça os URIs de ficheiros aos seus três ficheiros como valores para as **seguintes** propriedades:
   * **device_ca_cert**: certificado ca do dispositivo
   * **device_ca_pk**: chave privada ca do dispositivo
   * **trusted_ca_certs**: certificado de CA raiz

5. Guarde e feche o ficheiro.

6. Reiniciar ioT Edge.
   * Janelas:`Restart-Service iotedge`
   * Linux:`sudo systemctl restart iotedge`

## <a name="deploy-edgehub-to-the-gateway"></a>Implementar edgeHub para o gateway

Quando instala pela primeira vez o IoT Edge num dispositivo, apenas um módulo de sistema começa automaticamente: o agente IoT Edge. Uma vez que você cria a primeira implementação para um dispositivo, o segundo módulo do sistema, o hub IoT Edge, também é iniciado.

O hub IoT Edge é responsável por receber mensagens recebidas de dispositivos a jusante e encaminhá-las para o próximo destino. Se o módulo **EdgeHub** não estiver a funcionar no seu dispositivo, crie uma implementação inicial para o seu dispositivo. A implementação vai parecer vazia porque não adiciona nenhum módulo, mas irá certificar-se de que ambos os módulos do sistema estão em funcionamento.

Pode verificar quais os módulos que estão a ser executadas num dispositivo, verificando os detalhes do dispositivo no portal Azure, visualizando o estado do dispositivo no Visual Studio ou visual Studio Code, ou executando o comando `iotedge list` no próprio dispositivo.

Se o módulo **edgeAgent** estiver em funcionamento sem o módulo **EdgeHub,** utilize os seguintes passos:

1. No portal do Azure, navegue para o seu hub IoT.

2. Vá ao **IoT Edge** e selecione o seu dispositivo IoT Edge que pretende utilizar como porta de entrada.

3. Selecione **módulos de conjunto**.

4. Selecione **Seguinte: Rotas**.

5. Na página **Rotas,** deverá ter uma rota predefinida que envie todas as mensagens, seja de um módulo ou de um dispositivo a jusante, para o IoT Hub. Caso contrário, adicione uma nova rota com os seguintes valores e, em seguida, selecione **Review + create**:
   * **Nome:**`route`
   * **Valor:**`FROM /messages/* INTO $upstream`

6. Na página **'Rever + criar',** selecione **Criar.**

## <a name="open-ports-on-gateway-device"></a>Portas abertas no dispositivo gateway

Os dispositivos Standard IoT Edge não precisam de qualquer conectividade de entrada para funcionar, porque toda a comunicação com o IoT Hub é feita através de ligações de saída. Os dispositivos gateway são diferentes porque precisam de receber mensagens dos seus dispositivos a jusante. Se uma firewall estiver entre os dispositivos a jusante e o dispositivo gateway, então a comunicação também tem de ser possível através da firewall.

Para que um cenário de gateway funcione, pelo menos um dos protocolos suportados do hub IoT Edge deve estar aberto para o tráfego de entrada a partir de dispositivos a jusante. Os protocolos suportados são MQTT, AMQP, HTTPS, MQTT sobre WebSockets e AMQP sobre WebSockets.

| Porta | Protocolo |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT+WS <br> AMQP+WS |

## <a name="route-messages-from-downstream-devices"></a>Mensagens de rota de dispositivos a jusante

O tempo de funcionaamento do IoT Edge pode encaminhar mensagens enviadas de dispositivos a jusante, tal como as mensagens enviadas por módulos. Esta funcionalidade permite-lhe realizar análises num módulo em execução no gateway antes de enviar quaisquer dados para a nuvem.

Atualmente, a forma como encaminha as mensagens enviadas por dispositivos a jusante é diferenciando-as das mensagens enviadas por módulos. As mensagens enviadas por módulos contêm uma propriedade do sistema chamada **ligaçãoModuleId,** mas as mensagens enviadas por dispositivos a jusante não contêm. Pode utilizar a cláusula WHERE da rota para excluir quaisquer mensagens que contenham essa propriedade do sistema.

A rota abaixo é um exemplo que enviaria mensagens de qualquer dispositivo a jusante para um módulo chamado `ai_insights` , e depois de `ai_insights` ioT Hub.

```json
{
    "routes":{
        "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")",
        "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream"
    }
}
```

Para obter mais informações sobre o encaminhamento de mensagens, consulte [os módulos de implantação e estabeleça rotas.](./module-composition.md#declare-routes)

## <a name="enable-extended-offline-operation"></a>Permitir uma operação offline alargada

A partir da [libertação 1.0.4](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4) do tempo de funcionamento do IoT Edge, o dispositivo gateway e os dispositivos a jusante que o ligam podem ser configurados para um funcionamento offline prolongado.

Com esta capacidade, os módulos locais ou dispositivos a jusante podem reautorá-lo com o dispositivo IoT Edge conforme necessário e comunicar uns com os outros usando mensagens e métodos mesmo quando desligados do hub IoT. Para obter mais informações, consulte [compreender as capacidades offline alargadas para dispositivos IoT Edge, módulos e dispositivos infantis](offline-capabilities.md).

Para ativar capacidades offline alargadas, estabelece uma relação pai-filho entre um dispositivo de gateway IoT Edge e dispositivos a jusante que se ligarão a ele. Esses passos são explicados mais detalhadamente no próximo artigo desta série, [Autenticar um dispositivo a jusante para o Azure IoT Hub](how-to-authenticate-downstream-device.md).

## <a name="next-steps"></a>Próximos passos

Agora que tem um dispositivo IoT Edge configurado como um gateway transparente, precisa de configurar os seus dispositivos a jusante para confiar no gateway e enviar-lhe mensagens. Continue a [autenticar um dispositivo a jusante até ao Azure IoT Hub](how-to-authenticate-downstream-device.md) para os próximos passos na configuração do seu cenário transparente de gateway.
