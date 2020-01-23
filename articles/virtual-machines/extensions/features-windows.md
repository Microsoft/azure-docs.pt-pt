---
title: Recursos e extensões de VM do Azure para Windows
description: Saiba quais extensões estão disponíveis para máquinas virtuais do Azure, agrupadas pelo que elas fornecem ou aperfeiçoadas.
services: virtual-machines-windows
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 999d63ee-890e-432e-9391-25b3fc6cde28
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/30/2018
ms.author: akjosh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 69d08af9fd34728860343db3578f7283802f1611
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76544757"
---
# <a name="virtual-machine-extensions-and-features-for-windows"></a>Recursos e extensões de máquina virtual para Windows

As extensões de máquina virtual (VM) do Azure são pequenas aplicações que proporcionam tarefas de automação e configuração pós-implementação nas VMs do Azure. Por exemplo, se uma máquina virtual exigir a instalação de software, a proteção antivírus ou a execução de um script, poderá ser utilizada uma extensão de VM. As extensões de VM do Azure podem ser executadas na CLI do Azure, no PowerShell, nos modelos do Azure Resource Manager e no portal do Azure. As extensões podem ser agrupadas com uma nova implementação da VM ou executadas em qualquer sistema existente.

Este artigo fornece uma visão geral das extensões de VM, pré-requisitos para usar extensões de VM do Azure e orientação sobre como detectar, gerenciar e remover extensões de VM. Este artigo fornece informações generalizadas porque muitas extensões de VM estão disponíveis, cada uma com uma configuração potencialmente exclusiva. Detalhes específicos da extensão podem ser encontrados em cada documento específico para a extensão individual.

 

## <a name="use-cases-and-samples"></a>Casos de uso e amostras

Várias extensões de VM do Azure diferentes estão disponíveis, cada uma com um caso de uso específico. Alguns exemplos incluem:

- Aplique as configurações de estado desejado do PowerShell a uma VM com a extensão de DSC para Windows. Para obter mais informações, consulte [extensão de configuração de estado desejado do Azure](dsc-overview.md).
- Configure o monitoramento de uma VM com a extensão de VM do agente de Log Analytics. Para obter mais informações, consulte [conectar VMs do Azure a logs de Azure monitor](../../log-analytics/log-analytics-azure-vm-extension.md).
- Configure uma VM do Azure usando o chefe. Para obter mais informações, consulte [automatizando a implantação de VM do Azure com o chefe](../windows/chef-automation.md).
- Configure o monitoramento de sua infraestrutura do Azure com a extensão Datadog. Para obter mais informações, consulte o [blog do Datadog](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/).


Além das extensões específicas do processo, uma extensão de script personalizado está disponível para máquinas virtuais Windows e Linux. A extensão de script personalizado para o Windows permite que qualquer script do PowerShell seja executado em uma VM. Os scripts personalizados são úteis para criar implantações do Azure que exigem configuração além das ferramentas nativas do Azure que podem ser fornecidas. Para obter mais informações, consulte [extensão de script personalizado da VM do Windows](custom-script-windows.md).

## <a name="prerequisites"></a>Pré-requisitos

Para lidar com a extensão na VM, você precisa do agente do Windows do Azure instalado. Algumas extensões individuais têm pré-requisitos, como o acesso a recursos ou dependências.

### <a name="azure-vm-agent"></a>Agente da VM do Azure

O agente de VM do Azure gerencia as interações entre uma VM do Azure e o controlador de malha do Azure. O agente de VM é responsável por muitos aspectos funcionais da implantação e do gerenciamento de VMs do Azure, incluindo a execução de extensões de VM. O agente de VM do Azure é pré-instalado em imagens do Azure Marketplace e pode ser instalado manualmente em sistemas operacionais com suporte. O agente de VM do Azure para Windows é conhecido como agente convidado do Windows.

Para obter informações sobre sistemas operacionais e instruções de instalação com suporte, consulte [agente de máquina virtual do Azure](agent-windows.md).

