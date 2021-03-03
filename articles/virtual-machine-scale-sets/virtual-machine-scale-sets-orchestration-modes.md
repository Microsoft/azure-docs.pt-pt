---
title: Modos de orquestração para conjuntos de escala de máquina virtual em Azure
description: Aprenda a utilizar modos de orquestração flexíveis e uniformes para conjuntos de escala de máquinas virtuais em Azure.
author: fitzgeraldsteele
ms.author: fisteele
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: extensions
ms.date: 02/12/2021
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 1a276915f8132f2941bfb4425011c2d34454093b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101693888"
---
# <a name="preview-orchestration-modes-for-virtual-machine-scale-sets-in-azure"></a>Pré-visualização: Modos de orquestração para conjuntos de escala de máquina virtual em Azure 

Os conjuntos de escala de máquinas virtuais fornecem um agrupamento lógico de máquinas virtuais geridas pela plataforma. Com os conjuntos de escala, cria-se um modelo de configuração de máquina virtual, adiciona automaticamente ou remove casos adicionais com base na cpu ou na carga de memória e atualiza-se automaticamente para a versão mais recente do SO. Tradicionalmente, os conjuntos de escala permitem criar máquinas virtuais utilizando um modelo de configuração VM fornecido no momento da criação do conjunto de escala, e o conjunto de escala só pode gerir máquinas virtuais que são criadas implicitamente com base no modelo de configuração. 

Os modos de orquestração definidos em escala permitem-lhe ter um maior controlo sobre como as instâncias de máquinas virtuais são geridas pelo conjunto de escalas. 

> [!IMPORTANT]
> O modo de orquestração é definido quando cria o conjunto de escala e não pode ser alterado ou atualizado mais tarde.


## <a name="scale-sets-with-uniform-orchestration"></a>Conjuntos de escala com orquestração uniforme
Otimizado para cargas apátridas em larga escala com instâncias idênticas.

Conjuntos de escala de máquina virtual com orquestração uniforme use um perfil ou modelo de máquina virtual para escalar até a capacidade desejada. Embora exista alguma capacidade de gerir ou personalizar instâncias individuais de máquinas virtuais, a Uniform utiliza instâncias VM idênticas. As instâncias VM uniformes individuais são expostas através dos comandos VM API da escala de máquina virtual. As instâncias individuais não são compatíveis com os comandos Azure IaaS VM API padrão, funcionalidades de gestão Azure, tais como recursos Azure Resource Manager que marcam permissões de RBAC, Backup Azure ou Recuperação do Local Azure. A orquestração uniforme oferece garantias de alta disponibilidade do domínio de falhas quando configuradas com menos de 100 instâncias. A orquestração uniforme está geralmente disponível e suporta uma gama completa de gestão e orquestração de conjuntos de escala, incluindo autoscalagem baseadas em métricas, proteção de instâncias e upgrades automáticos de SO. 


## <a name="scale-sets-with-flexible-orchestration"></a>Conjuntos de escala com orquestração flexível 
Obtenha uma elevada disponibilidade à escala com tipos de máquinas virtuais idênticos ou múltiplos.

Com orquestração flexível, a Azure proporciona uma experiência unificada em todo o ecossistema Azure VM. A orquestração flexível oferece garantias de alta disponibilidade (até 1000 VMs) espalhando VMs através de domínios de avaria numa região ou dentro de uma Zona de Disponibilidade. Isto permite-lhe aumentar a sua aplicação mantendo o isolamento do domínio de falhas que é essencial para executar cargas de trabalho baseadas em quórum ou stateful, incluindo:
- Cargas de trabalho baseadas em quórum
- bases de dados Open-Source
- Aplicações imponentes
- Serviços que requerem Alta Disponibilidade e grande escala
- Serviços que querem misturar tipos de máquinas virtuais, ou alavancar spot e VMs a pedido juntos
- Aplicações de conjunto de disponibilidade existentes  

