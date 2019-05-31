---
title: Descrição geral da possível de mensagem do IoT Hub do Azure
description: Descrição geral da possível de mensagem para mensagens do IoT Hub do Azure
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: robinsh
ms.openlocfilehash: 2de945d2ef1d87b47ea81aac034916466d3c9bda
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/28/2019
ms.locfileid: "66258853"
---
# <a name="message-enrichments-for-device-to-cloud-iot-hub-messages-preview"></a>Possível de mensagem para mensagens do dispositivo-para-cloud do Hub de IoT (pré-visualização)

*Mensagem possível* é a capacidade do IoT Hub para *carimbo de data /* mensagens com informações adicionais antes das mensagens são enviadas para o ponto de final designado. Uma das razões para utilizar possível de mensagem é incluir os dados que podem ser utilizados para simplificar o processamento a jusante. Por exemplo, Aprimorando as mensagens de telemetria do dispositivo com uma etiqueta do dispositivo duplo pode reduzir a carga de clientes para fazer chamadas à API para obter informações do dispositivo duplo.

![Fluxo de possível de mensagens](./media/iot-hub-message-enrichments-overview/message-enrichments-flow.png)

Uma melhoria de mensagem tem três elementos principais:

* Nome de melhoria ou chave

* Um valor

* Um ou mais [pontos de extremidade](iot-hub-devguide-endpoints.md) para que deve ser aplicada a melhoria do utilizador.

A chave pode ser qualquer cadeia de caracteres.

O valor pode ser qualquer um dos exemplos a seguir:

* Qualquer cadeia de caracteres estática. Valores dinâmicos como condições, lógica, operações e as funções não são permitidos. Por exemplo, se desenvolver uma aplicação SaaS que é utilizada por vários clientes, pode atribuir um identificador a cada cliente e disponibilizar esse identificador no aplicativo. Quando o aplicativo for executado, o IoT Hub será carimbar o dispositivo mensagens de telemetria com o identificador do cliente, tornando possível processar as mensagens de forma diferente para cada cliente.

* Informações do dispositivo duplo, como o seu caminho. Exemplos seriam *$twin.tags.field* e *$twin.tags.latitude*.

* O nome do hub IoT enviar a mensagem. Este valor é *$iothubname*.

## <a name="applying-enrichments"></a>Aplicar possível

As mensagens podem ser provenientes de qualquer origem de dados suportada pelo [roteamento de mensagens do IoT Hub](iot-hub-devguide-messages-d2c.md), incluindo os exemplos seguintes:

* telemetria do dispositivo, tais como temperatura ou pressão
* notificações de alteração do dispositivo duplo - alterações no dispositivo duplo
* eventos de ciclo de vida do dispositivo, como quando o dispositivo é criado ou eliminado

Pode adicionar o possível para mensagens que vão para o ponto final interno de um IoT Hub ou mensagens que estão a ser encaminhadas para os pontos finais personalizados, como armazenamento de Blobs do Azure, uma fila do Service Bus ou um tópico do Service Bus.

Possível é aplicadas por ponto final. Se especificar cinco possível para ser marcados para um ponto final específico, todas as mensagens que passam para esse ponto final são marcadas com o mesmo cinco possível.

Para ver como testar o possível de mensagem, consulte o [tutorial possível de mensagem](tutorial-message-enrichments.md)

## <a name="limitations"></a>Limitações

* Pode adicionar até 10 possível por IoT Hub desses hubs no escalão standard ou basic. Para os Hubs IoT no escalão gratuito, pode adicionar até 2 possível.

* Em alguns casos, se está aplicando uma melhoria do utilizador com um valor definido como uma etiqueta ou propriedade no dispositivo duplo, o valor será marcado como um valor de cadeia de caracteres. Por exemplo, se um valor de melhoria é definido como $twin.tags.field, as mensagens vão ser marcadas com a cadeia de caracteres "$twin.tags.field" em vez do valor deste campo do duplo. Isto acontece nos seguintes casos:

   * O IoT Hub está no escalão básico. Os hubs IoT do escalão básico não suportam dispositivos duplos.

   * O IoT Hub está no escalão standard, mas o dispositivo que envia a mensagem não tem nenhum dispositivo duplo.

   * O IoT Hub está no escalão standard, mas o caminho de twin do dispositivo utilizado para o valor da melhoria do utilizador não existe. Por exemplo, se o valor de melhoria é definido como $twin.tags.location e o twin do dispositivo não tem uma propriedade de localização em etiquetas, a mensagem está marcada com a cadeia de caracteres "$twin.tags.location". 

* As atualizações para um dispositivo duplo podem demorar até cinco minutos sejam refletidas no valor de enriquecimento correspondente.

* O tamanho total de mensagens, incluindo a possível, não pode exceder os 256 KB. Se um tamanho de mensagem exceder os 256 KB, o IoT Hub irá remover a mensagem. Pode usar [métricas do IoT Hub](iot-hub-metrics.md) para identificar e depurar erros quando as mensagens são ignoradas. Por exemplo, pode monitorizar d2c.telemetry.egress.invalid.

## <a name="pricing"></a>Preços

Possível de mensagem está disponíveis sem nenhum custo adicional. Atualmente, é cobrado quando envia uma mensagem para um IoT Hub. É cobrado apenas uma vez para aquela mensagem, mesmo que a mensagem vai para vários pontos de extremidade.

## <a name="availability"></a>Disponibilidade

Esta capacidade está disponível em pré-visualização e está disponível em todas as regiões, exceto E.U.A. leste, E.U.A. oeste, Europa Ocidental, [do Azure Government](/azure-government/documentation-government-welcome.md), [Azure China 21Vianet](/azure/china/china-welcome.md), e [Azure Alemanha](https://azure.microsoft.com/global-infrastructure/germany/).

## <a name="next-steps"></a>Passos Seguintes

Veja os artigos seguintes para obter mais informações sobre o encaminhamento de mensagens para um IoT Hub:

* [Utilizar o encaminhamento de mensagens do IoT Hub para enviar mensagens do dispositivo para a cloud para diferentes pontos de extremidade](iot-hub-devguide-messages-d2c.md)

* [Tutorial: IoT Hub encaminhamento](tutorial-routing.md)