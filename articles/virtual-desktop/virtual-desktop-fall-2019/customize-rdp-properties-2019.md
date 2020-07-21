---
title: Personalize propriedades RDP com PowerShell Fall 2019 - Azure
description: Como personalizar propriedades RDP para windows Virtual Desktop com cmdlets PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 844c59c6e5ea6d730c64fea5fb2dee51f602c382
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86527630"
---
# <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>Personalize propriedades do Protocolo de Ambiente de Trabalho Remoto para uma piscina de anfitriões

>[!IMPORTANT]
>Este conteúdo aplica-se à versão outono 2019 que não suporta objetos de ambiente de trabalho virtuais do Gestor de Recursos Azure. Se está a tentar gerir os objetos virtuais do Azure Resource Manager Windows, introduzidos na atualização da primavera de 2020, consulte [este artigo](../customize-rdp-properties.md).

A personalização das propriedades do Protocolo remoto de Ambiente de Trabalho (RDP) de uma piscina anfitriã, como experiência multi-monitor e reorientação de áudio, permite-lhe proporcionar uma experiência ideal para os seus utilizadores com base nas suas necessidades. Pode personalizar propriedades RDP no Windows Virtual Desktop utilizando o parâmetro **-CustomRdpProperty** no **cmdlet Set-RdsHostPool.**

Consulte [as definições de ficheiros RDP suportados](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files?context=/azure/virtual-desktop/context/context) para obter uma lista completa de propriedades suportadas e os seus valores predefinidos.

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
> ![Uma imagem do PowerShell cmdlet Get-RDSRemoteApp com nome e Nome Amigável em destaque.](../media/singlecustomrdpproperty.png)

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>Adicione ou edite várias propriedades RDP personalizadas

Para adicionar ou editar várias propriedades RDP personalizadas, executar os seguintes cmdlets PowerShell, fornecendo as propriedades RDP personalizadas como uma cadeia separada de semicolon:

```powershell
$properties="<property1>;<property2>;<property3>"
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty $properties
```

> [!div class="mx-imgBorder"]
> ![Uma imagem do PowerShell cmdlet Get-RDSRemoteApp com nome e Nome Amigável em destaque.](../media/multiplecustomrdpproperty.png)

## <a name="reset-all-custom-rdp-properties"></a>Redefinir todas as propriedades RDP personalizadas

Pode redefinir as propriedades RDP personalizadas individuais para os seus valores predefinidos seguindo as instruções em [Adicionar ou editar uma única propriedade DDP personalizada,](#add-or-edit-a-single-custom-rdp-property)ou pode redefinir todas as propriedades RDP personalizadas para uma piscina de anfitrião executando o seguinte cmdlet PowerShell:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty ""
```

> [!div class="mx-imgBorder"]
> ![Uma imagem do PowerShell cmdlet Get-RDSRemoteApp com nome e Nome Amigável em destaque.](../media/resetcustomrdpproperty.png)

## <a name="next-steps"></a>Próximos passos

Agora que personalizou as propriedades RDP para uma determinada piscina de anfitriões, pode iniciar sessão num cliente virtual do Windows desktop para testá-las como parte de uma sessão de utilizador. Estes próximos dois How-tos dir-lhe-ão como se conectar a uma sessão utilizando o cliente à sua escolha:

- [Ligar ao cliente de Ambiente de Trabalho do Windows](connect-windows-7-10-2019.md)
- [Ligar com o cliente web](connect-web-2019.md)
