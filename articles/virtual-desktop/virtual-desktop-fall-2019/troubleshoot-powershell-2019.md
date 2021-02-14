---
title: Windows Virtual Desktop (clássico) PowerShell - Azure
description: Como resolver problemas com o PowerShell quando configurar um ambiente de inquilino virtual do Windows Desktop (clássico).
author: Heidilohr
ms.topic: troubleshooting
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 08c9f7a6c5f8fd4a51c464018438bf6e7db119fb
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100374903"
---
# <a name="windows-virtual-desktop-classic-powershell"></a>Windows Virtual Desktop (clássico) PowerShell

>[!IMPORTANT]
>Este conteúdo aplica-se ao Windows Virtual Desktop (clássico), que não suporta objetos de desktop virtual do Gestor de Recursos Azure Windows. Se estiver a tentar gerir os objetos de ambiente de trabalho virtuais do Azure Resource Manager Windows, consulte [este artigo](../troubleshoot-powershell.md).

Utilize este artigo para resolver erros e problemas ao utilizar o PowerShell com o Windows Virtual Desktop. Para obter mais informações sobre o PowerShell dos Serviços de Ambiente de Trabalho Remoto, consulte [o Windows Virtual Desktop Powershell](/powershell/windows-virtual-desktop/overview).

## <a name="provide-feedback"></a>Enviar comentários

Visite a [Comunidade Virtual desktop tech do Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) para discutir o serviço de desktop virtual do Windows com a equipa de produtos e membros da comunidade ativa.

## <a name="powershell-commands-used-during-windows-virtual-desktop-setup"></a>Comandos PowerShell utilizados durante a configuração do Windows Virtual Desktop

Esta secção lista os comandos PowerShell que são normalmente utilizados durante a configuração do Windows Virtual Desktop e fornece formas de resolver problemas que podem ocorrer durante a sua utilização.

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-is-already-assigned-to-a-remoteapp-app-group-in-the-specified-host-pool"></a>Erro: Add-RdsAppGroupUser comando -- O Nome Do Utilizador Especificado já está atribuído a um grupo de aplicações RemoteApp no Pool host especificado

```Powershell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName 'Desktop Application Group' -UserPrincipalName <UserName>
```

**Causa:** O nome de utilizador utilizado já foi atribuído a um grupo de aplicações de um tipo diferente. Os utilizadores não podem ser designados para um ambiente de trabalho remoto e um grupo de aplicações remotos no mesmo pool de anfitriões de sessão.

**Correção:** Se o utilizador precisar de aplicações remotas e de desktop remoto, crie diferentes piscinas de anfitriões ou conceda o acesso do utilizador ao ambiente de trabalho remoto, o que permitirá a utilização de qualquer aplicação no VM do anfitrião da sessão.

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-doesnt-exist-in-the-azure-active-directory-associated-with-the-remote-desktop-tenant"></a>Erro: Add-RdsAppGroupUser comando -- O Nome Do Utilizador Especificado não existe no Diretório Ativo Azure associado ao inquilino de Ambiente de Trabalho Remoto

```PowerShell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName "Desktop Application Group" -UserPrincipalName <UserPrincipalName>
```

**Causa:** O utilizador especificado pelo -UserPrincipalName não pode ser encontrado no Diretório Ativo Azure ligado ao inquilino virtual do Windows Desktop.

**Correção:** Confirme os itens na lista a seguir.

- O utilizador está sincronizado com o Azure Ative Directory.
- O utilizador não está ligado ao comércio de negócios (B2C) ou de negócios-a-negócios (B2B).
- O inquilino virtual do Windows desktop está ligado à correção do Azure Ative Directory.

### <a name="error-get-rdsdiagnosticactivities----user-isnt-authorized-to-query-the-management-service"></a>Erro: Get-RdsDiagnosticActivities -- O utilizador não está autorizado a consultar o serviço de gestão

```PowerShell
Get-RdsDiagnosticActivities -ActivityId <ActivityId>
```

**Causa:** -Parâmetro de Nome do Inquilino

**Correção:** Emissão Get-RdsDiagnosticActivities com -TenantName. \<TenantName>

### <a name="error-get-rdsdiagnosticactivities----the-user-isnt-authorized-to-query-the-management-service"></a>Erro: Get-RdsDiagnosticActivities -- o utilizador não está autorizado a consultar o serviço de gestão

```PowerShell
Get-RdsDiagnosticActivities -Deployment -username <username>
```

**Causa:** Utilização do interruptor de implantação.

**Correção:** -O interruptor de implantação só pode ser utilizado por administradores de implantação. Estes administradores são geralmente membros da equipa remote desktop Services/Windows Virtual Desktop. Substitua o interruptor de implantação por -TenantName \<TenantName> .

### <a name="error-new-rdsroleassignment----the-user-isnt-authorized-to-query-the-management-service"></a>Erro: New-RdsRoleAssignment -- o utilizador não está autorizado a consultar o serviço de gestão

**Causa 1:** A conta que está a ser utilizada não tem permissões do Proprietário de Serviços de Secretária Remota no inquilino.

**Correção 1:** Um utilizador com permissões de proprietário de Serviços de Ambiente de Trabalho remoto precisa executar a atribuição de funções.

**Causa 2:** A conta que está a ser utilizada tem permissões de proprietário de Serviços de Secretária Remotas, mas não faz parte do Diretório Ativo Azure do arrendatário ou não tem permissões para consultar o Diretório Ativo Azure onde o utilizador está localizado.

**Correção 2:** Um utilizador com permissões de Ative Directory precisa de executar a atribuição de funções.

>[!Note]
>New-RdsRoleAssignment não pode dar permissões a um utilizador que não exista no Diretório Ativo Azure (AD).

## <a name="next-steps"></a>Passos seguintes

- Para obter uma visão geral sobre a resolução de problemas do Windows Virtual Desktop e as faixas de escalada, consulte [a visão geral da resolução de problemas, o feedback e o suporte](troubleshoot-set-up-overview-2019.md).
- Para resolver problemas enquanto cria um inquilino e piscina de hospedagem em um ambiente de desktop virtual windows, consulte [o Inquilino e a criação de piscina de anfitrião.](troubleshoot-set-up-issues-2019.md)
- Para resolver problemas enquanto configura uma máquina virtual (VM) no Windows Virtual Desktop, consulte a [configuração da máquina virtual do anfitrião da Sessão](troubleshoot-vm-configuration-2019.md).
- Para resolver problemas com as ligações do cliente virtual do Windows Desktop, consulte [as ligações do serviço de desktop virtual do Windows](troubleshoot-service-connection-2019.md).
- Para resolver problemas com clientes de ambiente de trabalho remoto, consulte [Troubleshoot o cliente Remote Desktop](../troubleshoot-client.md)
- Para saber mais sobre o serviço, consulte o [ambiente de ambiente de trabalho virtual do Windows.](environment-setup-2019.md)
- Para passar por um tutorial de resolução de [problemas, consulte Tutorial: Implementações de modelos do Gestor de Recursos de Resolução de Problemas](../../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Para conhecer as ações de auditoria, consulte [as operações de Auditoria com o Gestor de Recursos.](../../azure-resource-manager/management/view-activity-logs.md)
- Para obter ações para determinar os erros durante a implementação, consulte [as operações de implantação](../../azure-resource-manager/templates/deployment-history.md)da visualização .
