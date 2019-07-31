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
ms.openlocfilehash: b1bbc11d7772e4f56d7dc6ead580b0a0cbd3cd8d
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68669783"
---
O Functions facilita a adição de Application Insights integração a um aplicativo de funções do [portal do Azure].

1. No portal do [portal][portal do azure], selecione **todos os serviços > aplicativos de função**, selecione seu aplicativo de funções e, em seguida, selecione a faixa de **Application insights** na parte superior da janela

    ![Habilitar Application Insights no portal](media/functions-connect-new-app-insights/enable-application-insights.png)

1. Crie um recurso de Application Insights usando as configurações especificadas na tabela abaixo da imagem.

   ![Criar um recurso do Application Insights](media/functions-connect-new-app-insights/ai-general.png)

    | Definição      | Valor sugerido  | Descrição                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Name** | Nome de aplicativo exclusivo | É mais fácil usar o mesmo nome que seu aplicativo de funções, que deve ser exclusivo em sua assinatura. | 
    | **Location** | Europa Ocidental | Se possível, use a mesma [região](https://azure.microsoft.com/regions/) que o seu aplicativo de funções, ou um que esteja próximo dessa região. |

1. Selecione **OK**. O recurso Application Insights é criado no mesmo grupo de recursos e assinatura que o seu aplicativo de funções. Depois que o recurso for criado, feche a janela Application Insights.

1. De volta ao seu aplicativo de funções, selecione **configurações do aplicativo**e role para baixo até **configurações do aplicativo**. Se você vir uma configuração chamada `APPINSIGHTS_INSTRUMENTATIONKEY`, Application insights integração será habilitada para seu aplicativo de funções em execução no Azure.

[Portal do Azure]: https://portal.azure.com
