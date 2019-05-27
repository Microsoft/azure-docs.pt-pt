---
title: incluir ficheiro
description: incluir ficheiro
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/05/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 400f12237ae8b8cbaf6d66bda1663ecb680136f3
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66162584"
---
[Roteamento de mensagens](../articles/iot-hub/iot-hub-devguide-messages-d2c.md) permite enviar dados de telemetria dos seus dispositivos IoT para pontos finais compatíveis com o Event Hub incorporados ou pontos finais personalizados como armazenamento de BLOBs, filas do Service Bus, tópicos do Service Bus e os Hubs de eventos. Para configurar o encaminhamento de mensagem personalizada, crie [consultas encaminhamento](../articles/iot-hub/iot-hub-devguide-routing-query-syntax.md) para personalizar a rota que corresponde a uma determinada condição. Uma vez configurado, os dados de entrada são automaticamente encaminhados para os pontos finais pelo Hub IoT. Se uma mensagem não corresponde a nenhum das consultas de encaminhamento definidas, é encaminhado para o ponto final predefinido.

Este tutorial parte 2, saiba como configurar e utilizar estas consultas de encaminhamento personalizadas com o IoT Hub. Encaminhar mensagens de um dispositivo IoT para um dos vários pontos de extremidade, incluindo o armazenamento de BLOBs e uma fila do Service Bus. Mensagens na fila do Service Bus são captadas por uma aplicação lógica e enviadas por email. As mensagens que não tenham definido de encaminhamento de mensagens personalizadas são enviadas para o ponto final predefinido, em seguida, pegou pelo Azure Stream Analytics e exibidas numa visualização do Power BI.

 A conclusão partes 1 e 2 do tutorial, execute as seguintes tarefas:

**Parte i: Criar recursos, configurar o encaminhamento de mensagens**
> [!div class="checklist"]
> * Crie os recursos de um hub IoT, uma conta de armazenamento, uma fila do Service Bus e um dispositivo simulado. Isso pode ser feito com o portal, a CLI do Azure, Azure PowerShell ou um modelo Azure Resource Manager.
> * Configure os pontos de extremidade e rotas de mensagens no IoT Hub para a conta de armazenamento e a fila do Service Bus.

**Parte II: Enviar mensagens para o hub, ver resultados roteados**
> [!div class="checklist"]
> * Criar uma Aplicação Lógica que é acionada para enviar e-mails quando é adicionada uma mensagem à fila do Service Bus.
> * Transferir e executar uma aplicação que simula um Dispositivo IoT a enviar mensagens para o hub para as diferentes opções de encaminhamento.
> * Criar uma visualização do Power BI de dados enviados para o ponto final predefinido.
> * Visualizar os resultados...
> * …na fila do Service Bus e nos e-mails.
> * …na conta de armazenamento.
> * ...na visualização do Power BI.

## <a name="prerequisites"></a>Pré-requisitos

* Para a parte 1 deste Tutorial:
  - Precisa de uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

* Para a parte 2 deste Tutorial:
  - Tem de ter concluído parte 1 deste tutorial e tem os recursos continuam disponíveis.
  - Instale o [Visual Studio](https://www.visualstudio.com/).
  - Uma conta do Power BI para examinar a análise de transmissão do ponto final predefinido. ([Experimente o Power BI gratuitamente](https://app.powerbi.com/signupredirect?pbi_source=web).)
  - Uma conta do Office 365 para enviar e-mails de notificação.

[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]
