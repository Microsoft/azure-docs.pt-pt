---
title: Scale um serviço de nuvem Azure no Windows PowerShell / Microsoft Docs
description: (clássico) Aprenda a usar o PowerShell para escalar um papel web ou papel de trabalhador dentro ou fora em Azure.
services: cloud-services
author: mmccrory
ms.service: cloud-services
ms.topic: article
ms.date: 12/01/2016
ms.author: memccror
ms.openlocfilehash: a1945aad12eb34bad1b593878779e1ceb0dae686
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "68359034"
---
# <a name="how-to-scale-a-cloud-service-in-powershell"></a>Como escalar um serviço de nuvem no PowerShell

Pode utilizar o Windows PowerShell para escalar uma função web ou um papel de trabalhador dentro ou fora, adicionando ou removendo instâncias.  

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Antes de poder efetuar quaisquer operações na sua subscrição através do PowerShell, deve iniciar sessão:

```powershell
Add-AzureAccount
```

Se tiver várias subscrições associadas à sua conta, poderá ter de alterar a subscrição atual dependendo do local onde o seu serviço na nuvem reside. Para verificar a subscrição atual, executar:

```powershell
Get-AzureSubscription -Current
```

Se precisar de alterar a subscrição atual, execute:

```powershell
Set-AzureSubscription -SubscriptionId <subscription_id>
```

## <a name="check-the-current-instance-count-for-your-role"></a>Verifique a contagem de exemplos atuais para o seu papel

Para verificar o estado atual do seu papel, corra:

```powershell
Get-AzureRole -ServiceName '<your_service_name>' -RoleName '<your_role_name>'
```

Deve obter informações sobre o papel, incluindo a versão atual do SO e a contagem de instâncias. Neste caso, o papel tem um único exemplo.

![Informação sobre o papel](./media/cloud-services-how-to-scale-powershell/get-azure-role.png)

## <a name="scale-out-the-role-by-adding-more-instances"></a>Esforce o papel adicionando mais casos

Para escalonar o seu papel, passe o número desejado de instâncias como parâmetro **count** para o cmdlet **Set-AzureRole:**

```powershell
Set-AzureRole -ServiceName '<your_service_name>' -RoleName '<your_role_name>' -Slot <target_slot> -Count <desired_instances>
```

O cmdlet bloqueia momentaneamente enquanto as novas instâncias são aprovisionadas e iniciadas. Durante este tempo, se abrir uma nova janela PowerShell e ligar para **get-AzureRole** como mostrado anteriormente, verá a nova contagem de exemplos alvo. E se inspecionar o estado do papel no portal, deve ver a nova instância a começar:

![Caso VM a partir do portal](./media/cloud-services-how-to-scale-powershell/role-instance-starting.png)

Uma vez iniciadas as novas instâncias, o cmdlet regressará com sucesso:

![Exemplo de exemplo aumentar o sucesso](./media/cloud-services-how-to-scale-powershell/set-azure-role-success.png)

## <a name="scale-in-the-role-by-removing-instances"></a>Escala no papel removendo instâncias

Pode escalar um papel removendo os casos da mesma forma. Defina o parâmetro **Count** no **Set-AzureRole** para o número de casos que pretende ter após a escala em funcionamento estar completa.

## <a name="next-steps"></a>Passos seguintes

Não é possível configurar automaticamente para serviços em nuvem a partir da PowerShell. Para isso, veja como escalar automaticamente um serviço de [nuvem.](cloud-services-how-to-scale-portal.md)