#### <a name="supported-agent-versions"></a>Versões de agente com suporte

Para fornecer a melhor experiência possível, há versões mínimas do agente. Para obter mais informações, consulte [este artigo](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

#### <a name="supported-oses"></a>Sistemas operacionais com suporte

O agente convidado do Windows é executado em vários sistemas operacionais, no entanto, o Framework de extensões tem um limite para os SOS que são extensões. Para obter mais informações, consulte [este artigo](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems
).

Algumas extensões não têm suporte em todos os sistemas operacionais e podem emitir o *código de erro 51, ' sistema operacional sem suporte '* . Verifique a documentação de extensão individual para obter suporte.

#### <a name="network-access"></a>Acesso à rede

Os pacotes de extensão são baixados do repositório de extensões de armazenamento do Azure, e os carregamentos de status de extensão são postados no armazenamento do Azure. Se você usar a versão [com suporte](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) dos agentes, não será necessário permitir o acesso ao armazenamento do Azure na região da VM, pois pode usar o agente para redirecionar a comunicação para o controlador de malha do Azure para comunicações do agente (recurso HostGAPlugin por meio do canal privilegiado em [168.63.129.16](https://docs.microsoft.com/azure/virtual-network/what-is-ip-address-168-63-129-16)de IP privado). Se você estiver em uma versão sem suporte do agente, precisará permitir o acesso de saída ao armazenamento do Azure nessa região da VM.

> [!IMPORTANT]
> Se você tiver bloqueado o acesso ao *168.63.129.16* usando o firewall convidado ou com um proxy, as extensões falharão independentemente das anteriores. As portas 80, 443 e 32526 são necessárias.

Os agentes só podem ser usados para baixar pacotes de extensão e status de relatório. Por exemplo, se uma instalação de extensão precisar baixar um script do GitHub (script personalizado) ou precisar de acesso ao armazenamento do Azure (backup do Azure), as portas de grupo de segurança de rede/firewall adicionais precisarão ser abertas. Extensões diferentes têm requisitos diferentes, pois são aplicativos por conta própria. Para extensões que exigem acesso ao armazenamento ou Azure Active Directory do Azure, você pode permitir o acesso usando as [marcas do serviço NSG do Azure](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) para armazenamento ou AzureActiveDirectory.

O agente convidado do Windows não tem suporte de servidor proxy para redirecionar solicitações de tráfego do agente por meio do, o que significa que o agente convidado do Windows dependerá do seu proxy personalizado (se você tiver um) para acessar recursos na Internet ou no host por meio de IP 168.63.129.16.

## <a name="discover-vm-extensions"></a>Descobrir extensões de VM

Estão disponíveis muitas VMs diferentes para utilização com as VMs do Azure. Para ver uma lista completa, use [Get-AzVMExtensionImage](https://docs.microsoft.com/powershell/module/az.compute/get-azvmextensionimage). O exemplo a seguir lista todas as extensões disponíveis no local *westus* :

```powershell
Get-AzVmImagePublisher -Location "WestUS" | `
Get-AzVMExtensionImageType | `
Get-AzVMExtensionImage | Select Type, Version
```

## <a name="run-vm-extensions"></a>Executar extensões de VM

As extensões de VM do Azure são executadas em VMs existentes, o que é útil quando você precisa fazer alterações de configuração ou recuperar a conectividade em uma VM já implantada. As extensões de VM também podem ser agrupadas com Azure Resource Manager implantações de modelo. Usando extensões com modelos do Resource Manager, as VMs do Azure podem ser implantadas e configuradas sem intervenção pós-implantação.

Os métodos a seguir podem ser usados para executar uma extensão em uma VM existente.

### <a name="powershell"></a>PowerShell

Existem vários comandos do PowerShell para executar extensões individuais. Para ver uma lista, use [Get-Command](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/get-command) e Filter na *extensão*:

```powershell
Get-Command Set-Az*Extension* -Module Az.Compute
```

Isso fornece uma saída semelhante à seguinte:

```powershell
CommandType     Name                                          Version    Source
-----------     ----                                          -------    ------
Cmdlet          Set-AzVMAccessExtension                       4.5.0      Az.Compute
Cmdlet          Set-AzVMADDomainExtension                     4.5.0      Az.Compute
Cmdlet          Set-AzVMAEMExtension                          4.5.0      Az.Compute
Cmdlet          Set-AzVMBackupExtension                       4.5.0      Az.Compute
Cmdlet          Set-AzVMBginfoExtension                       4.5.0      Az.Compute
Cmdlet          Set-AzVMChefExtension                         4.5.0      Az.Compute
Cmdlet          Set-AzVMCustomScriptExtension                 4.5.0      Az.Compute
Cmdlet          Set-AzVMDiagnosticsExtension                  4.5.0      Az.Compute
Cmdlet          Set-AzVMDiskEncryptionExtension               4.5.0      Az.Compute
Cmdlet          Set-AzVMDscExtension                          4.5.0      Az.Compute
Cmdlet          Set-AzVMExtension                             4.5.0      Az.Compute
Cmdlet          Set-AzVMSqlServerExtension                    4.5.0      Az.Compute
Cmdlet          Set-AzVmssDiskEncryptionExtension             4.5.0      Az.Compute
```

O exemplo a seguir usa a extensão de script personalizado para baixar um script de um repositório GitHub na máquina virtual de destino e, em seguida, executar o script. Para obter mais informações sobre a extensão de script personalizado, consulte [visão geral da extensão de script personalizado](custom-script-windows.md).

```powershell
Set-AzVMCustomScriptExtension -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" -Name "myCustomScript" `
    -FileUri "https://raw.githubusercontent.com/neilpeterson/nepeters-azure-templates/master/windows-custom-script-simple/support-scripts/Create-File.ps1" `
    -Run "Create-File.ps1" -Location "West US"
```

No exemplo a seguir, a extensão de acesso à VM é usada para redefinir a senha administrativa de uma VM do Windows para uma senha temporária. Para obter mais informações sobre a extensão de acesso à VM, consulte [redefinir o serviço de área de trabalho remota em uma VM do Windows](../windows/reset-rdp.md). Depois de executar isso, você deve redefinir a senha no primeiro logon:

```powershell
$cred=Get-Credential

Set-AzVMAccessExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myVMAccess" `
    -Location WestUS -UserName $cred.GetNetworkCredential().Username `
    -Password $cred.GetNetworkCredential().Password -typeHandlerVersion "2.0"
```

O comando `Set-AzVMExtension` pode ser usado para iniciar qualquer extensão de VM. Para obter mais informações, consulte a [referência Set-AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension).


### <a name="azure-portal"></a>Portal do Azure

As extensões de VM podem ser aplicadas a uma VM existente por meio do portal do Azure. Selecione a VM no portal, escolha **extensões**e, em seguida, selecione **Adicionar**. Escolha a extensão desejada na lista de extensões disponíveis e siga as instruções no assistente.

O exemplo a seguir mostra a instalação da extensão Microsoft antimalware do portal do Azure:

![Instalar extensão Antimalware](./media/features-windows/installantimalwareextension.png)

### <a name="azure-resource-manager-templates"></a>Modelos do Azure Resource Manager

As extensões de VM podem ser adicionadas a um modelo de Azure Resource Manager e executadas com a implantação do modelo. Ao implantar uma extensão com um modelo, você pode criar implantações do Azure totalmente configuradas. Por exemplo, o JSON a seguir é obtido de um modelo do Resource Manager que implanta um conjunto de VMs com balanceamento de carga e um banco de dados SQL do Azure e instala um aplicativo .NET Core em cada VM. A extensão de VM cuida da instalação do software.

Para obter mais informações, consulte o [modelo completo do Resource Manager](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows).

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.9",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
    }
}
```

Para obter mais informações sobre como criar modelos do Resource Manager, consulte Criando [modelos de Azure Resource Manager com extensões de VM do Windows](../windows/template-description.md#extensions).

## <a name="secure-vm-extension-data"></a>Proteger dados de extensão de VM

Quando você executa uma extensão de VM, pode ser necessário incluir informações confidenciais, como credenciais, nomes de conta de armazenamento e chaves de acesso da conta de armazenamento. Muitas extensões de VM incluem uma configuração protegida que criptografa dados e apenas descriptografa-os dentro da VM de destino. Cada extensão tem um esquema de configuração protegida específico e cada uma é detalhada na documentação específica da extensão.

O exemplo a seguir mostra uma instância da extensão de script personalizado para o Windows. O comando a ser executado inclui um conjunto de credenciais. Neste exemplo, o comando a ser executado não é criptografado:

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.9",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ],
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
    }
}
```

Mover o **comando para executar** a propriedade para a configuração **protegida** protege a cadeia de caracteres de execução, conforme mostrado no exemplo a seguir:

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.9",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
    }
}
```

Em uma VM IaaS do Azure que usa extensões, no console certificados, você pode ver certificados que têm o assunto **_gerador de certificado do Microsoft Azure CRP_** . Em uma VM RDFE clássica, esses certificados têm o nome da entidade **_Gerenciamento de serviços do Windows Azure para extensões_** .

Esses certificados protegem a comunicação entre a VM e seu host durante a transferência de configurações protegidas (senha, outras credenciais) usadas pelas extensões. Os certificados são criados pelo controlador de malha do Azure e passados para o agente de VM. Se você parar e iniciar a VM todos os dias, um novo certificado poderá ser criado pelo controlador de malha. O certificado é armazenado no repositório de certificados pessoais do computador. Esses certificados podem ser excluídos. O agente de VM recria certificados se necessário.

### <a name="how-do-agents-and-extensions-get-updated"></a>Como os agentes e as extensões são atualizados?

Os agentes e as extensões compartilham o mesmo mecanismo de atualização. Algumas atualizações não exigem regras de firewall adicionais.

Quando uma atualização está disponível, ela só é instalada na VM quando há uma alteração nas extensões e outras alterações de modelo de VM, como:

- Discos de dados
- Extensões
- Contêiner de diagnóstico de inicialização
- Segredos do SO convidado
- Tamanhos de VM
- Perfil de rede

Os Publicadores disponibilizam atualizações para regiões em momentos diferentes, para que seja possível ter VMs em diferentes regiões em diferentes versões.

#### <a name="listing-extensions-deployed-to-a-vm"></a>Listando extensões implantadas em uma VM

```powershell
$vm = Get-AzVM -ResourceGroupName "myResourceGroup" -VMName "myVM"
$vm.Extensions | select Publisher, VirtualMachineExtensionType, TypeHandlerVersion
```

```powershell
Publisher             VirtualMachineExtensionType          TypeHandlerVersion
---------             ---------------------------          ------------------
Microsoft.Compute     CustomScriptExtension                1.9
```

#### <a name="agent-updates"></a>Atualizações do agente

O agente convidado do Windows contém apenas o *código de manipulação de extensão*, o código de provisionamento do *Windows* é separado. Você pode desinstalar o agente convidado do Windows. Não é possível desabilitar a atualização automática do agente convidado do Windows.

O *código de manipulação de extensão* é responsável pela comunicação com a malha do Azure e pela manipulação das operações de extensões de VM, como instalações, status de relatórios, atualização de extensões individuais e remoção delas. As atualizações contêm correções de segurança, correções de bugs e aprimoramentos no *código de manipulação de extensão*.

Para verificar qual versão está sendo executada, consulte [detectando o agente convidado do Windows instalado](agent-windows.md#detect-the-vm-agent).

#### <a name="extension-updates"></a>Atualizações de extensão

Quando uma atualização de extensão está disponível, o agente convidado do Windows baixa e atualiza a extensão. As atualizações de extensão automáticas são *secundária* ou de *hotfix*. Você pode aceitar ou recusar atualizações *secundárias* de extensões ao provisionar a extensão. O exemplo a seguir mostra como atualizar as versões secundárias automaticamente em um modelo do Resource Manager com *autoUpgradeMinorVersion ": true,"* :

```json
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.9",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ]
    },
