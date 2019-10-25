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
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72808876"
---
<!-- description of message routing used in the Azure CLI, PowerShell, and RM routing articles.-->

Vai encaminhar mensagens para diferentes recursos com base nas propriedades anexadas à mensagem através do dispositivo simulado. As mensagens sem encaminhamento personalizado são enviadas para o ponto final predefinido (mensagens/eventos). No próximo tutorial, você envia mensagens para o Hub IoT e vê-las roteadas para os diferentes destinos.

|Valor |Resultado|
|------|------|
|level="storage" |Escrever no Armazenamento do Azure.|
|level="critical" |Escrever numa fila do Service Bus. Uma Aplicação Lógica obtém a mensagem da fila e utiliza o Office 365 para enviar o e-mail.|
|predefinição |Apresentar estes dados com o Power BI.|

A primeira etapa é configurar o ponto de extremidade para o qual os dados serão roteados. A segunda etapa é configurar a rota de mensagem que usa esse ponto de extremidade. Depois de configurar o roteamento, você pode exibir os pontos de extremidade e as rotas de mensagens no Portal.