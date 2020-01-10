---
title: Entender os pontos de extremidade do Hub IoT do Azure | Microsoft Docs
description: Guia do desenvolvedor-informações de referência sobre os pontos de extremidade voltados para o dispositivo do Hub IoT e voltados para o serviço.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: da6d17e42407048b7ecbcacade67ef48046d7fe1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75457116"
---
# <a name="reference---iot-hub-endpoints"></a>Referência-pontos de extremidade do Hub IoT

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="iot-hub-names"></a>Nomes de Hub IoT

Você pode encontrar o nome do host do Hub IoT que hospeda seus pontos de extremidade no portal na página de **visão geral** do Hub. Por padrão, o nome DNS de um hub IoT é semelhante a: `{your iot hub name}.azure-devices.net`.

## <a name="list-of-built-in-iot-hub-endpoints"></a>Lista de pontos de extremidade do Hub IoT internos

O Hub IoT do Azure é um serviço multilocatário que expõe sua funcionalidade a vários atores. O diagrama a seguir mostra os vários pontos de extremidade expostos pelo Hub IoT.

![Pontos finais do Hub IoT](./media/iot-hub-devguide-endpoints/endpoints.png)

A lista a seguir descreve os pontos de extremidade:

* **Provedor de recursos**. O provedor de recursos do Hub IoT expõe uma interface [Azure Resource Manager](../azure-resource-manager/management/overview.md) . Essa interface permite que os proprietários da assinatura do Azure criem e excluam hubs IoT e atualizem as propriedades do Hub IoT. As propriedades do Hub IoT regem [as políticas de segurança no nível do Hub](iot-hub-devguide-security.md#access-control-and-permissions), em oposição ao controle de acesso no nível do dispositivo e às opções funcionais para mensagens da nuvem para o dispositivo e do dispositivo para a nuvem. O provedor de recursos do Hub IoT também permite [Exportar identidades de dispositivo](iot-hub-devguide-identity-registry.md#import-and-export-device-identities).

* **Gerenciamento de identidade do dispositivo**. Cada Hub IoT expõe um conjunto de pontos de extremidade de REST HTTPS para gerenciar identidades de dispositivo (criar, recuperar, atualizar e excluir). As [identidades de dispositivo](iot-hub-devguide-identity-registry.md) são usadas para autenticação de dispositivo e controle de acesso.

* **Gerenciamento do dispositivo de entrelaçamento**. Cada Hub IoT expõe um conjunto de ponto de extremidade REST do HTTPS voltado ao serviço para consultar e atualizar [dispositivos gêmeos](iot-hub-devguide-device-twins.md) (atualizar marcas e propriedades).

* **Gerenciamento de trabalhos**. Cada Hub IoT expõe um conjunto de ponto de extremidade REST do HTTPS voltado ao serviço para consultar e gerenciar [trabalhos](iot-hub-devguide-jobs.md).

* **Pontos de extremidade do dispositivo**. para cada dispositivo no registo de identidades, o Hub IoT expõe um conjunto de pontos finais –

  * *Enviar mensagens do dispositivo para a nuvem*. Um dispositivo usa esse ponto de extremidade para [enviar mensagens do dispositivo para a nuvem](iot-hub-devguide-messages-d2c.md).

  * *Receber mensagens da nuvem para o dispositivo*. Um dispositivo usa esse ponto de extremidade para receber mensagens direcionadas da [nuvem para o dispositivo](iot-hub-devguide-messages-c2d.md).

  * *Inicie carregamentos de arquivos*. Um dispositivo usa esse ponto de extremidade para receber um URI de SAS do armazenamento do Azure do Hub IoT para [carregar um arquivo](iot-hub-devguide-file-upload.md).

  * *Recuperar e atualizar as propriedades do dispositivo de atualização*. Um dispositivo usa esse ponto de extremidade para acessar as propriedades do seu [dispositivo](iot-hub-devguide-device-twins.md).

  * *Receber solicitações de método direto*. Um dispositivo usa esse ponto de extremidade para escutar as solicitações do [método direto](iot-hub-devguide-direct-methods.md).

    Esses pontos de extremidade são expostos usando os protocolos [MQTT v 3.1.1](https://mqtt.org/), https 1,1 e [AMQP 1,0](https://www.amqp.org/) . O AMQP também está disponível sobre [WebSockets](https://tools.ietf.org/html/rfc6455) na porta 443.

* **Pontos de extremidade de serviço**. Cada Hub IoT expõe um conjunto de pontos de extremidade para que o back-end da solução se comunique com seus dispositivos. Com uma exceção, esses pontos de extremidade são expostos apenas usando o protocolo [AMQP](https://www.amqp.org/) . O ponto de extremidade de invocação de método é exposto pelo protocolo HTTPS.
  
  * *Receber mensagens do dispositivo para a nuvem*. Esse ponto de extremidade é compatível com os [hubs de eventos do Azure](https://azure.microsoft.com/documentation/services/event-hubs/). Um serviço de back-end pode usá-lo para ler as [mensagens do dispositivo para a nuvem](iot-hub-devguide-messages-d2c.md) enviadas por seus dispositivos. Você pode criar pontos de extremidade personalizados em seu hub IoT, além desse Endpoint interno.
  
  * *Enviar mensagens da nuvem para o dispositivo e receber confirmações de entrega*. Esses pontos de extremidade permitem que o back-end da sua solução envie mensagens confiáveis da [nuvem para o dispositivo](iot-hub-devguide-messages-c2d.md)e receba as confirmações de entrega ou expiração correspondentes.
  
  * *Receber notificações de arquivo*. Esse ponto de extremidade de mensagens permite que você receba notificações de quando os dispositivos carregam um arquivo com êxito. 
  
  * *Invocação de método direto*. Esse ponto de extremidade permite que um serviço de back-end invoque um [método direto](iot-hub-devguide-direct-methods.md) em um dispositivo.
  
  * *Receber eventos de monitoramento de operações*. Esse ponto de extremidade permite que você receba eventos de monitoramento de operações se o Hub IoT tiver sido configurado para emiti-los. Para obter mais informações, consulte [monitoramento de operações do Hub IOT](iot-hub-operations-monitoring.md).

O artigo [SDKs do Azure IOT](iot-hub-devguide-sdks.md) descreve as várias maneiras de acessar esses pontos de extremidade.

Todos os pontos de extremidade do Hub IoT usam o protocolo [TLS](https://tools.ietf.org/html/rfc5246) e nenhum ponto de extremidades é exposto em canais não criptografados/não seguros.

## <a name="custom-endpoints"></a>Pontos finais personalizados

Você pode vincular os serviços do Azure existentes em sua assinatura ao Hub IoT para atuar como pontos de extremidade para o roteamento de mensagens. Esses pontos de extremidade atuam como pontos de extremidade de serviço e são usados como coletores para rotas de mensagens. Os dispositivos não podem gravar diretamente nos pontos de extremidade adicionais. Saiba mais sobre o [Roteamento de mensagens](../iot-hub/iot-hub-devguide-messages-d2c.md).

O Hub IoT atualmente dá suporte aos seguintes serviços do Azure como pontos de extremidade adicionais:

* Contêineres de armazenamento do Azure
* Event Hubs
* Filas do Service Bus
* Tópicos do Service Bus

Para os limites do número de pontos de extremidade que você pode adicionar, consulte [cotas e limitação](iot-hub-devguide-quotas-throttling.md).

Você pode usar a integridade do [ponto de extremidade Get](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) da API REST para obter o status de integridade dos pontos de extremidade. É recomendável usar as [métricas do Hub IOT](iot-hub-metrics.md) relacionadas à latência da mensagem de roteamento para identificar e depurar erros quando a integridade do ponto de extremidade está inativa ou não íntegra, pois esperamos que a latência seja maior quando o ponto de extremidade estiver em um desses Estados.

|Estado de Funcionamento|Descrição|
|---|---|
|healthy|O ponto de extremidade está aceitando mensagens conforme o esperado.|
|não íntegro|O ponto de extremidade não está aceitando mensagens conforme esperado e o Hub IoT está tentando novamente enviar dados para esse ponto de extremidade. O status de um ponto de extremidade não íntegro será atualizado para íntegro quando o Hub IoT tiver estabelecido um estado de integridade eventualmente consistente.|
|desconhecido|O Hub IoT não estabeleceu uma conexão com o ponto de extremidade. Nenhuma mensagem foi entregue ou rejeitada neste ponto de extremidade.|
|mortos|O ponto de extremidade não está aceitando mensagens, após o Hub IoT tentar enviar mensagens para o período de nova avaliação.|

## <a name="field-gateways"></a>Gateways de campo

Em uma solução de IoT, um *Gateway de campo* fica entre os seus dispositivos e os pontos de extremidade do Hub IOT. Normalmente, ele está localizado próximo aos seus dispositivos. Seus dispositivos se comunicam diretamente com o gateway de campo usando um protocolo com suporte dos dispositivos. O gateway de campo se conecta a um ponto de extremidade de Hub IoT usando um protocolo que é suportado pelo Hub IoT. Um gateway de campo pode ser um dispositivo de hardware dedicado ou um computador de baixa energia que executa um software de gateway personalizado.

Você pode usar [Azure IOT Edge](/azure/iot-edge/) para implementar um gateway de campo. O IoT Edge oferece funcionalidade como a multiplexação de comunicações de vários dispositivos na mesma conexão do Hub IoT.

## <a name="next-steps"></a>Passos seguintes

Outros tópicos de referência neste guia do desenvolvedor do Hub IoT incluem:

* [Linguagem de consulta do Hub IoT para dispositivos gêmeos, trabalhos e roteamento de mensagens](iot-hub-devguide-query-language.md)
* [Quotas e limitação](iot-hub-devguide-quotas-throttling.md)
* [Suporte a MQTT do Hub IoT](iot-hub-mqtt-support.md)
* [Entender o endereço IP do Hub IoT](iot-hub-understand-ip-address.md)