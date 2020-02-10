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
ms.openlocfilehash: 9a20dca71727e83db98c4c97567949bd127fc7fb
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/09/2020
ms.locfileid: "77111207"
---
[O encaminhamento de mensagens](../articles/iot-hub/iot-hub-devguide-messages-d2c.md) permite o envio de dados de telemetria dos seus dispositivos IoT para pontos finais compatíveis com o Event Hub ou pontos finais personalizados, tais como armazenamento de blob, filas de ônibus de serviço, tópicos de ônibus de serviço e centros de eventos. Para configurar o encaminhamento de mensagens personalizadas, cria consultas de [encaminhamento](../articles/iot-hub/iot-hub-devguide-routing-query-syntax.md) para personalizar o percurso que corresponde a uma determinada condição. Uma vez configurado, os dados de entrada são automaticamente encaminhados para os pontos finais pelo Hub IoT. Se uma mensagem não corresponder a nenhuma das consultas de encaminhamento definidas, é encaminhada para o ponto final padrão.

Neste tutorial de duas partes, você aprende a configurar e usar estas consultas de encaminhamento personalizado com IoT Hub. Você encaminha mensagens de um dispositivo IoT para um de vários pontos finais, incluindo armazenamento de blob e uma fila de ônibus de serviço. As mensagens para a fila do Ônibus de serviço são captadas por uma App Lógica e enviadas por e-mail. As mensagens que não possuem o encaminhamento de mensagens personalizadas definidos são enviadas para o ponto final padrão, depois captadas pelo Azure Stream Analytics e visualizadas numa visualização do Power BI.

Para completar as Partes 1 e 2 deste tutorial, executa as seguintes tarefas:

**Parte I: Criar recursos, configurar o encaminhamento de mensagens**
> [!div class="checklist"]
> * Criar os recursos, um hub IoT, uma conta de armazenamento, uma fila de ônibus de serviço, e um dispositivo simulado. Isto pode ser feito usando o portal Azure, um modelo de Gestor de Recursos Azure, o Azure CLI ou Azure PowerShell.
> * Configure os pontos finais e as rotas de mensagem no IoT Hub para a conta de armazenamento e fila de ônibus de serviço.

**Parte II: Enviar mensagens para o centro, ver resultados direcionados**
> [!div class="checklist"]
> * Criar uma Aplicação Lógica que é acionada para enviar e-mails quando é adicionada uma mensagem à fila do Service Bus.
> * Transferir e executar uma aplicação que simula um Dispositivo IoT a enviar mensagens para o hub para as diferentes opções de encaminhamento.
> * Criar uma visualização do Power BI de dados enviados para o ponto final predefinido.
> * Visualizar os resultados...
> * …na fila do Service Bus e nos e-mails.
> * …na conta de armazenamento.
> * ...na visualização do Power BI.

## <a name="prerequisites"></a>Pré-requisitos

* Para a parte 1 deste tutorial:
  - Precisa de uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

* Para a parte 2 deste tutorial:
  - Deve ter completado a Parte 1 deste tutorial e ter os recursos ainda disponíveis.
  - Instalar o [Visual Studio](https://www.visualstudio.com/).
  - Tenha acesso a uma conta Power BI para analisar a análise de fluxo do ponto final padrão. ([Experimente o Power BI gratuitamente](https://app.powerbi.com/signupredirect?pbi_source=web).)
  - Tenha uma conta do Office 365 para o envio de e-mails de notificação.
  - Certifique-se de que a porta 8883 está aberta na sua firewall. A amostra deste tutorial utiliza o protocolo MQTT, que comunica sobre a porta 8883. Este porto pode estar bloqueado em alguns ambientes de rede corporativa e educativa. Para obter mais informações e formas de resolver este problema, consulte [A Ligação ao IoT Hub (MQTT)](../articles/iot-hub/iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]
