---
title: Como criar definições de política de configuração de convidados a partir da linha de base da Política de Grupo para Windows
description: Saiba como converter a Política de Grupo do Windows Server 2019 Base de Segurança numa definição de política.
ms.date: 08/17/2020
ms.topic: how-to
ms.openlocfilehash: 7f7e2af70efa6771d94d7ceaa14d1408175b1d12
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93348649"
---
# <a name="how-to-create-guest-configuration-policy-definitions-from-group-policy-baseline-for-windows"></a>Como criar definições de política de configuração de convidados a partir da linha de base da Política de Grupo para Windows

Antes de criar definições de política personalizadas, é uma boa ideia ler a informação conceptual geral na [Configuração de Convidados da Política Azure](../concepts/guest-configuration.md). Para aprender a criar definições de política de configuração personalizada de hóspedes para o Linux, consulte [como criar políticas de configuração de hóspedes para o Linux.](./guest-configuration-create-linux.md) Para saber sobre a criação de definições de política de configuração personalizada de hóspedes para windows, consulte [como criar políticas de configuração de hóspedes para windows](./guest-configuration-create.md).

Ao auditar o Windows, a Configuração de Convidado utiliza um módulo de recurso [Desired State Configuration](/powershell/scripting/dsc/overview/overview) (DSC) para criar o ficheiro de configuração. A configuração DSC define a condição em que o computador deverá estar. Se a avaliação da configuração não for **conforme,** a auditoria de efeito de *políticaIfNotExists* é desencadeada.
[A Azure Policy Guest Configuration](../concepts/guest-configuration.md) apenas audita as definições dentro de máquinas.

> [!IMPORTANT]
> A extensão de Configuração de Convidado é necessária para realizar auditorias nas máquinas virtuais do Azure. Para implementar a extensão em escala em todas as máquinas do Windows, atribua as seguintes definições de política:
> - [Implementar pré-requisitos para ativar a política de configuração do hóspede em VMs windows.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ecd903d-91e7-4726-83d3-a229d7f2e293)
> 
> Não utilize segredos ou informações confidenciais em pacotes de conteúdo personalizado.

