---
title: Analisar o relatório do Azure Site Recovery Deployment Planner para recuperação após desastre do VMware para o Azure | Documentos da Microsoft
description: Este artigo descreve como analisar o relatório gerado pelo Azure Site Recovery Deployment Planner para a recuperação de desastre do VMware para o Azure.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 3/20/2019
ms.author: mayg
ms.openlocfilehash: cbea6785239c70a3cdb229d0811497f051224238
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58286351"
---
# <a name="analyze-the-azure-site-recovery-deployment-planner-report-for-vmware-disaster-recovery-to-azure"></a>Analisar o relatório do Azure Site Recovery Deployment Planner para a recuperação de desastre do VMware para o Azure

O relatório gerado pelo Microsoft Excel contém as seguintes folhas:
## <a name="on-premises-summary"></a>Resumo no local
A folha do Resumo no local proporciona uma descrição geral do ambiente do VMware com perfis criados.

![Resumo no local do ambiente da VMware](media/site-recovery-vmware-deployment-planner-analyze-report/on-premises-summary-v2a.png)

**Data de início** e **data de fim**: As datas de início e de fim dos dados de criação de perfis considerados para a geração do relatório. Por predefinição, a data de início é a data em que a criação dos perfis é iniciada e a de fim é a data em que a criação para. Podem ser os valores de “StartDate” e “EndDate”, caso o relatório seja gerado com estes parâmetros.

**Número total de dias de criação de perfis**: O número total de dias de criação de perfis entre as datas de início e de fim para o qual o relatório é gerado.

**Número de máquinas virtuais compatíveis**: O número total de VMs compatíveis para as quais são calculadas a largura de banda de rede necessária, o número de contas de armazenamento, núcleos da Microsoft Azure, servidores de configuração e servidores de processos adicionais.

**Número total de discos em todas as máquinas virtuais compatíveis**: O número é utilizado como uma das entradas para decidir o número de servidores de configuração e servidores de processos adicionais a utilizar na implementação.

**Número médio de discos por máquina virtual compatível**: O número médio de discos calculado em todas as VMs compatíveis.

**Média de tamanho do disco (GB)**: O tamanho do disco médio calculado em todas as VMs compatíveis.

**Desired RPO (minutos)**: A recuperação de predefinição objetivo de ponto ou o valor transmitido para o parâmetro "DesiredRPO" no momento da geração de relatórios para calcular a largura de banda necessária.

**Desired largura de banda (Mbps)**: O valor que transmitiu para o parâmetro "Bandwidth" no momento da geração de relatórios para calcular o RPO alcançável.

**Alterações a dados típicas observadas por dia (GB)**: A alteração a dados média registada em dias de criação de todos os perfis. Este número é utilizado como uma das entradas para decidir o número de servidores de configuração e de servidores de processos adicionais a utilizar na implementação.

## <a name="recommendations"></a>Recomendações

>[!Note]
>Quando replicar diretamente para os discos geridos, ignore a recomendação para o número de contas de armazenamento.

A folha de recomendações do relatório de VMware para o Azure tem os seguintes detalhes de acordo com o RPO pretendido selecionado:

![Recomendações para o relatório de VMware para o Azure](media/site-recovery-vmware-deployment-planner-analyze-report/Recommendations-v2a.png)

### <a name="profiled-data"></a>Dados de criação de perfis
![A vista de dados de criação de perfil no Deployment Planner](media/site-recovery-vmware-deployment-planner-analyze-report/profiled-data-v2a.png)

**Período de criação de perfis de dados**: O período durante o qual a criação de perfis foi executada. Por predefinição, a ferramenta utiliza todos os dados de criação de perfis para o cálculo, a não ser gere o relatório para um período específico, mediante a utilização das opções StartDate e EndDate.

**Nome do servidor**: O nome ou endereço IP do VMware vCenter ou anfitrião ESXi do relatório cujas VMs é gerado.

**RPO pretendido**: O objetivo de ponto de recuperação para a sua implementação. Por predefinição, a largura de banda de rede necessária é calculada para valores de RPO de 15, 30 e 60 minutos. Com base na seleção, os valores afetados são atualizados na folha. Se tiver utilizado o parâmetro *DesiredRPOinMin* ao gerar o relatório, esse valor é mostrado no resultado Desired RPO (RPO Pretendido).

### <a name="profiling-overview"></a>Descrição geral da criação de perfis

![Resultados da criação de perfis no Deployment Planner](media/site-recovery-vmware-deployment-planner-analyze-report/profiling-overview-v2a.png)

