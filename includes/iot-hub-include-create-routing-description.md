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
ms.openlocfilehash: b640a3cb9382ad72bb48e06c6a7074c96409e2e4
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67184880"
---
<!-- description of message routing used in the Azure CLI, PowerShell, and RM routing articles.-->

Vai encaminhar mensagens para diferentes recursos com base nas propriedades anexadas à mensagem através do dispositivo simulado. As mensagens sem encaminhamento personalizado são enviadas para o ponto final predefinido (mensagens/eventos). O tutorial seguinte, enviar mensagens para o IoT Hub e vê-los encaminhados para os diferentes destinos.

|value |Resultado|
|------|------|
|level="storage" |Escrever no Armazenamento do Azure.|
|level="critical" |Escrever numa fila do Service Bus. Uma Aplicação Lógica obtém a mensagem da fila e utiliza o Office 365 para enviar o e-mail.|
|predefinição |Apresentar estes dados com o Power BI.|

A primeira etapa é configurar o ponto final ao qual os dados serão encaminhados. A segunda etapa é configurar as rotas de mensagens que utiliza o ponto de extremidade. Depois de configurar o encaminhamento, pode ver os pontos de extremidade e rotas de mensagens no portal.