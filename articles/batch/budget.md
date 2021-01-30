---
title: Obtenha análise de custos e definir orçamentos para O Azure Batch
description: Aprenda a obter uma análise de custos, estabeleça um orçamento e reduza os custos para os recursos de computação subjacentes e licenças de software usadas para executar as suas cargas de trabalho do Batch.
ms.topic: how-to
ms.date: 01/29/2021
ms.openlocfilehash: d1fc2d15a7037e56a8056efa67d2017badb77ffd
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/30/2021
ms.locfileid: "99091332"
---
# <a name="get-cost-analysis-and-set-budgets-for-azure-batch"></a>Obtenha análise de custos e definir orçamentos para O Azure Batch

Este tópico irá ajudá-lo a entender os custos que podem estar associados ao Azure Batch, como definir um orçamento para um pool ou conta batch, e formas de reduzir os custos para cargas de trabalho do Lote.

## <a name="understand-costs-associated-with-batch-resources"></a>Compreender os custos associados aos recursos do Lote

Não existem custos para a utilização do próprio Azure Batch, embora possam existir taxas para os recursos computativos subjacentes e licenças de software usadas para executar cargas de trabalho do Batch. Os custos podem ser incorridos a partir de máquinas virtuais (VMs) num pool, transferência de dados a partir do VM, ou quaisquer dados de entrada ou saída armazenados na nuvem.

### <a name="virtual-machines"></a>Máquinas virtuais

As máquinas virtuais são o recurso mais significativo utilizado para o processamento do Lote. O custo da utilização de VMs para lote é calculado com base no tipo, quantidade e duração da utilização. As opções de faturação VM incluem [Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0003p/) ou [reserva](../cost-management-billing/reservations/save-compute-costs-reservations.md) (pagamento antecipado). Ambas as opções de pagamento têm benefícios diferentes dependendo da sua carga de trabalho do cálculo e afetarão a sua conta de forma diferente.

