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
ms.openlocfilehash: 0b17a87fa02c382ae19cca6e4abcfff2ec475450
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/28/2019
ms.locfileid: "66252678"
---
# <a name="compare-message-routing-and-event-grid-for-iot-hub"></a>Comparar o encaminhamento de mensagens e o Event Grid para o IoT Hub

O IoT Hub do Azure fornece a capacidade de transmitir dados a partir de seus dispositivos ligados e integrar esses dados nas suas aplicações empresariais. IoT Hub oferece dois métodos para a integração de eventos de IoT para outros serviços do Azure ou para aplicativos de negócios. Este artigo aborda os dois recursos que fornecem esta capacidade, para que pode escolher qual é a opção mais adequada para seu cenário.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

**[Roteamento de mensagens do IoT Hub](iot-hub-devguide-messages-d2c.md)** : Esta funcionalidade do IoT Hub permite aos utilizadores encaminhar mensagens do dispositivo para a cloud para pontos finais de serviço, como contentores de armazenamento do Azure, os Hubs de eventos, filas do Service Bus e tópicos do Service Bus. Também encaminhamento fornece um recurso de consulta para filtrar os dados antes de encaminhamento para os pontos de extremidade. Além de dados de telemetria do dispositivo, também pode enviar [eventos sem ser de telemetria](iot-hub-devguide-messages-d2c.md#non-telemetry-events) que pode ser utilizado para acionar ações. 

**Integração do IoT Hub com o Event Grid**: O Azure Event Grid é um serviço de encaminhamento de eventos totalmente gerido que utiliza um publicar-subscrever o modelo. IoT Hub e o Event Grid que funcionam em conjunto para [integrar eventos do IoT Hub no Azure e serviços não pertencente ao Azure](iot-hub-event-grid.md), em tempo quase real. IoT Hub publica [eventos de dispositivo](iot-hub-event-grid.md#event-types), que estão disponível em geral e agora também publica os eventos de telemetria, que está em pré-visualização pública.

## <a name="differences"></a>Diferenças

Embora o roteamento de mensagens e o Event Grid ativar a configuração de alerta, existem algumas diferenças fundamentais entre os dois. Consulte a tabela seguinte para obter mais detalhes:

| Funcionalidade | Roteamento de mensagens do IoT Hub | Integração do IoT Hub com o Event Grid |
| ------- | --------------- | ---------- |
| **Mensagens do dispositivo e eventos** | Sim, o encaminhamento de mensagens pode ser utilizado para dados de telemetria e as alterações de duplo de relatório e eventos de ciclo de vida do dispositivo. | Sim, o Event Grid pode ser utilizado para dados de telemetria mas também pode comunicar quando os dispositivos são criados, eliminados, ligados e desligados a partir do IoT Hub |
| **Ordenação** | Sim, é mantida ordenação de eventos.  | Não, a ordem de eventos não é garantido. | 
| **Filtragem** | Avançados de filtragem nas propriedades da aplicação de mensagem, propriedades do sistema de mensagem, o corpo da mensagem, o etiquetas do dispositivo duplo e o dispositivo duplo propriedades. Para obter exemplos, consulte [sintaxe de consulta de encaminhamento de mensagens](iot-hub-devguide-routing-query-syntax.md). | Filtragem com base no tipo de evento, o tipo de assunto e atributos em cada evento. Para obter exemplos, consulte [compreender a filtragem de eventos em subscrições do Event Grid](../event-grid/event-filtering.md). Ao subscrever a eventos de telemetria, pode aplicar filtros adicionais nos dados filtrar nas propriedades da mensagem, duplo de corpo e o dispositivo de mensagem do seu IoT Hub, antes de publicar para o Event Grid. Ver [como filtrar eventos](../iot-hub/iot-hub-event-grid.md#filter-events). |
| **Pontos finais** | <ul><li>Hubs de Eventos</li> <li>Armazenamento de Blobs do Azure</li> <li>Fila do Service Bus</li> <li>Tópicos do Service Bus</li></ul><br>IoT Hub aos SKUs pagos (S1, S2 e S3) estão limitados a 10 pontos de extremidade personalizados. 100 rotas podem ser criadas por IoT Hub. | <ul><li>Funções do Azure</li> <li>Automatização do Azure</li> <li>Hubs de Eventos</li> <li>Aplicações Lógicas</li> <li>Blob de Armazenamento</li> <li>Tópicos Personalizados</li> <li>Armazenamento de filas</li> <li>Microsoft Flow</li> <li>Serviços de terceiros através de WebHooks</li></ul><br>500 pontos finais por IoT Hub suporta. Para obter a lista mais atualizada dos pontos finais, consulte [manipuladores de eventos do Event Grid](../event-grid/overview.md#event-handlers). |
| **Custo** | Não existe cobrança separada para o encaminhamento de mensagens. Apenas uma entrada de telemetria no IoT Hub é cobrada. Por exemplo, se tiver uma mensagem encaminhada para três diferentes pontos de extremidade, é cobrada apenas uma mensagem. | Não há nenhum custo do IoT Hub. Grelha de eventos oferece as primeiro 100.000 operações por mês gratuitamente e, em seguida, 0,60 us $ por milhão de operações posteriormente. |

## <a name="similarities"></a>Semelhanças

Roteamento de mensagens do IoT Hub e o Event Grid tem também semelhanças, alguns dos quais estão descritos na tabela a seguir:

| Funcionalidade | Roteamento de mensagens do IoT Hub | Integração do IoT Hub com o Event Grid |
| ------- | --------------- | ---------- |
| **Tamanho máximo da mensagem** | 256 KB, dispositivo para a cloud | 256 KB, dispositivo para a cloud |
| **Fiabilidade** | Alta: Fornece a cada mensagem para o ponto final, pelo menos, uma vez para cada rota. Expira a todas as mensagens que não são entregues dentro de uma hora. | Alta: Fornece a cada mensagem para o webhook, pelo menos, uma vez para cada subscrição. Expira a todos os eventos que não são entregues no prazo de 24 horas. | 
| **Escalabilidade** | Alta: Otimizado para suportar milhões de dispositivos ligados em simultâneo a enviar milhares de milhões de mensagens. | Alta: Com capacidade de encaminhamento de 10.000.000 eventos por segundo por região. |
| **Latência** | Baixa: Quase em tempo real. | Baixa: Quase em tempo real. |
| **Enviar para vários pontos de extremidade** | Sim, envie uma única mensagem para vários pontos de extremidade. | Sim, envie uma única mensagem para vários pontos de extremidade.  
| **Segurança** | O IOT Hub fornece identidade por dispositivo e controlo de acesso irrevogável. Para obter mais informações, consulte a [controlo de acesso do IoT Hub](iot-hub-devguide-security.md). | Event Grid fornece validação em três pontos: subscrições de eventos, a publicação de eventos e a entrega de eventos de webhook. Para obter mais informações, consulte [Event Grid segurança e autenticação](../event-grid/security-authentication.md). |

## <a name="how-to-choose"></a>Como escolher

Roteamento de mensagens do IoT Hub e a integração do IoT Hub com o Event Grid executar ações diferentes para alcançar resultados semelhantes. Elas obtêm informações de sua solução de IoT Hub tanto transmitir para que podem reagir a outros serviços. Então, como decidir qual devo utilizar? Considere as seguintes perguntas para ajudar a orientar a sua decisão: 

* **Que tipo de dados estão a enviar para os pontos finais?**

   Utilize o IoT Hub roteamento de mensagens quando tiver de enviar dados telemétricos para outros serviços. Mensagem de encaminhamento, também permite consultar as propriedades das aplicações e do sistema de mensagens, corpo da mensagem, etiquetas de twin do dispositivo e propriedades de twin do dispositivo.

   A integração do IoT Hub com o Event Grid funciona com eventos que ocorrem no serviço IoT Hub. Estes eventos do IoT Hub incluem dados de telemetria, dispositivo criados, eliminados, ligado e desligado. Ao subscrever a eventos de telemetria, pode aplicar filtros adicionais nos dados filtrar nas propriedades da mensagem, duplo de corpo e o dispositivo de mensagem do seu IoT Hub, antes de publicar para o Event Grid. Ver [como filtrar eventos](../iot-hub/iot-hub-event-grid.md#filter-events).

* **O que os pontos finais necessário receber essas informações?**

   Roteamento de mensagens do IoT Hub suporta um número limitado de pontos de extremidade exclusivos e tipos de ponto final, mas pode criar conectores para redirecionar os dados e eventos para pontos finais adicionais. Para obter uma lista completa de pontos finais suportados, consulte a tabela na secção anterior. 

   A integração do IoT Hub com o Event Grid suporta 500 pontos finais por IoT Hub e uma maior variedade de tipos de ponto final. Ingenuamente integra-se com as funções do Azure, o Logic Apps, filas de armazenamento e o Service Bus e também funciona com webhooks para estender o envio de dados fora do ecossistema de serviço do Azure e em aplicações empresariais de terceiros.

* **É importante se seus dados chegam em ordem?**

   Roteamento de mensagens do IoT Hub mantém a ordem na qual as mensagens são enviadas, para que chegam da mesma forma.

   Grelha de eventos não garante que os pontos finais vão receber eventos na mesma ordem em que ocorreu. Para os casos em que ordem absoluto das mensagens é significativo e/ou em que um consumidor tem um identificador exclusivo confiável de mensagens, recomendamos que utilize o encaminhamento de mensagens. 

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre [roteamento de mensagens do Hub de IoT](iot-hub-devguide-messages-d2c.md) e o [pontos finais do IoT Hub](iot-hub-devguide-endpoints.md).
* Saiba mais sobre o [Azure Event Grid](../event-grid/overview.md).
* Para saber como criar rotas de mensagens, consulte a [mensagens de dispositivo-para-cloud do Hub de IoT de processo utilizar rotas](../iot-hub/tutorial-routing.md) tutorial.
* Experimente a integração do Event Grid pela [enviando notificações de e-mail sobre eventos do IoT Hub do Azure com o Logic Apps](../event-grid/publish-iot-hub-events-to-logic-apps.md).