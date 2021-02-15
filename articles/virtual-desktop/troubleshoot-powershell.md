---
title: Windows Virtual Desktop PowerShell - Azure
description: Como resolver problemas com o PowerShell quando configurar um ambiente de ambiente de trabalho virtual do Windows.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 06/05/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: cd02ee8ab794858566aeafa96fa78919be3b85a5
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100367627"
---
# <a name="windows-virtual-desktop-powershell"></a>Windows Virtual Desktop PowerShell

>[!IMPORTANT]
>Este conteúdo aplica-se ao Windows Virtual Desktop com objetos de ambiente de trabalho virtuais do Windows, gestor de recursos do Azure. Se estiver a utilizar o Windows Virtual Desktop (clássico) sem objetos Azure Resource Manager, consulte [este artigo](./virtual-desktop-fall-2019/troubleshoot-powershell-2019.md).

Utilize este artigo para resolver erros e problemas ao utilizar o PowerShell com o Windows Virtual Desktop. Para obter mais informações sobre o PowerShell dos Serviços de Ambiente de Trabalho Remoto, consulte [o Windows Virtual Desktop PowerShell](/powershell/windows-virtual-desktop/overview).

## <a name="provide-feedback"></a>Enviar comentários

Visite a [Comunidade Virtual desktop tech do Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) para discutir o serviço de desktop virtual do Windows com a equipa de produtos e membros da comunidade ativa.

## <a name="powershell-commands-used-during-windows-virtual-desktop-setup"></a>Comandos PowerShell utilizados durante a configuração do Windows Virtual Desktop

Esta secção lista os comandos PowerShell que são normalmente utilizados durante a configuração do Windows Virtual Desktop e fornece formas de resolver problemas que podem ocorrer durante a sua utilização.

### <a name="error-new-azroleassignment-the-provided-information-does-not-map-to-an-ad-object-id"></a>Erro: Designação de Novas AzRole: As informações fornecidas não mapeiam para um ID de objeto AD

```powershell
New-AzRoleAssignment -SignInName "admins@contoso.com" -RoleDefinitionName "Desktop Virtualization User" -ResourceName "0301HP-DAG" -ResourceGroupName 0301RG -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

**Causa:** O utilizador especificado pelo parâmetro *-SignInName* não pode ser encontrado no Diretório Ativo Azure ligado ao ambiente de ambiente de trabalho virtual do Windows.

**Correção:** Certifique-se das seguintes coisas.

- O utilizador deve ser sincronizado com o Azure Ative Directory.
- O utilizador não deve estar ligado ao comércio entre empresas e consumidores (B2C) ou a empresas (B2B).
- O ambiente de ambiente de trabalho virtual do Windows deve ser ligado à correção do Azure Ative Directory.

### <a name="error-new-azroleassignment-the-client-with-object-id-does-not-have-authorization-to-perform-action-over-scope-code-authorizationfailed"></a>Erro: New-AzRoleAssignment: "O cliente com id de objeto não tem autorização para realizar ação sobre âmbito (código: AutorizaçãoFailed)"

**Causa 1:** A conta que está a ser utilizada não tem permissões do Proprietário na subscrição.

**Correção 1:** Um utilizador com permissões do Proprietário precisa de executar a atribuição de funções. Em alternativa, o utilizador precisa de ser atribuído à função de Administrador de Acesso ao Utilizador para atribuir um utilizador a um grupo de aplicações.

**Causa 2:** A conta que está a ser utilizada tem permissões do Proprietário, mas não faz parte do Diretório Ativo Azure do ambiente ou não tem permissões para consultar o Diretório Ativo Azure onde o utilizador está localizado.

**Correção 2:** Um utilizador com permissões de Ative Directory precisa de executar a atribuição de funções.

### <a name="error-new-azwvdhostpool----the-location-is-not-available-for-resource-type"></a>Erro: New-AzWvdHostPool -- a localização não está disponível para o tipo de recurso

```powershell
New-AzWvdHostPool_CreateExpanded: The provided location 'southeastasia' is not available for resource type 'Microsoft.DesktopVirtualization/hostpools'. List of available regions for the resource type is 'eastus,eastus2,westus,westus2,northcentralus,southcentralus,westcentralus,centralus'.
```

Causa: O Windows Virtual Desktop suporta selecionar a localização de piscinas de anfitriões, grupos de aplicações e espaços de trabalho para armazenar metadados de serviço em determinados locais. As suas opções estão restritas ao local onde esta funcionalidade está disponível. Este erro significa que a funcionalidade não está disponível no local que escolheu.

Correção: Na mensagem de erro, será publicada uma lista de regiões apoiadas. Utilize uma das regiões apoiadas.

### <a name="error-new-azwvdapplicationgroup-must-be-in-same-location-as-host-pool"></a>Erro: New-AzWvdApplicationGroup deve estar no mesmo local que a piscina anfitriã

```powershell
New-AzWvdApplicationGroup_CreateExpanded: ActivityId: e5fe6c1d-5f2c-4db9-817d-e423b8b7d168 Error: ApplicationGroup must be in same location as associated HostPool
```

**Causa:** Há uma incompatibilidade de localização. Todas as piscinas hospedeiras, grupos de aplicações e espaços de trabalho têm uma localização para armazenar metadados de serviço. Quaisquer objetos que criem associados entre si devem estar no mesmo local. Por exemplo, se uma piscina de anfitriões estiver em `eastus` , então também precisa criar os grupos de aplicação em `eastus` . Se criar um espaço de trabalho para registar estes grupos de aplicação, esse espaço de trabalho também precisa de `eastus` estar.

**Correção:** Recupere a localização em que a piscina de anfitrião foi criada e, em seguida, atribua o grupo de aplicação que está a criar para o mesmo local.

## <a name="next-steps"></a>Passos seguintes

- Para obter uma visão geral sobre a resolução de problemas do Windows Virtual Desktop e as faixas de escalada, consulte [a visão geral da resolução de problemas, o feedback e o suporte](troubleshoot-set-up-overview.md).
- Para resolver problemas durante a configuração do ambiente de ambiente de trabalho virtual do Windows e das piscinas de anfitrião, consulte [ambiente e criação de piscina de anfitrião.](troubleshoot-set-up-issues.md)
- Para resolver problemas enquanto configura uma máquina virtual (VM) no Windows Virtual Desktop, consulte a [configuração da máquina virtual do anfitrião da Sessão](troubleshoot-vm-configuration.md).
- Para resolver problemas com as ligações do cliente virtual do Windows Desktop, consulte [as ligações do serviço de desktop virtual do Windows](troubleshoot-service-connection.md).
- Para resolver problemas com clientes de ambiente de trabalho remoto, consulte [Troubleshoot o cliente Remote Desktop](troubleshoot-client.md)
- Para saber mais sobre o serviço, consulte o [ambiente de ambiente de trabalho virtual do Windows.](environment-setup.md)
- Para conhecer as ações de auditoria, consulte [as operações de Auditoria com o Gestor de Recursos.](../azure-resource-manager/management/view-activity-logs.md)
- Para obter ações para determinar os erros durante a implementação, consulte [as operações de implantação](../azure-resource-manager/templates/deployment-history.md)da visualização .
