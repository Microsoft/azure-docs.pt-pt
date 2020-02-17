---
title: Configure Windows Virtual Desktop equilíbrio de carga - Azure
description: Como configurar o método de equilíbrio de carga para um ambiente de ambiente de trabalho virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
ms.openlocfilehash: 1b341732dd844ba172c7e682fb7089c98ca9c165
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/15/2020
ms.locfileid: "77367601"
---
# <a name="configure-the-windows-virtual-desktop-load-balancing-method"></a>Configurar o método de balanceamento de carga do Windows Virtual Desktop

Configurar o método de equilíbrio de carga para um pool de anfitriões permite-lhe ajustar o ambiente de ambiente de trabalho virtual do Windows para melhor atender às suas necessidades.

>[!NOTE]
> Isto não se aplica a um grupo de anfitriões persistente seletiva porque os utilizadores têm sempre um mapeamento 1:1 para um anfitrião de sessão dentro da piscina anfitriã.

## <a name="configure-breadth-first-load-balancing"></a>Configure o equilíbrio de primeira carga de amplitude

O equilíbrio de carga de primeira amplitude é a configuração padrão para novas piscinas hospedeiras não persistentes. O equilíbrio de carga de primeira largura distribui novas sessões de utilizador em todos os anfitriões disponíveis na piscina anfitriã. Ao configurar o equilíbrio de carga de primeira largura, pode definir um limite máximo de sessão por anfitrião da sessão na piscina anfitriã.

Primeiro, [descarregue e importe o módulo Windows Virtual Desktop PowerShell](/powershell/windows-virtual-desktop/overview/) para utilizar na sua sessão PowerShell se ainda não o fez. Depois disso, execute o seguinte cmdlet para iniciar sessão na sua conta:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Para configurar uma piscina hospedeira para realizar um equilíbrio de carga de primeira largura sem ajustar o limite máximo de sessão, execute o seguinte cmdlet PowerShell:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer
```

Para configurar uma piscina hospedeira para realizar um equilíbrio de carga de primeira largura e utilizar um novo limite máximo de sessão, execute o seguinte cmdlet PowerShell:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer -MaxSessionLimit ###
```

## <a name="configure-depth-first-load-balancing"></a>Configurar o equilíbrio de primeira carga de profundidade

O equilíbrio de carga de primeira profundidade distribui novas sessões de utilizador para um anfitrião de sessão disponível com o maior número de ligações, mas não atingiu o seu limite máximo de sessão. Ao configurar o equilíbrio de carga de profundidade primeiro, **deve** definir um limite máximo de sessão por anfitrião da sessão na piscina anfitriã.

Para configurar uma piscina hospedeira para realizar um equilíbrio de carga de primeira profundidade, execute o seguinte cmdlet PowerShell:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -DepthFirstLoadBalancer -MaxSessionLimit ###
```
