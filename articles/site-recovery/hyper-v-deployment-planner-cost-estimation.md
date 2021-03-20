---
title: Reveja o relatório de estimativa de custos do Planeamento de Recuperação do Local Azure para a recuperação de hiper-VMs para Azure| Microsoft Docs
description: Este artigo descreve como rever o relatório de estimativa de custos gerado o Azure Site Recovery Planner para a recuperação de desastres hiper-V para Azure.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/9/2019
ms.author: mayg
ms.openlocfilehash: 29457f2f5021fed9d8785f5764c4119de4be1fa9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "95999217"
---
# <a name="cost-estimation-report-by-azure-site-recovery-deployment-planner"></a>Relatório de estimativa de custos do Planeador de Implementações do Azure Site Recovery 

O relatório do Planeador de Implementações do Azure Site Recovery fornece o resumo da estimativa de custos nas folhas [Recomendações](hyper-v-deployment-planner-analyze-report.md#recommendations) e a análise detalhada de custos na folha Estimativa de Custos. Inclui a análise detalhada de custos por VM. 

### <a name="cost-estimation-summary"></a>Resumo da estimativa de custos 
O gráfico mostra a vista de resumo do custo total estimado da recuperação após desastre (DR) para o Azure da região de destino que escolheu e a moeda que especificou para a geração do relatório.

![Resumo da estimativa de custos](media/hyper-v-azure-deployment-planner-cost-estimation/cost-estimation-summary-h2a.png)

O resumo ajuda-o a compreender o custo que tem de pagar por armazenamento, computação, rede e licença quando protege as VMs compatíveis com o Azure Site Recovery. O custo é calculado para as VMs compatíveis e não para todas as VMs para as quais foram criados perfis. 
 
Pode ver o custo mensalmente ou anualmente. Saiba mais sobre as [regiões de destino suportadas](./hyper-v-deployment-planner-cost-estimation.md#supported-target-regions) e as [moedas suportadas](./hyper-v-deployment-planner-cost-estimation.md#supported-currencies).

**Custo por componentes**: o custo total de DR é dividido em quatro componentes: custo de computação, armazenamento, rede e licenciamento do Site Recovery. O custo é calculado com base no consumo incorrido durante a replicação e no momento da exploração de DR. Para os cálculos, são utilizados a computação, o armazenamento (premium e standard), o ExpressRoute/VPN configurado entre o site no local e o licenciamento do Site Recovery.

**Custo por estados**: a categoria de custo total da recuperação após desastre (DR) é baseada em dois estados diferentes, replicação e exploração de DR. 

**Custo da replicação**: o custo que é incorrido durante a replicação. Abrange o custo de armazenamento, de rede e de licenciamento do Site Recovery. 

**Custo de exploração de DR**: o custo que é incorrido durante as ativações pós-falha de teste. O Site Recovery acelera as VMS durante a ativação pós-falha de teste. O custo de exploração de DR abrange o custo de armazenamento e computação das VMs em execução. 

**Custo de armazenamento do Azure por Mês/Ano**: o custo total de armazenamento que é incorrido para armazenamento premium e standard para replicação e exploração de DR.

## <a name="detailed-cost-analysis"></a>Análise detalhada de custos
Os preços do Azure para computação, armazenamento e rede varia entre regiões do Azure. Pode gerar um relatório de estimativa de custos com os preços mais recentes do Azure com base na sua subscrição, na oferta associada à sua subscrição e na região de destino do Azure indicada na moeda especificada. Por predefinição, a ferramenta utiliza a região do Azure E.U.A. Oeste 2 e a moeda Dólar norte-americano (USD). Se utilizar qualquer outra região e moeda, da próxima vez que gerar um relatório sem ID de subscrição, ID de oferta, região de destino e moeda, a ferramenta utiliza os preços da última região de destino utilizada e da moeda utilizada para a estimativa de custos.

Esta secção mostra o ID da subscrição e o ID de oferta que utilizou para a geração do relatório. Se não forem utilizados, estará em branco.

No relatório completo, as células marcadas a cinzento são só de leitura. As células em branco podem ser modificadas de acordo com os seus requisitos.

![Detalhes da estimativa de custos](media/hyper-v-azure-deployment-planner-cost-estimation/cost-estimation1-h2a.png)

### <a name="overall-dr-costs-by-components"></a>Custos globais de DR por componentes
A primeira secção mostra o custo global de DR por componentes e o custo de DR por estados. 

**Computação**: o custo das VMs IaaS que são executadas no Azure para as necessidades de DR. Inclui as VMs criadas pelo Site Recovery durante as explorações de DR (ativações pós-falha de teste). Também inclui as VMs em execução no Azure, como o SQL Server com grupos de disponibilidade Always On e controladores de domínio ou servidores de nomes de domínio.

**Armazenamento**: o custo do consumo de armazenamento do Azure para as necessidades de DR. Inclui o consumo de armazenamento para a replicação e durante as explorações de DR.

**Rede**: custo do ExpressRoute e da rede de VPNs para as necessidades de DR. 

**Licença de recuperação do local**: O custo da licença de recuperação do local para todos os VMs compatíveis. Caso tenha introduzido manualmente uma VM na tabela de análise detalhada de custos, o custo da licença do Site Recovery também é incluído para essa VM.

### <a name="overall-dr-costs-by-states"></a>Custos globais de DR por estados
O custo total da DR é categorizado com base em dois estados diferentes, replicação e exploração de DR.

**Replicação**: o custo incorrido do momento da replicação. Abrange o custo de armazenamento, de rede e de licenciamento do Site Recovery. 

**Exploração de DR**: o custo incorrido no momento das explorações de DR. O Site Recovery acelera as VMS durante as explorações de DR. O custo de exploração de DR abrange o custo de armazenamento e de computação das VMs em execução.

* Duração total de exploração de DR num ano = número de explorações de DR x duração de cada exploração de DR (dias)
* Custo médio de exploração de DR (por mês) = custo total de exploração de DR / 12

### <a name="storage-cost-table"></a>Tabela de custos de armazenamento
Esta tabela mostra os custos de armazenamento premium e standard incorridos para a replicação e explorações de DR com e sem descontos.

### <a name="site-to-azure-network"></a>Site para a rede do Azure
Selecione a definição adequada de acordo com os seus requisitos. 

**ExpressRoute**: por predefinição, a ferramenta seleciona o plano mais próximo do ExpressRoute que corresponda à largura de banda de rede necessária para a replicação de diferenças. Pode alterar o plano de acordo com os seus requisitos.

**Tipo de Gateway de VPN**: selecione o Gateway de VPN do Azure se tiver algum no seu ambiente. Por predefinição, é NA.

**Região alvo**: Região de Azure especificada para DR. O preço utilizado no relatório para computação, armazenamento, rede e licença baseia-se nos preços do Azure para essa região. 

### <a name="vm-running-on-azure"></a>VM em execução no Azure
Talvez tenha um controlador de domínio, uma VM de DNS ou uma VM do SQL Server com grupos de disponibilidade Always On em execução no Azure para a DR. Pode fornecer o número de VMs e o tamanho a ter em consideração no custo de computação no custo total de DR. 

### <a name="apply-overall-discount-if-applicable"></a>Aplicar desconto global, se aplicável
Se for um parceiro ou cliente do Azure e tiver direito a qualquer desconto sobre os preços gerais do Azure, pode utilizar este campo. A ferramenta aplica o desconto (em percentagem) sobre todos os componentes.

### <a name="number-of-virtual-machines-type-and-compute-cost-per-year"></a>Número de tipo de máquinas virtuais e custo de computação (por ano)
Esta tabela mostra o número de VMs do Windows e não Windows e o custo de computação de exploração de DR para as mesmas.

### <a name="settings"></a>Definições 
**Com disco gerido**: esta definição especifica se o disco gerido está a ser utilizado no momento das explorações de DR. A predefinição é **Sim**. Se tiver definido **-UseManagedDisks** como **Não**, é utilizado o preço de disco não gerido para o cálculo do custo.

**Moeda**: a moeda na qual o relatório é gerado.

**Duração do custo**: Pode ver todos os custos durante o mês ou durante todo o ano. 

## <a name="detailed-cost-analysis-table"></a>Tabela de análise detalhada de custos
![Análise detalhada de custos](media/hyper-v-azure-deployment-planner-cost-estimation/detailed-cost-analysis-h2a.png)

A tabela lista a análise detalhada do custo por cada VM compatível. Também pode utilizar esta tabela para obter o custo estimado de DR do Azure de VMs sem perfis criados ao adicionar manualmente as VMs. Estas informações são úteis nos casos em que precisa de estimar os custos do Azure para uma nova implementação de DR sem a criação detalhada de perfis.

Para adicionar VMs manualmente:

1. Selecione **Inserir linha** para inserir uma nova linha entre as linhas **Início** e **Fim**.

1. Preencha as seguintes colunas com base no tamanho aproximado da VM e no número de VMs que correspondem a esta configuração: 

    a. **Número de VMs**

    b. **Tamanho de IaaS (a sua seleção)**

    c. **Tipo de armazenamento Standard/Premium**

    d. **Tamanho de armazenamento total da VM (GB)**

    e. **Número de explorações de DR num ano**

    f. **Duração de cada exploração de DR (Dias)**

    exemplo, **Tipo de SO**

    h. **Redundância de dados**

    i. **Benefício Híbrido do Azure**

1. Pode aplicar o mesmo valor a todas as VMs na tabela ao selecionar **Aplicar a todos** para **Número de Explorações de DR num ano**, **Duração de cada Exploração de DR (Dias)**, **Redundância de dados** e **Benefício Híbrido do Azure**.

1. Selecione **Voltar a calcular o custo** para atualizar o custo.

**VM Name (Nome da VM)**: o nome da VM.

**Number of VMs (Número de VMs)**: o número de VMs que correspondem à configuração. Pode atualizar o número de VMs existentes se uma configuração semelhante de VMs não tiver perfis criados, mas forem protegidas.

**Tamanho de IaaS (Recomendação)**: o tamanho da função da VM compatível que a ferramenta recomenda. 

**Tamanho de IaaS (a sua seleção)**: por predefinição, é o mesmo que o tamanho da função da VM recomendado. Pode alterar a função com base nos seus requisitos. O custo de computação baseia-se no tamanho da função VM que selecionou.

**Tipo de armazenamento**: o tipo de armazenamento utilizado pela VM. É armazenamento standard ou premium.

**VM total storage size (GB) [Tamanho de armazenamento total da VM (GB)]**: o armazenamento total da VM.

**Número de Explorações de DR num ano**: o número de vezes que executa explorações de DR num ano. Por predefinição, é quatro vezes por ano. Pode modificar o período para VMs específicas ou aplicar o novo valor a todas as VMs. Introduza o novo valor na linha superior e selecione **Aplicar a todos**. Com base no número de explorações de DR num ano e no período de duração de cada exploração de DR, é calculado o custo total de exploração de DR. 

**Duração de cada Exploração de DR (Dias)**: a duração de cada exploração de DR. Por predefinição, é 7 dias a cada 90 dias, de acordo com o [benefício Recuperação Após Desastre do Software Assurance](https://azure.microsoft.com/pricing/details/site-recovery). Pode modificar o período para VMs específicas ou aplicar um novo valor a todas as VMs. Introduza um novo valor na linha superior e selecione **Aplicar a todos**. O custo total de exploração de DR é calculado com base no número de explorações de DR num ano e no período de duração de cada exploração de DR.
 
**Tipo de SO**: o tipo de sistema operativo (SO) da VM. É Windows ou Linux. Se o tipo de SO for Windows, o Benefício Híbrido do Azure pode ser aplicado a essa VM. 

**Redundância de dados**: pode ser armazenamento localmente redundante, armazenamento georredundante ou armazenamento georredundante com acesso de leitura. A predefinição é armazenamento localmente redundante. Pode alterar o tipo com base na sua conta de armazenamento para VMs específicas ou pode aplicar o novo tipo a todas as VMs. Introduza o tipo na linha superior e selecione **Aplicar a todos**. O custo de armazenamento para a replicação é calculado com base no preço da redundância de dados que selecionou. 

**Benefício Híbrido do Azure**: pode aplicar o Benefício Híbrido do Azure a VMs do Windows, se aplicável. A predefinição é **Sim**. Pode alterar a definição para VMs específicas ou atualizar todas as VMs. Selecione **Aplicar a todos**.

**Consumo total do Azure**: inclui o custo de computação, armazenamento e licença do Site Recovery para a DR. Com base na sua seleção, mostra o custo mensal ou anualmente.

**Custo de replicação de estado de repouso**: o custo de armazenamento para a replicação.

**Custo total de Exploração de DR (média)**: o custo de computação e armazenamento para as explorações de DR.

**Custo da licença de recuperação do site Azure**: O custo da licença de recuperação do site.

## <a name="supported-target-regions"></a>Regiões de destino suportadas
O Planeador de Implementações do Site Recovery fornece a estimativa de custos para as seguintes regiões do Azure. Se a sua região não estiver listada aqui, pode utilizar qualquer uma das regiões seguintes cujos preços estejam mais próximos da sua região:

eastus, eastus2, westus, centralus, northcentralus, southcentralus, northeurope, westeurope, eastasia, southeastasia, japaneast, japanwest, australiaeast, australiasoutheast, brazilsouth, southindia, centralindia, westindia, canadacentral, canadaeast, westus2, westcentralus, uksouth, ukwest, koreacentral, koreasouth 

## <a name="supported-currencies"></a>Moedas suportadas
O Planeador de Implementações do Site Recovery pode gerar o relatório de custos com qualquer uma das moedas seguintes.

|Moeda|Name|Moeda|Name|Moeda|Name|
|---|---|---|---|---|---|---|---|
|ARS|Peso argentino ($)|AUD|Dólar australiano ($)|BRL|Real brasileiro (R$)|
|CAD|Dólar canadiano ($)|CHF|Franco suíço (chf)|DKK|Coroa dinamarquesa (kr)|
|EUR|Euro (€)|GBP|Libra britânica (£)|HKD|Dólar de Hong Kong (HK$)|
|IDR|Rupia da Indonésia (Rp)|INR|Rupia indiana (₹)|JPY|Iene japonês (¥)|
|KRW|Coreano venceu (₩)|MXN|Peso mexicano (MX$)|MYR|Ringgit malaio (RM$)|
|NOK|Coroa norueguesa (kr)||NZD|Dólar da Nova Zelândia ($)||RUB|Rublo russo (руб)|
|SAR|Rial saudita (SR)|SEK|Coroa sueca (kr)|TWD|Dólar de Taiwan (NT$)|
|TRY|Lira turca (TL)|USD| Dólar norte-americano ($)|ZAR|Rand da África do Sul (R)|

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre como proteger [VMs de Hyper-V para o Azure com o Site Recovery](hyper-v-azure-tutorial.md).
