---
title: Perguntas comuns sobre as migrações para Azure
description: Obtenha respostas para perguntas comuns sobre o serviço migrações para Azure.
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: snehaa
ms.openlocfilehash: dc7dff0119ec849b447754ae54a45911038f6c48
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74284457"
---
# <a name="azure-migrate-common-questions"></a>Migrações para Azure: perguntas comuns

Este artigo responde a perguntas comuns sobre as migrações para Azure. Se você tiver outras consultas depois de ler este artigo, poste-as no [Fórum de migrações para Azure](https://aka.ms/AzureMigrateForum).

## <a name="general"></a>Geral

### <a name="which-azure-geographies-are-supported"></a>Quais regiões do Azure têm suporte?

Examine as geografias com suporte para migrações do Azure para [VM VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#azure-migrate-projects) e para [VMs do Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#azure-migrate-projects).

### <a name="whats-the-difference-between-azure-migrate-and-site-recovery"></a>Qual é a diferença entre migrações e Site Recovery do Azure?

As migrações para Azure fornecem um hub centralizado para gerenciar e acompanhar a descoberta, avaliação e migração de máquinas locais e cargas de trabalho para o Azure. [Azure site Recovery](../site-recovery/site-recovery-overview.md) é uma solução de recuperação de desastre. Migrações para Azure: a ferramenta de migração de servidor usa algumas funcionalidades de Site Recovery de back-end para a migração de comparação entre alguns computadores locais.

### <a name="how-do-i-delete-an-azure-migrate-project"></a>Como fazer excluir um projeto de migrações para Azure?

Siga [estas instruções](how-to-delete-project.md) para excluir um projeto. [Examine os recursos](how-to-delete-project.md#created-resources) que são criados à medida que você descobre, avalia e migra máquinas e cargas de trabalho em um projeto de migrações para Azure.


## <a name="azure-migrate-appliance"></a>Aplicação do Azure Migrate

### <a name="how-does-the-appliance-connect-to-azure"></a>Como o dispositivo se conecta ao Azure?

A conexão pode ser pela Internet ou usar o Azure ExpressRoute com emparelhamento público/Microsoft.

### <a name="what-network-connectivity-is-needed-for-azure-migrate-server-assessment-and-migration"></a>Qual conectividade de rede é necessária para a migração e avaliação do servidor de migrações para Azure?

Examine as matrizes de suporte do [VMware](migrate-support-matrix-vmware.md) e [Hyper-V](migrate-support-matrix-hyper-v.md) para obter informações sobre as URLs e portas necessárias para a migração do Azure para se comunicar com o Azure.

### <a name="can-i-harden-the-appliance-vm-created-with-the-template"></a>Posso proteger a VM do dispositivo criada com o modelo?

Você pode adicionar componentes (por exemplo, antivírus) ao modelo, desde que você deixe as regras de comunicação e firewall necessárias para o dispositivo de migrações para Azure como está.

### <a name="what-data-is-collected-by-the-azure-migrate-appliance"></a>Quais dados são coletados pelo dispositivo de migrações para Azure?

Examine os dados coletados pelo dispositivo da seguinte maneira:
- [Dados de desempenho](migrate-appliance.md#collected-performance-data-vmware) e [metadados](migrate-appliance.md#collected-metadata-vmware) para VMs VMware.
- [Dados de desempenho](migrate-appliance.md#collected-performance-data-hyper-v) e [metadados](migrate-appliance.md#collected-metadata-hyper-v) para VMs do Hyper-V.


### <a name="does-appliance-analysis-impact-performance"></a>A análise do dispositivo afeta o desempenho?

O dispositivo de migrações para Azure cria perfis de máquinas locais continuamente para medir os dados de desempenho da VM. Essa criação de perfil não tem impacto sobre o desempenho nos hosts Hyper-V/ESXi ou no vCenter Server.

### <a name="where-and-how-long-is-collected-data-stored"></a>Onde e por quanto tempo os dados coletados são armazenados?

Os dados coletados pelo dispositivo migrações para Azure são armazenados no local do Azure que você escolhe ao criar o projeto de migração. Os dados são armazenados com segurança em uma assinatura da Microsoft e são excluídos quando você exclui o projeto de migrações para Azure.

Para a visualização de dependência, os dados coletados são armazenados no Estados Unidos, em um espaço de trabalho Log Analytics criado na assinatura do Azure. Esses dados são excluídos quando você exclui o espaço de trabalho Log Analytics em sua assinatura. [Saiba mais](concepts-dependency-visualization.md) sobre a visualização de dependência.

### <a name="what-volume-of-data-is-uploaded-during-continuous-profiling"></a>Qual volume de dados é carregado durante a criação de perfil contínua?

O volume de dados enviados para migrações para Azure varia dependendo de vários parâmetros. Para dar uma noção do volume, um projeto de migrações para Azure com 10 computadores (cada um com um disco e uma NIC) envia cerca de 50 MB por dia. Esse valor é aproximado e muda com base no número de pontos de dados para as NICs e os discos. O aumento nos dados enviados será não linear se o número de computadores, NICs ou discos aumentar.

### <a name="is-the-data-encrypted-at-rest-and-in-transit"></a>Os dados são criptografados em repouso e em trânsito?

Sim, ambos.
- Os metadados são enviados com segurança para o serviço de migrações para Azure pela Internet, via HTTPS.
- Os metadados são armazenados em um banco de dados [do banco de dados Cosmos do Azure](../cosmos-db/database-encryption-at-rest.md) e no [armazenamento de BLOBs do Azure](../storage/common/storage-service-encryption.md) em uma assinatura da Microsoft. Os metadados são criptografados em repouso.
- Os dados para análise de dependência também são criptografados em trânsito (HTTPS seguro). Ele é armazenado em um espaço de trabalho Log Analytics em sua assinatura. Ele também é criptografado em repouso.

### <a name="how-does-the-appliance-communicate-with-vcenter-server-and-azure-migrate"></a>Como o dispositivo se comunica com vCenter Server e migrações para Azure?

1. O dispositivo se conecta ao vCenter Server (porta 443), usando as credenciais fornecidas quando você configura o dispositivo.
2. O dispositivo usa o VMware PowerCLI para consultar vCenter Server, a fim de coletar metadados sobre as VMs gerenciadas pelo vCenter Server. Ele coleta dados de configuração sobre VMs (núcleos, memória, discos, NICs) e o histórico de desempenho de cada VM para o mês passado.
3. Os metadados coletados são enviados para migrações para Azure: avaliação de servidor (pela Internet via HTTPS) para avaliação.

### <a name="can-i-connect-the-same-appliance-to-multiple-vcenter-server-instances"></a>Posso conectar o mesmo dispositivo a várias instâncias de vCenter Server?

Não. Há um mapeamento de um para um entre um dispositivo e vCenter Server. Para descobrir VMs em várias instâncias de vCenter Server, você precisa implantar vários dispositivos.


### <a name="machine-size-changed-can-i-run-the-assessment-again"></a>Tamanho da máquina alterado. Posso executar a avaliação novamente?

O dispositivo de migração do Azure coleta continuamente informações sobre o ambiente local. Porém, uma avaliação é um instantâneo pontual de VMs locais. Se você alterar as configurações em uma VM que deseja avaliar, use a opção recalcular para atualizar a avaliação com as alterações mais recentes.

### <a name="how-can-i-perform-discovery-in-a-multitenant-environment"></a>Como posso executar a descoberta em um ambiente multilocatário?

- Para o VMware, se seu ambiente for compartilhado entre locatários e você não quiser descobrir as VMs de um locatário na assinatura de outro locatário, crie vCenter Server credenciais que possam acessar somente as VMs que você deseja descobrir. Em seguida, use essas credenciais quando iniciar a descoberta no dispositivo de migrações para Azure.
- Para o Hyper-V, a descoberta usa credenciais de host do Hyper-V. Se as VMs compartilharem o mesmo host do Hyper-V, não há como separar a descoberta no momento.  

### <a name="how-many-vms-can-i-discover-with-a-single-appliance"></a>Quantas VMs posso descobrir com um único dispositivo?

Você pode descobrir até 10.000 VMs VMware e até 5.000 VMs Hyper-V com um único dispositivo de migração. Se você tiver mais computadores no seu ambiente local, saiba como dimensionar o [Hyper-V](scale-hyper-v-assessment.md) e a avaliação do [VMware](scale-vmware-assessment.md) .

### <a name="can-i-delete-the-azure-migrate-appliance-from-the-project"></a>Posso excluir o dispositivo de migrações para Azure do projeto?

Atualmente, não há suporte para a exclusão do dispositivo do projeto.

- A única maneira de excluir o dispositivo é excluir o grupo de recursos que contém o projeto de migrações para Azure associado ao dispositivo.
- No entanto, a exclusão do grupo de recursos também excluirá outros dispositivos registrados, o inventário descoberto, as avaliações e todos os outros componentes do Azure associados ao projeto no grupo de recursos.

## <a name="azure-migrate-server-assessment"></a>Avaliação do servidor de migrações para Azure


### <a name="does-azure-migrate-need-vcenter-server-for-vmware-vm-discovery"></a>As migrações para Azure precisam de vCenter Server para a descoberta de VM VMWare?

Sim, as migrações para Azure precisam vCenter Server para executar a descoberta em um ambiente VMware. Ele não dá suporte à descoberta de hosts ESXi que não são gerenciados pelo vCenter Server.

### <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-map-toolkit"></a>Qual é a diferença entre a avaliação do servidor de migrações para Azure e o MAP Toolkit?

A avaliação do servidor fornece avaliação para ajudar com a preparação da migração e a avaliação de cargas de trabalho para migração para o Azure. [O kit de ferramentas de avaliação e planejamento da Microsoft (MAP)](https://www.microsoft.com/download/details.aspx?id=7826) ajuda com outras tarefas, incluindo o planejamento de migração para versões mais recentes de sistemas operacionais de cliente/servidor do Windows e acompanhamento de uso de software. Para esses cenários, continue a usar o MAP Toolkit.

### <a name="whats-the-difference-between-server-assessment-and-the-site-recovery-deployment-planner"></a>Qual é a diferença entre a avaliação do servidor e o Site Recovery Planejador de Implantações?

A avaliação do servidor é uma ferramenta de planejamento de migração. O Site Recovery Planejador de Implantações é uma ferramenta de planejamento de recuperação de desastre.

- **Planejar a migração local para o Azure**: se você planeja migrar seus servidores locais para o Azure, use a avaliação do servidor para planejamento de migração. Ele avalia as cargas de trabalho locais e fornece orientações e ferramentas para ajudá-lo a migrar. Depois que o plano de migração estiver em vigor, você poderá usar ferramentas, incluindo migração de servidor de migrações para Azure, para migrar os computadores para o Azure.
- **Planejar a recuperação de desastre para o Azure**: se você planeja configurar a recuperação de desastre do local para o azure com site Recovery, use o Planejador de Implantações de site Recovery. O Planejador de Implantações fornece uma avaliação profunda e específica Site Recovery do seu ambiente local para fins de recuperação de desastres. Ele fornece recomendações sobre a recuperação de desastre, como replicação e failover.

### <a name="whats-the-difference-between-as-on-premises-and-performance-based-sizing"></a>Qual é a diferença entre o dimensionamento baseado no local e no desempenho?

Com o dimensionamento local, as migrações para Azure não consideram os dados de desempenho da VM para avaliação. Ele avalia os tamanhos de VM com base na configuração local. Com o dimensionamento baseado em desempenho, o dimensionamento é baseado nos dados de utilização.

- Por exemplo, se uma VM local tiver 4 núcleos e 8 GB de memória às 50% de utilização da CPU e 50% de utilização da memória, ocorrerá o seguinte:
    - O dimensionamento local recomendará um SKU de VM do Azure que tenha quatro núcleos e 8 GB de memória.
    - O dimensionamento baseado em desempenho recomendará uma SKU de VM que tenha dois núcleos e 4 GB de memória, pois a porcentagem de utilização é considerada.

- Da mesma forma, o dimensionamento de disco depende de duas propriedades de avaliação: critérios de dimensionamento e tipo de armazenamento.
    - Se os critérios de dimensionamento forem baseados em desempenho e o tipo de armazenamento for automático, as migrações para Azure levarão os valores de IOPS e taxa de transferência do disco em conta quando identificarem o tipo de disco de destino (Standard ou Premium).
    - Se os critérios de dimensionamento forem baseados em desempenho e o tipo de armazenamento for Premium, as migrações para Azure recomendarão uma SKU de disco Premium, com base no tamanho do disco local. A mesma lógica é aplicada ao dimensionamento de disco, quando o dimensionamento é o local, e o tipo de armazenamento é Standard ou Premium.

### <a name="does-performance-history-and-utilization-percentile-impact-size-recommendations"></a>O histórico de desempenho e a utilização afetam as recomendações de tamanho de impacto?

Estas propriedades só são aplicáveis ao dimensionamento baseado no desempenho.

- As migrações para Azure coletam o histórico de desempenho de máquinas locais e as usam para recomendar o tamanho da VM e o tipo de disco no Azure.
- O dispositivo cria o perfil continuamente no ambiente local, para coletar dados de utilização em tempo real a cada 20 segundos.
- O dispositivo acumula os exemplos de 20 segundos e cria um único ponto de dados a cada 15 minutos.
- Para criar o ponto de dados, o dispositivo seleciona o valor de pico de todos os exemplos de 20 segundos.
- O dispositivo envia esse ponto de dados para o Azure.

Quando você cria uma avaliação no Azure, com base no valor de duração de desempenho e do percentual de histórico de desempenho, as migrações para Azure calculam o valor de utilização efetivo e as usam para o dimensionamento.

- Por exemplo, se você definir a duração do desempenho para um dia e o valor de percentil como 95 percentil, as migrações para Azure classificarão os pontos de exemplo de 15 minutos enviados pelo coletor para o dia anterior em ordem crescente e escolherá o valor de 95 º percentil como o efetivo utilização.
- O uso do valor 95 º percentil garante que as exceções sejam ignoradas. As exceções podem ser incluídas se você usar o 99 º percentil. Se você quiser escolher o pico de uso do período, sem exceções, selecione o 99 º percentil.

## <a name="server-assessment---dependency-visualization"></a>Avaliação do servidor – visualização de dependência


### <a name="what-is-dependency-visualization"></a>O que é a visualização de dependência?

Use a visualização de dependência para avaliar grupos de VMs para migração com maior confiança. A visualização de dependência faz verificações entre as dependências do computador antes de executar uma avaliação. Ele ajuda a garantir que nada seja deixado para trás e, portanto, ajuda a evitar interrupções inesperadas ao migrar para o Azure. As migrações para Azure usam a solução Mapa do Serviço no Azure Monitor para habilitar a visualização de dependência. [saiba mais](concepts-dependency-visualization.md).

> [!NOTE]
> A visualização de dependência não está disponível no Azure governamental.

### <a name="do-i-need-to-pay-to-use-dependency-visualization"></a>Preciso pagar para usar a visualização de dependência?
Não. [Saiba mais](https://azure.microsoft.com/pricing/details/azure-migrate/) sobre os preços do Azure Migrate.

### <a name="do-i-need-to-install-anything-for-dependency-visualization"></a>É necessário instalar qualquer coisa para a visualização de dependência?

Para usar a visualização de dependência, você precisa baixar e instalar agentes em cada computador local que você deseja avaliar.

Você precisa instalar os seguintes agentes em cada computador:
- [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).
- [Agente de dependência](../azure-monitor/platform/agents-overview.md#dependency-agent).
- Se você tiver computadores sem conectividade com a Internet, será necessário baixar e instalar Log Analytics gateway neles.

Você não precisa desses agentes, a menos que esteja usando a visualização de dependência.

### <a name="can-i-use-an-existing-workspace-for-dependency-visualization"></a>Posso usar um espaço de trabalho existente para a visualização de dependência?

Sim, você pode anexar um espaço de trabalho existente ao projeto de migração e usá-lo para a visualização de dependência. [Saiba mais](concepts-dependency-visualization.md#how-does-it-work).

### <a name="can-i-export-the-dependency-visualization-report"></a>Posso exportar o relatório de visualização de dependência?

Não, a visualização de dependência não pode ser exportada. No entanto, as migrações para Azure usam Mapa do Serviço e você pode usar a [API REST mapa do serviço](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections) para recuperar as dependências no formato JSON.

### <a name="how-can-i-automate-the-installation-of-microsoft-monitoring-agent-mma-and-the-dependency-agent"></a>Como é possível automatizar a instalação do Microsoft Monitoring Agent (MMA) e o Dependency Agent?

Use esse [script para instalar o Dependency Agent](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples). Siga estas [instruções para instalar o MMA](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) usando a linha de comando ou automação. Para MMA, use [este script](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab).

Além de scripts, você também pode usar ferramentas de implantação como System Center Configuration Manager e [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration) para implantar os agentes.


### <a name="what-operating-systems-are-supported-by-mma"></a>Quais sistemas operacionais são compatíveis com o MMA?

- Exiba a lista de [sistemas operacionais Windows com suporte pelo MMA](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems).
- Exiba a lista de [sistemas operacionais Linux com suporte do MMA](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems).

### <a name="can-i-visualize-dependencies-for-more-than-an-hour"></a>Posso Visualizar dependências por mais de uma hora?
Não. Você pode visualizar dependências por até uma hora. Você pode voltar para uma data específica no histórico, até um mês, mas a duração máxima da visualização é de uma hora. Por exemplo, você pode usar a duração de tempo no mapa de dependências para exibir dependências de ontem, mas você pode exibir dependências somente para uma janela de uma hora. No entanto, você pode usar os logs de Azure Monitor para [consultar dados de dependência](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) por uma duração maior.

### <a name="can-i-use-dependency-visualization-for-groups-of-more-than-10-vms"></a>Posso usar a visualização de dependência para grupos com mais de 10 VMs?
Você pode [Visualizar dependências](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) para grupos que contêm até 10 VMS. Se você tiver um grupo com mais de 10 VMs, é recomendável dividir o grupo em grupos menores e, em seguida, Visualizar as dependências.

## <a name="azure-migrate-server-migration"></a>Migração de servidor de migrações para Azure

### <a name="whats-the-difference-between-azure-migrate-server-migration-and-site-recovery"></a>Qual é a diferença entre a migração e a Site Recovery do servidor de migrações para Azure?

- A migração sem agente de VMs do VMware locais é nativa na migração de servidor de migrações para Azure.
- Para a migração de VMs do Hyper-V, servidores físicos e VMs VMware baseadas em agente, a migração de servidor de migrações para Azure usa o mecanismo de replicação Azure Site Recovery.


## <a name="next-steps"></a>Passos seguintes
Leia a [visão geral de migrações para Azure](migrate-services-overview.md).
