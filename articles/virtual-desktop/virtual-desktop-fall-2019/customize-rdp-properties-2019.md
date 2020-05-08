---
title: Personalize propriedades RDP com PowerShell - Azure
description: Como personalizar propriedades RDP para Windows Virtual Desktop com cmdlets PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: dc89b0f6ed745b3de829c0fff145adc79c5af642
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82615555"
---
# <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>Personalize propriedades do Protocolo de Ambiente de Trabalho Remoto para uma piscina de anfitriões

>[!IMPORTANT]
>Este conteúdo aplica-se à versão outono 2019 que não suporta objetos de ambiente de trabalho virtual do Gestor de Recursos Do Azure. Se está a tentar gerir os objetos de ambiente de trabalho virtual do Gestor de Recursos Do Azure Windows introduzidos na atualização da primavera de 2020, consulte [este artigo](../customize-rdp-properties.md).

Personalizar as propriedades do Protocolo de Ambiente de Trabalho Remoto (RDP) de um pool anfitrião, como experiência de multimonitor e reorientação de áudio, permite-lhe oferecer uma experiência ideal para os seus utilizadores com base nas suas necessidades. Pode personalizar propriedades RDP no Windows Virtual Desktop utilizando o parâmetro **-CustomRdpProperty** no **set-RdsHostPool** cmdlet.

Consulte as definições de [ficheiroRD Suportadas](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files?context=/azure/virtual-desktop/context/context) para obter uma lista completa de propriedades suportadas e os seus valores predefinidos.

Primeiro, [descarregue e importe o módulo Windows Virtual Desktop PowerShell](/powershell/windows-virtual-desktop/overview/) para utilizar na sua sessão PowerShell se ainda não o fez. Depois disso, execute o seguinte cmdlet para iniciar sessão na sua conta:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="default-rdp-properties"></a>Propriedades de RDP padrão

Por predefinição, os ficheiros RDP publicados contêm as seguintes propriedades:

|Propriedades de RDP | Computadores de Secretária | Aplicações Remotas |
|---|---| --- |
| Modo multi-monitor | Ativado | N/D |
| Redirecionamentos de acionamento ativados | Unidades, clipboard, impressoras, portas COM, dispositivos USB e smartcards| Unidades, clipboard e impressoras |
| Modo de áudio remoto | Jogar localmente | Jogar localmente |

Quaisquer propriedades personalizadas que defina para a piscina anfitriã anularão estes incumprimentos.

## <a name="add-or-edit-a-single-custom-rdp-property"></a>Adicione ou edite uma única propriedade personalizada rdp

Para adicionar ou editar uma única propriedade personalizada de RDP, executar o seguinte cmdlet PowerShell:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty "<property>"
```

![Uma imagem de PowerShell cmdlet Get-RDSRemoteApp com nome e nome amigável destacados.](../media/singlecustomrdpproperty.png)

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>Adicionar ou editar várias propriedades personalizadas de RDP

Para adicionar ou editar várias propriedades personalizadas de RDP, execute os seguintes cmdlets PowerShell, fornecendo as propriedades de RDP personalizadas como uma cadeia separada pelo ponto-ponto:

```powershell
$properties="<property1>;<property2>;<property3>"
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty $properties
```

![Uma imagem de PowerShell cmdlet Get-RDSRemoteApp com nome e nome amigável destacados.](../media/multiplecustomrdpproperty.png)

## <a name="reset-all-custom-rdp-properties"></a>Redefinir todas as propriedades personalizadas de RDP

Pode redefinir propriedades de RDP personalizadas individuais para os seus valores padrão seguindo as instruções em [Adicionar ou editar uma única propriedade RDP personalizada,](#add-or-edit-a-single-custom-rdp-property)ou pode redefinir todas as propriedades de RDP personalizadas para uma piscina hospedeira executando o seguinte cmdlet PowerShell:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty ""
```

![Uma imagem de PowerShell cmdlet Get-RDSRemoteApp com nome e nome amigável destacados.](../media/resetcustomrdpproperty.png)

## <a name="next-steps"></a>Passos seguintes

Agora que personalizou as propriedades rdp para uma determinada piscina de anfitriões, pode iniciar sessão com um cliente do Windows Virtual Desktop para testá-las como parte de uma sessão de utilizador. Estes próximos dois How-tos dir-lhe-ão como se conectar a uma sessão usando o cliente da sua escolha:

- [Ligar ao cliente de Ambiente de Trabalho do Windows](../connect-windows-7-and-10.md)
- [Ligar com o cliente web](connect-web-2019.md)
