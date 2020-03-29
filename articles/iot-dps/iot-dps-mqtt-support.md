---
title: Compreender o suporte mqtt do serviço de fornecimento de dispositivos Azure IoT Microsoft Docs
description: Guia de desenvolvimento - suporte para dispositivos que liguem ao ponto final virado para dispositivos Azure IoT (DPS) utilizando o protocolo MQTT.
author: rajeevmv
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: ravokkar
ms.openlocfilehash: ea6ece7e34ddb9c25f9f8349239ab3a1c3405abf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74973378"
---
# <a name="communicate-with-your-dps-using-the-mqtt-protocol"></a>Comunique com o seu DPS usando o protocolo MQTT

O DPS permite que os dispositivos se comuniquem com o ponto final do dispositivo DPS utilizando:

* [MQTT v3.1.1](https://mqtt.org/) na porta 8883
* [MQTT v3.1.1](http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718127) sobre WebSocket na porta 443.

O DPS não é um corretor MQTT completo e não suporta todos os comportamentos especificados na norma MQTT v3.1.1. Este artigo descreve como os dispositivos podem usar comportamentos MQTT suportados para comunicar com DPS.

Todas as comunicações do dispositivo com DPS devem ser protegidas utilizando TLS/SSL. Portanto, o DPS não suporta ligações não seguras sobre a porta 1883.

 > [!NOTE] 
 > ADPs não suporta atualmente dispositivos que utilizem mecanismo de [atestado](https://docs.microsoft.com/azure/iot-dps/concepts-device#attestation-mechanism) TPM sobre o protocolo MQTT.

## <a name="connecting-to-dps"></a>Ligação ao DPS

Um dispositivo pode utilizar o protocolo MQTT para ligar a um DPS utilizando qualquer uma das seguintes opções.

* Bibliotecas nos [SDKs de Provisionamento Azure IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks#microsoft-azure-provisioning-sdks).
* O protocolo MQTT diretamente.

## <a name="using-the-mqtt-protocol-directly-as-a-device"></a>Utilização direta do protocolo MQTT (como dispositivo)

Se um dispositivo não puder utilizar os SDKs do dispositivo, ainda pode ligar-se aos pontos finais do dispositivo público utilizando o protocolo MQTT na porta 8883. No pacote **CONNECT,** o dispositivo deve utilizar os seguintes valores:

* Para o campo **ClientId,** utilize **o registoId**.

* Para o campo **Username,** utilize, `{idScope}/registrations/{registration_id}/api-version=2019-03-31`onde `{idScope}` está o [idScope](https://docs.microsoft.com/azure/iot-dps/concepts-device#id-scope) do DPS.

* Para o campo **Password,** utilize um token SAS. O formato do token SAS é o mesmo que para os protocolos HTTPS e AMQP:

  `SharedAccessSignature sr={URL-encoded-resourceURI}&sig={signature-string}&se={expiry}&skn=registration`O recursoURI deve estar `{idScope}/registrations/{registration_id}`no formato . O nome da `registration`apólice deve ser.

  > [!NOTE]
  > Se utilizar a autenticação do certificado X.509, não são necessárias senhas de token SAS.

  Para obter mais informações sobre como gerar tokens SAS, consulte a secção de fichas de segurança do [acesso ao DPS](how-to-control-access.md#security-tokens).

Segue-se uma lista de comportamentos específicos de implementação do DPS:

 * O DPS não suporta que a funcionalidade da bandeira **CleanSession** seja definida para **0**.

 * Quando uma aplicação de dispositivo subscreve um tópico com **QoS 2,** o DPS concede o nível máximo de QoS 1 no pacote **SUBACK.** Depois disso, o DPS entrega mensagens ao dispositivo utilizando o QoS 1.

## <a name="tlsssl-configuration"></a>Configuração TLS/SSL

Para utilizar o protocolo MQTT diretamente, o seu cliente *deve* ligar-se ao TLS 1.2. As tentativas de saltar este passo falham com erros de ligação.


## <a name="registering-a-device"></a>Registar um dispositivo

Para registar um dispositivo através de DPS, um dispositivo deve subscrever utilizando `$dps/registrations/res/#` como **filtro**tópico . O wildcard `#` multi-nível no Filtro tópico é utilizado apenas para permitir que o dispositivo receba propriedades adicionais no nome tópico. O DPS não permite `#` a `?` utilização dos ou wildcards para filtragem de subtópicos. Uma vez que o DPS não é um corretor de mensagens sub-sub de uso geral, apenas suporta os nomes de tópicos documentados e filtros tópicos.

O dispositivo deve publicar uma mensagem `$dps/registrations/PUT/iotdps-register/?$rid={request_id}` de registo no DPS utilizando como **nome**tópico . A carga útil deve conter o objeto [de registo](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice#deviceregistration) do dispositivo no formato JSON.
Num cenário bem sucedido, o dispositivo `$dps/registrations/res/202/?$rid={request_id}&retry-after=x` receberá uma resposta sobre o nome tópico onde x é o valor de retry-after em segundos. A carga útil da resposta conterá o objeto [RegistrationOperationStatus](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice#registrationoperationstatus) no formato JSON.

## <a name="polling-for-registration-operation-status"></a>Boletim de voto para o estado da operação de registo

O aparelho deve fazer uma sondagem periódica ao serviço para receber o resultado da operação de registo do dispositivo. Assumindo que o dispositivo já `$dps/registrations/res/#` subscreveu o tópico como indicado acima, `$dps/registrations/GET/iotdps-get-operationstatus/?$rid={request_id}&operationId={operationId}` pode publicar uma mensagem de estado de funcionamento para o nome tópico. O ID de funcionamento nesta mensagem deve ser o valor recebido na mensagem de resposta RegistrationOperationOperationStatus na fase anterior. No caso de sucesso, o `$dps/registrations/res/200/?$rid={request_id}` serviço responderá sobre o tema. A carga útil da resposta conterá o objeto RegistrationOperationStatus. O dispositivo deve continuar a votar no serviço se o código de resposta for 202 após um atraso igual ao período de retry-after. A operação de registo do dispositivo é bem sucedida se o serviço devolver um código de estado de 200.

## <a name="connecting-over-websocket"></a>Ligação sobre Websocket
Ao ligar-se à Websocket, `mqtt`especifique o subprotocolo como . Siga [o RFC 6455](https://tools.ietf.org/html/rfc6455).

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o protocolo MQTT, consulte a [documentação mQTT](https://mqtt.org/documentation).

Para explorar ainda mais as capacidades do DPS, consulte:

* [Sobre o IoT DPS](about-iot-dps.md)
