---
title: Análise de custos e orçamento
description: Aprenda a obter uma análise de custos e detetete um orçamento para os recursos computacionais subjacentes e licenças de software usadas para executar as suas cargas de trabalho do Batch.
ms.topic: article
ms.date: 07/19/2019
ms.openlocfilehash: c3c3203882d003d7fbb5f9d5092a1c93886f313a
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82117493"
---
# <a name="cost-analysis-and-budgets-for-azure-batch"></a>Análise de custos e orçamentos para o Lote Azure

Não há nenhum custo para o próprio Lote Azure, apenas os recursos de computação subjacentes e licenças de software usadas para executar cargas de trabalho de Batch. A um nível elevado, os custos são incorridos de máquinas virtuais (VMs) num pool, transferência de dados do VM, ou quaisquer dados de entrada ou saída armazenados na nuvem. Vamos dar uma olhada em alguns componentes chave do Batch para entender de onde vêm os custos, como definir um orçamento para uma piscina ou conta, e algumas técnicas para tornar as suas cargas de trabalho do Lote mais eficientes em termos de custos.

## <a name="batch-resources"></a>Recursos de lote

As máquinas virtuais são o recurso mais significativo utilizado para o processamento de Lotes. O custo da utilização de VMs para o Lote é calculado com base no tipo, quantidade e duração da utilização. As opções de faturação vM incluem [Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0003p/) ou [reserva](../cost-management-billing/reservations/save-compute-costs-reservations.md) (pague antecipadamente). Ambas as opções de pagamento têm benefícios diferentes dependendo da carga de trabalho da sua computação, e ambos os modelos de pagamento irão afetar a sua conta de forma diferente.

Quando as aplicações são implementadas para os nós de Lote (VMs) utilizando pacotes de [aplicações,](batch-application-packages.md)você é cobrado para os recursos de Armazenamento Azure que os seus pacotes de aplicação consomem. Também é cobrado para o armazenamento de quaisquer ficheiros de entrada ou saída, tais como ficheiros de recursos e outros dados de registo. Em geral, o custo dos dados de armazenamento associados ao Batch é muito inferior ao custo dos recursos computacionais. Cada VM numa piscina criada com **VirtualMachineConfiguration** tem um disco OPERATIVO associado que utiliza discos geridos pelo Azure. Os discos geridos pelo Azure têm um custo adicional, e outros níveis de desempenho do disco também têm custos diferentes.

Os pools de lotes utilizam recursos de networking. Em particular, para os conjuntos **VirtualMachineConfiguration** são utilizados os equilibradores de carga padrão, que requerem endereços IP estáticos. Os equilibradores de carga utilizados pelo Batch são visíveis para as contas de **subscrição** do utilizador, mas não são visíveis para as contas **do Serviço de Lote.** Os equilibradores de carga padrão incorrem em encargos para todos os dados passados de e para VMs do pool de lote; selecione APIs de lote que recuperem dados de nós de piscina (como Obter Tarefa/Ficheiro Node), pacotes de aplicação de tarefas, ficheiros de recursos/saída e imagens de contentores incorrerão em cargas.

### <a name="additional-services"></a>Serviços adicionais

Os serviços que não incluem VMs e armazenamento podem ter em conta o custo da sua conta Batch.

Outros serviços comumente utilizados com lote podem incluir:

- Application Insights
- Data Factory
- Azure Monitor
- Rede Virtual
- VMs com aplicações gráficas

