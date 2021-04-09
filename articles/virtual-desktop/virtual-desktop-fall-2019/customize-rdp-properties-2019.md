---
title: Personalize propriedades RDP com PowerShell Windows Virtual Desktop (clássico) - Azure
description: Como personalizar propriedades RDP para Windows Virtual Desktop (clássico) com cmdlets PowerShell.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5110e97e52939ea2115bb839768cc7ab96802961
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "95020713"
---
# <a name="customize-remote-desktop-protocol-properties-for-a--windows-virtual-desktop-classic-host-pool"></a>Personalize propriedades do Protocolo de Ambiente de Trabalho Remoto para uma piscina de anfitriões virtual do Windows (clássico)

>[!IMPORTANT]
>Este conteúdo aplica-se ao Windows Virtual Desktop (clássico), que não suporta objetos de desktop virtual do Gestor de Recursos Azure Windows. Se estiver a tentar gerir os objetos de ambiente de trabalho virtuais do Azure Resource Manager Windows, consulte [este artigo](../customize-rdp-properties.md).

A personalização das propriedades do Protocolo remoto de Ambiente de Trabalho (RDP) de uma piscina anfitriã, como experiência multi-monitor e reorientação de áudio, permite-lhe proporcionar uma experiência ideal para os seus utilizadores com base nas suas necessidades. Pode personalizar propriedades RDP no Windows Virtual Desktop utilizando o parâmetro **-CustomRdpProperty** no **cmdlet Set-RdsHostPool.**

Consulte [as definições de ficheiros RDP suportados](/windows-server/remote/remote-desktop-services/clients/rdp-files?context=%2fazure%2fvirtual-desktop%2fcontext%2fcontext) para obter uma lista completa de propriedades suportadas e os seus valores predefinidos.

Em primeiro lugar, [descarregue e importe o módulo PowerShell virtual do Windows Desktop](/powershell/windows-virtual-desktop/overview/) para utilizar na sessão PowerShell se ainda não o fez. Depois disso, execute o seguinte cmdlet para iniciar scontabilidade na sua conta:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="default-rdp-properties"></a>Propriedades RDP predefinidos

Por predefinição, os ficheiros RDP publicados contêm as seguintes propriedades:

|Propriedades de RDP | Computadores de Secretária | RemotasApps |
|---|---| --- |
| Modo multi-monitor | Ativado | N/D |
| Redirecionamentos de unidade ativados | Unidades, pranchetas, impressoras, portas COM, dispositivos USB e smartcards| Unidades, pranchetas e impressoras |
| Modo áudio remoto | Jogue localmente | Jogue localmente |

Quaisquer propriedades personalizadas que você definir para a piscina anfitriã irá anular estes padrão.

## <a name="add-or-edit-a-single-custom-rdp-property"></a>Adicione ou edite uma única propriedade RDP personalizada

Para adicionar ou editar uma única propriedade RDP personalizada, executar o seguinte cmdlet PowerShell:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty "<property>"
```

> [!div class="mx-imgBorder"]
> ![Uma imagem de Get-RDSRemoteApp de cmdlet PowerShell com Nome e Amistoso destacados para editar uma propriedade R D P personalizada.](../media/singlecustomrdpproperty.png)

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>Adicione ou edite várias propriedades RDP personalizadas

Para adicionar ou editar várias propriedades RDP personalizadas, executar os seguintes cmdlets PowerShell, fornecendo as propriedades RDP personalizadas como uma cadeia separada de semicolon:

```powershell
$properties="<property1>;<property2>;<property3>"
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty $properties
```

> [!div class="mx-imgBorder"]
> ![Uma imagem de Set-RDSRemoteApp de cmdlet PowerShell com Nome e Amistoso destacados para editar uma propriedade R D P personalizada.](../media/multiplecustomrdpproperty.png)

## <a name="reset-all-custom-rdp-properties"></a>Redefinir todas as propriedades RDP personalizadas

Pode redefinir as propriedades RDP personalizadas individuais para os seus valores predefinidos seguindo as instruções em [Adicionar ou editar uma única propriedade DDP personalizada,](#add-or-edit-a-single-custom-rdp-property)ou pode redefinir todas as propriedades RDP personalizadas para uma piscina de anfitrião executando o seguinte cmdlet PowerShell:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty ""
```

> [!div class="mx-imgBorder"]
> ![Uma imagem de powerShell cmdlet Get-RDSRemoteApp com Nome e Amistoso em destaque.](../media/resetcustomrdpproperty.png)

## <a name="next-steps"></a>Passos seguintes

Agora que personalizou as propriedades RDP para uma determinada piscina de anfitriões, pode iniciar sessão num cliente virtual do Windows desktop para testá-las como parte de uma sessão de utilizador. Estes próximos dois How-tos dir-lhe-ão como se conectar a uma sessão utilizando o cliente à sua escolha:

- [Ligar ao cliente de Ambiente de Trabalho do Windows](connect-windows-7-10-2019.md)
- [Ligar com o cliente web](connect-web-2019.md)