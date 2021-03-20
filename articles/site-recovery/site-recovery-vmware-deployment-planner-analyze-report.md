---
title: Analise o relatório do Planejador de Implantação para a recuperação de desastres da VMware com a recuperação do local de Azure
description: Este artigo descreve como analisar o relatório gerado pelo Planejador de Implementação de Recuperação para a recuperação de desastres VMware para Azure, utilizando a Recuperação do Local de Azure.
author: rajeswari-mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/4/2019
ms.author: ramamill
ms.openlocfilehash: 7e2db720bb37a25b8511bd1c42c0c18e139aa216
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93186608"
---
# <a name="analyze-the-deployment-planner-report-for-vmware-disaster-recovery-to-azure"></a>Analise o relatório do Planejador de Implantação para a recuperação de desastres da VMware para Azure

O relatório gerado pelo Microsoft Excel contém as seguintes folhas:
## <a name="on-premises-summary"></a>Resumo no local
A folha do Resumo no local proporciona uma descrição geral do ambiente do VMware com perfis criados.

![Resumo no local do ambiente da VMware](media/site-recovery-vmware-deployment-planner-analyze-report/on-premises-summary-v2a.png)

**Start Date** (Data de Início) e **End Date** (Data de Fim): as datas de início e fim dos dados de criação de perfis considerados para a geração de relatórios. Por predefinição, a data de início é a data em que a criação dos perfis é iniciada e a de fim é a data em que a criação para. Podem ser os valores de “StartDate” e “EndDate”, caso o relatório seja gerado com estes parâmetros.

**Total number of profiling days (Número total de dias de criação de perfis)**: o número total de dias de criação de perfis entre as datas de início e de fim para as quais o relatório é gerado.

**Number of compatible virtual machines (Número de máquinas virtuais compatíveis)**: o número total de máquinas virtuais compatíveis para as quais são calculadas a largura de banda de rede necessária, o número de contas armazenamento, os núcleos do Microsoft Azure, os servidores de configuração e os servidores de processos adicionais.

**Total number of disks across all compatible virtual machines (Número total de discos em todas as máquinas virtuais compatíveis)**: o número que é utilizado como uma das entradas para decidir o número de servidores de configuração e de servidores de processos adicionais a utilizar na implementação.

**Average number of disks per compatible virtual machine (Número médio de discos por máquina virtual compatível)**: o número médio de discos calculado em todas as VMs compatíveis.

**Average disk size (GB) (Tamanho médio de discos [GB])**: o tamanho de disco médio calculado em todas as VNs compatíveis.

**Desired RPO (minutes) [RPO Pretendido (minutos)]**: o objetivo de ponto de recuperação predefinido ou o valor transmitido para o parâmetro “DesiredRPO” no momento da geração do relatório, para calcular a largura de banda necessária.

**Desired bandwidth (Mbps) (Largura de Banda Pretendida [Mbps])**: o valor que transmitiu para o parâmetro “Bandwidth” no momento da geração do relatório, para calcular o RPO alcançável.

**Observed typical data churn per day (GB) (Alterações a dados típicas registadas por dia [GB])**: a alteração a dados média registada em todos os dias de criação de perfis. Este número é utilizado como uma das entradas para decidir o número de servidores de configuração e de servidores de processos adicionais a utilizar na implementação.

## <a name="recommendations"></a>Recomendações

A folha de recomendações do relatório de VMware para o Azure tem os seguintes detalhes de acordo com o RPO pretendido selecionado:

![Recomendações para o relatório de VMware para o Azure](media/site-recovery-vmware-deployment-planner-analyze-report/Recommendations-v2a.png)

### <a name="profiled-data"></a>Dados de criação de perfis
![A vista de dados de criação de perfil no Deployment Planner](media/site-recovery-vmware-deployment-planner-analyze-report/profiled-data-v2a.png)

**Período de dados de criação de perfis**: o período durante o qual a criação de perfis foi executada. Por predefinição, a ferramenta utiliza todos os dados de criação de perfis para o cálculo, a não ser gere o relatório para um período específico, mediante a utilização das opções StartDate e EndDate.

