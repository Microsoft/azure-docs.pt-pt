---
title: Recursos para Windows e as extensões de VM do Azure | Documentos da Microsoft
description: Saiba que extensões estão disponíveis para máquinas virtuais do Azure, agrupadas pelo que fornecer ou melhorar.
services: virtual-machines-windows
documentationcenter: ''
author: roiyz-msft
manager: jeconnoc
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 999d63ee-890e-432e-9391-25b3fc6cde28
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/30/2018
ms.author: roiyz
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 542fe0064de84e6266d2d0274809d7b2046b855c
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56874414"
---
# <a name="virtual-machine-extensions-and-features-for-windows"></a>Extensões de máquina virtual e funcionalidades para Windows

As extensões de máquina virtual do Azure (VM) são aplicativos pequenos que fornecem as tarefas de automatização e configuração de pós-implementação em VMs do Azure. Por exemplo, se uma máquina virtual requer a instalação de software, proteção de software antivírus, ou para executar um script dentro dela, uma extensão de VM pode ser utilizada. Extensões VM do Azure podem ser executadas com a CLI do Azure, PowerShell, modelos do Azure Resource Manager e o portal do Azure. As extensões podem ser agrupadas com uma nova implementação de VM ou executar qualquer sistema existente.

Este artigo fornece uma descrição geral das extensões de VM, pré-requisitos para utilizar extensões de VM do Azure, e obter orientações sobre como detetar, gerir e remover extensões de VM. Este artigo fornece informações generalizadas, porque muitas extensões VM estão disponíveis, cada um com uma configuração potencialmente exclusiva. Extensão específica pode encontrar detalhes de cada documento específicos para a extensão individual.

## <a name="use-cases-and-samples"></a>Casos de utilização e exemplos

Várias extensões de VM do Azure diferentes estão disponíveis, cada um com uma específicas do caso de utilização. Alguns exemplos incluem:

- Aplica configurações de estado de pretendido do PowerShell para uma VM com a extensão de DSC para Windows. Para obter mais informações, consulte [extensão de configuração de estado pretendido do Azure](dsc-overview.md).
- Configure a monitorização de uma VM com a extensão de VM de agente de monitorização Microsoft. Para obter mais informações, consulte [ligar as VMs do Azure para o Log Analytics](../../log-analytics/log-analytics-azure-vm-extension.md).
- Com o Chef para configurar uma VM do Azure. Para obter mais informações, consulte [implementação de automação de VM do Azure com o Chef](../windows/chef-automation.md).
- Configure a monitorização da sua infraestrutura do Azure com a extensão de Datadog. Para obter mais informações, consulte a [Datadog blog](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/).


Além de extensões específicas do processo, uma extensão de Script personalizado está disponível para máquinas virtuais Windows e Linux. A extensão de Script personalizado para o Windows permite que qualquer script do PowerShell para ser executada numa VM. Scripts personalizados são úteis para a criação de implementações do Azure que necessitam de configuração para além dos quais as de ferramentas do Azure nativa podem fornecer. Para obter mais informações, consulte [extensão de Script do Windows VM personalizado](custom-script-windows.md).

## <a name="prerequisites"></a>Pré-requisitos

Para lidar com a extensão na VM, terá do agente do Windows Azure instalado. Algumas extensões individuais têm pré-requisitos, tais como o acesso a recursos ou dependências.

### <a name="azure-vm-agent"></a>Agente da VM do Azure

O agente de VM do Azure gere as interações entre uma VM do Azure e o controlador de recursos de infraestrutura do Azure. O agente da VM é responsável por muitos aspetos funcionais de implementar e gerir VMs do Azure, incluindo a execução de extensões de VM. O agente de VM do Azure é previamente instalado nas imagens do Azure Marketplace e pode ser instalado manualmente em sistemas operativos suportados. O Azure VM Agent para Windows é conhecido como o agente convidado do Windows.

Para obter informações sobre sistemas operativos suportados e instruções de instalação, consulte [agente da máquina virtual do Azure](agent-windows.md).

#### <a name="supported-agent-versions"></a>Versões de agentes suportados

