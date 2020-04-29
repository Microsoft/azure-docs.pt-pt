---
title: Extensões e funcionalidades de VM Azure para Windows
description: Saiba quais as extensões disponíveis para máquinas virtuais Azure, agrupando supérum pelo que fornecem ou melhoram.
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
ms.openlocfilehash: cd6439bf1b1f52b8e63819e8e519fc4971d1bc2a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80066844"
---
# <a name="virtual-machine-extensions-and-features-for-windows"></a>Extensões e funcionalidades de máquinavirtual para Windows

As extensões de máquina virtual (VM) do Azure são pequenas aplicações que proporcionam tarefas de automação e configuração pós-implementação nas VMs do Azure. Por exemplo, se uma máquina virtual exigir a instalação de software, a proteção antivírus ou a execução de um script, poderá ser utilizada uma extensão de VM. As extensões de VM do Azure podem ser executadas na CLI do Azure, no PowerShell, nos modelos do Azure Resource Manager e no portal do Azure. As extensões podem ser agrupadas com uma nova implementação da VM ou executadas em qualquer sistema existente.

Este artigo fornece uma visão geral das extensões VM, pré-requisitos para a utilização de extensões VM Azure e orientações sobre como detetar, gerir e remover extensões VM. Este artigo fornece informações generalizadas porque muitas extensões VM estão disponíveis, cada uma com uma configuração potencialmente única. Detalhes específicos da extensão podem ser encontrados em cada documento específico da extensão individual.

 

## <a name="use-cases-and-samples"></a>Utilizar casos e amostras

Várias extensões Azure VM diferentes estão disponíveis, cada uma com um caso de utilização específico. Alguns exemplos incluem:

- Aplique as configurações do Estado desejado powerShell num VM com a extensão DSC para Windows. Para mais informações, consulte a extensão de [configuração do Estado Pretendido.](dsc-overview.md)
- Configure a monitorização de um VM com a extensão VM do Agente de Análise de Log Analytics. Para mais informações, consulte [Os VMs Connect Azure para os registos do Monitor Azure](../../log-analytics/log-analytics-azure-vm-extension.md).
- Configure um Azure VM usando o Chef. Para mais informações, consulte [automating Azure VM deployment with Chef](../../chef/chef-automation.md).
- Configure a monitorização da sua infraestrutura Azure com a extensão Datadog. Para mais informações, consulte o [blog Datadog](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/).


Além de extensões específicas do processo, uma extensão de Script Personalizado está disponível tanto para máquinas virtuais Windows como Linux. A extensão custom Script para Windows permite que qualquer script PowerShell seja executado num VM. Scripts personalizados são úteis para projetar implementações Azure que requerem configuração para além do que a ferramenta nativa Azure pode fornecer. Para mais informações, consulte a [extensão do Script Personalizado do Windows VM](custom-script-windows.md).

## <a name="prerequisites"></a>Pré-requisitos

Para lidar com a extensão do VM, precisa do Agente Azure Windows instalado. Algumas extensões individuais têm pré-requisitos, tais como o acesso a recursos ou dependências.

### <a name="azure-vm-agent"></a>Agente da VM do Azure

O agente Azure VM gere interações entre um VM Azure e o controlador de tecido Azure. O agente VM é responsável por muitos aspetos funcionais de implantação e gestão de VMs Azure, incluindo extensões VM de execução. O agente Azure VM está pré-instalado nas imagens do Azure Marketplace e pode ser instalado manualmente em sistemas operativos suportados. O Agente Azure VM para Windows é conhecido como o agente Convidado do Windows.

Para obter informações sobre sistemas operativos suportados e instruções de instalação, consulte o [agente de máquinas virtuais Azure](agent-windows.md).

#### <a name="supported-agent-versions"></a>Versões de agente suportado

