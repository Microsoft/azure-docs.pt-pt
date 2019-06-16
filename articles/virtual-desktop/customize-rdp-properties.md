---
title: Personalizar as propriedades RDP com o PowerShell - Azure
description: Como personalizar o RDP propriedades para o Windows área de Trabalho Virtual com cmdlets do PowerShell.
services: virtual-desktop
author: v-hevem
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 06/03/2019
ms.author: v-hevem
ms.openlocfilehash: 21d0b45b974f4bc806b26423b7deaef96e4bf350
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67082654"
---
# <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>Personalizar as propriedades de protocolo de ambiente de trabalho remoto para um conjunto de anfitrião

Personalizar propriedades de protocolo RDP (Remote Desktop) de um conjunto de anfitrião, como a experiência de vários monitor e redirecionamento de áudio, permite-lhe fornecer uma experiência ideal para os seus utilizadores com base nas suas necessidades. Pode personalizar as propriedades RDP na área de Trabalho Virtual do Windows utilizando o **- CustomRdpProperty** parâmetro na **conjunto RdsHostPool** cmdlet.

Ver [definições do ficheiro Remote Desktop RDP](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files) para obter uma lista completa de propriedades suportadas e seus valores padrão.

Primeiro, [transferir e importar o módulo do Windows PowerShell de ambiente de Trabalho Virtual](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) a utilizar na sua sessão do PowerShell, se ainda não o fez.

## <a name="add-or-edit-a-single-custom-rdp-property"></a>Adicionar ou editar uma única propriedade personalizada do RDP

Para adicionar ou editar uma única propriedade personalizada do RDP, execute o seguinte cmdlet do PowerShell:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty "<property>"
```
![Captura de ecrã do cmdlet do PowerShell Get-RDSRemoteApp com o nome e FriendlyName realçado.](media/singlecustomrdpproperty.png)

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>Adicionar ou editar várias propriedades personalizadas de RDP

Para adicionar ou editar várias propriedades personalizadas do RDP, execute os seguintes cmdlets do PowerShell, fornecendo as propriedades RDP personalizadas como uma cadeia de caracteres delimitada por vírgulas:

```powershell
$properties="<property1>;<property2>;<property3>"
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty $properties
```
![Captura de ecrã do cmdlet do PowerShell Get-RDSRemoteApp com o nome e FriendlyName realçado.](media/multiplecustomrdpproperty.png)

## <a name="reset-all-custom-rdp-properties"></a>Repor todas as propriedades personalizadas de RDP

Pode repor as propriedades personalizadas de RDP individuais para os valores predefinidos ao seguir as instruções em [adicionar ou editar uma única propriedade personalizada do RDP](#add-or-edit-a-single-custom-rdp-property), ou pode redefinir todas as propriedades personalizadas do RDP para um conjunto de host, executando o seguinte Cmdlet do PowerShell:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty ""
```
![Captura de ecrã do cmdlet do PowerShell Get-RDSRemoteApp com o nome e FriendlyName realçado.](media/resetcustomrdpproperty.png)

## <a name="next-steps"></a>Passos Seguintes

Agora que personalizou as propriedades RDP para um conjunto de determinado anfitrião, pode iniciar sessão cliente de área de Trabalho Virtual do Windows para testá-los como parte de uma sessão de utilizador. Para fazê-lo, avance para ligar ao Windows Virtual Desktop procedimentos:

- [Ligar a partir do Windows 10 e Windows 7](connect-windows-7-and-10.md)
- [Ligar a partir de um navegador da web](connect-web.md)