**Total Profiled máquinas virtuais**: O número total de VMs cujos dados de criação de perfis estão disponíveis. Se VMListFile tiver nomes de VMs para as quais não foram criados perfis, essas VMs não são consideradas na geração do relatório e são excluídas da contagem total de VMs para as quais foram criados perfis.

**Máquinas virtuais compatíveis**: O número de VMs que podem ser protegidas para o Azure utilizando a recuperação de Site. É o número total de VMs compatíveis para as quais são calculadas a largura de banda de rede necessária, o número de contas de armazenamento, o número de núcleos do Azure e o número de servidores de configuração e de servidores de processos adicionais. Os detalhes de todas as VMs compatíveis estão disponíveis na secção “Compatible VMs” (“VMs Compatíveis”).

**Máquinas virtuais incompatíveis**: O número de VMS para as que são incompatíveis para proteção com o Site Recovery. Os motivos da incompatibilidade estão apontados na secção “Incompatible VMs” (“VMs Incompatíveis”), abaixo. Se VMListFile tiver nomes de VMs para as quais não foram criados perfis, essas VMs são excluídas da contagem de VMs incompatíveis. Estas VMs são listadas como “Data not found” (“Dados não encontrados”), na parte final da secção “Incompatible VMs”.

**RPO pretendido**: A recuperação desejada objetivo de ponto de, em minutos. O relatório é gerado para três valores de RPO: 15 (predefinição), 30 e 60 minutos. A recomendação de largura de banda no relatório é alterada com base no que selecionar na lista pendente de Desired RPO, no canto superior direito da folha. Se tiver utilizado o parâmetro *-DesiredRPO* com um valor personalizado para gerar o relatório, este valor personalizado será mostrado como a predefinição na lista pendente de Desired RPO.

### <a name="required-network-bandwidth-mbps"></a>Required network bandwidth (Mbps) (Largura de banda de rede necessária [Mbps])

![Largura de banda de rede necessária no Deployment Planner](media/site-recovery-vmware-deployment-planner-analyze-report/required-network-bandwidth-v2a.png)

**Para satisfazer o RPO durante 100 por cento do tempo:** A largura de banda recomendada em Mbps a ser alocada para satisfazer o seu RpO pretendido durante 100 por cento do tempo. Esta quantidade de largura de banda tem de estar dedicada à replicação delta de estado estável de todas as VMs compatíveis, para evitar possíveis violações ao RPO.

**Para satisfazer o RPO durante 90 por cento do tempo**: Devido a preços de banda larga ou por qualquer outro motivo, se não é possível definir a largura de banda necessária para satisfazer o seu RpO pretendido durante 100 por cento do tempo, pode optar por ficar com uma definição de largura de banda inferior que pode satisfazer o seu RpO pretendido durante 90 por cento do tempo. Para compreender as implicações de definir esta menor largura de banda, o relatório disponibiliza uma análise de hipóteses quanto ao número e à duração das violações de RPO que podem ser esperadas.

**Débito alcançado:** O débito do servidor em que executou o comando GetThroughput para a região do Microsoft Azure onde está localizada a conta de armazenamento. O número do débito indica o nível aproximado que poderá alcançar se proteger as VMs compatíveis com o Site Recovery, desde que as características de armazenamento e de rede do servidor de configuração ou do servidor de processos permaneçam iguais às do servidor de onde executou a ferramenta.

Para a replicação, deve definir a largura de banda recomendada para satisfazer o RPO durante 100 por cento do tempo. Se depois de definir a largura de banda, não vir um aumento no débito alcançado, conforme comunicado pela ferramenta, faça o seguinte:

1. Verifique se existe algum Quality of Service (QoS) de rede que esteja a limitar o débito do Site Recovery.

2. Verifique se o cofre do Site Recovery está na região física suportada mais próxima do Microsoft Azure, para minimizar a latência de rede.

3. Verifique as características do seu armazenamento local para determinar se pode melhorar o hardware (por exemplo, passar de HDD para SSD).

