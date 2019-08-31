---
title: Personalizar propriedades RDP com o PowerShell – Azure
description: Como personalizar as propriedades de RDP para a área de trabalho virtual do Windows com cmdlets do PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
ms.openlocfilehash: fbc24705288d9bb4acf0d1b2d99dfbda32149614
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70163562"
---
# <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>Personalizar propriedades de protocolo RDP para um pool de hosts

Personalizar as propriedades de protocolo RDP (RDP) de um pool de hosts, como experiência de vários monitores e redirecionamento de áudio, permite que você forneça uma experiência ideal para seus usuários com base em suas necessidades. Você pode personalizar as propriedades de RDP na área de trabalho virtual do Windows usando o parâmetro **-CustomRdpProperty** no cmdlet **set-RdsHostPool** .

Consulte [área de trabalho remota configurações de arquivo RDP](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files) para obter uma lista completa das propriedades com suporte e seus valores padrão.

Primeiro, [Baixe e importe o módulo do PowerShell de área de trabalho virtual do Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) para usar em sua sessão do PowerShell, se ainda não tiver feito isso. Depois disso, execute o seguinte cmdlet para entrar em sua conta:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="add-or-edit-a-single-custom-rdp-property"></a>Adicionar ou editar uma única propriedade RDP personalizada

Para adicionar ou editar uma única propriedade RDP personalizada, execute o seguinte cmdlet do PowerShell:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty "<property>"
```
![Uma captura de tela do cmdlet Get-RDSRemoteApp do PowerShell com Name e FriendlyName realçado.](media/singlecustomrdpproperty.png)

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>Adicionar ou editar várias propriedades personalizadas de RDP

Para adicionar ou editar várias propriedades de RDP personalizadas, execute os seguintes cmdlets do PowerShell fornecendo as propriedades de RDP personalizadas como uma cadeia de caracteres separada por ponto-e-vírgula:

```powershell
$properties="<property1>;<property2>;<property3>"
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty $properties
```
![Uma captura de tela do cmdlet Get-RDSRemoteApp do PowerShell com Name e FriendlyName realçado.](media/multiplecustomrdpproperty.png)

## <a name="reset-all-custom-rdp-properties"></a>Redefinir todas as propriedades de RDP personalizadas

Você pode redefinir propriedades individuais de RDP personalizadas para seus valores padrão seguindo as instruções em [Adicionar ou editar uma única propriedade RDP personalizada](#add-or-edit-a-single-custom-rdp-property), ou você pode redefinir todas as propriedades RDP personalizadas para um pool de hosts executando o seguinte cmdlet do PowerShell:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty ""
```
![Uma captura de tela do cmdlet Get-RDSRemoteApp do PowerShell com Name e FriendlyName realçado.](media/resetcustomrdpproperty.png)

## <a name="next-steps"></a>Passos Seguintes

Agora que você personalizou as propriedades de RDP para um determinado pool de hosts, você pode entrar em um cliente de área de trabalho virtual do Windows para testá-los como parte de uma sessão de usuário. Para fazer isso, vá para o How-tos da área de trabalho virtual do Windows:

- [Conecte-se do Windows 10 e Windows 7](connect-windows-7-and-10.md)
- [Conectar-se de um navegador da Web](connect-web.md)
