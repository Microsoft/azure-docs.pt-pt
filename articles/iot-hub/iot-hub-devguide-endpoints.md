---
title: Compreenda os pontos finais do Azure IoT Hub | Microsoft Docs
description: Guia do desenvolvedor - informações de referência sobre dispositivos IoT Hub e pontos finais virados para o serviço.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/10/2019
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: System Architecture'
ms.openlocfilehash: d2b9ea2e075ddcf20860ccb9ab1f2eff654993ad
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107499381"
---
# <a name="reference---iot-hub-endpoints"></a>Referência - Pontos finais IoT Hub

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="iot-hub-names"></a>Nomes do IoT Hub

Pode encontrar o nome de anfitrião do hub IoT que hospeda os seus pontos finais no portal na página  **geral** do seu hub. Por padrão, o nome DNS de um hub IoT parece: `{your iot hub name}.azure-devices.net` .

## <a name="list-of-built-in-iot-hub-endpoints"></a>Lista de pontos finais do IoT Hub incorporados

O Azure IoT Hub é um serviço multi-inquilino que expõe a sua funcionalidade a vários atores. O diagrama seguinte mostra os vários pontos finais que o IoT Hub expõe.

![Pontos finais do Hub IoT](./media/iot-hub-devguide-endpoints/endpoints.png)

A lista que se segue descreve os pontos finais:

