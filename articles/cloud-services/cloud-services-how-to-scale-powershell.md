---
title: Dimensionar um serviço de nuvem do Azure no Windows PowerShell | Microsoft Docs
description: clássico Saiba como usar o PowerShell para dimensionar uma função Web ou função de trabalho para dentro ou para fora no Azure.
services: cloud-services
author: mmccrory
ms.service: cloud-services
ms.topic: article
ms.date: 12/01/2016
ms.author: memccror
ms.openlocfilehash: a1945aad12eb34bad1b593878779e1ceb0dae686
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359034"
---
# <a name="how-to-scale-a-cloud-service-in-powershell"></a>Como dimensionar um serviço de nuvem no PowerShell

Você pode usar o Windows PowerShell para dimensionar uma função Web ou função de trabalho para dentro ou para fora adicionando ou removendo instâncias.  

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Antes de executar qualquer operação em sua assinatura por meio do PowerShell, você deve fazer logon:

```powershell
Add-AzureAccount
```

Se você tiver várias assinaturas associadas à sua conta, talvez seja necessário alterar a assinatura atual dependendo de onde o serviço de nuvem reside. Para verificar a assinatura atual, execute:

```powershell
Get-AzureSubscription -Current
```

Se você precisar alterar a assinatura atual, execute:

```powershell
Set-AzureSubscription -SubscriptionId <subscription_id>
```

## <a name="check-the-current-instance-count-for-your-role"></a>Verificar a contagem de instâncias atual para sua função

Para verificar o estado atual da sua função, execute:

```powershell
Get-AzureRole -ServiceName '<your_service_name>' -RoleName '<your_role_name>'
```

Você deve obter informações sobre a função, incluindo a versão atual do sistema operacional e a contagem de instâncias. Nesse caso, a função tem uma única instância.

![Informações sobre a função](./media/cloud-services-how-to-scale-powershell/get-azure-role.png)

## <a name="scale-out-the-role-by-adding-more-instances"></a>Escalar horizontalmente a função adicionando mais instâncias

Para escalar horizontalmente sua função, passe o número desejado de instâncias como o parâmetro de **contagem** para o cmdlet **set-AzureRole** :

```powershell
Set-AzureRole -ServiceName '<your_service_name>' -RoleName '<your_role_name>' -Slot <target_slot> -Count <desired_instances>
```

O cmdlet é bloqueado momentaneamente enquanto as novas instâncias são provisionadas e iniciadas. Durante esse tempo, se você abrir uma nova janela do PowerShell e chamar **Get-AzureRole** conforme mostrado anteriormente, verá a nova contagem de instâncias de destino. E se você inspecionar o status da função no portal, verá a nova instância iniciando:

![Instância de VM iniciada no portal](./media/cloud-services-how-to-scale-powershell/role-instance-starting.png)

Depois que as novas instâncias forem iniciadas, o cmdlet retornará com êxito:

![A instância de função aumenta o êxito](./media/cloud-services-how-to-scale-powershell/set-azure-role-success.png)

## <a name="scale-in-the-role-by-removing-instances"></a>Dimensionar na função removendo instâncias

Você pode dimensionar em uma função removendo instâncias da mesma maneira. Defina o parâmetro de **contagem** em **set-AzureRole** como o número de instâncias que você deseja ter após a conclusão da operação de dimensionamento.

## <a name="next-steps"></a>Passos Seguintes

Não é possível configurar o dimensionamento automático para serviços de nuvem do PowerShell. Para fazer isso, consulte [como dimensionar automaticamente um serviço de nuvem](cloud-services-how-to-scale-portal.md).