4. Altere as definições do Site Recovery no servidor de processos, para [aumentar a quantidade de largura de banda utilizada para a replicação](./site-recovery-plan-capacity-vmware.md#control-network-bandwidth).

Se estiver a executar a ferramenta num servidor de configuração ou num servidor de processos que já tenha VMs protegidas, execute-a algumas vezes. O número do débito alcançado altera-se, consoante a quantidade de alterações a dados que estejam a ser processadas nesse momento.

Para todas as implementações empresariais do Site Recovery, recomendamos utilizar o [ExpressRoute](https://aka.ms/expressroute).

### <a name="required-storage-accounts"></a>Required storage accounts (Contas de armazenamento necessárias)
O gráfico seguinte mostra o número total de contas de armazenamento (standard e premium) necessárias para proteger todas as VMs compatíveis. Para obter a conta de armazenamento a utilizar para cada VM, veja a secção "VM-storage placement” (“Colocação de armazenamento de VM").

![Contas de armazenamento necessárias no Deployment Planner](media/site-recovery-vmware-deployment-planner-analyze-report/required-storage-accounts-v2a.png)

### <a name="required-number-of-azure-cores"></a>Required number of Azure cores (Número de núcleos do Azure necessários)
Este resultado é o número total de núcleos a configurar antes da ativação pós-falha ou da ativação pós-falha de teste de todas as VMs compatíveis. Se não estiverem disponíveis núcleos suficientes na subscrição, o Site Recovery não conseguirá criar VMs no momento da ativação pós-falha de teste ou da ativação pós-falha.

![Número de núcleos do Azure necessários no Deployment Planner](media/site-recovery-vmware-deployment-planner-analyze-report/required-cores-v2a.png)

### <a name="required-on-premises-infrastructure"></a>Required on-premises infrastructure (Infraestrutura no local necessária)
Este número é o total de servidores de configuração e de servidores de processos adicionais a configurar e que serão suficientes para proteger todas as VMs compatíveis. Consoante as [recomendações de tamanho do servidor de configuração](https://aka.ms/asr-v2a-on-prem-components) suportadas, a ferramenta poderá recomendar servidores adicionais. A recomendação baseia-se na maior das configurações de alterações a dados por dia ou do número máximo de VMs protegidas (presumindo uma média de três discos por VM), a que for atingida primeiro no servidor de configuração ou no servidor de processos adicional. Os detalhes do número total de alterações a dados por dia e do número total de discos protegidos estão disponíveis na secção “Resumo no local”.

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

**Custo por componentes** custo total de DR é dividido em quatro componentes: Custo de licença de computação, armazenamento, rede e do Azure Site Recovery. O custo é calculado com base no consumo que será incorrido durante a replicação e à hora de exploração da DR para computação, armazenamento (premium e standard), ExpressRoute/VPN que está configurada entre o site no local e o Azure, e a licença do Azure Site Recovery.

**Custo por estados** O custo total da recuperação após desastre (DR) é baseado em categorias em dois estados diferentes - Replicação e exploração de DR.

**Custo de replicação**:  O custo que será incorrido durante a replicação. Abrange o custo de armazenamento, rede e licença do Azure Site Recovery.

**Custo de exploração de DR**: O custo que será incorrido durante as ativações pós-falha de teste. O Azure Site Recovery acelera as VMS durante a ativação pós-falha de teste. O custo de exploração da DR abrange o custo de armazenamento e computação das máquinas virtuais em execução.

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
>Quando replicar diretamente para os discos geridos, não é necessário se preocupar sobre o número de contas de armazenamento. Para armazenamento, utilize apenas a recomendação no tipo de armazenamento (Standard ou Premium). O mesmo tipo é aplicável para discos geridos.

![Colocação do armazenamento de VMs](media/site-recovery-vmware-deployment-planner-analyze-report/vm-storage-placement-v2a.png)

**Tipo de armazenamento de disco**: De qualquer uma conta de armazenamento standard ou premium, que é utilizada para replicar todas as VMs correspondentes mencionadas na **VMs para o lugar** coluna.

**Sugestões de prefixo**: O prefixo de três carateres sugerido que pode ser utilizado para atribuir nomes a conta de armazenamento. Pode utilizar o seu próprio prefixo, mas a sugestão da ferramenta segue a [convenção de nomenclatura de partições para contas de armazenamento](https://aka.ms/storage-performance-checklist).

**Sugestões de nome de conta**: O nome de conta de armazenamento depois de incluir o prefixo sugerido. Substitua o nome dentro dos parênteses (< e >) pela sua entrada personalizada.

**Conta de armazenamento de registo**: Todos os registos de replicação são armazenados numa conta de armazenamento standard. Relativamente a VMs que são replicadas para uma conta de armazenamento premium, configure uma conta de armazenamento standard adicional para o armazenamento de registos. Uma conta de armazenamento de registos standard individual pode ser utilizada por várias contas de armazenamento de replicações premium. As VMs que são replicadas para contas de armazenamento standard utilizam a mesma conta de armazenamento para os registos.

**Sugestões de nome de conta de registo**: Registo nome da sua conta depois de incluir o prefixo sugerido. Substitua o nome dentro dos parênteses (< e >) pela sua entrada personalizada.

**Resumo da colocação**: Um resumo do total de VMs de carga na conta de armazenamento no momento da replicação e ativação pós-falha ou ativação pós-falha de teste. Inclui o número total de VMs mapeadas para a conta de armazenamento, o total de IOPS de leitura/escrita em todas as VMs que estão a ser colocadas nesta conta de armazenamento, o total de IOPS de escrita (replicação), o tamanho total configurado em todos os discos e o número total de discos.

**Máquinas virtuais para o lugar**: Uma lista de todas as VMs que devem ser colocadas nesta conta de armazenamento específica para otimizar o desempenho e utilização.

## <a name="compatible-vms"></a>Compatible VMs (VMs Compatíveis)
![Folha de cálculo do Excel de VMs compatíveis](media/site-recovery-vmware-deployment-planner-analyze-report/compatible-vms-v2a.png)

**Nome da VM**: O nome da VM ou o endereço IP que é utilizado em VMListFile quando é gerado um relatório. Esta coluna também apresenta os discos (VMDKs) que estão ligados às VMs. Para distinguir VMs do vCenter com nomes ou endereços IP duplicados, os nomes incluem o nome de anfitrião ESXi. O anfitrião ESXi listado é aquele em que a VM foi colocada quando a ferramenta detetou durante o período de criação de perfis.

**Compatibilidade VM**: Os valores são **Sim** e **Sim**\*. **Sim** \* é para instâncias nas quais a VM se enquadra [premium SSDs](../virtual-machines/windows/disks-types.md). Aqui, o disco de alterações a dados ou IOPS elevados com perfis criados enquadra-se na categoria P20 ou P30, mas o tamanho do mesmo faz com que seja mapeado para P10 ou P20. A conta de armazenamento decide para que tipo de disco de armazenamento premium mapear os discos com base no tamanho destes. Por exemplo:
* < 128 GB é P10.
* 128 GB a 256 GB é P15
* 256 GB a 512 GB é P20.
* 512 GB a 1024 GB é P30.
* 1025 GB a 2048 GB é P40.
* 2049 GB a 4095 GB é P50.

Por exemplo, se as características da carga de trabalho de um disco o colocarem na categoria P20 ou P30, mas o tamanho do mesmo o mapear para um tipo de disco de armazenamento premium inferior, a ferramenta marca essa VM como **Yes**\*. Também lhe recomenda que altere o tamanho do disco de origem, para se enquadrar no tipo de disco de armazenamento premium aconselhado, ou que altere o tipo de disco de destino a seguir à ativação pós-falha.

**Tipo de armazenamento**: Standard ou premium.

**Sugestões de prefixo**: O prefixo da conta de armazenamento de três caracteres.

**Conta de armazenamento**: O nome que utiliza o prefixo da conta de armazenamento sugerido.

**Picos de R/W IOPS (com fator de crescimento)**: O pico carga de trabalho IOPS de leitura/escrita no disco (a predefinição é o percentil 95), incluindo o fator de crescimento futuro (a predefinição é 30 por cento). Tenha em conta que o total de IOPS de leitura/escrita de uma VM nem sempre é a soma de IOPS de leitura/escrita dos discos individuais da mesma, porque o pico destas operações da VM é o pico da soma de IOPS de leitura/escrita dos discos individuais da VM durante todos os minutos do período de criação de perfis.

**Alterações a dados de pico em Mbps (com fator de crescimento)**: O pico das alterações a dados no disco (a predefinição é o percentil 95), incluindo o fator de crescimento futuro (a predefinição é 30 por cento). Tenha em conta que o total de alterações a dados da VM nem sempre é a soma das alterações a dados dos discos individuais da mesma, porque o pico de alterações a dados da VM é o pico da soma das alterações a dados dos respetivos discos individuais durante todos os minutos do período de criação de perfis.

**Tamanho da VM do Azure**: O tamanho mapeado ideal serviços Cloud do Azure máquinas virtuais para esta VM no local. O mapeamento baseia-se na memória da VM no local, no número de discos/núcleos/NICs e IOPS de leitura/escrita. A recomendação é sempre o tamanho de VM do Azure mais baixo que corresponda a todas as características da VM no local.

**Número de discos**: O número total de discos (VMDKs) da máquina virtual na VM.

**Tamanho (GB) de disco**: O tamanho total de configuração de todos os discos da VM. A ferramenta também mostra o tamanho dos discos individuais na VM.

**Núcleos**: O número de núcleos de CPU na VM.

**Memória (MB)**: A quantidade de RAM na VM.

**NICs**: O número de NICs na VM.

**Tipo de arranque**: Tipo de arranque da VM. Pode ser BIOS ou EFI.  Atualmente, o Azure Site Recovery suporta VMs de EFI do Windows Server (Windows Server 2012, 2012 R2 e 2016), desde que o número de partições no disco de arranque seja inferior a 4 e que o tamanho do setor de arranque seja 512 bytes. Para proteger as VMs de EFI, a versão do serviço de mobilidade do Azure Site Recovery tem de ser 9.13 ou superior. Apenas a ativação pós-falha é suportada para VMs de EFI. A reativação pós-falha não é suportada.  

**Tipo de SO**: É o tipo de SO da VM. Pode ser Windows ou Linux, ou outros, com base no modelo do VMware vSphere que escolheu ao criar a VM.  

## <a name="incompatible-vms"></a>Incompatible VMs (VMs Não Compatíveis)

![Folha de cálculo do Excel de VMs incompatíveis
](media/site-recovery-vmware-deployment-planner-analyze-report/incompatible-vms-v2a.png)

**Nome da VM**: O nome da VM ou o endereço IP que é utilizado em VMListFile quando é gerado um relatório. Esta coluna também apresenta os VMDKs que estão ligados às VMs. Para distinguir VMs do vCenter com nomes ou endereços IP duplicados, os nomes incluem o nome de anfitrião ESXi. O anfitrião ESXi listado é aquele em que a VM foi colocada quando a ferramenta detetou durante o período de criação de perfis.

**Compatibilidade VM**: Indica por que a VM especificada é incompatível para utilização com o Site Recovery. São descritas as razões para todos os discos incompatíveis da VM, que, com base nos [limites do armazenamento](https://aka.ms/azure-storage-scalbility-performance) publicados, podem ser as seguintes:

* O tamanho do disco é >4095 GB. Atualmente, o Armazenamento do Azure não suporta tamanhos de discos superiores a 4095 GB.

* O disco do SO é >2048 GB. Atualmente, o Armazenamento do Azure não suporta tamanhos de discos de SO superiores a 2048 GB.

* O tamanho total da VM (replicação + ativação pós-falha de teste) excede o limite de tamanho da conta de armazenamento suportado (35 TB). Geralmente, esta incompatibilidade ocorre quando um disco individual na VM tem uma característica de desempenho que excede os limites máximos suportados pelo Azure ou o Site Recovery relativamente ao armazenamento standard. Uma instância deste género envia a VM para a zona de armazenamento premium. No entanto, o tamanho máximo suportado das contas de armazenamento premium são 35 TB e não é possível proteger VMs protegidas individuais em várias contas de armazenamento. Tenha também em atenção que, quando é executada uma ativação pós-falha de teste numa VM protegida, esta é executada na mesma conta de armazenamento na qual a replicação está em curso. Neste caso, configure duas vezes o tamanho do disco para que a replicação progrida e a ativação pós-falha de teste seja concluída em paralelo.

* O IOPS de origem excede o limite de IOPS de armazenamento suportado de 7500 por disco.

* O IOPS de origem excede o limite de IOPS suportado pelo armazenamento de 80 000 por VM.

* A média de alterações a dados excede o limite de alterações a dados suportado pelo Site Recovery de 10 MB/s para o tamanho de E/S médio para o disco.

* A média de alterações a dados excede o limite de alterações a dados suportado pelo Site Recovery de 25 MB/s para o tamanho de E/S médio para a VM (soma de todas as alterações aos discos).

* O pico de alterações a dados em todos os discos na VM excede o limite máximo de pico de alterações a dados suportado pelo Site Recovery de 54 MB/s por VM.

* A média de IOPS de escrita efetiva excede o limite de IOPS suportado pelo Site Recovery de 840 por disco.

* O armazenamento de instantâneos calculado excede o limite de armazenamento de instantâneos suportado de 10 TB.

* O total de alterações a dados por dia ultrapassa o limite de alterações suportadas por dia de 2 GB por um Servidor de Processos.


**Picos de R/W IOPS (com fator de crescimento)**: A pico carga de trabalho IOPS no disco (a predefinição é o percentil 95), incluindo o fator de crescimento futuro (a predefinição é 30 por cento). Tenha em conta que o total de IOPS de leitura/escrita da VM nem sempre é a soma de IOPS de leitura/escrita dos discos individuais da mesma, porque o pico destas operações da VM é o pico da soma de IOPS de leitura/escrita dos discos individuais da VM durante todos os minutos do período de criação de perfis.

**Alterações a dados de pico em Mbps (com fator de crescimento)**: O pico das alterações a dados no disco (a predefinição 95 º percentil) incluindo o fator de crescimento futuro (predefinição 30 por cento). Tenha em conta que o total de alterações a dados da VM nem sempre é a soma das alterações a dados dos discos individuais da mesma, porque o pico de alterações a dados da VM é o pico da soma das alterações a dados dos respetivos discos individuais durante todos os minutos do período de criação de perfis.

**Número de discos**: O número total de VMDKs na VM.

**Tamanho (GB) de disco**: O tamanho total de configuração de todos os discos da VM. A ferramenta também mostra o tamanho dos discos individuais na VM.

**Núcleos**: O número de núcleos de CPU na VM.

**Memória (MB)**: A quantidade de RAM na VM.

**NICs**: O número de NICs na VM.

**Tipo de arranque**: Tipo de arranque da VM. Pode ser BIOS ou EFI.  Atualmente, o Azure Site Recovery suporta VMs de EFI do Windows Server (Windows Server 2012, 2012 R2 e 2016), desde que o número de partições no disco de arranque seja inferior a 4 e que o tamanho do setor de arranque seja 512 bytes. Para proteger as VMs de EFI, a versão do serviço de mobilidade do Azure Site Recovery tem de ser 9.13 ou superior. Apenas a ativação pós-falha é suportada para VMs de EFI. A reativação pós-falha não é suportada.

**Tipo de SO**:  É o tipo de SO da VM. Pode ser Windows ou Linux, ou outros, com base no modelo do VMware vSphere que escolheu ao criar a VM.

## <a name="azure-site-recovery-limits"></a>Limites do Azure Site Recovery
A tabela seguinte fornece os limites do Azure Site Recovery. Estes limites baseiam-se nos nossos testes, mas não abrangem todas as combinações de E/S de aplicações possíveis. Os resultados reais podem variar consoante a combinação de E/S da sua aplicação. Para obter os melhores resultados, mesmo após o planeamento da implementação, recomendamos sempre que faça testes exaustivos às aplicações através da emissão de uma ativação pós-falha de teste, para ter a perspetiva verdadeira quanto ao desempenho da aplicação.

**Destino do armazenamento da replicação** | **Tamanho médio de E/S do disco de origem** |**Média de alterações a dados do disco de origem** | **Total de alterações a dados do disco de origem por dia**
---|---|---|---
Armazenamento Standard | 8 KB | 2 MB/s | 168 GB por disco
Disco Premium P10 ou P15 | 8 KB  | 2 MB/s | 168 GB por disco
Disco Premium P10 ou P15 | 16 KB | 4 MB/s |  336 GB por disco
Disco Premium P10 ou P15 | 32 KB ou superior | 8 MB/s | 672 GB por disco
Disco Premium P20 ou P30 ou P40 ou P50 | 8 KB    | 5 MB/s | 421 GB por disco
Disco Premium P20 ou P30 ou P40 ou P50 | 16 KB ou superior | 20 MB/s | 1684 GB por disco

**Alterações a dados de origem** | **Limite Máximo**
---|---
Média de alterações a dados por VM| 25 MB/s
Alterações a dados de pico em todos os discos numa VM | 54 MB/s
Máximo de alterações a dados por dia suportadas por um Servidor de Processos | 2 TB

Estes são números médios, que pressupõem uma sobreposição de 30 por cento de E/S. O Site Recovery é capaz de processar um débito superior com base no rácio de sobreposição, em tamanhos de escrita maiores e no comportamento real de E/S da carga de trabalho. Os números anteriores pressupõem um atraso típico de aproximadamente cinco minutos. Ou seja, depois de os dados serem carregados, são processados e é criado um ponto de recuperação ao fim de cinco minutos.


## <a name="cost-estimation"></a>Estimativa de custos
Saiba mais sobre a [estimativa de custos](site-recovery-vmware-deployment-planner-cost-estimation.md).


## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre a [estimativa de custos](site-recovery-vmware-deployment-planner-cost-estimation.md).