Dependendo dos serviços que utiliza com a sua solução Batch, poderá incorrer em taxas adicionais. Consulte a [Calculadora](https://azure.microsoft.com/pricing/calculator/) de Preços para determinar o custo de cada serviço adicional.

## <a name="cost-analysis-and-budget-for-a-pool"></a>Análise de custos e orçamento para uma piscina

Através do portal Azure, pode criar orçamentos e alertas de gastos para a sua conta de Lote ou Lote. Os orçamentos e alertas são úteis para notificar as partes interessadas de quaisquer riscos de gastos excessivos. É possível que haja um atraso nos alertas de gastos e que exceda ligeiramente um orçamento. Neste exemplo, veremos a análise de custos de um pool de lote individual.

1. No portal Azure, selecione **Cost Management + Faturação** a partir da barra de navegação esquerda.
1. Selecione a sua subscrição na secção **Minhas assinaturas**
1. Vá à análise de **custos** sob a secção de Gestão de **Custos** da barra de navegação esquerda, que mostrará uma vista como esta:
1. Selecione **Adicionar filtro**. Na primeira gota, selecione **Resource** ![Selecione o filtro de recursos](./media/batch-budget/resource-filter.png)
1. Na segunda descida, selecione a piscina batch. Quando a piscina for selecionada, a análise de custos será semelhante à seguinte análise.
    ![Análise de custos de uma piscina](./media/batch-budget/pool-cost-analysis.png)

A análise de custos resultante mostra o custo da piscina, bem como os recursos que contribuem para este custo. Neste exemplo, os VMs utilizados na piscina são o recurso mais dispendioso.

Para criar um orçamento para o pool select **Budget: nenhum**, e, em seguida, selecione **Criar novos >orçamentais **. Agora use a janela para configurar um orçamento especificamente para a sua piscina.

Para obter mais informações sobre a configuração de um orçamento, consulte [Criar e gerir os orçamentos do Azure.](../cost-management-billing/costs/tutorial-acm-create-budgets.md)

> [!NOTE]
> O Azure Batch é construído com base na tecnologia Azure Cloud Services e Azure Virtual Machines. Ao escolher a **Configuração de Serviços cloud,** é cobrado com base na estrutura de preços dos Serviços cloud. Quando escolhe a Configuração da **Máquina Virtual,** é carregado com base na estrutura de preços das Máquinas Virtuais. O exemplo nesta página utiliza a **Configuração**da Máquina Virtual .

## <a name="minimize-cost"></a>Minimizar o custo

A utilização de vários serviços vMs e Azure por longos períodos de tempo pode ser dispendiosa. Felizmente, existem serviços disponíveis para ajudar a reduzir os seus gastos, bem como estratégias para maximizar a eficiência da sua carga de trabalho.

### <a name="low-priority-virtual-machines"></a>Máquinas virtuais de baixa prioridade

Os VMs de baixa prioridade reduzem o custo das cargas de trabalho do Lote, tirando partido da capacidade de computação excedentária em Azure. Quando especifica VMs de baixa prioridade nas suas piscinas, o Batch utiliza este excedente para executar a sua carga de trabalho. Existe uma poupança substancial de custos utilizando VMs de baixa prioridade em vez de VMs dedicados.

Saiba mais sobre como configurar VMs de baixa prioridade para a sua carga de trabalho em [Utilizar VMs de baixa prioridade com Lote](batch-low-pri-vms.md).

### <a name="virtual-machine-os-disk-type"></a>Tipo de disco de máquina virtual

Existem vários tipos de [disco VM OS](../virtual-machines/windows/disks-types.md). A maioria das séries VM têm tamanhos que suportam armazenamento premium e padrão. Quando um tamanho VM 's' é selecionado para uma piscina, o Batch configura discos SSD OS premium. Quando o tamanho vm 'não-s' é selecionado, então o tipo de disco HDD mais barato e padrão é utilizado. Por exemplo, os discos SSD `Standard_D2s_v3` OS premium são utilizados e `Standard_D2_v3`os discos HDD OS padrão são utilizados para .

Os discos Premium SSD OS são mais caros, mas têm um desempenho mais elevado e os VMs com discos premium podem começar ligeiramente mais rápidos do que os VMs com discos hDD os padrão. Com o Batch, o disco OS não é frequentemente utilizado tanto quanto as aplicações e ficheiros de tarefas estão localizados no disco SSD temporário vMs. Portanto, em muitos casos, não há necessidade de pagar o custo aumentado para o SSD premium que é provisionado quando um tamanho vM 's' é especificado.

### <a name="reserved-virtual-machine-instances"></a>Casos de máquinas virtuais reservadas

Se pretender utilizar o Batch durante um longo período de tempo, pode economizar no custo dos VMs utilizando [reservas Azure](../cost-management-billing/reservations/save-compute-costs-reservations.md) para as suas cargas de trabalho. Uma taxa de reserva é consideravelmente inferior a uma taxa de pagamento. As instâncias de máquinas virtuais utilizadas sem reserva são cobradas à taxa pay-as-you-go. Se comprar uma reserva, o desconto de reserva é aplicado e já não é cobrado pelas tarifas pay-as-you-go.

### <a name="automatic-scaling"></a>Dimensionamento automático

[A escala automática](batch-automatic-scaling.md) escala dinamicamente o número de VMs na sua piscina de Lote com base nas exigências do trabalho atual. Ao escalonar a piscina com base na vida útil de um trabalho, a escala automática garante que os VMs aumentaram e são utilizados apenas quando há um trabalho a desempenhar. Quando o trabalho está concluído, ou não há empregos, os VMs são automaticamente reduzidos para poupar recursos computacionais. A escala permite-lhe reduzir o custo global da sua solução Batch utilizando apenas os recursos de que necessita.

Para obter mais informações sobre escala automática, consulte automaticamente os nós de [computação numa piscina de Lote Azure](batch-automatic-scaling.md).

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre as [APIs do Lote e as ferramentas](batch-apis-tools.md) disponíveis para a construção e monitorização das soluções do Lote.  

- Saiba mais sobre [VMs de baixa prioridade com Lote](batch-low-pri-vms.md).
