---
title: Visão geral dos enriquecimentos de mensagens Azure IoT Hub
description: Este artigo mostra enriquecimentos de mensagens, que dão ao IoT Hub a capacidade de carimbar mensagens com informações adicionais antes de as mensagens serem enviadas para o ponto final designado.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: robinsh
ms.openlocfilehash: c3dbd01faf61c164c88f09b0da03c07be4abd187
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75429113"
---
# <a name="message-enrichments-for-device-to-cloud-iot-hub-messages"></a>Enriquecimento de mensagens para mensagens IoT Hub dispositivo-para-nuvem

O enriquecimento de *mensagens* é a capacidade do IoT Hub *carimbar* mensagens com informações adicionais antes de as mensagens serem enviadas para o ponto final designado. Uma das razões para utilizar o enriquecimento de mensagens é incluir dados que possam ser usados para simplificar o processamento a jusante. Por exemplo, o enriquecimento de mensagens de telemetria do dispositivo com uma etiqueta dupla de dispositivos pode reduzir a carga nos clientes para fazer com que o dispositivo twin API admente apresente chamadas para esta informação.

![Fluxo de enriquecimento de mensagens](./media/iot-hub-message-enrichments-overview/message-enrichments-flow.png)

Um enriquecimento de mensagens tem três elementos-chave:

* Nome ou chave de enriquecimento

* Um valor

* Um ou mais [pontos finais](iot-hub-devguide-endpoints.md) para os quais o enriquecimento deve ser aplicado.

A **chave** é uma corda. Uma chave só pode conter caracteres alfanuméricos`-`ou estes`_`caracteres especiais: hífen ( ), sublinhado ( e período ).`.`

O **valor** pode ser qualquer um dos seguintes exemplos:

* Qualquer corda estática. Valores dinâmicos como condições, lógica, operações e funções não são permitidos. Por exemplo, se desenvolver uma aplicação SaaS que é utilizada por vários clientes, pode atribuir um identificador a cada cliente e disponibilizar esse identificador na aplicação. Quando a aplicação estiver em funcionamento, o IoT Hub carimbará as mensagens de telemetria do dispositivo com o identificador do cliente, tornando possível processar as mensagens de forma diferente para cada cliente.

* O nome do centro ioT enviando a mensagem. Este valor é *$iothubname.*

* Informações do dispositivo twin, como o seu caminho. Exemplos seriam *$twin.tags.field* e *$twin.tags.latitude*.

   > [!NOTE]
   > Neste momento, apenas $iothubname, $twin.tags, $twin.properties.desejadas e $twin.properties.reported são variáveis suportadas para enriquecimento de mensagens.

Os enriquecimentos de mensagens são adicionados como propriedades de aplicação a mensagens enviadas para o ponto final escolhido.  

## <a name="applying-enrichments"></a>Aplicação de enriquecimentos

As mensagens podem vir de qualquer fonte de dados suportada pelo [encaminhamento de mensagens IoT Hub,](iot-hub-devguide-messages-d2c.md)incluindo os seguintes exemplos:

* telemetria do dispositivo, como temperatura ou pressão
* notificações de alteração de twin dispositivo -- alterações no dispositivo twin
* dispositivo eventos de ciclo de vida, como quando o dispositivo é criado ou eliminado

Você pode adicionar enriquecimentos a mensagens que vão para o ponto final incorporado de um IoT Hub, ou mensagens que estão sendo encaminhadas para pontos finais personalizados como armazenamento Azure Blob, uma fila de ônibus de serviço, ou um tópico de ônibus de serviço.

Pode adicionar enriquecimentos a mensagens que estão a ser publicadas na Grelha de Eventos, selecionando o ponto final como Grelha de Eventos. Criamos uma rota padrão no IoT Hub para telemetria de dispositivos, com base na subscrição da Sua Grelha de Eventos. Esta única rota pode lidar com todas as suas subscrições da Rede de Eventos. Pode configurar enriquecimentos para o ponto final da grelha de eventos depois de ter criado a subscrição da grelha de eventos para a telemetria do dispositivo. Para mais informações, consulte [O Hub e A Grelha de Eventos.](iot-hub-event-grid.md)

