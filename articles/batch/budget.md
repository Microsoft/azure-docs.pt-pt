---
title: Análise de custos e orçamentos
description: Aprenda a obter uma análise de custos, estabeleça um orçamento e reduza os custos para os recursos de computação subjacentes e licenças de software usadas para executar as suas cargas de trabalho do Batch.
ms.topic: how-to
ms.date: 01/21/2021
ms.openlocfilehash: 1a950f23b7ecee11dd7da5414b7eed9e87277850
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98679323"
---
# <a name="cost-analysis-and-budgets-for-azure-batch"></a>Análise de custos e orçamentos para o Azure Batch

Não existem custos para a utilização do próprio Azure Batch, embora possam existir taxas para os recursos computativos subjacentes e licenças de software usadas para executar cargas de trabalho do Batch. Os custos podem ser incorridos a partir de máquinas virtuais (VMs) num pool, transferência de dados a partir do VM, ou quaisquer dados de entrada ou saída armazenados na nuvem. Este tópico irá ajudá-lo a entender de onde vêm os custos, como definir um orçamento para uma piscina ou conta batch, e formas de reduzir os custos para as cargas de trabalho do Batch.

## <a name="batch-resources"></a>Recursos de lote

As máquinas virtuais são o recurso mais significativo utilizado para o processamento do Lote. O custo da utilização de VMs para lote é calculado com base no tipo, quantidade e duração da utilização. As opções de faturação VM incluem [Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0003p/) ou [reserva](../cost-management-billing/reservations/save-compute-costs-reservations.md) (pagamento antecipado). Ambas as opções de pagamento têm benefícios diferentes dependendo da sua carga de trabalho do cálculo e afetarão a sua conta de forma diferente.

