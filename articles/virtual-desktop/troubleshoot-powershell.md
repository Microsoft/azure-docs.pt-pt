---
title: Windows Virtual Desktop PowerShell - Azure
description: Como resolver problemas com a PowerShell quando configura um ambiente de ambiente de trabalho virtual windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: ce19c670df5062a11bf86e9c383a322f9033818d
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612015"
---
# <a name="windows-virtual-desktop-powershell"></a>Windows Virtual Desktop PowerShell

>[!IMPORTANT]
>Este conteúdo aplica-se à atualização da primavera de 2020 com os objetos de ambiente de trabalho virtual do Gestor de Recursos Do Azure Windows. Se estiver a utilizar o lançamento do Windows Virtual Desktop Fall 2019 sem objetos do Gestor de Recursos Azure, consulte [este artigo](./virtual-desktop-fall-2019/troubleshoot-powershell-2019.md).
>
> A atualização Do Windows Virtual Desktop Spring 2020 encontra-se atualmente em pré-visualização pública. Esta versão de pré-visualização é fornecida sem um acordo de nível de serviço, e não recomendamos usá-la para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. 
> Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

Utilize este artigo para resolver erros e problemas ao utilizar o PowerShell com o Windows Virtual Desktop. Para obter mais informações sobre os serviços de ambiente de trabalho remotoPowerShell, consulte [o Windows Virtual Desktop PowerShell](/powershell/module/windowsvirtualdesktop/).

## <a name="provide-feedback"></a>Enviar comentários

Visite o [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) para discutir o serviço de desktop virtual windows com a equipa de produtos e membros ativos da comunidade.

## <a name="powershell-commands-used-during-windows-virtual-desktop-setup"></a>Comandos PowerShell utilizados durante a configuração do Ambiente de Trabalho Virtual do Windows

Esta secção lista os comandos PowerShell que são normalmente utilizados durante a configuração do Windows Virtual Desktop e fornece formas de resolver problemas que podem ocorrer durante a sua utilização.

### <a name="error-new-azroleassignment-the-provided-information-does-not-map-to-an-ad-object-id"></a>Erro: New-AzRoleAssignment: As informações fornecidas não mapeiam para um ID de objeto ad

```powershell
AzRoleAssignment -SignInName "admins@contoso.com" -RoleDefinitionName "Desktop Virtualization User" -ResourceName "0301HP-DAG" -ResourceGroupName 0301RG -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups' 
```

**Causa:** O utilizador especificado pelo parâmetro *-SignInName* não pode ser encontrado no Diretório Ativo Azure ligado ao ambiente de ambiente de trabalho virtual do Windows. 

**Correção:** Certifique-se das seguintes coisas.

- O utilizador deve ser sincronizado com o Diretório Ativo Azure.
- O utilizador não deve estar ligado ao comércio entre empresas e consumidores (B2C) ou de negócios a empresas (B2B).
- O ambiente de ambiente de trabalho virtual do Windows deve estar ligado ao diretório Ativo Azure correto.

### <a name="error-new-azroleassignment-the-client-with-object-id-does-not-have-authorization-to-perform-action-over-scope-code-authorizationfailed"></a>Erro: New-AzRoleAssignment: "O cliente com id de objeto não tem autorização para realizar ação sobre o âmbito (código: AutorizaçãoFalhada)"

**Causa 1:** A conta que está a ser usada não tem permissões do Proprietário na subscrição. 

**Correção 1:** Um utilizador com permissões do Proprietário precisa executar a atribuição de funções. Em alternativa, o utilizador tem de ser atribuído à função de Administrador de Acesso ao Utilizador para atribuir um utilizador a um grupo de aplicações.

**Causa 2:** A conta que está a ser utilizada tem permissões do Proprietário, mas não faz parte do Diretório Ativo Azure do ambiente ou não tem permissões para consultar o Diretório Ativo Azure onde o utilizador está localizado.

**Correção 2:** Um utilizador com permissões de Diretório Ativo precisa executar a atribuição de funções.

### <a name="error-new-azwvdhostpool----the-location-is-not-available-for-resource-type"></a>Erro: New-AzWvdHostPool -- a localização não está disponível para o tipo de recursos

```powershell
New-AzWvdHostPool_CreateExpanded: The provided location 'southeastasia' is not available for resource type 'Microsoft.DesktopVirtualization/hostpools'. List of available regions for the resource type is 'eastus,eastus2,westus,westus2,northcentralus,southcentralus,westcentralus,centralus'. 
```

Causa: O Windows Virtual Desktop suporta selecionar a localização de piscinas de anfitriões, grupos de aplicações e espaços de trabalho para armazenar metadados de serviço em determinados locais. As suas opções estão restritas ao local onde esta funcionalidade está disponível. Este erro significa que a funcionalidade não está disponível no local que escolheu.

Correção: Na mensagem de erro, será publicada uma lista de regiões apoiadas. Utilize uma das regiões apoiadas.

### <a name="error-new-azwvdapplicationgroup-must-be-in-same-location-as-host-pool"></a>Erro: New-AzWvdApplicationGroup deve estar no mesmo local que o pool anfitrião

```powershell
New-AzWvdApplicationGroup_CreateExpanded: ActivityId: e5fe6c1d-5f2c-4db9-817d-e423b8b7d168 Error: ApplicationGroup must be in same location as associated HostPool
```

**Causa:** Há uma incompatibilidade de localização. Todas as piscinas de anfitriões, grupos de aplicações e espaços de trabalho têm uma localização para armazenar metadados de serviço. Quaisquer objetos que criem que estejam associados uns aos outros devem estar no mesmo local. Por exemplo, se um `eastus`pool de anfitriões estiver dentro `eastus`, então você também precisa criar os grupos de aplicação em . Se criar um espaço de trabalho para registar estes grupos `eastus` de aplicações, esse espaço de trabalho também precisa de estar.

**Correção:** Recupere a localização onde a piscina anfitriã foi criada e, em seguida, atribua o grupo de aplicações que está a criar para o mesmo local.

## <a name="next-steps"></a>Passos seguintes

- Para uma visão geral sobre a resolução de problemas do Windows Virtual Desktop e as faixas de escalada, consulte a [visão geral, feedback e suporte](troubleshoot-set-up-overview.md)de Resolução de Problemas.
- Para resolver problemas ao configurar o ambiente de ambiente de trabalho virtual do Windows e piscinas de hospeda, consulte ambiente e criação de [piscinas de hospedas.](troubleshoot-set-up-issues.md)
- Para resolver problemas ao configurar uma máquina virtual (VM) no Windows Virtual Desktop, consulte a [configuração virtual](troubleshoot-vm-configuration.md)do anfitrião da sessão .
- Para resolver problemas com as ligações do cliente do Windows Virtual Desktop, consulte [as ligações](troubleshoot-service-connection.md)de serviço do Windows Virtual Desktop .
- Para resolver problemas com clientes do Desktop Remoto, consulte [Troubleshoot o cliente Remote Desktop](troubleshoot-client.md)
- Para saber mais sobre o serviço, consulte o ambiente de ambiente de [trabalho virtual do Windows](environment-setup.md).
- Para conhecer as ações de auditoria, consulte operações de [auditoria com o Gestor de Recursos.](../azure-resource-manager/management/view-activity-logs.md)
- Para aprender sobre as ações para determinar os erros durante a implementação, consulte as operações de [implantação do View](../azure-resource-manager/templates/deployment-history.md).
