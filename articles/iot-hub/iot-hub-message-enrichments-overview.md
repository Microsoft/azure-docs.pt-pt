---
title: Visão geral dos enriquecimentos de mensagens Azure IoT Hub
description: Este artigo mostra enriquecimentos de mensagens, que dão ao Hub IoT a capacidade de carimbar mensagens com informações adicionais antes de as mensagens serem enviadas para o ponto final designado.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: robinsh
ms.openlocfilehash: 10e999a7f0662c421d73872448506a9c9ca05975
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106079034"
---
# <a name="message-enrichments-for-device-to-cloud-iot-hub-messages"></a>Enriquecimentos de mensagens para mensagens IoT Hub de dispositivo para nuvem

*Enriquecimento de mensagens* é a capacidade do Hub IoT de *carimbar* mensagens com informações adicionais antes de as mensagens serem enviadas para o ponto final designado. Uma das razões para usar o enriquecimento de mensagens é incluir dados que podem ser usados para simplificar o processamento a jusante. Por exemplo, enriquecer mensagens de telemetria do dispositivo com uma etiqueta dupla do dispositivo pode reduzir a carga sobre os clientes para fazer chamadas API gémeas do dispositivo para esta informação.

![Fluxo de enriquecimentos de mensagens](./media/iot-hub-message-enrichments-overview/message-enrichments-flow.png)

Um enriquecimento de mensagens tem três elementos-chave:

* Nome ou chave de enriquecimento

* Um valor

* Um ou mais [pontos finais](iot-hub-devguide-endpoints.md) para os quais o enriquecimento deve ser aplicado.