Quando as aplicações são implementadas nos nós de Lote (VMs) utilizando [pacotes de aplicações,](batch-application-packages.md)você é faturado para os recursos de Armazenamento Azure que os seus pacotes de aplicação consomem. Também é faturado para o armazenamento de quaisquer ficheiros de entrada ou saída, tais como ficheiros de recursos e outros dados de registo. Em geral, o custo dos dados de armazenamento associados ao Batch é muito inferior ao custo dos recursos computacional. Cada VM numa piscina criada com [configuração de máquina virtual](nodes-and-pools.md#virtual-machine-configuration) tem um disco de OS associado que utiliza discos geridos pelo Azure. Os discos geridos pelo Azure têm um custo adicional, e outros níveis de desempenho do disco também têm custos diferentes.

As piscinas de lotes utilizam recursos de rede. Em particular, para piscinas **de configuração virtual,** são utilizados equilibradores de carga padrão, que requerem endereços IP estáticos. Os esquilibradores de carga utilizados pelo Batch são visíveis para contas **de Subscrição de Utilizador,** mas não estão visíveis para contas **de Serviço de Lote.** Os balançadores de carga padrão incorrem em encargos para todos os dados transmitidos de e para o Lote pool VMs; selecione APIs de lote que obtenha dados de nós de pool (tais como Get Task/Node File), pacotes de aplicação de tarefas, ficheiros de recursos/saída e imagens de contentores incorrerão em encargos.

### <a name="additional-services"></a>Serviços adicionais

Serviços que não incluem VMs e armazenamento podem ter em conta o custo da sua conta Batch.

Outros serviços comumente utilizados com o Batch podem incluir:

- Application Insights
- Data Factory
- Azure Monitor
- Rede Virtual
- VMs com aplicações gráficas

Dependendo dos serviços que utiliza com a sua solução Batch, poderá incorrer em taxas adicionais. Consulte a [Calculadora de Preços](https://azure.microsoft.com/pricing/calculator/) para determinar o custo de cada serviço adicional.

## <a name="cost-analysis-and-budget-for-a-pool"></a>Análise de custos e orçamento para uma piscina

No portal Azure, pode criar orçamentos e alertas de gastos para as suas piscinas de Lote ou contas Batch. Orçamentos e alertas são úteis para notificar as partes interessadas de quaisquer riscos de gastos excessivos, embora seja possível que haja um atraso nos alertas de gastos e que exceda ligeiramente um orçamento.

Neste exemplo, vamos ver a análise de custos de um lote individual.

1. No portal Azure, digite ou selecione **Gestão de Custos + Faturação** .
1. Selecione a sua subscrição na secção **de âmbitos de faturação.**
1. Em **Cost Management**, selecione **Análise de custos**.
1. **Selecione adicionar filtro**. Na primeira entrega, selecione **Recurso**
1. Na segunda descida, selecione a piscina Batch. Quando a piscina estiver selecionada, você verá a análise de custos para a piscina, semelhante ao exemplo mostrado aqui.
    ![Screenshot mostrando análise de custos de uma piscina no portal Azure.](./media/batch-budget/pool-cost-analysis.png)

A análise de custos resultante mostra o custo da piscina, bem como os recursos que contribuem para este custo. Neste exemplo, os VMs utilizados na piscina são o recurso mais dispendioso.

Para criar um orçamento para o pool select **Budget: nenhum**, em seguida, selecione **Criar novo orçamento >**. Agora use a janela para [configurar um orçamento](../cost-management-billing/costs/tutorial-acm-create-budgets.md) especificamente para a sua piscina.

> [!NOTE]
> O Azure Batch é construído com base nos Serviços Azure Cloud e na tecnologia Azure Virtual Machines. Ao escolher **a Configuração dos Serviços em Nuvem,** é cobrado com base na estrutura de preços dos Serviços cloud. Ao escolher **a Configuração virtual da máquina,** é cobrado com base na estrutura de preços das Máquinas Virtuais. O exemplo nesta página utiliza a **Configuração da Máquina Virtual,** que é recomendada para a maioria dos pools de Lote.

## <a name="minimize-cost"></a>Minimizar o custo

A utilização de vários serviços de VMs e Azure por longos períodos de tempo pode ser dispendiosa. Considere usar estas estratégias para maximizar a eficiência das suas cargas de trabalho e reduzir os seus custos.

### <a name="low-priority-virtual-machines"></a>Máquinas virtuais de baixa prioridade

[Os VM de baixa prioridade reduzem](batch-low-pri-vms.md) o custo das cargas de trabalho do Lote, tirando partido da capacidade de computação excedentária em Azure. Quando especificar VMs de baixa prioridade nas suas piscinas, o Batch utiliza este excedente para executar a sua carga de trabalho. Pode haver poupanças substanciais de custos quando se utiliza VMs de baixa prioridade em vez de VMs dedicados.

### <a name="virtual-machine-os-disk-type"></a>Tipo de disco de so máquina virtual

O Azure oferece vários [tipos de discos VM OS](../virtual-machines/disks-types.md). A maioria das séries VM tem tamanhos que suportam tanto o armazenamento premium como o armazenamento padrão. Quando um tamanho VM 's' é selecionado para uma piscina, o Batch configura discos de SO SSD premium. Quando o tamanho VM 'não-s' é selecionado, então o tipo de disco HDD mais barato e padrão é usado. Por exemplo, discos de SO SSD premium são utilizados `Standard_D2s_v3` e os discos HDD OS standard são usados para `Standard_D2_v3` .

Os discos premium SSD OS são mais caros, mas têm um desempenho mais elevado. Os VMs com discos premium podem começar ligeiramente mais rápido do que os VMs com discos HDD OS padrão. Com o Batch, o disco DE não é muitas vezes utilizado muito, uma vez que as aplicações e ficheiros de tarefa estão localizados no disco SSD temporário do VM. Por isso, pode frequentemente selecionar o tamanho VM 'não-s' para evitar pagar o custo acrescido do SSD premium que é previsto quando um tamanho 's' VM é especificado.

### <a name="reserved-virtual-machine-instances"></a>Casos de máquinas virtuais reservados

Se pretender utilizar o Batch por um longo período de tempo, pode reduzir o custo dos VM utilizando [Reservas Azure](../cost-management-billing/reservations/save-compute-costs-reservations.md) para as suas cargas de trabalho. Uma taxa de reserva é consideravelmente inferior à taxa de pagamento. As instâncias de máquina virtual utilizadas sem reserva são cobradas na taxa de pagamento.como-você-go. Ao comprar uma reserva, aplica-se o desconto de reserva.

### <a name="automatic-scaling"></a>Dimensionamento automático

[A escala automática](batch-automatic-scaling.md) escala dinamicamente o número de VMs na sua piscina batch com base nas exigências do trabalho atual. Ao escalar a piscina com base na vida útil de um trabalho, o dimensionamento automático garante que os VMs são dimensionados e utilizados apenas quando há um trabalho a executar. Quando o trabalho está concluído, ou quando não há empregos, os VMs são automaticamente reduzidos para economizar recursos computacional. O dimensionamento permite-lhe reduzir o custo global da sua solução Batch utilizando apenas os recursos de que necessita.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre as APIs do [lote e as ferramentas](batch-apis-tools.md) disponíveis para construir e monitorizar soluções batch.  
- Saiba como [utilizar VMs de baixa prioridade com o Batch.](batch-low-pri-vms.md)
