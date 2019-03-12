---
title: Compreender os pontos finais do IoT Hub do Azure | Documentos da Microsoft
description: Guia do programador - informações de referência sobre o IoT Hub pontos finais com acesso de dispositivo e voltado para o serviço.
author: robinsh
manager: philmea
ms.author: robin.shahan
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/18/2018
ms.openlocfilehash: 28019163cfec1a9d2e3c12346a6aba2bd00b30b1
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57539552"
---
# <a name="reference---iot-hub-endpoints"></a>Referência - pontos finais do IoT Hub

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="iot-hub-names"></a>Nomes do IoT Hub

Pode encontrar o nome de anfitrião do hub IoT que aloja os pontos finais no portal no seu hub **descrição geral** página. Por predefinição, o nome DNS de um hub IoT é semelhante a: `{your iot hub name}.azure-devices.net`.

Pode utilizar o DNS do Azure para criar um nome DNS personalizado para o seu hub IoT. Para obter mais informações, veja [Utilizar o DNS do Azure para oferecer definições de domínio personalizado para um serviço do Azure](../dns/dns-custom-domain.md).

## <a name="list-of-built-in-iot-hub-endpoints"></a>Lista de pontos finais incorporados do IoT Hub

O IoT Hub do Azure é um serviço de multi-inquilino, que expõe sua funcionalidade para vários atores. O diagrama seguinte mostra vários pontos de extremidade que expõe o IoT Hub.

![Pontos finais do Hub IoT](./media/iot-hub-devguide-endpoints/endpoints.png)

A lista seguinte descreve os pontos de extremidade:

