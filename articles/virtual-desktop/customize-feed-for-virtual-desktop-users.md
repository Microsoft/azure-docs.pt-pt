---
title: Personalizar o feed para usuários de área de Trabalho Virtual do Windows - Azure
description: Como personalizar o feed para os utilizadores da área de Trabalho Virtual do Windows com cmdlets do PowerShell.
services: virtual-desktop
author: v-hevem
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 05/30/2019
ms.author: v-hevem
ms.openlocfilehash: 869760a12089ed7453199ad8a3fa18a6cca87511
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/17/2019
ms.locfileid: "67157093"
---
# <a name="customize-feed-for-windows-virtual-desktop-users"></a>Personalizar o feed para utilizadores do Windows Virtual Desktop

Pode personalizar o feed, para que o RemoteApp e os recursos de área de trabalho remotos são apresentados de forma reconhecível para os seus utilizadores.

Primeiro, [transferir e importar o módulo do Windows PowerShell de ambiente de Trabalho Virtual](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) a utilizar na sua sessão do PowerShell, se ainda não o fez.

## <a name="customize-the-display-name-for-a-remoteapp"></a>Personalizar o nome a apresentar para um RemoteApp

Pode alterar o nome a apresentar para um RemoteApp publicado ao definir o nome amigável. Por predefinição, o nome amigável é igual ao nome do programa RemoteApp.

Para obter uma lista dos RemoteApps publicados para um grupo de aplicações, execute o seguinte cmdlet do PowerShell:

```powershell
Get-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```
![Captura de ecrã do cmdlet do PowerShell Get-RDSRemoteApp com o nome e FriendlyName realçado.](media/get-rdsremoteapp.png)

Para atribuir um nome amigável para um RemoteApp, execute o seguinte cmdlet do PowerShell:

```powershell
Set-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -Name <existingappname> -FriendlyName <newfriendlyname>
```
![Captura de ecrã do cmdlet do PowerShell Set-RDSRemoteApp com o nome e o novo FriendlyName realçado.](media/set-rdsremoteapp.png)

## <a name="customize-the-display-name-for-a-remote-desktop"></a>Personalizar o nome a apresentar para um ambiente de trabalho remoto

Pode alterar o nome a apresentar para um ambiente de trabalho remoto publicado, definindo um nome amigável. Se um anfitrião criados manualmente conjunto e o grupo de aplicações de ambiente de trabalho através do PowerShell, o nome amigável de predefinido é "Desktop de sessão". Se tiver criado um host de agrupamento e o grupo de aplicações de ambiente de trabalho através do modelo do GitHub do Azure Resource Manager ou a oferta do Azure Marketplace, o nome amigável do padrão é o mesmo que o nome do conjunto de anfitrião.

Para obter o recurso de ambiente de trabalho remoto, execute o seguinte cmdlet do PowerShell:

```powershell
Get-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```
![Captura de ecrã do cmdlet do PowerShell Get-RDSRemoteApp com o nome e FriendlyName realçado.](media/get-rdsremotedesktop.png)

Para atribuir um nome amigável para o recurso de ambiente de trabalho remoto, execute o seguinte cmdlet do PowerShell:

```powershell
Set-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -FriendlyName <newfriendlyname>
```
![Captura de ecrã do cmdlet do PowerShell Set-RDSRemoteApp com o nome e o novo FriendlyName realçado.](media/set-rdsremotedesktop.png)

## <a name="next-steps"></a>Passos Seguintes

Agora que personalizou o feed para os utilizadores, pode iniciar sessão cliente de área de Trabalho Virtual do Windows para testá-lo. Para fazê-lo, avance para ligar ao Windows Virtual Desktop procedimentos:
    
 * [Ligar a partir do Windows 10 ou Windows 7](connect-windows-7-and-10.md)
 * [Ligar a partir de um navegador da web](connect-web.md) 
