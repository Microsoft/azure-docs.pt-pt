---
title: Crie um conjunto de escala que utilize máquinas virtuais Azure Spot
description: Saiba como criar conjuntos de escala de máquina virtual Azure que usam máquinas virtuais Azure Spot para economizar em custos.
author: JagVeerappan
ms.author: jagaveer
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: spot
ms.date: 02/26/2021
ms.reviewer: cynthn
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 61bb87d84b96f988ae065a70b85d445fc8b96ccf
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762952"
---
# <a name="azure-spot-virtual-machines-for-virtual-machine-scale-sets"></a>Máquinas virtuais Azure Spot para conjuntos de escala de máquinas virtuais 

A utilização de Máquinas Virtuais Azure Spot em conjuntos de escala permite-lhe tirar partido da nossa capacidade não utilizada com uma economia de custos significativa. Em qualquer momento em que a Azure precise da capacidade de volta, a infraestrutura Azure irá despejar instâncias da Máquina Virtual Azure Spot. Por isso, as instâncias da Azure Spot Virtual Machine são ótimas para cargas de trabalho que podem lidar com interrupções como trabalhos de processamento de lotes, ambientes dev/teste, grandes cargas de trabalho de computação, e muito mais.

A quantidade de capacidade disponível pode variar em função do tamanho, região, hora do dia e muito mais. Ao implementar instâncias da Máquina Virtual Azure Spot em conjuntos de escala, o Azure apenas atribuirá o caso se houver capacidade disponível, mas não existe SLA para estes casos. Um conjunto de escala de máquina virtual Azure Spot é implantado num único domínio de avaria e não oferece garantias de alta disponibilidade.


## <a name="pricing"></a>Preços

Os preços das instâncias da Máquina Virtual Azure Spot são variáveis, com base na região e no SKU. Para mais informações, consulte os preços para [Linux](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) e [Windows.](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/) 


Com preços variáveis, você tem a opção de definir um preço máximo, em dólares americanos (USD), usando até cinco casas decimais. Por exemplo, o valor `0.98765` seria um preço máximo de $0.98765 USD por hora. Se definir o preço `-1` máximo, o caso não será despejado com base no preço. O preço, por exemplo, será o preço atual para a Azure Spot Virtual Machine ou o preço de uma instância padrão, que sempre é menor, desde que haja capacidade e quota disponível.


## <a name="limitations"></a>Limitações

Os seguintes tamanhos não são suportados para máquinas virtuais Azure Spot:
 - Série B
 - Versões promocionais de qualquer tamanho (como tamanhos promocionais Dv2, NV, NC, H)

A azure Spot Virtual Machine pode ser implantada em qualquer região, exceto microsoft Azure China 21Vianet.

<a name="channel"></a>

