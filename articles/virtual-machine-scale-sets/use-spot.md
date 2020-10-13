---
title: Crie um conjunto de escala que utilize VMs Azure Spot
description: Saiba como criar conjuntos de escala de máquina virtual Azure que usam VMs spot para economizar em custos.
author: cynthn
ms.author: cynthn
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: spot
ms.date: 03/25/2020
ms.reviewer: jagaveer
ms.custom: jagaveer, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 4c5386e2fad0ebdd30ca8f9a8f4933e8adaf5d6b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91729020"
---
# <a name="azure-spot-vms-for-virtual-machine-scale-sets"></a>VMs Azure Spot para conjuntos de escala de máquina virtual 

A utilização do Azure Spot em conjuntos de escala permite-lhe tirar partido da nossa capacidade não utilizada com uma poupança significativa de custos. Em qualquer momento em que Azure precisa da capacidade de volta, a infraestrutura Azure irá despejar instâncias spot. Por isso, as instâncias spot são ótimas para cargas de trabalho que podem lidar com interrupções como trabalhos de processamento de lotes, ambientes dev/teste, grandes cargas de trabalho de computação, e muito mais.

A quantidade de capacidade disponível pode variar em função do tamanho, região, hora do dia e muito mais. Ao implementar instâncias spot em conjuntos de escala, a Azure só atribuirá o caso se houver capacidade disponível, mas não há SLA para estes casos. Um conjunto de escala spot é implantado num único domínio de falha e não oferece garantias de disponibilidade elevadas.


## <a name="pricing"></a>Preços

Os preços dos casos spot são variáveis, com base na região e no SKU. Para mais informações, consulte os preços para [Linux](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) e [Windows.](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/) 


Com preços variáveis, você tem a opção de definir um preço máximo, em dólares americanos (USD), usando até 5 casas decimais. Por exemplo, o valor `0.98765` seria um preço máximo de $0.98765 USD por hora. Se definir o preço `-1` máximo, o caso não será despejado com base no preço. O preço, por exemplo, será o preço atual para o Spot ou o preço de uma instância padrão, que sempre é menor, desde que haja capacidade e quota disponíveis.

## <a name="eviction-policy"></a>Política de expulsão

Ao criar conjuntos de escala spot, pode definir a política de despejo para *Deallocate* (predefinição) ou *Eliminar*. 

A política *deallocate* move as suas instâncias despejadas para o estado de paragem que lhe permite redistribuir casos despejados. No entanto, não há garantias de que a dotação seja bem sucedida. Os VMs deallocados contarão com a sua quota de instância definida em escala e você será cobrado pelos seus discos subjacentes. 

Se quiser que as suas instâncias na escala spot sejam eliminadas quando forem despejadas, pode definir a política de despejo para *eliminar*. Com a política de despejo definida para apagar, pode criar novos VMs aumentando a escala definida caso contando propriedade. Os VMs despejados são eliminados juntamente com os seus discos subjacentes, pelo que não será cobrado pelo armazenamento. Também pode utilizar a função de escala automática dos conjuntos de escala para tentar compensar automaticamente os VMs despejados, no entanto, não há garantias de que a atribuição tenha sucesso. Recomenda-se que utilize apenas a função de escala automática nos conjuntos de escala spot quando definir a política de despejo para eliminar para evitar o custo dos seus discos e bater os limites de quota. 

Os utilizadores podem optar por receber notificações in-VM através de [Eventos Agendados Azure.](../virtual-machines/linux/scheduled-events.md) Isto irá notificá-lo se os seus VMs estiverem a ser despejados e terá 30 segundos para terminar quaisquer trabalhos e executar tarefas de encerramento antes do despejo. 

## <a name="placement-groups"></a>Grupos de colocação
O grupo de colocação é uma construção semelhante a um conjunto de disponibilidades Azure, com os seus próprios domínios de falha e domínios de upgrade. Por predefinição, um conjunto de dimensionamento consiste num único grupo de colocação com o tamanho máximo de 100 VMs. Se a propriedade definida em escala `singlePlacementGroup` for definida como *falsa,* o conjunto de escala pode ser composto por vários grupos de colocação e tem uma gama de 0-1.000 VMs. 

> [!IMPORTANT]
> A menos que você esteja usando Infiniband com HPC, é fortemente recomendado definir a propriedade definida `singlePlacementGroup` escala para *falso* para permitir vários grupos de colocação para uma melhor escala em toda a região ou zona. 

## <a name="deploying-spot-vms-in-scale-sets"></a>Implantação de VMs spot em conjuntos de escala

