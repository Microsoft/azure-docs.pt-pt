---
title: Compreenda o suporte do Serviço de Fornecimento de Dispositivos IoT Azure IoT / Microsoft Docs
description: Guia do programador - suporte para dispositivos que se liguem ao dispositivo Azure IoT Device Provisioning Service (DPS) com o protocolo MQTT.
author: rajeevmv
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: ravokkar
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 0a7ec2f4f8fdf631a6bc5096296275291ec41751
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967130"
---
# <a name="communicate-with-your-dps-using-the-mqtt-protocol"></a>Comunique com o seu DPS utilizando o protocolo MQTT

O DPS permite que os dispositivos se comuniquem com o ponto final do dispositivo DPS utilizando:

* [MQTT v3.1.1](https://mqtt.org/) na porta 8883
* [MQTT v3.1.1](http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718127) sobre WebSocket na porta 443.

O DPS não é um corretor MQTT completo e não suporta todos os comportamentos especificados na norma MQTT v3.1.1. Este artigo descreve como os dispositivos podem usar comportamentos MQTT suportados para comunicar com DPS.

Toda a comunicação do dispositivo com DPS deve ser fixada utilizando TLS/SSL. Portanto, o DPS não suporta ligações não seguras sobre a porta 1883.

 > [!NOTE] 
 > Atualmente, o DPS não suporta dispositivos que utilizem [mecanismos de atestado TPM](./concepts-service.md#attestation-mechanism) sobre o protocolo MQTT.

## <a name="connecting-to-dps"></a>Ligação ao DPS

Um dispositivo pode utilizar o protocolo MQTT para ligar a um DPS utilizando qualquer uma das seguintes opções.

* Bibliotecas nos [Azure IoT Provisioning SDKs](../iot-hub/iot-hub-devguide-sdks.md#microsoft-azure-provisioning-sdks).
* O protocolo MQTT diretamente.

## <a name="using-the-mqtt-protocol-directly-as-a-device"></a>Utilizando o protocolo MQTT diretamente (como um dispositivo)

Se um dispositivo não puder utilizar os SDKs do dispositivo, ainda pode ligar-se aos pontos finais do dispositivo público utilizando o protocolo MQTT na porta 8883. No pacote **CONNECT,** o dispositivo deve utilizar os seguintes valores:

* Para o campo **ClientId,** utilize **o registoId.**

* Para o campo **Username,** `{idScope}/registrations/{registration_id}/api-version=2019-03-31` utilize, onde `{idScope}` está o [idScope](./concepts-service.md#id-scope) do DPS.

* Para o campo **Palavra-Passe,** utilize um token SAS. O formato do token SAS é o mesmo que para os protocolos HTTPS e AMQP:

  `SharedAccessSignature sr={URL-encoded-resourceURI}&sig={signature-string}&se={expiry}&skn=registration` O recursoURI deve estar no formato `{idScope}/registrations/{registration_id}` . O nome da apólice deve ser `registration` .

  > [!NOTE]
  > Se utilizar a autenticação do certificado X.509, não são necessárias palavras-passe para o símbolo SAS.

  Para obter mais informações sobre como gerar fichas SAS, consulte a secção de fichas de segurança do Controlo de [Acesso a DPS.](how-to-control-access.md#security-tokens)

Segue-se uma lista de comportamentos específicos de implementação do DPS:

 * O DPS não suporta a funcionalidade de que a bandeira **cleanSession** está a ser definida como **0**.

 * Quando uma aplicação de dispositivo subscreve um tópico com **qoS 2,** o DPS concede o nível QoS máximo 1 no pacote **SUBACK.** Depois disso, o DPS entrega mensagens ao dispositivo utilizando o QoS 1.

## <a name="tlsssl-configuration"></a>Configuração TLS/SSL

Para utilizar o protocolo MQTT diretamente, o seu cliente *deve* ligar-se ao TLS 1.2. As tentativas de saltar este passo falham com erros de ligação.


## <a name="registering-a-device"></a>Registo de um dispositivo

Para registar um dispositivo através de DPS, um dispositivo deve subscrever `$dps/registrations/res/#` usando como **Filtro de Tópicos**. O wildcard de vários níveis `#` no Filtro Tópico é utilizado apenas para permitir que o dispositivo receba propriedades adicionais no nome do tópico. O DPS não permite a utilização dos `#` `?` ou wildcards para filtragem de subtópicos. Uma vez que o DPS não é um corretor de mensagens pub-sub de uso geral, apenas suporta os nomes de tópicos documentados e filtros tópicos.

O dispositivo deve publicar uma mensagem de registo para DPS utilizando `$dps/registrations/PUT/iotdps-register/?$rid={request_id}` como **Nome tópico**. A carga útil deve conter o objeto [de registo](/rest/api/iot-dps/runtimeregistration/registerdevice#deviceregistration) do dispositivo no formato JSON.
Num cenário bem sucedido, o dispositivo receberá uma resposta sobre o `$dps/registrations/res/202/?$rid={request_id}&retry-after=x` nome do tópico onde x é o valor de retrip-after em segundos. A carga útil da resposta conterá o objeto [RegistrationOperationStatus](/rest/api/iot-dps/runtimeregistration/registerdevice#registrationoperationstatus) no formato JSON.

## <a name="polling-for-registration-operation-status"></a>Sondagem para o estado da operação de registo

O aparelho deve fazer uma sondagem periódica do serviço para receber o resultado da operação de registo do dispositivo. Assumindo que o dispositivo já subscreveu o `$dps/registrations/res/#` tema como indicado acima, pode publicar uma mensagem de operações para o `$dps/registrations/GET/iotdps-get-operationstatus/?$rid={request_id}&operationId={operationId}` nome do tópico. O ID de funcionamento nesta mensagem deve ser o valor recebido na mensagem de resposta RegistrationOperaTionStatus no passo anterior. No caso de sucesso, o serviço responderá sobre o `$dps/registrations/res/200/?$rid={request_id}` tema. A carga útil da resposta conterá o objeto RegistrationOperationStatus. O dispositivo deve continuar a sondar o serviço se o código de resposta for 202 após um atraso igual ao período de retrips. A operação de registo do dispositivo é bem sucedida se o serviço devolver um código de estado de 200.

## <a name="connecting-over-websocket"></a>Ligação sobre Websocket
Ao ligar o Websocket, especifique o subprotocol como `mqtt` . Siga [o RFC 6455](https://tools.ietf.org/html/rfc6455).

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o protocolo MQTT, consulte a [documentação MQTT](https://mqtt.org/).

Para explorar ainda mais as capacidades do DPS, consulte:

* [Sobre o IoT DPS](about-iot-dps.md)