* **Fornecedor de recursos**. O fornecedor de recursos do IoT Hub expõe um [do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) interface. Essa interface permite que os proprietários de subscrições do Azure para criar e eliminar os hubs IoT e para atualizar as propriedades do hub IoT. As propriedades do IoT Hub regem [políticas de segurança ao nível do hub](iot-hub-devguide-security.md#access-control-and-permissions), em vez de controlo de acesso ao nível do dispositivo e as opções de funcionais para mensagens na cloud para o dispositivo e dispositivo-para-cloud. O fornecedor de recursos do IoT Hub também permite-lhe [Exportar identidades do dispositivo](iot-hub-devguide-identity-registry.md#import-and-export-device-identities).

* **Gestão de identidades de dispositivo**. Cada hub IoT expõe um conjunto de pontos finais de HTTPS REST para gerir identidades de dispositivos (criar, obter, atualizar e eliminar). [Identidades do dispositivo](iot-hub-devguide-identity-registry.md) são utilizados para controlo de acesso e autenticação do dispositivo.

* **Gestão do dispositivo duplo**. Cada hub IoT expõe um conjunto de ponto final de REST de HTTPS de serviço com acesso à consulta e atualização [dispositivos duplos](iot-hub-devguide-device-twins.md) (etiquetas e propriedades de atualização).

* **Tarefas de gestão**. Cada hub IoT expõe um conjunto de ponto final de REST de HTTPS de serviço com acesso à consultar e gerir [tarefas](iot-hub-devguide-jobs.md).

* **Pontos finais do dispositivo**. Para cada dispositivo no registo de identidade, o IoT Hub expõe um conjunto de pontos de extremidade:

  * *Enviar mensagens dispositivo-para-cloud*. Um dispositivo utiliza este ponto final para [enviar mensagens dispositivo-para-cloud](iot-hub-devguide-messages-d2c.md).

  * *Receber mensagens da cloud para dispositivo*. Um dispositivo utiliza este ponto final para receber um alvo [mensagens da cloud para dispositivo](iot-hub-devguide-messages-c2d.md).

  * *Iniciar carregamentos de ficheiros*. Um dispositivo utiliza este ponto final para receber um URI de SAS do armazenamento do Azure a partir do IoT Hub para [carregar um ficheiro](iot-hub-devguide-file-upload.md).

  * *Obter e atualizar as propriedades dos dispositivos duplos*. Um dispositivo utiliza este ponto final para aceder aos respetivos [dispositivo duplo](iot-hub-devguide-device-twins.md)do propriedades.

  * *Receber pedidos de método direto*. Um dispositivo utiliza este ponto final para escutar [método direto](iot-hub-devguide-direct-methods.md)do pedidos.

    Estes pontos finais expostos por meio [MQTT v3.1.1](https://mqtt.org/), HTTPS 1.1, e [AMQP 1.0](https://www.amqp.org/) protocolos. Também está disponível através de AMQP [WebSockets](https://tools.ietf.org/html/rfc6455) na porta 443.

* **Pontos finais de serviço**. Cada hub IoT expõe um conjunto de pontos finais para a sua solução de back-end comunicar com os seus dispositivos. Com uma exceção, estes pontos finais são expostos apenas com o [AMQP](https://www.amqp.org/) protocolo. O ponto final de invocação de método é exposto através do protocolo HTTPS.
  
  * *Receber mensagens dispositivo-para-cloud*. Este ponto final é compatível com [os Hubs de eventos do Azure](https://azure.microsoft.com/documentation/services/event-hubs/). Um serviço de back-end pode utilizá-lo para ler os [mensagens dispositivo-para-cloud](iot-hub-devguide-messages-d2c.md) enviados pelos seus dispositivos. Pode criar pontos finais personalizados no seu hub IoT para além deste ponto final incorporado.
  
  * *Enviar mensagens da cloud para dispositivo e receber confirmações de entrega*. Estes pontos finais ativar a sua solução de back-end enviar fiável [mensagens da cloud para dispositivo](iot-hub-devguide-messages-c2d.md)e para receber o confirmações de expiração ou entrega correspondente.
  
  * *Receber notificações de ficheiro*. Este ponto final de mensagens permite-lhe receber notificações de quando os dispositivos com êxito a carregar um ficheiro. 
  
  * *Direcionar a invocação de método*. Este ponto final permite que um serviço de back-end invocar um [método direto](iot-hub-devguide-direct-methods.md) num dispositivo.
  
  * *Receber eventos de monitorização de operações*. Este ponto final permite-lhe receber eventos de monitorização, se seu hub IoT tiver sido configurado para emiti-los de operações. Para obter mais informações, consulte [monitorização de operações do IoT Hub](iot-hub-operations-monitoring.md).

O [do Azure IoT SDKs](iot-hub-devguide-sdks.md) artigo descreve as várias formas de aceder a estes pontos finais.

Todos os pontos de extremidade do IoT Hub utilizam o [TLS](https://tools.ietf.org/html/rfc5246) protocolo e nenhum ponto final nunca é exposto em canais não encriptados/desprotegido.

## <a name="custom-endpoints"></a>Pontos finais personalizados

Pode ligar os serviços do Azure existentes na sua subscrição ao seu hub IoT para atuar como pontos finais para encaminhamento de mensagens. Estes pontos finais funcionam como pontos finais de serviço e são utilizados como sinks para rotas de mensagens. Dispositivos não é possível escrever diretamente para os pontos finais adicionais. Saiba mais sobre [roteamento de mensagens](../iot-hub/iot-hub-devguide-messages-d2c.md).

Atualmente, o IoT Hub suporta os seguintes serviços do Azure como pontos finais adicionais:

* Contentores de Armazenamento do Azure
* Hubs de Eventos
* Filas do Service Bus
* Tópicos do Service Bus

Para os limites no número de pontos de extremidade pode adicionar, ver [Quotas e limitação](iot-hub-devguide-quotas-throttling.md).

Pode utilizar a API REST [obter estado de funcionamento do ponto de extremidade](https://docs.microsoft.com/de-de/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) para obter o estado de funcionamento dos pontos finais. Recomendamos que utilize o [métricas do IoT Hub](iot-hub-metrics.md) relacionadas com a latência da mensagem de encaminhamento para identificar e depurar erros quando o estado de funcionamento do ponto final está inativo ou mau estado de funcionamento.

|Estado de Funcionamento|Descrição|
|---|---|
|bom estado de funcionamento|O ponto final está a aceitar mensagens conforme esperado.|
|Mau estado de funcionamento|O ponto final não está a aceitar mensagens conforme esperado e o IoT Hub está a tentar novamente para enviar dados para este ponto final. O estado de um ponto de extremidade de mau estado de funcionamento será atualizado para o bom estado de funcionamento quando o IoT Hub estabeleceu um Estado de funcionamento eventualmente consistente do Estado de funcionamento.|
|desconhecido|IoT Hub não estabeleceu uma conexão com o ponto final. Não existem mensagens foram entregues ao ou rejeitadas a partir deste ponto final.|
|papel já era|O ponto final não está a aceitar mensagens, depois do IoT Hub repetida envio de mensagens para o período de retrial.|

## <a name="field-gateways"></a>Gateways de campo

Numa solução de IoT, um *gateway de campo* encontra-se entre os dispositivos e os pontos finais do IoT Hub. Ele está normalmente localizado perto dos seus dispositivos. Os dispositivos comunicam diretamente com o gateway de campo com um protocolo suportado pelos dispositivos. O gateway de campo liga-se para um ponto de final do IoT Hub com um protocolo suportado pelo IoT Hub. Um gateway de campo pode ser um dispositivo de hardware dedicado ou um computador de baixa energia com o software de gateway personalizada.

Pode usar [do Azure IoT Edge](/azure/iot-edge/) para implementar um gateway de campo. IoT Edge oferece funcionalidade como comunicações de multiplexação de vários dispositivos para a mesma ligação do IoT Hub.

## <a name="next-steps"></a>Passos Seguintes

Outros tópicos de referência neste guia de programador do IoT Hub incluem:

* [Linguagem de consulta do IoT Hub para dispositivos duplos, tarefas e encaminhamento de mensagens](iot-hub-devguide-query-language.md)
* [Quotas e limitação](iot-hub-devguide-quotas-throttling.md)
* [Suporte para MQTT de Hub IoT](iot-hub-mqtt-support.md)
