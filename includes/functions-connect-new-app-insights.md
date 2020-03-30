---
title: incluir ficheiro
description: incluir ficheiro
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/06/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 266cd52117f36b282fdd4bc8615a15e451cc203f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132616"
---
As funções facilitam a adição de Informações de Aplicação a uma aplicação de função do [portal Azure].

1. No [portal][Portal Azure], digite `Function Apps` na barra de pesquisa na parte superior da página, escolha a sua aplicação de função e, em seguida, selecione o Banner Application Insights não é **configurado** banner na parte superior da janela. Se não vir este banner, então a sua aplicação já tem Informações de Aplicação ativadas.

    ![Ativar insights de aplicação a partir do portal](media/functions-connect-new-app-insights/enable-application-insights.png)

1. Crie um recurso Deinsights de Aplicação utilizando as definições especificadas na tabela abaixo da imagem.

   ![Criar um recurso do Application Insights](media/functions-connect-new-app-insights/ai-general.png)

    | Definição      | Valor sugerido  | Descrição                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome** | Nome exclusivo da aplicação | É mais fácil usar o mesmo nome que a sua aplicação de funções, que deve ser única na sua subscrição. | 
    | **Localização** | Europa ocidental | Se possível, utilize a mesma [região](https://azure.microsoft.com/regions/) que a sua app de funções, ou uma que esteja próxima daquela região. |

1. Selecione **OK**. O recurso Application Insights é criado no mesmo grupo de recursos e subscrição que a sua aplicação de funções. Depois de criado o recurso, feche a janela Application Insights.

1. Volte para a aplicação de funções, selecione **as definições**de Aplicação e, em seguida, desloque-se para **as definições**da Aplicação . Se vir uma `APPINSIGHTS_INSTRUMENTATIONKEY`definição chamada , a integração de Insights de Aplicação está ativada para a sua aplicação de função em funcionamento no Azure.

[Portal do Azure]: https://portal.azure.com