```

Para obter as correções mais recentes do bug de lançamento secundário, é altamente recomendável que você sempre Selecione atualização automática em suas implantações de extensão. Atualizações de hotfix que contêm correções de bug de chave ou segurança não podem ser recusadas.

### <a name="how-to-identify-extension-updates"></a>Como identificar atualizações de extensão

#### <a name="identifying-if-the-extension-is-set-with-autoupgrademinorversion-on-a-vm"></a>Identificar se a extensão está definida com autoUpgradeMinorVersion em uma VM

Você poderá ver no modelo de VM se a extensão tiver sido provisionada com ' autoUpgradeMinorVersion '. Para verificar, use [Get-AzVm](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) e forneça o grupo de recursos e o nome da VM da seguinte maneira:

```powerShell
 $vm = Get-AzVm -ResourceGroupName "myResourceGroup" -VMName "myVM"
 $vm.Extensions
```

A saída de exemplo a seguir mostra que *autoUpgradeMinorVersion* está definido como *true*:

```powershell
ForceUpdateTag              :
Publisher                   : Microsoft.Compute
VirtualMachineExtensionType : CustomScriptExtension
TypeHandlerVersion          : 1.9
AutoUpgradeMinorVersion     : True
```

#### <a name="identifying-when-an-autoupgrademinorversion-occurred"></a>Identificando quando ocorreu um autoUpgradeMinorVersion

Para ver quando uma atualização da extensão ocorreu, examine os logs de agente na VM em *C:\WindowsAzure\Logs\WaAppAgent.log*

No exemplo a seguir, a VM tinha *Microsoft. COMPUTE. CustomScriptExtension 1,8* instalado. Um hotfix estava disponível para a versão *1,9*:

```powershell
[INFO]  Getting plugin locations for plugin 'Microsoft.Compute.CustomScriptExtension'. Current Version: '1.8', Requested Version: '1.9'
[INFO]  Auto-Upgrade mode. Highest public version for plugin 'Microsoft.Compute.CustomScriptExtension' with requested version: '1.9', is: '1.9'
```

## <a name="agent-permissions"></a>Permissões do agente

Para executar suas tarefas, o agente precisa ser executado como *sistema local*.

## <a name="troubleshoot-vm-extensions"></a>Solucionar problemas de extensões de VM

Cada extensão de VM pode ter etapas de solução de problemas específicas para a extensão. Por exemplo, quando você usa a extensão de script personalizado, os detalhes de execução de script podem ser encontrados localmente na VM em que a extensão foi executada. Qualquer etapa de solução de problemas específica de extensão é detalhada na documentação específica da extensão.

As etapas de solução de problemas a seguir se aplicam a todas as extensões de VM.

1. Para verificar o log do agente convidado do Windows, examine a atividade quando sua extensão estava sendo provisionada no *C:\WindowsAzure\Logs\WaAppAgent.txt*

2. Verifique os logs de extensão reais para obter mais detalhes em *C:\WindowsAzure\Logs\Plugins\<extensionname >*

3. Verifique as seções específicas de solução de problemas de extensão para códigos de erro, problemas conhecidos etc.

4. Examine os logs do sistema. Verifique se há outras operações que possam interferir na extensão, como uma instalação de longa execução de outro aplicativo que exigia acesso exclusivo ao Gerenciador de pacotes.

### <a name="common-reasons-for-extension-failures"></a>Motivos comuns para falhas de extensão

1. As extensões têm 20 minutos para serem executadas (as exceções são as extensões CustomScript, chefe e DSC que têm 90 minutos). Se sua implantação exceder esse tempo, ela será marcada como um tempo limite. A causa disso pode ser devido a pequenas VMs de recursos, outras configurações de VM/inicialização de tarefas que consomem grandes quantidades de recursos, enquanto a extensão está tentando provisionar.

2. Pré-requisitos mínimos não atendidos. Algumas extensões têm dependências em SKUs de VM, como imagens do HPC. As extensões podem exigir certos requisitos de acesso à rede, como a comunicação com o armazenamento do Azure ou serviços públicos. Outros exemplos podem ser acesso a repositórios de pacotes, ficando sem espaço em disco ou restrições de segurança.

3. Acesso exclusivo do Gerenciador de pacotes. Em alguns casos, você pode encontrar uma configuração de VM de longa execução e uma instalação de extensão em conflito, onde ambas precisam de acesso exclusivo ao Gerenciador de pacotes.

### <a name="view-extension-status"></a>Exibir status da extensão

Depois que uma extensão de VM for executada em uma VM, use [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) para retornar o status da extensão. *Substatus [0]* mostra que o provisionamento de extensão foi bem-sucedido, o que significa que ele foi implantado com êxito na VM, mas a execução da extensão dentro da VM falhou, *substatus [1]* .

```powershell
Get-AzVM -ResourceGroupName "myResourceGroup" -VMName "myVM" -Status
```

A saída é semelhante à seguinte saída de exemplo:

```powershell
Extensions[0]           :
  Name                  : CustomScriptExtension
  Type                  : Microsoft.Compute.CustomScriptExtension
  TypeHandlerVersion    : 1.9
  Substatuses[0]        :
    Code                : ComponentStatus/StdOut/succeeded
    Level               : Info
    DisplayStatus       : Provisioning succeeded
    Message             : Windows PowerShell \nCopyright (C) Microsoft Corporation. All rights reserved.\n
  Substatuses[1]        :
    Code                : ComponentStatus/StdErr/succeeded
    Level               : Info
    DisplayStatus       : Provisioning succeeded
    Message             : The argument 'cseTest%20Scriptparam1.ps1' to the -File parameter does not exist. Provide the path to an existing '.ps1' file as an argument to the

