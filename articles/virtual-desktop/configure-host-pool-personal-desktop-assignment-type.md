---
title: Tipo de atribuição da área de trabalho virtual do Windows para desktop pessoal-Azure
description: Como configurar o tipo de atribuição para um pool de hosts da área de trabalho virtual pessoal do Windows.
services: virtual-desktop
author: HeidiLohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: helohr
ms.openlocfilehash: 0db1ffe46165d91148b6980abbf89d03340e646e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75486693"
---
# <a name="configure-the-personal-desktop-host-pool-assignment-type"></a>Configurar o tipo de atribuição de pool de hosts de área de trabalho pessoal

Você pode configurar o tipo de atribuição de seu pool de hosts de área de trabalho pessoal para ajustar o ambiente de área de trabalho virtual do Windows para atender melhor às suas necessidades. Neste tópico, mostraremos como configurar a atribuição automática ou direta para seus usuários.

>[!NOTE]
> As instruções neste artigo se aplicam somente a pools de hosts de área de trabalho pessoais, não pools de hosts em pool, pois os usuários em pools de hosts em pool não são atribuídos a hosts de sessão

## <a name="configure-automatic-assignment"></a>Configurar atribuição automática

Atribuição automática é o tipo de atribuição padrão para novos pools de hosts de área de trabalho pessoal criados no seu ambiente de área de trabalho virtual do Windows. A atribuição automática de usuários não exige um host de sessão específico.

Para atribuir usuários automaticamente, primeiro atribua-os ao pool de hosts de área de trabalho pessoal para que eles possam ver a área de trabalho em seu feed. Quando um usuário atribuído iniciar a área de trabalho no feed, ele solicitará um host de sessão disponível se ainda não tiver se conectado ao pool de hosts, o que concluirá o processo de atribuição.

Antes de começar, [Baixe e importe o módulo do PowerShell da área de trabalho virtual do Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) , caso ainda não tenha feito isso. 

> [!NOTE]
> Verifique se você instalou o módulo do PowerShell da área de trabalho virtual do Windows versão 1.0.1534.2001 ou posterior antes de seguir estas instruções.

Depois disso, execute o seguinte cmdlet para entrar em sua conta:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Para configurar um pool de hosts para atribuir usuários automaticamente a VMs, execute o seguinte cmdlet do PowerShell:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -AssignmentType Automatic
```

Para atribuir um usuário ao pool de hosts de área de trabalho pessoal, execute o seguinte cmdlet do PowerShell:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

## <a name="configure-direct-assignment"></a>Configurar atribuição direta

Ao contrário da atribuição automática, ao usar a atribuição direta, você deve atribuir o usuário ao pool de hosts da área de trabalho pessoal e a um host de sessão específico antes que eles possam se conectar à área de trabalho pessoal. Se o usuário for atribuído apenas a um pool de hosts sem uma atribuição de host de sessão, ele não poderá acessar recursos.

Para configurar um pool de hosts para exigir atribuição direta de usuários a hosts de sessão, execute o seguinte cmdlet do PowerShell:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -AssignmentType Direct
```

Para atribuir um usuário ao pool de hosts de área de trabalho pessoal, execute o seguinte cmdlet do PowerShell:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

Para atribuir um usuário a um host de sessão específico, execute o seguinte cmdlet do PowerShell:

```powershell
Set-RdsSessionHost <tenantname> <hostpoolname> -Name <sessionhostname> -AssignedUser <userupn>
```

## <a name="next-steps"></a>Passos seguintes

Agora que você configurou o tipo de atribuição de área de trabalho pessoal, você pode entrar em um cliente de área de trabalho virtual do Windows para testá-lo como parte de uma sessão de usuário. Esses próximos dois como tos informarão como se conectar a uma sessão usando o cliente de sua escolha:

- [Conectar-se ao cliente de desktop do Windows](connect-windows-7-and-10.md)
- [Conectar-se ao cliente Web](connect-web.md)