A **chave** é uma corda. Uma chave só pode conter caracteres alfanuméricos ou estes caracteres especiais: hífen `-` (, sublinhado ( `_` , e período ( `.` ).

O **valor** pode ser qualquer um dos seguintes exemplos:

* Qualquer corda estática. Não são permitidos valores dinâmicos como condições, lógica, operações e funções. Por exemplo, se desenvolver uma aplicação SaaS que seja utilizada por vários clientes, pode atribuir um identificador a cada cliente e disponibilizar esse identificador na aplicação. Quando a aplicação for escorva, o IoT Hub carimbará as mensagens de telemetria do dispositivo com o identificador do cliente, possibilitando o processo das mensagens de forma diferente para cada cliente.

* O nome do hub IoT enviando a mensagem. Este valor é *$iothubname.*

* Informação do dispositivo twin, como o seu caminho. Exemplos seriam *$twin.tags.field* e *$twin.tags.latitude*.

   > [!NOTE]
   > Neste momento, apenas $iothubname, $twin.tags, $twin.properties.desejadas, e $twin.properties.relatados são variáveis suportadas para enriquecimento de mensagens.

Os enriquecimentos de mensagens são adicionados como propriedades de aplicação a mensagens enviadas para o(s) ponto final escolhidos.  

## <a name="applying-enrichments"></a>Aplicação de enriquecimentos

As mensagens podem vir de qualquer fonte de dados suportada pelo [encaminhamento de mensagens IoT Hub](iot-hub-devguide-messages-d2c.md), incluindo os seguintes exemplos:

* telemetria do dispositivo, como temperatura ou pressão
* notificações de alteração dupla do dispositivo - alterações no dispositivo twin
* eventos de ciclo de vida do dispositivo, tais como quando o dispositivo é criado ou eliminado

Pode adicionar enriquecimentos a mensagens que vão para o ponto final incorporado de um Hub IoT, ou mensagens que estão a ser encaminhadas para pontos finais personalizados, como o armazenamento Azure Blob, uma fila de autocarros de serviço ou um tópico de Service Bus.

Pode adicionar enriquecimentos a mensagens que estão a ser publicadas na Grade de Eventos selecionando o ponto final como Grade de Eventos. Criamos uma rota padrão no IoT Hub para dispositivos de telemetria, com base na subscrição da Sua Grade de Eventos. Esta rota única pode lidar com todas as subscrições da Grelha de Eventos. Pode configurar enriquecimentos para o ponto final da grelha de eventos depois de ter criado a subscrição da grelha de eventos para telemetria do dispositivo. Para mais informações, consulte [Iot Hub e Event Grid.](iot-hub-event-grid.md)

Os enriquecimentos são aplicados por ponto final. Se especificar cinco enriquecimentos a carimbar para um ponto final específico, todas as mensagens que vão para esse ponto final são carimbadas com os mesmos cinco enriquecimentos.

Os enriquecimentos podem ser configurados utilizando os seguintes métodos:

| **Método** | **Comando** |
| ----- | -----| 
| Portal | [Portal Azure](https://portal.azure.com) Veja o [tutorial de enriquecementos de mensagens](tutorial-message-enrichments.md) | 
| CLI do Azure   | [az iot hub mensagem-enriquecimento](/cli/azure/iot/hub/message-enrichment) |
| Azure PowerShell | [Add-AzIotHubMessageEnrichment](/powershell/module/az.iothub/add-aziothubmessageenrichment) |

Adicionar enriquecimentos de mensagens não adiciona latência ao encaminhamento de mensagens.

Para experimentar enriquecimentos de mensagens, consulte o [tutorial de enriquecimentos de mensagens](tutorial-message-enrichments.md)

## <a name="limitations"></a>Limitações

* Você pode adicionar até 10 enriquecimentos por IoT Hub para esses centros no nível padrão ou básico. Para ioT Hubs no nível livre, você pode adicionar até 2 enriquecimentos.

* Em alguns casos, se estiver a aplicar um enriquecimento com um valor definido para uma etiqueta ou propriedade no dispositivo twin, o valor será carimbado como um valor de cadeia. Por exemplo, se um valor de enriquecimento for definido para $twin.tags.field, as mensagens serão carimbadas com a cadeia "$twin.tags.field" em vez do valor desse campo a partir do gémeo. Isto acontece nos seguintes casos:

   * O seu IoT Hub está no nível básico. Os centros IoT de nível básico não suportam gémeos dispositivos.

   * O seu IoT Hub está no nível padrão, mas o dispositivo que envia a mensagem não tem nenhum dispositivo gémeo.

   * O seu IoT Hub está no nível padrão, mas o caminho duplo do dispositivo utilizado para o valor do enriquecimento não existe. Por exemplo, se o valor de enriquecimento for definido para $twin.tags.location, e o dispositivo twin não tiver uma propriedade de localização sob etiquetas, a mensagem é carimbada com a cadeia "$twin.tags.location". 

* As atualizações a um dispositivo gémeo podem demorar até cinco minutos a refletir-se no valor de enriquecimento correspondente.

* O tamanho total da mensagem, incluindo os enriquecimentos, não pode exceder 256 KB. Se o tamanho de uma mensagem exceder 256 KB, o Hub IoT deixará cair a mensagem. Pode utilizar [métricas do IoT Hub](monitor-iot-hub-reference.md#metrics) para identificar e depurar erros quando as mensagens são largadas. Por exemplo, pode monitorizar incompatível com as mensagens de *telemetria* *(d2c.telemetria.egress.inválido)* nas [métricas](monitor-iot-hub-reference.md#routing-metrics)de encaminhamento . Para saber mais, consulte [o Monitor IoT Hub.](monitor-iot-hub.md)

* Os enriquecimentos de mensagens não se aplicam a eventos digitais de mudança de gémeos.

## <a name="pricing"></a>Preços

Os enriquecimentos de mensagens estão disponíveis sem custos adicionais. Atualmente, é cobrado quando envia uma mensagem para um Hub IoT. Só é cobrado uma vez por essa mensagem, mesmo que a mensagem vá para vários pontos finais.

## <a name="next-steps"></a>Passos seguintes

Consulte estes artigos para obter mais informações sobre o encaminhamento de mensagens para um Hub IoT:

* [Tutoria de enriquecimentos de mensagem](tutorial-message-enrichments.md)

* [Utilize o encaminhamento de mensagens IoT Hub para enviar mensagens dispositivo-a-nuvem para diferentes pontos finais](iot-hub-devguide-messages-d2c.md)

* [Tutorial: Encaminhamento IoT Hub](tutorial-routing.md)