Para proporcionar a melhor experiência possível, existem versões mínimas do agente. Para obter mais informações, consulte [este artigo](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

#### <a name="supported-oses"></a>OSes suportados

O agente Windows Guest funciona em várias OSes, no entanto a estrutura de extensões tem um limite para os OSes que se prolongam. Para obter mais informações, consulte [este artigo](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems
).

Algumas extensões não são suportadas em todos os OSes e podem emitir código de *erro 51, "Os Não Suportado".* Verifique a documentação individual de extensão para obter suporte.

#### <a name="network-access"></a>Acesso à rede

Os pacotes de extensão são descarregados do repositório de extensão de armazenamento Azure, e os uploads de estado de extensão são publicados no Armazenamento Azure. Se utilizar a versão [suportada](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) dos agentes, não precisa de permitir o acesso ao Armazenamento Azure na região vM, assim como pode utilizar o agente para redirecionar a comunicação para o controlador de tecido Sino para comunicações de agentes (funcionalidade HostGAPlugin através do canal privilegiado no IP [168.63.129.16](https://docs.microsoft.com/azure/virtual-network/what-is-ip-address-168-63-129-16)privado ). Se estiver numa versão não suportada do agente, tem de permitir o acesso de saída ao armazenamento Azure naquela região a partir do VM.

> [!IMPORTANT]
> Se bloqueou o acesso a *168.63.129.16* utilizando a firewall do hóspede ou com um representante, as extensões falham independentemente do acima referido. São necessários os portos 80, 443 e 32526.

Os agentes só podem ser usados para descarregar pacotes de extensão e o estado de reporte. Por exemplo, se uma instalação de extensão precisar de descarregar um script do GitHub (Script Personalizado) ou precisar de acesso ao Armazenamento Azure (Backup Azure), então as portas adicionais de firewall/Network Security Group precisam de ser abertas. As diferentes extensões têm requisitos diferentes, uma vez que são aplicações por si só. Para extensões que requeiram acesso ao Azure Storage ou ao Azure Ative Directory, pode permitir o acesso utilizando etiquetas de [serviço Azure NSG](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) para armazenamento ou AzureActiveDirectory.

O Windows Guest Agent não dispõe de suporte proxy para redirecionar os pedidos de tráfego do agente através do IP 168.63.129.16.

## <a name="discover-vm-extensions"></a>Descubra extensões VM

Estão disponíveis muitas VMs diferentes para utilização com as VMs do Azure. Para ver uma lista completa, utilize [a Get-AzVMExtensionImage](https://docs.microsoft.com/powershell/module/az.compute/get-azvmextensionimage). O exemplo seguinte lista todas as extensões disponíveis na localização *westus:*

```powershell
Get-AzVmImagePublisher -Location "WestUS" | `
Get-AzVMExtensionImageType | `
Get-AzVMExtensionImage | Select Type, Version
```

## <a name="run-vm-extensions"></a>Executar extensões VM

As extensões De VM Azure funcionam em VMs existentes, o que é útil quando precisa de fazer alterações de configuração ou recuperar a conectividade num VM já implantado. As extensões VM também podem ser agregadas com implementações de modelos do Gestor de Recursos Azure. Utilizando extensões com modelos de Gestor de Recursos, os VMs Azure podem ser implantados e configurados sem intervenção pós-implantação.

Os seguintes métodos podem ser utilizados para executar uma extensão contra um VM existente.

### <a name="powershell"></a>PowerShell

Existem vários comandos PowerShell para executar extensões individuais. Para ver uma lista, utilize [o Get-Command](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/get-command) e o filtro na *Extensão:*

```powershell
Get-Command Set-Az*Extension* -Module Az.Compute
```

Isto fornece uma saída semelhante à seguinte:

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

O exemplo seguinte utiliza a extensão do Script Personalizado para descarregar um script de um repositório GitHub para a máquina virtual alvo e, em seguida, executar o script. Para obter mais informações sobre a extensão do Script Personalizado, consulte a visão geral da [extensão do Script Personalizado](custom-script-windows.md).

```powershell
Set-AzVMCustomScriptExtension -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" -Name "myCustomScript" `
    -FileUri "https://raw.githubusercontent.com/neilpeterson/nepeters-azure-templates/master/windows-custom-script-simple/support-scripts/Create-File.ps1" `
    -Run "Create-File.ps1" -Location "West US"
```

No exemplo seguinte, a extensão VM Access é utilizada para redefinir a palavra-passe administrativa de um VM do Windows para uma senha temporária. Para obter mais informações sobre a extensão de Acesso VM, consulte redefinir o serviço de ambiente de [trabalho remoto num Windows VM](../windows/reset-rdp.md). Uma vez executada isto, deve redefinir a palavra-passe no primeiro início de sessão:

```powershell
$cred=Get-Credential

Set-AzVMAccessExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myVMAccess" `
    -Location WestUS -UserName $cred.GetNetworkCredential().Username `
    -Password $cred.GetNetworkCredential().Password -typeHandlerVersion "2.0"
```

O `Set-AzVMExtension` comando pode ser utilizado para iniciar qualquer extensão VM. Para mais informações, consulte a [referência Set-AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension).


### <a name="azure-portal"></a>Portal do Azure

As extensões VM podem ser aplicadas a um VM existente através do portal Azure. Selecione o VM no portal, escolha **extensões**e, em seguida, **selecione Adicionar**. Escolha a extensão desejada na lista de extensões disponíveis e siga as instruções no assistente.

O exemplo que se segue mostra a instalação da extensão Antimalware da Microsoft a partir do portal Azure:

![Instalar extensão antimalware](./media/features-windows/installantimalwareextension.png)

### <a name="azure-resource-manager-templates"></a>Modelos do Azure Resource Manager

As extensões VM podem ser adicionadas a um modelo de Gestor de Recursos Azure e executadas com a implementação do modelo. Quando implementa uma extensão com um modelo, pode criar implementações Azure totalmente configuradas. Por exemplo, o seguinte JSON é retirado de um modelo de Gestor de Recursos implementa um conjunto de VMs equilibrados em carga e uma base de dados Azure SQL, em seguida, instala uma aplicação .NET Core em cada VM. A extensão VM cuida da instalação do software.

Para mais informações, consulte o [modelo completo do Gestor de Recursos](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows).

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

Para obter mais informações sobre a criação de modelos de Gestor de Recursos, consulte [os modelos de Gestor de Recursos Do Azure autor com extensões VM do Windows](../windows/template-description.md#extensions).

## <a name="secure-vm-extension-data"></a>Dados de extensão VM seguros

Quando executa uma extensão VM, pode ser necessário incluir informações sensíveis, tais como credenciais, nomes de contas de armazenamento e chaves de acesso à conta de armazenamento. Muitas extensões VM incluem uma configuração protegida que encripta dados e apenas os desencripta dentro do VM alvo. Cada extensão tem um esquema de configuração protegido específico, e cada uma é detalhada em documentação específica de extensão.

O exemplo que se segue mostra uma instância da extensão do Script Personalizado para Windows. O comando para executar inclui um conjunto de credenciais. Neste exemplo, o comando a executar não é encriptado:

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

Mover o **comando para executar** a propriedade para a configuração **protegida** segura a cadeia de execução, como mostra o seguinte exemplo:

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

Num VM Azure IaaS que utiliza extensões, na consola de certificados, poderá ver certificados que tenham o **_tema Windows Azure CRP Certificate Generator_**. Num VM CLÁSSICO do RDFE, estes certificados têm o nome de nome **_Windows Azure Service Management for Extensions_**.

Estes certificados asseguram a comunicação entre o VM e o seu anfitrião durante a transferência de definições protegidas (palavra-passe, outras credenciais) utilizadas por extensões. Os certificados são construídos pelo controlador de tecido Azure e passados para o Agente VM. Se parar e iniciar o VM todos os dias, um novo certificado pode ser criado pelo controlador de tecido. O certificado está guardado na loja de certificados pessoais do computador. Estes certificados podem ser eliminados. O Agente VM recria certificados se necessário.

### <a name="how-do-agents-and-extensions-get-updated"></a>Como é que os agentes e extensões são atualizados?

Os Agentes e Extensões partilham o mesmo mecanismo de atualização. Algumas atualizações não requerem regras adicionais de firewall.

Quando uma atualização está disponível, só é instalada no VM quando há uma alteração às extensões, e outras alterações do Modelo VM, tais como:

- Discos de dados
- Extensões
- Recipiente de diagnóstico de arranque
- Segredos do Os convidado
- Tamanho da VM
- Perfil de rede

As editoras disponibilizam atualizações para regiões em diferentes momentos, pelo que é possível que possa ter VMs em diferentes regiões em diferentes versões.

#### <a name="listing-extensions-deployed-to-a-vm"></a>Extensões de listagem implantadas para um VM

```powershell
$vm = Get-AzVM -ResourceGroupName "myResourceGroup" -VMName "myVM"
$vm.Extensions | select Publisher, VirtualMachineExtensionType, TypeHandlerVersion
```

```powershell
Publisher             VirtualMachineExtensionType          TypeHandlerVersion
---------             ---------------------------          ------------------
Microsoft.Compute     CustomScriptExtension                1.9
```

#### <a name="agent-updates"></a>Atualizações de agente

O Agente Convidado do Windows apenas contém código de tratamento de *extensão,* o código de *provisionamento do Windows* é separado. Pode desinstalar o Agente Convidado do Windows. Não é possível desativar a atualização automática do Agente Convidado da Janela.

O código de tratamento de *extensões* é responsável pela comunicação com o tecido Azure e manuseamento das operações de extensões VM, tais como instalações, estado de reporte, atualização das extensões individuais e remoção das mesmas. As atualizações contêm correções de segurança, correções de erros e melhorias no código de manuseamento de *extensões*.

Para verificar que versão está a executar, consulte [detetando o Agente convidado do Windows instalado](agent-windows.md#detect-the-vm-agent).

#### <a name="extension-updates"></a>Atualizações de extensão

Quando uma atualização de extensão está disponível, o Windows Guest Agent descarrega e atualiza a extensão. As atualizações automáticas de extensão são *Menor* ou *Hotfix*. Pode optar por não prorrogar as atualizações *menores* quando fornecer a extensão. O exemplo seguinte mostra como atualizar automaticamente versões menores num modelo de Gestor de Recursos com *autoUpgradeMinorVersion": verdadeiro,':*

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

Para obter as mais recentes correções de erros de desbloqueio, é altamente recomendável que selecione sempre a atualização automática nas suas implementações de extensão. As atualizações de hotfix que transportam correções de segurança ou de erros chave não podem ser optadas.

### <a name="how-to-identify-extension-updates"></a>Como identificar atualizações de extensão

#### <a name="identifying-if-the-extension-is-set-with-autoupgrademinorversion-on-a-vm"></a>Identificar se a extensão é definida com autoUpgradeMinorVersion num VM

Pode ver no modelo VM se a extensão foi aprovisionada com 'autoUpgradeMinorVersion'. Para verificar, utilize o [Get-AzVm](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) e forneça o nome do grupo de recursos e VM da seguinte forma:

```powerShell
 $vm = Get-AzVm -ResourceGroupName "myResourceGroup" -VMName "myVM"
 $vm.Extensions
```

A saída de exemplo a seguir mostra que a *versão autoUpgradeMinorVersion* está definida como *verdadeira:*

```powershell
ForceUpdateTag              :
Publisher                   : Microsoft.Compute
VirtualMachineExtensionType : CustomScriptExtension
TypeHandlerVersion          : 1.9
AutoUpgradeMinorVersion     : True
```

#### <a name="identifying-when-an-autoupgrademinorversion-occurred"></a>Identificar quando ocorreu uma versão automáticaDaMenor

Para ver quando ocorreu uma atualização da extensão, reveja os registos do agente no VM em *C:\WindowsAzure\Logs\WaAppAgent.log*

No exemplo seguinte, o VM tinha *microsoft.Compute.CustomScriptExtension 1.8* instalado. Um hotfix estava disponível para a versão *1.9:*

```powershell
[INFO]  Getting plugin locations for plugin 'Microsoft.Compute.CustomScriptExtension'. Current Version: '1.8', Requested Version: '1.9'
[INFO]  Auto-Upgrade mode. Highest public version for plugin 'Microsoft.Compute.CustomScriptExtension' with requested version: '1.9', is: '1.9'
```

## <a name="agent-permissions"></a>Permissões do agente

Para executar as suas tarefas, o agente precisa de funcionar como *Sistema Local.*

## <a name="troubleshoot-vm-extensions"></a>Extensões VM de resolução de problemas

Cada extensão VM pode ter passos de resolução de problemas específicos da extensão. Por exemplo, quando utiliza a extensão do Script Personalizado, os detalhes de execução do script podem ser encontrados localmente no VM onde a extensão foi executada. Quaisquer etapas específicas de resolução de problemas específicas para a extensão são detalhadas em documentação específica de extensão.

As seguintes etapas de resolução de problemas aplicam-se a todas as extensões VM.

1. Para verificar o Registo do Agente Convidado do Windows, veja a atividade quando a sua extensão estava a ser aprovisionada em *C:\WindowsAzure\Logs\WaAppAgent.log*

2. Verifique os registos de extensão reais para obter mais detalhes em *C:\WindowsAzure\Logs\Plugins\<extensãoName>*

3. Verifique as secções específicas de resolução de problemas de documentação específica para códigos de erro, questões conhecidas, etc.

4. Olhe para os registos do sistema. Verifique se existem outras operações que possam ter interferido com a extensão, como uma instalação de longa duração de outra aplicação que exigia acesso exclusivo ao gestor de pacotes.

### <a name="common-reasons-for-extension-failures"></a>Razões comuns para falhas na extensão

1. As extensões têm 20 minutos para executar (exceções são as extensões CustomScript, Chef e DSC que têm 90 minutos). Se a sua implantação exceder este tempo, está marcada como um tempo limite. A causa disto pode ser devido a VMs de baixo recurso, outras configurações vm/tarefas de arranque que consomem elevadas quantidades de recursos enquanto a extensão está a tentar fornecer.

2. Pré-requisitos mínimos não cumpridos. Algumas extensões têm dependências de VM SKUs, como imagens HPC. As extensões podem exigir determinados requisitos de acesso em rede, tais como a comunicação ao Armazenamento Azure ou serviços públicos. Outros exemplos poderiam ser o acesso a repositórios de pacotes, ficando sem espaço de disco, ou restrições de segurança.

3. Acesso exclusivo ao gestor de pacotes. Em alguns casos, poderá encontrar uma configuração vm de longa duração e instalação de extensão conflituosa, onde ambos precisam de acesso exclusivo ao gestor do pacote.

### <a name="view-extension-status"></a>Ver estado de extensão

Depois de uma extensão VM ter sido executada contra um VM, utilize [o Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) para devolver o estado de extensão. *Os subestatutos[0]* mostram que o fornecimento de extensão foi bem sucedido, o que significa que foi bem sucedido para o VM, mas a execução da extensão dentro do VM falhou, *subestatutos[1]*.

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

O estado de execução da extensão também pode ser encontrado no portal Azure. Para ver o estado de uma extensão, selecione o VM, escolha **extensões**e, em seguida, selecione a extensão desejada.

### <a name="rerun-vm-extensions"></a>Reexecutar extensões VM

Pode haver casos em que uma extensão VM precisa de ser reexecutada. Pode reproduzir uma extensão removendo-a e, em seguida, reexecutar a extensão com um método de execução à sua escolha. Para remover uma extensão, utilize a [extensão remove-AzVM](https://docs.microsoft.com/powershell/module/az.compute/Remove-AzVMExtension) da seguinte forma:

```powershell
Remove-AzVMExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myExtensionName"
```

Também pode remover uma extensão no portal Azure da seguinte forma:

1. Selecione um VM.
2. Escolha **extensões**.
3. Selecione a extensão desejada.
4. Escolha **desinstalar**.

## <a name="common-vm-extensions-reference"></a>Referência de extensões VM comuns
| Nome de extensão | Descrição | Mais informações |
| --- | --- | --- |
| Extensão de Script Personalizado para o Windows |Executar scripts contra uma máquina virtual Azure |[Extensão de Script Personalizado para o Windows](custom-script-windows.md) |
| Extensão DSC para Windows |Extensão PowerShell DSC (Configuração de Estado Desejada) |[Extensão DSC para Windows](dsc-overview.md) |
| Extensão do Diagnóstico do Azure |Gerir diagnósticos azure |[Extensão de Diagnóstico Azure](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Extensão de acesso Azure VM |Gerir utilizadores e credenciais |[Extensão de acesso VM para Linux](https://azure.microsoft.com/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre as extensões de VM, veja [Descrição geral das funcionalidades e extensões de máquinas virtuais do Azure](overview.md).
