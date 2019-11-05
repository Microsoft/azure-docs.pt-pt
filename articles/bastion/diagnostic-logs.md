---
title: Habilitar e trabalhar com os logs de diagnóstico de bastiões do Azure | Microsoft Docs
description: Neste artigo, saiba como habilitar e trabalhar com os logs de diagnóstico de bastiões do Azure.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: cherylmc
ms.openlocfilehash: 8ae421b48f3cfa8055f636052c990f99e0c775b4
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512408"
---
# <a name="enable-and-work-with-bastion-diagnostic-logs"></a>Habilitar e trabalhar com logs de diagnóstico de bastiões

À medida que os usuários se conectam a cargas de trabalho usando a bastiões do Azure, a bastiões pode registrar em log o diagnóstico das sessões remotas. Você pode usar o diagnóstico para exibir quais usuários se conectaram a quais cargas de trabalho, em que horário, de onde e outras informações de registro em log relevantes. Para usar o diagnóstico, você deve habilitar os logs de diagnóstico na bastiões do Azure. Este artigo ajuda você a habilitar os logs de diagnóstico e a exibir os logs.

## <a name="enable"></a>Habilitar o log de diagnóstico

1. Na [portal do Azure](https://portal.azure.com), navegue até o recurso de bastiões do Azure e selecione **configurações de diagnóstico** na página de bastiões do Azure.

   ![configurações de diagnóstico](./media/diagnostic-logs/1diagnostics-settings.png)
2. Selecione **configurações de diagnóstico**e, em seguida, selecione **+ Adicionar configuração de diagnóstico** para adicionar um destino para os logs.

   ![Adicionar configuração de diagnóstico](./media/diagnostic-logs/2add-diagnostic-setting.png)
3. Na página **configurações de diagnóstico** , selecione o tipo de conta de armazenamento a ser usado para armazenar os logs de diagnóstico.

   ![selecionar local de armazenamento](./media/diagnostic-logs/3add-storage-account.png)
4. Quando você concluir as configurações, a aparência será semelhante a este exemplo:

   ![configurações de exemplo](./media/diagnostic-logs/4example-settings.png)

## <a name="view"></a>Exibir log de diagnóstico

Para acessar seus logs de diagnóstico, você pode usar diretamente a conta de armazenamento que você especificou ao habilitar as configurações de diagnóstico.

1. Navegue até o recurso de conta de armazenamento e, em seguida, para **contêineres**. Você verá o blob **insights-logs-bastionauditlogs** criado no seu contêiner de BLOB da conta de armazenamento.

   ![configurações de diagnóstico](./media/diagnostic-logs/1-navigate-to-logs.png)
2. Ao navegar para dentro do contêiner, você verá várias pastas em seu blog. Essas pastas indicam a hierarquia de recursos para o recurso de bastiões do Azure.

   ![Adicionar configuração de diagnóstico](./media/diagnostic-logs/2-resource-h.png)
3. Navegue até a hierarquia completa do seu recurso de bastiões do Azure cujos logs de diagnóstico você deseja acessar/exibir. O ' y = ', ' M' = ', ' d' = ', ' h = ' e ' M' = ' indicam o ano, mês, dia, hora e minuto, respectivamente, para os logs de diagnóstico.

   ![selecionar local de armazenamento](./media/diagnostic-logs/3-resource-location.png)
4. Localize o arquivo JSON criado pela bastiões do Azure que contém os dados de log de diagnóstico para o período de tempo de navegação.

5. Baixe o arquivo JSON do seu contêiner de blob de armazenamento. Uma entrada de exemplo do arquivo JSON é mostrada abaixo para referência:

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

Leia as [perguntas frequentes sobre bastiões](bastion-faq.md).
