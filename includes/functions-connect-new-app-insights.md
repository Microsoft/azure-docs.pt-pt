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
As funções facilitam a adição da integração do Application Insights numa aplicação de funções a partir do [portal do Azure].

1. No [portal do Azure][Portal do Azure], procure e selecione **aplicação de funções** e, em seguida, escolha a sua aplicação de funções. 

1. Selecione a faixa **O Application Insights não está configurado** na parte superior da janela. Se não vir esta faixa, a sua aplicação já tem o Application Insights ativado.

    :::image type="content" source="media/functions-connect-new-app-insights/enable-application-insights.png" alt-text="Ativar a o Application Insights a partir do portal":::

1. Crie um recurso do Application Insights com as definições especificadas na tabela abaixo da imagem.

   :::image type="content" source="media/functions-connect-new-app-insights/ai-general.png" alt-text="Criar um recurso do Application Insights":::

    | Definição      | Valor sugerido  | Descrição                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome** | Nome de aplicação exclusivo | É mais fácil utilizar o mesmo nome da aplicação de funções, que tem de ser exclusivo na subscrição. | 
    | **Localização** | Europa Ocidental | Se possível, utilize a mesma [região](https://azure.microsoft.com/regions/) da aplicação de funções, ou uma que esteja perto dessa região. |

1. Selecione **Aplicar**. O recurso do Application Insights é criado no mesmo grupo de recursos e subscrição da aplicação de funções. Após a criação do recurso, feche a janela do Application Insights.

1. De volta à aplicação de funções, selecione **Definições** > **Configuração** e, em seguida, selecione **Definições da aplicação**. Se vir uma definição chamada `APPINSIGHTS_INSTRUMENTATIONKEY`, a integração do Application Insights está ativada para a aplicação de funções em execução no Azure.

[Portal do Azure]: https://portal.azure.com
