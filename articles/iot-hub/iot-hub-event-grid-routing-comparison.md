---
title: Compare a Grelha de Eventos, encaminhamento para IoT Hub [ Hub] Microsoft Docs
description: O IoT Hub oferece o seu próprio serviço de encaminhamento de mensagens, mas também integra-se com a Event Grid para a publicação de eventos. Compare as duas características.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: kgremban
ms.openlocfilehash: 877467b65d346c871dd93f4b3f96b2c1664fa4b9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "73906799"
---
# <a name="compare-message-routing-and-event-grid-for-iot-hub"></a>Compare o encaminhamento de mensagens e a grelha de eventos para o Hub IoT

O Azure IoT Hub fornece a capacidade de transmitir dados dos seus dispositivos conectados e integrar esses dados nas suas aplicações de negócio. O IoT Hub oferece dois métodos para integrar eventos IoT em outros serviços Azure ou aplicações empresariais. Este artigo discute as duas funcionalidades que fornecem esta capacidade, para que possa escolher qual a opção melhor para o seu cenário.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

**[Encaminhamento de mensagens IoT Hub](iot-hub-devguide-messages-d2c.md)**: Esta funcionalidade IoT Hub permite aos utilizadores direcionar mensagens dispositivo-nuvem para servir pontos finais como contentores de armazenamento Azure, Hubs de Eventos, filas de ônibus de serviço e tópicos de ônibus de serviço. O encaminhamento também fornece uma capacidade de consulta para filtrar os dados antes de encaminha-lo para os pontos finais. Além dos dados de telemetria do dispositivo, também pode enviar [eventos de não telemetria](iot-hub-devguide-messages-d2c.md#non-telemetry-events) que podem ser usados para desencadear ações. 

**A integração do IoT Hub com a Event Grid**: Azure Event Grid é um serviço de encaminhamento de eventos totalmente gerido que utiliza um modelo de subscrição de editores. O IoT Hub e a Event Grid trabalham em conjunto para [integrar eventos do IoT Hub em serviços Azure e não-Azure,](iot-hub-event-grid.md)em tempo quase real. O IoT Hub publica eventos de [dispositivos](iot-hub-event-grid.md#event-types) e eventos de telemetria.

## <a name="differences"></a>Diferenças

Embora tanto o encaminhamento de mensagens como a Rede de Eventos permitam a configuração do alerta, existem algumas diferenças fundamentais entre os dois. Consulte a tabela seguinte para obter mais detalhes:

| Funcionalidade | Encaminhamento de mensagem IoT Hub | Integração do IoT Hub com grelha de eventos |
| ------- | --------------- | ---------- |
| **Mensagens e eventos do dispositivo** | Sim, o encaminhamento de mensagens pode ser usado para dados de telemetria, relatar alterações gémeas do dispositivo, eventos de ciclo de vida do dispositivo e eventos de mudança de twin digital (parte da [pré-visualização pública IoT Plug e Play).](../iot-pnp/overview-iot-plug-and-play.md) | Sim, a Grelha de Eventos pode ser usada para dados de telemetria, mas também pode reportar quando os dispositivos são criados, eliminados, conectados e desligados do IoT Hub |
| **Ordenação** | Sim, a ordem dos acontecimentos é mantida.  | Não, a ordem dos acontecimentos não está garantida. | 
| **Filtragem** | Filtragem rica em propriedades de aplicação de mensagens, propriedades do sistema de mensagens, corpo de mensagem, etiquetas gémeas do dispositivo e propriedades gémeas do dispositivo. A filtragem não é aplicada a eventos digitais de mudança de gémeos. Por exemplo, consulte [Message Routing Query Syntax](iot-hub-devguide-routing-query-syntax.md). | Filtragem com base no tipo de evento, tipo de assunto e atributos em cada evento. Por exemplo, consulte [Compreender eventos de filtragem em Assinaturas de Grelha](../event-grid/event-filtering.md)de Eventos . Ao subscrever eventos de telemetria, pode aplicar filtros adicionais nos dados para filtrar as propriedades da mensagem, corpo de mensagem e dispositivo gémeo no seu Hub IoT, antes de publicar na Event Grid. Veja [como filtrar eventos.](../iot-hub/iot-hub-event-grid.md#filter-events) |
| **Pontos Finais** | <ul><li>Hubs de Eventos</li> <li>Armazenamento de Blobs do Azure</li> <li>Fila do Service Bus</li> <li>Tópicos de ônibus de serviço</li></ul><br>As IoT Hub SKUs pagas (S1, S2 e S3) estão limitadas a 10 pontos finais personalizados. Podem ser criadas 100 rotas por Hub IoT. | <ul><li>Funções do Azure</li> <li>Automatização do Azure</li> <li>Hubs de Eventos</li> <li>Aplicações Lógicas</li> <li>Blob de Armazenamento</li> <li>Tópicos Personalizados</li> <li>Armazenamento de filas</li> <li>Microsoft Flow</li> <li>Serviços de terceiros através do WebHooks</li></ul><br>São apoiados 500 pontos finais por Hub IoT. Para obter a lista mais atualizada de pontos finais, consulte os manipuladores de [eventos da Rede](../event-grid/overview.md#event-handlers)de Eventos . |
| **Custo** | Não há nenhuma taxa separada para o encaminhamento de mensagens. É cobrada apenas ingresso de telemetria no IoT Hub. Por exemplo, se tiver uma mensagem direcionada para três pontos finais diferentes, é cobrado apenas por uma mensagem. | Não há nenhuma acusação do IoT Hub. O Event Grid oferece as primeiras 100.000 operações por mês gratuitamente, e depois $0,60 por milhão de operações. |

## <a name="similarities"></a>Semelhanças

O encaminhamento de mensagens IoT Hub e a Grelha de Eventos também têm semelhanças, algumas das quais são detalhadas na tabela seguinte:

| Funcionalidade | Encaminhamento de mensagem IoT Hub | Integração do IoT Hub com grelha de eventos |
| ------- | --------------- | ---------- |
| **Tamanho máximo da mensagem** | 256 KB, dispositivo-para-nuvem | 256 KB, dispositivo-para-nuvem |
| **Fiabilidade** | Alto: Entrega cada mensagem ao ponto final pelo menos uma vez para cada rota. Expira todas as mensagens que não são entregues dentro de uma hora. | Alto: Entrega cada mensagem ao webhook pelo menos uma vez para cada subscrição. Expira todos os eventos que não são entregues dentro de 24 horas. | 
| **Escalabilidade** | Alto: Otimizado para suportar milhões de dispositivos conectados simultaneamente enviando milhares de milhões de mensagens. | Alta: Capaz de encaminhar 10.000.000 eventos por segundo por região. |
| **Latência** | Em tempo quase real. | Em tempo quase real. |
| **Enviar para vários pontos finais** | Sim, envie uma única mensagem para vários pontos finais. | Sim, envie uma única mensagem para vários pontos finais.  
| **Segurança** | O Iot Hub fornece identidade por dispositivo e controlo de acesso revogável. Para mais informações, consulte o controlo de [acesso ioT Hub](iot-hub-devguide-security.md). | A Event Grid fornece validação em três pontos: subscrições de eventos, publicação de eventos e entrega de eventos webhook. Para mais informações, consulte [a segurança e a autenticação da Rede de Eventos.](../event-grid/security-authentication.md) |

## <a name="how-to-choose"></a>Como escolher

O encaminhamento de mensagens IoT Hub e a integração do IoT Hub com a Event Grid realizam diferentes ações para obter resultados semelhantes. Ambos pegam na informação da sua solução IoT Hub e transmitem-na para que outros serviços possam reagir. Então, como decide qual usar? Considere as seguintes questões para ajudar a orientar a sua decisão: 

* **Que tipo de dados está a enviar para os pontos finais?**

   Utilize o encaminhamento de mensagens IoT Hub quando tiver de enviar dados de telemetria para outros serviços. O encaminhamento de mensagens também permite consultar a aplicação de mensagens e propriedades do sistema, corpo de mensagem, etiquetas gémeas do dispositivo e propriedades gémeas do dispositivo.

   A integração do IoT Hub com a Event Grid trabalha com eventos que ocorrem no serviço IoT Hub. Estes eventos ioT Hub incluem dados de telemetria, dispositivo criado, eliminado, conectado e desligado. Ao subscrever eventos de telemetria, pode aplicar filtros adicionais nos dados para filtrar as propriedades da mensagem, corpo de mensagem e dispositivo gémeo no seu Hub IoT, antes de publicar na Event Grid. Veja [como filtrar eventos.](../iot-hub/iot-hub-event-grid.md#filter-events)

* **Que pontos finais precisam de receber esta informação?**

   O encaminhamento de mensagens IoT Hub suporta um número limitado de pontos finais únicos e tipos de pontos finais, mas pode construir conectores para redirecionar os dados e eventos para pontos finais adicionais. Para obter uma lista completa de pontos finais suportados, consulte a tabela na secção anterior. 

   A integração do IoT Hub com a Event Grid suporta 500 pontos finais por IoT Hub e uma maior variedade de tipos de pontos finais. Integra-se de forma nativa com as filas azure Functions, Logic Apps, Storage and Service Bus, e também trabalha com webhooks para alargar o envio de dados para fora do ecossistema de serviços Azure e em aplicações empresariais de terceiros.

* **Importa se os seus dados chegam em ordem?**

   O encaminhamento de mensagens IoT Hub mantém a ordem na qual as mensagens são enviadas, para que cheguem da mesma forma.

   A Event Grid não garante que os pontos finais recebam eventos na mesma ordem que ocorreram. Para os casos em que a ordem absoluta das mensagens é significativa e/ou em que um consumidor precisa de um identificador único de confiança para mensagens, recomendamos a utilização de encaminhamento de mensagens. 

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre o [Encaminhamento de Mensagens IoT Hub](iot-hub-devguide-messages-d2c.md) e os [pontos finais do Hub IoT](iot-hub-devguide-endpoints.md).
* Saiba mais sobre o [Azure Event Grid](../event-grid/overview.md).
* Para aprender a criar rotas de mensagens, consulte as mensagens de [dispositivo-para-nuvem do Process IoT Hub utilizando o](../iot-hub/tutorial-routing.md) tutorial de rotas.
* Experimente a integração da Rede de [Eventos enviando notificações de e-mail sobre eventos do Hub Azure IoT usando aplicações lógicas.](../event-grid/publish-iot-hub-events-to-logic-apps.md)
