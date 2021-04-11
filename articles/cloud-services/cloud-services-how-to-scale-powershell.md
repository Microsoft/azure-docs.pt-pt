---
title: Dimensione um serviço em nuvem Azure (clássico) no Windows PowerShell | Microsoft Docs
description: (clássico) Aprenda a usar o PowerShell para escalar um papel web ou um papel de trabalhador dentro ou fora em Azure.
ms.topic: article
ms.service: cloud-services
ms.subservice: autoscale
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 0b0a9cc8032cfd849f053e2d45e05f85e4216a23
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105936619"
---
# <a name="how-to-scale-an-azure-cloud-service-classic-in-powershell"></a>Como escalar um Azure Cloud Service (clássico) em PowerShell

> [!IMPORTANT]
> [Azure Cloud Services (suporte alargado)](../cloud-services-extended-support/overview.md) é um novo modelo de implementação baseado em Recursos Azure para o produto Azure Cloud Services.Com esta alteração, os Serviços Azure Cloud em execução no modelo de implementação baseado no Azure Service Manager foram renomeados como Cloud Services (clássico) e todas as novas implementações devem utilizar [os Serviços Cloud (suporte alargado)](../cloud-services-extended-support/overview.md).

Pode utilizar o Windows PowerShell para escalar uma função web ou papel de trabalhador dentro ou fora, adicionando ou removendo instâncias.  

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Antes de realizar qualquer operação na sua subscrição através do PowerShell, tem de iniciar sessão:

```powershell
Add-AzureAccount
```

Se tiver várias subscrições associadas à sua conta, poderá ter de alterar a subscrição atual dependendo do local onde reside o seu serviço na nuvem. Para verificar a subscrição atual, corra:

```powershell
Get-AzureSubscription -Current
```

Se precisar de alterar a subscrição atual, corra:

```powershell
Set-AzureSubscription -SubscriptionId <subscription_id>
```

## <a name="check-the-current-instance-count-for-your-role"></a>Verifique a contagem de exemplos atuais para o seu papel

Para verificar o estado atual do seu papel, corra:

```powershell
Get-AzureRole -ServiceName '<your_service_name>' -RoleName '<your_role_name>'
```

Deve obter informações sobre o papel, incluindo a sua versão atual do SO e contagem de instâncias. Neste caso, o papel tem um único caso.

![Informação sobre o papel](./media/cloud-services-how-to-scale-powershell/get-azure-role.png)

## <a name="scale-out-the-role-by-adding-more-instances"></a>Escale o papel adicionando mais instâncias

Para aumentar o seu papel, passe o número desejado de instâncias como parâmetro **conde** para o **cmdlet Set-AzureRole:**

```powershell
Set-AzureRole -ServiceName '<your_service_name>' -RoleName '<your_role_name>' -Slot <target_slot> -Count <desired_instances>
```

O cmdlet bloqueia momentaneamente enquanto as novas instâncias são a provisionadas e iniciadas. Durante este tempo, se abrir uma nova janela PowerShell e ligar para **o Get-AzureRole** como mostrado anteriormente, verá a contagem da nova instância-alvo. E se inspecionar o estado da função no portal, deverá ver a nova instância a começar:

![VM instância começando no portal](./media/cloud-services-how-to-scale-powershell/role-instance-starting.png)

Uma vez iniciadas as novas instâncias, o cmdlet regressará com sucesso:

![Exemplo de papel aumenta o sucesso](./media/cloud-services-how-to-scale-powershell/set-azure-role-success.png)

## <a name="scale-in-the-role-by-removing-instances"></a>Escala no papel removendo instâncias

Pode escalar um papel removendo os casos da mesma forma. Desfiza o parâmetro **Contagem** no **Set-AzureRole** para o número de casos que deseja ter após a escala em funcionamento estar completa.

## <a name="next-steps"></a>Passos seguintes

Não é possível configurar a escala automática para serviços em nuvem da PowerShell. Para isso, consulte [como escalar automaticamente um serviço de nuvem](cloud-services-how-to-scale-portal.md).