**Server Name (Nome do Servidor)**: o nome ou o endereço IP do VMware vCenter ou do anfitrião ESXi para cujas VMs é criado o relatório.

**RPO Pretendido**: o objetivo de ponto de recuperação para a sua implementação. Por predefinição, a largura de banda de rede necessária é calculada para valores de RPO de 15, 30 e 60 minutos. Com base na seleção, os valores afetados são atualizados na folha. Se tiver utilizado o parâmetro *DesiredRPOinMin* ao gerar o relatório, esse valor é mostrado no resultado Desired RPO (RPO Pretendido).

### <a name="profiling-overview"></a>Descrição geral da criação de perfis

![Resultados da criação de perfis no Deployment Planner](media/site-recovery-vmware-deployment-planner-analyze-report/profiling-overview-v2a.png)

**Total Profiled Virtual Machines (Total de Máquinas Virtuais para as quais Foram Criados Perfis)**: o número total de VMs cujos dados de criação de perfis estão disponíveis. Se VMListFile tiver nomes de VMs para as quais não foram criados perfis, essas VMs não são consideradas na geração do relatório e são excluídas da contagem total de VMs para as quais foram criados perfis.

**Compatible Virtual Machines (Máquinas Virtuais Compatíveis)**: o número de VMs que podem ser protegidas para o Azure com o Site Recovery. É o número total de VMs compatíveis para as quais são calculadas a largura de banda de rede necessária, o número de contas de armazenamento, o número de núcleos do Azure e o número de servidores de configuração e de servidores de processos adicionais. Os detalhes de todas as VMs compatíveis estão disponíveis na secção “Compatible VMs” (“VMs Compatíveis”).

**Incompatible Virtual Machines (Máquinas Virtuais Não Compatíveis)**: o número de VMs para as quais foram criados perfis que são incompatíveis para proteção com o Site Recovery. Os motivos da incompatibilidade estão apontados na secção “Incompatible VMs” (“VMs Incompatíveis”), abaixo. Se VMListFile tiver nomes de VMs para as quais não foram criados perfis, essas VMs são excluídas da contagem de VMs incompatíveis. Estas VMs são listadas como “Data not found” (“Dados não encontrados”), na parte final da secção “Incompatible VMs”.

**Desired RPO (RPO Pretendido)**: o objetivo de ponto de recuperação pretendido, em minutos. O relatório é gerado para três valores de RPO: 15 (predefinição), 30 e 60 minutos. A recomendação de largura de banda no relatório é alterada com base no que selecionar na lista pendente de Desired RPO, no canto superior direito da folha. Se tiver utilizado o parâmetro *-DesiredRPO* com um valor personalizado para gerar o relatório, este valor personalizado será mostrado como a predefinição na lista pendente de Desired RPO.

### <a name="required-network-bandwidth-mbps"></a>Required network bandwidth (Mbps) (Largura de banda de rede necessária [Mbps])

![Largura de banda de rede necessária no Deployment Planner](media/site-recovery-vmware-deployment-planner-analyze-report/required-network-bandwidth-v2a.png)

**To meet RPO 100 percent of the time (Para satisfazer o RPO em 100 por cento do tempo):** é a largura de banda recomendada em Mbps a ser alocada para satisfazer o seu RPO pretendido durante 100 por cento do tempo. Esta quantidade de largura de banda tem de estar dedicada à replicação delta de estado estável de todas as VMs compatíveis, para evitar possíveis violações ao RPO.

**To meet RPO 90 percent of the time (Para satisfazer o RPO em 90 por cento do tempo)**: se não puder definir a largura de banda necessária para satisfazer o seu RPO pretendido em 100 por cento do tempo, devido aos preços da largura de banda ou a qualquer outro motivo, pode optar por utilizar uma quantidade de largura de banda inferior que possa satisfazer esse RPO durante 90 por cento do tempo. Para compreender as implicações de definir esta menor largura de banda, o relatório disponibiliza uma análise de hipóteses quanto ao número e à duração das violações de RPO que podem ser esperadas.

