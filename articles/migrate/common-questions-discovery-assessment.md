---
title: Descoberta, avaliação e análise de dependência FAQ
description: Obtenha respostas a perguntas comuns sobre descoberta, avaliação e análise de dependência em Azure Migrate.
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 7733213f78452b3f35b835eec847ec837138b8e5
ms.sourcegitcommit: 9cbd5b790299f080a64bab332bb031543c2de160
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/08/2020
ms.locfileid: "78932606"
---
# <a name="discovery-assessment-and-dependency-analysis---common-questions"></a>Descoberta, avaliação e análise de dependência - Questões comuns

Este artigo responde a perguntas comuns sobre descoberta, avaliação e análise de dependência em Azure Migrate. Se tiver outras perguntas, verifique estes recursos:

- [Perguntas gerais](resources-faq.md) sobre Azure Migrate
- Perguntas sobre o [aparelho Azure Migrate](common-questions-appliance.md)
- Perguntas sobre [migração de servidores](common-questions-server-migration.md)
- Obter perguntas respondidas no [fórum Migratório Azure](https://aka.ms/AzureMigrateForum)

## <a name="how-many-vms-can-i-discover-with-an-appliance"></a>Quantos VMs posso descobrir com um aparelho?

Pode descobrir até 10.000 VMware VMs, até 5.000 VMs Hiper-V e até 250 servidores físicos utilizando um único aparelho. Se tiver mais máquinas, leia sobre [a escala de uma avaliação Hyper-V,](scale-hyper-v-assessment.md) [dimensionando uma avaliação vMware,](scale-vmware-assessment.md)ou [escalando uma avaliação do servidor físico](scale-physical-assessment.md).

## <a name="the-size-of-my-vm-changed-can-i-run-an-assessment-again"></a>O tamanho do meu VM mudou. Posso fazer uma avaliação de novo?

O aparelho Azure Migrate recolhe continuamente informações sobre o ambiente no local.  Uma avaliação é uma imagem pontual dos VMs no local. Se alterar as definições de um VM que pretende avaliar, utilize a opção de recalcular para atualizar a avaliação com as alterações mais recentes.

## <a name="how-do-i-discover-vms-in-a-multitenant-environment"></a>Como descubro VMs num ambiente multiarrendatário?

- **VMware**: Se um ambiente é partilhado entre inquilinos e você não quer descobrir vMs de um inquilino na subscrição de outro inquilino, crie credenciais VMware vCenter Server que só podem aceder aos VMs que pretende descobrir. Em seguida, use essas credenciais quando começar a ser descoberta no aparelho Migratório Azure.
- **Hyper-V:** Discovery usa credenciais de hospedeiro hiper-V. Se os VMs partilham o mesmo hospedeiro Hyper-V, não há forma de separar a descoberta.  

## <a name="do-i-need-vcenter-server"></a>Preciso do Servidor VCenter?

Sim, o Azure Migrate requer o VCenter Server num ambiente VMware para realizar a descoberta. A Azure Migrate não suporta a descoberta de anfitriões ESXi que não são geridos pelo VCenter Server.

## <a name="what-are-the-sizing-options"></a>Quais são as opções de tamanho?

Com o dimensionamento das instalações, a Azure Migrate não considera os dados de desempenho da VM para avaliação. A Azure Migrate avalia os tamanhos de VM com base na configuração no local. Com o tamanho baseado no desempenho, o tamanho baseia-se em dados de utilização.

Por exemplo, se um VM no local tiver quatro núcleos e 8 GB de memória a 50% de utilização de CPU e 50% de utilização da memória:
- As-on-premises sku recomendará um Azure VM SKU que tem quatro núcleos e 8 GB de memória.
- O tamanho baseado no desempenho irá recomendar um VM SKU que tem dois núcleos e 4 GB de memória porque a percentagem de utilização é considerada.

Do mesmo modo, o dimensionamento do disco depende de critérios de dimensionamento e tipo de armazenamento:
- Se os critérios de dimensionamento forem baseados no desempenho e o tipo de armazenamento for automático, o Azure Migrate leva em conta os valores de iOPS e de entrada do disco quando identifica o tipo de disco-alvo (Standard ou Premium).
- Se os critérios de dimensionamento forem baseados no desempenho e o tipo de armazenamento for Premium, a Azure Migrate recomenda um SKU de disco Premium baseado no tamanho do disco no local. A mesma lógica aplica-se ao dimensionamento do disco quando o tamanho é tão no local e o tipo de armazenamento é Standard ou Premium.

## <a name="does-performance-history-and-utilization-affect-sizing"></a>O histórico de desempenho e a utilização afetam o dimensionamento?

Sim, o histórico de desempenho e a utilização afetam o dimensionamento em Azure Migrate.

### <a name="performance-history"></a>História do desempenho

Apenas para dimensionamento baseado no desempenho, a Azure Migrate recolhe o histórico de desempenho das máquinas no local e, em seguida, usa-a para recomendar o tamanho VM e o tipo de disco em Azure:

1. O aparelho perfis continuamente do ambiente no local para recolher dados de utilização em tempo real a cada 20 segundos.
1. O aparelho recolhe as amostras recolhidas de 20 segundos e utiliza-as para criar um único ponto de dados a cada 15 minutos.
1. Para criar o ponto de dados, o aparelho seleciona o valor máximo de todas as amostras de 20 segundos.
1. O aparelho envia os dados para o Azure.

### <a name="utilization"></a>Utilização

Quando se cria uma avaliação em Azure, dependendo da duração do desempenho e do valor percentil de histórico de desempenho definido, a Azure Migrate calcula o valor de utilização eficaz e, em seguida, usa-a para dimensionamento.

Por exemplo, se fixar a duração do desempenho para um dia e o valor percentil para o percentil 95, o Azure Migrate classifica os pontos de amostra de 15 minutos enviados pelo colecionador para o último dia em ordem ascendente. Escolhe o 95º valor por cento como a utilização eficaz.

A utilização do valor percentil 95 garante que os forasteiros são ignorados. Outliers podem ser incluídos se o seu Azure Migrate usar o percentil 99. Para escolher o pico de utilização para o período sem perder nenhum outliers, coloque Azure Migrate para usar o percentil 99.

## <a name="what-is-dependency-visualization"></a>O que é a visualização da dependência?

A visualização da dependência pode ajudá-lo a avaliar grupos de VMs para migrar com maior confiança. A visualização da dependência cruza as dependências das máquinas antes de fazer uma avaliação. Ajuda a garantir que nada é deixado para trás, e ajuda a evitar interrupções inesperadas quando migra para Azure. A Azure Migrate utiliza a solução Service Map no Monitor Azure para permitir a visualização da dependência. [Saiba mais](concepts-dependency-visualization.md).

> [!NOTE]
> A visualização da dependência não está disponível no Governo de Azure.

## <a name="whats-the-difference-between-agent-based-and-agentless"></a>Qual é a diferença entre a sede de agente e o agente?

As diferenças entre a visualização sem agente e a visualização baseada em agentes são resumidas na tabela.

**Requisito** | **Sem agente** | **Baseado em agente**
--- | --- | ---
Suporte | Esta opção encontra-se atualmente em pré-visualização e só está disponível para VMware VMs. [Reveja](migrate-support-matrix-vmware.md#agentless-dependency-visualization) os sistemas operativos suportados. | Na disponibilidade geral (GA).
Agente | Não é necessário instalar agentes em máquinas que queira sondar. | Agentes a instalar em cada máquina no local que pretende analisar: O agente de monitorização da [Microsoft (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows), e o [agente dependency](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview#dependency-agent). 
Pré-requisitos | [Reveja](concepts-dependency-visualization.md#agentless-visualization) os pré-requisitos e os requisitos de implantação. | [Reveja](concepts-dependency-visualization.md#agent-based-visualization) os pré-requisitos e os requisitos de implantação.
Log Analytics | Não é necessário. | A Azure Migrate utiliza a solução [Service Map](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) em [registos do Monitor Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) para visualização da dependência. [Saiba mais](concepts-dependency-visualization.md#agent-based-visualization).
Como funciona | Captura dados de ligação TCP em máquinas habilitadas para visualização da dependência. Após a descoberta, reúne dados em intervalos de cinco minutos. | Os agentes do Mapa de Serviço instalados numa máquina recolhem dados sobre os processos de TCP e ligações de entrada/saída para cada processo.
Dados | Nome do servidor da máquina de origem, processo, nome da aplicação.<br/><br/> Nome, processo, nome de servidor de máquina de destino, e porta. | Nome do servidor da máquina de origem, processo, nome da aplicação.<br/><br/> Nome, processo, nome de servidor de máquina de destino, e porta.<br/><br/> O número de ligações, latência e informações de transferência de dados são recolhidos e disponíveis para consultas de Log Analytics. 
Visualização | O mapa de dependência de um único servidor pode ser visto durante uma hora a 30 dias. | Mapa de dependência de um único servidor.<br/><br/> O mapa só pode ser visto ao longo de uma hora.<br/><br/> Mapa de dependência de um grupo de servidores.<br/><br/> Adicione e remova os servidores num grupo a partir da vista do mapa.
Exportação de dados | Atualmente não pode ser descarregado em formato tabular. | Os dados podem ser consultados com log Analytics.

## <a name="do-i-pay-for-dependency-visualization"></a>Pago pela visualização da dependência?

Não. Saiba mais sobre os preços do [Azure Migrate.](https://azure.microsoft.com/pricing/details/azure-migrate/)

## <a name="what-do-i-install-for-agent-based-dependency-visualization"></a>O que posso instalar para visualização da dependência baseada em agentes?

Para utilizar a visualização da dependência baseada no agente, descarregue e instale agentes em cada máquina no local que pretende avaliar:

- [Agente de Monitorização da Microsoft (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)
- [Agente de dependência](../azure-monitor/platform/agents-overview.md#dependency-agent)
- Se tiver máquinas que não tenham conectividade com a Internet, descarregue e instale o gateway Log Analytics.

Só precisa destes agentes se usar visualização de dependência baseada em agentes.

## <a name="can-i-use-an-existing-workspace"></a>Posso usar um espaço de trabalho existente?

Sim, para visualização da dependência baseada em agentes, você pode anexar um espaço de trabalho existente ao projeto de migração e usá-lo para visualização de dependência. 

## <a name="can-i-export-the-dependency-visualization-report"></a>Posso exportar o relatório de visualização da dependência?

Não, o relatório de visualização da dependência na visualização baseada em agentes não pode ser exportado. No entanto, o Azure Migrate utiliza o Mapa de Serviço, e pode utilizar o Mapa de [Serviço REST API](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections) para recuperar as dependências em formato JSON.

## <a name="can-i-automate-agent-installation"></a>Posso automatizar a instalação de agente?

Para visualização da dependência baseada em agentes:

- Utilize um [script para instalar o agente Dependency](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples).
- Para MMA, [utilize a linha de comando ou automação,](../azure-monitor/platform/log-analytics-agent.md#installation-and-configuration)ou utilize um [script](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab).
- Além de scripts, pode utilizar ferramentas de implementação como o Microsoft Endpoint Configuration Manager e a [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration) para implementar os agentes.

## <a name="what-operating-systems-does-mma-support"></a>Que sistemas operativos suporta mMmA?

- Ver a lista de [sistemas operativos Windows que o MMA suporta.](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems)
- Veja a lista de [sistemas operativos Linux que o MMA suporta.](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems)

## <a name="can-i-visualize-dependencies-for-more-than-one-hour"></a>Posso visualizar dependências por mais de uma hora?

Para visualização baseada em agentes, pode visualizar dependências até uma hora. Pode recuar até um mês até uma data específica na história, mas a duração máxima para visualização é de uma hora. Por exemplo, você pode usar a duração do tempo no mapa de dependência para ver dependências para ontem, mas você pode ver dependências apenas por uma janela de uma hora. No entanto, pode utilizar registos do Monitor Azure para [consultar dados](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) de dependência por uma maior duração.

Para visualização sem agente, pode ver o mapa de dependência de um único servidor de uma duração entre uma hora e 30 dias.

## <a name="can-i-visualize-dependencies-for-groups-of-more-than-10-vms"></a>Posso visualizar dependências de grupos de mais de 10 VMs?

Pode [visualizar dependências](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) de grupos que tenham até 10 VMs. Se você tem um grupo que tem mais de 10 VMs, recomendamos que divida o grupo em grupos menores, e depois visualize as dependências.

## <a name="next-steps"></a>Passos seguintes

Leia a visão geral do [Azure Migrate.](migrate-services-overview.md)
