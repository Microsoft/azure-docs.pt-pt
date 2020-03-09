---
title: Analise o relatório do Planificador de Implantação para recuperação de desastres da VMware com a Recuperação do Site Azure
description: Este artigo descreve como analisar o relatório gerado pelo Planejador de Implantação de Recuperação para recuperação de desastres vMware para Azure, usando a Recuperação do Site Azure.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/4/2019
ms.author: mayg
ms.openlocfilehash: 4dad11e8331064a9df1b1aed561e00b9a9b24017
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78362989"
---
# <a name="analyze-the-deployment-planner-report-for-vmware-disaster-recovery-to-azure"></a>Analise o relatório do Planificador de Implantação para a recuperação de desastres da VMware para o Azure

O relatório gerado do Microsoft Excel contém as seguintes folhas:
## <a name="on-premises-summary"></a>Resumo no local
A folha de cálculo sumária on-local fornece uma visão geral do ambiente vMware perfilado.

![Resumo no local do ambiente VMware](media/site-recovery-vmware-deployment-planner-analyze-report/on-premises-summary-v2a.png)

**Data de início** e **data de fim**: As datas de início e fim dos dados de perfis considerados para a geração de relatórios. Por padrão, a data de início é a data em que o perfil começa, e a data de fim é a data em que o perfil para. Estes podem ser os valores 'StartDate' e 'EndDate' se o relatório for gerado com estes parâmetros.

**Número total de dias de perfis**: O número total de dias de perfis entre as datas de início e de fim para os quais o relatório é gerado.

**Número de máquinas virtuais compatíveis**: O número total de VMs compatíveis para os quais são calculadas as larguras de banda necessárias da rede, o número necessário de contas de armazenamento, os núcleos do Microsoft Azure, servidores de configuração e servidores de processos adicionais.

**Número total de discos em todas as máquinas virtuais compatíveis**: O número que é usado como uma das inputs para decidir o número de servidores de configuração e servidores de processo seleções adicionais a utilizar na implementação.

**Número médio de discos por máquina virtual compatível**: O número médio de discos calculados em todos os VMs compatíveis.

**Tamanho médio do disco (GB)** : O tamanho médio do disco calculado em todos os VMs compatíveis.

**RPO desejado (minutos)** : Quer o objetivo do ponto de recuperação por defeito quer o valor passado para o parâmetro 'DesiredRPO' no momento da geração do relatório para estimar a largura de banda necessária.

**Largura de banda desejada (Mbps)** : O valor que passou para o parâmetro 'Largura de Banda' no momento da geração do relatório para estimar rpo alcançável.

**Observados dados típicos churn por dia (GB)** : A média de dados observada em todos os dias de perfis. Este número é usado como uma das inputs para decidir o número de servidores de configuração e servidores de processo seleções adicionais a serem utilizados na implementação.

## <a name="recommendations"></a>recomendações

A folha de recomendações do relatório VMware para o Relatório Azure tem os seguintes detalhes de acordo com o RPO pretendido selecionado:

![Recomendações para vMware para relatório Azure](media/site-recovery-vmware-deployment-planner-analyze-report/Recommendations-v2a.png)

### <a name="profiled-data"></a>Dados perfilados
![A visão de dados perfilados no planejador de implementação](media/site-recovery-vmware-deployment-planner-analyze-report/profiled-data-v2a.png)

Período de **dados perfilado**: Período durante o qual o perfil foi executado. Por predefinição, a ferramenta inclui todos os dados perfilados no cálculo, a menos que gere o relatório por um período específico, utilizando opções de StartDate e EndDate durante a geração do relatório.

**Nome**do servidor : O nome ou endereço IP do vCenter VMware ou do hospedeiro ESXi cujo relatório de VMs é gerado.

**RPO desejado**: O objetivo do ponto de recuperação para a sua implantação. Por predefinição, a largura de banda de rede necessária é calculada para valores de RPO de 15, 30 e 60 minutos. Com base na seleção, os valores afetados são atualizados na folha. Se utilizou o parâmetro *DesiredRPOinMin* enquanto gera o relatório, esse valor é mostrado no resultado do RPO desejado.

### <a name="profiling-overview"></a>Visão geral do perfil

