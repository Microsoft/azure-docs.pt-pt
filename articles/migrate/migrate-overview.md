---
title: Acerca do Azure Migrate | Microsoft Docs
description: Disponibiliza uma descrição geral do serviço do Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 01/11/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: e2cafe0c8c5d8194519428839ec3c73f5dc726c0
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56992038"
---
# <a name="about-azure-migrate"></a>Acerca do Azure Migrate

O serviço do Azure Migrate avalia as cargas de trabalho no local para migração para o Azure. O serviço avalia a adequabilidade da migração de máquinas no local, faz o dimensionamento com base no desempenho e oferece estimativas de custos para executar máquinas no local no Azure. Se estiver a considerar migrações lift-and-shift ou estiver nas primeiras etapas da migração, este serviço é para si. Após a avaliação, pode utilizar serviços como o [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) e o [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview) para migrar as máquinas para o Azure.

## <a name="why-use-azure-migrate"></a>Porquê utilizar o Azure Migrate?

O Azure Migrate ajuda-o a:

- **Avaliar a preparação para o Azure**: Avalie se a suas máquinas no local são adequadas para execução no Azure.
- **Obter recomendações de tamanho**: Obter recomendações de tamanho das VMs do Azure com base no histórico de desempenho das VMs no local.
- **Estimar custos mensais**: Obtenha custos estimados para executar máquinas no local no Azure.  
- **Migrar com confiança elevada**: Visualize as dependências de máquinas no local para criar grupos de máquinas que vai avaliar e migrar em conjunto.

## <a name="current-limitations"></a>Limitações atuais