-File parameter.
  Statuses[0]           :
    Code                : ProvisioningState/failed/-196608
    Level               : Error
    DisplayStatus       : Provisioning failed
    Message             : Finished executing command
```

O status de execução da extensão também pode ser encontrado no portal do Azure. Para exibir o status de uma extensão, selecione a VM, escolha **extensões**e, em seguida, selecione a extensão desejada.

### <a name="rerun-vm-extensions"></a>Executar novamente as extensões de VM

Pode haver casos em que uma extensão de VM precisa ser executada novamente. Você pode executar novamente uma extensão removendo-a e, em seguida, executando novamente a extensão com um método de execução de sua escolha. Para remover uma extensão, use [Remove-AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/Remove-AzVMExtension) da seguinte maneira:

```powershell
Remove-AzVMExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myExtensionName"
```

Você também pode remover uma extensão na portal do Azure da seguinte maneira:

1. Selecione uma VM.
2. Escolha **extensões**.
3. Selecione a extensão desejada.
4. Escolha **desinstalar**.

## <a name="common-vm-extensions-reference"></a>Referência de extensões de VM comuns
| Nome da extensão | Descrição | Mais informações |
| --- | --- | --- |
| Extensão de script personalizado para Windows |Executar scripts em uma máquina virtual do Azure |[Extensão de Script Personalizado para o Windows](custom-script-windows.md) |
| Extensão de DSC para Windows |Extensão do PowerShell DSC (configuração de estado desejado) |[Extensão de DSC para Windows](dsc-overview.md) |
| Extensão do Diagnóstico do Azure |Gerenciar Diagnóstico do Azure |[Extensão do Diagnóstico do Azure](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Extensão de acesso à VM do Azure |Gerenciar usuários e credenciais |[Extensão de acesso à VM para Linux](https://azure.microsoft.com/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre extensões de VM, consulte [visão geral de extensões e recursos de máquinas virtuais do Azure](overview.md).