![Perfis resultam no planejador de implantação](media/site-recovery-vmware-deployment-planner-analyze-report/profiling-overview-v2a.png)

**Máquinas virtuais perfiladas totais**: O número total de VMs cujos dados perfilados estão disponíveis. Se o VMListFile tiver nomes de quaisquer VMs que não tenham sido perfilados, esses VMs não são considerados na geração do relatório e estão excluídos da contagem total de VMs perfilados.

**Máquinas virtuais compatíveis**: O número de VMs que podem ser protegidos para o Azure utilizando a Recuperação do Local. É o número total de VMs compatíveis para os quais são calculadas a largura de banda de rede necessária, o número de contas de armazenamento, o número de núcleos Azure e o número de servidores de configuração e servidores de processos adicionais. Os detalhes de cada VM compatível estão disponíveis na secção "VMs compatíveis".

**Máquinas virtuais incompatíveis**: O número de VMs perfilados que são incompatíveis para proteção com a Recuperação do Local. As razões da incompatibilidade são observadas na secção "VMs incompatíveis". Se o VMListFile tiver nomes de quaisquer VMs que não tenham sido perfilados, esses VMs são excluídos da contagem incompatível de VMs. Estes VMs estão listados como "Dados não encontrados" no final da secção "VMs incompatíveis".

**RPO desejado**: O seu objetivo de ponto de recuperação desejado, em minutos. O relatório é gerado por três valores de RPO: 15 (padrão), 30 e 60 minutos. A recomendação de largura de banda no relatório é alterada com base na sua seleção na lista de abandono de RPO desejado na parte superior direita da folha. Se gerou o relatório utilizando o parâmetro *-DesiredRPO* com um valor personalizado, este valor personalizado mostrará como o padrão na lista de abandono de RPO desejado.

### <a name="required-network-bandwidth-mbps"></a>Largura de banda de rede necessária (Mbps)

![Largura de banda de rede necessária no planejador de implantação](media/site-recovery-vmware-deployment-planner-analyze-report/required-network-bandwidth-v2a.png)

**Para conhecer o RPO 100% das vezes:** A largura de banda recomendada em Mbps a atribuir para atender ao seu RPO desejado 100% do tempo. Esta largura de banda deve ser dedicada para a replicação delta de todos os seus VMs compatíveis para evitar qualquer violação de RPO.

**Para satisfazer o RPO 90% do tempo**: Devido aos preços de banda larga ou por qualquer outra razão, se não conseguir definir a largura de banda necessária para atender ao rpo desejado 100% das vezes, pode optar por optar por uma definição de largura de banda mais baixa que pode atender ao seu RPO desejado 90% das vezes. Para compreender as implicações de definir esta largura de banda mais baixa, o relatório fornece uma análise do número e duração das violações de RPO que se espera.

**Throughput alcançado:** A entrada do servidor em que executou o comando GetThroughput para a região do Microsoft Azure onde está localizada a conta de armazenamento. Este número de entrada indica o nível estimado que pode atingir quando protege os VMs compatíveis utilizando a Recuperação do Site, desde que o seu servidor de configuração ou o armazenamento do servidor de processos e as características da rede permaneçam os mesmos que os do servidor a partir da qual executou a ferramenta.

Para a replicação, deve definir a largura de banda recomendada para atender o RPO a 100% do tempo. Depois de definir a largura de banda, se não vir qualquer aumento na entrada alcançada, conforme relatado pela ferramenta, faça o seguinte:

1. Verifique se existe alguma rede Qualidade de Serviço (QoS) que esteja a limitar a entrada de Recuperação do Site.

2. Verifique se o seu cofre de recuperação do site está na região do Microsoft Azure fisicamente mais próxima para minimizar a latência da rede.

3. Verifique as suas características de armazenamento locais para determinar se pode melhorar o hardware (por exemplo, HDD para SSD).

