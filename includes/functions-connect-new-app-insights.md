---
title: incluir ficheiro
description: incluir ficheiro
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/10/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 87af50c5b5e5b69fd175ac4a570c4b6f659b97e6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84731045"
---
As Funções Azure facilitam a adição da integração de Application Insights a uma aplicação de função a partir do [portal Azure.]

1. No [portal do Azure][Portal do Azure], procure e selecione **aplicação de funções** e, em seguida, escolha a sua aplicação de funções. 

1. Selecione a faixa **O Application Insights não está configurado** na parte superior da janela. Se não vir este banner, então a sua aplicação pode já ter o Application Insights ativado.

    :::image type="content" source="media/functions-connect-new-app-insights/enable-application-insights.png" alt-text="Ativar a o Application Insights a partir do portal":::

1. Expanda **o seu recurso** e crie um recurso Application Insights utilizando as definições especificadas na tabela seguinte.  

    | Definição      | Valor sugerido  | Descrição                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Novo nome do recurso** | Nome de aplicação exclusivo | É mais fácil utilizar o mesmo nome da aplicação de funções, que tem de ser exclusivo na subscrição. | 
    | **Localização** | Europa Ocidental | Se possível, utilize a mesma [região](https://azure.microsoft.com/regions/) da aplicação de funções, ou uma que esteja perto dessa região. |

    :::image type="content" source="media/functions-connect-new-app-insights/ai-general.png" alt-text="Ativar a o Application Insights a partir do portal":::

1. Selecione **Aplicar**. 

   O recurso do Application Insights é criado no mesmo grupo de recursos e subscrição da aplicação de funções. Após a criação do recurso, feche a janela do Application Insights.

1. Na sua aplicação de função, selecione **Configuração** em **Definições**e, em seguida, selecione **definições de Aplicação**. Se vir uma definição chamada `APPINSIGHTS_INSTRUMENTATIONKEY`, a integração do Application Insights está ativada para a aplicação de funções em execução no Azure.

[Portal do Azure]: https://portal.azure.com
