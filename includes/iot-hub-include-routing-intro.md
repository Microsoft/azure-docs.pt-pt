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
ms.openlocfilehash: 863989f8e2cb90fe5ec0921ea6e080b61fc1b4ae
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72808823"
---
O [Roteamento de mensagens](../articles/iot-hub/iot-hub-devguide-messages-d2c.md) permite enviar dados de telemetria de seus dispositivos IOT para pontos de extremidade compatíveis com o Hub de eventos internos ou pontos de extremidade personalizados, como armazenamento de BLOBs, filas do barramento de serviço, tópicos do barramento de serviço e hubs de eventos. Para configurar o roteamento de mensagens personalizado, você cria [consultas de roteamento](../articles/iot-hub/iot-hub-devguide-routing-query-syntax.md) para personalizar a rota que corresponde a uma determinada condição. Uma vez configurado, os dados de entrada são automaticamente encaminhados para os pontos finais pelo Hub IoT. Se uma mensagem não corresponder a nenhuma das consultas de roteamento definidas, ela será roteada para o ponto de extremidade padrão.

Neste tutorial de duas partes, você aprende a configurar e usar essas consultas de roteamento personalizadas com o Hub IoT. Você roteia mensagens de um dispositivo IoT para um de vários pontos de extremidade, incluindo o armazenamento de BLOBs e uma fila do barramento de serviço. As mensagens para a fila do barramento de serviço são coletadas por um aplicativo lógico e enviadas por email. As mensagens que não têm o roteamento de mensagens personalizado definido são enviadas para o ponto de extremidade padrão e, em seguida, obtidas por Azure Stream Analytics e exibidas em uma visualização de Power BI.

Para concluir as partes 1 e 2 deste tutorial, execute as seguintes tarefas:

**Parte I: criar recursos, configurar o roteamento de mensagens**
> [!div class="checklist"]
> * Crie os recursos – um hub IoT, uma conta de armazenamento, uma fila do barramento de serviço e um dispositivo simulado. Isso pode ser feito usando o portal do Azure, um modelo de Azure Resource Manager, o CLI do Azure ou Azure PowerShell.
> * Configure os pontos de extremidade e as rotas de mensagem no Hub IoT para a conta de armazenamento e a fila do barramento de serviço.

**Parte II: enviar mensagens para o Hub, exibir resultados roteados**
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
  - Você deve ter concluído a parte 1 deste tutorial e ter os recursos ainda disponíveis.
  - Instale o [Visual Studio](https://www.visualstudio.com/).
  - Ter acesso a uma conta de Power BI para analisar o Stream Analytics do ponto de extremidade padrão. ([Experimente o Power BI gratuitamente](https://app.powerbi.com/signupredirect?pbi_source=web).)
  - Ter uma conta do Office 365 para enviar emails de notificação.

[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]