> [!IMPORTANT]
> Os conjuntos de escala de máquina virtual no modo de orquestração flexível estão atualmente em pré-visualização pública. É necessário um procedimento de opt-in para utilizar a funcionalidade de pré-visualização pública descrita abaixo.
> Esta versão de pré-visualização é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="what-has-changed-with-flexible-orchestration-mode"></a>O que mudou com o modo de orquestração flexível?
Uma das principais vantagens da orquestração flexível é que fornece características de orquestração sobre os VMs Azure IaaS padrão, em vez de máquinas virtuais infantis em escala. Isto significa que pode utilizar todas as APIs padrão de VM ao gerir instâncias de orquestração flexíveis, em vez do conjunto virtual de VM APIs de escala de máquina que utiliza com orquestração uniforme. Durante o período de pré-estreia, existem várias diferenças entre a gestão de instâncias na orquestração flexível versus orquestração uniforme. Em geral, recomendamos que utilize as APS VM standard Azure IaaS quando possível. Nesta secção, destacamos exemplos de boas práticas para a gestão de instâncias VM com orquestração flexível.  

### <a name="assign-fault-domain-during-vm-creation"></a>Atribuir domínio de falha durante a criação de VM
Pode escolher o número de domínios de avaria para o conjunto de escala de orquestração flexível. Por predefinição, quando adiciona um VM a um conjunto de escala flexível, o Azure espalha igualmente as instâncias através de domínios de avaria. Embora seja recomendado que o Azure atribua o domínio de avaria, para cenários avançados ou de resolução de problemas pode anular este comportamento predefinido e especificar o domínio de avaria onde a instância irá aterrar.

```azurecli-interactive 
az vm create –vmss "myVMSS"  –-platform_fault_domain 1
```

### <a name="instance-naming"></a>Nomeação de exemplo 
Quando cria um VM e o adiciona a um conjunto de escala flexível, tem total controlo sobre os nomes de instâncias dentro das regras da convenção Azure Naming. Quando os VMs são automaticamente adicionados à escala definida através de autoscalagem, fornece-se um prefixo e o Azure anexa um número único ao final do nome.

### <a name="query-instances-for-power-state"></a>Casos de consulta para estado de poder
O método preferido é utilizar o Gráfico de Recursos Azure para consultar todos os VMs num Conjunto de Escala de Máquina Virtual. O Azure Resource Graph fornece capacidades de consulta eficientes para os recursos Azure em escala através de subscrições. 

``` 
| where type =~ 'Microsoft.Compute/virtualMachines' 
| where properties.virtualMachineScaleSet contains "demo" 
| extend powerState = properties.extended.instanceView.powerState.code 
| project name, resourceGroup, location, powerState 
| order by resourceGroup desc, name desc 
```

Consultar recursos com [o Azure Resource Graph](../governance/resource-graph/overview) é uma forma conveniente e eficiente de consultar os recursos da Azure e minimiza as chamadas de API para o fornecedor de recursos. O Azure Resource Graph é uma cache eventualmente consistente onde recursos novos ou atualizados podem não ser refletidos até 60 segundos. Pode:
- Listar VMs em um grupo de recursos ou subscrição.
- Utilize a opção de expansão para recuperar a visualização do caso (estados de atribuição de domínio de avaria, potência e provisionamento) para todos os VMs na sua subscrição.
- Utilize a API Get VM e os comandos para obter a visualização do modelo e da instância para uma única instância.

### <a name="scale-sets-vm-batch-operations"></a>Escala define operações de lote VM
Utilize os comandos VM padrão para iniciar, parar, reiniciar, eliminar instâncias, em vez das APIs de Série de Máquinas Virtuais. As operações de conjunto de escala de máquina virtual VM Batch (iniciar tudo, parar tudo, reimagem all, etc.) não são utilizadas com o modo de orquestração flexível. 

### <a name="monitor-application-health"></a>Monitorizar o estado de funcionamento de aplicações 
A monitorização de saúde da aplicação permite que a sua aplicação forneça ao Azure um batimento cardíaco para determinar se a sua aplicação é saudável ou pouco saudável. O Azure pode substituir automaticamente as instâncias VM que não são saudáveis. Para instâncias de escala flexível, deve instalar e configurar a Extensão de Saúde da Aplicação na máquina virtual. Para instâncias de escala uniforme, pode utilizar a Extensão de Saúde da Aplicação ou medir a saúde com uma sonda de saúde personalizada Azure Load Balancer. 

