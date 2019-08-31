---
title: Personalizar feed para usuários da área de trabalho virtual do Windows-Azure
description: Como personalizar o feed para usuários da área de trabalho virtual do Windows com cmdlets do PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
ms.openlocfilehash: 49b678bea820f3cbead7479bb414ca9f35a29fa4
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70163625"
---
# <a name="customize-feed-for-windows-virtual-desktop-users"></a>Personalizar o feed para utilizadores do Windows Virtual Desktop

Você pode personalizar o feed para que os recursos do RemoteApp e da área de trabalho remota apareçam de maneira reconhecível para seus usuários.

Primeiro, [Baixe e importe o módulo do PowerShell de área de trabalho virtual do Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) para usar em sua sessão do PowerShell, se ainda não tiver feito isso. Depois disso, execute o seguinte cmdlet para entrar em sua conta:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="customize-the-display-name-for-a-remoteapp"></a>Personalizar o nome de exibição para um RemoteApp

Você pode alterar o nome de exibição de um RemoteApp publicado definindo o nome amigável. Por padrão, o nome amigável é igual ao nome do programa RemoteApp.

Para recuperar uma lista de RemoteApps publicados para um grupo de aplicativos, execute o seguinte cmdlet do PowerShell:

```powershell
Get-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```
![Uma captura de tela do cmdlet Get-RDSRemoteApp do PowerShell com Name e FriendlyName realçado.](media/get-rdsremoteapp.png)

Para atribuir um nome amigável a um RemoteApp, execute o seguinte cmdlet do PowerShell:

```powershell
Set-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -Name <existingappname> -FriendlyName <newfriendlyname>
```
![Uma captura de tela do cmdlet do PowerShell Set-RDSRemoteApp com nome e novo FriendlyName realçado.](media/set-rdsremoteapp.png)

## <a name="customize-the-display-name-for-a-remote-desktop"></a>Personalizar o nome de exibição para um Área de Trabalho Remota

Você pode alterar o nome de exibição de uma área de trabalho remota publicada definindo um nome amigável. Se você criou manualmente um pool de hosts e um grupo de aplicativos da área de trabalho por meio do PowerShell, o nome amigável padrão é "área de trabalho da sessão". Se você criou um pool de hosts e um grupo de aplicativos de área de trabalho por meio do modelo de Azure Resource Manager do GitHub ou da oferta do Azure Marketplace, o nome amigável padrão é o mesmo que o nome do pool de hosts.

Para recuperar o recurso de área de trabalho remota, execute o seguinte cmdlet do PowerShell:

```powershell
Get-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```
![Uma captura de tela do cmdlet Get-RDSRemoteApp do PowerShell com Name e FriendlyName realçado.](media/get-rdsremotedesktop.png)

Para atribuir um nome amigável ao recurso de área de trabalho remota, execute o seguinte cmdlet do PowerShell:

```powershell
Set-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -FriendlyName <newfriendlyname>
```
![Uma captura de tela do cmdlet do PowerShell Set-RDSRemoteApp com nome e novo FriendlyName realçado.](media/set-rdsremotedesktop.png)

## <a name="next-steps"></a>Passos Seguintes

Agora que você personalizou o feed para os usuários, você pode entrar em um cliente de área de trabalho virtual do Windows para testá-lo. Para fazer isso, vá para o How-tos da área de trabalho virtual do Windows:
    
 * [Conecte-se do Windows 10 ou Windows 7](connect-windows-7-and-10.md)
 * [Conectar-se de um navegador da Web](connect-web.md) 
