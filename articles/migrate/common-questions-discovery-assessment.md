---
title: Questões comuns - descoberta, avaliação e análise de dependência em Azure Migrate
description: Obtenha respostas a perguntas comuns sobre descoberta, avaliação e análise de dependência em Azure Migrate.
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 7edc73742b61e4e5e94431c50d14d263bbbea641
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78362257"
---
# <a name="common-questions-about-discovery-assessment-and-dependency-analysis"></a>Questões comuns sobre descoberta, avaliação e análise de dependência

Este artigo responde a perguntas comuns sobre descoberta, avaliação e análise de dependência em Azure Migrate. Se tiver outras perguntas, reveja estes artigos:

- [Perguntas gerais](resources-faq.md) sobre Azure Migrate.
- [Perguntas](common-questions-appliance.md) sobre o aparelho Azure Migrate.
- [Perguntas](common-questions-server-migration.md) sobre migração de servidores.
- Colocar perguntas sobre o [fórum Migratório Azure](https://aka.ms/AzureMigrateForum)



## <a name="how-many-vms-can-i-discover-with-an-appliance"></a>Quantos VMs posso descobrir com um aparelho?

Pode descobrir até 10.000 VMware VMs, até 5.000 VMs Hiper-V e até 250 servidores físicos com um único aparelho. Se tiver mais máquinas, reveja artigos sobre a escala [de Hiper-V,](scale-hyper-v-assessment.md) [VMware](scale-vmware-assessment.md)e avaliação [do servidor físico.](scale-physical-assessment.md)



## <a name="vm-size-changed-can-i-run-an-assessment-again"></a>O tamanho da VM mudou. Posso fazer uma avaliação de novo?

O aparelho Azure Migrate recolhe continuamente informações sobre máquinas no local, e uma avaliação é um instantâneo pontual. Se alterar as definições de um VM que pretende avaliar, utilize a opção de recalcular para atualizar a avaliação com as alterações mais recentes.

## <a name="how-do-i-discover-vms-in-a-multitenant-environment"></a>Como descubro VMs num ambiente multiarrendatário?

- **VMware**: Se um ambiente é partilhado entre inquilinos, e você não quer descobrir vMs de um inquilino na subscrição de outro inquilino, crie credenciais vCenter Server que podem aceder apenas aos VMs que você quer descobrir. Em seguida, use essas credenciais quando começar a ser descoberta no aparelho Migratório Azure.
- **Hyper-v**: Discovery usa credenciais de hospedeiro hiper-V. Se os VMs partilham o mesmo hospedeiro Hyper-V, não há forma de separar a descoberta.  


## <a name="do-i-need-vcenter-server"></a>Preciso do Servidor VCenter?

Sim, o Azure Migrate precisa do VCenter Server para realizar a descoberta num ambiente VMware. Não suporta a descoberta de anfitriões ESXi que não são geridos pelo VCenter Server.


## <a name="whats-are-the-sizing-options"></a>Quais são as opções de tamanho?

Com o dimensionamento das instalações, a Azure Migrate não considera os dados de desempenho da VM para avaliação. Avalia os tamanhos de VM com base na configuração no local. Com o tamanho baseado no desempenho, o tamanho baseia-se em dados de utilização.

- Por exemplo, se um VM no local tiver 4 núcleos e 8 GB de memória a 50% de utilização de CPU e 50% de utilização da memória:
    - As-on-premises sku recomendará um Azure VM SKU que tem quatro núcleos e 8 GB de memória.
    - O tamanho baseado no desempenho irá recomendar um VM SKU que tem dois núcleos e 4 GB de memória, porque a percentagem de utilização é considerada.

- Da mesma forma, o tamanho do disco depende de critérios de dimensionamento e tipo de armazenamento.
    - Se os critérios de dimensionamento forem baseados no desempenho e o tipo de armazenamento for automático, o Azure Migrate leva em conta os valores de iOPS e de entrada do disco quando identifica o tipo de disco-alvo (standard ou premium).
    - Se os critérios de dimensionamento forem baseados no desempenho e o tipo de armazenamento for premium, a Azure Migrate recomenda um SKU de disco premium, com base no tamanho do disco no local. A mesma lógica aplica-se ao dimensionamento do disco, quando o tamanho é como no local, e o tipo de armazenamento é padrão ou premium.

## <a name="does-performance-historyutilization-impact-sizing"></a>O histórico de desempenho/utilização impacta o tamanho?

Estas propriedades só são aplicáveis ao dimensionamento baseado no desempenho.

- A Azure Migrate recolhe o histórico de desempenho das máquinas no local e utiliza-a para recomendar o tamanho VM e o tipo de disco em Azure.
- O aparelho perfis continuamente do ambiente no local, para recolher dados de utilização em tempo real a cada 20 segundos.
- O aparelho enrola as amostras de 20 segundos e cria um único ponto de dados a cada 15 minutos.
- Para criar o ponto de dados, o aparelho seleciona o valor máximo de todas as amostras de 20 segundos.
- O aparelho envia estes dados para o Azure.

Quando se cria uma avaliação em Azure, com base na duração do desempenho e no valor percentil do histórico de desempenho, a Azure Migrate calcula o valor de utilização eficaz e utiliza-a para dimensionamento.

- Por exemplo, se fixar a duração do desempenho para um dia e o valor percentil para 95 percentil, o Azure Migrate classifica os pontos de amostra de 15 minutos enviados pelo colecionador no último dia em ordem ascendente, e escolhe o 95º valor percentil como o eficaz utilização.
- A utilização do valor do percentil 95 garante que os forasteiros são ignorados. Outliers podem ser incluídos se usar o percentil 99. Se quiser escolher o pico de utilização para o período, sem perder nenhum outliers, selecione o percentil 99.

## <a name="what-is-dependency-visualization"></a>O que é a visualização da dependência?

Utilize a visualização da dependência para avaliar grupos de VMs para migração com maior confiança. A visualização da dependência cruza as dependências das máquinas antes de fazer uma avaliação. Ajuda a garantir que nada é deixado para trás, e assim ajuda a evitar interrupções inesperadas quando migra para Azure. A Azure Migrate utiliza a solução Service Map no Monitor Azure para permitir a visualização da dependência. [aprender mais.](concepts-dependency-visualization.md)

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
Não. [Saiba mais](https://azure.microsoft.com/pricing/details/azure-migrate/) sobre os preços do Azure Migrate.

## <a name="what-do-i-install-for-agent-based-dependency-visualization"></a>O que posso instalar para visualização da dependência baseada em agentes?

Para utilizar a visualização da dependência baseada no agente, precisa de descarregar e instalar agentes em cada máquina no local que pretende avaliar.

Instale os seguintes agentes em cada máquina:
- [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).
- [Agente de dependência.](../azure-monitor/platform/agents-overview.md#dependency-agent)
- Se tiver máquinas sem conectividade de internet, precisa de descarregar e instalar o gateway Log Analytics.

Não precisas destes agentes a não ser que estejas a usar visualização de dependência baseada em agentes.

## <a name="can-i-use-an-existing-workspace"></a>Posso usar um espaço de trabalho existente?

Sim, para visualização da dependência baseada em agentes, você pode anexar um espaço de trabalho existente ao projeto de migração e usá-lo para visualização de dependência. 

## <a name="can-i-export-the-dependency-visualization-report"></a>Posso exportar o relatório de visualização da dependência?

Não, o relatório de visualização da dependência na visualização baseada em agentes não pode ser exportado. No entanto, o Azure Migrate utiliza o Mapa de Serviço, e pode utilizar o Mapa de [Serviço REST API](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections) para recuperar as dependências em formato JSON.

## <a name="can-i-automate-agent-installation"></a>Posso automatizar a instalação de agente?
Para visualização da dependência baseada no agente, automatizar da seguinte forma:

- Utilize este [script para instalar o agente Dependency](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples).
- Para o MMA, siga estas [instruções](../azure-monitor/platform/log-analytics-agent.md#installation-and-configuration) para utilizar a linha de comando ou automação, ou utilize [este script](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab).
- Além de scripts, também pode utilizar ferramentas de implementação como o Microsoft Endpoint Configuration Manager e [a Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration) para implementar os agentes.


## <a name="what-operating-systems-does-mma-support"></a>Que sistemas operativos suporta mMmA?

- Ver a lista de [sistemas operativos Windows suportados pelo MMA](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems).
- Ver a lista de [sistemas operativos Linux suportados pelo MMA](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems).

## <a name="can-i-visualize-dependencies-for-more-than-an-hour"></a>Posso visualizar dependências por mais de uma hora?
Para visualização baseada em agentes, pode visualizar dependências até uma hora. Pode voltar a uma data específica na história, desde há um mês, mas a duração máxima para visualização é de uma hora. Por exemplo, você pode usar a duração do tempo no mapa de dependência para ver dependências para ontem, mas você pode ver dependências apenas por uma janela de uma hora. No entanto, pode utilizar registos do Monitor Azure para [consultar dados](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) de dependência durante uma duração mais longa.

Para visualização sem agente, pode ver o mapa de dependência de um único servidor de uma duração de uma hora a 30 dias.


## <a name="can-visualize-dependencies-for-groups-of-more-than-10-vms"></a>Pode visualizar dependências para grupos de mais de 10 VMs?
Pode [visualizar dependências](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) de grupos que contenham até 10 VMs. Se você tem um grupo com mais de 10 VMs, recomendamos que divida o grupo em grupos menores e, em seguida, visualize as dependências.




## <a name="next-steps"></a>Passos seguintes
Leia a visão geral do [Azure Migrate.](migrate-services-overview.md)
