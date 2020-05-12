---
title: Crie um conjunto de escala que utilize VMs De Mancha Azure
description: Aprenda a criar conjuntos de escala de máquinas virtuais Azure que usam VMs spot para economizar em custos.
author: cynthn
ms.author: cynthn
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: spot
ms.date: 03/25/2020
ms.reviewer: jagaveer
ms.custom: jagaveer
ms.openlocfilehash: 59de7a8decef807b548ff4b85f06fc1115ce110b
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83125048"
---
# <a name="azure-spot-vms-for-virtual-machine-scale-sets"></a>VMs de Spot Azure para conjuntos de escala de máquinas virtuais 

A utilização do Azure Spot em conjuntos de escala permite-lhe tirar partido da nossa capacidade não utilizada a uma significativa poupança de custos. Em qualquer momento em que o Azure precise de capacidade de volta, a infraestrutura Azure despejará as instâncias do Spot. Por isso, os casos de spot são ótimos para cargas de trabalho que podem lidar com interrupções como trabalhos de processamento de lotes, ambientes de v/teste, grandes cargas de trabalho de computação, e muito mais.

A quantidade de capacidade disponível pode variar em função do tamanho, região, hora do dia e muito mais. Ao implementar as instâncias spot em conjuntos de escala, o Azure só atribuirá a instância se houver capacidade disponível, mas não existe SLA para estes casos. Um conjunto de escala spot é implantado num domínio de falha única e não oferece garantias de alta disponibilidade.


## <a name="pricing"></a>Preços

Os preços dos casos spot são variáveis, com base na região e no SKU. Para mais informações, consulte os preços para [Linux](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) e [Windows](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/). 


Com preços variáveis, você tem opção de definir um preço máximo, em dólares americanos (USD), usando até 5 lugares decimais. Por exemplo, o valor `0.98765` seria um preço máximo de $0.98765 USD por hora. Se fixar o preço máximo para `-1` ser, a instância não será despejada com base no preço. O preço por exemplo será o preço atual para o Spot ou o preço de uma instância padrão, o que sempre é menor, desde que haja capacidade e quota disponíveis.

## <a name="eviction-policy"></a>Política de despejo

Ao criar conjuntos de escala spot, pode definir a política de despejo para *Deallocate (padrão)* ou *Eliminar*. 

A política *de Deatribuição* move as suas instâncias despejadas para o estado de parado, permitindo-lhe recolocar casos despejados. No entanto, não há garantias de que a dotação seja bem sucedida. Os VMs deallocated contarão com a sua quota de instância definida de escala e você será cobrado pelos seus discos subjacentes. 

Se quiser que as suas instâncias na sua escala Spot sejam eliminadas quando forem despejadas, pode definir a política de despejo para *apagar*. Com a política de despejo definida para apagar, você pode criar novos VMs aumentando a escala definida de contagem de propriedades. Os VMs despejados são eliminados juntamente com os seus discos subjacentes, pelo que não serão cobrados pelo armazenamento. Também pode utilizar a função de escala automática de conjuntos de escala para tentar compensar automaticamente os VMs despejados, no entanto, não há garantias de que a atribuição tenha sucesso. Recomenda-se que utilize apenas a função de escala automática nos conjuntos de escala Spot quando definir a política de despejo para eliminar para evitar o custo dos seus discos e atingir os limites de quota. 

Os utilizadores podem optar por receber notificações in-VM através de [Eventos Agendados do Azure.](../virtual-machines/linux/scheduled-events.md) Isto irá notificá-lo se os seus VMs estão sendo despejados e você terá 30 segundos para terminar quaisquer trabalhos e executar tarefas de encerramento antes do despejo. 


## <a name="deploying-spot-vms-in-scale-sets"></a>Implantação de VMs spot em conjuntos de escala

