---
title: Compreenda os pontos finais do Hub Azure IoT [ Microsoft Docs
description: Guia de desenvolvimento - informações de referência sobre pontos finais virados para dispositivos IoT Hub e virados para o serviço.
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
ms.openlocfilehash: 53660ad93ab2218d546ae6f363873c4d66872e2b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81730315"
---
# <a name="reference---iot-hub-endpoints"></a>Referência - Pontos finais do Hub IoT

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="iot-hub-names"></a>Nomes do Hub IoT

Você pode encontrar o nome de anfitrião do hub IoT que acolhe seus pontos finais no portal na página de **visão geral** do seu hub. Por padrão, o nome DNS de um `{your iot hub name}.azure-devices.net`hub IoT parece: .

## <a name="list-of-built-in-iot-hub-endpoints"></a>Lista de pontos finais do IoT Hub incorporado

O Azure IoT Hub é um serviço multi-inquilino que expõe a sua funcionalidade a vários atores. O diagrama seguinte mostra os vários pontos finais que o IoT Hub expõe.

![Pontos finais do Hub IoT](./media/iot-hub-devguide-endpoints/endpoints.png)

A lista seguinte descreve os pontos finais:

* **Fornecedor de recursos.** O fornecedor de recursos IoT Hub expõe uma interface do Gestor de [Recursos Azure.](../azure-resource-manager/management/overview.md) Esta interface permite que os proprietários de subscrições azure criem e apaguem os hubs IoT e atualizem propriedades do hub IoT. As propriedades do IoT Hub governam [as políticas de segurança ao nível do hub,](iot-hub-devguide-security.md#access-control-and-permissions)em oposição ao controlo de acesso ao nível do dispositivo, e opções funcionais para mensagens cloud-to-device e dispositivo-to-cloud. O fornecedor de recursos IoT Hub também lhe permite exportar identidades de [dispositivos.](iot-hub-devguide-identity-registry.md#import-and-export-device-identities)

* **Gestão da identidade**do dispositivo. Cada hub IoT expõe um conjunto de pontos finais HTTPS REST para gerir identidades do dispositivo (criar, recuperar, atualizar e eliminar). [As identidades](iot-hub-devguide-identity-registry.md) do dispositivo são utilizadas para a autenticação e controlo de acesso do dispositivo.

* **Gestão de gémeos dispositivos.** Cada hub IoT expõe um conjunto de ponto final HTTPS REST virado para o serviço para consulta e atualização de [gémeos dispositivos](iot-hub-devguide-device-twins.md) (etiquetas de atualização e propriedades).

* **Gestão de empregos.** Cada hub IoT expõe um conjunto de ponto final HTTPS REST virado para o serviço para consulta e gestão de [empregos](iot-hub-devguide-jobs.md).

* **Pontos finais**do dispositivo . para cada dispositivo no registo de identidades, o Hub IoT expõe um conjunto de pontos finais –

  * *Envie mensagens de dispositivo-nuvem*. Um dispositivo utiliza este ponto final para [enviar mensagens dispositivo-cloud](iot-hub-devguide-messages-d2c.md).

  * *Receba mensagens cloud-to-device*. Um dispositivo utiliza este ponto final para receber [mensagens cloud-to-device](iot-hub-devguide-messages-c2d.md)direcionadas .

  * *Iniciar uploads de ficheiros*. Um dispositivo utiliza este ponto final para receber um Azure Storage SAS URI do IoT Hub para [fazer o upload](iot-hub-devguide-file-upload.md)de um ficheiro .

  * *Recuperar e atualizar propriedades gémeas*do dispositivo . Um dispositivo utiliza este ponto final para aceder às propriedades do [seu dispositivo twin.](iot-hub-devguide-device-twins.md)

  * *Receba pedidos de método direto*. Um dispositivo utiliza este ponto final para ouvir os pedidos do [método direto.](iot-hub-devguide-direct-methods.md)

    Estes pontos finais são expostos utilizando os protocolos [MQTT v3.1.1,](https://mqtt.org/)HTTPS 1.1 e [AMQP 1.0.](https://www.amqp.org/) A AMQP também está disponível através [da WebSockets](https://tools.ietf.org/html/rfc6455) na porta 443.

* **Pontos finais de serviço**. Cada hub IoT expõe um conjunto de pontos finais para a sua solução na parte de trás para comunicar com os seus dispositivos. Com uma exceção, estes pontos finais só são expostos usando o protocolo [AMQP.](https://www.amqp.org/) O ponto final da invocação do método é exposto sobre o protocolo HTTPS.
  
  * *Receba mensagens de dispositivo-nuvem*. Este ponto final é compatível com hubs de [eventos Azure.](https://azure.microsoft.com/documentation/services/event-hubs/) Um serviço de back-end pode usá-lo para ler as [mensagens de dispositivo-para-nuvem](iot-hub-devguide-messages-d2c.md) enviadas pelos seus dispositivos. Você pode criar pontos finais personalizados no seu hub IoT além deste ponto final incorporado.
  
  * *Envie mensagens cloud-to-device e receba reconhecimentos*de entrega . Estes pontos finais permitem que a sua solução volte a enviar mensagens fiáveis [cloud-to-device](iot-hub-devguide-messages-c2d.md), e receber os correspondentes reconhecimentos de entrega ou expiração.
  
  * *Receba notificações*de ficheiros . Este ponto final de mensagens permite-lhe receber notificações de quando os seus dispositivos fazem o upload com sucesso de um ficheiro. 
  
  * *Invocação do método direto.* Este ponto final permite que um serviço back-end invoque um [método direto](iot-hub-devguide-direct-methods.md) num dispositivo.
  
  * Receba eventos de monitorização de *operações.* Este ponto final permite-lhe receber eventos de monitorização de operações se o seu hub IoT tiver sido configurado para os emitir. Para mais informações, consulte [a monitorização das operações do IoT Hub](iot-hub-operations-monitoring.md).

O artigo da [Azure IoT SDKs](iot-hub-devguide-sdks.md) descreve as várias formas de aceder a estes pontos finais.

Todos os pontos finais do IoT Hub utilizam o protocolo [TLS,](https://tools.ietf.org/html/rfc5246) e nenhum ponto final é nunca exposto em canais não encriptados/não seguros.

## <a name="custom-endpoints"></a>Pontos finais personalizados

Pode ligar os serviços Azure existentes na sua subscrição ao seu hub IoT para atuar como pontos finais para o encaminhamento de mensagens. Estes pontos finais funcionam como pontos finais de serviço e são usados como pias para rotas de mensagens. Os dispositivos não podem escrever diretamente para os pontos finais adicionais. Saiba mais sobre [o encaminhamento de mensagens.](../iot-hub/iot-hub-devguide-messages-d2c.md)

O IoT Hub suporta atualmente os seguintes serviços Azure como pontos finais adicionais:

* Recipientes de armazenamento Azure
* Hubs de Eventos
* Filas de Service Bus
* Tópicos de Service Bus

Para os limites do número de pontos finais que pode adicionar, ver [Quotas e estrangulamento](iot-hub-devguide-quotas-throttling.md).

Você pode usar o REST API [Get Endpoint Health](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) para obter o estado de saúde dos pontos finais. Recomendamos a utilização das [métricas do IoT Hub relacionadas](iot-hub-metrics.md) com a latência da mensagem de encaminhamento para identificar e depurar erros quando a saúde do ponto final está morta ou insalubre, pois esperamos que a latência seja maior quando o ponto final está num desses estados.

|Estado de Funcionamento|Descrição|
|---|---|
|saudável|O ponto final é aceitar mensagens como esperado.|
|insalubre|O ponto final não é aceitar mensagens como esperado e o IoT Hub está a tentar enviar dados para este ponto final. O estado de um ponto final pouco saudável será atualizado para saudável quando o IoT Hub estabelecer um estado de saúde eventualmente consistente.|
|desconhecido|O IoT Hub não estabeleceu uma ligação com o ponto final. Não foram entregues ou rejeitados mensagens deste ponto final.|
|mortos|O ponto final não é aceitar mensagens, depois de o IoT Hub ter retentado enviar mensagens para o período de novo julgamento.|

## <a name="field-gateways"></a>Gateways de campo

Numa solução IoT, um gateway de *campo* situa-se entre os seus dispositivos e os seus pontos finais do IoT Hub. Está tipicamente localizado perto dos seus dispositivos. Os seus dispositivos comunicam diretamente com o gateway de campo utilizando um protocolo suportado pelos dispositivos. O gateway de campo liga-se a um ponto final do IoT Hub usando um protocolo que é suportado pelo IoT Hub. Um gateway de campo pode ser um dispositivo de hardware dedicado ou um computador de baixa potência executando software de gateway personalizado.

Você pode usar [Azure IoT Edge](/azure/iot-edge/) para implementar um gateway de campo. O IoT Edge oferece funcionalidades como comunicações multiplexing de vários dispositivos para a mesma ligação IoT Hub.

## <a name="next-steps"></a>Passos seguintes

Outros tópicos de referência neste guia de desenvolvimento do IoT Hub incluem:

* [IoT Hub consulta linguagem para gémeos dispositivo, empregos e encaminhamento de mensagens](iot-hub-devguide-query-language.md)
* [Quotas e limitação](iot-hub-devguide-quotas-throttling.md)
* [Suporte IoT Hub MQTT](iot-hub-mqtt-support.md)
* [Compreenda o seu endereço IP do seu hub IoT](iot-hub-understand-ip-address.md)