### <a name="list-scale-sets-vm-api-changes"></a>Escala de lista define alterações de VM API 
Os conjuntos de escala de máquina virtual permitem-lhe listar as instâncias que pertencem ao conjunto de escalas. Com orquestração flexível, a lista virtual de conjuntos de escala VM fornece uma lista de conjuntos de escala VM IDs. Em seguida, pode ligar para os comandos VM de escala de máquina virtual GET para obter mais detalhes sobre como o conjunto de escala está a funcionar com a instância VM. Para obter todos os detalhes do VM, utilize os comandos STANDARD GET VM ou [Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview). 

### <a name="retrieve-boot-diagnostics-data"></a>Recuperar dados de diagnóstico de arranque 
Utilize as APIs e comandos padrão para recuperar dados e imagens de boot diagnostics de exemplo. As placas de escala de máquina virtual VM de diagnóstico de botas APIs e comandos não são usados com instâncias de modo de orquestração flexível.

### <a name="vm-extensions"></a>Extensões de VM 
Utilize extensões direcionadas para máquinas virtuais padrão, em vez de extensões direcionadas para instâncias uniformes de modo de orquestração.


## <a name="a-comparison-of-flexible-uniform-and-availability-sets"></a>Uma comparação de conjuntos flexíveis, uniformes e de disponibilidade 
A tabela a seguir compara o modo de orquestração flexível, o modo de orquestração uniforme e os conjuntos de disponibilidade pelas suas características.

| Funcionalidade | Apoiado por orquestração flexível (Preview) | Apoiado por orquestração uniforme (Disponibilidade Geral) | Suportado por AvSets (Disponibilidade Geral) |
|-|-|-|-|
|         Tipo de máquina virtual  | Standard Azure IaaS VM (Microsoft.compute /virtualmachines)  | Conjunto de escala VMs específicos (Microsoft.compute /virtualmachinescalesets/virtualmachines)  | Standard Azure IaaS VM (Microsoft.compute /virtualmachines)  |
|         SKUs apoiado  |            Série D, sérieS E, Série F, Série A, Série B, Intel, AMD  |            Todos os SKUs  |            Todos os SKUs  |
|         Zonas de Disponibilidade  |            Especificar opcionalmente todas as instâncias aterram numa única zona de disponibilidade |            Especificar casos de terra em 1, 2 ou 3 zonas de disponibilidade  |            Não suportado  |
|         Controlo total sobre VM, NICs, Discos  |            Sim  |            Controlo limitado com conjuntos de escala de máquina virtual VM API  |            Sim  |
|         Escala automática  |            Não  |            Sim  |            Não  |
|         Atribuir VM a um domínio de falha específico  |            Sim  |             Não   |            Não  |
|         Remover NICs e Discos ao eliminar instâncias de VM  |            Não  |            Sim  |            Não  |
|         Política de atualização (conjuntos de escala VM) |            Não  |            Automático, Rolante, Manual  |            N/D  |
|         Atualizações automáticas do SO (conjuntos de escala VM) |            Não  |            Sim  |            N/D  |
|         Em Patching de Segurança de Hóspedes  |            Sim  |            Não  |            Sim  |
|         Notificações terminais (conjuntos de escala VM) |            Não  |            Sim  |            N/D  |
|         Reparação de instâncias (conjuntos de escala VM) |            Não  |            Sim   |            N/D  |
|         Redes aceleradas  |            Sim  |            Sim  |            Sim  |
|         Instâncias à vista e preços   |            Sim, pode ter instâncias prioritárias do Spot e do Regular  |            Sim, as instâncias devem ser todas spot ou todos regulares  |            Não, instâncias prioritárias regulares apenas  |
|         Misturar sistemas operativos  |            Sim, Linux e Windows podem residir no mesmo conjunto de escala flexível |            Não, os casos são o mesmo sistema operativo.  |               Sim, Linux e Windows podem residir no mesmo conjunto de escala flexível |
|         Monitorizar a saúde da aplicação  |            Extensão de saúde da aplicação  |            Extensão de saúde da aplicação ou sonda equilibrador Azure Load  |            Extensão de saúde da aplicação  |
|         Discos UltraSSD   |            Sim  |            Sim, apenas para implantações zonais  |            Não  |
|         Infiniband   |            Não  |            Sim, apenas um grupo de colocação única  |            Sim  |
|         Escrever Acelerador   |            Não  |            Sim  |            Sim  |
|         Grupos de colocação de proximidade   |            Sim  |            Sim  |            Sim  |
|         Anfitriões Dedicados Azure   |            Não  |            Sim  |            Sim  |
|         SLB básico   |            Não  |            Sim  |            Sim  |
|         Azure Load Balancer Standard SKU |            Sim  |            Sim  |            Sim  |
|         Gateway de Aplicação  |            Não  |            Sim  |            Sim  |
|         Controlo de Manutenção   |            Não  |            Sim  |            Sim  |
|         VMs da lista em conjunto  |            Sim  |            Sim  |            Sim, lista VMs em AvSet  |
|         Alertas Azure  |            Não  |            Sim  |            Sim  |
|         VM Insights  |            Não  |            Sim  |            Sim  |
|         Azure Backup  |            Sim  |            Sim  |            Sim  |
|         Azure Site Recovery  |            Sim, apenas PowerShell  |            Sim  |            Sim  |
|         Adicionar/remover vm existente ao grupo  |            Não  |            Não  |            Não  | 


