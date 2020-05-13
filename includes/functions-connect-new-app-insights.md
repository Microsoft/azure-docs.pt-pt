---
title: incluir ficheiro
description: incluir ficheiro
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/09/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 3289ba03d0f613d004bc8bff4dbcf2bd434f3da3
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83121577"
---
As funções facilitam a adição de Informações de Aplicação a uma aplicação de função do [portal Azure].

1. No [portal Azure][Portal,]procure e selecione app de **funções,** e depois escolha a sua aplicação de função. 

1. Selecione o Banner **Application Insights não está configurado** na parte superior da janela. Se não vir este banner, então a sua aplicação já tem Informações de Aplicação ativadas.

    :::image type="content" source="media/functions-connect-new-app-insights/enable-application-insights.png" alt-text="Ativar insights de aplicação a partir do portal":::

1. Crie um recurso Deinsights de Aplicação utilizando as definições especificadas na tabela abaixo da imagem.

   :::image type="content" source="media/functions-connect-new-app-insights/ai-general.png" alt-text="Criar um recurso Deinsights de Aplicação":::

    | Definição      | Valor sugerido  | Descrição                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome** | Nome exclusivo da aplicação | É mais fácil usar o mesmo nome que a sua aplicação de funções, que deve ser única na sua subscrição. | 
    | **Localização** | Europa ocidental | Se possível, utilize a mesma [região](https://azure.microsoft.com/regions/) que a sua app de funções, ou uma que esteja próxima daquela região. |

1. Selecione **Aplicar**. O recurso Application Insights é criado no mesmo grupo de recursos e subscrição que a sua aplicação de funções. Depois de criado o recurso, feche a janela Application Insights.

1. Volte à aplicação de funções, selecione **Configuração**de  >  **Configurações**, e, em seguida, selecione **as definições**de aplicação . Se vir uma definição chamada , a integração de Insights de `APPINSIGHTS_INSTRUMENTATIONKEY` Aplicação está ativada para a sua aplicação de função em funcionamento no Azure.

[Azure Portal]: https://portal.azure.com
