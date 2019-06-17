---
title: PowerShell-área de Trabalho Virtual de Windows - Azure
description: Como resolver problemas com o PowerShell, quando configurar um ambiente de inquilino de área de Trabalho Virtual do Windows.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshoot
ms.date: 04/08/2019
ms.author: v-chjenk
ms.openlocfilehash: ad32f7ff883812830dbcf2ed900c4034bd90abfc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64927511"
---
# <a name="windows-virtual-desktop-powershell"></a>Windows Virtual Desktop PowerShell

Utilize este artigo para resolver erros e problemas ao utilizar o PowerShell com a área de Trabalho Virtual do Windows. Para obter mais informações sobre o PowerShell de serviços de ambiente de trabalho remoto, consulte [Windows Powershell de ambiente de Trabalho Virtual](https://docs.microsoft.com/powershell/module/windowsvirtualdesktop/).

## <a name="provide-feedback"></a>Enviar comentários

Estamos atualmente não estão a demorar incidentes de suporte, enquanto a área de Trabalho Virtual do Windows está em pré-visualização. Visite o [Comunidade tecnológica da área de Trabalho Virtual do Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) para discutir o serviço de área de Trabalho Virtual do Windows com a equipe do produto e membros da Comunidade de Active Directory.

## <a name="powershell-commands-used-during-windows-virtual-desktop-setup"></a>Comandos do PowerShell utilizados durante a configuração de área de Trabalho Virtual do Windows

Esta secção lista os comandos do PowerShell que são normalmente usados ao configurar a área de Trabalho Virtual do Windows e fornece formas de resolver os problemas que possam ocorrer ao mesmo tempo a utilizá-los.

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-is-already-assigned-to-a-remoteapp-app-group-in-the-specified-host-pool"></a>Erro: Comando RdsAppGroupUser adicionar – o UserPrincipalName especificado já está atribuído a um grupo de aplicações do RemoteApp no conjunto de anfitrião especificado

```Powershell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName 'Desktop Application Group' -UserPrincipalName <UserName>
```

**Causa:** O nome de utilizador utilizada foi já atribuído a um grupo de aplicação de um tipo diferente. Os utilizadores não não possível atribuir a ambos os um grupo de aplicação de ambiente de trabalho e remoto remoto sob o mesmo pool de host de sessão.

**CORREÇÃO:** Se o utilizador tem de aplicativos remotos e o ambiente de trabalho remoto, criar conjuntos de outro anfitrião ou conceder acesso de utilizador para o ambiente de trabalho remoto, o que irá permitir a utilização de qualquer aplicativo sobre o anfitrião de sessões de VM.

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-doesnt-exist-in-the-azure-active-directory-associated-with-the-remote-desktop-tenant"></a>Erro: Comando RdsAppGroupUser adicionar – o UserPrincipalName especificado não existe no Azure Active Directory associado ao inquilino de ambiente de trabalho remoto

```PowerShell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName “Desktop Application Group” -UserPrincipalName <UserPrincipalName>
```

**Causa:** Não é possível localizar o utilizador especificado pelo - UserPrincipalName no Azure Active Directory associado ao inquilino de área de Trabalho Virtual do Windows.

**CORREÇÃO:** Certifique-se os itens na lista seguinte.

- O utilizador está sincronizado com o Azure Active Directory.
- O utilizador não está vinculado a empresa-consumidor (B2C) ou commerce do empresa-empresa (B2B).
- O inquilino de área de Trabalho Virtual do Windows está associado ao Azure Active Directory correto.

### <a name="error-get-rdsdiagnosticactivities----user-isnt-authorized-to-query-the-management-service"></a>Erro: Get-RdsDiagnosticActivities – O utilizador não está autorizado para consultar o serviço de gestão

```PowerShell
Get-RdsDiagnosticActivities -ActivityId <ActivityId>
```

**Causa:** - TenantName parâmetro

**CORREÇÃO:** Emitir Get-RdsDiagnosticActivities com - TenantName <TenantName>.

### <a name="error-get-rdsdiagnosticactivities----the-user-isnt-authorized-to-query-the-management-service"></a>Erro: Get-RdsDiagnosticActivities – o utilizador não está autorizado para consultar o serviço de gestão

```PowerShell
Get-RdsDiagnosticActivities -Deployment -username <username>
```

**Causa:** Usando - opção de implantação.

**CORREÇÃO:** -opção de implantação pode ser utilizado apenas por administradores de implementação. Estes administradores normalmente são membros da Equipe do ambiente de trabalho serviços/Windows Virtual ambiente de trabalho remoto. Substitua a - opção de implantação com - TenantName <TenantName>.

### <a name="error-new-rdsroleassignment----the-user-isnt-authorized-to-query-the-management-service"></a>Erro: Novo-RdsRoleAssignment – o utilizador não está autorizado para consultar o serviço de gestão

**Fazer com que 1:** A conta que está a ser utilizada não tem permissões de proprietário de serviços de ambiente de trabalho remoto no inquilino.

**Corrigi 1:** Um utilizador com permissões de proprietário do Remote Desktop Services tem de executar a atribuição de função.

**Causa 2:** A conta que está a ser utilizada tem permissões de proprietário de serviços de ambiente de trabalho remoto, mas não faz parte do Azure Active Directory do inquilino ou não tem permissões para consultar o Azure Active Directory onde o utilizador está localizado.

**Corrigi 2:** Um utilizador com permissões do Active Directory tem de executar a atribuição de função.

>[!Note]
>Novo RdsRoleAssignment não é possível atribuir permissões a um utilizador que não existe no Azure Active Directory (AD).

## <a name="next-steps"></a>Passos Seguintes

- Para uma descrição geral na área de Trabalho Virtual do Windows e as faixas de escalonamento de resolução de problemas, consulte [descrição geral, comentários e suporte de resolução de problemas](troubleshoot-set-up-overview.md).
- Para resolver problemas ao criar um conjunto de inquilino e o anfitrião num ambiente de área de Trabalho Virtual do Windows, consulte [inquilino e o host a criação do agrupamento](troubleshoot-set-up-issues.md).
- Para resolver problemas ao configurar uma máquina virtual (VM) na área de Trabalho Virtual do Windows, consulte [configuração de máquina virtual do anfitrião de sessão](troubleshoot-vm-configuration.md).
- Para resolver problemas com ligações de cliente de área de Trabalho Virtual do Windows, consulte [ligações de cliente de ambiente de trabalho remoto](troubleshoot-client-connection.md).
- Para saber mais sobre o serviço de pré-visualização, veja [ambiente de pré-visualização do Windows Desktop](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
- Para seguir um tutorial de resolução de problemas, consulte [Tutorial: Resolver problemas de implementações de modelo do Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot).
- Para saber mais sobre a auditoria de ações, veja [auditar operações com o Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Para saber mais sobre as ações para determinar os erros durante a implementação, veja [ver as operações de implementação](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).