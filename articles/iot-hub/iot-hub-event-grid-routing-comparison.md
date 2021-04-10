---
title: Compare a Grelha de Eventos, o encaminhamento para ioT Hub | Microsoft Docs
description: O IoT Hub oferece o seu próprio serviço de encaminhamento de mensagens, mas também se integra com a Event Grid para a publicação de eventos. Compare as duas características.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: kgremban
ms.custom: fasttrack-edit
ms.openlocfilehash: 37b411792ea1a3e21e0f26df0c7905eb8d46310e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97897710"
---
# <a name="compare-message-routing-and-event-grid-for-iot-hub"></a>Compare o encaminhamento de mensagens e a grelha de eventos para ioT Hub

O Azure IoT Hub fornece a capacidade de transmitir dados dos seus dispositivos conectados e integrar esses dados nas suas aplicações empresariais. O IoT Hub oferece dois métodos para integrar eventos IoT em outros serviços Azure ou aplicações empresariais. Este artigo discute as duas funcionalidades que fornecem esta capacidade, para que possa escolher qual a melhor opção para o seu cenário.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

**[Encaminhamento de mensagens IoT Hub](iot-hub-devguide-messages-d2c.md)**: Esta funcionalidade IoT Hub permite aos utilizadores encaminhar mensagens dispositivo-a-nuvem para pontos finais de serviço como recipientes de armazenamento Azure, Centros de Eventos, filas de autocarros de serviço e tópicos de Service Bus. O encaminhamento também fornece uma capacidade de consulta para filtrar os dados antes de encaminhá-lo para os pontos finais. Além dos dados de telemetria do dispositivo, também pode enviar [eventos de não telemetria](iot-hub-devguide-messages-d2c.md#non-telemetry-events) que podem ser usados para desencadear ações. 

**Integração IoT Hub com a Grade de Eventos**: Azure Event Grid é um serviço de encaminhamento de eventos totalmente gerido que utiliza um modelo de subscrição de publicação. IoT Hub e Event Grid trabalham em conjunto para [integrar eventos IoT Hub em serviços Azure e não-Azure,](iot-hub-event-grid.md)em tempo quase real. O IoT Hub publica eventos de [dispositivos](iot-hub-event-grid.md#event-types) e eventos de telemetria.

## <a name="differences"></a>Diferenças

Embora tanto o encaminhamento de mensagens como a Grade de Eventos permitam a configuração do alerta, existem algumas diferenças fundamentais entre os dois. Consulte a tabela seguinte para mais detalhes:

| Funcionalidade | Encaminhamento de mensagens IoT Hub | Integração do IoT Hub com Grade de Eventos |
| ------- | --------------- | ---------- |
| **Mensagens e eventos do dispositivo** | Sim, o encaminhamento de mensagens pode ser usado para dados de telemetria, alterações gémeas do dispositivo, eventos de ciclo de vida do dispositivo e eventos de mudança de gémeos digitais. | Sim, a Grelha de Eventos pode ser utilizada para dados de telemetria e eventos de dispositivos como dispositivo criado/eliminado/conectado/desligado. Mas a grelha de eventos não pode ser usada para eventos de mudança de dois dispositivos e eventos de mudança de gémeos digitais. |
| **Ordenação** | Sim, a ordem dos acontecimentos mantém-se.  | Não, a ordem dos acontecimentos não está garantida. | 
| **Filtragem** | Filtragem rica nas propriedades da aplicação de mensagens, propriedades do sistema de mensagens, corpo de mensagens, etiquetas gémeas do dispositivo e propriedades gémeas do dispositivo. A filtragem não é aplicada a eventos digitais de mudança de gémeos. Por exemplo, consulte [a Sinaxe de Encaminhamento de Mensagens](iot-hub-devguide-routing-query-syntax.md)de Mensagem . | Filtragem com base no tipo de evento, tipo de sujeito e atributos em cada evento. Por exemplo, consulte [os eventos de filtragem de filtros em Subscrições de Grelha de Eventos.](../event-grid/event-filtering.md) Ao subscrever eventos de telemetria, pode aplicar filtros adicionais nos dados para filtrar as propriedades da mensagem, o corpo da mensagem e o twin do dispositivo no seu Hub IoT, antes de publicar na Grelha de Eventos. Veja [como filtrar eventos.](../iot-hub/iot-hub-event-grid.md#filter-events) |
| **Pontos Finais** | <ul><li>Hubs de Eventos</li> <li>Armazenamento de Blobs do Azure</li> <li>Fila de ônibus de serviço</li> <li>Tópicos do Service Bus</li></ul><br>Os SKUs do Hub IoT (S1, S2 e S3) estão limitados a 10 pontos finais personalizados. 100 rotas podem ser criadas por IoT Hub. | <ul><li>Funções do Azure</li> <li>Automatização do Azure</li> <li>Hubs de Eventos</li> <li>Logic Apps</li> <li>Blob de Armazenamento</li> <li>Tópicos Personalizados</li> <li>Armazenamento de Filas</li> <li>Power Automate</li> <li>Serviços de terceiros através do WebHooks</li></ul><br>São suportados 500 pontos finais por IoT Hub. Para obter a lista mais atualizada de pontos finais, consulte os manipuladores de [eventos Event Grid](../event-grid/overview.md#event-handlers). |
| **Custo** | Não existe uma carga separada para o encaminhamento de mensagens. Só é cobrada a entrada de telemetria no IoT Hub. Por exemplo, se tiver uma mensagem encaminhada para três pontos finais diferentes, é faturada apenas por uma mensagem. | Não há nenhuma acusação do IoT Hub. O Event Grid oferece as primeiras 100.000 operações por mês gratuitamente e, em seguida, $0,60 por milhão de operações depois. |

## <a name="similarities"></a>Semelhanças

O encaminhamento de mensagens IoT Hub e a Grade de Eventos também têm semelhanças, algumas das quais são detalhadas na tabela seguinte:

| Funcionalidade | Encaminhamento de mensagens IoT Hub | Integração do IoT Hub com Grade de Eventos |
| ------- | --------------- | ---------- |
| **Tamanho máximo da mensagem** | 256 KB, dispositivo-a-nuvem | 256 KB, dispositivo-a-nuvem |
| **Fiabilidade** | Alto: Entrega cada mensagem ao ponto final pelo menos uma vez para cada rota. Expira todas as mensagens que não são entregues dentro de uma hora. | Alto: Entrega cada mensagem ao webhook pelo menos uma vez para cada subscrição. Expira todos os eventos que não são entregues dentro de 24 horas. | 
| **Escalabilidade** | Alto: Otimizado para suportar milhões de dispositivos ligados simultaneamente enviando milhares de milhões de mensagens. | Alto: Capaz de encaminhar 10.000.000 eventos por segundo por região. |
| **Latência** | Baixo: quase em tempo real. | Baixo: quase em tempo real. |
| **Enviar para vários pontos finais** | Sim, envie uma única mensagem para vários pontos finais. | Sim, envie uma única mensagem para vários pontos finais.  
| **Segurança** | O Iot Hub fornece identidade por dispositivo e controlo de acesso revogável. Para mais informações, consulte o controlo de acesso do [IoT Hub.](iot-hub-devguide-security.md) | A Event Grid fornece validação em três pontos: subscrições de eventos, publicação de eventos e entrega de eventos webhook. Para mais informações, consulte [a segurança e a autenticação da Grade de Eventos.](../event-grid/security-authentication.md) |

## <a name="how-to-choose"></a>Como escolher

O encaminhamento de mensagens IoT Hub e a integração do IoT Hub com a Grade de Eventos realizam diferentes ações para obter resultados semelhantes. Ambos tiram informações da sua solução IoT Hub e transmitem-nas para que outros serviços possam reagir. Então, como decide qual usar? Considere as seguintes questões para ajudar a orientar a sua decisão: 

* **Que tipo de dados está a enviar para os pontos finais?**

   Utilize o encaminhamento de mensagens IoT Hub quando tiver de enviar dados de telemetria para outros serviços. O encaminhamento de mensagens também permite consultar a aplicação de mensagens e propriedades do sistema, corpo de mensagens, etiquetas gémeas do dispositivo e propriedades gémeas do dispositivo.

   A integração do IoT Hub com a Grade de Eventos funciona com eventos que ocorrem no serviço IoT Hub. Estes eventos IoT Hub incluem dados de telemetria, dispositivo criado, eliminado, conectado e desligado. Ao subscrever eventos de telemetria, pode aplicar filtros adicionais nos dados para filtrar as propriedades da mensagem, o corpo da mensagem e o twin do dispositivo no seu Hub IoT, antes de publicar na Grelha de Eventos. Veja [como filtrar eventos.](../iot-hub/iot-hub-event-grid.md#filter-events)

* **Que pontos finais precisam para receber esta informação?**

   O encaminhamento de mensagens IoT Hub suporta um número limitado de pontos finais únicos e tipos de pontos finais, mas pode construir conectores para redirecionar os dados e eventos para pontos finais adicionais. Para obter uma lista completa dos pontos finais suportados, consulte a tabela na secção anterior. 

   A integração do IoT Hub com a Grade de Eventos suporta 500 pontos finais por IoT Hub e uma maior variedade de tipos de pontos finais. Integra-se de forma nativa com as filas Azure Functions, Logic Apps, Storage and Service Bus, e também trabalha com webhooks para estender o envio de dados para fora do ecossistema de serviços Azure e em aplicações empresariais de terceiros.

* **Importa se os seus dados chegam em ordem?**

   O encaminhamento de mensagens IoT Hub mantém a ordem pela qual as mensagens são enviadas, para que cheguem da mesma forma.

   A Event Grid não garante que os pontos finais recebam eventos da mesma ordem que ocorreram. Para os casos em que a ordem absoluta das mensagens é significativa e/ou em que um consumidor necessita de um identificador único de confiança para mensagens, recomendamos a utilização do encaminhamento de mensagens. 

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [o encaminhamento de mensagens IoT Hub](iot-hub-devguide-messages-d2c.md) e os [pontos finais do IoT Hub](iot-hub-devguide-endpoints.md).
* Saiba mais sobre o [Azure Event Grid](../event-grid/overview.md).
* Para aprender a criar Rotas de Mensagens, consulte as [mensagens process IoT Hub dispositivo-nuvem usando](../iot-hub/tutorial-routing.md) o tutorial de rotas.
* Experimente a integração da Grelha de [Eventos enviando notificações de e-mail sobre eventos Azure IoT Hub usando Aplicações Lógicas.](../event-grid/publish-iot-hub-events-to-logic-apps.md)
