---
title: Personalize o feed para utilizadores do Windows Virtual Desktop - Azure
description: Como personalizar o feed para utilizadores do Windows Virtual Desktop com cmdlets PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: a93aa35353940cfdbded1634448d4f6d2865c365
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82614840"
---
# <a name="customize-feed-for-windows-virtual-desktop-users"></a>Personalizar o feed para utilizadores do Windows Virtual Desktop

>[!IMPORTANT]
>Este conteúdo aplica-se à versão outono 2019 que não suporta objetos de ambiente de trabalho virtual do Gestor de Recursos Do Azure. Se está a tentar gerir os objetos de ambiente de trabalho virtual do Gestor de Recursos Do Azure Windows introduzidos na atualização da primavera de 2020, consulte [este artigo](../customize-feed-for-virtual-desktop-users.md).

Pode personalizar o feed para que os recursos remotos e de ambiente de trabalho remoto apareçam de forma reconhecível para os seus utilizadores.

Primeiro, [descarregue e importe o módulo Windows Virtual Desktop PowerShell](/powershell/windows-virtual-desktop/overview/) para utilizar na sua sessão PowerShell se ainda não o fez. Depois disso, execute o seguinte cmdlet para iniciar sessão na sua conta:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="customize-the-display-name-for-a-remoteapp"></a>Personalize o nome do ecrã para um RemoteApp

Pode alterar o nome do ecrã para um RemoteApp publicado, definindo o nome amigável. Por padrão, o nome amigável é o mesmo que o nome do programa RemoteApp.

Para recuperar uma lista de RemoteApps publicados para um grupo de aplicações, execute o seguinte cmdlet PowerShell:

```powershell
Get-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```
![Uma imagem de PowerShell cmdlet Get-RDSRemoteApp com nome e nome amigável destacados.](../media/get-rdsremoteapp.png)

Para atribuir um nome amigável a um RemoteApp, execute o seguinte cmdlet PowerShell:

```powershell
Set-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -Name <existingappname> -FriendlyName <newfriendlyname>
```
![Uma imagem de PowerShell cmdlet Set-RDSRemoteApp com nome e novo nome amigável destacado.](../media/set-rdsremoteapp.png)

## <a name="customize-the-display-name-for-a-remote-desktop"></a>Personalize o nome do ecrã para um ambiente de trabalho remoto

Pode alterar o nome do ecrã para um ambiente de trabalho remoto publicado, definindo um nome amigável. Se criou manualmente um grupo de aplicativos de hospedagem e desktop através do PowerShell, o nome amigável padrão é "Session Desktop". Se criou um grupo de aplicativos de piscina e desktop através do modelo GitHub Azure Resource Manager ou da oferta do Azure Marketplace, o nome amigável padrão é o mesmo que o nome da piscina anfitriã.

Para recuperar o recurso remoto de ambiente de trabalho, execute o seguinte cmdlet PowerShell:

```powershell
Get-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```
![Uma imagem de PowerShell cmdlet Get-RDSRemoteApp com nome e nome amigável destacados.](../media/get-rdsremotedesktop.png)

Para atribuir um nome amigável ao recurso de ambiente de trabalho remoto, execute o seguinte cmdlet PowerShell:

```powershell
Set-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -FriendlyName <newfriendlyname>
```
![Uma imagem de PowerShell cmdlet Set-RDSRemoteApp com nome e novo nome amigável destacado.](../media/set-rdsremotedesktop.png)

## <a name="next-steps"></a>Passos seguintes

Agora que personalizou o feed para os utilizadores, pode iniciar sessão num cliente do Windows Virtual Desktop para o testar. Para tal, continue a ligar ao Windows Virtual Desktop How-tos:
    
 * [Ligar a partir do Windows 10 ou Windows 7](../connect-windows-7-and-10.md)
 * [Ligar a partir de um browser web](connect-web-2019.md) 
