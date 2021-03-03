---
title: Patching automático de hóspedes VM para VMs Azure
description: Aprenda a remendar automaticamente as máquinas virtuais em Azure.
author: mayanknayar
ms.service: virtual-machines
ms.subservice: automatic-guest-patching
ms.collection: windows
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/17/2021
ms.author: manayar
ms.openlocfilehash: 276762bc2b8624f687cbb77e1af771478791a57b
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101680315"
---
# <a name="preview-automatic-vm-guest-patching-for-azure-vms"></a>Pré-visualização: Patching automático de hóspedes VM para VMs Azure

Permitir o patch de hóspedes VM automático para os seus VMs Azure ajuda a facilitar a gestão da atualização, remendando de forma segura e automática máquinas virtuais para manter a conformidade com a segurança.

O patching automático de hóspedes VM tem as seguintes características:
- Patches classificados como *Critical* ou *Security* são automaticamente descarregados e aplicados no VM.
- As manchas são aplicadas durante as horas de ponta no fuso horário do VM.
- A orquestração de remendos é gerida pelo Azure e os patches são aplicados seguindo [os princípios de disponibilidade primeiro.](#availability-first-patching)
- A saúde da máquina virtual, tal como determinada através de sinais de saúde da plataforma, é monitorizada para detetar falhas de correção.
- Funciona para todos os tamanhos VM.

> [!IMPORTANT]
> O patching automático de hóspedes VM está atualmente em Visualização Pública. É necessário um procedimento de opt-in para utilizar a funcionalidade de pré-visualização pública descrita abaixo.
> Esta versão de pré-visualização é fornecida sem um contrato de nível de serviço, e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="how-does-automatic-vm-guest-patching-work"></a>Como funciona o patching automático de hóspedes VM?

Se o patching automático de hóspedes VM estiver ativado num VM, então os *patches* de Segurança e *Crítica* disponíveis são descarregados e aplicados automaticamente no VM. Este processo arranca automaticamente todos os meses quando são lançados novos patches. A avaliação e instalação do patch são automáticas, e o processo inclui o reinício do VM conforme necessário.

O VM é avaliado periodicamente de dois em poucos dias e várias vezes em qualquer período de 30 dias para determinar os patches aplicáveis para esse VM. As correções podem ser instaladas em qualquer dia no VM durante as horas de ponta para o VM. Esta avaliação automática garante que quaisquer patches em falta sejam descobertos o mais rapidamente possível.

Os patches são instalados no prazo de 30 dias após os lançamentos mensais do patch, após a primeira orquestração de disponibilidade descrita abaixo. As correções são instaladas apenas durante as horas de ponta para o VM, dependendo do fuso horário do VM. O VM deve estar a funcionar durante as horas de ponta para que os patches sejam automaticamente instalados. Se um VM for desligado durante uma avaliação periódica, o VM será automaticamente avaliado e os patches aplicáveis serão instalados automaticamente durante a próxima avaliação periódica (geralmente dentro de alguns dias) quando o VM é ligado.

Atualizações de definição e outros patches não classificados como *Critical* ou *Security* não serão instalados através de remendos automáticos de hóspedes VM. Para instalar patches com outras classificações de patches ou programar a instalação de patch dentro da sua própria janela de manutenção personalizada, pode utilizar [a Gestão de Atualização](./windows/tutorial-config-management.md#manage-windows-updates).

### <a name="availability-first-patching"></a>Patching de disponibilidade primeiro

O processo de instalação do patch é orquestrado globalmente pela Azure para todos os VMs que tenham remendos automáticos de hóspedes VM ativados. Esta orquestração segue os princípios de disponibilidade em diferentes níveis de disponibilidade fornecidos pela Azure.

Para um grupo de máquinas virtuais submetidas a uma atualização, a plataforma Azure irá orquestrar atualizações:

**Em todas as regiões:**
- Uma atualização mensal é orquestrada em todo o Azure globalmente de forma faseada para evitar falhas de implantação global.
- Uma fase pode ter uma ou mais regiões, e uma atualização passa para as fases seguintes apenas se vMs elegíveis numa atualização de fase com sucesso.
- As regiões geo emparelhadas não são atualizadas simultaneamente e não podem estar na mesma fase regional.
- O sucesso de uma atualização é medido através do acompanhamento da atualização do post de saúde do VM. A VM Health é rastreada através de indicadores de saúde da plataforma para o VM.

**Dentro de uma região:**
- VMs em diferentes Zonas de Disponibilidade não são atualizados simultaneamente.
- Os VMs que não fazem parte de um conjunto de disponibilidade são lotados numa base de esforço melhor para evitar atualizações simultâneas para todos os VMs numa subscrição.

**Dentro de um conjunto de disponibilidade:**
- Todos os VMs num conjunto de disponibilidade comum não são atualizados simultaneamente.
-   Os VMs num conjunto de disponibilidade comum são atualizados dentro dos limites do Domínio de Atualização e os VMs em vários domínios de atualização não são atualizados simultaneamente.

A data de instalação do patch para um determinado VM pode variar de mês para mês, uma vez que um VM específico pode ser recolhido num lote diferente entre ciclos de remendos mensais.

### <a name="which-patches-are-installed"></a>Que patches estão instalados?
Os patches instalados dependem da fase de lançamento do VM. Todos os meses, inicia-se um novo lançamento global onde todos os patches de segurança e críticos avaliados para um VM individual são instalados para esse VM. O lançamento é orquestrado em todas as regiões de Azure em lotes (descrito na secção de remendos de disponibilidade acima).

O conjunto exato de patches a instalar varia em função da configuração VM, incluindo o tipo de SO e o tempo de avaliação. É possível que dois VMs idênticos em diferentes regiões obtenham diferentes patches instalados se houver mais ou menos patches disponíveis quando a orquestração de patchs atinge diferentes regiões em diferentes momentos. Da mesma forma, mas com menos frequência, os VMs dentro da mesma região, mas avaliados em momentos diferentes (devido a diferentes lotes de Availability Zone ou Availability set) podem obter diferentes patches.

Como o Patching Automático de Hóspedes VM não configura a fonte de remendo, dois VMs semelhantes configurados para diferentes fontes de correção, tais como repositório público vs repositório privado, também podem ver uma diferença no conjunto exato de patches instalados.

Para os tipos de OS que libertam patches numa cadência fixa, os VM configurados para o repositório público para o SO podem esperar receber o mesmo conjunto de patches nas diferentes fases de lançamento num mês. Por exemplo, os VM do Windows configurados para o repositório público do Windows Update.

À medida que um novo lançamento é ativado todos os meses, um VM receberá pelo menos um patch de lançamento todos os meses se o VM for ligado durante as horas de ponta. Isto garante que o VM é remendado com as mais recentes correções de segurança e críticas disponíveis mensalmente. Para garantir a consistência no conjunto de patches instalados, pode configurar os seus VMs para avaliar e descarregar patches dos seus próprios repositórios privados.

## <a name="supported-os-images"></a>Imagens de SO suportadas
Apenas VMs criados a partir de certas imagens da plataforma OS são atualmente suportados na pré-visualização. As imagens personalizadas não são suportadas na pré-visualização.

As seguintes plataformas SKUs são atualmente suportadas (e mais são adicionadas periodicamente):

| Publisher               | Oferta de OS      |  Sku               |
|-------------------------|---------------|--------------------|
| Canónico  | UbuntuServer | 18.04-LTS |
| Redhat  | RHEL | 7.x |
| MicrosoftWindowsServer  | WindowsServer | 2012-R2-Datacenter |
| MicrosoftWindowsServer  | WindowsServer | Centro de Dados de 2016    |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter-Server-Core |
| MicrosoftWindowsServer  | WindowsServer | Centro de Dados 2019 |
| MicrosoftWindowsServer  | WindowsServer | 2019-Datacenter-Core |

## <a name="patch-orchestration-modes"></a>Modos de orquestração de remendos
Os VMs em Azure suportam agora os seguintes modos de orquestração de remendos:

**AutomáticoByPlatform:**
- Este modo é suportado tanto para Os VMs Do Linux como do Windows.
- Este modo permite o remendamento automático do hóspede VM para a máquina virtual e a instalação de remendos subsequente é orquestrada pela Azure.
- Este modo é necessário para a primeira correção de disponibilidade.
- Este modo é suportado apenas para VMs que são criados usando as imagens da plataforma oss suportadas acima.
- Para os VMs do Windows, a definição deste modo também desativa as atualizações automáticas nativas na máquina virtual do Windows para evitar duplicações.
- Para utilizar este modo em VMs Linux, desajuste a propriedade `osProfile.linuxConfiguration.patchSettings.patchMode=AutomaticByPlatform` no modelo VM.
- Para utilizar este modo em VMs do Windows, desajuste a propriedade `osProfile.windowsConfiguration.enableAutomaticUpdates=true` e define a propriedade no modelo  `osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByPlatform` VM.

**AutomaticamenteByOS:**
- Este modo é suportado apenas para VMs Windows.
- Este modo permite atualizações automáticas na máquina virtual do Windows e os patches são instalados no VM através de Atualizações Automáticas.
- Este modo não suporta a primeira correção de disponibilidade.
- Este modo é definido por predefinição se nenhum outro modo de remendos for especificado para um VM do Windows.
- Para utilizar este modo em VMs do Windows, desajuste a propriedade `osProfile.windowsConfiguration.enableAutomaticUpdates=true` e define a propriedade no modelo  `osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByOS` VM.

**Manual:**
- Este modo é suportado apenas para VMs Windows.
- Este modo desativa atualizações automáticas na máquina virtual do Windows.
- Este modo não suporta a primeira correção de disponibilidade.
- Este modo deve ser definido quando se utilizam soluções de remendos personalizadas.
- Para utilizar este modo em VMs do Windows, desajuste a propriedade `osProfile.windowsConfiguration.enableAutomaticUpdates=false` e define a propriedade no modelo  `osProfile.windowsConfiguration.patchSettings.patchMode=Manual` VM.

**ImageDefault:**
- Este modo é suportado apenas para Os VMs Linux.
- Este modo não suporta a primeira correção de disponibilidade.
- Este modo honra a configuração de remendação padrão na imagem utilizada para criar o VM.
- Este modo é definido por predefinição se nenhum outro modo de remendos for especificado para um Linux VM.
- Para utilizar este modo em VMs Linux, desajuste a propriedade `osProfile.linuxConfiguration.patchSettings.patchMode=ImageDefault` no modelo VM.

> [!NOTE]
>Para o Windows VMs, a propriedade `osProfile.windowsConfiguration.enableAutomaticUpdates` só pode ser definida quando o VM é criado pela primeira vez. Atualmente, não é suportada a passagem de Manual para modo Automático ou de modo automático para modo Manual. É suportada a passagem do modo AutomaticByOS para o modo AutomáticaByPlatfom.

## <a name="requirements-for-enabling-automatic-vm-guest-patching"></a>Requisitos para permitir o patching automático de hóspedes VM

- A máquina virtual deve ter o Agente VM Azure para [Windows](./extensions/agent-windows.md) ou [Linux](./extensions/agent-linux.md) instalado.
- Para os VMs Linux, o agente Azure Linux deve ser a versão 2.2.53.1 ou superior. [Atualize o agente Linux](./extensions/update-linux-agent.md) se a versão atual for inferior à versão necessária.
- Para os VMs do Windows, o serviço De Atualização do Windows deve estar a funcionar na máquina virtual.
- A máquina virtual deve poder aceder aos pontos finais de atualização configurados. Se a sua máquina virtual estiver configurada para utilizar repositórios privados para serviços de atualização de Linux ou Windows Server (WSUS) para VMs do Windows, os pontos finais de atualização relevantes devem estar acessíveis.
- Use a versão API compute 2020-12-01 ou superior. A versão API compute 2020-06-01 pode ser utilizada para VMs do Windows com funcionalidade limitada.

Ativar a funcionalidade de pré-visualização requer um opt-in único para as funcionalidades **InGuestAutoPatchVMPreview** e **InGuestPatchVMPreview** por subscrição, conforme detalhado na secção seguinte.

### <a name="rest-api"></a>API REST
O exemplo a seguir descreve como ativar a pré-visualização da sua subscrição:

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview/register?api-version=2015-12-01`
```
```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestPatchVMPreview/register?api-version=2015-12-01`
```

A inscrição da funcionalidade pode demorar até 15 minutos. Para verificar o estado do registo:

```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview?api-version=2015-12-01`
```
```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestPatchVMPreview?api-version=2015-12-01`
```
Uma vez registada a funcionalidade para a sua subscrição, complete o processo de opt-in propagando a alteração no fornecedor de recursos Compute.

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2020-06-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
Utilize o [cmdlet Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) para ativar a pré-visualização da sua subscrição.

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
Register-AzProviderFeature -FeatureName InGuestPatchVMPreview -ProviderNamespace Microsoft.Compute
```

A inscrição da funcionalidade pode demorar até 15 minutos. Para verificar o estado do registo:

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
Get-AzProviderFeature -FeatureName InGuestPatchVMPreview -ProviderNamespace Microsoft.Compute
```

Uma vez registada a funcionalidade para a sua subscrição, complete o processo de opt-in propagando a alteração no fornecedor de recursos Compute.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli-20"></a>CLI 2.0 do Azure
Utilize [o registo de funcionalidades AZ](/cli/azure/feature#az-feature-register) para ativar a pré-visualização da sua subscrição.

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview `
az feature register --namespace Microsoft.Compute --name InGuestPatchVMPreview
```

A inscrição da funcionalidade pode demorar até 15 minutos. Para verificar o estado do registo:

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview `
az feature show --namespace Microsoft.Compute --name InGuestPatchVMPreview
```

Uma vez registada a funcionalidade para a sua subscrição, complete o processo de opt-in propagando a alteração no fornecedor de recursos Compute.

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```

## <a name="enable-automatic-vm-guest-patching"></a>Ative a aplicação de patches automática de convidado da VM
Para permitir o remendamento automático do hóspede VM num VM do Windows, certifique-se de que o *osProfile.windowsConfiguration.enableAutomaticUpdates* está definido como *verdadeiro* na definição do modelo VM. Esta propriedade só pode ser definida ao criar o VM. Esta propriedade adicional não é aplicável para Os VMs Linux.

### <a name="rest-api-for-linux-vms"></a>REST API para LMs Linux
O exemplo a seguir descreve como permitir a remendação automática de hóspedes VM:

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine?api-version=2020-12-01`
```

```json
{
  "properties": {
    "osProfile": {
      "linuxConfiguration": {
        "provisionVMAgent": true,
        "patchSettings": {
          "patchMode": "AutomaticByPlatform"
        }
      }
    }
  }
}
```

### <a name="rest-api-for-windows-vms"></a>REST API para VMs windows
O exemplo a seguir descreve como permitir a remendação automática de hóspedes VM:

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine?api-version=2020-06-01`
```

```json
{
  "properties": {
    "osProfile": {
      "windowsConfiguration": {
        "provisionVMAgent": true,
        "enableAutomaticUpdates": true,
        "patchSettings": {
          "patchMode": "AutomaticByPlatform"
        }
      }
    }
  }
}
```

### <a name="azure-powershell-for-windows-vms"></a>Azure PowerShell para VMs windows
Utilize o [cmdlet Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) para permitir a remendação automática de hóspedes VM ao criar ou atualizar um VM.

```azurepowershell-interactive
Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate -PatchMode "AutomaticByPlatform"
```

### <a name="azure-cli-for-windows-vms"></a>CLI Azure para VMs windows
Utilize [a az vm criar](/cli/azure/vm#az-vm-create) para permitir remendos automáticos de hóspedes VM ao criar um novo VM. O exemplo a seguir configura o patching automático de hóspedes VM para um VM nomeado *myVM* no grupo de recursos chamado *myResourceGroup*:

```azurecli-interactive
az vm create --resource-group myResourceGroup --name myVM --image Win2019Datacenter --enable-agent --enable-auto-update --patch-mode AutomaticByPlatform
```

Para modificar um VM existente, utilize [a atualização az vm](/cli/azure/vm#az-vm-update)

```azurecli-interactive
az vm update --resource-group myResourceGroup --name myVM --set osProfile.windowsConfiguration.enableAutomaticUpdates=true osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByPlatform
```

## <a name="enablement-and-assessment"></a>Habilitação e avaliação

> [!NOTE]
>Pode levar mais de três horas para permitir atualizações automáticas de hóspedes VM num VM, uma vez que a ativação é completada durante as horas de pico do VM. Uma vez que a avaliação e a instalação do remendo ocorrem apenas durante as horas de ponta, o seu VM também deve estar a funcionar durante as horas de ponta para aplicar patches.

Quando o patching automático do hóspede VM está ativado para um VM, uma extensão vm do tipo `Microsoft.CPlat.Core.LinuxPatchExtension` é instalada num VM Linux ou uma extensão vm de tipo `Microsoft.CPlat.Core.WindowsPatchExtension` é instalada num VM do Windows. Esta extensão não necessita de ser instalada manualmente ou atualizada, uma vez que esta extensão é gerida pela plataforma Azure como parte do processo automático de remendos de hóspedes VM.

Pode levar mais de três horas para permitir atualizações automáticas de hóspedes VM num VM, uma vez que a ativação é completada durante as horas de pico do VM. A extensão também é instalada e atualizada durante as horas de ponta para o VM. Se as horas de pico do VM terminarem antes de poder ser concluída a ativação, o processo de ativação será retomado durante o próximo tempo fora do pico disponível.

As atualizações automáticas são desativadas na maioria dos cenários, e a instalação do patch é feita através da extensão em curso. Aplicam-se as seguintes condições.
- Se um VM do Windows previamente tivesse a Atualização Automática do Windows ligada através do modo de patch AutomáticoByOS, então a Atualização Automática do Windows será desligada para o VM quando a extensão for instalada.
- Para os VMs Ubuntu, as atualizações automáticas predefinidos são desativadas automaticamente quando o Remendos Automáticos de Convidados VM completa a ativação.
- Para o RHEL, as atualizações automáticas devem ser desativadas manualmente (trata-se de uma limitação de pré-visualização). Executar:

```
systemctl stop packagekit
```

```
systemctl mask packagekit
```

Para verificar se o patching automático do hóspede VM foi concluído e a extensão de remendação está instalada no VM, pode rever a visão de exemplo do VM. Se o processo de habilitação estiver concluído, a extensão será instalada e os resultados de avaliação para o VM estarão disponíveis em `patchStatus` . A visão de exemplo do VM pode ser acedida através de várias formas, conforme descrito abaixo.

### <a name="rest-api"></a>API REST

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine/instanceView?api-version=2020-12-01`
```
### <a name="azure-powershell"></a>Azure PowerShell
Utilize o [cmdlet Get-AzVM](/powershell/module/az.compute/get-azvm) com o `-Status` parâmetro para aceder à vista de exemplo para o seu VM.

```azurepowershell-interactive
Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM" -Status
```

Atualmente, a PowerShell apenas fornece informações sobre a extensão do patch. As informações `patchStatus` sobre também estarão disponíveis em breve através do PowerShell.

### <a name="azure-cli"></a>CLI do Azure
Utilize [a visão de instância az vm](/cli/azure/vm#az-vm-get-instance-view) para aceder à vista de exemplo para o seu VM.

```azurecli-interactive
az vm get-instance-view --resource-group myResourceGroup --name myVM
```

### <a name="understanding-the-patch-status-for-your-vm"></a>Compreender o estado do patch para o seu VM

A `patchStatus` secção da resposta da visualização do exemplo fornece detalhes sobre a última avaliação e a última instalação de patch para o seu VM.

Os resultados da avaliação do seu VM podem ser revistos na `availablePatchSummary` secção. É realizada periodicamente uma avaliação para um VM que tem remendos automáticos de hóspedes VM habilitados. A contagem de patches disponíveis após uma avaliação é fornecida sob `criticalAndSecurityPatchCount` e `otherPatchCount` resultados. O patching automático de hóspedes VM instalará todos os patches avaliados no âmbito das classificações de patch *de Critical* and *Security.* Qualquer outro patch avaliado é ignorado.

Os resultados da instalação do patch para o seu VM podem ser revistos na `lastPatchInstallationSummary` secção. Esta secção fornece detalhes sobre a última tentativa de instalação do patch no VM, incluindo o número de patches que foram instalados, pendentes, falhados ou ignorados. As correções são instaladas apenas durante a janela de manutenção fora do pico para o VM. As correções pendentes e falhadas são automaticamente novamente experimentadas durante a próxima janela de manutenção fora do pico.

## <a name="on-demand-patch-assessment"></a>Avaliação do patch a pedido
Se o patching automático de hóspedes VM já estiver ativado para o seu VM, é efetuada uma avaliação periódica de correção no VM durante as horas de ponta do VM. Este processo é automático e os resultados da última avaliação podem ser revistos através da visão de exemplo da VM, tal como descrito anteriormente neste documento. Também pode desencadear uma avaliação de patch a pedido para o seu VM a qualquer momento. A avaliação do patch pode demorar alguns minutos a ser concluída e o estado da última avaliação é atualizado na opinião de exemplo do VM.

Ativar a funcionalidade de pré-visualização requer um opt-in único para a funcionalidade **InGuestPatchVMPreview** por subscrição. Esta pré-visualização de funcionalidade é diferente da inscrição automática de patching de hóspedes VM feita anteriormente para **InGuestAutoPatchVMPreview**. Permitir a pré-visualização adicional da funcionalidade é um requisito separado e adicional. A pré-visualização da funcionalidade para avaliação do patch on-demand pode ser ativada após o processo de [pré-visualização](automatic-vm-guest-patching.md#requirements-for-enabling-automatic-vm-guest-patching) descrito anteriormente para remendos automáticos de hóspedes VM.

> [!NOTE]
>A avaliação do patch a pedido não aciona automaticamente a instalação do patch. Se tiver ativado o remendos automáticos de hóspedes VM, os patches avaliados e aplicáveis para o VM serão instalados durante as horas de ponta do VM, seguindo o processo de remendação de disponibilidade descrito anteriormente neste documento.

### <a name="rest-api"></a>API REST
```
POST on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine/assessPatches?api-version=2020-12-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
Utilize o [cmdlet Invoke-AzVmPatchAssessment](/powershell/module/az.compute/invoke-azvmpatchassessment) para avaliar as correções disponíveis para a sua máquina virtual.

```azurepowershell-interactive
Invoke-AzVmPatchAssessment -ResourceGroupName "myResourceGroup" -VMName "myVM"
```

### <a name="azure-cli"></a>CLI do Azure
Utilize [patches de avaliação az vm](/cli/azure/vm#az-vm-assess-patches) para avaliar os patches disponíveis para a sua máquina virtual.

```azurecli-interactive
az vm assess-patches --resource-group myResourceGroup --name myVM
```

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Saiba mais sobre a criação e gestão de máquinas virtuais do Windows](./windows/tutorial-manage-vm.md)