Cada VM numa piscina criada com [configuração de máquina virtual](nodes-and-pools.md#virtual-machine-configuration) tem um disco de OS associado que utiliza discos geridos pelo Azure. Os discos geridos pelo Azure têm um custo adicional, e outros níveis de desempenho do disco também têm custos diferentes.

### <a name="storage"></a>Armazenamento

Quando as aplicações são implementadas nos nós de Lote (VMs) utilizando [pacotes de aplicações,](batch-application-packages.md)você é faturado para os recursos de Armazenamento Azure que os seus pacotes de aplicação consomem. Também é faturado para o armazenamento de quaisquer ficheiros de entrada ou saída, tais como ficheiros de recursos e outros dados de registo.

Em geral, o custo dos dados de armazenamento associados ao Batch é muito inferior ao custo dos recursos computacional.

### <a name="networking-resources"></a>Recursos de rede

Os lotes utilizam recursos de rede, alguns dos quais têm custos associados. Em particular, para piscinas de configuração de máquinas virtuais, são utilizados equilibradores de carga padrão, que requerem endereços IP estáticos. Os equilibradores de carga utilizados pelo Batch são visíveis para [contas](accounts.md#batch-accounts) configuradas no modo de subscrição do utilizador, mas não nas do modo de serviço Batch.

Os balançadores de carga padrão incorrem em encargos para todos os dados transmitidos de e para o Batch pool VMs. Selecione APIs de lote que obtenha dados de nós de pool (como Get Task/Node File), pacotes de aplicação de tarefas, ficheiros de recursos/saída e imagens de contentores também incorrerão em encargos.

### <a name="additional-services"></a>Serviços adicionais

Dependendo dos serviços que utiliza com a sua solução Batch, poderá incorrer em taxas adicionais. Consulte a [Calculadora de Preços](https://azure.microsoft.com/pricing/calculator/) para determinar o custo de cada serviço adicional. Os serviços comumente utilizados com o Batch que podem ter custos associados incluem:

- Application Insights
- Data Factory
- Azure Monitor
- Rede Virtual
- VMs com aplicações gráficas

## <a name="view-cost-analysis-and-create-a-budget-for-a-pool"></a>Ver análise de custos e criar um orçamento para uma piscina

No portal Azure, pode criar orçamentos e alertas de gastos para as suas piscinas de Lote ou contas Batch. Orçamentos e alertas são úteis para notificar as partes interessadas de quaisquer riscos de gastos excessivos, embora seja possível que haja um atraso nos alertas de gastos e que exceda ligeiramente um orçamento.

> [!NOTE]
> A piscina neste exemplo utiliza **configuração de máquina virtual,** que é recomendada para a maioria das piscinas e tem custos baseados na estrutura de preços das Máquinas Virtuais. As piscinas que utilizam **a Configuração de Serviços cloud** são carregadas com base na estrutura de preços dos Serviços cloud.

### <a name="view-cost-analysis-for-a-batch-pool"></a>Ver análise de custos para uma piscina de lote

1. No portal Azure, digite ou selecione **Gestão de Custos + Faturação** .
1. Selecione a sua subscrição na secção **de âmbitos de faturação.**
1. Em **Cost Management**, selecione **Análise de custos**.
1. **Selecione adicionar filtro**. Na primeira entrega, selecione **Recurso**
1. Na segunda descida, selecione a piscina Batch. Quando a piscina estiver selecionada, você verá a análise de custos para a piscina, semelhante ao exemplo mostrado aqui.
    ![Screenshot mostrando análise de custos de uma piscina no portal Azure.](./media/batch-budget/pool-cost-analysis.png)

A análise de custos resultante mostra o custo da piscina, bem como os recursos que contribuem para este custo. Neste exemplo, os VMs utilizados na piscina são o recurso mais dispendioso.

### <a name="create-a-budget-for-a-batch-pool"></a>Crie um orçamento para uma piscina de lote

1. Na página **de análise de custos,** selecione **Orçamento: nenhum**.
1. **Selecione Criar novos >orçamentais**.
1. Use a janela resultante para configurar um orçamento especificamente para a sua piscina. Para mais informações, consulte [Tutorial: Criar e gerir orçamentos do Azure.](../cost-management-billing/costs/tutorial-acm-create-budgets.md)

## <a name="minimize-costs-associated-with-azure-batch"></a>Minimizar os custos associados ao Azure Batch

Dependendo do seu cenário, pode querer reduzir os custos o máximo possível. Considere usar uma ou mais destas estratégias para maximizar a eficiência das suas cargas de trabalho e reduzir custos potenciais.

### <a name="use-low-priority-virtual-machines"></a>Use máquinas virtuais de baixa prioridade

[Os VM de baixa prioridade reduzem](batch-low-pri-vms.md) o custo das cargas de trabalho do Lote, tirando partido da capacidade de computação excedentária em Azure. Quando especificar VMs de baixa prioridade nas suas piscinas, o Batch utiliza este excedente para executar a sua carga de trabalho. Pode haver poupanças substanciais de custos quando se utiliza VMs de baixa prioridade em vez de VMs dedicados.

### <a name="select-a-standard-virtual-machine-os-disk-type"></a>Selecione um tipo de disco de sistema operativo virtual padrão

O Azure oferece vários [tipos de discos VM OS](../virtual-machines/disks-types.md). A maioria das séries VM tem tamanhos que suportam tanto o armazenamento premium como o armazenamento padrão. Quando um tamanho VM 's' é selecionado para uma piscina, o Batch configura discos de SO SSD premium. Quando o tamanho VM 'não-s' é selecionado, então o tipo de disco HDD mais barato e padrão é usado. Por exemplo, discos de SO SSD premium são utilizados `Standard_D2s_v3` e os discos HDD OS standard são usados para `Standard_D2_v3` .

Os discos premium SSD OS são mais caros, mas têm um desempenho mais elevado. Os VMs com discos premium podem começar ligeiramente mais rápido do que os VMs com discos HDD OS padrão. Com o Batch, o disco DE não é muitas vezes utilizado muito, uma vez que as aplicações e ficheiros de tarefa estão localizados no disco SSD temporário do VM. Por isso, pode frequentemente selecionar o tamanho VM 'não-s' para evitar pagar o custo acrescido do SSD premium que é previsto quando um tamanho 's' VM é especificado.

### <a name="purchase-reservations-for-virtual-machine-instances"></a>Compre reservas para casos de máquinas virtuais

Se pretender utilizar o Batch por um longo período de tempo, pode reduzir o custo dos VM utilizando [Reservas Azure](../cost-management-billing/reservations/save-compute-costs-reservations.md) para as suas cargas de trabalho. Uma taxa de reserva é consideravelmente inferior à taxa de pagamento. As instâncias de máquina virtual utilizadas sem reserva são cobradas na taxa de pagamento.como-você-go. Ao comprar uma reserva, aplica-se o desconto de reserva.

### <a name="use-automatic-scaling"></a>Use o escalonamento automático

[A escala automática](batch-automatic-scaling.md) escala dinamicamente o número de VMs na sua piscina batch com base nas exigências do trabalho atual. Ao escalar a piscina com base na vida útil de um trabalho, o dimensionamento automático garante que os VMs são dimensionados e utilizados apenas quando há um trabalho a executar. Quando o trabalho está concluído, ou quando não há empregos, os VMs são automaticamente reduzidos para economizar recursos computacional. O dimensionamento permite-lhe reduzir o custo global da sua solução Batch utilizando apenas os recursos de que necessita.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [a Azure Cost Management + Billing](../cost-management-billing/cost-management-billing-overview.md)
- Saiba como [utilizar VMs de baixa prioridade com o Batch.](batch-low-pri-vms.md)
