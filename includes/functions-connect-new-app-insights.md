---
title: incluir ficheiro
description: incluir ficheiro
services: functions
author: ggailey777
ms.service: functions
ms.topic: include
ms.date: 04/06/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: b6cafcfe6c892cd43f056458fe3586da834c2fd1
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66131441"
---
Funções torna simples para adicionar a integração do Application Insights para uma aplicação de funções a partir da [portal do Azure].

1. Na [portal][Portal do Azure], selecione **todos os serviços > aplicações Function App**, selecione a sua aplicação de função e, em seguida, escolha o **doApplicationInsights** faixa na parte superior da janela

    ![Ativar o Application Insights a partir do portal](media/functions-connect-new-app-insights/enable-application-insights.png)

1. Crie um recurso do Application Insights com as definições especificadas na tabela abaixo da imagem:

   ![Criar um recurso do Application Insights](media/functions-connect-new-app-insights/ai-general.png)

    | Definição      | Valor sugerido  | Descrição                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome** | Nome exclusivo da aplicação | É mais fácil de utilizar o mesmo nome que a sua aplicação de função, que tem de ser exclusiva na sua subscrição. | 
    | **Localização** | Europa Ocidental | Se possível, utilize o mesmo [região](https://azure.microsoft.com/regions/) que a sua aplicação de função ou próximo-lo. |

1. Escolha **OK**. O recurso do Application Insights é criado no mesmo grupo de recursos e subscrição que a sua aplicação de função. Depois de concluída a criação, feche a janela do Application Insights.

1. Na sua aplicação function app, selecione **as configurações do aplicativo**e desloque para baixo até **configurações de aplicativo**. Quando vir uma definição denominada `APPINSIGHTS_INSTRUMENTATIONKEY`, significa que a integração do Application Insights está ativada para a sua aplicação de função em execução no Azure.

[Portal do Azure]: https://portal.azure.com