**Achieved Throughput (Débito Alcançado):** o débito do servidor onde executou o comando GetThroughput para a região do Microsoft Azure onde está localizada a conta de armazenamento. O número do débito indica o nível aproximado que poderá alcançar se proteger as VMs compatíveis com o Site Recovery, desde que as características de armazenamento e de rede do servidor de configuração ou do servidor de processos permaneçam iguais às do servidor de onde executou a ferramenta.

Para a replicação, deve definir a largura de banda recomendada para satisfazer o RPO durante 100 por cento do tempo. Se depois de definir a largura de banda, não vir um aumento no débito alcançado, conforme comunicado pela ferramenta, faça o seguinte:

1. Verifique se existe algum Quality of Service (QoS) de rede que esteja a limitar o débito do Site Recovery.

2. Verifique se o cofre do Site Recovery está na região física suportada mais próxima do Microsoft Azure, para minimizar a latência de rede.

3. Verifique as características do seu armazenamento local para determinar se pode melhorar o hardware (por exemplo, passar de HDD para SSD).

4. Altere as definições do Site Recovery no servidor de processos, para [aumentar a quantidade de largura de banda utilizada para a replicação](./site-recovery-plan-capacity-vmware.md#control-network-bandwidth).

Se estiver a executar a ferramenta num servidor de configuração ou num servidor de processos que já tenha VMs protegidas, execute-a algumas vezes. O número do débito alcançado altera-se, consoante a quantidade de alterações a dados que estejam a ser processadas nesse momento.

Para todas as implementações empresariais do Site Recovery, recomendamos utilizar o [ExpressRoute](https://aka.ms/expressroute).

### <a name="required-storage-accounts"></a>Required storage accounts (Contas de armazenamento necessárias)
O gráfico seguinte mostra o número total de contas de armazenamento (standard e premium) necessárias para proteger todas as VMs compatíveis. Para obter a conta de armazenamento a utilizar para cada VM, veja a secção "VM-storage placement” (“Colocação de armazenamento de VM"). Se estiver a utilizar o v2.5 do Deployment Planner, esta recomendação apenas mostra o número de contas padrão de armazenamento de cache que são necessárias para a replicação, uma vez que os dados estão a ser escritos diretamente para Discos Geridos.

![Contas de armazenamento necessárias no Deployment Planner](media/site-recovery-vmware-deployment-planner-analyze-report/required-storage-accounts-v2a.png)

### <a name="required-number-of-azure-cores"></a>Required number of Azure cores (Número de núcleos do Azure necessários)
Este resultado é o número total de núcleos a configurar antes da ativação pós-falha ou da ativação pós-falha de teste de todas as VMs compatíveis. Se não estiverem disponíveis núcleos suficientes na subscrição, o Site Recovery não conseguirá criar VMs no momento da ativação pós-falha de teste ou da ativação pós-falha.

![Número de núcleos do Azure necessários no Deployment Planner](media/site-recovery-vmware-deployment-planner-analyze-report/required-cores-v2a.png)

### <a name="required-on-premises-infrastructure"></a>Required on-premises infrastructure (Infraestrutura no local necessária)
Este número é o total de servidores de configuração e de servidores de processos adicionais a configurar e que serão suficientes para proteger todas as VMs compatíveis. Consoante as [recomendações de tamanho do servidor de configuração](/en-in/azure/site-recovery/site-recovery-plan-capacity-vmware#size-recommendations-for-the-configuration-server) suportadas, a ferramenta poderá recomendar servidores adicionais. A recomendação baseia-se na maior das configurações de alterações a dados por dia ou do número máximo de VMs protegidas (presumindo uma média de três discos por VM), a que for atingida primeiro no servidor de configuração ou no servidor de processos adicional. Os detalhes do número total de alterações a dados por dia e do número total de discos protegidos estão disponíveis na secção “Resumo no local”.

![Infraestrutura no local necessária no Deployment Planner](media/site-recovery-vmware-deployment-planner-analyze-report/required-on-premises-components-v2a.png)

### <a name="what-if-analysis"></a>Análise de hipóteses
Esta análise mostra quantas violações podem ocorrer durante o período de criação de perfis se configurar uma largura de banda inferior para que o RPO pretendido seja cumprido apenas durante 90 por cento do tempo. Podem ocorrer uma ou mais violações de RPO num determinado dia. O gráfico mostra o pico de RPO do dia.
Com base nesta análise, pode decidir se o número de violações de RPO de todos os dias e a contagem de picos do RPO por dia é aceitável com a largura de banda inferior especificada. Se for aceitável, pode alocar a menor largura de banda à replicação; caso contrário, aloque a largura de banda superior, conforme sugerido, para cumprir o RPO pretendido durante 100 por cento do tempo.

![Análise de hipóteses no Deployment Planner](media/site-recovery-vmware-deployment-planner-analyze-report/what-if-analysis-v2a.png)

### <a name="recommended-vm-batch-size-for-initial-replication"></a>Recommended VM batch size for initial replication (Tamanho de lote de VMs recomendado para a replicação inicial)
Nesta secção, recomendamos definir o número de VMs que podem ser protegidas em paralelo, para concluir a replicação inicial em 72 horas com a largura de banda sugerida, de modo a cumprir o RPO pretendido durante 100 por cento. Este valor é configurável. Para alterá-lo durante a geração de relatórios, utilize o parâmetro *GoalToCompleteIR*.

Este gráfico mostra um intervalo de valores de largura de banda e a contagem do tamanho de lotes de VMs calculado para concluir a replicação inicial em 72 horas, com base no tamanho médio das VMs detetado em todas as VMs compatíveis.

Na pré-visualização pública, o relatório não especifica que VMs devem ser incluídas nos lotes. Pode utilizar o tamanho de disco apresentado na secção “Compatible VMs” para encontrar o tamanho de cada VM e selecioná-las para um lote ou pode selecionar as VMs com base nas características conhecidas da carga de trabalho. O tempo de conclusão da replicação inicial altera-se proporcionalmente, com base no tamanho de disco real das VMs, no espaço no disco utilizado e no débito de rede disponível.

![Tamanho de lote de VMs recomendado](media/site-recovery-vmware-deployment-planner-analyze-report/ir-batching-v2a.png)

### <a name="cost-estimation"></a>Estimativa de custos
O gráfico mostra a vista de resumo do custo total estimado da recuperação após desastre (DR) para o Azure da região de destino que escolheu e a moeda que especificou para a geração do relatório.

![Resumo da estimativa de custos](media/site-recovery-vmware-deployment-planner-analyze-report/cost-estimation-summary-v2a.png)

O resumo ajuda-o a compreender o custo que tem de pagar por armazenamento, computação, rede e licença quando protege todas as VMs compatíveis para o Azure com o Azure Site Recovery. O custo é calculado para as VMs compatíveis e não para todas as VMs para as quais foram criados perfis.  

Pode ver o custo mensalmente ou anualmente. Saiba mais sobre as [regiões de destino suportadas](./site-recovery-vmware-deployment-planner-cost-estimation.md#supported-target-regions) e as [moedas suportadas](./site-recovery-vmware-deployment-planner-cost-estimation.md#supported-currencies).

**Custo por componentes** O custo total de DR é dividido em quatro componentes: custo de Computação, Armazenamento, Rede e licença do Azure Site Recovery. O custo é calculado com base no consumo que será incorrido durante a replicação e à hora de exploração da DR para computação, armazenamento (premium e standard), ExpressRoute/VPN que está configurada entre o site no local e o Azure, e a licença do Azure Site Recovery.

**Custo por estados** O custo total da recuperação após desastre (DR) é baseado em categorias em dois estados diferentes - Replicação e exploração de DR.

**Custo de replicação**: o custo que será incorrido durante a replicação. Abrange o custo de armazenamento, rede e licença do Azure Site Recovery.

**Custo de Exploração de DR**: o custo que será incorrido durante as ativações pós-falha de teste. O Azure Site Recovery acelera as VMS durante a ativação pós-falha de teste. O custo de exploração da DR abrange o custo de armazenamento e computação das máquinas virtuais em execução.

**Custo de armazenamento do Azure por Mês/Ano** Mostra o custo total de armazenamento que será incorrido para armazenamento premium e standard para a replicação e exploração de DR.
Pode ver a análise de custo detalhada por VM na folha [Estimativa de Custos](site-recovery-vmware-deployment-planner-cost-estimation.md).

### <a name="growth-factor-and-percentile-values-used"></a>Growth factor and percentile values used (Fator de crescimento e valores de percentil utilizados)
Esta secção da parte inferior da folha mostra o valor do percentil utilizado em todos os contadores de desempenho das VNs com perfis criados (a predefinição é o percentil 95) e o fator de crescimento (a predefinição é 30 por cento) utilizado em todos os cálculos.

![Growth factor and percentile values used (Fator de crescimento e valores de percentil utilizados)](media/site-recovery-vmware-deployment-planner-analyze-report/growth-factor-v2a.png)

## <a name="recommendations-with-available-bandwidth-as-input"></a>Recomendações tendo a largura de banda disponível como entrada

![Recomendações tendo a largura de banda disponível como entrada](media/site-recovery-vmware-deployment-planner-analyze-report/profiling-overview-bandwidth-input-v2a.png)

Poderá haver casos em que sabe que não pode definir uma largura de banda com mais de x Mbps para a replicação do Site Recovery. A ferramenta permite-lhe introduzir a largura de banda disponível (mediante a utilização do parâmetro -Bandwidth durante a geração do relatório) e obter o RPO alcançável em minutos. Com este valor de RPO alcançável, pode decidir se precisa de configurar largura de banda adicional ou se não há inconveniente em ter uma solução de recuperação após desastre com este RPO.

![RPO alcançável para largura de banda de 500 Mbps](media/site-recovery-vmware-deployment-planner-analyze-report/achievable-rpo-v2a.png)

## <a name="vm-storage-placement"></a>Colocação do armazenamento de VMs

>[!Note]
>O Planejador de Implementação v2.5 recomenda a colocação de armazenamento para máquinas que se replicarão diretamente para discos geridos.

![Colocação do armazenamento de VMs](media/site-recovery-vmware-deployment-planner-analyze-report/vm-storage-placement-v2a.png)

**Tipo de armazenamento de replicação**: Um disco gerido padrão ou premium, que é utilizado para replicar todos os VMs correspondentes mencionados na coluna **VMs to Place.**

**Tipo de conta de armazenamento de** registo : Todos os registos de replicação são armazenados numa conta de armazenamento padrão.

**Prefixo sugerido para a conta de armazenamento**: O prefixo de três caracteres sugerido que pode ser usado para nomear a conta de armazenamento de cache. Pode utilizar o seu próprio prefixo, mas a sugestão da ferramenta segue a [convenção de nomenclatura de partições para contas de armazenamento](/en-in/azure/storage/blobs/storage-performance-checklist).

**Nome da conta de registo sugerido**: O nome da conta de armazenamento depois de incluir o prefixo sugerido. Substitua o nome dentro dos parênteses (< e >) pela sua entrada personalizada.

**Resumo da colocação**: Um resumo dos discos necessários para proteger os VMs por tipo de armazenamento. Inclui o número total de VMs, o tamanho total provisível em todos os discos e o número total de discos.

**Virtual Machines to Place (Máquinas Virtuais a Colocar)**: uma lista de todas as VMs que devem ser colocadas nesta conta de armazenamento específica, para otimizar o desempenho e a utilização.

## <a name="compatible-vms"></a>Compatible VMs (VMs Compatíveis)
![Folha de cálculo do Excel de VMs compatíveis](media/site-recovery-vmware-deployment-planner-analyze-report/compatible-vms-v2a.png)

**VM Name (Nome da VM)**: o nome ou o endereço IP da VM que é utilizado em VMListFile quando é gerado um relatório. Esta coluna também apresenta os discos (VMDKs) que estão ligados às VMs. Para distinguir VMs do vCenter com nomes ou endereços IP duplicados, os nomes incluem o nome de anfitrião ESXi. O anfitrião ESXi listado é aquele em que a VM foi colocada quando a ferramenta detetou durante o período de criação de perfis.

**VM Compatibilidade**: Valores são **Sim** e **Sim \* *_. _* Sim** é para os \* casos em que o VM é apto para [SSDs premium](../virtual-machines/disks-types.md). Aqui, o disco de alterações a dados ou IOPS elevados com perfis criados enquadra-se na categoria P20 ou P30, mas o tamanho do mesmo faz com que seja mapeado para P10 ou P20. A conta de armazenamento decide para que tipo de disco de armazenamento premium mapear os discos com base no tamanho destes. Por exemplo:
* < 128 GB é P10.
* 128 GB a 256 GB é P15
* 256 GB a 512 GB é P20.
* 512 GB a 1024 GB é P30.
* 1025 GB a 2048 GB é P40.
* 2049 GB a 4095 GB é P50.

Por exemplo, se as características de carga de trabalho de um disco o colocarem na categoria P20 ou P30, mas o tamanho o mapear para um tipo de disco de armazenamento premium mais baixo, a ferramenta marca que VM como **Sim** \* . Também lhe recomenda que altere o tamanho do disco de origem, para se enquadrar no tipo de disco de armazenamento premium aconselhado, ou que altere o tipo de disco de destino a seguir à ativação pós-falha.

**Storage Type (Tipo de Armazenamento)**: standard ou premium.

**Asrseeddisk (Disco Gerido) criado para replicação**: O nome do disco que é criado quando ativa a replicação. Armazena os dados e as suas fotos em Azure.

**IOPS de Leitura/Escrita n Pico (com Fator de Crescimento)**: IOPS de leitura/escrita da carga de trabalho dentro do pico no disco (a predefinição é o percentil 95), incluindo o fator de crescimento futuro (a predefinição são 30%). Tenha em conta que o total de IOPS de leitura/escrita de uma VM nem sempre é a soma de IOPS de leitura/escrita dos discos individuais da mesma, porque o pico destas operações da VM é o pico da soma de IOPS de leitura/escrita dos discos individuais da VM durante todos os minutos do período de criação de perfis.

**Alterações a Dados em Mbps no Pico (com Fator de Crescimento)**: taxa de alterações a dados dentro do pico no disco (a predefinição é o percentil 95), incluindo o fator de crescimento futuro (a predefinição são 30%). Tenha em conta que o total de alterações a dados da VM nem sempre é a soma das alterações a dados dos discos individuais da mesma, porque o pico de alterações a dados da VM é o pico da soma das alterações a dados dos respetivos discos individuais durante todos os minutos do período de criação de perfis.

**Azure VM Size (Tamanho da VM do Azure)**: o tamanho mapeado ideal da máquina virtual dos Serviços Cloud do Azure para esta VM no local. O mapeamento baseia-se na memória da VM no local, no número de discos/núcleos/NICs e IOPS de leitura/escrita. A recomendação é sempre o tamanho de VM do Azure mais baixo que corresponda a todas as características da VM no local.

**Number of Disks (Número de Discos)**: o número total de discos (VMDKs) de máquina virtual na VM.

**Disk size (GB) [Tamanho do disco (GB)]**: o tamanho total de configuração de todos os discos na VM. A ferramenta também mostra o tamanho dos discos individuais na VM.

**Cores (Núcleos)**: o número de núcleos de CPU na VM.

**Memory (MB)**: a RAM na VM.

**NICs**: o número de NICs na VM.

**Boot Type (Tipo de Arranque)**: o tipo de arranque da VM. Pode ser BIOS ou EFI.  Atualmente, o Azure Site Recovery suporta VMs de EFI do Windows Server (Windows Server 2012, 2012 R2 e 2016), desde que o número de partições no disco de arranque seja inferior a 4 e que o tamanho do setor de arranque seja 512 bytes. Para proteger as VMs de EFI, a versão do serviço de mobilidade do Azure Site Recovery tem de ser 9.13 ou superior. Apenas a ativação pós-falha é suportada para VMs de EFI. A reativação pós-falha não é suportada.  

**Tipo de SO**: é o tipo de SO da VM. Pode ser Windows ou Linux, ou outros, com base no modelo do VMware vSphere que escolheu ao criar a VM.  

## <a name="incompatible-vms"></a>Incompatible VMs (VMs Não Compatíveis)

![Folha de cálculo do Excel de VMs incompatíveis
](media/site-recovery-vmware-deployment-planner-analyze-report/incompatible-vms-v2a.png)

**VM Name (Nome da VM)**: o nome ou o endereço IP da VM que é utilizado em VMListFile quando é gerado um relatório. Esta coluna também apresenta os VMDKs que estão ligados às VMs. Para distinguir VMs do vCenter com nomes ou endereços IP duplicados, os nomes incluem o nome de anfitrião ESXi. O anfitrião ESXi listado é aquele em que a VM foi colocada quando a ferramenta detetou durante o período de criação de perfis.

**VM Compatibility (Compatibilidade de VM)**: indica a razão pela qual a VM especificada é incompatível para utilização com o Site Recovery. São descritas as razões para todos os discos incompatíveis da VM, que, com base nos [limites do armazenamento](/en-in/azure/storage/common/scalability-targets-standard-account) publicados, podem ser as seguintes:

* Tamanho errado do disco de dados ou tamanho errado do disco de so. [Reveja](vmware-physical-azure-support-matrix.md#azure-vm-requirements) os limites de suporte. 
* O tamanho total da VM (replicação + ativação pós-falha de teste) excede o limite de tamanho da conta de armazenamento suportado (35 TB). Geralmente, esta incompatibilidade ocorre quando um disco individual na VM tem uma característica de desempenho que excede os limites máximos suportados pelo Azure ou o Site Recovery relativamente ao armazenamento standard. Uma instância deste género envia a VM para a zona de armazenamento premium. No entanto, o tamanho máximo suportado das contas de armazenamento premium são 35 TB e não é possível proteger VMs protegidas individuais em várias contas de armazenamento. Tenha também em atenção que, quando é executada uma ativação pós-falha de teste numa VM protegida, esta é executada na mesma conta de armazenamento na qual a replicação está em curso. Neste caso, configure duas vezes o tamanho do disco para que a replicação progrida e a ativação pós-falha de teste seja concluída em paralelo.

* O IOPS de origem excede o limite de IOPS de armazenamento suportado de 7500 por disco.

* O IOPS de origem excede o limite de IOPS suportado pelo armazenamento de 80 000 por VM.

* O churn médio de dados excede o limite de 20 MB/s suportados para o tamanho médio de E/S para o disco.

* O pico de alterações a dados em todos os discos na VM excede o limite máximo de pico de alterações a dados suportado pelo Site Recovery de 54 MB/s por VM.

* A média de IOPS de escrita efetiva excede o limite de IOPS suportado pelo Site Recovery de 840 por disco.

* O armazenamento de instantâneos calculado excede o limite de armazenamento de instantâneos suportado de 10 TB.

* O total de alterações a dados por dia ultrapassa o limite de alterações suportadas por dia de 2 GB por um Servidor de Processos.


**Pico de IOPS de R/W (com Fator de Crescimento)**: o pico de IOPS da carga de trabalho no disco (a predefinição é o percentil 95), incluindo o fator de crescimento futuro (a predefinição são 30 por cento). Tenha em conta que o total de IOPS de leitura/escrita da VM nem sempre é a soma de IOPS de leitura/escrita dos discos individuais da mesma, porque o pico destas operações da VM é o pico da soma de IOPS de leitura/escrita dos discos individuais da VM durante todos os minutos do período de criação de perfis.

**Pico de Alterações a Dados em Mbps (com Fator de Crescimento)**: o pico da taxa de alterações a dados no disco (a predefinição é o percentil 95), incluindo o fator de crescimento futuro (a predefinição são 30 por cento). Tenha em conta que o total de alterações a dados da VM nem sempre é a soma das alterações a dados dos discos individuais da mesma, porque o pico de alterações a dados da VM é o pico da soma das alterações a dados dos respetivos discos individuais durante todos os minutos do período de criação de perfis.

**Number of Disks (Número de Discos)**: o número total de VMDKs na VM.

**Disk size (GB) [Tamanho do disco (GB)]**: o tamanho total de configuração de todos os discos na VM. A ferramenta também mostra o tamanho dos discos individuais na VM.

**Cores (Núcleos)**: o número de núcleos de CPU na VM.

**Memory (MB) (Memória [MB])**: a quantidade de RAM na VM.

**NICs**: o número de NICs na VM.

**Boot Type (Tipo de Arranque)**: o tipo de arranque da VM. Pode ser BIOS ou EFI.  Atualmente, o Azure Site Recovery suporta VMs de EFI do Windows Server (Windows Server 2012, 2012 R2 e 2016), desde que o número de partições no disco de arranque seja inferior a 4 e que o tamanho do setor de arranque seja 512 bytes. Para proteger as VMs de EFI, a versão do serviço de mobilidade do Azure Site Recovery tem de ser 9.13 ou superior. Apenas a ativação pós-falha é suportada para VMs de EFI. A reativação pós-falha não é suportada.

**Tipo OS**: É do tipo de SO do VM. Pode ser Windows ou Linux, ou outros, com base no modelo do VMware vSphere que escolheu ao criar a VM.

## <a name="azure-site-recovery-limits"></a>Limites do Azure Site Recovery
A tabela seguinte fornece os limites do Azure Site Recovery. Estes limites baseiam-se nos nossos testes, mas não abrangem todas as combinações de E/S de aplicações possíveis. Os resultados reais podem variar consoante a combinação de E/S da sua aplicação. Para obter os melhores resultados, mesmo após o planeamento da implementação, recomendamos sempre que faça testes exaustivos às aplicações através da emissão de uma ativação pós-falha de teste, para ter a perspetiva verdadeira quanto ao desempenho da aplicação.

**Alvo de armazenamento de replicação** | **Tamanho médio de E/S do disco de origem** |**Média de alterações a dados do disco de origem** | **Total de dados de disco de origem por dia**
---|---|---|---
Armazenamento Standard | 8 KB | 2 MB/s | 168 GB por disco
Disco Premium P10 ou P15 | 8 KB  | 2 MB/s | 168 GB por disco
Disco Premium P10 ou P15 | 16 KB | 4 MB/s |  336 GB por disco
Disco Premium P10 ou P15 | 32 KB ou superior | 8 MB/s | 672 GB por disco
Disco Premium P20 ou P30 ou P40 ou P50 | 8 KB    | 5 MB/s | 421 GB por disco
Disco Premium P20 ou P30 ou P40 ou P50 | 16 KB ou superior | 20 MB/s | 1684 GB por disco

**Alterações a dados de origem** | **Limite Máximo**
---|---
Alterações a dados de pico em todos os discos numa VM | 54 MB/s
Máximo de alterações a dados por dia suportadas por um Servidor de Processos | 2 TB

Estes são números médios, que pressupõem uma sobreposição de 30 por cento de E/S. O Site Recovery é capaz de processar um débito superior com base no rácio de sobreposição, em tamanhos de escrita maiores e no comportamento real de E/S da carga de trabalho. Os números anteriores pressupõem um atraso típico de aproximadamente cinco minutos. Ou seja, depois de os dados serem carregados, são processados e é criado um ponto de recuperação ao fim de cinco minutos.


## <a name="cost-estimation"></a>Estimativa de custos
Saiba mais sobre a [estimativa de custos](site-recovery-vmware-deployment-planner-cost-estimation.md).


## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre a [estimativa de custos](site-recovery-vmware-deployment-planner-cost-estimation.md).