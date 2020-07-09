---
title: Perguntas sobre a descoberta, avaliação e análise de dependência em Azure Migrate
description: Obtenha respostas a perguntas comuns sobre a descoberta, avaliação e análise de dependência em Azure Migrate.
ms.topic: conceptual
ms.date: 06/09/2020
ms.openlocfilehash: 7b26d4442f9a84375205e7778ae037b565f53438
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86118839"
---
# <a name="discovery-assessment-and-dependency-analysis---common-questions"></a>Descoberta, avaliação e análise de dependência - Questões comuns

Este artigo responde a questões comuns sobre a descoberta, avaliação e análise de dependência em Azure Migrate. Se tiver outras questões, verifique estes recursos:

- [Perguntas gerais](resources-faq.md) sobre Azure Migrate
- Perguntas sobre o [aparelho Azure Migrate](common-questions-appliance.md)
- Perguntas sobre [migração de servidores](common-questions-server-migration.md)
- Obtenha perguntas respondidas no [fórum Azure Migrate](https://aka.ms/AzureMigrateForum)


## <a name="what-geographies-are-supported-for-discovery-and-assessment-with-azure-migrate"></a>Que geografias são apoiadas para a descoberta e avaliação com Azure Migrate?

Reveja as regiões suportadas em [clouds públicas](migrate-support-matrix.md#supported-geographies-public-cloud) e do [Azure Government](migrate-support-matrix.md#supported-geographies-azure-government).


## <a name="how-many-vms-can-i-discover-with-an-appliance"></a>Quantos VMs posso descobrir com um aparelho?

Pode descobrir até 10.000 VMware VMs, até 5.000 VMs Hiper-V e até 1000 servidores físicos utilizando um único aparelho. Se tiver mais máquinas, leia sobre [o escalonamento de uma avaliação de Hiper-V,](scale-hyper-v-assessment.md) [escalonando uma avaliação de VMware,](scale-vmware-assessment.md)ou [escalando uma avaliação do servidor físico](scale-physical-assessment.md).

## <a name="how-do-i-choose-the-assessment-type"></a>Como escolho o tipo de avaliação?

- Utilize **avaliações de VM do Azure** quando pretender avaliar os seus [VMs VMs, VMs](how-to-set-up-appliance-vmware.md) [hiper-V](how-to-set-up-appliance-hyper-v.md)e [servidores físicos](how-to-set-up-appliance-physical.md) para migração para VMs Azure. [Saiba mais](concepts-assessment-calculation.md)

- Utilize avaliações **da Solução VMware Azure (AVS)** quando pretender avaliar os seus [VMS VMware](how-to-set-up-appliance-vmware.md) no local para migração para [Azure VMware Solution (AVS)](../azure-vmware/introduction.md) utilizando este tipo de avaliação. [Saiba mais](concepts-azure-vmware-solution-assessment-calculation.md)

- Pode utilizar um grupo comum com máquinas VMware apenas para executar ambos os tipos de avaliações. Note que se estiver a fazer avaliações AVS em Azure Migrate pela primeira vez, é aconselhável criar um novo grupo de máquinas VMware.

## <a name="i-cant-see-some-groups-when-i-am-creating-an-azure-vmware-solution-avs-assessment"></a>Não consigo ver alguns grupos quando estou a criar uma avaliação da Solução VMware (AVS) do Azure VMware

- A avaliação do AVS pode ser feita em grupos que têm apenas máquinas VMware. Por favor, remova qualquer máquina não-VMware do grupo se pretender realizar uma avaliação AVS.
- Se estiver a realizar avaliações AVS em Azure Migrate pela primeira vez, é aconselhável criar um novo grupo de máquinas VMware.

## <a name="how-do-i-select-ftt-raid-level-in-avs-assessment"></a>Como selecionar o nível FTT-RAID na avaliação AVS?

O motor de armazenamento utilizado em AVS é vSAN. as polícias de armazenamento vSAN definem os requisitos de armazenamento para as suas máquinas virtuais. Estas políticas garantem o nível de serviço necessário para os seus VMs porque determinam como o armazenamento é atribuído ao VM. Estas são as combinações FTT-Raid disponíveis: 

**Falhas na toleração (FTT)** | **Configuração RAID** | **Anfitriões mínimos necessários** | **Consideração de dimensionamento**
--- | --- | --- | --- 
1 | RAID-1 (Espelhamento) | 3 | Um VM de 100GB consumiria 200GB.
1 | RAID-5 (Codificação de Apagação) | 4 | Um VM de 100GB consumiria 133.33GB
2 | RAID-1 (Espelhamento) | 5 | Um VM de 100GB consumiria 300GB.
2 | RAID-6 (Codificação de Apagação) | 6 | Um VM de 100GB consumiria 150GB.
3 | RAID-1 (Espelhamento) | 7 | Um VM de 100GB consumiria 400GB.

## <a name="i-cant-see-some-vm-types-in-azure-government"></a>Não consigo ver alguns tipos de VM no Governo de Azure.

Os tipos de VM apoiados para avaliação e migração dependem da disponibilidade na localização do Governo de Azure. Pode [rever e comparar](https://azure.microsoft.com/global-infrastructure/services/?regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia&products=virtual-machines) tipos de VM no Governo Azure.

## <a name="the-size-of-my-vm-changed-can-i-run-an-assessment-again"></a>O tamanho do meu VM mudou. Posso fazer uma avaliação de novo?

O aparelho Azure Migrate recolhe continuamente informações sobre o ambiente no local.  Uma avaliação é um instantâneo pontual dos VMs no local. Se alterar as definições de um VM que pretende avaliar, utilize a opção de recalculação para atualizar a avaliação com as alterações mais recentes.

## <a name="how-do-i-discover-vms-in-a-multitenant-environment"></a>Como descubro VMs num ambiente multitenant?

- **VMware**: Se um ambiente é partilhado entre inquilinos e você não quer descobrir os VMs de um inquilino na subscrição de outro inquilino, crie credenciais VMware vCenter Server que possam aceder apenas aos VMs que você deseja descobrir. Em seguida, use estas credenciais quando começar a ser descoberto no aparelho Azure Migrate.
- **Hiper-V:** A Descoberta utiliza credenciais de anfitrião Hyper-V. Se os VMs partilham o mesmo hospedeiro Hiper-V, não há forma de separar a descoberta.  

## <a name="do-i-need-vcenter-server"></a>Preciso do servidor vCenter?

Sim, a Azure Migrate requer o vCenter Server num ambiente VMware para realizar a descoberta. A Azure Migrate não suporta a descoberta de anfitriões ESXi que não são geridos pelo vCenter Server.

## <a name="what-are-the-sizing-options-in-an-azure-vm-assessment"></a>Quais são as opções de dimensionamento numa avaliação de VM Azure?

Com o tamanho de as-on-ins, Azure Migrate não considera os dados de desempenho de VM para avaliação. Azure Migrate avalia os tamanhos de VM com base na configuração no local. Com o dimensionamento baseado no desempenho, o dimensionamento baseia-se em dados de utilização.

Por exemplo, se um VM no local tiver quatro núcleos e 8 GB de memória a 50% de utilização do CPU e 50% de utilização da memória:
- O tamanho do as-on-ins recomendará um Azure VM SKU que tem quatro núcleos e 8 GB de memória.
- O tamanho baseado no desempenho recomendará um SKU VM que tenha dois núcleos e 4 GB de memória porque a percentagem de utilização é considerada.

Da mesma forma, o tamanho do disco depende de critérios de dimensionamento e tipo de armazenamento:
- Se os critérios de dimensionamento forem baseados no desempenho e o tipo de armazenamento for automático, a Azure Migrate tem em conta os valores de IOPS e de produção do disco quando identifica o tipo de disco-alvo (Standard ou Premium).
- Se os critérios de dimensionamento forem baseados no desempenho e o tipo de armazenamento for Premium, a Azure Migrate recomenda um SKU de disco Premium baseado no tamanho do disco no local. A mesma lógica aplica-se ao tamanho do disco quando o dimensionamento é as-no-nas-instalações e o tipo de armazenamento é Standard ou Premium.

## <a name="does-performance-history-and-utilization-affect-sizing-in-an-azure-vm-assessment"></a>O histórico de desempenho e a utilização afetam o dimensionamento numa avaliação de VM do Azure?

Sim, o histórico de desempenho e a utilização afetam o dimensionamento numa avaliação de VM Azure.

### <a name="performance-history"></a>Histórico de desempenho

Apenas para o tamanho baseado no desempenho, a Azure Migrate recolhe o histórico de desempenho das máquinas no local e, em seguida, usa-a para recomendar o tamanho VM e o tipo de disco em Azure:

1. O aparelho traça o perfil contínuo do ambiente no local para recolher dados de utilização em tempo real a cada 20 segundos.
2. O aparelho enrola as amostras recolhidas de 20 segundos e utiliza-as para criar um único ponto de dados a cada 15 minutos.
3. Para criar o ponto de dados, o aparelho seleciona o valor máximo de todas as amostras de 20 segundos.
4. O aparelho envia o ponto de dados para Azure.

### <a name="utilization"></a>Utilização

Quando cria uma avaliação em Azure, dependendo da duração do desempenho e do valor percentil do desempenho que está definido, a Azure Migrate calcula o valor de utilização eficaz e, em seguida, usa-a para dimensionamento.

Por exemplo, se definir a duração do desempenho para um dia e o valor percentil para 95º percentil, Azure Migrate classifica os pontos de amostra de 15 minutos enviados pelo coletor para o último dia em ordem crescente. Escolhe o valor percentil 95 como utilização eficaz.

A utilização do valor percentil 95 garante que os forasteiros são ignorados. Os outliers podem ser incluídos se o seu Azure Migrate usar o percentil 99. Para escolher o pico de utilização para o período sem perder nenhum outliers, coloque Azure Migrate para usar o percentil 99.


## <a name="how-are-import-based-assessments-different-from-assessments-with-discovery-source-as-appliance"></a>Como as avaliações baseadas nas importações são diferentes das avaliações com fonte de descoberta como aparelho?

As avaliações de VM Azure baseadas em importação são avaliações criadas com máquinas que são importadas para a Azure Migrate utilizando um ficheiro CSV. Apenas quatro campos são obrigatórios para importar: nome do servidor, núcleos, memória e sistema operativo. Aqui estão algumas coisas a notar: 
 - Os critérios de prontidão são menos rigorosos nas avaliações baseadas nas importações no parâmetro do tipo de arranque. Se o tipo de arranque não for fornecido, presume-se que a máquina tem o tipo de arranque BIOS e a máquina não está marcada como **Conditionally Ready**. Em avaliações com fonte de descoberta como aparelho, a prontidão é marcada como **Conditionally Ready** se o tipo de arranque estiver em falta. Esta diferença no cálculo da prontidão deve-se ao facto de os utilizadores não terem toda a informação sobre as máquinas nas fases iniciais do planeamento migratório quando as avaliações baseadas nas importações forem efetuadas. 
 - As avaliações de importação baseadas no desempenho utilizam o valor de utilização fornecido pelo utilizador para cálculos de tamanho correto. Uma vez que o valor de utilização é fornecido pelo utilizador, o histórico de **desempenho** e as opções **de utilização por percentil** são desativadas nas propriedades de avaliação. Em avaliações com fonte de descoberta como aparelho, o valor percentil escolhido é colhido a partir dos dados de desempenho recolhidos pelo aparelho.

## <a name="why-is-the-suggested-migration-tool-in-import-based-avs-assessment-marked-as-unknown"></a>Porque é que o instrumento de migração sugerido na avaliação do AVS baseado nas importações é marcado como desconhecido?

Para as máquinas importadas através de um ficheiro CSV, a ferramenta de migração padrão numa avaliação AVS é desconhecida. No entanto, para máquinas VMware, é aconselhável utilizar a solução VMWare Hybrid Cloud Extension (HCX). [Saiba mais.](../azure-vmware/hybrid-cloud-extension-installation.md)


## <a name="what-is-dependency-visualization"></a>O que é visualização de dependência?

A visualização da dependência pode ajudá-lo a avaliar grupos de VMs para migrar com maior confiança. A visualização da dependência cruza as dependências das máquinas antes de fazer uma avaliação. Ajuda a garantir que nada é deixado para trás, e ajuda a evitar interrupções inesperadas quando migra para Azure. A Azure Migrate utiliza a solução de Mapa de Serviço no Monitor Azure para permitir a visualização da dependência. [Saiba mais](concepts-dependency-visualization.md).

> [!NOTE]
> A análise da dependência baseada em agentes não está disponível no Governo de Azure. Pode usar a análise de dependência sem agente

## <a name="whats-the-difference-between-agent-based-and-agentless"></a>Qual é a diferença entre agente e agente?

As diferenças entre visualização sem agente e visualização baseada em agentes são resumidas na tabela.

**Requisito** | **Sem agente** | **Baseada em agente**
--- | --- | ---
Suporte | Esta opção está atualmente em pré-visualização, e só está disponível para VMware VMs. [Reveja](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) os sistemas operativos suportados. | Em disponibilidade geral (GA).
Agente | Não é necessário instalar agentes em máquinas que pretende verificar. | Agentes a instalar em cada máquina no local que pretende analisar: O agente de monitorização da [Microsoft (MMA)](../azure-monitor/platform/agent-windows.md)e o [agente Desadependido](../azure-monitor/platform/agents-overview.md#dependency-agent). 
Pré-requisitos | [Reveja](concepts-dependency-visualization.md#agentless-analysis) os requisitos pré-requisitos e requisitos de implantação. | [Reveja](concepts-dependency-visualization.md#agent-based-analysis) os requisitos pré-requisitos e requisitos de implantação.
Log Analytics | Não é necessário. | A Azure Migrate utiliza a solução [de Mapa de Serviço](../azure-monitor/insights/service-map.md) nos [registos do Monitor Azure](../azure-monitor/log-query/log-query-overview.md) para visualização da dependência. [Saiba mais](concepts-dependency-visualization.md#agent-based-analysis).
Como funciona | Captura dados de ligação TCP em máquinas ativadas para visualização de dependência. Após a descoberta, recolhe dados em intervalos de cinco minutos. | Os agentes do Mapa de Serviço instalados numa máquina recolhem dados sobre processos TCP e ligações de entrada/saída para cada processo.
Dados | Nome do servidor da máquina de origem, processo, nome da aplicação.<br/><br/> Nome do servidor da máquina de destino, processo, nome da aplicação e porta. | Nome do servidor da máquina de origem, processo, nome da aplicação.<br/><br/> Nome do servidor da máquina de destino, processo, nome da aplicação e porta.<br/><br/> O número de ligações, latência e informações de transferência de dados são recolhidos e disponíveis para consultas de Log Analytics. 
Visualização | O mapa de dependência de um único servidor pode ser visto durante uma hora a 30 dias. | Mapa de dependência de um único servidor.<br/><br/> O mapa pode ser visto apenas durante uma hora.<br/><br/> Mapa de dependência de um grupo de servidores.<br/><br/> Adicione e remova os servidores num grupo a partir da vista do mapa.
Exportação de dados | Os dados dos últimos 30 dias podem ser descarregados num formato CSV. | Os dados podem ser consultados com Log Analytics.


## <a name="do-i-need-to-deploy-the-appliance-for-agentless-dependency-analysis"></a>Preciso de colocar o aparelho para análise de dependência sem agentes?

Sim, o [aparelho Azure Migrate](migrate-appliance.md) deve ser acionado.

## <a name="do-i-pay-for-dependency-visualization"></a>Pago pela visualização da dependência?

Não. Saiba mais sobre [os preços da Azure Migrate.](https://azure.microsoft.com/pricing/details/azure-migrate/)

## <a name="what-do-i-install-for-agent-based-dependency-visualization"></a>O que é que instalo para visualização de dependência baseada em agentes?

Para utilizar agentes de visualização de dependência baseados em agentes, descarregue e instale agentes em cada máquina no local que pretende avaliar:

- [Agente de monitorização da Microsoft (MMA)](../azure-monitor/platform/agent-windows.md)
- [Agente de Dependência](../azure-monitor/platform/agents-overview.md#dependency-agent)
- Se tiver máquinas que não tenham conectividade com a Internet, descarregue e instale o gateway Do Log Analytics nelas.

Só precisa destes agentes se utilizar a visualização de dependência baseada em agentes.

## <a name="can-i-use-an-existing-workspace"></a>Posso usar um espaço de trabalho existente?

Sim, para visualização de dependência baseada em agente, você pode anexar um espaço de trabalho existente ao projeto de migração e usá-lo para visualização de dependência. 

## <a name="can-i-export-the-dependency-visualization-report"></a>Posso exportar o relatório de visualização de dependência?

Não, o relatório de visualização de dependência na visualização baseada em agentes não pode ser exportado. No entanto, a Azure Migrate utiliza o Mapa de Serviços e pode utilizar o [Mapa de Serviços REST API](/rest/api/servicemap/machines/listconnections) para recuperar as dependências no formato JSON.

## <a name="can-i-automate-agent-installation"></a>Posso automatizar a instalação do agente?

Para visualização de dependência baseada em agentes:

- Utilize um [script para instalar o agente Dependency](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples).
- Para MMA, [utilize a linha de comando ou automação,](../azure-monitor/platform/log-analytics-agent.md#installation-and-configuration)ou utilize um [script](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab).
- Além dos scripts, pode utilizar ferramentas de implementação como o Microsoft Endpoint Configuration Manager e [o Intigua](https://www.intigua.com/intigua-for-azure-migration) para implementar os agentes.

## <a name="what-operating-systems-does-mma-support"></a>Que sistemas operativos suporta o MMA?

- Veja a lista de [sistemas operativos Windows que o MMA suporta.](../azure-monitor/platform/log-analytics-agent.md#supported-windows-operating-systems)
- Veja a lista dos [sistemas operativos Linux que o MMA suporta.](../azure-monitor/platform/log-analytics-agent.md#supported-linux-operating-systems)

## <a name="can-i-visualize-dependencies-for-more-than-one-hour"></a>Posso visualizar as dependências por mais de uma hora?

Para visualização baseada em agentes, pode visualizar dependências até uma hora. Pode voltar até um mês até uma data específica na história, mas a duração máxima para visualização é de uma hora. Por exemplo, você pode usar a duração do tempo no mapa de dependência para ver dependências para ontem, mas você pode ver dependências apenas por uma janela de uma hora. No entanto, pode utilizar registos do Azure Monitor para [consultar dados de dependência](./how-to-create-group-machine-dependencies.md) por uma duração mais longa.

Para visualização sem agente, pode ver o mapa de dependência de um único servidor a partir de uma duração entre uma hora e 30 dias.

## <a name="can-i-visualize-dependencies-for-groups-of-more-than-10-vms"></a>Posso visualizar dependências para grupos de mais de 10 VMs?

Pode [visualizar dependências](./how-to-create-a-group.md#refine-a-group-with-dependency-mapping) para grupos que tenham até 10 VMs. Se tiver um grupo com mais de 10 VMs, recomendamos que divida o grupo em grupos mais pequenos e, em seguida, visualize as dependências.

## <a name="next-steps"></a>Próximos passos

Leia a visão geral do [Azure Migrate](migrate-services-overview.md).
