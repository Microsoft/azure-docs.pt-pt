---
title: Atualização automática de extensão para VMs e conjuntos de escala em Azure
description: Saiba como ativar o Upgrade automático de extensão para as suas máquinas virtuais e conjuntos de balanças de máquinas virtuais em Azure.
author: mayanknayar
ms.service: virtual-machines
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/12/2020
ms.author: manayar
ms.openlocfilehash: acc014785105d14c3109cfa420f0e9402ca3f534
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100417555"
---
# <a name="preview-automatic-extension-upgrade-for-vms-and-scale-sets-in-azure"></a>Pré-visualização: Atualização automática de extensão para VMs e conjuntos de escala em Azure

A Atualização automática de extensão está disponível em pré-visualização para VMs Azure e conjuntos de balança de máquina virtual Azure. Quando a atualização automática da extensão é ativada num conjunto de VM ou escala, a extensão é atualizada automaticamente sempre que o editor de extensão lança uma nova versão para essa extensão.

 A atualização automática da extensão tem as seguintes funcionalidades:
- Suportado para VMs Azure e conjuntos de balança de máquina virtual Azure. Os conjuntos de balança de máquina virtual do tecido de serviço não estão atualmente suportados.
- As atualizações são aplicadas num modelo de implantação de primeira disponibilidade (detalhado abaixo).
- Quando aplicados a um conjunto de balanças de máquina virtual, não mais de 20% das máquinas virtuais de escala de máquinas serão atualizadas num único lote (sujeito a um mínimo de uma máquina virtual por lote).
- Funciona para todos os tamanhos VM, e para extensões Windows e Linux.
- Pode optar por não fazer upgrades automáticos a qualquer momento.
- A atualização automática da extensão pode ser ativada num conjunto de escala de máquina virtual de qualquer tamanho.
- Cada extensão suportada é inscrita individualmente, e pode escolher quais extensões para atualizar automaticamente.
- Apoiado em todas as regiões de nuvem pública.


> [!IMPORTANT]
> A atualização automática da extensão está atualmente em visualização pública. É necessário um procedimento de opt-in para utilizar a funcionalidade de pré-visualização pública descrita abaixo.
> Esta versão de pré-visualização é fornecida sem um contrato de nível de serviço, e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="how-does-automatic-extension-upgrade-work"></a>Como funciona a atualização automática da extensão?
O processo de atualização de extensão funciona substituindo a versão de extensão existente num VM com a nova versão de extensão publicada pela editora de extensão. A saúde do VM é monitorizada após a instalação da nova extensão. Se o VM não estiver em estado saudável no prazo de 5 minutos após a conclusão da atualização, a nova versão de extensão é revertida para a versão anterior.

Uma atualização de extensão falhada é automaticamente novamente experimentada. Uma repetição é tentada de vez em quando automaticamente sem a intervenção do utilizador.


## <a name="upgrade-process-for-virtual-machine-scale-sets"></a>Processo de upgrade para conjuntos de escala de máquina virtual
1. Antes de iniciar o processo de upgrade, o orquestrador garantirá que não mais de 20% dos VM em toda a escala definida não são saudáveis (por qualquer motivo).

2. O orquestrador de upgrade identifica o lote de instâncias VM para atualizar, com qualquer lote com um máximo de 20% da contagem total de VM, sujeito a um tamanho mínimo de lote de uma máquina virtual.

3. Para conjuntos de escala com sondas de saúde de aplicação configuradas ou extensão de Saúde de Aplicação, a atualização espera até 5 minutos (ou a configuração definida da sonda de saúde) para que o VM fique saudável, antes de avançar para atualizar o próximo lote. Se um VM não recuperar a sua saúde após uma atualização, então, por predefinição, a versão de extensão anterior para o VM é reinstalada.

4. O orquestrador de upgrade também acompanha a percentagem de VMs que se tornam insalubres após uma atualização. A atualização irá parar se mais de 20% das instâncias atualizadas ficarem insalubres durante o processo de upgrade.

O processo acima continua até que todas as instâncias do conjunto de escala tenham sido atualizadas.

A escala configurada para atualizar o orquestrador verifica a saúde global definida antes de atualizar cada lote. Ao atualizar um lote, pode haver outras atividades de manutenção planeadas ou não planeadas que possam afetar a saúde das máquinas virtuais definidas à escala. Nesses casos, se mais de 20% dos casos do conjunto de escalas ficarem insalubres, então a escala de atualização para no final do lote atual.


### <a name="availability-first-updates"></a>Atualizações de disponibilidade
O modelo de disponibilidade primeiro para atualizações orquestradas pela plataforma irá garantir que as configurações de disponibilidade no Azure sejam respeitadas em vários níveis de disponibilidade.