Para fornecer a melhor experiência possível, existem versões mínimas do agente. Para obter mais informações, consulte [este artigo](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

#### <a name="supported-oses"></a>Sistemas operacionais suportados

O agente convidado do Windows é executado em vários sos, no entanto, a estrutura de extensões tem um limite para os sistemas operacionais que extensões. Para obter mais informações, consulte [este artigo](https://support.microsoft.com/en-us/help/4078134/azure-extension-supported-operating-systems
).

Algumas extensões não são suportadas em todos os sos e pode emitir *51 de código de erro, o "SO não suportada"*. Consulte a documentação de extensão individuais para a capacidade de suporte.

#### <a name="network-access"></a>Acesso à rede

Pacotes de extensão são transferidos a partir do repositório de extensão do armazenamento do Azure e carregamentos de estado de extensão são lançados para o armazenamento do Azure. Se usar [suportado](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) versão dos agentes, não é necessário permitir o acesso ao armazenamento do Azure na região da VM, como pode utilizar o agente para redirecionar a comunicação para o controlador de malha do Azure para comunicações de agente. Se estiver numa versão não suportada do agente, terá de permitir o acesso de saída para o armazenamento do Azure na região da VM.

> [!IMPORTANT]
> Se bloqueou o acesso a *168.63.129.16* utilizando a firewall de convidado, em seguida, extensões falharem independentemente acima.

Agentes só podem ser utilizados para transferir pacotes de extensão e comunicar o estado. Por exemplo, se uma instalação da extensão tem de transferir um script a partir do GitHub (Script personalizado) ou tem de aceder ao armazenamento do Azure (Azure Backup), em seguida, adicionais/rede de firewall segurança portas de grupo tem de ser aberto. Extensões diferentes têm requisitos diferentes, uma vez que são aplicativos por si mesmos. Para extensões que necessitam de acesso ao armazenamento do Azure, pode permitir acesso utilizando as etiquetas de serviço do Azure NSG para [armazenamento](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

O agente convidado do Windows não tem suporte para redirecionar pedidos de tráfego do agente através de servidor de proxy.

## <a name="discover-vm-extensions"></a>Descubra as extensões de VM

Estão disponíveis muitas VMs diferentes para utilização com as VMs do Azure. Para ver uma lista completa, utilize [Get-AzVMExtensionImage](https://docs.microsoft.com/powershell/module/az.compute/get-azvmextensionimage). O exemplo seguinte lista todas as extensões disponíveis no *WestUS* localização:

```powershell
Get-AzVmImagePublisher -Location "WestUS" | `
Get-AzVMExtensionImageType | `
Get-AzVMExtensionImage | Select Type, Version
```

## <a name="run-vm-extensions"></a>Executar as extensões de VM

Extensões VM do Azure, execute em VMs existentes, que é útil quando precisa fazer alterações de configuração ou recuperar a conectividade numa VM já implementada. Extensões de VM também podem ser agrupadas com implementações de modelo do Azure Resource Manager. Ao utilizar extensões com modelos do Resource Manager, VMs do Azure pode ser implementadas e configuradas sem a intervenção de pós-implementação.

Os seguintes métodos podem ser utilizados para executar uma extensão em relação a uma VM existente.

### <a name="powershell"></a>PowerShell

Existem vários comandos do PowerShell para a execução de extensões individuais. Para ver uma lista, utilize [Get-Command](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/get-command) e filtre *extensão*:

```powershell
Get-Command Set-Az*Extension* -Module AzureRM.Compute
```

Isso fornece o resultado semelhante ao seguinte:

```powershell
CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Set-AzVMAccessExtension                       4.5.0      AzureRM.Compute
Cmdlet          Set-AzVMADDomainExtension                     4.5.0      AzureRM.Compute
Cmdlet          Set-AzVMAEMExtension                          4.5.0      AzureRM.Compute
Cmdlet          Set-AzVMBackupExtension                       4.5.0      AzureRM.Compute
Cmdlet          Set-AzVMBginfoExtension                       4.5.0      AzureRM.Compute
Cmdlet          Set-AzVMChefExtension                         4.5.0      AzureRM.Compute
Cmdlet          Set-AzVMCustomScriptExtension                 4.5.0      AzureRM.Compute
Cmdlet          Set-AzVMDiagnosticsExtension                  4.5.0      AzureRM.Compute
Cmdlet          Set-AzVMDiskEncryptionExtension               4.5.0      AzureRM.Compute
Cmdlet          Set-AzVMDscExtension                          4.5.0      AzureRM.Compute
Cmdlet          Set-AzVMExtension                             4.5.0      AzureRM.Compute
Cmdlet          Set-AzVMSqlServerExtension                    4.5.0      AzureRM.Compute
Cmdlet          Set-AzVmssDiskEncryptionExtension             4.5.0      AzureRM.Compute
```

O exemplo seguinte utiliza a extensão de Script personalizado para transferir um script a partir de um repositório do GitHub para a máquina virtual de destino e, em seguida, execute o script. Para obter mais informações sobre a extensão de Script personalizado, consulte [descrição geral da extensão de Script personalizado](custom-script-windows.md).

```powershell
Set-AzVMCustomScriptExtension -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" -Name "myCustomScript" `
    -FileUri "https://raw.githubusercontent.com/neilpeterson/nepeters-azure-templates/master/windows-custom-script-simple/support-scripts/Create-File.ps1" `
    -Run "Create-File.ps1" -Location "West US"
```

No exemplo seguinte, a extensão de acesso à VM é utilizada para repor a palavra-passe administrativa de uma VM do Windows para uma palavra-passe temporária. Para obter mais informações sobre a extensão de acesso à VM, consulte [serviço de reposição de ambiente de trabalho remoto numa VM do Windows](../windows/reset-rdp.md). Depois de executar isto, deverá repor a palavra-passe no primeiro início de sessão:

```powershell
$cred=Get-Credential

Set-AzVMAccessExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myVMAccess" `
    -Location WestUS -UserName $cred.GetNetworkCredential().Username `
    -Password $cred.GetNetworkCredential().Password -typeHandlerVersion "2.0"
```

O `Set-AzVMExtension` comando pode ser utilizado para iniciar qualquer extensão de VM. Para obter mais informações, consulte a [referência de conjunto AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension).


### <a name="azure-portal"></a>Portal do Azure

Extensões de VM podem ser aplicadas a uma VM existente através do portal do Azure. Selecione a VM no portal, escolha **extensões**, em seguida, selecione **Add**. Escolha a extensão de que pretende na lista de extensões disponíveis e siga as instruções no assistente.

O exemplo seguinte mostra a instalação da extensão de Antimalware da Microsoft do portal do Azure:

![Instalar a extensão de antimalware](./media/features-windows/installantimalwareextension.png)

### <a name="azure-resource-manager-templates"></a>Modelos do Azure Resource Manager

Extensões de VM podem ser adicionadas a um modelo Azure Resource Manager e executadas com a implementação do modelo. Quando implementa uma extensão com um modelo, pode criar implementações do Azure totalmente configuradas. Por exemplo, está a ser utilizado o seguinte JSON de um Gerenciador de recursos modelo implementa um conjunto de VMs com balanceamento de carga e uma base de dados SQL do Azure, em seguida, instala uma aplicação .NET Core em cada VM. A extensão da VM se encarrega da instalação de software.

Para obter mais informações, consulte a [modelo do Resource Manager completo](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows).

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

Para obter mais informações sobre a criação de modelos do Resource Manager, consulte [modelos de Authoring Azure Resource Manager com extensões de VM do Windows](../windows/template-description.md#extensions).

## <a name="secure-vm-extension-data"></a>Proteger os dados de extensão VM

Quando executa uma extensão de VM, poderá ser necessário incluir informações confidenciais, como credenciais, nomes de conta de armazenamento e chaves de acesso da conta de armazenamento. Várias extensões VM incluem uma configuração protegida que encripta os dados e só descriptografa-a no interior da VM de destino. Cada extensão tem um esquema de configuração protegida específica, e cada está detalhada na documentação de extensão específica.

O exemplo seguinte mostra uma instância da extensão de Script personalizado para Windows. O comando a executar inclui um conjunto de credenciais. Neste exemplo, não está encriptado o comando a executar:

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

Mover o **comando para executar** propriedade para o **protegidos** configuração protege a cadeia de caracteres de execução, conforme mostrado no exemplo a seguir:

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

### <a name="how-do-agents-and-extensions-get-updated"></a>Como e extensões do agentes atualizadas?

Os agentes e extensões partilham o mesmo mecanismo de atualização. Algumas atualizações não necessitam de regras de firewall adicionais.

Quando uma atualização está disponível, ele só é instalado na VM quando existe uma alteração às extensões e outras alterações de modelo de VM, tais como:

- Discos de dados
- Extensões
- Contentor de diagnóstico de arranque
- Segredos do SO convidado
- Tamanho da VM
- Perfil de rede

Os publicadores disponibilizar as atualizações à regiões em alturas diferentes, portanto, é possível que pode ter VMs em diferentes regiões em versões diferentes.

#### <a name="listing-extensions-deployed-to-a-vm"></a>Extensões de listagem implementadas para uma VM

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

O agente convidado do Windows contém apenas *código de processamento de extensão*, o *código de Provisionamento do Windows* separado. Pode desinstalar o agente convidado do Windows. Não é possível desativar a atualização automática do agente de convidado de janela.

O *código de processamento de extensão* é responsável por comunicar com os recursos de infraestrutura do Azure e o processamento de como as operações de extensões VM instala, comunicar o estado, a instalação das extensões individuais e removê-los. Atualizações incluem correções de segurança, correções de erros e melhorias para o *código de processamento de extensão*.

Para verificar qual estiver a executar a versão, consulte [detectando instalado o agente de convidado do Windows](agent-windows.md#detect-the-vm-agent).

#### <a name="extension-updates"></a>Atualizações de extensão

Quando está disponível uma atualização de extensão, o agente convidado do Windows transfere e atualiza a extensão. Atualizações automáticas de extensão são *pequenas* ou *correção*. Pode optar ativamente por participar no ou opção de desativar as extensões *pequenas* atualiza quando Aprovisiona a extensão. O exemplo seguinte mostra como atualizar automaticamente as versões secundárias num modelo do Resource Manager com *autoUpgradeMinorVersion ": true,'*:

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

Para obter as correções de erros de versão secundária mais recente, é altamente recomendável que selecione sempre a atualização automática das implementações de extensão. Atualizações de correção que vão ser correções de bugs de segurança ou a chave não podem ser excluídas.

### <a name="how-to-identify-extension-updates"></a>Como identificar atualizações de extensão

#### <a name="identifying-if-the-extension-is-set-with-autoupgrademinorversion-on-a-vm"></a>Identificando se a extensão estiver definida com o autoUpgradeMinorVersion numa VM

Pode ver o modelo de VM se a extensão foi aprovisionada com o "autoUpgradeMinorVersion". Para verificar, utilize [Get-AzVm](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) e forneça o nome do grupo de recursos e a VM da seguinte forma:

```powerShell
 $vm = Get-AzVm -ResourceGroupName "myResourceGroup" -VMName "myVM"
 $vm.Extensions
```

O resultado de exemplo seguinte mostra que *autoUpgradeMinorVersion* está definida como *verdadeiro*:

```powershell
ForceUpdateTag              :
Publisher                   : Microsoft.Compute
VirtualMachineExtensionType : CustomScriptExtension
TypeHandlerVersion          : 1.9
AutoUpgradeMinorVersion     : True
```

#### <a name="identifying-when-an-autoupgrademinorversion-occurred"></a>Identificar quando ocorreu uma autoUpgradeMinorVersion

Para ver em que ocorreu uma atualização para a extensão, reveja o agente de registos na VM no *C:\WindowsAzure\Logs\WaAppAgent.log*

No exemplo seguinte, a VM tinha *Microsoft.Compute.CustomScriptExtension 1.8* instalado. Uma correção estava disponível para a versão *1.9*:

```powershell
[INFO]  Getting plugin locations for plugin 'Microsoft.Compute.CustomScriptExtension'. Current Version: '1.8', Requested Version: '1.9'
[INFO]  Auto-Upgrade mode. Highest public version for plugin 'Microsoft.Compute.CustomScriptExtension' with requested version: '1.9', is: '1.9'
```

## <a name="agent-permissions"></a>Permissões de agente

Para realizar suas tarefas, o agente tem de executar como *Sistema Local*.

## <a name="troubleshoot-vm-extensions"></a>Resolver problemas de extensões de VM

Cada extensão VM pode ter passos específicos para a extensão de resolução de problemas. Por exemplo, quando utiliza a extensão de Script personalizado, detalhes da execução do script podem ser encontrados localmente na VM em que a extensão foi executada. Quaisquer passos de resolução de problemas específicos de extensão são detalhados na documentação de extensão específica.

Os passos de resolução de problemas seguintes aplicam-se a todas as extensões VM.

1. Para verificar o registo do agente convidado do Windows, examinar a atividade quando sua extensão foi a ser aprovisionada no *C:\WindowsAzure\Logs\WaAppAgent.txt*

2. Verifique os registos de extensão real para obter mais detalhes no *C:\WindowsAzure\Logs\Plugins\<extensionName >*

3. Consulte as secções de resolução de problemas de documentação específica de extensão para códigos de erro, problemas etc conhecidos.

4. Consulte os registos de sistema. Verificar a existência de outras operações que podem ter violado extensão, como uma instalação de longa execução de outro aplicativo que exigia acesso de Gestor do pacote exclusivo.

### <a name="common-reasons-for-extension-failures"></a>Motivos comuns para falhas de extensões

1. As extensões de ter 20 minutos para ser executado (as exceções são as extensões CustomScript, o Chef e o DSC que tenham a 90 minutos). Se a implementação exceder neste momento, está marcada como um tempo limite. A causa isso pode ser devido a poucos recursos VMs, outras VM configurações/tarefas de arranque consumir quantidades elevadas de recursos, lidando simultaneamente e a extensão está a tentar aprovisionar.

2. Pré-requisitos mínimos não cumpridos. Algumas extensões têm dependências em SKUs de VM, como imagens HPC. As extensões podem exigir a determinados requisitos de acesso à rede, como comunicar com o armazenamento do Azure ou serviços públicos. Outros exemplos podem ser o acesso a repositórios de pacote, a ficar sem espaço em disco ou restrições de segurança.

3. Acesso de Gestor do pacote exclusivo. Em alguns casos, pode encontrar uma configuração de VM de longa execução e em conflito, a instalação da extensão onde precisam de acesso exclusivo para o Gestor de pacotes.

### <a name="view-extension-status"></a>Ver o estado de extensão

Depois de executar uma extensão de VM em relação a uma VM, utilize [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) para devolver o estado da extensão. *Substatuses [0]* mostra que o aprovisionamento de extensão foi concluída com êxito, que significa que ele bem-sucedida implementado na VM, mas a execução da extensão dentro da VM falhou, *Substatuses [1]*.

```powershell
Get-AzVM -ResourceGroupName "myResourceGroup" -VMName "myVM" -Status
```

O resultado é semelhante à saída de exemplo seguinte:

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

Estado de execução da extensão também pode ser encontrado no portal do Azure. Para ver o estado de uma extensão, selecione a VM, escolha **extensões**, em seguida, selecione a extensão desejada.

### <a name="rerun-vm-extensions"></a>Volte a executar as extensões de VM

Pode haver casos em que uma extensão de VM tem de ser executados novamente. Pode executar novamente uma extensão, removendo-a e, em seguida, executar novamente a extensão com um método de execução da sua preferência. Para remover uma extensão, utilize [Remove-AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/Remove-AzVMExtension) da seguinte forma:

```powershell
Remove-AzVMExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myExtensionName"
```

Também pode remover uma extensão no portal do Azure da seguinte forma:

1. Selecione uma VM.
2. Escolher **extensões**.
3. Selecione a extensão desejada.
4. Escolher **desinstalar**.

## <a name="common-vm-extensions-reference"></a>Referência de extensões VM comuns
| Nome da extensão | Descrição | Mais informações |
| --- | --- | --- |
| Extensão de Script personalizado para Windows |Executar scripts numa máquina virtual do Azure |[Extensão de Script personalizado para Windows](custom-script-windows.md) |
| Extensão de DSC para o Windows |Extensão do PowerShell DSC (Desired State Configuration) |[Extensão de DSC para o Windows](dsc-overview.md) |
| Extensão do Diagnóstico do Azure |Gerir o diagnóstico do Azure |[Extensão do Diagnóstico do Azure](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Extensão de acesso VM do Azure |Gerir utilizadores e as credenciais |[Extensão de acesso da VM para Linux](https://azure.microsoft.com/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre as extensões de VM, consulte [descrição geral de extensões e funcionalidades de máquina virtual do Azure](overview.md).