Para implantar VMs spot em conjuntos de escala, pode definir a nova bandeira *prioritária* para *o Spot*. Todos os VMs da sua escala serão definidos para Spot. Para criar um conjunto de escala com VMs spot, utilize um dos seguintes métodos:
- [Portal do Azure](#portal)
- [CLI do Azure](#azure-cli)
- [Azure PowerShell](#powershell)
- [Modelos do Azure Resource Manager](#resource-manager-templates)

## <a name="portal"></a>Portal

O processo para criar um conjunto de escala que utilize VMs spot é o mesmo que detalhado no [artigo de início.](quick-create-portal.md) Ao implementar um conjunto de escala, pode optar por definir a bandeira do Spot e a política de despejo: ![ Criar um conjunto de escala com VMs spot](media/virtual-machine-scale-sets-use-spot/vmss-spot-portal-max-price.png)


## <a name="azure-cli"></a>CLI do Azure

O processo de criação de um conjunto de escala com VMs spot é o mesmo que detalhado no [artigo de início.](quick-create-cli.md) Basta adicionar o "--Ponto Prioritário", e adicionar `--max-price` . Neste exemplo, usamos `-1` para que o caso não seja despejado com base no `--max-price` preço.

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

O processo de criação de um conjunto de escala com VMs spot é o mesmo que detalhado no [artigo de início.](quick-create-powershell.md)
Basta adicionar '-Ponto Prioritário', e fornecer um `-max-price` ao [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig).

```powershell
$vmssConfig = New-AzVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Priority "Spot" `
    --max-price -1
```

## <a name="resource-manager-templates"></a>Modelos do Resource Manager

O processo para criar um conjunto de escala que utilize VMs spot é o mesmo que detalhado no artigo de início para [Linux](quick-create-template-linux.md) ou [Windows](quick-create-template-windows.md). 

Para implementações de modelos spot, use `"apiVersion": "2019-03-01"` ou mais tarde. 

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

## <a name="faq"></a>FAQ

**Q:** Uma vez criado, um caso spot é o mesmo que a instância padrão?

**A:** Sim, exceto que não há SLA para VMs spot e podem ser despejados a qualquer momento.


**Q:** O que fazer quando for despejado, mas ainda precisa de capacidade?

**A:** Recomendamos que utilize VMs padrão em vez de VMs spot se precisar de capacidade imediatamente.


**Q:** Como é gerida a quota para o Spot?

**A:** As instâncias pontuais e as instâncias-padrão terão piscinas de quotas separadas. A quota pontual será partilhada entre VMs e instâncias definidas em escala. Para obter mais informações, veja [Subscrição do Azure e limites de serviço, quotas e restrições](../azure-resource-manager/management/azure-subscription-service-limits.md).


**Q:** Posso pedir uma quota adicional para o Spot?

**A:** Sim, poderá submeter o pedido de aumento da sua quota de VMs spot através do [processo padrão de pedido de quota.](../azure-portal/supportability/per-vm-quota-requests.md)


**Q:** Posso converter os conjuntos de escala existentes em conjuntos de escala spot?

**A:** Não, a colocação da `Spot` bandeira só é suportada no momento da criação.


**Q:** Se eu estava a usar `low` para conjuntos de escala de baixa prioridade, preciso de começar a `Spot` usar?

**A:** Por enquanto, ambos `low` e `Spot` vão funcionar, mas deve começar a transitar para a utilização `Spot` .


**Q:** Posso criar um conjunto de escala com VMs regulares e VMs?

**A:** Não, um conjunto de escala não pode suportar mais do que um tipo prioritário.


**Q:**  Posso usar a escala automática com conjuntos de escala spot?

**A:** Sim, pode definir regras de auto-cálculo no seu conjunto de escala spot. Se os seus VMs forem despejados, a autoescalada pode tentar criar novos VMs spot. Lembre-se, mas não tem a garantia desta capacidade. 


**Q:**  A autoescalação funciona com ambas as políticas de despejo (deallocate e delete)?

**A:** Sim, no entanto, recomenda-se que desemocmente a sua política de despejo para eliminar quando utilizar a autoescala. Isto porque os casos de negociação são contados contra a sua capacidade de contar na escala definida. Ao utilizar a autoescala, é provável que atinja a contagem de casos de alvo rapidamente devido às situações de despejo e desalojados. Além disso, as suas operações de escala podem ser afetadas por despejos à vista. Por exemplo, as instâncias VMSS podem ficar abaixo da contagem de min definida devido a vários despejos de pontos durante as operações de escala. 

**Q:** Que canais suportam Spot VMs?

**A:** Consulte a tabela abaixo para obter disponibilidade do Spot VM.

<a name="channel"></a>

| Canais Azure               | Disponibilidade de VMs Azure Spot       |
|------------------------------|-----------------------------------|
| Contrato Enterprise         | Sim                               |
| Pay As You Go                | Sim                               |
| Fornecedor de serviços na nuvem (CSP) | [Contacte o seu parceiro](/partner-center/azure-plan-get-started) |
| Benefícios                     | Não disponível                     |
| Patrocinado                    | Sim                               |
| Avaliação Gratuita                   | Não disponível                     |


**Q:** Onde posso fazer perguntas?

**A:** Você pode postar e marcar a sua pergunta com `azure-spot` [q&A](/answers/topics/azure-spot.html). 

## <a name="next-steps"></a>Passos seguintes

Consulte a [página de preços da escala de máquina virtual](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) para obter detalhes sobre os preços.