Os enriquecimentos são aplicados por ponto final. Se especificar cinco enriquecimentos a carimbar para um ponto final específico, todas as mensagens que vão para esse ponto final são carimbadas com os mesmos cinco enriquecimentos.

Os enriquecimentos podem ser configurados utilizando os seguintes métodos:

| **Método** | **Comando** |
| ----- | -----| 
| Portal | [Portal do Azure](https://portal.azure.com) | Veja o tutorial de [enriquecimento de mensagens](tutorial-message-enrichments.md) | 
| CLI do Azure   | [az iot hub mensagem-enriquecimento](https://docs.microsoft.com/cli/azure/iot/hub/message-enrichment?view=azure-cli-latest) |
| Azure PowerShell | [Add-AzIotHubMessageEnrichment](https://docs.microsoft.com/powershell/module/az.iothub/add-aziothubmessageenrichment?view=azps-2.8.0) |

Adicionar enriquecimentos de mensagens não adiciona latência ao encaminhamento da mensagem.

Para experimentar enriquecimentos de mensagens, consulte o tutorial de [enriquecimento de mensagens](tutorial-message-enrichments.md)

## <a name="limitations"></a>Limitações

* Você pode adicionar até 10 enriquecimentos por IoT Hub para esses centros no nível padrão ou básico. Para ioT Hubs no nível livre, você pode adicionar até 2 enriquecimentos.

* Em alguns casos, se estiver a aplicar um enriquecimento com um valor definido a uma etiqueta ou propriedade no dispositivo twin, o valor será carimbado como um valor de cadeia. Por exemplo, se um valor de enriquecimento for definido para $twin.tags.field, as mensagens serão carimbadas com a corda "$twin.tags.field" em vez do valor desse campo a partir do gémeo. Isto acontece nos seguintes casos:

   * O seu Hub IoT está no nível básico. Os centros básicos de ioT de nível básico não suportam gémeos dispositivos.

   * O seu IoT Hub está no nível padrão, mas o dispositivo que envia a mensagem não tem dispositivo gémeo.

   * O seu IoT Hub está no nível padrão, mas o caminho gémeo do dispositivo utilizado para o valor do enriquecimento não existe. Por exemplo, se o valor de enriquecimento for definido para $twin.tags.location, e o dispositivo twin não tiver uma propriedade de localização sob etiquetas, a mensagem é carimbada com a corda "$twin.tags.location". 

* As atualizações de um dispositivo twin podem demorar até cinco minutos a refletir no valor de enriquecimento correspondente.

* O tamanho total da mensagem, incluindo os enriquecimentos, não pode exceder 256 KB. Se um tamanho de mensagem exceder 256 KB, o IoT Hub deixará cair a mensagem. Pode utilizar [métricas do Hub IoT](iot-hub-metrics.md) para identificar e depurar erros quando as mensagens são retiradas. Por exemplo, pode monitorizar d2c.telemetria.egress.inválido.

* Os enriquecimentos de mensagens não se aplicam a eventos de mudança de duplas digitais (parte da [pré-visualização pública IoT Plug e Play).](../iot-pnp/overview-iot-plug-and-play.md)

## <a name="pricing"></a>Preços

Os enriquecimentos de mensagens estão disponíveis sem custos adicionais. Atualmente, é cobrado quando envia uma mensagem para um Hub IoT. Só é cobrado uma vez por essa mensagem, mesmo que a mensagem vá para vários pontos finais.

## <a name="next-steps"></a>Passos seguintes

Consulte estes artigos para obter mais informações sobre mensagens de encaminhamento para um Hub IoT:

* [Tutorial de enriquecimento de mensagens](tutorial-message-enrichments.md)

* [Use o encaminhamento de mensagens IoT Hub para enviar mensagens dispositivo-cloud para diferentes pontos finais](iot-hub-devguide-messages-d2c.md)

* [Tutorial: Encaminhamento do Hub IoT](tutorial-routing.md)