## <a name="register-for-flexible-orchestration-mode"></a>Registe-se no modo de orquestração flexível
Antes de poder implantar conjuntos de escala de máquina virtual no modo de orquestração flexível, tem primeiro de registar a sua subscrição para a funcionalidade de pré-visualização. A inscrição pode demorar vários minutos a ser concluída. Pode utilizar os seguintes comandos Azure PowerShell ou Azure CLI para registar.

### <a name="azure-powershell"></a>Azure PowerShell 
Utilize o [cmdlet Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) para ativar a pré-visualização da sua subscrição. 

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName VMOrchestratorMultiFD -ProviderNamespace Microsoft.Compute `
Register-AzProviderFeature -FeatureName VMOrchestratorSingleFD -ProviderNamespace Microsoft.Compute  
```

A inscrição da funcionalidade pode demorar até 15 minutos. Para verificar o estado do registo: 

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName VMOrchestratorMultiFD -ProviderNamespace Microsoft.Compute 
```

Uma vez registada a funcionalidade para a sua subscrição, complete o processo de opt-in propagando a alteração no fornecedor de recursos Compute. 

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute 
```

### <a name="azure-cli-20"></a>CLI 2.0 do Azure 
Utilize [o registo de funcionalidades AZ](/cli/azure/feature#az-feature-register) para ativar a pré-visualização da sua subscrição. 

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name VMOrchestratorMultiFD
az feature register --namespace microsoft.compute --name VMOrchestratorSingleFD 
```

A inscrição da funcionalidade pode demorar até 15 minutos. Para verificar o estado do registo: 

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name VMOrchestratorMultiFD 
```

Uma vez registada a funcionalidade para a sua subscrição, complete o processo de opt-in propagando a alteração no fornecedor de recursos Compute. 

```azurecli-interactive
az provider register --namespace Microsoft.Compute 
```


## <a name="get-started-with-flexible-orchestration-mode"></a>Começar com o modo de orquestração flexível

Começa com o modo de orquestração flexível para os conjuntos de escala através do portal Azure, Azure CLI, Terraform ou REST API.

### <a name="azure-portal"></a>Portal do Azure

Crie uma escala de máquina virtual definida em modo de orquestração flexível através do portal Azure.

1. Inicie sessão no [portal Azure](https://portal.azure.com).
1. Na barra de pesquisa, procure e selecione **conjuntos de escala de máquina virtual**. 
1. Selecione **Criar** na página **de conjuntos de escala de máquina virtual.**
1. Na página de conjunto de **escala de máquina virtual,** veja a secção **Orquestração.**
1. Para o **modo Orquestração,** selecione a opção **Flexible.**
1. Desa estação a **contagem de domínio de falha**.
1. Termine de criar o seu conjunto de escala. Consulte [criar uma escala definida no portal Azure](quick-create-portal.md#create-virtual-machine-scale-set) para obter mais informações sobre como criar um conjunto de escala.

:::image type="content" source="./media/virtual-machine-scale-sets-orchestration-modes/portal-create-orchestration-mode-flexible.png" alt-text="Modo de orquestração no Portal ao criar um conjunto de escala":::

Em seguida, adicione uma máquina virtual à escala definida no modo de orquestração flexível.

1. Na barra de pesquisa, procure e selecione **máquinas Virtuais.**
1. **Selecione Adicionar** na página **de máquinas virtuais.**
1. No separador **Básicos,** consulte a secção detalhes de **Instância.**
1. Adicione o seu VM à escala definida no modo de orquestração flexível selecionando a escala definida nas **opções Disponibilidade**. Pode adicionar a máquina virtual a uma escala definida na mesma região, zona e grupo de recursos.
1. Termine de criar a sua máquina virtual. 

:::image type="content" source="./media/virtual-machine-scale-sets-orchestration-modes/vm-portal-orchestration-mode-flexible.png" alt-text="Adicione VM ao conjunto de escala de modo de orquestração flexível":::


### <a name="azure-cli-20"></a>CLI 2.0 do Azure
Crie um conjunto de balança de máquina virtual flexível com Azure CLI. O exemplo a seguir mostra a criação de um conjunto de escala flexível onde a contagem de domínio de avaria é definida para 3, uma máquina virtual é criada e, em seguida, adicionada ao conjunto de escala flexível. 

```azurecli-interactive
vmssflexname="my-vmss-vmssflex"  
vmname="myVM"  
rg="my-resource-group"  

az group create -n "$rg" -l $location  
az vmss create -n "$vmssflexname" -g "$rg" -l $location --orchestration-mode flexible --platform-fault-domain-count 3  
az vm create -n "$vmname" -g "$rg" -l $location --vmss $vmssflexname --image UbuntuLTS 
```

### <a name="terraform"></a>Terraform
Crie um conjunto de balança de máquina virtual flexível com Terraform. Este processo requer **o fornecedor Terraform Azurerm v2.15.0** ou mais tarde. Note os seguintes parâmetros:
- Quando nenhuma zona é especificada, `platform_fault_domain_count` pode ser 1, 2 ou 3 dependendo da região.
- Quando uma zona é especificada, `the fault domain count` pode ser 1.
- `single_placement_group` o parâmetro deve ser `false` para conjuntos flexíveis de escala de máquina virtual.
- Se estiver a fazer uma implantação regional, não há necessidade de especificar `zones` .

```terraform
resource "azurerm orchestrated_virtual_machine_scale_set" "tf_vmssflex" {
name = "tf_vmssflex"
location = azurerm_resource_group.myterraformgroup.location
resource_group_name = azurerm_resource_group.myterraformgroup.name
platform_fault_domain_count = 1
single_placement_group = false
zones = ["1"]
}
```


### <a name="rest-api"></a>API REST

1. Crie um conjunto de escala vazia. São necessários os seguintes parâmetros:
    - Versão API 2019-12-01 (ou maior) 
    - O grupo de colocação única deve ser `false` ao criar um conjunto de escala flexível

    ```json
    {
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[parameters('virtualMachineScaleSetName')]",
    "apiVersion": "2019-12-01",
    "location": "[parameters('location')]",
    "properties": {
        "singlePlacementGroup": false,
        "platformFaultDomainCount": "[parameters('virtualMachineScaleSetPlatformFaultDomainCount')]"
        },
    "zones": "[variables('selectedZone')]"
    }
    ```

2. Adicione máquinas virtuais ao conjunto de escala.
    1. Atribua a `virtualMachineScaleSet` propriedade ao conjunto de escala que criou anteriormente. Você é obrigado a especificar a `virtualMachineScaleSet` propriedade no momento da criação de VM. 
    1. Pode utilizar a **função de** modelo do Gestor de Recursos Azure para criar vários VMs ao mesmo tempo. Consulte [a iteração de recursos](https://docs.microsoft.com/azure/azure-resource-manager/templates/copy-resources#iteration-for-a-child-resource) nos modelos do Gestor de Recursos Azure. 

    ```json
    {
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[concat(parameters('virtualMachineNamePrefix'), copyIndex(1))]",
    "apiVersion": "2019-12-01",
    "location": "[parameters('location')]",
    "copy": {
        "name": "VMcopy",
        "count": "[parameters('virtualMachineCount')]"
        },
    "dependsOn": [
        "
        [resourceID('Microsoft.Compute/virtualMachineScaleSets', parameters('virtualMachineScaleSetName'))]",
        "
        [resourceID('Microsoft.Storage/storageAccounts', variables('diagnosticsStorageAccountName'))]",
        "
        [resourceID('Microsoft.Network/networkInterfaces', concat(parameters('virtualMachineNamePrefix'), copyIndex(1), '-NIC1'))]"
        ],
    "properties": {
        "virtualMachineScaleSet": {
            "id": "[resourceID('Microsoft.Compute/virtualMachineScaleSets', parameters('virtualMachineScaleSetName'))]"
        }
    }
    ```

Consulte [o Azure quickstart](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-vmss-flexible-orchestration-mode) para obter um exemplo completo.


## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

**Qual a escala que a orquestração flexível suporta?**

Pode adicionar até 1000 VMs a uma escala definida no modo de orquestração flexível.

**Como é que a disponibilidade com orquestração flexível se compara a Conjuntos de Disponibilidade ou Orquestração Uniforme?**

|   | Orquestração flexível  | Orquestração uniforme  | Conjuntos de Disponibilidade  |
|-|-|-|-|
| Implementar em todas as zonas de disponibilidade  | Não  | Sim  | Não  |
| Garantias de disponibilidade de domínio de avaria dentro de uma região  | Sim, até 1000 casos podem ser espalhados por até 3 domínios de falhas na região. A contagem máxima de domínio de avaria varia por região  | Sim, até 100 casos  | Sim, até 200 casos  |
| Grupos de colocação  | O modo flexível usa sempre vários grupos de colocação (singlePlacementGroup = falso)  | Pode escolher grupo de colocação única ou grupos de colocação múltipla | N/D  |
| Domínios de atualização  | Nenhuma, as atualizações de manutenção ou anfitrião são feitas por domínio de avarias  | Até 5 domínios de atualização  | Até 20 domínios de atualização  |


## <a name="troubleshoot-scale-sets-with-flexible-orchestration"></a>Conjuntos de escala de resolução de problemas com orquestração flexível
Encontre a solução certa para o seu cenário de resolução de problemas. 

```
InvalidParameter. The value 'False' of parameter 'singlePlacementGroup' is not allowed. Allowed values are: True
```

**Causa:** A subscrição não está registada para o modo de orquestração flexível Visualização Pública. 

**Solução:** Siga as instruções acima para se registar para o modo de visualização pública do modo de orquestração flexível. 

```
InvalidParameter. The specified fault domain count 2 must fall in the range 1 to 1.
```

**Causa:** O `platformFaultDomainCount` parâmetro é inválido para a região ou zona selecionada. 

**Solução:** Tem de selecionar um `platformFaultDomainCount` valor válido. Para implementações zonais, o valor máximo `platformFaultDomainCount` é 1. Para as deslocações regionais em que não é especificada nenhuma zona, o máximo `platformFaultDomainCount` varia consoante a região. Ver [Gerir a disponibilidade de VMs para scripts para](../virtual-machines/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) determinar a contagem máxima de domínio de avaria por região. 

```
OperationNotAllowed. Deletion of Virtual Machine Scale Set is not allowed as it contains one or more VMs. Please delete or detach the VM(s) before deleting the Virtual Machine Scale Set.
```

**Causa:** Tentar eliminar uma escala definida no modo de orquestração flexível que está associada a uma ou mais máquinas virtuais. 

**Solução:** Elimine todas as máquinas virtuais associadas à escala definida no modo de orquestração flexível e, em seguida, pode eliminar o conjunto de escalas.

```
InvalidParameter. The value 'True' of parameter 'singlePlacementGroup' is not allowed. Allowed values are: False.
```
**Causa:** A subscrição está registada para a pré-visualização do modo de orquestração flexível; no entanto, o `singlePlacementGroup` parâmetro é definido como *Verdadeiro*. 

**Solução:** O `singlePlacementGroup` deve ser definido como *Falso*. 


## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Saiba como implementar a sua aplicação no conjunto de escala.](virtual-machine-scale-sets-deploy-app.md)
