---
title: Personalize o feed para utilizadores do Windows Virtual Desktop (clássico) - Azure
description: Como personalizar o feed para utilizadores do Windows Virtual Desktop (clássico) com cmdlets PowerShell.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: cd7496690ec88fbe4297386c32d1b8a2c3234577
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91540766"
---
# <a name="customize-feed-for-windows-virtual-desktop-classic-users"></a>Personalize o feed para utilizadores do Windows Virtual Desktop (clássico)

>[!IMPORTANT]
>Este conteúdo aplica-se ao Windows Virtual Desktop (clássico), que não suporta objetos de desktop virtual do Gestor de Recursos Azure Windows. Se estiver a tentar gerir os objetos de ambiente de trabalho virtuais do Azure Resource Manager Windows, consulte [este artigo](../customize-feed-for-virtual-desktop-users.md).

Pode personalizar o feed para que os recursos de ambiente de trabalho remoto e remoto apareçam de forma reconhecível para os seus utilizadores.

Em primeiro lugar, [descarregue e importe o módulo PowerShell virtual do Windows Desktop](/powershell/windows-virtual-desktop/overview/) para utilizar na sessão PowerShell se ainda não o fez. Depois disso, execute o seguinte cmdlet para iniciar scontabilidade na sua conta:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="customize-the-display-name-for-a-remoteapp"></a>Personalize o nome do ecrã para um RemoteApp

Pode alterar o nome de exibição para um RemoteApp publicado definindo o nome amigável. Por predefinição, o nome amigável é o mesmo que o nome do programa RemoteApp.

Para obter uma lista de RemoteApps publicados para um grupo de aplicações, execute o seguinte cmdlet PowerShell:

```powershell
Get-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```

> [!div class="mx-imgBorder"]
> ![Uma imagem de Get-RDSRemoteApp de cmdlet PowerShell com Nome e Amistoso destacados para personalizar o nome do ecrã.](../media/get-rdsremoteapp.png)

Para atribuir um nome amigável a um RemoteApp, execute o seguinte cmdlet PowerShell:

```powershell
Set-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -Name <existingappname> -FriendlyName <newfriendlyname>
```

> [!div class="mx-imgBorder"]
> ![Uma imagem de Set-RDSRemoteApp de cmdlet PowerShell com Nome e Novo Nome Amigável destacados para personalizar o nome do ecrã.](../media/set-rdsremoteapp.png)

## <a name="customize-the-display-name-for-a-remote-desktop"></a>Personalize o nome do visor para um ambiente de trabalho remoto

Pode alterar o nome de visualização para um ambiente de trabalho remoto publicado definindo um nome amigável. Se criou manualmente um grupo de aplicações de anfitrião e de mesa através do PowerShell, o nome padrão amigável é "Session Desktop". Se criou um grupo de aplicações de pool e desktop de anfitriões através do modelo GitHub Azure Resource Manager ou da oferta do Azure Marketplace, o nome padrão amigável é o mesmo que o nome da piscina do anfitrião.

Para recuperar o recurso de ambiente de trabalho remoto, execute o seguinte cmdlet PowerShell:

```powershell
Get-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```

> [!div class="mx-imgBorder"]
> ![Uma imagem de powerShell cmdlet Get-RDSRemoteApp com Nome e Amistoso em destaque.](../media/get-rdsremotedesktop.png)

Para atribuir um nome amigável ao recurso de ambiente de trabalho remoto, execute o seguinte cmdlet PowerShell:

```powershell
Set-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -FriendlyName <newfriendlyname>
```

> [!div class="mx-imgBorder"]
> ![Uma imagem de powerShell cmdlet Set-RDSRemoteApp com Nome e Novo Nome Amigável em destaque.](../media/set-rdsremotedesktop.png)

## <a name="next-steps"></a>Passos seguintes

Agora que personalizou o feed para os utilizadores, pode iniciar sedução num cliente do Windows Virtual Desktop para o testar. Para tal, continue a ligar ao Windows Virtual Desktop Como-tos:

 * [Ligar a partir do Windows 10 ou Windows 7](connect-windows-7-10-2019.md)
 * [Ligar a partir de um browser web](connect-web-2019.md)