A comunidade DSC publicou o [módulo BaselineManagement](https://github.com/microsoft/BaselineManagement) para converter modelos de Política de Grupo exportados para formato DSC. Juntamente com o cmdlet GuestConfiguration, o módulo BaselineManagement cria o pacote de configuração de convidados Azure Policy para Windows a partir de conteúdo de Política de Grupo. Para obter mais informações sobre a utilização do módulo BaselineManagement, consulte o artigo [Quickstart: Converta a Política de Grupo em DSC](/powershell/scripting/dsc/quickstarts/gpo-quickstart).

Neste guia, percorremos o processo para criar um pacote de configuração de convidados Azure Policy a partir de um Objeto de Política de Grupo (GPO). Enquanto o walkthrough descreve a conversão do Windows Server 2019 Security Baseline, o mesmo processo pode ser aplicado a outros GPOs.  

## <a name="download-windows-server-2019-security-baseline-and-install-related-powershell-modules"></a>Descarregue o Windows Server 2019 Security Baseline e instale módulos Relacionados PowerShell

Para instalar o **DSC,** **GuestConfiguration,** **Baseline Management,** e módulos Azure relacionados em PowerShell:

1. A partir de um pedido PowerShell, executar o seguinte comando:

   ```azurepowershell-interactive
   # Install the BaselineManagement module, Guest Configuration DSC resource module, and relevant Azure modules from PowerShell Gallery
   Install-Module az.resources, az.policyinsights, az.storage, guestconfiguration, gpregistrypolicyparser, securitypolicydsc, auditpolicydsc, baselinemanagement -scope currentuser -Repository psgallery -AllowClobber
   ```

1. Crie um diretório para e descarregue a Linha de Segurança do Windows Server 2019 a partir do kit de ferramentas De conformidade com segurança do Windows.

   ```azurepowershell-interactive
   # Download the 2019 Baseline files from https://docs.microsoft.com/windows/security/threat-protection/security-compliance-toolkit-10
   New-Item -Path 'C:\git\policyfiles\downloads' -Type Directory
   Invoke-WebRequest -Uri 'https://download.microsoft.com/download/8/5/C/85C25433-A1B0-4FFA-9429-7E023E7DA8D8/Windows%2010%20Version%201909%20and%20Windows%20Server%20Version%201909%20Security%20Baseline.zip' -Out C:\git\policyfiles\downloads\Server2019Baseline.zip
   ```

1. Desbloqueie e expanda o Servidor 2019 Base descarregado.

   ```azurepowershell-interactive
   Unblock-File C:\git\policyfiles\downloads\Server2019Baseline.zip
   Expand-Archive -Path C:\git\policyfiles\downloads\Server2019Baseline.zip -DestinationPath C:\git\policyfiles\downloads\
   ```

1. Validar os conteúdos de base do Servidor 2019 utilizando **MapGuidsToGpoNames.ps1**.

   ```azurepowershell-interactive
   # Show content details of downloaded GPOs
   C:\git\policyfiles\downloads\Scripts\Tools\MapGuidsToGpoNames.ps1 -rootdir C:\git\policyfiles\downloads\GPOs\ -Verbose
   ```

## <a name="convert-from-group-policy-to-azure-policy-guest-configuration"></a>Converter da Política de Grupo para Azure Policy Guest Configuration

Em seguida, convertemos o Base de Base do Servidor 2019 descarregado num Pacote de Configuração de Convidados utilizando os módulos de Configuração de Convidados e Gestão de Bases.

1. Converter a Política de Grupo para a Configuração do Estado Desejada utilizando o Módulo de Gestão de Linha de Base.

   ```azurepowershell-interactive
   ConvertFrom-GPO -Path 'C:\git\policyfiles\downloads\GPOs\{3657C7A2-3FF3-4C21-9439-8FDF549F1D68}\' -OutputPath 'C:\git\policyfiles\' -OutputConfigurationScript -Verbose
   ```

1. Mude o nome, reforme e execute os scripts convertidos antes de criar um pacote de conteúdo de política.

   ```azurepowershell-interactive
   Rename-Item -Path C:\git\policyfiles\DSCFromGPO.ps1 -NewName C:\git\policyfiles\Server2019Baseline.ps1
   (Get-Content -Path C:\git\policyfiles\Server2019Baseline.ps1).Replace('DSCFromGPO', 'Server2019Baseline') | Set-Content -Path C:\git\policyfiles\Server2019Baseline.ps1
   (Get-Content -Path C:\git\policyfiles\Server2019Baseline.ps1).Replace('PSDesiredStateConfiguration', 'PSDscResources') | Set-Content -Path C:\git\policyfiles\Server2019Baseline.ps1
   C:\git\policyfiles\Server2019Baseline.ps1
   ```

1. Crie um pacote de conteúdo sonoro para a Configuração do Hóspede da Política Azure.

   ```azurepowershell-interactive
   New-GuestConfigurationPackage -Name Server2019Baseline -Configuration c:\git\policyfiles\localhost.mof -Verbose
   ```

## <a name="create-azure-policy-guest-configuration"></a>Criar configuração de convidados de política azul

1. O próximo passo é publicar o ficheiro no Azure Blob Storage. O comando `Publish-GuestConfigurationPackage` requer o `Az.Storage` módulo.

   ```azurepowershell-interactive
   Publish-GuestConfigurationPackage -Path ./AuditBitlocker.zip -ResourceGroupName  myResourceGroupName -StorageAccountName myStorageAccountName
   ```

1. Uma vez criado e carregado um pacote de política personalizada de Configuração de Convidados, crie a definição de política de Configuração de Convidados. Utilize o `New-GuestConfigurationPolicy` cmdlet para criar a Configuração do Convidado.

   ```azurepowershell-interactive
    $NewGuestConfigurationPolicySplat = @{
        ContentUri = $Uri 
        DisplayName = 'Server 2019 Configuration Baseline' 
        Description 'Validation of using a completely custom baseline configuration for Windows VMs' 
        Path = 'C:\git\policyfiles\policy'  
        Platform = Windows 
        }
   New-GuestConfigurationPolicy @NewGuestConfigurationPolicySplat
   ```
    
1. Publique as definições de política utilizando o `Publish-GuestConfigurationPolicy` cmdlet. O cmdlet tem apenas o parâmetro **Path** que aponta para a localização dos ficheiros JSON criados por `New-GuestConfigurationPolicy` . Para executar o comando Publicar, precisa de ter acesso para criar definições de política em Azure. Os requisitos específicos de autorização estão documentados na página [Azure Policy Overview.](../overview.md#getting-started) O melhor papel incorporado é o **Contribuinte para a Política de Recursos.**

   ```azurepowershell-interactive
   Publish-GuestConfigurationPolicy -Path C:\git\policyfiles\policy\ -Verbose
   ```

## <a name="assign-guest-configuration-policy-definition"></a>Atribuir definição de política de configuração de hóspedes

Com a política criada em Azure, o último passo é atribuir a iniciativa. Veja como atribuir a iniciativa ao [Portal,](../assign-policy-portal.md) [Azure CLI](../assign-policy-azurecli.md)e [Azure PowerShell.](../assign-policy-powershell.md)

> [!IMPORTANT]
> As definições de política de configuração de hóspedes devem ser **sempre** atribuídas utilizando a iniciativa que combina as políticas _auditIfNotExists_ e _DeployIfNotExists._ Se apenas a política _auditIfNotExists_ for atribuída, os pré-requisitos não são implementados e a política mostra sempre que os servidores '0' estão em conformidade.

Atribuir uma definição de política com o efeito _DeployIfNotExists_ requer um nível de acesso adicional. Para conceder o menor privilégio, pode criar uma definição de papel personalizada que alarga o **Contribuinte para a Política de Recursos.** O exemplo abaixo cria uma função chamada **Resource Policy Contributor DINE** com a permissão adicional _Microsoft.Authorization/roleAssignments/write_.

   ```azurepowershell-interactive
   $subscriptionid = '00000000-0000-0000-0000-000000000000'
   $role = Get-AzRoleDefinition "Resource Policy Contributor"
   $role.Id = $null
   $role.Name = "Resource Policy Contributor DINE"
   $role.Description = "Can assign Policies that require remediation."
   $role.Actions.Clear()
   $role.Actions.Add("Microsoft.Authorization/roleAssignments/write")
   $role.AssignableScopes.Clear()
   $role.AssignableScopes.Add("/subscriptions/$subscriptionid")
   New-AzRoleDefinition -Role $role
   ```

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre a auditoria de VMs com [configuração de hóspedes.](../concepts/guest-configuration.md)
- Entenda como [criar políticas programáticas.](./programmatically-create.md)
- Saiba como [obter dados de conformidade.](./get-compliance-data.md)
