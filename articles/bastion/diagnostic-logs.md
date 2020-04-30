---
title: Ativar e trabalhar com registos de recursos da Azure Bastion
description: Neste artigo, aprenda a ativar e trabalhar com registos de diagnóstico da Azure Bastion.
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: charwen
ms.openlocfilehash: 4b71dba02a311b8fa6a16645364f90d7632693a2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82131280"
---
# <a name="enable-and-work-with-bastion-resource-logs"></a>Ativar e trabalhar com registos de recursos da Bastion

À medida que os utilizadores se ligam às cargas de trabalho utilizando o Azure Bastion, a Bastion pode registar diagnósticos das sessões remotas. Em seguida, pode utilizar os diagnósticos para visualizar quais os utilizadores ligados a que cargas de trabalho, a que horas, de onde e outras informações relevantes sobre o registo. Para utilizar os diagnósticos, deve ativar os registos de diagnóstico no Azure Bastion. Este artigo ajuda-o a ativar os registos de diagnóstico e, em seguida, a visualizar os registos.

## <a name="enable-the-resource-log"></a><a name="enable"></a>Ativar o registo de recursos

1. No [portal Azure,](https://portal.azure.com)navegue para o seu recurso Azure Bastion e selecione **definições** de Diagnóstico a partir da página Do Bastião Azure.

   ![definições de diagnóstico](./media/diagnostic-logs/1diagnostics-settings.png)
2. Selecione **definições de Diagnóstico**e, em seguida, selecione **+Adicione a definição de diagnóstico** para adicionar um destino para os registos.

   ![adicionar definição de diagnóstico](./media/diagnostic-logs/2add-diagnostic-setting.png)
3. Na página de definições de **Diagnóstico,** selecione o tipo de conta de armazenamento a utilizar para armazenar registos de diagnóstico.

   ![selecionar local de armazenamento](./media/diagnostic-logs/3add-storage-account.png)
4. Quando completar as definições, será semelhante a este exemplo:

   ![definições de exemplo](./media/diagnostic-logs/4example-settings.png)

## <a name="view-diagnostics-log"></a><a name="view"></a>Ver registo de diagnósticos

Para aceder aos registos de diagnóstico, pode utilizar diretamente a conta de armazenamento que especificou ao mesmo tempo que permite as definições de diagnóstico.

1. Navegue para o recurso da sua conta de armazenamento e, em seguida, para **contentores.** Você vê a bolha de **insights-logs-bastionauditlogs** criado no seu recipiente de blob de conta de armazenamento.

   ![definições de diagnóstico](./media/diagnostic-logs/1-navigate-to-logs.png)
2. Ao navegar para dentro do contentor, vê várias pastas no seu blog. Estas pastas indicam a hierarquia de recursos para o seu recurso Azure Bastion.

   ![adicionar definição de diagnóstico](./media/diagnostic-logs/2-resource-h.png)
3. Navegue até à hierarquia completa do seu recurso Azure Bastion cujos registos de diagnóstico deseja aceder/visualizar. O 'y=', 'm=', 'd=', 'h=' e 'm=' indicam o ano, mês, dia, hora e minuto, respectivamente, para os registos de recursos.

   ![selecionar local de armazenamento](./media/diagnostic-logs/3-resource-location.png)
4. Localize o ficheiro json criado pela Azure Bastion que contém os dados de registo de diagnóstico sintetizadores para o período de tempo navegado.

5. Faça o download do ficheiro json do seu recipiente de armazenamento blob. Uma entrada de exemplo do ficheiro json é mostrada abaixo para referência:

   ```json
   { 
   "time":"2019-10-03T16:03:34.776Z",
   "resourceId":"/SUBSCRIPTIONS/<subscripionID>/RESOURCEGROUPS/MYBASTION/PROVIDERS/MICROSOFT.NETWORK/BASTIONHOSTS/MYBASTION-BASTION",
   "operationName":"Microsoft.Network/BastionHost/connect",
   "category":"BastionAuditLogs",
   "level":"Informational",
   "location":"eastus",
   "properties":{ 
      "userName":"<username>",
      "userAgent":"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/77.0.3865.90 Safari/537.36",
      "clientIpAddress":"131.107.159.86",
      "clientPort":24039,
      "protocol":"ssh",
      "targetResourceId":"/SUBSCRIPTIONS/<subscripionID>/RESOURCEGROUPS/MYBASTION/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/LINUX-KEY",
      "subscriptionId":"<subscripionID>",
      "message":"Successfully Connected.",
      "resourceType":"VM",
      "targetVMIPAddress":"172.16.1.5",
      "tunnelId":"<tunnelID>"
   },
   "FluentdIngestTimestamp":"2019-10-03T16:03:34.0000000Z",
   "Region":"eastus",
   "CustomerSubscriptionId":"<subscripionID>"
   }
   ```

## <a name="next-steps"></a>Passos seguintes

Leia o [Bastião FAQ.](bastion-faq.md)
