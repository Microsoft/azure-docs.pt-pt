---
title: Dimensionar um serviço cloud do Azure no Windows PowerShell | Documentos da Microsoft
description: (clássico) Saiba como utilizar o PowerShell para dimensionar uma função da web ou a função de trabalho e reduzir no Azure.
services: cloud-services
documentationcenter: ''
author: mmccrory
manager: timlt
editor: ''
ms.assetid: ee37dd8c-6714-4c61-adb8-03d6bbf76c9a
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2016
ms.author: mmccrory
ms.openlocfilehash: 240b34abd2f9b937c8a7ea449ad7f148870a9d33
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61433263"
---
# <a name="how-to-scale-a-cloud-service-in-powershell"></a>Como dimensionar um serviço em nuvem no PowerShell

Pode utilizar o Windows PowerShell para dimensionar uma função da web ou a função de trabalho e reduzir adicionando ou removendo instâncias.  

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Antes de poder executar quaisquer operações na sua subscrição através do PowerShell, tem de iniciar sessão:

```powershell
Add-AzureAccount
```

Se tiver várias subscrições associadas à sua conta, terá de alterar a subscrição atual, dependendo de onde reside o seu serviço cloud. Para verificar a subscrição atual, execute:

```powershell
Get-AzureSubscription -Current
```

Se precisar de alterar a subscrição atual, execute:

```powershell
Set-AzureSubscription -SubscriptionId <subscription_id>
```

## <a name="check-the-current-instance-count-for-your-role"></a>Verifique a contagem de instâncias atual para a sua função

Para verificar o estado atual da sua função, execute:

```powershell
Get-AzureRole -ServiceName '<your_service_name>' -RoleName '<your_role_name>'
```

Deve obter informações sobre a função, incluindo sua contagem de versão e instância de SO atual. Neste caso, a função tem uma única instância.

![Informações sobre a função](./media/cloud-services-how-to-scale-powershell/get-azure-role.png)

## <a name="scale-out-the-role-by-adding-more-instances"></a>Ampliar a função ao adicionar mais instâncias

Para ampliar a sua função, passar o número pretendido de instâncias como o **contagem** parâmetro para o **conjunto AzureRole** cmdlet:

```powershell
Set-AzureRole -ServiceName '<your_service_name>' -RoleName '<your_role_name>' -Slot <target_slot> -Count <desired_instances>
```

Os blocos de cmdlet em instantes enquanto as novas instâncias são aprovisionados e iniciados. Durante este período, se abrir uma nova janela do PowerShell e chamada **Get-AzureRole** conforme mostrado anteriormente, verá a nova contagem de instância de destino. E se inspecionar o estado de função no portal, deve ver a nova instância a ser iniciado:

![Instância de VM a partir do portal](./media/cloud-services-how-to-scale-powershell/role-instance-starting.png)

Assim que as novas instâncias de ter iniciado, o cmdlet irá devolver com êxito:

![Sucesso de aumento de instância de função](./media/cloud-services-how-to-scale-powershell/set-azure-role-success.png)

## <a name="scale-in-the-role-by-removing-instances"></a>Reduzir horizontalmente a função removendo instâncias

Pode dimensionar numa função, removendo instâncias da mesma forma. Definir o **contagem** parâmetro na **conjunto AzureRole** para o número de instâncias que pretende ter depois de concluída a operação de dimensionamento.

## <a name="next-steps"></a>Passos Seguintes

Não é possível configurar o dimensionamento automático para os serviços de cloud a partir do PowerShell. Para tal, consulte [como um serviço em nuvem do dimensionamento automático](cloud-services-how-to-scale-portal.md).
