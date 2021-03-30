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
ms.openlocfilehash: 3a5747912a18b8406cabd03c9823e3f6fe6898a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "72808876"
---
<!-- description of message routing used in the Azure CLI, PowerShell, and RM routing articles.-->

Vai encaminhar mensagens para diferentes recursos com base nas propriedades anexadas à mensagem através do dispositivo simulado. As mensagens sem encaminhamento personalizado são enviadas para o ponto final predefinido (mensagens/eventos). No tutorial seguinte, envia mensagens para o IoT Hub e vê-las encaminhadas para os diferentes destinos.

|Valor |Resultado|
|------|------|
|level="storage" |Escrever no Armazenamento do Azure.|
|level="critical" |Escrever numa fila do Service Bus. Uma Aplicação Lógica obtém a mensagem da fila e utiliza o Office 365 para enviar o e-mail.|
|predefinição |Apresentar estes dados com o Power BI.|

O primeiro passo é estabelecer o ponto final para o qual os dados serão encaminhados. O segundo passo é configurar a rota de mensagem que utiliza esse ponto final. Depois de configurar o encaminhamento, pode visualizar os pontos finais e as rotas de mensagens no portal.