Para um grupo de máquinas virtuais submetidas a uma atualização, a plataforma Azure irá orquestrar atualizações:

**Em todas as regiões:**
- Uma atualização irá mover-se através do Azure globalmente de forma faseada para evitar falhas de implantação em todo o Azure.
- Uma «fase» pode constituir uma ou mais regiões, e uma atualização só se move por fases se os VM elegíveis numa fase forem atualizados com êxito.
- As regiões geo-emparelhadas não serão atualizadas simultaneamente e não podem estar na mesma fase regional.
- O sucesso de uma atualização é medido através do rastreio da saúde de uma atualização de posts VM. A saúde vM é rastreada através de indicadores de saúde da plataforma para o VM. No caso dos Conjuntos de Balanças De Máquina Virtual, a saúde VM é rastreada através de sondas de saúde de aplicação ou da extensão de Saúde da Aplicação, se aplicada ao conjunto de escalas.

**Dentro de uma região:**
- VMs em diferentes Zonas de Disponibilidade não são atualizados simultaneamente.
- Os VM únicos que não fazem parte de um conjunto de disponibilidade são lotados numa base de esforço melhor para evitar atualizações simultâneas para todos os VMs numa subscrição.  

**Dentro de um 'conjunto':**
- Todos os VMs num conjunto de disponibilidade ou escala comum não são atualizados simultaneamente.  
- Os VMs num conjunto de disponibilidade comum são atualizados dentro dos limites do Domínio de Atualização e os VMs em vários domínios de atualização não são atualizados simultaneamente.  
- Os VMs num conjunto de escala de máquina virtual comum são agrupados em lotes e atualizados dentro dos limites do Domínio de Atualização.


## <a name="supported-extensions"></a>Extensões apoiadas
A pré-visualização da Atualização Automática de Extensões suporta as seguintes extensões (e mais são adicionadas periodicamente):
- Agente de Dependência - [Windows](./extensions/agent-dependency-windows.md) e [Linux](./extensions/agent-dependency-linux.md)
- [Extensão de Saúde da Aplicação](../virtual-machine-scale-sets/virtual-machine-scale-sets-health-extension.md) - Windows e Linux


## <a name="enabling-preview-access"></a>Permitindo o acesso à pré-visualização
Ativar a funcionalidade de pré-visualização requer um opt-in único para a funcionalidade **AutomaticExtensionUpgradePreview** por subscrição, conforme descrito abaixo.

### <a name="rest-api"></a>API REST
O exemplo a seguir descreve como ativar a pré-visualização da sua subscrição:

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/AutomaticExtensionUpgradePreview/register?api-version=2015-12-01`
```

A inscrição da funcionalidade pode demorar até 15 minutos. Para verificar o estado do registo:

```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/AutomaticExtensionUpgradePreview?api-version=2015-12-01`
```

Uma vez registada a funcionalidade para a sua subscrição, complete o processo de opt-in propagando a alteração no fornecedor de recursos Compute.

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2020-06-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
Utilize o [cmdlet Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) para ativar a pré-visualização da sua subscrição.

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName AutomaticExtensionUpgradePreview -ProviderNamespace Microsoft.Compute
```

A inscrição da funcionalidade pode demorar até 15 minutos. Para verificar o estado do registo:

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName AutomaticExtensionUpgradePreview -ProviderNamespace Microsoft.Compute
```

Uma vez registada a funcionalidade para a sua subscrição, complete o processo de opt-in propagando a alteração no fornecedor de recursos Compute.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli"></a>CLI do Azure
Utilize [o registo de funcionalidades AZ](/cli/azure/feature#az-feature-register) para ativar a pré-visualização da sua subscrição.

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name AutomaticExtensionUpgradePreview
```

A inscrição da funcionalidade pode demorar até 15 minutos. Para verificar o estado do registo:

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name AutomaticExtensionUpgradePreview
```

Uma vez registada a funcionalidade para a sua subscrição, complete o processo de opt-in propagando a alteração no fornecedor de recursos Compute.

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```


## <a name="enabling-automatic-extension-upgrade"></a>Ativar a atualização automática da extensão
Para ativar a atualização automática da extensão para uma extensão, deve garantir que o ativo da propriedade *AutomaticUpgrade* está definido como *verdadeiro* e adicionado a cada definição de extensão individualmente.


### <a name="rest-api-for-virtual-machines"></a>REST API para Máquinas Virtuais
Para permitir uma atualização automática de extensão para uma extensão (neste exemplo, a extensão do Agente de Dependência) num VM Azure, utilize o seguinte:

```
PUT on `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachines/<vmName>/extensions/<extensionName>?api-version=2019-12-01`
```

```json
{    
    "name": "extensionName",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "location": "<location>",
    "properties": {
        "autoUpgradeMinorVersion": true,
        "enableAutomaticUpgrade": true, 
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5"
        }
}
```

### <a name="rest-api-for-virtual-machine-scale-sets"></a>REST API para conjuntos de balanças de máquinas virtuais
Utilize o seguinte para adicionar a extensão ao modelo de conjunto de escala:

```
PUT on `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachineScaleSets/<vmssName>?api-version=2019-12-01`
```

```json
{
   "location": "<location>",
   "properties": {
        "virtualMachineProfile": {
            "extensionProfile": {
                "extensions": [
                {
                "name": "<extensionName>",
                  "properties": {
                        "autoUpgradeMinorVersion": true,
                        "enableAutomaticUpgrade": true,
                    "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
                    "type": "DependencyAgentWindows",
                    "typeHandlerVersion": "9.5"
                    }
                }
                ]
            }
        }
    }
}
```

### <a name="azure-powershell-for-virtual-machines"></a>Azure PowerShell para máquinas virtuais
Utilize o [cmdlet Set-AzVMExtension:](/powershell/module/az.compute/set-azvmextension)

```azurepowershell-interactive
Set-AzVMExtension -ExtensionName "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ExtensionType "DependencyAgentWindows" `
    -TypeHandlerVersion 9.5 `
    -Location WestUS `
    -EnableAutomaticUpgrade $true
```


### <a name="azure-powershell-for-virtual-machine-scale-sets"></a>Azure PowerShell para conjuntos de balança de máquinas virtuais
Utilize o [cmdlet Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) para adicionar a extensão ao modelo de conjunto de escala:

```azurepowershell-interactive
Add-AzVmssExtension -VirtualMachineScaleSet $vmss
    -Name "Microsoft.Azure.Monitoring.DependencyAgent" `
    -Publisher "Microsoft.Azure.Monitoring.DependencyAgent" `
    -Type "DependencyAgentWindows" `
    -TypeHandlerVersion 9.5 `
    -EnableAutomaticUpgrade $true
```

Atualize o conjunto de escala utilizando [Update-AzVmss](/powershell/module/az.compute/update-azvmss) depois de adicionar a extensão.


### <a name="azure-cli-for-virtual-machines"></a>CLI do Azure para Máquinas Virtuais
Utilize o [cmdlet de extensão az vm:](/cli/azure/vm/extension#az_vm_extension_set)

```azurecli-interactive
az vm extension set \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name DependencyAgentLinux \
    --publisher Microsoft.Azure.Monitoring.DependencyAgent \
    --version 9.5 \
    --enable-auto-upgrade true
```

### <a name="azure-cli-for-virtual-machine-scale-sets"></a>Azure CLI para conjuntos de balança de máquinas virtuais
Utilize o cmdlet [conjunto de extensão az vmss](/cli/azure/vmss/extension#az_vmss_extension_set) para adicionar a extensão ao modelo definido em escala:

```azurecli-interactive
az vmss extension set \
    --resource-group myResourceGroup \
    --vmss-name myVMSS \
    --name DependencyAgentLinux \
    --publisher Microsoft.Azure.Monitoring.DependencyAgent \
    --version 9.5 \
    --enable-auto-upgrade true
```

## <a name="extension-upgrades-with-multiple-extensions"></a>Atualizações de extensão com múltiplas extensões

Um Conjunto de Balanças VM ou Máquina Virtual pode ter múltiplas extensões com atualização de extensão automática ativada, além de outras extensões sem atualizações automáticas de extensão.  

Se várias atualizações de extensão estiverem disponíveis para uma máquina virtual, as atualizações podem ser emgrurentas. No entanto, cada atualização de extensão é aplicada individualmente numa máquina virtual. Uma falha numa extensão não afeta a outra extensão que pode estar a ser melhorada. Por exemplo, se duas extensões estiverem agendadas para uma atualização, e a primeira atualização de extensão falhar, a segunda extensão ainda será atualizada.

As atualizações automáticas de extensão também podem ser aplicadas quando um conjunto de escala de VM ou de máquina virtual tem múltiplas extensões configuradas com [sequenciação de extensão](../virtual-machine-scale-sets/virtual-machine-scale-sets-extension-sequencing.md). A sequência de extensão é aplicável para a primeira implantação do VM, e quaisquer atualizações subsequentes de extensão de uma extensão são aplicadas de forma independente.


## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Conheça a Extensão de Saúde da Aplicação](./windows/automatic-vm-guest-patching.md)