* **Fornecedor de recursos.** O fornecedor de recursos IoT Hub expõe uma interface [Azure Resource Manager.](../azure-resource-manager/management/overview.md) Esta interface permite aos proprietários de subscrições do Azure criar e eliminar centros IoT e atualizar as propriedades do hub IoT. As propriedades do IoT Hub regem as políticas de segurança ao [nível do hub,](iot-hub-devguide-security.md#access-control-and-permissions)em oposição ao controlo de acesso ao nível do dispositivo, e opções funcionais para mensagens cloud-to-device e dispositivo-a-nuvem. O fornecedor de recursos IoT Hub também permite [exportar identidades de dispositivos.](iot-hub-devguide-identity-registry.md#import-and-export-device-identities)

* **Gestão da identidade do dispositivo**. Cada hub IoT expõe um conjunto de pontos finais HTTPS REST para gerir identidades do dispositivo (criar, recuperar, atualizar e eliminar). [As identidades do dispositivo](iot-hub-devguide-identity-registry.md) são utilizadas para a autenticação do dispositivo e para o controlo de acessos.

* **Gestão de dois dispositivos**. Cada hub IoT expõe um conjunto de ponto final HTTPS REST virado para o serviço para consultar e atualizar [gémeos do dispositivo](iot-hub-devguide-device-twins.md) (etiquetas e propriedades de atualização). 

* **Gestão de empregos.** Cada hub IoT expõe um conjunto de ponto final HTTPS REST virado para o serviço para consultar e gerir [postos de trabalho.](iot-hub-devguide-jobs.md)

* **Pontos finais do dispositivo**. Para cada dispositivo no registo de identidade, o IoT Hub expõe um conjunto de pontos finais. Salvo quando anotados, estes pontos finais são expostos utilizando protocolos [MQTT v3.1.1](https://mqtt.org/), HTTPS 1.1 e [AMQP 1.0.](https://www.amqp.org/) AMQP e MQTT também estão disponíveis através [de WebSockets](https://tools.ietf.org/html/rfc6455) na porta 443.

  * *Enviar mensagens dispositivo-a-nuvem*. Um dispositivo utiliza este ponto final para [enviar mensagens dispositivo-a-nuvem](iot-hub-devguide-messages-d2c.md).

  * *Receba mensagens nuvem-dispositivo*. Um dispositivo utiliza este ponto final para receber [mensagens de nuvem para dispositivos direcionados](iot-hub-devguide-messages-c2d.md).

  * *Iniciar uploads de ficheiros*. Um dispositivo utiliza este ponto final para receber um Azure Storage SAS URI do IoT Hub para [carregar um ficheiro](iot-hub-devguide-file-upload.md).

  * *Recupere e atualize as propriedades gémeas do dispositivo*. Um dispositivo utiliza este ponto final para aceder às propriedades do seu [dispositivo Twin.](iot-hub-devguide-device-twins.md) HTTPS não é suportado.

  * *Receba pedidos de métodos diretos.* Um dispositivo utiliza este ponto final para ouvir os pedidos do [método direto.](iot-hub-devguide-direct-methods.md) HTTPS não é suportado.

  [!INCLUDE [iot-hub-include-x509-ca-signed-support-note](../../includes/iot-hub-include-x509-ca-signed-support-note.md)]

* **Pontos finais de serviço**. Cada hub IoT expõe um conjunto de pontos finais para a sua solução traseira para comunicar com os seus dispositivos. Com uma exceção, estes pontos finais só são expostos utilizando os protocolos [AMQP](https://www.amqp.org/) e AMQP sobre webSockets. O ponto final de invocação do método direto está exposto sobre o protocolo HTTPS.
  
  * *Receba mensagens dispositivo-a-nuvem*. Este ponto final é compatível com [Azure Event Hubs](https://azure.microsoft.com/documentation/services/event-hubs/). Um serviço de back-end pode usá-lo para ler as [mensagens dispositivo-a-nuvem](iot-hub-devguide-messages-d2c.md) enviadas pelos seus dispositivos. Pode criar pontos finais personalizados no seu hub IoT para além deste ponto final incorporado.
  
  * *Envie mensagens nuvem-dispositivo e receba agradecimentos de entrega*. Estes pontos finais permitem que a sua solução volte a enviar [mensagens fiáveis em nuvem para dispositivo](iot-hub-devguide-messages-c2d.md), e para receber os correspondentes agradecimentos de entrega ou expiração.
  
  * *Receber notificações de ficheiros.* Este ponto final de mensagens permite-lhe receber notificações de quando os seus dispositivos fazem o upload com sucesso de um ficheiro. 
  
  * *Invocação direta do método*. Este ponto final permite que um serviço de back-end invoque um [método direto](iot-hub-devguide-direct-methods.md) num dispositivo.
  
  * *Receber eventos de monitorização de operações.* Este ponto final permite-lhe receber eventos de monitorização de operações se o seu hub IoT tiver sido configurado para os emitir. Para obter mais informações, consulte [a monitorização das operações do IoT Hub.](iot-hub-operations-monitoring.md)

O artigo [da Azure IoT SDKs](iot-hub-devguide-sdks.md) descreve as várias formas de aceder a estes pontos finais.

Todos os pontos finais do IoT Hub utilizam o protocolo [TLS,](https://tools.ietf.org/html/rfc5246) e nenhum ponto final é exposto em canais não encriptados/não protegidos.

## <a name="custom-endpoints"></a>Pontos finais personalizados

Pode ligar os serviços Azure existentes nas suas subscrições Azure ao seu hub IoT para funcionar como ponto final para o encaminhamento de mensagens. Estes pontos finais funcionam como pontos finais de serviço e são usados como pias para rotas de mensagens. Os dispositivos não podem escrever diretamente para os pontos finais adicionais. Saiba mais sobre [o encaminhamento de mensagens](../iot-hub/iot-hub-devguide-messages-d2c.md).

O IoT Hub suporta atualmente os seguintes serviços Azure como pontos finais adicionais:

* Recipientes de armazenamento Azure
* Hubs de Eventos
* Filas de Service Bus
* Tópicos de Service Bus

Para os limites do número de pontos finais que pode adicionar, consulte [quotas e estrangulamento](iot-hub-devguide-quotas-throttling.md).

## <a name="endpoint-health"></a>Saúde endpoint

[!INCLUDE [iot-hub-endpoint-health](../../includes/iot-hub-include-endpoint-health.md)]

## <a name="field-gateways"></a>Gateways de campo

Numa solução IoT, fica um *gateway de campo* entre os seus dispositivos e os seus pontos finais IoT Hub. Está tipicamente localizado perto dos seus dispositivos. Os seus dispositivos comunicam diretamente com o gateway de campo utilizando um protocolo suportado pelos dispositivos. O gateway de campo liga-se a um ponto final do IoT Hub usando um protocolo que é suportado pelo IoT Hub. Um gateway de campo pode ser um dispositivo de hardware dedicado ou um computador de baixa potência que executa software de gateway personalizado.

Você pode usar [Azure IoT Edge](../iot-edge/index.yml) para implementar um gateway de campo. O IoT Edge oferece funcionalidades como comunicações multiplexing de vários dispositivos para a mesma ligação IoT Hub.

## <a name="next-steps"></a>Passos seguintes

Outros tópicos de referência neste guia de desenvolvimento do IoT Hub incluem:

* [IoT Hub linguagem de consulta para gémeos de dispositivo, empregos e encaminhamento de mensagens](iot-hub-devguide-query-language.md)
* [Quotas e limitação](iot-hub-devguide-quotas-throttling.md)
* [Suporte IoT Hub MQTT](iot-hub-mqtt-support.md)
* [Compreenda o seu endereço IP do hub IoT](iot-hub-understand-ip-address.md)
