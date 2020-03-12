---
title: Windows Virtual Desktop PowerShell - Azure
description: Como resolver problemas com a PowerShell quando configura um ambiente de inquilino sinuoso do Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 04/08/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 3fb5436c2b5c30c5336385792d0597bdcea2b538
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2020
ms.locfileid: "79127479"
---
# <a name="windows-virtual-desktop-powershell"></a>Windows Virtual Desktop PowerShell

Utilize este artigo para resolver erros e problemas ao utilizar o PowerShell com o Windows Virtual Desktop. Para obter mais informações sobre os serviços de ambiente de trabalho remotoPowerShell, consulte [o Windows Virtual Desktop Powershell](/powershell/module/windowsvirtualdesktop/).

## <a name="provide-feedback"></a>Enviar comentários

Visite o [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) para discutir o serviço de desktop virtual windows com a equipa de produtos e membros ativos da comunidade.

## <a name="powershell-commands-used-during-windows-virtual-desktop-setup"></a>Comandos PowerShell utilizados durante a configuração do Ambiente de Trabalho Virtual do Windows

Esta secção lista os comandos PowerShell que são normalmente utilizados durante a configuração do Windows Virtual Desktop e fornece formas de resolver problemas que podem ocorrer durante a sua utilização.

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-is-already-assigned-to-a-remoteapp-app-group-in-the-specified-host-pool"></a>Erro: Comando Add-RdsAppGroupUser -- O nome principal do utilizador especificado já está atribuído a um grupo de aplicações RemoteApp no conjunto de anfitriões especificado

```Powershell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName 'Desktop Application Group' -UserPrincipalName <UserName>
```

**Causa:** O nome de utilizador utilizado já foi atribuído a um grupo de aplicações de um tipo diferente. Os utilizadores não podem ser atribuídos a um grupo remoto de desktop e aplicações remotas na mesma piscina de anfitriões da sessão.

**Correção:** Se o utilizador precisar tanto de aplicações remotas como de ambiente de trabalho remoto, crie diferentes piscinas de anfitriões ou conceda ao utilizador acesso ao ambiente de trabalho remoto, o que permitirá a utilização de qualquer aplicação no VM anfitrião da sessão.

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-doesnt-exist-in-the-azure-active-directory-associated-with-the-remote-desktop-tenant"></a>Erro: Comando Add-RdsAppGroupUser -- O nome principal especificado do utilizador não existe no Diretório Ativo Azure associado ao inquilino do Ambiente de Trabalho Remoto

```PowerShell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName "Desktop Application Group" -UserPrincipalName <UserPrincipalName>
```

**Causa:** O utilizador especificado pelo -UserPrincipalName não pode ser encontrado no Diretório Ativo Azure ligado ao inquilino do Windows Virtual Desktop.

**Correção:** Confirme os itens na lista seguinte.

- O utilizador é sincronizado com o Diretório Ativo Azure.
- O utilizador não está ligado ao comércio de consumidores (B2C) ou business-to-business (B2B).
- O inquilino do Windows Virtual Desktop está ligado ao diretório Ativo Azure.

### <a name="error-get-rdsdiagnosticactivities----user-isnt-authorized-to-query-the-management-service"></a>Erro: Get-RdsDiagnosticActivities -- O utilizador não está autorizado a consultar o serviço de gestão

```PowerShell
Get-RdsDiagnosticActivities -ActivityId <ActivityId>
```

**Causa:** -Parâmetro de Nome do Inquilino

**Correção:** Emissão Get-RdsDiagnosticActivities com -TenantName \<TenantName>.

### <a name="error-get-rdsdiagnosticactivities----the-user-isnt-authorized-to-query-the-management-service"></a>Erro: Get-RdsDiagnosticActivities -- o utilizador não está autorizado a consultar o serviço de gestão

```PowerShell
Get-RdsDiagnosticActivities -Deployment -username <username>
```

**Causa:** Utilização - Interruptor de implantação.

**Correção:** -O interruptor de implantação só pode ser utilizado pelos administradores de implantação. Estes administradores são geralmente membros da equipa remote Desktop Services/Windows Virtual Desktop. Substitua o interruptor de implantação por -TenantName \<TenantName>.

### <a name="error-new-rdsroleassignment----the-user-isnt-authorized-to-query-the-management-service"></a>Erro: New-RdsRoleAssignment -- o utilizador não está autorizado a consultar o serviço de gestão

**Causa 1:** A conta que está a ser usada não tem permissões do proprietário dos Serviços de Ambiente de Trabalho Remoto no inquilino.

**Correção 1:** Um utilizador com permissões do proprietário do Remote Desktop Services precisa de executar a atribuição de funções.

**Causa 2:** A conta que está a ser utilizada tem permissões do proprietário dos Serviços de Ambiente de Trabalho Remoto, mas não faz parte do Diretório Ativo Azure do inquilino ou não tem permissões para consultar o Diretório Ativo azure onde o utilizador está localizado.

**Correção 2:** Um utilizador com permissões de Diretório Ativo precisa executar a atribuição de funções.

>[!Note]
>A New-RdsRoleAssignment não pode dar permissões a um utilizador que não existe no Diretório Ativo Azure (AD).

## <a name="next-steps"></a>Passos seguintes

- Para uma visão geral sobre a resolução de problemas do Windows Virtual Desktop e as faixas de escalada, consulte a [visão geral, feedback e suporte](troubleshoot-set-up-overview.md)de Resolução de Problemas.
- Para resolver problemas ao criar um inquilino e uma piscina de hospedas num ambiente de ambiente de trabalho virtual windows, consulte [tenant e host pool creation](troubleshoot-set-up-issues.md).
- Para resolver problemas ao configurar uma máquina virtual (VM) no Windows Virtual Desktop, consulte a [configuração virtual](troubleshoot-vm-configuration.md)do anfitrião da sessão .
- Para resolver problemas com as ligações do cliente do Windows Virtual Desktop, consulte [as ligações](troubleshoot-service-connection.md)de serviço do Windows Virtual Desktop .
- Para resolver problemas com clientes do Desktop Remoto, consulte [Troubleshoot o cliente Remote Desktop](troubleshoot-client.md)
- Para saber mais sobre o serviço, consulte o ambiente de ambiente de [trabalho virtual do Windows](environment-setup.md).
- Para passar por um tutorial de resolução de [problemas, consulte Tutorial: Implementações](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)do modelo de gestor de recursos de resolução de problemas .
- Para conhecer as ações de auditoria, consulte operações de [auditoria com o Gestor de Recursos.](../azure-resource-manager/management/view-activity-logs.md)
- Para aprender sobre as ações para determinar os erros durante a implementação, consulte as operações de [implantação do View](../azure-resource-manager/templates/deployment-history.md).
