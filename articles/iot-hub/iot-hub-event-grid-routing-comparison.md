---
title: Comparar o Event Grid, encaminhamento para o IoT Hub | Documentos da Microsoft
description: IoT Hub oferece seu próprio serviço de encaminhamento de mensagens, mas também se integra com o Event Grid para publicação de eventos. Compare os dois recursos.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: kgremban
ms.openlocfilehash: e7d5ab3ebdf29fbf699220a3b214176ec4862739
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56672802"
---
# <a name="compare-message-routing-and-event-grid-for-iot-hub"></a>Comparar o encaminhamento de mensagens e o Event Grid para o IoT Hub

O IoT Hub do Azure fornece a capacidade de transmitir dados a partir de seus dispositivos ligados e integrar esses dados nas suas aplicações empresariais. IoT Hub oferece dois métodos para a integração de eventos de IoT para outros serviços do Azure ou para aplicativos de negócios. Este artigo aborda os dois recursos que fornecem esta capacidade, para que pode escolher qual é a opção mais adequada para seu cenário.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

**[Roteamento de mensagens do IoT Hub](iot-hub-devguide-messages-d2c.md)**: Esta funcionalidade do IoT Hub permite aos utilizadores encaminhar mensagens do dispositivo para a cloud para pontos finais de serviço, como contentores de armazenamento do Azure, os Hubs de eventos, filas do Service Bus e tópicos do Service Bus. Também encaminhamento fornece um recurso de consulta para filtrar os dados antes de encaminhamento para os pontos de extremidade. Além de dados de telemetria do dispositivo, também pode enviar [eventos sem ser de telemetria](iot-hub-devguide-messages-d2c.md#non-telemetry-events) que pode ser utilizado para acionar ações. 

**Integração do IoT Hub com o Event Grid**: O Azure Event Grid é um serviço de encaminhamento de eventos totalmente gerido que utiliza um publicar-subscrever o modelo. IoT Hub e o Event Grid que funcionam em conjunto para [integrar eventos do IoT Hub no Azure e serviços não pertencente ao Azure](iot-hub-event-grid.md), em tempo quase real. 

## <a name="differences"></a>Diferenças

Embora o roteamento de mensagens e o Event Grid ativar a configuração de alerta, existem algumas diferenças fundamentais entre os dois. Consulte a tabela seguinte para obter mais detalhes:

| Funcionalidade | Roteamento de mensagens do IoT Hub | Integração do IoT Hub com o Event Grid |
| ------- | --------------- | ---------- |
| **Mensagens do dispositivo** | Sim, o encaminhamento de mensagens pode ser utilizado para dados de telemetria. | Não, só pode ser utilizado o Event Grid para eventos do IoT Hub sem ser de telemetria. |
| **Eventos de dispositivos** | Sim, roteamento de mensagens pode reportar alterações duplo e eventos de ciclo de vida do dispositivo. | Sim, o Event Grid pode reportar quando os dispositivos são criados, eliminados, ligados e desligados a partir do IoT Hub |
| **Ordenação** | Sim, é mantida ordenação de eventos.  | Não, a ordem de eventos não é garantido. | 
| **Tamanho máximo da mensagem** | 256 KB, dispositivo para a cloud | 64 KB |
| **Filtragem** | Avançados de filtragem nas propriedades da aplicação de mensagem, propriedades do sistema de mensagem, o corpo da mensagem, o etiquetas do dispositivo duplo e o dispositivo duplo propriedades. Para obter exemplos, consulte [sintaxe de consulta de encaminhamento de mensagens](iot-hub-devguide-routing-query-syntax.md). | Filtragem baseada no sufixo/prefixo identificações de dispositivo, que funciona bem para serviços hierárquicos, como o armazenamento. |
| **Pontos finais** | <ul><li>Hubs de Eventos</li> <li>Armazenamento de Blobs do Azure</li> <li>Fila do Service Bus</li> <li>Tópicos do Service Bus</li></ul><br>IoT Hub aos SKUs pagos (S1, S2 e S3) estão limitados a 10 pontos de extremidade personalizados. 100 rotas podem ser criadas por IoT Hub. | <ul><li>Funções do Azure</li> <li>Automatização do Azure</li> <li>Hubs de Eventos</li> <li>Aplicações Lógicas</li> <li>Blob de Armazenamento</li> <li>Tópicos Personalizados</li> <li>Serviços de terceiros através de WebHooks</li></ul><br>Para obter a lista mais atualizada dos pontos finais, consulte [manipuladores de eventos do Event Grid](../event-grid/overview.md#event-handlers). |
| **Custo** | Não existe cobrança separada para o encaminhamento de mensagens. Apenas uma entrada de telemetria no IoT Hub é cobrada. Por exemplo, se tiver uma mensagem encaminhada para três diferentes pontos de extremidade, é cobrada apenas uma mensagem. | Não há nenhum custo do IoT Hub. Grelha de eventos oferece as primeiro 100.000 operações por mês gratuitamente e, em seguida, 0,60 us $ por milhão de operações posteriormente. |

## <a name="similarities"></a>Semelhanças

Roteamento de mensagens do IoT Hub e o Event Grid tem também semelhanças, alguns dos quais estão descritos na tabela a seguir:

| Funcionalidade | Roteamento de mensagens do IoT Hub | Integração do IoT Hub com o Event Grid |
| ------- | --------------- | ---------- |
| **Fiabilidade** | Alta: Fornece a cada mensagem para o ponto final, pelo menos, uma vez para cada rota. Expira a todas as mensagens que não são entregues dentro de uma hora. | Alta: Fornece a cada mensagem para o webhook, pelo menos, uma vez para cada subscrição. Expira a todos os eventos que não são entregues no prazo de 24 horas. | 
| **Escalabilidade** | Alta: Otimizado para suportar milhões de dispositivos ligados em simultâneo a enviar milhares de milhões de mensagens. | Alta: Com capacidade de encaminhamento de 10.000.000 eventos por segundo por região. |
| **Latência** | Baixo: Quase em tempo real. | Baixo: Quase em tempo real. |
| **Enviar para vários pontos de extremidade** | Sim, envie uma única mensagem para vários pontos de extremidade. | Sim, envie uma única mensagem para vários pontos de extremidade.  | 
| **Segurança** | O IOT Hub fornece identidade por dispositivo e controlo de acesso irrevogável. Para obter mais informações, consulte a [controlo de acesso do IoT Hub](iot-hub-devguide-security.md). | Event Grid fornece validação em três pontos: subscrições de eventos, a publicação de eventos e a entrega de eventos de webhook. Para obter mais informações, consulte [Event Grid segurança e autenticação](../event-grid/security-authentication.md). |

## <a name="how-to-choose"></a>Como escolher

Roteamento de mensagens do IoT Hub e a integração do IoT Hub com o Event Grid executar ações diferentes para alcançar resultados semelhantes. Elas obtêm informações de sua solução de IoT Hub tanto transmitir para que podem reagir a outros serviços. Então, como decidir qual devo utilizar? Considere as seguintes perguntas para ajudar a orientar a sua decisão: 

* **Que tipo de dados estão a enviar para os pontos finais?**

   Utilize o IoT Hub roteamento de mensagens quando tiver de enviar dados telemétricos para outros serviços. Mensagem de encaminhamento, também permite consultar as propriedades das aplicações e do sistema de mensagens, corpo da mensagem, etiquetas de twin do dispositivo e propriedades de twin do dispositivo.

   A integração do IoT Hub com o Event Grid funciona com eventos que ocorrem no serviço IoT Hub. Estes eventos do IoT Hub incluem dispositivo criados, eliminados, ligado e desligado. 

* **O que os pontos finais necessário receber essas informações?**

   Roteamento de mensagens do IoT Hub suporta pontos de extremidade limitados, mas pode criar conectores para redirecionar os dados e eventos para pontos finais adicionais. Para obter uma lista completa de pontos finais suportados, consulte a tabela na secção anterior. 

   A integração do IoT Hub com o Event Grid suporta mais pontos finais. Ele também funciona com webhooks para estender o encaminhamento fora do ecossistema de serviço do Azure e em aplicações empresariais de terceiros. 

* **É importante se seus dados chegam em ordem?**

   Roteamento de mensagens do IoT Hub mantém a ordem na qual as mensagens são enviadas, para que chegam da mesma forma.

   Grelha de eventos não garante que os pontos finais vão receber eventos na mesma ordem em que ocorreu. No entanto, o esquema de eventos incluem um carimbo que pode ser utilizado para identificar a ordem após os eventos deparar-se com o ponto final. 

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre [roteamento de mensagens do Hub de IoT](iot-hub-devguide-messages-d2c.md) e o [pontos finais do IoT Hub](iot-hub-devguide-endpoints.md).
* Saiba mais sobre o [Azure Event Grid](../event-grid/overview.md).
* Para saber como criar rotas de mensagens, consulte a [mensagens de dispositivo-para-cloud do Hub de IoT de processo utilizar rotas](../iot-hub/tutorial-routing.md) tutorial.
* Experimente a integração do Event Grid pela [enviando notificações de e-mail sobre eventos do IoT Hub do Azure com o Logic Apps](../event-grid/publish-iot-hub-events-to-logic-apps.md).