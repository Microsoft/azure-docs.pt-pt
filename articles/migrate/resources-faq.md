---
title: Migrações para Azure-perguntas frequentes (FAQ) | Microsoft Docs
description: Aborda perguntas frequentes sobre as migrações para Azure
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: snehaa
ms.openlocfilehash: 96fde6ade45bb92036fbc9ac803eebd50640bf85
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/23/2019
ms.locfileid: "70013453"
---
# <a name="azure-migrate-frequently-asked-questions-faq"></a>Azure Migrate: Perguntas Mais Frequentes (FAQ)

Este artigo responde às perguntas frequentes sobre as migrações para Azure. Se você tiver outras consultas depois de ler este artigo, poste-as no fórum de migrações para [Azure](https://aka.ms/AzureMigrateForum).

## <a name="general"></a>Geral

### <a name="which-azure-geographies-does-azure-migrate-support"></a>Quais Geografia do Azure há suporte para migrações para Azure?

Consulte a [lista para VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#azure-migrate-projects) e a [lista para o Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#azure-migrate-projects).

### <a name="whats-the-difference-between-azure-migrate-and-azure-site-recovery"></a>Qual é a diferença entre migrações e Azure Site Recovery do Azure?

As migrações para Azure fornecem um hub centralizado para iniciar a migração, executar e acompanhar a descoberta e a avaliação de máquinas e cargas de trabalho e executar e acompanhar a migração de máquinas e cargas de trabalho para o Azure. [Azure site Recovery](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure) é uma solução de recuperação de desastre. A migração de servidor de migrações para Azure usa Azure Site Recovery no back-end para habilitar cenários de migração para a migração de comparação entre computadores locais e com o deslocamento de precisão.

## <a name="azure-migrate-appliance-vmwarephysical-servers"></a>Dispositivo de migrações para Azure (servidores VMware/físicos)

### <a name="how-does-the-azure-migrate-appliance-connect-to-azure"></a>Como o dispositivo de migrações para Azure se conecta ao Azure?

A conexão pode ser pela Internet ou você pode usar o Azure ExpressRoute com o emparelhamento público/Microsoft.

### <a name="what-are-the-network-connectivity-requirements-for-azure-migrate-server-assessment-and-migration"></a>Quais são os requisitos de conectividade de rede para migração e avaliação do servidor de migrações para Azure?

Consulte as matrizes de suporte do [VMware](migrate-support-matrix-vmware.md) e [Hyper-V](migrate-support-matrix-hyper-v.md) para obter informações sobre as URLs e as portas necessárias para habilitar a migração do Azure para se comunicar com o Azure.

### <a name="can-i-harden-the-appliance-vm-that-i-set-up-with-the-template"></a>Posso proteger a VM do dispositivo que configurei com o modelo?

Você pode adicionar componentes (por exemplo, antivírus) ao modelo, desde que você deixe as regras de comunicação e firewall necessárias para o dispositivo de migrações para Azure como está.

### <a name="what-data-is-collected-by-the-azure-migrate-appliance"></a>Quais dados são coletados pelo dispositivo de migrações para Azure?

Você pode exibir detalhes sobre os dados coletados pelo dispositivo de migrações para Azure [na documentação de migrações para Azure](https://docs.microsoft.com/azure/migrate/migrate-appliance#collected-performance-data-vmware).

### <a name="is-there-any-performance-impact-on-the-analyzed-vmware-or-hyper-v-environment"></a>Há algum impacto no desempenho sobre o ambiente analisado do VMware ou do Hyper-V?

O dispositivo de migrações para Azure cria perfis de máquinas locais continuamente para medir os dados de desempenho da VM. Essa criação de perfil não tem impacto sobre o desempenho nos hosts Hyper-V/ESXi ou no vCenter Server.

### <a name="where-is-the-collected-data-stored-and-for-how-long"></a>Onde os dados coletados são armazenados e por quanto tempo?

Os dados coletados pelo dispositivo migrações para Azure são armazenados no local do Azure que você escolhe ao criar o projeto de migração. Os dados são armazenados com segurança em uma assinatura da Microsoft e são excluídos quando você exclui o projeto de migrações para Azure.

Para a visualização de dependência, se você instalar agentes nas VMs, os dados coletados pelos agentes de dependência serão armazenados nos EUA, em um espaço de trabalho Log Analytics criado na assinatura do Azure. Esses dados são excluídos quando você exclui o espaço de trabalho Log Analytics em sua assinatura. Para obter mais informações, consulte [visualização de dependência](concepts-dependency-visualization.md).

### <a name="what-volume-of-data-is-uploaded-by-the-azure-migrate-appliance-during-continuous-profiling"></a>Qual volume de dados é carregado pelo dispositivo de migrações para Azure durante a criação de perfil contínua?

O volume de dados enviados para migrações para Azure varia dependendo de vários parâmetros. Para dar uma noção do volume: um projeto de migrações para Azure com 10 computadores (cada um com um disco e uma NIC) envia cerca de 50 MB por dia. Esse valor é uma aproximação. Ele muda com base no número de pontos de dados para os NICs e discos. (O aumento nos dados enviados será não linear se o número de computadores, NICs ou discos aumentar.)

### <a name="is-the-data-encrypted-at-rest-and-in-transit"></a>Os dados são criptografados em repouso e em trânsito?

Sim, ambos. Os metadados são enviados com segurança para o serviço de migrações para Azure pela Internet, via HTTPS. Os metadados são armazenados em um banco de dados [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest) e no [armazenamento de BLOBs do Azure](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) em uma assinatura da Microsoft. Os metadados são criptografados em repouso.

Os dados coletados pelos agentes de dependência também são criptografados em trânsito (HTTPS seguro). Ele é armazenado em um espaço de trabalho Log Analytics em sua assinatura. Ele também é criptografado em repouso.

### <a name="how-does-the-azure-migrate-appliance-communicate-with-vcenter-server-and-the-azure-migrate-service"></a>Como o dispositivo de migrações para Azure se comunica com vCenter Server e o serviço de migrações para Azure?

O dispositivo se conecta ao vCenter Server (porta 443) usando as credenciais fornecidas quando você configura o dispositivo. Ele usa o VMware PowerCLI para consultar vCenter Server coletar metadados sobre as VMs gerenciadas pelo vCenter Server. Ele coleta dados de configuração sobre VMs (núcleos, memória, discos, NICs e assim por diante) e também o histórico de desempenho de cada VM no último mês. Os metadados coletados são enviados para a avaliação do servidor de migrações para Azure (pela Internet via HTTPS) para avaliação.

### <a name="can-i-connect-the-same-appliance-to-multiple-vcenter-server-instances"></a>Posso conectar o mesmo dispositivo a várias instâncias de vCenter Server?

Não. Há um mapeamento de um para um entre um dispositivo e vCenter Server. Se precisar descobrir VMs em várias instâncias de vCenter Server, você precisará implantar vários dispositivos.


### <a name="i-changed-my-machine-size-can-i-run-the-assessment-again"></a>Alterei o tamanho do meu computador. Posso executar a avaliação novamente?

O dispositivo de migração do Azure coleta continuamente informações sobre o ambiente local. Porém, uma avaliação é um instantâneo pontual de VMs locais. Se você alterar as configurações em uma VM que deseja avaliar, use a opção recalcular para atualizar a avaliação com as alterações mais recentes.

### <a name="how-can-i-perform-discovery-in-a-multitenant-environment-in-azure-migrate-server-assessment"></a>Como posso executar a descoberta em um ambiente multilocatário na avaliação do servidor de migrações para Azure?

Para o VMware, se você tiver um ambiente compartilhado entre locatários e não quiser descobrir as VMs de um locatário na assinatura de outro locatário, crie vCenter Server credenciais que possam acessar somente as VMs que você deseja descobrir. Em seguida, use essas credenciais quando iniciar a descoberta no dispositivo de migrações para Azure.

Para o Hyper-V, a descoberta usa credenciais de host do Hyper-V. Se as VMs compartilharem o mesmo host do Hyper-V, não há como separar a descoberta no momento.  

### <a name="how-many-vms-can-i-discover-with-a-single-migration-appliance"></a>Quantas VMs posso descobrir com um único dispositivo de migração?

Você pode descobrir até 10.000 VMs VMware e até 5.000 VMs Hyper-V com um único dispositivo de migração. Se você tiver mais computadores no seu ambiente local, saiba como dimensionar o [Hyper-V](scale-hyper-v-assessment.md) e a avaliação do [VMware](scale-vmware-assessment.md) .

## <a name="azure-migrate-server-assessment"></a>Avaliação do servidor de migrações para Azure

### <a name="does-azure-migrate-server-assessment-support-assessment-of-physical-servers"></a>A avaliação do Azure migra o servidor oferece suporte à avaliação de servidores físicos?

Não, as migrações para Azure atualmente não dão suporte à avaliação de servidores físicos.

### <a name="does-azure-migrate-need-vcenter-server-to-perform-discovery-in-a-vmware-environment"></a>As migrações para Azure precisam de vCenter Server para executar a descoberta em um ambiente VMware?

Sim, as migrações para Azure precisam vCenter Server para executar a descoberta em um ambiente VMware. Ele não dá suporte à descoberta de hosts ESXi que não são gerenciados pelo vCenter Server.

### <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-map-toolkit"></a>Qual é a diferença entre a avaliação do servidor de migrações para Azure e o MAP Toolkit?

A avaliação de servidor de migrações para Azure fornece avaliação de migração para ajudar com a prontidão de migração e avaliação de cargas de trabalho para migração para o Azure. [O kit de ferramentas de avaliação e planejamento da Microsoft (MAP)](https://www.microsoft.com/download/details.aspx?id=7826) ajuda com outras tarefas, como o planejamento de migração para versões mais recentes de sistemas operacionais de cliente e servidor do Windows e acompanhamento de uso de software. Para esses cenários, continue a usar o MAP Toolkit.

### <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-azure-site-recovery-deployment-planner"></a>Qual é a diferença entre a avaliação do servidor de migrações para Azure e a Planejador de Implantações do Azure Site Recovery?

A avaliação do servidor de migrações para Azure é uma ferramenta de planejamento de migração. O Planejador de Implantações do Azure Site Recovery é uma ferramenta de planejamento de recuperação de desastre.

**Migração do VMware/Hyper-V para o Azure**: Se você planeja migrar seus servidores locais para o Azure, use a avaliação de servidor de migrações para Azure para planejamento de migração. Ele avalia as cargas de trabalho locais e fornece orientações, ideias e ferramentas para ajudá-lo a migrar seus servidores para o Azure. Depois de estar pronto com seu plano de migração, você pode usar ferramentas como migração de servidor de migrações para Azure para migrar os computadores para o Azure.

**Recuperação de desastre do VMware/Hyper-V para o Azure**: Para a recuperação de desastre no Azure por meio de Site Recovery, use o Planejador de Implantações de Site Recovery para planejamento. Site Recovery Planejador de Implantações faz uma avaliação profunda Site Recovery específica de seu ambiente local. Ele fornece recomendações necessárias por Site Recovery para operações de desastres bem-sucedidas, como replicação e failover de VMs.

### <a name="does-azure-migrate-support-cost-estimation-for-the-enterprise-agreement-ea-program"></a>As migrações para Azure dão suporte à estimativa de custo para o programa Enterprise Agreement (EA)?

Atualmente, as migrações para Azure não dão suporte à estimativa de custo para o [programa Enterprise Agreement](https://azure.microsoft.com/offers/enterprise-agreement-support/). A solução alternativa é especificar o pré- **pago** como a **oferta** e especificar manualmente a porcentagem de desconto (aplicável à assinatura) na caixa de **desconto** das propriedades de avaliação:

  ![Propriedades de avaliação](./media/resources-faq/discount.png)

### <a name="whats-the-difference-between-as-on-premises-sizing-and-performance-based-sizing"></a>Qual é a diferença entre o dimensionamento com base no dimensionamento e no desempenho no local?

Com o dimensionamento local, as migrações para Azure não consideram os dados de desempenho da VM. Ele dimensiona as VMs com base na configuração local. Com o dimensionamento baseado em desempenho, o dimensionamento é baseado nos dados de utilização.

Por exemplo, considere uma VM local com 4 núcleos e 8 GB de memória às 50% de utilização da CPU e 50% de utilização da memória. Para essa VM, o dimensionamento local recomenda uma SKU de VM do Azure que tem quatro núcleos e 8 GB de memória. O dimensionamento baseado em desempenho recomenda uma SKU de VM que tenha dois núcleos e 4 GB de memória, pois a porcentagem de utilização é considerada.

Da mesma forma, o dimensionamento de disco depende de duas propriedades de avaliação: critérios de dimensionamento e tipo de armazenamento. Se os critérios de dimensionamento forem baseados em desempenho e o tipo de armazenamento for automático, as migrações para Azure levarão em conta os valores de IOPS e taxa de transferência do disco quando identificarem o tipo de disco de destino (Standard ou Premium).

Se os critérios de dimensionamento forem baseados em desempenho e o tipo de armazenamento for Premium, as migrações para Azure recomendarão um disco Premium. A SKU do disco Premium é selecionada com base no tamanho do disco local. A mesma lógica é usada para fazer o dimensionamento do disco quando os critérios de dimensionamento são de dimensionamento local e o tipo de armazenamento é Standard ou Premium.

### <a name="what-impact-does-performance-history-and-utilization-percentile-have-on-size-recommendations"></a>Que impacto o histórico de desempenho e a utilização do percentil têm recomendações de tamanho?

Estas propriedades só são aplicáveis ao dimensionamento baseado no desempenho.

As migrações para Azure coletam o histórico de desempenho de máquinas locais e as usam para recomendar o tamanho da VM e o tipo de disco no Azure.

O dispositivo cria o perfil continuamente no ambiente local para coletar dados de utilização em tempo real a cada 20 segundos. O dispositivo acumula os exemplos de 20 segundos e cria um único ponto de dados para cada 15 minutos. Para criar o ponto de dados, o dispositivo seleciona o valor de pico de todos os exemplos de 20 segundos. O dispositivo envia esse ponto de dados para o Azure.

Quando você cria uma avaliação no Azure (com base no valor do percentual de duração de desempenho e histórico de desempenho), as migrações para Azure calculam o valor de utilização efetivo e as usam para o dimensionamento.

Por exemplo, suponha que você defina a duração do desempenho para um dia e o valor de percentil como 95 percentil. As migrações para Azure classifica os pontos de exemplo de 15 minutos enviados pelo coletor para o dia anterior em ordem crescente e escolhe o valor de 95 º percentil como a utilização efetiva.

O valor do 95 º percentil garante que você está ignorando as exceções. As exceções podem ser incluídas se você usar o 99 º percentil. Se você quiser escolher o pico de uso do período e não quiser perder as exceções, selecione o 99 º percentil.

### <a name="what-is-dependency-visualization"></a>O que é a visualização de dependência?

A visualização de dependências permite que você avalie grupos de VMs para migração com maior confiança. Ele faz verificações entre as dependências de computador antes de executar uma avaliação. A visualização de dependência ajuda a garantir que nada seja deixado para evitar interrupções inesperadas ao migrar para o Azure. As migrações para Azure usam a solução Mapa do Serviço em logs de Azure Monitor para habilitar a visualização de dependências.

> [!NOTE]
> A visualização de dependência não está disponível no Azure governamental.

### <a name="do-i-need-to-pay-to-use-dependency-visualization"></a>Preciso pagar para usar a visualização de dependência?

Não. Para obter mais informações, consulte preços de migrações para [Azure](https://azure.microsoft.com/pricing/details/azure-migrate/).

### <a name="do-i-need-to-install-anything-for-dependency-visualization"></a>É necessário instalar qualquer coisa para a visualização de dependência?

Para usar a visualização de dependência, você precisa baixar e instalar agentes em cada computador local que você deseja avaliar.

Você precisa instalar os seguintes agentes em cada computador:
- [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).
- [Agente de dependência](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure).
- Se você tiver computadores sem conectividade com a Internet, será necessário baixar e instalar Log Analytics gateway neles.

Você não precisa desses agentes, a menos que esteja usando a visualização de dependência.

### <a name="can-i-use-an-existing-workspace-for-dependency-visualization"></a>Posso usar um espaço de trabalho existente para a visualização de dependência?

Sim, você pode anexar um espaço de trabalho existente ao projeto de migração e usá-lo para a visualização de dependência. Para obter mais informações, consulte "como funciona" no artigo sobre a [visualização de dependência](concepts-dependency-visualization.md#how-does-it-work) .

### <a name="can-i-export-the-dependency-visualization-report"></a>Posso exportar o relatório de visualização de dependência?

Não, a visualização de dependência não pode ser exportada. Mas como as migrações para Azure usam Mapa do Serviço para a visualização de dependência, você pode usar a [API REST mapa do serviço](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections) para obter as dependências no formato JSON.

### <a name="how-can-i-automate-the-installation-of-microsoft-monitoring-agent-mma-and-the-dependency-agent"></a>Como é possível automatizar a instalação do Microsoft Monitoring Agent (MMA) e o Dependency Agent?

Use esse [script para instalar os agentes](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#installation-script-examples). Siga estas [instruções para instalar o MMA](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) usando a linha de comando ou automação. Para MMA, use [este script](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab).

Além de scripts, você também pode usar ferramentas de implantação como System Center Configuration Manager e [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration) para implantar os agentes.

### <a name="what-operating-systems-are-supported-by-mma"></a>Quais sistemas operacionais são compatíveis com o MMA?

- Exiba a lista de [sistemas operacionais Windows com suporte pelo MMA](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems).
- Exiba a lista de [sistemas operacionais Linux com suporte do MMA](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems).

### <a name="what-operating-systems-are-supported-by-the-dependency-agent"></a>Quais sistemas operacionais são compatíveis com o Dependency Agent?

- Exiba a lista de [sistemas operacionais Windows com suporte pelo Dependency Agent](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-windows-operating-systems).
- Exiba a lista de [sistemas operacionais Linux com suporte pelo Dependency Agent](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-linux-operating-systems).

### <a name="can-i-visualize-dependencies-in-azure-migrate-for-more-than-an-hour"></a>Posso Visualizar dependências no Azure migrar por mais de uma hora?
Não. Você pode visualizar dependências por até uma hora. Você pode voltar para uma data específica no histórico, até um mês, mas a duração máxima da visualização é de uma hora. Por exemplo, você pode usar a duração de tempo no mapa de dependências para exibir dependências de ontem, mas você pode exibi-las apenas para uma janela de uma hora. No entanto, você pode usar os logs de Azure Monitor para [consultar dados de dependência](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) por uma duração maior.

### <a name="can-i-use-dependency-visualization-for-groups-that-contain-more-than-10-vms"></a>Posso usar a visualização de dependência para grupos que contêm mais de 10 VMs?
Você pode [Visualizar dependências para grupos](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) que contêm até 10 VMS. Se você tiver um grupo com mais de 10 VMs, recomendamos dividir o grupo em grupos menores e, em seguida, Visualizar as dependências.

## <a name="azure-migrate-server-migration"></a>Migração de servidor de migrações para Azure

### <a name="whats-the-difference-between-azure-migrate-server-migration-and-azure-site-recovery"></a>Qual é a diferença entre a migração e a Azure Site Recovery do servidor de migrações para Azure?

A migração de servidor de migrações para Azure usa o mecanismo de replicação Site Recovery para a migração baseada em agente de VMs VMware, a migração de VMs do Hyper-V e a migração de servidores físicos para o Azure. A opção sem agente para migrar VMs VMware é originalmente incorporada à migração de servidor.

## <a name="next-steps"></a>Passos Seguintes
Leia a [visão geral](migrate-services-overview.md)de migrações para Azure.
