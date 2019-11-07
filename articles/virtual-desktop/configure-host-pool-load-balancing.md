---
title: Configurar o balanceamento de carga de área de trabalho virtual do Windows-Azure
description: Como configurar o método de balanceamento de carga para um ambiente de área de trabalho virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
ms.openlocfilehash: 61c11e342f3b4f906b453e0962018a8f8c34acd4
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73605879"
---
# <a name="configure-the-windows-virtual-desktop-load-balancing-method"></a>Configurar o método de balanceamento de carga do Windows Virtual Desktop

Configurar o método de balanceamento de carga para um pool de hosts permite que você ajuste o ambiente de área de trabalho virtual do Windows para atender melhor às suas necessidades.

>[!NOTE]
> Isso não se aplica a um pool de hosts de área de trabalho persistente porque os usuários sempre têm um mapeamento 1:1 para um host de sessão dentro do pool de hosts.

## <a name="configure-breadth-first-load-balancing"></a>Configurar o balanceamento de carga da primeira amplitude

O balanceamento de carga da primeira amplitude é a configuração padrão para novos pools de hosts não persistentes. O balanceamento de carga da primeira distribuição distribui novas sessões de usuário em todos os hosts de sessão disponíveis no pool de hosts. Ao configurar o balanceamento de carga da primeira amplitude, você pode definir um limite máximo de sessão por host de sessão no pool de hosts.

Primeiro, [Baixe e importe o módulo do PowerShell de área de trabalho virtual do Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) para usar em sua sessão do PowerShell, se ainda não tiver feito isso. Depois disso, execute o seguinte cmdlet para entrar em sua conta:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Para configurar um pool de hosts para executar o balanceamento de carga de primeira amplitude sem ajustar o limite máximo de sessão, execute o seguinte cmdlet do PowerShell:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer
```

Para configurar um pool de hosts para executar o balanceamento de carga da primeira amplitude e usar um novo limite máximo de sessão, execute o seguinte cmdlet do PowerShell:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer -MaxSessionLimit ###
```

## <a name="configure-depth-first-load-balancing"></a>Configurar o balanceamento de carga de profundidade inicial

O balanceamento de carga em profundidade distribui novas sessões de usuário para um host de sessão disponível com o número mais alto de conexões, mas não atingiu seu limite máximo de limite de sessão. Ao configurar o balanceamento de carga de profundidade inicial, você **deve** definir um limite máximo de sessão por host de sessão no pool de hosts.

Para configurar um pool de hosts para executar o balanceamento de carga de profundidade primeiro, execute o seguinte cmdlet do PowerShell:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -DepthFirstLoadBalancer -MaxSessionLimit ###
```