Para implantar VMs spot em conjuntos de escala, pode definir a nova bandeira *Prioritária* para *o Spot*. Todos os VMs no seu conjunto de escala serão definidos para spot. Para criar um conjunto de escala com VMs spot, utilize um dos seguintes métodos:
- [Portal do Azure](#portal)
- [CLI do Azure](#azure-cli)
- [Azure PowerShell](#powershell)
- [Modelos do Azure Resource Manager](#resource-manager-templates)

## <a name="portal"></a>Portal

O processo de criação de um conjunto de escala que utiliza VMs spot é o mesmo que detalhado no [artigo de início.](quick-create-portal.md) Quando estiver a implantar um conjunto de escala, pode optar por definir a bandeira do Spot e a política de despejo: ![ Criar uma escala com VMs de mancha](media/virtual-machine-scale-sets-use-spot/vmss-spot-portal-max-price.png)


## <a name="azure-cli"></a>CLI do Azure

O processo de criação de um conjunto de escala com VMs spot é o mesmo que detalhado no [artigo de início.](quick-create-cli.md) Basta adicionar o "-Ponto Prioritário", e adicionar `--max-price` . Neste exemplo, usamos `-1` para que a instância não seja `--max-price` despejada com base no preço.

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --max-price -1 
```

## <a name="powershell"></a>PowerShell

O processo de criação de um conjunto de escala com VMs spot é o mesmo que detalhado no [artigo de início.](quick-create-powershell.md)
Basta adicionar 'Ponto Prioritário', e fornecer um `-max-price` ao [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig).

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

O processo para criar um conjunto de escala que utiliza VMs spot é o mesmo que detalhado no artigo de início para [Linux](quick-create-template-linux.md) ou [Windows](quick-create-template-windows.md). 

Para implementações de modelos spot, utilize `"apiVersion": "2019-03-01"` ou mais tarde. Adicione as `priority` propriedades e propriedades à secção do seu `evictionPolicy` `billingProfile` `"virtualMachineProfile":` modelo: 

```json
                "priority": "Spot",
                "evictionPolicy": "Deallocate",
                "billingProfile": {
                    "maxPrice": -1
                }
```

Para eliminar a ocorrência depois de ter sido despejada, mude o `evictionPolicy` parâmetro para `Delete` .

## <a name="faq"></a>FAQ

**Q:** Uma vez criado, um spot é o mesmo que o caso padrão?

**A:** Sim, exceto que não há SLA para VMs spot e eles podem ser despejados a qualquer momento.


**Q:** O que fazer quando é despejado, mas ainda precisa de capacidade?

**A:** Recomendamos que utilize VMs padrão em vez de VMs spot se precisar de capacidade imediatamente.


**Q:** Como é gerida a quota para o Spot?

**A:** As instâncias pontuais e as instâncias padrão terão piscinas de quotas separadas. A quota spot será partilhada entre VMs e instâncias de escala. Para obter mais informações, veja [Subscrição do Azure e limites de serviço, quotas e restrições](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits).


**Q:** Posso pedir quota adicional para o Spot?

**A:** Sim, poderá submeter o pedido de aumento da sua quota para VMs spot através do processo padrão de pedido de [quotas.](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests)


**Q:** Posso converter conjuntos de escala existentes em conjuntos de escala spot?

**A:** Não, a colocação da bandeira só é apoiada no momento da `Spot` criação.


**Q:** Se eu estava a usar `low` para conjuntos de baixa prioridade, preciso de começar a `Spot` usar?

**A:** Por enquanto, ambos `low` e `Spot` vão funcionar, mas deve começar a transitar para a utilização. `Spot`


**Q:** Posso criar um conjunto de escala sem VMs regulares e VMs spot?

**A:** Não, um conjunto de escala não pode suportar mais do que um tipo prioritário.


**Q:**  Posso usar a escala automática com conjuntos de escala spot?

**A:** Sim, pode definir regras de autoscalcificação no seu conjunto de escala spot. Se os seus VMs forem despejados, a escala automática pode tentar criar novos VMs spot. Lembre-se, não é garantida esta capacidade. 


**Q:**  A escala automática funciona com ambas as políticas de despejo (desolo e eliminação)?

**A:** Recomenda-se que defina a sua política de despejo para eliminar ao utilizar a escala automática. Isto porque as instâncias de negociação são contadas contra a sua contagem de capacidade seleções na escala definida. Ao utilizar a escala automática, provavelmente irá acertar a sua contagem de exemplos de alvo rapidamente devido às ocorrências de saques e desalojados. 


**Q:** Que canais suportam Os VMs spot?

**A:** Consulte a tabela abaixo para obter disponibilidade de Spot VM.

<a name="channel"></a>

| Canais Azure               | Disponibilidade de VMs spot azure       |
|------------------------------|-----------------------------------|
| Contrato Enterprise         | Sim                               |
| Pay As You Go                | Sim                               |
| Provedor de Serviço sinuoso (CSP) | [Contacte o seu parceiro](https://docs.microsoft.com/partner-center/azure-plan-get-started) |
| Benefícios                     | Não disponível                     |
| Patrocinado                    | Não disponível                     |
| Avaliação Gratuita                   | Não disponível                     |


**Q:** Onde posso fazer perguntas?

**A:** Pode publicar e marcar a sua pergunta `azure-spot` no Q&[A](https://docs.microsoft.com/answers/topics/azure-spot.html). 

## <a name="next-steps"></a>Passos seguintes

Consulte a página de preços da [escala de máquina virtual](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) para obter detalhes de preços.
