---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 3c008e77116a9b42a2ea137069529c5e241adddd
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66160062"
---
Pode facilmente [Dimensionar automaticamente](../articles/azure-monitor/platform/autoscale-best-practices.md) seu [máquinas virtuais (VMs)](../articles/virtual-machines/windows/overview.md) quando utiliza [conjuntos de dimensionamento de máquinas virtuais](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) e o [funcionalidade de dimensionamento automático do Azure Monitor](../articles/azure-monitor/platform/autoscale-overview.md). As suas VMs tem de ser membros de um conjunto de dimensionamento para ser dimensionada automaticamente. Este artigo fornece informações que permitem-lhe compreender melhor como dimensionar as suas VMs verticalmente e horizontalmente usando métodos automática e manuais.

## <a name="horizontal-or-vertical-scaling"></a>Dimensionamento horizontal ou vertical

A funcionalidade de dimensionamento automático do Azure Monitor só pode ser dimensionada horizontalmente, que é um aumento ("de saída") ou diminuição ("") do número de VMs. Dimensionamento horizontal é mais flexível numa situação de nuvem, pois permite a execução de potencialmente milhares de VMs para processar carga. Aumentar horizontalmente, automática ou manualmente, alterando a capacidade (ou a contagem de instâncias) do conjunto de dimensionamento. 

Dimensionamento vertical mantém o mesmo número de VMs, mas torna as VMs mais ("up") ou menos ("down") poderoso. Power é medido em atributos como memória, velocidade da CPU ou espaço em disco. Dimensionamento vertical é dependente da disponibilidade de hardware de maior, que rapidamente atinge um limite superior e pode variar consoante a região. Normalmente, também dimensionamento vertical requer uma VM parar e reiniciar. Dimensionar verticalmente, tem de definir um novo tamanho na configuração das VMs no conjunto de dimensionamento.

Através de runbooks no [automatização do Azure](../articles/automation/automation-intro.md), pode facilmente [Dimensionar VMs num conjunto de dimensionamento](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-vertical-scale-reprovision.md) ou reduzir verticalmente.

## <a name="create-a-virtual-machine-scale-set"></a>Criar um conjunto de dimensionamento de máquinas virtuais