4. Altere as definições de Recuperação do Site no servidor de processo para [aumentar a largura de banda da rede de quantidade utilizada para a replicação](./site-recovery-plan-capacity-vmware.md#control-network-bandwidth).

Se estiver a executar a ferramenta num servidor de configuração ou num servidor de processos que já tenha VMs protegidos, execute a ferramenta algumas vezes. O número de entrada alcançado muda dependendo da quantidade de churn que está a ser processada nesse momento.

Para todas as implementações de Recuperação do Site da empresa, recomendamos que utilize o [ExpressRoute](https://aka.ms/expressroute).

### <a name="required-storage-accounts"></a>Contas de armazenamento necessárias
O gráfico seguinte mostra o número total de contas de armazenamento (standard e premium) que são necessárias para proteger todos os VMs compatíveis. Para saber qual a conta de armazenamento a utilizar para cada VM, consulte a secção "Colocação de armazenamento vm". Se estiver a utilizar o v2.5 do Deployment Planner, esta recomendação apenas mostra o número de contas padrão de armazenamento de cache que são necessárias para a replicação uma vez que os dados estão a ser diretamente escritos para Discos Geridos.

![Contas de armazenamento necessárias no planejador de implantação](media/site-recovery-vmware-deployment-planner-analyze-report/required-storage-accounts-v2a.png)

### <a name="required-number-of-azure-cores"></a>Número exigido de núcleos Azure
Este resultado é o número total de núcleos a criar antes da falha ou do teste de todos os VMs compatíveis. Se estiverem disponíveis poucos núcleos na subscrição, a Recuperação do Site não cria VMs no momento da falha de teste ou da falha.

![Número exigido de núcleos Azure no planejador de implantação](media/site-recovery-vmware-deployment-planner-analyze-report/required-cores-v2a.png)

### <a name="required-on-premises-infrastructure"></a>Infraestrutura necessária no local
Esta figura é o número total de servidores de configuração e servidores de processos adicionais a configurar que seriam suficientes para proteger todos os VMs compatíveis. Dependendo das recomendações de tamanho suportado para o servidor de [configuração,](https://aka.ms/asr-v2a-on-prem-components)a ferramenta pode recomendar servidores adicionais. A recomendação baseia-se no maior do churn por dia ou no número máximo de VMs protegidos (assumindo uma média de três discos por VM), qualquer que seja o primeiro atingido no servidor de configuração ou no servidor de processo adicional. Você encontrará os detalhes do total de churn por dia e o número total de discos protegidos na secção "On-premises sumário".

![Infraestrutura no local necessária no planejador de implantação](media/site-recovery-vmware-deployment-planner-analyze-report/required-on-premises-components-v2a.png)

### <a name="what-if-analysis"></a>E se a análise
Esta análise descreve quantas violações podem ocorrer durante o período de perfis quando você define uma largura de banda mais baixa para o RPO desejado ser cumprido apenas 90 por cento do tempo. Uma ou mais violações de RPO podem ocorrer em qualquer dia. O gráfico mostra o pico de RPO do dia.
Com base nesta análise, você pode decidir se o número de violações de RPO em todos os dias e pico de rpo hit por dia é aceitável com a largura de banda mais baixa especificada. Se for aceitável, pode alocar a largura de banda mais baixa para replicação, então alocar a largura de banda mais alta, como sugerido para satisfazer o RPO desejado 100% do tempo.

![E se a análise no planejador de implantação](media/site-recovery-vmware-deployment-planner-analyze-report/what-if-analysis-v2a.png)

### <a name="recommended-vm-batch-size-for-initial-replication"></a>Tamanho recomendado do lote VM para replicação inicial
Nesta secção, recomendamos o número de VMs que podem ser protegidos paralelamente para completar a replicação inicial dentro de 72 horas com a largura de banda sugerida para satisfazer o RPO desejado 100% do tempo definido. Este valor é de valor configurável. Para o alterar no tempo de geração de relatórios, utilize o parâmetro *GoalToCompleteIR.*

O gráfico aqui mostra uma gama de valores de largura de banda e uma contagem calculada de tamanho de lote VM para completar a replicação inicial em 72 horas, com base no tamanho médio de VM detetado em todos os VMs compatíveis.

Na pré-visualização pública, o relatório não especifica quais os VMs que devem ser incluídos num lote. Pode utilizar o tamanho do disco mostrado na secção "VMs compatíveis" para encontrar o tamanho de cada VM e selecioná-los para um lote, ou pode selecionar os VMs com base em características de carga de trabalho conhecidas. O tempo de conclusão da replicação inicial muda proporcionalmente, com base no tamanho real do disco VM, no espaço utilizado do disco e na entrada de rede disponível.

![Tamanho recomendado do lote VM](media/site-recovery-vmware-deployment-planner-analyze-report/ir-batching-v2a.png)

### <a name="cost-estimation"></a>Estimativa de custos
O gráfico mostra a visão sumária do custo estimado de recuperação total de desastres (DR) para O Azure da sua região-alvo escolhida e da moeda que especificou para a geração de relatórios.

![Resumo da estimativa de custos](media/site-recovery-vmware-deployment-planner-analyze-report/cost-estimation-summary-v2a.png)

O resumo ajuda-o a compreender o custo que precisa de pagar pelo armazenamento, computação, rede e licença quando protege todos os seus VMs compatíveis para o Azure utilizando a Azure Site Recovery. O custo é calculado para VMs compatíveis e não em todos os VMs perfilados.  

Pode ver o custo mensal ou anualmente. Saiba mais sobre [regiões-alvo apoiadas](./site-recovery-vmware-deployment-planner-cost-estimation.md#supported-target-regions) e [moedas apoiadas.](./site-recovery-vmware-deployment-planner-cost-estimation.md#supported-currencies)

**Custo por componentes** O custo total do DR é dividido em quatro componentes: Compute, Storage, Network e Azure Site Recovery custo da licença. O custo é calculado com base no consumo que será incorrido durante a replicação e no tempo de perfuração DR para computação, armazenamento (premium e standard), ExpressRoute/VPN que está configurado entre o site no local e a licença Azure, e azure site recovery.

**Custo por estados** O custo total de recuperação de desastres (DR) é categorias baseadas em dois estados diferentes - Replicação e broca DR.

**Custo de replicação**: O custo que será incorrido durante a replicação. Cobre o custo de armazenamento, rede e licença de recuperação do site Azure.

**Custo da broca DR**: O custo que será incorrido durante as falhas do teste. A recuperação do site Azure gira os VMs durante a falha do teste. O custo da broca DR cobre o cálculo e o custo de armazenamento dos VMs em execução.

**Custo de armazenamento azure por mês/ano** Mostra o custo total de armazenamento que será incorrido para o armazenamento premium e padrão para replicação e broca DR.
Pode visualizar uma análise detalhada dos custos por VM na folha de estimativa de [custos.](site-recovery-vmware-deployment-planner-cost-estimation.md)

### <a name="growth-factor-and-percentile-values-used"></a>Fator de crescimento e valores percentuais utilizados
Esta secção na parte inferior da folha mostra o valor percentil utilizado para todos os contadores de desempenho dos VMs perfilados (o padrão é 95º percentil), e o fator de crescimento (padrão é de 30 por cento) que é usado em todos os cálculos.

![Fator de crescimento e valores percentuais utilizados](media/site-recovery-vmware-deployment-planner-analyze-report/growth-factor-v2a.png)

## <a name="recommendations-with-available-bandwidth-as-input"></a>Recomendações com largura de banda disponível como entrada

![Recomendações com largura de banda disponível como entrada](media/site-recovery-vmware-deployment-planner-analyze-report/profiling-overview-bandwidth-input-v2a.png)

Pode ter uma situação em que sabe que não pode definir uma largura de banda superior a x Mbps para a replicação da Recuperação do Site. A ferramenta permite-lhe inserir largura de banda disponível (utilizando o parâmetro de largura de banda durante a geração de relatórios) e obter o RPO alcançável em minutos. Com este valor rpo realizável, você pode decidir se você precisa configurar uma largura de banda adicional ou você está ok em ter uma solução de recuperação de desastres com este RPO.

![RPO alcançável para 500 Mbps de largura de banda](media/site-recovery-vmware-deployment-planner-analyze-report/achievable-rpo-v2a.png)

## <a name="vm-storage-placement"></a>Colocação vm-armazenamento

>[!Note]
>O Planificador de Implantação v2.5 recomenda a colocação de armazenamento para máquinas que se replicarão diretamente para discos geridos.

![Colocação vm-armazenamento](media/site-recovery-vmware-deployment-planner-analyze-report/vm-storage-placement-v2a.png)

Tipo de **armazenamento de replicação**: Ou um disco gerido padrão ou premium, que é utilizado para replicar todos os VMs correspondentes mencionados na coluna **VMs to Place.**

Tipo de conta de armazenamento de **registos**: Todos os registos de replicação são armazenados numa conta de armazenamento padrão.

**Prefixo sugerido para conta**de armazenamento : O prefixo de três caracteres sugerido que pode ser usado para nomear a conta de armazenamento de cache. Pode usar o seu próprio prefixo, mas a sugestão da ferramenta segue a convenção de nomeação de [divisórias para contas](https://aka.ms/storage-performance-checklist)de armazenamento.

**Nome**da conta de registo sugerido : O nome da conta de armazenamento depois de incluir o prefixo sugerido. Substitua o nome dentro dos suportes angulares (< e >) pela sua entrada personalizada.

Resumo da **colocação**: Um resumo dos discos necessários para proteger os VMs por tipo de armazenamento. Inclui o número total de VMs, o tamanho total aprovisionado em todos os discos e o número total de discos.

**Máquinas Virtuais para Colocar**: Uma lista de todos os VMs que devem ser colocados na conta de armazenamento dada para um desempenho e utilização ótimos.

## <a name="compatible-vms"></a>VMs compatíveis
![Folha de cálculo excel de VMs compatíveis](media/site-recovery-vmware-deployment-planner-analyze-report/compatible-vms-v2a.png)

**Nome VM**: O nome VM ou endereço IP que é usado no VMListFile quando um relatório é gerado. Esta coluna também lista os discos (VMDKs) que estão ligados aos VMs. Para distinguir VMs vCenter com nomes duplicados ou endereços IP, os nomes incluem o nome de anfitrião ESXi. O hospedeiro ESXi listado é aquele em que o VM foi colocado quando a ferramenta foi descoberta durante o período de perfis.

**Compatibilidade VM**: Os valores são **Sim** e **Sim\*.** **Sim**\* é por exemplo em que o VM é adequado para [SSDs premium.](../virtual-machines/windows/disks-types.md) Aqui, o disco de alto-churn ou IOPS encaixa-se na categoria P20 ou P30, mas o tamanho do disco faz com que seja mapeado para um P10 ou P20. A conta de armazenamento decide a que tipo de disco de armazenamento premium para mapear um disco, com base no seu tamanho. Por exemplo:
* <128 GB é um P10.
* 128 GB a 256 GB é um P15
* 256 GB a 512 GB é um P20.
* 512 GB a 1024 GB é um P30.
* 1025 GB a 2048 GB é um P40.
* 2049 GB a 4095 GB é um P50.

Por exemplo, se as características de carga de trabalho de um disco o colocarem na categoria P20 ou P30, mas o tamanho o mapeia para um tipo de disco de armazenamento premium mais baixo, a ferramenta marca que VM como **Yes**\*. A ferramenta também recomenda que altere o tamanho do disco de origem para se encaixar no tipo de disco de armazenamento premium recomendado ou altere o tipo de disco-alvo pós-falha.

**Tipo de armazenamento**: Standard ou premium.

**Asrseeddisk (Disco Gerido) criado para replicação**: O nome do disco que é criado quando ativa a replicação. Armazena os dados e as suas fotos em Azure.

**Pico R/W IOPS (com Fator**de Crescimento) : O pico da carga de trabalho leia/escreva IOPS no disco (o padrão é 95º percentil), incluindo o fator de crescimento futuro (o padrão é de 30 por cento). Note que o iOPS de leitura/escrita total de um VM nem sempre é a soma dos IOPS de leitura/escrita individuais dos discos vM, porque o pico de leitura/escrita IOPS do VM é o pico da soma dos seus discos individuais de leitura/escrita IOPS durante cada minuto do período de perfis.

**Pico de Dados Churn em Mbps (com Fator de Crescimento)** : A taxa de pico de churn no disco (padrão é 95º percentil), incluindo o fator de crescimento futuro (o padrão é de 30 por cento). Note que o volume total de dados do VM nem sempre é a soma dos dados individuais dos discos da VM, porque o pico de dados do VM é o pico da soma dos seus discos individuais durante cada minuto do período de perfis.

**Tamanho Azure VM**: O tamanho ideal da máquina virtual Azure Cloud Services para este VM no local. O mapeamento baseia-se na memória do VM no local, no número de discos/núcleos/NICs e no IOPS de leitura/escrita. A recomendação é sempre o tamanho de VM Azure mais baixo que corresponde a todas as características VM no local.

**Número de Discos**: O número total de discos de máquinas virtuais (VMDKs) no VM.

**Tamanho do disco (GB)** : O tamanho total da configuração de todos os discos do VM. A ferramenta também mostra o tamanho do disco para os discos individuais no VM.

**Núcleos**: O número de núcleos de CPU no VM.

**Memória (MB)** : A RAM no VM.

**NICs**: O número de NICs no VM.

**Tipo de arranque**: Tipo de arranque do VM. Pode ser BIOS ou EFI.  Atualmente, a Recuperação do Site Azure suporta os VMs do Windows Server EFI (Windows Server 2012, 2012 R2 e 2016) desde que o número de divisórias no disco de arranque seja inferior a 4 e o tamanho do setor de boot é de 512 bytes. Para proteger os VMs EFI, a versão do serviço de mobilidade de recuperação de sítios Azure deve ser de 9.13 ou superior. Apenas a failover é suportada para VMs EFI. O failback não é suportado.  

**Tipo OS**: É o tipo OS do VM. Pode ser Windows ou Linux ou outro com base no modelo escolhido da VMware vSphere enquanto cria o VM.  

## <a name="incompatible-vms"></a>VMs incompatíveis

![Folha de cálculo excel de VMs incompatíveis
](media/site-recovery-vmware-deployment-planner-analyze-report/incompatible-vms-v2a.png)

**Nome VM**: O nome VM ou endereço IP que é usado no VMListFile quando um relatório é gerado. Esta coluna também lista os VMDKs que estão ligados aos VMs. Para distinguir VMs vCenter com nomes duplicados ou endereços IP, os nomes incluem o nome de anfitrião ESXi. O hospedeiro ESXi listado é aquele em que o VM foi colocado quando a ferramenta foi descoberta durante o período de perfis.

**Compatibilidade VM**: Indica por que razão o VM dado é incompatível com a Recuperação do Local. As razões são descritas para cada disco incompatível do VM e, com base nos limites de [armazenamento publicados,](https://aka.ms/azure-storage-scalbility-performance)pode ser qualquer um dos seguintes:

* Tamanho do disco de dados errado ou tamanho errado do disco OS. [Reveja](vmware-physical-azure-support-matrix.md#azure-vm-requirements) os limites de suporte. 
* O tamanho total do VM (replicação + TFO) excede o limite de tamanho da conta de armazenamento suportado (35 TB). Esta incompatibilidade ocorre geralmente quando um único disco no VM tem uma característica de desempenho que excede os limites máximos suportados de Azure ou de Recuperação do Local para armazenamento padrão. Tal instância empurra o VM para a zona de armazenamento premium. No entanto, o tamanho máximo suportado de uma conta de armazenamento premium é de 35 TB, e um Único VM protegido não pode ser protegido através de várias contas de armazenamento. Note também que quando um teste failover é executado em um VM protegido, ele corre na mesma conta de armazenamento onde a replicação está progredindo. Neste caso, configurar 2x o tamanho do disco para a replicação progredir e testar o fracasso para ter sucesso paralelo.

* O IOPS de origem excede o limite de IOPS de armazenamento suportado de 7500 por disco.

* O IOPS de origem excede o limite de IOPS de armazenamento suportado de 80.000 por VM.

* O churn médio de dados excede o limite de dados suportados de recuperação do site de 20 MB/s para o tamanho médio de E/S para o disco.

* Os dados de pico de dados que se espalham por todos os discos do VM excedem o limite máximo de dados de pico de recuperação do site suportado de 54 MB/s por VM.

* O IOPS de escrita e eficaz médio excede o limite de IOPS de Recuperação do Site suportado de 840 para o disco.

* O armazenamento de instantâneocalculado excede o limite de armazenamento de instantâneo suportado de 10 TB.

* O total de dados por dia excede o limite suportado por dia de 2 TB por um Servidor de Processo.


**Pico R/W IOPS (com Fator**de Crescimento) : O pico da carga de trabalho IOPS no disco (padrão é 95º percentil), incluindo o fator de crescimento futuro (o padrão é de 30 por cento). Note que o iOPS de leitura/escrita total do VM nem sempre é a soma dos IOPS de leitura/escrita individuais dos discos vM, porque o pico de leitura/escrita IOPS do VM é o pico da soma dos seus discos individuais de leitura/escrita IOPS durante cada minuto do período de perfis.

**Pico de Dados Churn em Mbps (com Fator de Crescimento)** : A taxa de pico de churn no disco (percentil padrão 95) incluindo o fator de crescimento futuro (padrão 30 por cento). Note que o volume total de dados do VM nem sempre é a soma dos dados individuais dos discos da VM, porque o pico de dados do VM é o pico da soma dos seus discos individuais durante cada minuto do período de perfis.

**Número de Discos**: O número total de VMDKs no VM.

**Tamanho do disco (GB)** : O tamanho total da configuração de todos os discos do VM. A ferramenta também mostra o tamanho do disco para os discos individuais no VM.

**Núcleos**: O número de núcleos de CPU no VM.

**Memória (MB)** : A quantidade de RAM no VM.

**NICs**: O número de NICs no VM.

**Tipo de arranque**: Tipo de arranque do VM. Pode ser BIOS ou EFI.  Atualmente, a Recuperação do Site Azure suporta os VMs do Windows Server EFI (Windows Server 2012, 2012 R2 e 2016) desde que o número de divisórias no disco de arranque seja inferior a 4 e o tamanho do setor de boot é de 512 bytes. Para proteger os VMs EFI, a versão do serviço de mobilidade de recuperação de sítios Azure deve ser de 9.13 ou superior. Apenas a failover é suportada para VMs EFI. O failback não é suportado.

**Tipo OS**: É o tipo OS do VM. Pode ser Windows ou Linux ou outro com base no modelo escolhido da VMware vSphere enquanto cria o VM.

## <a name="azure-site-recovery-limits"></a>Limites de recuperação do site Azure
A tabela seguinte fornece os limites de recuperação do site Azure. Estes limites baseiam-se nos nossos testes, mas não podem cobrir todas as combinações possíveis de I/S de aplicação. Os resultados reais podem variar em função da mistura de I/O da sua aplicação. Para obter os melhores resultados, mesmo após o planeamento de implementação, recomendamos sempre que realize testes extensivos de aplicação, emitindo uma falha no teste para obter a verdadeira imagem de desempenho da aplicação.

**Alvo de armazenamento de replicação** | **Tamanho médio do disco de origem I/O** |**Churn de dados de disco de origem média** | **Total de dados de disco de origem por dia**
---|---|---|---
Armazenamento padrão | 8 KB | 2 MB/s | 168 GB por disco
Disco Premium P10 ou P15 | 8 KB  | 2 MB/s | 168 GB por disco
Disco Premium P10 ou P15 | 16 KB | 4 MB/s |  336 GB por disco
Disco Premium P10 ou P15 | 32 KB ou superior | 8 MB/s | 672 GB por disco
Disco Premium P20 ou P30 ou P40 ou P50 | 8 KB    | 5 MB/s | 421 GB por disco
Disco Premium P20 ou P30 ou P40 ou P50 | 16 KB ou superior | 20 MB/s | 1684 GB por disco

**Churn de dados de origem** | **Limite máximo**
---|---
Dados de pico espalham-se por todos os discos de um VM | 54 MB/s
Máximo de dados por dia suportado por um Servidor de Processo | 2 TB

Estes são números médios assumindo uma sobreposição de 30% de I/S. A recuperação do site é capaz de lidar com uma produção mais elevada com base na relação de sobreposição, tamanhos de escrita maiores e comportamento real de I/S. Os números anteriores assumem um atraso típico de aproximadamente cinco minutos. Ou seja, depois de os dados ser carregados, é processado e um ponto de recuperação é criado dentro de cinco minutos.


## <a name="cost-estimation"></a>Estimativa de custos
Saiba mais sobre a estimativa de [custos.](site-recovery-vmware-deployment-planner-cost-estimation.md)


## <a name="next-steps"></a>Próximos passos
Saiba mais sobre a estimativa de [custos.](site-recovery-vmware-deployment-planner-cost-estimation.md)
