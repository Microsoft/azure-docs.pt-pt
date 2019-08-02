---
title: Examinar o relatório de estimativa de custo no Planejador de Implantações do Azure Site Recovery | Microsoft Docs
description: Este artigo descreve como examinar o relatório de estimativa de custo no Planejador de Implantações do Azure Site Recovery para a recuperação de desastre do VMware para o Azure.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 7/29/2019
ms.author: mayg
ms.openlocfilehash: 1f825b67baf36c9a1a9187d555522f5a5955d1c7
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68620070"
---
# <a name="review-the-cost-estimation-report-in-the-site-recovery-deployment-planner-for-vmware-disaster-recovery-to-azure"></a>Examine o relatório estimativa de custo no Planejador de Implantações de Site Recovery para a recuperação de desastres do VMware para o Azure

O relatório do Planeador de Implementações fornece o resumo da estimativa de custos em folhas de [Recomendações](site-recovery-vmware-deployment-planner-analyze-report.md#recommendations) e a análise detalhada de custos na folha Estimativa de Custos. Inclui a análise detalhada de custos por VM. 

>[!Note]
>A versão atual da ferramenta planejador de implantação v 2.5 fornece estimativa de custo para replicações de VMs para Managed Disks.

### <a name="cost-estimation-summary"></a>Resumo da estimativa de custos 
O gráfico mostra a vista de resumo do custo total estimado da recuperação após desastre (DR) para o Azure da região de destino que escolheu e a moeda que especificou para a geração do relatório.
Resumo da estimativa de custos

![Resumo da estimativa de custos](media/site-recovery-vmware-deployment-planner-analyze-report/cost-estimation-summary-v2a.png)

O resumo ajuda-o a compreender o custo que tem de pagar por armazenamento, computação, rede e licença quando protege todas as VMs compatíveis para o Azure com o Azure Site Recovery. O custo é calculado para as VMs compatíveis e não para todas as VMs para as quais foram criados perfis.  
 
Pode ver o custo mensalmente ou anualmente. Saiba mais sobre as [regiões de destino suportadas](./site-recovery-vmware-deployment-planner-cost-estimation.md#supported-target-regions) e as [moedas suportadas](./site-recovery-vmware-deployment-planner-cost-estimation.md#supported-currencies).

**Custo por componentes** O custo total de recuperação de desastres é dividido em quatro componentes: Custo de computação, armazenamento, rede e Azure Site Recovery licença. O custo é calculado com base no consumo que será incorrido durante a replicação e à hora de exploração da DR para computação, armazenamento (premium e standard), ExpressRoute/VPN que está configurada entre o site no local e o Azure, e a licença do Azure Site Recovery.

**Custo por estados** O custo total da recuperação após desastre (DR) é baseado em categorias em dois estados diferentes - Replicação e exploração de DR. 

**Custo de replicação**:  O custo que será incorrido durante a replicação. Abrange o custo de armazenamento, rede e licença do Azure Site Recovery. 

**Custo de análise de recuperação de desastre**: O custo que será incorrido durante os failovers de teste. O Azure Site Recovery acelera as VMS durante a ativação pós-falha de teste. O custo de exploração da DR abrange o custo de armazenamento e computação das máquinas virtuais em execução. 

**Custo de armazenamento do Azure por Mês/Ano** Mostra o custo total de armazenamento que será incorrido para armazenamento premium e standard para a replicação e exploração de DR.

## <a name="detailed-cost-analysis"></a>Análise detalhada de custos
Os preços do Azure para computação, armazenamento, rede, etc., varia entre regiões do Azure. Pode gerar um relatório de estimativa de custos com os preços mais recentes do Azure com base na sua subscrição, na oferta está associada à sua subscrição e para a região de destino do Azure especificada na moeda especificada. Por predefinição, a ferramenta utiliza a região do Azure E.U.A. Oeste 2 e a moeda Dólar norte-americano (USD). Se tiver utilizado qualquer outra região e moeda, da próxima vez que gerar um relatório sem ID de subscrição, ID de oferta, região de destino e moeda, a ferramenta irá utilizar os preços da última região de destino utilizada e da última moeda utilizada para a estimativa de custos.
Esta secção mostra o ID da subscrição e o ID de oferta que utilizou para a geração do relatório.  Caso não tenham sido utilizados, está em branco.

No relatório completo, as células marcadas a cinzento são só de leitura. As células em branco podem ser modificadas de acordo com os seus requisitos.

![Detalhes da estimativa de custos1](media/site-recovery-hyper-v-deployment-planner-cost-estimation/cost-estimation1-h2a.png)

### <a name="overall-dr-cost-by-components"></a>Custo global de DR por componentes
A primeira secção mostra o custo global de DR por componentes e o custo de DR por estados. 

**Computação**: Custo de VMs IaaS que são executadas no Azure para necessidades de recuperação de desastre. Inclui VMs que são criadas pelo Azure Site Recovery durante as explorações de DR (ativações pós-falha de teste) e as VMs executadas no Azure como o SQL Server com Grupos de Disponibilidade Always On e controladores de domínio/Servidores de Nomes de Domínio.

**Armazenamento**: Custo do consumo de armazenamento do Azure para necessidades de DR. Inclui o consumo de armazenamento para a replicação e durante as explorações de DR.
Rede O ExpressRoute e o custo de VPN site a site para necessidades de DR. 

**Licença de ASR**: Azure Site Recovery custo de licença para todas as VMs compatíveis. Caso tenha introduzido manualmente uma VM na tabela de análise detalhada de custos, o custo da licença do Azure Site Recovery também é incluído para essa VM.

### <a name="overall-dr-cost-by-states"></a>Custo global de DR por estados
O custo total da DR é categorizado com base em dois estados diferentes - replicação e exploração de DR.

**Custo de replicação**: O custo incorre no momento da replicação. Abrange o custo de armazenamento, rede e licença do Azure Site Recovery. 

**Custo de análise de recuperação de desastre**: O custo incorre no momento das análises de DR. O Azure Site Recovery acelera as VMS durante as explorações de DR. O custo de exploração da DR abrange o custo de armazenamento e de computação das VMs em execução.
Duração total de exploração de DR num ano = Número de explorações de DR x Duração de cada exploração de DR (dias) Custo médio de exploração de DR (por mês) = Custo total de exploração de DR / 12

### <a name="storage-cost-table"></a>Tabela de custo de armazenamento:
Esta tabela mostra o custo de armazenamento premium e standard incorrido para a replicação e explorações de DR com e sem desconto.

### <a name="site-to-azure-network"></a>Site para a rede do Azure
Selecione a definição adequada de acordo com os seus requisitos. 

**ExpressRoute**: Por padrão, a ferramenta seleciona o plano mais próximo do ExpressRoute que corresponde à largura de banda de rede necessária para a replicação delta. Pode alterar o plano de acordo com os seus requisitos.

**Gateway de VPN**: Selecione o gateway de VPN se você tiver algum em seu ambiente. Por predefinição, é NA.

**Região de destino**: Região do Azure especificada para DR. O preço utilizado no relatório para computação, armazenamento, rede e licença baseia-se nos preços do Azure para essa região. 

### <a name="vm-running-on-azure"></a>VM em execução no Azure
Se tiver qualquer controlador de domínio ou VM de DNS ou VM do SQL Server com Grupos de Disponibilidade Always On em execução no Azure para DR, pode fornecer o número de VMs e o tamanho para considerar o respetivo custo de computação no custo total de DR. 

### <a name="apply-overall-discount-if-applicable"></a>Aplicar desconto global, se aplicável
Se for um parceiro ou cliente do Azure e tiver direito a qualquer desconto sobre os preços gerais do Azure, pode utilizar este campo. A ferramenta aplica o desconto (em %) sobre todos os componentes.

### <a name="number-of-virtual-machines-type-and-compute-cost-per-year"></a>Número de tipo de máquinas virtuais e custo de computação (por ano)
Esta tabela mostra o número de VMs do Windows e não Windows e o custo de computação de exploração de DR para as mesmas.

### <a name="settings"></a>Definições 

**Moeda**: A moeda na qual o relatório é gerado. Duração do custo:  Você pode exibir todos os custos do mês ou do ano inteiro. 

## <a name="detailed-cost-analysis-table"></a>Tabela de análise detalhada de custos
![Análise detalhada de custos](media/site-recovery-hyper-v-deployment-planner-cost-estimation/detailed-cost-analysis-h2a.png) A tabela lista a análise detalhada do custo por cada VM compatível. Também pode utilizar esta tabela para obter o custo estimado de DR do Azure de VMs sem perfis criados ao adicionar manualmente as VMs. É útil nos casos em que precisa de estimar os custos do Azure para uma nova implementação de recuperação após desastre sem a criação detalhada de perfis.
Para adicionar VMs manualmente: 
1.  Clique no botão «Insert row» (Inserir linha) para inserir uma nova linha entre as linhas de Início e de Fim.

2.  Preencha as seguintes colunas com base no tamanho aproximado da VM e no número de VMs que correspondem a esta configuração: 

* Número de VMs, tamanho de IaaS (a sua seleção)
* Tipo de Armazenamento (Standard/Premium)
* Tamanho de armazenamento total da VM (GB) do computador de origem
* Número de explorações de DR num ano 
* Duração de cada exploração de DR (Dias) 
* Tipo de SO
* Redundância de dados 
* Benefício Híbrido do Azure

1. Pode aplicar o mesmo valor a todas as VMs na tabela ao clicar no botão «Apply to all» (Aplicar a todos) para Número de Explorações de DR num ano, Duração de cada Exploração de DR (Dias), Redundância de dados e Benefício Híbrido do Azure.

1. Clique em «Re-calculate cost» (Voltar a calcular o custo) para atualizar o custo.

**Nome da VM**: O nome da VM.

**Número de VMs**: O número de VMs que correspondem à configuração. Pode atualizar o número de VMs existentes se as VMs com configuração semelhante não tiverem perfis criados, mas irão ser protegidas.

**Tamanho de IaaS (recomendação)** : É o tamanho da função VM da VM compatível que a ferramenta recomenda. 

**Tamanho de IaaS (sua seleção)** : Por padrão, é o mesmo que o tamanho recomendado da função VM. Pode alterar a função com base nos seus requisitos. O custo de computação baseia-se no tamanho da função VM que selecionou.

**Tipo de armazenamento**: O tipo de armazenamento que é usado pela VM. É armazenamento standard ou premium.

**Tamanho total de armazenamento da VM (GB)** : O armazenamento total da VM de origem.

**Número de análises de recuperação de desastre em um ano**: O número de vezes que você executa uma análise de recuperação de desastre em um ano. Por predefinição, é 4 vezes num ano. Pode modificar o período para VMs específicas ou aplicar o novo valor a todas as VMs ao introduzir o novo valor na linha superior e clicar no botão «Apply to all» (Aplicar a todos). Com base no número de Explorações de DR num ano e no período de duração de cada Exploração de DR, é calculado o custo total de Exploração de DR.  

**Duração de cada análise de recuperação de desastre (dias)** : A duração de cada análise de recuperação de desastre. Por predefinição, é 7 dias a cada 90 dias, de acordo com o [benefício Recuperação Após Desastre do Software Assurance](https://azure.microsoft.com/pricing/details/site-recovery). Pode modificar o período para VMs específicas ou pode aplicar um novo valor a todas as VMs ao introduzir o novo valor na linha superior e clicar no botão «Apply to all» (Aplicar a todos). O custo total de Exploração de DR é calculado com base no número de Explorações de DR num ano e no período de duração de cada Exploração de DR.
  
**Tipo de so**: O tipo de so da VM. É Windows ou Linux. Se o tipo de SO for Windows, o Benefício Híbrido do Azure pode ser aplicado a essa VM. 

**Redundância de dados**: Pode ser um dos seguintes: armazenamento com redundância local (LRS), armazenamento com redundância geográfica (GRS) ou armazenamento com redundância geográfica com acesso de leitura (RA-GRS). A predefinição é LRS. Pode alterar o tipo com base na sua conta de armazenamento para VMs específicas ou pode aplicar o novo tipo a todas as VMs, alterando o tipo da linha superior e clicando no botão «Apply to all» (Aplicar a todos).  O custo de armazenamento para a replicação é calculado com base no preço da redundância de dados que selecionou. 

**Benefício híbrido do Azure**: Você pode aplicar Benefício Híbrido do Azure às VMs do Windows, se aplicável.  A predefinição é Sim. Pode alterar a definição para VMs específicas ou atualizar todas as VMs ao clicar no botão «Apply to all» (Aplicar a todos).

**Consumo total do Azure**: Ele inclui computação, armazenamento e Azure Site Recovery custo de licença para a sua recuperação de desastre. Com base na sua seleção, mostra o custo mensal ou anualmente.

**Custo de replicação de estado estável**: Ele inclui o custo de armazenamento para replicação.

**Custo total de análise de recuperação de desastre (média)** : Ele inclui o custo de computação e armazenamento para análise de recuperação de desastre.

**Custo da licença ASR**: Azure Site Recovery custo de licença.

## <a name="supported-target-regions"></a>Regiões de destino suportadas
O Planeador de Implementações do Azure Site Recovery fornece a estimativa de custos para as seguintes regiões do Azure. Se a sua região não estiver listada abaixo, pode utilizar qualquer uma das regiões seguintes cujos preços estejam mais próximos da sua região.

eastus, eastus2, westus, centralus, northcentralus, southcentralus, northeurope, westeurope, eastasia, southeastasia, japaneast, japanwest, australiaeast, australiasoutheast, brazilsouth, southindia, centralindia, westindia, canadacentral, canadaeast, westus2, westcentralus, uksouth, ukwest, koreacentral, koreasouth 

## <a name="supported-currencies"></a>Moedas suportadas
O Planeador de Implementações do Azure Site Recovery pode gerar o relatório de custos com qualquer uma das moedas seguintes.

|Currency|Nome||Currency|Nome||Currency|Nome|
|---|---|---|---|---|---|---|---|
|ARS|Peso Argentino ($)||AUD|Dólar Australiano ($)||BRL|Real Brasileiro (R$)|
|CAD|Dólar Canadiano ($)||CHF|Franco Suíço (chf)||DKK|Coroa Dinamarquesa (kr)|
|EUR|Euro (€)||GBP|Libra Esterlina Britânica (£)||HKD|Dólar de Hong Kong (HK$)|
|IDR|Rupia da Indonésia (Rp)||INR|Rupia Indiana (₹)||JPY|Iene Japonês (¥)|
|KRW|Won Sul-Coreano (₩)||MXN|Peso Mexicano (MX$)||MYR|Ringgit Malaio (RM$)|
|NOK|Coroa Norueguesa (kr)||NZD|Dólar da Nova Zelândia ($)||RUB|Rublo Russo (руб)|
|SAR|Rial Saudita (SR)||SEK|Coroa Sueca (kr)||TWD|Dólar de Taiwan (NT$)|
|TRY|Lira Turca (TL)||USD| Dólar Norte-Americano ($)||ZAR|Rand Sul-Africano (R)|

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre como proteger [VMs de VMware para o Azure com o Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/tutorial-vmware-to-azure).