Conjuntos de dimensionamento tornam mais fácil para a implementação e gestão de VMs idênticas como um conjunto. É possível criar o Linux ou conjuntos de dimensionamento Windows com o [portal do Azure](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md), [Azure PowerShell](../articles/virtual-machines/windows/tutorial-create-vmss.md), ou o [da CLI do Azure](../articles/virtual-machines/linux/tutorial-create-vmss.md). Também pode criar e gerir conjuntos de dimensionamento com SDKs, como [Python](https://azure.microsoft.com/develop/python/) ou [node. js](/nodejs/azure), ou diretamente com o [REST APIs](/rest/api/compute/virtualmachinescalesets). Dimensionamento automático de VMs é conseguido através da aplicação de regras e as métricas para o conjunto de dimensionamento.

## <a name="configure-autoscale-for-a-scale-set"></a>Configurar o dimensionamento automático para um conjunto de dimensionamento

Dimensionamento automático fornece o número certo de VMs para processar a carga na sua aplicação. Permite-lhe adicionar VMs para processar os aumentos de carga e poupar dinheiro ao remover as VMs que são parados. Especifique um número mínimo e máximo de VMs executada com base num conjunto de regras. Ter um mínimo de torna-se de que seu aplicativo está sempre em execução mesmo sob nenhuma carga. Ter um valor máximo limita o seu total possível de custo por hora.

Pode ativar o dimensionamento automático ao criar o conjunto de dimensionamento com [do Azure PowerShell](../articles/azure-monitor/platform/powershell-quickstart-samples.md#create-and-manage-autoscale-settings) ou [CLI do Azure](https://docs.microsoft.com/cli/azure/monitor/autoscale-settings). Também pode ativá-la depois do conjunto de dimensionamento é criado. Pode criar um conjunto de dimensionamento, instalar a extensão e configurar o dimensionamento automático com um [modelo Azure Resource Manager](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md). No portal do Azure, ativar o dimensionamento automático do Azure Monitor ou ativar o dimensionamento automático das definições do conjunto de dimensionamento.

![Ativar dimensionamento automático](./media/virtual-machines-autoscale/virtual-machines-autoscale-enable.png)
 
### <a name="metrics"></a>Métrica

A funcionalidade de dimensionamento automático do Azure Monitor permite-lhe aumentar o número de VMs em execução cópia de segurança ou para baixo com base na [métricas](../articles/azure-monitor/platform/autoscale-common-metrics.md). Por predefinição, as VMs fornecem métricas básicas de ao nível do anfitrião para a utilização da CPU, rede e disco. Ao configurar a recolha de dados de diagnóstico com a extensão de diagnóstico, contadores de desempenho do sistema operacional convidado adicionais ficam disponíveis para o disco, da CPU e memória.

![Critérios de métrica](./media/virtual-machines-autoscale/virtual-machines-autoscale-criteria.png)

Se a sua aplicação precisa para dimensionar com base nas métricas que não estão disponíveis através do anfitrião do, então as VMs no conjunto de dimensionamento tem de ter o [extensão de diagnóstico do Linux](../articles/virtual-machines/linux/diagnostic-extension.md) ou [extensão de diagnóstico do Windows](../articles/virtual-machines/windows/ps-extensions-diagnostics.md)instalado. Se criar um conjunto de dimensionamento com o portal do Azure, terá de também utilizar o Azure PowerShell ou a CLI do Azure para instalar a extensão com a configuração de diagnóstico que precisa.
 
### <a name="rules"></a>Regras

[Regras](../articles/monitoring-and-diagnostics/monitoring-autoscale-scale-by-custom-metric.md) combinar uma métrica com uma ação a ser executada. Quando forem cumpridas condições de regra, uma ou mais ações de dimensionamento automático são acionadas. Por exemplo, pode ter uma regra definida que aumenta o número de VMs em 1, se a utilização média da CPU está mais 85 por cento.

![Ações de dimensionamento automático](./media/virtual-machines-autoscale/virtual-machines-autoscale-actions.png)
 
### <a name="notifications"></a>Notificações

Pode [configurar acionadores](../articles/azure-monitor/platform/autoscale-webhook-email.md) para que são chamados de web específica URLs ou mensagens de correio eletrónico são enviadas com base nas regras de dimensionamento automático que criar. Os Webhooks permitem-lhe encaminhar as notificações de alertas do Azure para outros sistemas para notificações de pós-processamento ou personalizados.

## <a name="manually-scale-vms-in-a-scale-set"></a>Dimensionar manualmente as VMs num conjunto de dimensionamento

### <a name="horizontal"></a>Horizontal

Pode adicionar ou remover as VMs, alterando a capacidade do conjunto de dimensionamento. No portal do Azure, pode diminuir ou aumentar o número de VMs (mostrado como **contagem de instâncias**) no conjunto de dimensionamento em deslizante a barra de condição de substituição no ecrã de dimensionamento, esquerdo ou direito.

Com o Azure PowerShell, tem de obter o conjunto de dimensionamento objeto utilizando [Get-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/get-azvmss). Em seguida, defina o **SKU. CAPACITY** propriedade para o número de VMs que pretende e atualizar o conjunto de dimensionamento com [atualização AzVmss](https://docs.microsoft.com/powershell/module/az.compute/update-azvmss). Com a CLI do Azure, alterar a capacidade com o **– nova capacidade** parâmetro para o [dimensionamento do az vmss](/cli/azure/vmss?view=azure-cli-latest#az-vmss-scale) comando.

### <a name="vertical"></a>Vertical

Pode alterar manualmente o tamanho das VMs no portal do Azure, no ecrã de tamanho para o conjunto de dimensionamento. Pode utilizar o Azure PowerShell com o Get-AzVmss, definindo a propriedade de sku de referência de imagem e, em seguida, usando [atualização AzVmss](https://docs.microsoft.com/powershell/module/az.compute/update-azvmss) e [atualização AzVmssInstance](https://docs.microsoft.com/powershell/module/az.compute/update-azvmssinstance).

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre conjuntos de dimensionamento no [considerações de Design para conjuntos de dimensionamento de](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview.md).