Os [seguintes tipos de oferta](https://azure.microsoft.com/support/legal/offer-details/) são atualmente suportados:

-   Contrato Enterprise
-   Código de oferta pay-as-you-go (003P)
-   Patrocinado (0036P e 0136P)
- Para Provedor de Serviços em Nuvem (CSP), consulte o [Centro de Parceiros](/partner-center/azure-plan-get-started) ou contacte diretamente o seu parceiro.

## <a name="eviction-policy"></a>Política de expulsão

Ao criar um conjunto de escala utilizando máquinas virtuais Azure Spot, pode definir a política de despejo para *Deallocate* (predefinição) ou *Eliminar*. 

A política *deallocate* move as suas instâncias despejadas para o estado de paragem que lhe permite redistribuir casos despejados. No entanto, não há garantias de que a dotação seja bem sucedida. Os VMs deallocados contarão com a sua quota de instância definida em escala e você será cobrado pelos seus discos subjacentes. 

Se quiser que os seus casos sejam apagados quando forem despejados, pode definir a política de despejo para *eliminar*. Com a política de despejo definida para apagar, pode criar novos VMs aumentando a escala definida caso contando propriedade. Os VMs despejados são eliminados juntamente com os seus discos subjacentes, pelo que não será cobrado pelo armazenamento. Também pode utilizar a função de escala automática dos conjuntos de escala para tentar compensar automaticamente os VMs despejados, no entanto, não há garantias de que a atribuição tenha sucesso. Recomenda-se que utilize apenas a função de escala automática nos conjuntos de escala de máquinas Azure Spot Virtual quando definir a política de despejo para eliminar para evitar o custo dos seus discos e bater os limites de quota. 

Os utilizadores podem optar por receber notificações in-VM através de [Eventos Agendados Azure.](../virtual-machines/linux/scheduled-events.md) Isto irá notificá-lo se os seus VMs estiverem a ser despejados e terá 30 segundos para terminar quaisquer trabalhos e executar tarefas de encerramento antes do despejo. 

<a name="bkmk_try"></a>
## <a name="try--restore-preview"></a>Experimente & restauro (pré-visualização)

Esta nova funcionalidade ao nível da plataforma irá utilizar a IA para tentar restaurar automaticamente as instâncias da Máquina Virtual Azure Spot despejadas dentro de uma escala definida para manter a contagem de casos de alvo. 

> [!IMPORTANT]
> Tente & restauro está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Experimente & restaurar os benefícios:
- Tentativas de restaurar máquinas virtuais Azure Spot despejadas devido à capacidade.
- Espera-se que as máquinas virtuais do Azure Spot restauradas sejam executadas por uma duração mais longa, com uma menor probabilidade de uma capacidade desencadeada de despejo.
- Melhora o tempo de vida útil de uma Máquina Virtual Azure Spot, para que as cargas de trabalho decorram por uma duração mais longa.
- Ajuda os conjuntos de escala de máquinas virtuais a manter a contagem de alvos para máquinas virtuais Azure Spot, semelhantes para manter a funcionalidade de contagem de alvos que já existe para VMs Pay-As-You-Go.

Experimente & a restauração seja desativada em conjuntos de escala que utilizam [a Autoscale](virtual-machine-scale-sets-autoscale-overview.md). O número de VMs no conjunto de escala é impulsionado pelas regras de autoescala.

### <a name="register-for-try--restore"></a>Registe-se para tentar & restauro

Antes de poder utilizar a função de restauro &, tem de registar a sua subscrição para a pré-visualização. A inscrição pode demorar vários minutos a ser concluída. Pode utilizar o Azure CLI ou o PowerShell para completar o registo de funcionalidades.


**Utilizar a CLI**

Utilize [o registo de funcionalidades AZ](/cli/azure/feature#az_feature_register) para ativar a pré-visualização da sua subscrição. 

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name SpotTryRestore 
```

A inscrição da funcionalidade pode demorar até 15 minutos. Para verificar o estado do registo: 

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name SpotTryRestore 
```

Uma vez registada a funcionalidade para a sua subscrição, complete o processo de opt-in propagando a alteração no fornecedor de recursos Compute. 

```azurecli-interactive
az provider register --namespace Microsoft.Compute 
```
**Utilizar o PowerShell** 

Utilize o [cmdlet Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) para ativar a pré-visualização da sua subscrição. 

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName SpotTryRestore -ProviderNamespace Microsoft.Compute 
```

A inscrição da funcionalidade pode demorar até 15 minutos. Para verificar o estado do registo: 

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName SpotTryRestore -ProviderNamespace Microsoft.Compute 
```

Uma vez registada a funcionalidade para a sua subscrição, complete o processo de opt-in propagando a alteração no fornecedor de recursos Compute. 

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute 
```

## <a name="placement-groups"></a>Grupos de colocação

O grupo de colocação é uma construção semelhante a um conjunto de disponibilidades Azure, com os seus próprios domínios de falha e domínios de upgrade. Por predefinição, um conjunto de dimensionamento consiste num único grupo de colocação com o tamanho máximo de 100 VMs. Se a propriedade definida em escala `singlePlacementGroup` for definida como *falsa,* o conjunto de escala pode ser composto por vários grupos de colocação e tem uma gama de 0-1.000 VMs. 

> [!IMPORTANT]
> A menos que você esteja usando Infiniband com HPC, é fortemente recomendado definir a propriedade definida `singlePlacementGroup` escala para *falso* para permitir vários grupos de colocação para uma melhor escala em toda a região ou zona. 

## <a name="deploying-azure-spot-virtual-machines-in-scale-sets"></a>Implantação de máquinas virtuais Azure Spot em conjuntos de escala

Para colocar as Máquinas Virtuais Azure Spot em conjuntos de escala, pode definir a nova bandeira *prioritária* para *o Spot*. Todos os VMs da sua escala serão definidos para Spot. Para criar um conjunto de escala com máquinas virtuais Azure Spot, utilize um dos seguintes métodos:
- [Portal do Azure](#portal)
- [CLI do Azure](#azure-cli)
- [Azure PowerShell](#powershell)
- [Modelos do Azure Resource Manager](#resource-manager-templates)

## <a name="portal"></a>Portal

O processo para criar um conjunto de escala que utiliza máquinas virtuais Azure Spot é o mesmo que detalhado no [artigo de início.](quick-create-portal.md) Ao implementar um conjunto de escala, pode optar por definir a bandeira do Spot e a política de despejo: ![ Criar um conjunto de escala com máquinas virtuais Azure Spot](media/virtual-machine-scale-sets-use-spot/vmss-spot-portal-max-price.png)


## <a name="azure-cli"></a>CLI do Azure

O processo de criação de uma escala definida com máquinas virtuais Azure Spot é o mesmo que detalhado no [artigo de início.](quick-create-cli.md) Basta adicionar o "--Ponto Prioritário", e adicionar `--max-price` . Neste exemplo, usamos `-1` para que o caso não seja despejado com base no `--max-price` preço.

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --single-placement-group false \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --max-price -1 
```

## <a name="powershell"></a>PowerShell

O processo de criação de uma escala definida com máquinas virtuais Azure Spot é o mesmo que detalhado no [artigo de início.](quick-create-powershell.md)
Basta adicionar '-Ponto Prioritário', e fornecer um `-max-price` ao [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig).

```powershell
$vmssConfig = New-AzVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Priority "Spot" `
    -max-price -1
```

## <a name="resource-manager-templates"></a>Modelos do Resource Manager

O processo para criar um conjunto de escala que utiliza máquinas virtuais Azure Spot é o mesmo que detalhado no artigo de início para [Linux](quick-create-template-linux.md) ou [Windows](quick-create-template-windows.md). 

Para implementações, utilização ou posterior utilização do modelo do modelo da máquina virtual Azure `"apiVersion": "2019-03-01"` Spot. 

Adicione o `priority` , e propriedades à `evictionPolicy` `billingProfile` `"virtualMachineProfile":` seção e a propriedade à `"singlePlacementGroup": false,` secção no seu `"Microsoft.Compute/virtualMachineScaleSets"` modelo:

```json

{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  },
  "properties": {
    "singlePlacementGroup": false,
    }

        "virtualMachineProfile": {
              "priority": "Spot",
                "evictionPolicy": "Deallocate",
                "billingProfile": {
                    "maxPrice": -1
                }
            },
```

Para eliminar o caso depois de ter sido despejado, altere o `evictionPolicy` parâmetro para `Delete` .


## <a name="simulate-an-eviction"></a>Simular um despejo

Você pode [simular um despejo](/rest/api/compute/virtualmachines/simulateeviction) de uma Máquina Virtual Azure Spot para testar quão bem a sua aplicação irá responder a um despejo súbito. 

Substitua as seguintes informações: 

- `subscriptionId`
- `resourceGroupName`
- `vmName`


```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/simulateEviction?api-version=2020-06-01
```

`Response Code: 204` significa que o despejo simulado foi bem sucedido. 

## <a name="faq"></a>FAQ

**Q:** Uma vez criado, um exemplo de Máquina Virtual Azure Spot é o mesmo que a instância padrão?

**A:** Sim, exceto que não existe SLA para máquinas virtuais Azure Spot e podem ser despejadas a qualquer momento.


**Q:** O que fazer quando for despejado, mas ainda precisa de capacidade?

**A:** Recomendamos que utilize VMs padrão em vez de Máquinas Virtuais Azure Spot se precisar de capacidade imediatamente.


**Q:** Como é gerida a quota para a Azure Spot Virtual Machine?

**A:** As instâncias da Máquina Virtual Azure Spot e as instâncias padrão terão piscinas de quotas separadas. A quota da máquina virtual Azure Spot será partilhada entre VMs e instâncias definidas em escala. Para obter mais informações, veja [Subscrição do Azure e limites de serviço, quotas e restrições](../azure-resource-manager/management/azure-subscription-service-limits.md).


**Q:** Posso pedir uma quota adicional para a Máquina Virtual Azure Spot?

**A:** Sim, poderá submeter o pedido de aumento da sua quota para máquinas virtuais Azure Spot através do [processo padrão de pedido de quota.](../azure-portal/supportability/per-vm-quota-requests.md)


**Q:** Posso converter os conjuntos de escala existentes em conjuntos de escala virtual Azure Spot?

**A:** Não, a colocação da `Spot` bandeira só é suportada no momento da criação.


**Q:** Se eu estava a usar `low` para conjuntos de escala de baixa prioridade, preciso de começar a `Spot` usar?

**A:** Por enquanto, ambos `low` e `Spot` vão funcionar, mas deve começar a transitar para a utilização `Spot` .


**Q:** Posso criar um conjunto de escala com VMs regulares e máquinas virtuais Azure Spot?

**A:** Não, um conjunto de escala não pode suportar mais do que um tipo prioritário.


**Q:**  Posso utilizar uma escala automática com conjuntos de escala de máquina virtual Azure Spot?

**A:** Sim, pode definir regras de auto-cálculo no seu conjunto de escala de máquina virtual Azure Spot. Se os seus VMs forem despejados, a autoescalação pode tentar criar novas Máquinas Virtuais Azure Spot. Lembre-se, mas não tem a garantia desta capacidade. 


**Q:**  A autoescalação funciona com ambas as políticas de despejo (deallocate e delete)?

**A:** Sim, no entanto, recomenda-se que desemocmente a sua política de despejo para eliminar quando utilizar a autoescala. Isto porque os casos de negociação são contados contra a sua capacidade de contar na escala definida. Ao utilizar a autoescala, é provável que atinja a contagem de casos de alvo rapidamente devido às situações de despejo e desalojados. Além disso, as suas operações de escala podem ser afetadas por despejos à vista. Por exemplo, as instâncias de conjunto de escala de máquinas virtuais podem ficar abaixo da contagem de min definida devido a vários despejos de pontos durante as operações de escala. 


**Q:** Onde posso fazer perguntas?

**A:** Você pode postar e marcar a sua pergunta com `azure-spot` [q&A](/answers/topics/azure-spot.html). 

## <a name="next-steps"></a>Passos seguintes

Consulte a [página de preços da escala de máquina virtual](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) para obter detalhes sobre os preços.