- Pode avaliar máquinas virtuais (VMs) do VMware no local apenas para migração para VMs do Azure. As VMs do VMware tem de ser geridas pelo vCenter Server (versão 5.5, 6.0, 6.5 ou 6.7).
- Se quiser avaliar Hyper-VMs e servidores físicos, utilize o [Planeador de Implementações do Azure Site Recovery](https://aka.ms/asr-dp-hyperv-doc) para Hyper-V e as nossas [ferramentas para parceiros](https://azure.microsoft.com/migration/partners/) para máquinas físicas.
- Pode detetar até 1500 VMs numa única deteção e até 1500 VMs num único projeto. Além disso, pode avaliar até 1500 VMs numa única avaliação.
- Se pretender encontrar um ambiente maior, pode dividir a deteção e criar vários projetos. [Saiba mais](how-to-scale-assessment.md). O Azure Migrate suporta até 20 projetos por subscrição.
- O Azure Migrate só suporta discos geridos para avaliação de migrações.
-  Só pode criar um projeto do Azure Migrate em áreas geográficas indicadas a seguir. No entanto, isso não restringe a capacidade de criar avaliações para outras localizações do Azure de destino.
    **Geografia** | **Localização de armazenamento**
    --- | ---
    Azure Government | Gov (US) - Virginia
    Ásia | Sudeste Asiático
    Europa | Europa do Norte ou da Europa Ocidental
    Estados Unidos | E.U.A. Leste ou oeste dos E.U.A.

    A geografia associada ao projeto de migração é utilizada para armazenar os metadados detetados a partir do ambiente no local. Metadados são armazenados em uma das regiões com base na geografia especificada para o projeto de migração. Se utilizar a visualização de dependências através da criação de uma nova área de trabalho do Log Analytics, a área de trabalho é criada na mesma região que o projeto.
- A funcionalidade de visualização de dependência não está disponível no Azure Government.


## <a name="what-do-i-need-to-pay-for"></a>O que vou pagar?

[Saiba mais](https://azure.microsoft.com/pricing/details/azure-migrate/) sobre os preços do Azure Migrate.


## <a name="whats-in-an-assessment"></a>Novidades nas avaliações

As definições da avaliação podem ser personalizadas de acordo com as suas necessidades. As propriedades da avaliação estão resumidas na tabela abaixo.

**Propriedade** | **Detalhes**
--- | ---
**Localização de destino** | A localização do Azure para a qual pretende migrar.<br/><br/>O Azure Migrate suporta atualmente 33 regiões como localizações dos destinos de migração. [Veja as regiões](https://azure.microsoft.com/global-infrastructure/services/). Por predefinição, a região de destino está definida para E.U.A. Leste.
**Tipo de armazenamento** | O tipo de discos geridos que pretende alocar para todas as VMs que fazem parte da avaliação. Se o critério de dimensionamento for *no local dimensionamento* pode especificar o tipo de disco de destino como os discos premium (predefinição), os discos standard SSD ou discos HDD standard. Para *dimensionamento com base no desempenho*, juntamente com as opções acima, também tem a opção de selecionar automático que irá garantir que o disco a recomendação do tamanho é automaticamente feito com base nos dados de desempenho das VMs. Por exemplo, se quer atingir um [única instância de VM SLA de 99,9%](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/), talvez queira especificar o tipo de armazenamento como discos geridos Premium, que irão garantir que todos os discos na avaliação irão ser recomendados como discos geridos Premium. Note que o Azure Migrate só suporta discos geridos para avaliação de migrações.
**Instâncias Reservadas** |  Se tem [instâncias reservadas](https://azure.microsoft.com/pricing/reserved-vm-instances/) no Azure. O Azure Migrate calcula o custo em conformidade.
**Critérios de dimensionamento** | Dimensionamento pode basear-se no **histórico de desempenho** das VMs no local (predefinição) ou **como no local**, sem considerar o histórico de desempenho.
**Histórico de desempenho** | Por predefinição, o Azure Migrate avalia o desempenho das máquinas no local através do histórico de desempenho do último dia, com um valor de percentil de 95%.
**Fator de conforto** | O Azure Migrate considera uma memória intermédia (fator de conforto) durante a avaliação. Esta memória intermédia é aplicada em cima dos dados de utilização das VMs (CPU, memória, disco e rede). O fator de conforto dá conta de problemas como utilização sazonal, histórico de desempenho breve e prováveis aumentos na utilização futura.<br/><br/> Por exemplo, uma VM com 10 núcleos e 20% de utilização resulta, normalmente, numa VM de 2 núcleos. No entanto, com um fator de conforto de 2,0 x, o resultado é uma VM de 4 núcleos. A definição de conforto predefinida é 1,3x.
**Série das VMs** | A série das VMs utilizada para os cálculos de tamanho. Por exemplo, se tiver um ambiente de produção que não pretende migrar para VMs da série A no Azure, poderá excluir a série A da lista ou da série. O dimensionamento tem por base apenas as séries selecionadas.   
**Moeda** | A moeda de faturação. Predefinição é não utilizados no compromisso E.U.A.
**Desconto (%)** | Eventuais descontos para uma subscrição específica que receba sobre a oferta do Azure. A predefinição é 0%.
**Tempo de atividade de VM** | Se não se pretender que as VMs em execução 24x7 no Azure, pode especificar a duração (número de dias por mês) e o número de horas por dia para que eles estariam em execução e as estimativas de custos serão efetuadas em conformidade. O valor predefinido é 31 dias por mês e 24 horas por dia.
**Oferta do Azure** | A [oferta do Azure](https://azure.microsoft.com/support/legal/offer-details/) em que está escrito. O Azure Migrate calcula o custo em conformidade.
**Benefício Híbrido do Azure** | Se tiver o software assurance e for elegível para o [Benefício Híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/) com custos reduzidos.

## <a name="how-does-azure-migrate-work"></a>Como funciona o Azure Migrate?

1.  Cria um projeto do Azure Migrate.
2.  O Azure Migrate utiliza uma VM no local, denominada aplicação recoletora, para detetar informações sobre as suas máquinas no local. Para criar a aplicação, transfira um ficheiro de configuração no formato Open Virtualization Appliance (.ova) e importe-o como VM no seu vCenter Server no local.
3. Ligue à VM a partir do VCenter Server e especifique uma palavra-passe nova para a mesma durante a ligação.
4. Execute o recoletor na VM para iniciar a deteção.
5. O recoletor utiliza os cmdlets do VMware PowerCLI para recolher os metadados da VM. A deteção é feita sem agentes e não instala nada nos anfitriões do VMware nem nas VMs. Os metadados recolhidos incluem informações da VM (núcleos, memória, discos, tamanhos de discos e adaptadores de rede). Também recolhe dados de desempenho das VMs, incluindo utilização da CPU e de memória, IOPS de disco, débito de disco (MBps) e saída de rede (MBps).
5.  Os metadados são enviados para o projeto do Azure Migrate. Pode vê-los no portal do Azure.
6.  Para efeitos da avaliação, agrupa as VMs detetadas em grupos. Por exemplo, pode agrupar VMs que executem a mesma aplicação. Para um agrupamento mais preciso, pode utilizar a visualização de dependências para ver as dependências de uma máquina específica ou para todas as máquinas de um grupo e refinar o grupo.
7.  Depois de um grupo ser definido, crie uma avaliação para o mesmo.
8.  Depois de concluída a avaliação, pode vê-la no portal ou transferi-la no formato Excel.

  ![Arquitetura do Azure Migrate](./media/migration-planner-overview/overview-1.png)

## <a name="what-are-the-port-requirements"></a>Quais são os requisitos de porta?

A tabela resume as portas necessárias para as comunicações do Azure Migrate.

Componente | Comunica com |  Detalhes
--- | --- |---
Recoletor  | Serviço do Azure Migrate | O recoletor liga-se ao serviço através da porta SSL 443.
Recoletor | vCenter Server | Por predefinição, o recoletor liga-se ao vCenter Server na porta 443. Se o servidor escutar noutra porta, configure-a como porta de saída na VM do recoletor.
VM no local | Área de trabalho do Log Analytics | [TCP 443] | [O Microsoft Monitoring Agent (MMA)](../log-analytics/log-analytics-windows-agent.md) utiliza a porta TCP 443 para ligar ao Azure Monitor registos. Só precisa desta porta se estiver a utilizar a visualização de dependências, a qual precisa do agente MMA.


## <a name="what-happens-after-assessment"></a>O que acontece após a avaliação?

Depois de avaliar as máquinas no local, pode utilizar algumas ferramentas para realizar a migração:

- **Azure Site Recovery**: Pode utilizar o Azure Site Recovery para migrar para o Azure. Para tal, tem de [preparar os componentes do Azure](../site-recovery/tutorial-prepare-azure.md) de que precisa, incluindo uma conta de armazenamento e uma rede virtual. No local, [prepare o ambiente do VMware](../site-recovery/vmware-azure-tutorial-prepare-on-premises.md). Quando estiver tudo preparado, configure e ative a replicação para o Azure e migre as VMs. [Saiba mais](../site-recovery/vmware-azure-tutorial.md).
- **Migração de base de dados do Azure**: Se as máquinas no local estiver a executar uma base de dados, como o SQL Server, MySQL ou Oracle, pode utilizar o [Azure Database Migration Service](../dms/dms-overview.md) para migrá-los para o Azure.


## <a name="next-steps"></a>Passos Seguintes

- [Siga o tutorial](tutorial-assessment-vmware.md) para criar uma avaliação para uma VM do VMware no local.
- [Veja as perguntas mais frequentes](resources-faq.md) sobre o Azure Migrate.
