---
title: Azure Migrate - perguntas mais frequentes (FAQ) | Documentos da Microsoft
description: Endereços de perguntas mais frequentes sobre o Azure Migrate
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: snehaa
ms.openlocfilehash: 08a0312f12b3daab8b7f5e88da118b5bcbeb2f4c
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807330"
---
# <a name="azure-migrate---frequently-asked-questions-faq"></a>Azure Migrate - perguntas mais frequentes (FAQ)

Este artigo contém perguntas mais frequentes sobre o Azure Migrate. Se tiver quaisquer consultas adicionais depois de ler este artigo, publique o [fórum do Azure Migrate](https://aka.ms/AzureMigrateForum).

## <a name="general"></a>Geral

### <a name="which-azure-geographies-are-supported-by-azure-migrate"></a>Quais as geografias do Azure são suportadas pelo Azure Migrate?
Atualmente, o Azure Migrate suporta um número de localizações geográficas em que pode ser criado um projeto do Azure Migrate. Mesmo que só pode criar projetos nestes geografias, ainda pode avaliar as máquinas para outras localizações dos destinos. A geografia do projeto só é utilizada para armazenar os metadados detetados.

**Geografia** | **localização de armazenamento de metadados** Azure Government | US Gov Virgínia Ásia | Sudeste asiático ou na Europa do leste asiático | Centro-Sul Europa ou Europa Ocidental Reino Unido | Sul do Reino Unido ou no Reino Unido oeste dos Estados Unidos | Centro dos E.U.A. ou E.U.A. oeste 2

### <a name="how-is-azure-migrate-different-from-azure-site-recovery"></a>Como é o Azure Migrate diferente do Azure Site Recovery?

O Azure Migrate fornece ferramentas que ajudam a detetar, avaliar e migrar cargas de trabalho e as máquinas para o Azure. [O Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure) é uma solução de recuperação após desastre. Ambos os serviços compartilham alguns componentes.

## <a name="azure-migrate-appliance-vmwarephysical-servers"></a>O Azure Migrate appliance (VMware/servidores físicos)

### <a name="how-does-the-azure-migrate-appliance-connect-to-azure"></a>Como é que a aplicação do Azure Migrate ligar-se do Azure?

A ligação pode ser através da internet ou pode utilizar o ExpressRoute com peering público.

### <a name="what-network-connectivity-requirements-are-needed-for-azure-migrate-server-assessment-and-migration"></a>Que requisitos de conectividade de rede são necessários para a migração e de avaliação do servidor de migrar do Azure

Para os URLs e portas necessárias para o Azure Migrate comunicar com o Azure, reveja os [VMWare](migrate-support-matrix-vmware.md) e [Hyper-V](migrate-support-matrix-hyper-v.md) matrizes de suporte.

### <a name="can-i-harden-the-appliance-vmware-vm-i-set-up-with-the-ova-template"></a>Pode proteger a aplicação da VM de VMware, posso configurar com o modelo de OVA?

Componentes adicionais (por exemplo, software antivírus) podem ser adicionados no modelo OVA, desde que as regras de comunicação e de firewall necessárias para a aplicação do Azure Migrate re esquerda como está.   

### <a name="to-harden-the-azure-migrate-appliance-what-are-the-recommended-antivirus-av-exclusions"></a>Para proteger a aplicação do Azure Migrate, quais são as exclusões do antivírus (AV) recomendada?

Tem de excluir as seguintes pastas de análise na aplicação da:

- Pasta que contém os binários para o serviço do Azure Migrate. Exclua todas as subpastas.
- %ProgramFiles%\ProfilerService  
- Azure Migrate a aplicação Web. Exclua todas as subpastas.
- %SystemDrive%\inetpub\wwwroot
- A cache local para a base de dados e arquivos de log. O serviço Azure Migrate necessita de acesso de leitura/escrita a esta pasta.
  - %SystemDrive%\Profiler

### <a name="what-data-is-collected-by-azure-migrate"></a>Que dados são recolhidos pelo Azure Migrate?

A aplicação do Azure Migrate recolhe metadados para VMs no local, incluindo:

**Dados de configuração da VM**
- Nome de exibição da VM (no vCenter)
- Caminho de inventário VM (anfitrião/cluster/pasta no vCenter)
- Endereço IP
- Endereço MAC
- Sistema operativo
- Número de núcleos, discos, NICs
- Tamanho da memória, tamanhos de disco

**Dados de desempenho da VM**
- Utilização da CPU
- Utilização da memória
- Para cada disco anexado à VM:
  - Débito de leitura de disco
  - Débito de escritas de disco
  - Operações por seg de leitura de disco
  - Operações por segundo de escritas de disco
- Para cada adaptador de rede ligado à VM:
  - Entrada de rede
  - Saída de rede

Adição de TN, se implementar o mapeamento de dependências, os agentes de mapeamento de dependência recolhem informações que incluem a máquina FQDN, o sistema operativo, o endereço IP, o endereço MAC, processos em execução dentro da VM e ligações de TCP de entrada/saída para a VM. Esta deteção é opcional apenas utilizado se ativar o mapeamento da dependência para a deteção.

### <a name="is-there-any-performance-impact-on-the-analyzed-esxi-host-environment"></a>Existe qualquer impacto no desempenho no ambiente de anfitrião do ESXi analisado?

Com a contínua de criação de perfis de dados de desempenho, os perfis de aplicação do Azure Migrate máquinas no local para medir os dados de desempenho da VM. Isso tem quase zero impacto no desempenho nos anfitriões ESXi, bem como no vCenter Server.

### <a name="where-is-the-collected-data-stored-and-for-how-long"></a>Em que são os dados recolhidos armazenados e como há muito tempo?

Os dados recolhidos pela aplicação do Azure Migrate são armazenados na localização do Azure que especificar ao criar o projeto de migração. Os dados são armazenados em segurança numa subscrição Microsoft e são eliminados quando eliminar o projeto do Azure Migrate.

Para a visualização de dependência, se instalar agentes nas VMs, os dados recolhidos pelos agentes do dependência são armazenados nos EUA, numa área de trabalho do Log Analytics criada na subscrição do Azure. Estes dados são eliminados quando eliminar a área de trabalho do Log Analytics na sua subscrição. [Saiba mais](concepts-dependency-visualization.md).

### <a name="what-is-the-volume-of-data-uploaded-by-azure-migrate-during-continuous-profiling"></a>O que é o volume de dados carregados pelo Azure Migrate durante a criação de perfis contínua?

O volume de dados enviados para o Azure Migrate varia com base em vários parâmetros. Para dar um indicador número, um projeto do Azure Migrate com 10 máquinas (cada uma com um disco e uma NIC), envia cerca de 50 MB por dia. Este é um valor aproximado, que as alterações com base no número de pontos de dados para os NICs e discos (os dados enviados são não-lineares se aumentar o número de máquinas, NICs ou discos).

### <a name="is-the-data-encrypted-at-rest-and-in-transit"></a>É os dados encriptados em repouso e em trânsito?

Sim para ambos. Os metadados de forma segura é enviado para o serviço Azure Migrate através da internet, através de https. Os metadados são armazenados num [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest)e, na [armazenamento de Blobs do Azure](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) numa subscrição Microsoft, e é encriptado em repouso.

Os dados recolhidos pelos agentes do dependência é também criptografado em trânsito (HTTPS seguro) e são armazenados numa área de trabalho do Log Analytics na subscrição de utilizador. Também é encriptado em repouso.

### <a name="how-does-the-azure-migrate-appliance-communicate-with-the-vcenter-server-and-the-azure-migrate-service"></a>Como a aplicação do Azure Migrate se comunique com o vCenter Server e o serviço Azure Migrate?

A aplicação da liga-se ao vCenter Server (porta 443) utilizando as credenciais fornecidas quando configurou a aplicação. Ele consulta o vCenter Server com o VMware PowerCLI, para recolher os metadados sobre as VMs geridas pelo vCenter Server. Recolhe os dados de configuração acerca das VMs (núcleos, memória, discos, etc. NIC), bem como o histórico de desempenho de cada VM para o mês passado. Os metadados recolhidos, em seguida, é enviado para a avaliação de servidor do Azure Migrate (através da internet através de HTTPS) para avaliação. 

### <a name="can-i-connect-the-same-appliance-to-multiple-vcenter-servers"></a>Pode ligar a aplicação da mesma para vários servidores vCenter?

Sim, uma única aplicação do Azure Migrate pode ser utilizada para detetar o vCenter vários servidores, mas não em simultâneo. Precisa executar deteções um após o outro.

### <a name="is-the-ova-template-used-by-site-recovery-integrated-with-the-ova-used-by-azure-migrate"></a>O modelo de OVA utilizado pelo Site Recovery é integrado com o OVA utilizado pelo Azure Migrate?

Atualmente, não há nenhuma integração. A. Modelo de OVA no Site Recovery é utilizado para configurar um servidor de configuração do Site Recovery para a replicação de servidor de VM de VMware/físico. A. OVA utilizados pelo Azure Migrate é utilizado para detetar VMs de VMware, gerido por um vCenter server, para fins de avaliação e migração.

### <a name="i-changed-my-machine-size-can-i-rerun-an-assessment"></a>Alterei a minha tamanho da máquina. Pode executar novamente uma avaliação?
A aplicação do Azure Migrate continuamente recolhe informações sobre o ambiente no local. No entanto, uma avaliação é um instantâneo de ponto no tempo de VMs no local. Se alterar as definições numa VM que pretende avaliar, utilize a opção "Recalcular" para atualizar a avaliação com as alterações mais recentes.

### <a name="how-can-i-discover-a-multi-tenant-environment-in-azure-migrate"></a>Como posso descobrir um ambiente de multi-inquilino no Azure Migrate?

Para o VMware, se tiver um ambiente que é partilhado em inquilinos e não pretender detetar as VMs de um inquilino na subscrição de outro inquilino, crie vCenter as credenciais do servidor com acesso apenas para essas VMs que pretende detetar. Em seguida, utilize as credenciais quando iniciar deteção na aplicação do Azure Migrate.

Para o Hyper-V, a deteção utiliza credenciais de anfitrião de Hyper-V, se as VMs partilharem o mesmo anfitrião de Hyper-V, não é atualmente possível separar a deteção.  

### <a name="how-many-vms-can-be-discovered-using-a-single-migration-appliance"></a>O número de VMs pode ser detetado com uma aplicação de migração única?

Pode detetar até 10 000 VMs de VMware e até 5000 VMs de Hyper-V utilizando uma aplicação de migração única.  Se tiver mais máquinas no seu ambiente no local, saiba como dimensionar [Hyper-V](scale-hyper-v-assessment.md) e [VMware](scale-vmware-assessment.md) avaliação.


## <a name="azure-migrate-server-assessment"></a>Azure Migrate: Avaliação de servidor

### <a name="does-azure-migrate-server-assessment-support-assessment-of-physical-servers"></a>Azure Migrate: Avaliação de servidor suporta a avaliação de servidores físicos?

Não, do Azure Migrate atualmente não suporta a avaliação de servidores físicos. 

### <a name="does-azure-migrate-need-vcenter-server-to-discover-a-vmware-environment"></a>Azure Migrate precisa vCenter Server para detetar um ambiente do VMware?

Sim, o Azure Migrate requer o vCenter Server para detetar um ambiente de VMware. Ele não dá suporte a deteção de anfitriões ESXi que não são geridas pelo vCenter Server.

### <a name="whats-the-difference-between-using-azure-migrate-server-assessment-and-the-map-toolkit"></a>Qual é a diferença entre a utilização do Azure Migrate: Avaliação de servidor e o Map Toolkit?

Azure Migrate: Avaliação de servidor fornece avaliação de migração para ajudar na preparação da migração e avaliação das cargas de trabalho para a migração para o Azure. [Microsoft Assessment and Planning (MAP) Toolkit](https://www.microsoft.com/download/details.aspx?id=7826) tem outras funcionalidades, como o planejamento para as versões mais recentes de sistemas de operativos de cliente e servidor do Windows e o controlo de utilização do software de migração. Para esses cenários, continue a usar o MAP Toolkit.

### <a name="how-is-azure-migrate-server-assessment-different-from-azure-site-recovery-deployment-planner"></a>Como é o Azure Migrate: Avaliação de servidor diferente do Azure Site Recovery Deployment Planner?

Azure Migrate: Avaliação de servidor é uma ferramenta de planejamento de migração. O Azure Site Recovery Deployment Planner é uma ferramenta de planejamento de recuperação de desastres.

- **Migração do VMware/Hyper-V para o Azure**: Se pretender migrar os servidores no local para o Azure, utilize o Azure Migrate: Ferramenta de avaliação de servidor para planeamento da migração. A ferramenta avalia as cargas de trabalho no local e fornece orientações, informações e mecanismos para ajudá-lo a migrar para o Azure. Assim que estiver pronto com o seu plano de migração, pode usar ferramentas como o Azure Migrate: Migração do servidor, para migrar as máquinas para o Azure.
- **Recuperação após desastre do VMware/Hyper-V para o Azure**: Para recuperação após desastre para o Azure com o Site Recovery, utilize o Site Recovery Deployment Planner para o planejamento de recuperação após desastre. Site Recovery Deployment Planner faz uma avaliação detalhada, de específicas de recuperação de Site do seu ambiente no local. Fornece recomendações necessárias pelo Site Recovery para as operações de após desastre, como replicação e ativação pós-falha de VMs. 

### <a name="does-azure-migrate-support-enterprise-agreement-ea-based-cost-estimation"></a>O Azure Migrate suporta a estimativa de custo baseado no contrato Enterprise EA?

O Azure Migrate atualmente não suporta a estimativa de custos para [oferta Enterprise Agreement](https://azure.microsoft.com/offers/enterprise-agreement-support/). A solução é especificar o pay as you go como a oferta e especificar manualmente a percentagem de desconto (aplicável à subscrição) no campo 'Desconto' de propriedades de avaliação.

  ![Desconto](./media/resources-faq/discount.png)

### <a name="whats-the-difference-between-as-on-premises-sizing-and-performance-based-sizing"></a>O que é a diferença entre o dimensionamento como no local e o dimensionamento com base no desempenho?

- Como o dimensionamento, no local do Azure Migrate não considere dados de desempenho da VM. Tamanhos de VMs com base na configuração no local. -No dimensionamento baseado no desempenho, dimensionamento baseia-se nos dados de utilização.
- Por exemplo, se uma VM no local tem 4 núcleos e 8 GB de memória com 50% da utilização de CPU e de 50% da utilização de memória, como no local dimensionamento recomenda um SKU de VM do Azure com 4 núcleos e 8GB de memória. No entanto, o dimensionamento baseado no desempenho, recomenda um SKU de VM de 2 núcleos e 4 GB, porque se considera que a percentagem de utilização.
- Da mesma forma, dimensionamento do disco depende de duas propriedades de avaliação - tipo de armazenamento e de critério de dimensionamento.
= Se o critério de dimensionamento for baseado no desempenho e o tipo de armazenamento é automático, os valores IOPS e débito do disco são considerados ao identificar o tipo de disco de destino (Standard ou Premium).
- Se o critério de dimensionamento for baseado no desempenho e o tipo de armazenamento premium, recomenda-se um disco premium. O disco premium que SKU está selecionada com base no tamanho do disco no local. A mesma lógica é utilizada para o disco de dimensionamento quando o critério de dimensionamento é como o dimensionamento no local e o tipo de armazenamento é standard ou premium.

### <a name="what-impact-does-performance-history-and-percentile-utilization-have-on-the-size-recommendations"></a>O impacto que o utilização de percentil e de histórico de desempenho tem as recomendações de tamanho?

Estas propriedades só são aplicáveis ao dimensionamento baseado no desempenho.

- O Azure Migrate recolhe histórico de desempenho das máquinas no local e utiliza-o para recomendar o tipo de disco e de tamanho VM no Azure.
- A aplicação da perfis continuamente o ambiente no local para recolher dados de utilização em tempo real a cada 20 segundos. A aplicação acumula os exemplos de 20 segundos e cria um ponto de dados individual para cada 15 minutos. Para criar o ponto de dados individual, a aplicação seleciona o valor de pico de todos os exemplos de 20 segundos e envia-o para o Azure.
- Quando criar uma avaliação no Azure (com base na duração do desempenho e valor de percentil de histórico de desempenho), o Azure Migrate calcula o valor de utilização eficiente e, utiliza-o para o dimensionamento.
- Por exemplo, se definir a duração do desempenho para ser um dia, e o valor de percentil para o percentil 95, do Azure Migrate utiliza os 15 pontos de exemplo minuto enviados pelo recoletor para o último dia, classifica-os por ordem ascendente e escolhe o valor de percentil 95 como o utilização eficiente.
- O valor de percentil 95 garante que está a ignorar quaisquer valores atípicos que podem ocorrer se usar o percentil de 99. Se quiser escolher a utilização de pico para o período e não quiser perder quaisquer valores atípicos, deve selecionar o percentil 99.

### <a name="what-is-dependency-visualization"></a>O que é a visualização de dependências?

Visualização de dependência permite-lhe avaliar de grupos de VMs para a migração com mais confiança. Ele entre as verificações dependências de máquina antes de executar uma avaliação. Visualização de dependências ajuda a garantir que nada seja deixado além de evitar falhas inesperadas, quando migrar para o Azure. O Azure Migrate utiliza a solução mapa de serviço nos registos do Azure Monitor, para permitir a visualização de dependência.

> [!NOTE]
> A funcionalidade de visualização de dependência não está disponível no Azure Government.

### <a name="do-i-need-to-pay-to-use-the-dependency-visualization-feature"></a>Preciso de pagar para utilizar a funcionalidade de visualização de dependências?

Não. [Saiba mais](https://azure.microsoft.com/pricing/details/azure-migrate/) sobre os preços do Azure Migrate.

### <a name="do-i-need-to-install-anything-for-dependency-visualization"></a>É necessário instalar nada para visualização de dependências?

Para utilizar a visualização de dependência, terá de transferir e instalar agentes em cada máquina no local que pretende avaliar.

- [Microsoft Monitoring agent(MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows) tem de ser instalado em cada máquina.
- O [agente de dependência](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure) tem de ser instalado em cada máquina.
- Além disso, se tiver máquinas sem conectividade internet, terá de transferir e instalar o gateway de Log Analytics nos mesmos.

Não precisa destes agentes, a menos que está usando a visualização de dependência.

### <a name="can-i-use-an-existing-workspace-for-dependency-visualization"></a>Pode utilizar uma área de trabalho existente para visualização de dependências?

Sim, pode anexar uma área de trabalho existente para o projeto de migração e tirar partido dos mesmos para visualização de dependência. [Saiba mais](concepts-dependency-visualization.md#how-does-it-work).

### <a name="can-i-export-the-dependency-visualization-report"></a>Pode exportar o relatório de visualização de dependências?

Não, não é possível exportar a visualização de dependência. No entanto, uma vez que o Azure Migrate utiliza o mapa de serviço para visualização de dependências, pode utilizar o [APIs de REST de mapa de serviço](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections) para obter as dependências no formato json.

### <a name="how-can-i-automate-the-installation-of-microsoft-monitoring-agent-mma-and-dependency-agent"></a>Como posso automatizar a instalação do Microsoft Monitoring Agent (MMA) e o agente de dependência?

[Utilize este script](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#installation-script-examples) para a instalação dos agentes. [Siga estas instruções](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) para instalar o MMA com a linha de comandos ou a automatização. Para o MMA, tire partido [este script](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab).

Além de scripts, pode utilizar ferramentas de implantação, como o System Center Configuration Manager [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration) etc. para implantar os agentes.

### <a name="what-operating-systems-are-supported-by-mma"></a>Que sistemas operativos são suportados pelo MMA?

- [Revisão](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems) a lista de sistemas de operativos do Windows suportados pelo MMA.
- [. Revisão] https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems) a lista de sistemas de operativos Linux suportados pelo MMA.

### <a name="what-are-the-operating-systems-supported-by-the-dependency-agent"></a>Quais são os sistemas operativos suportados pelo agente de dependência?

[Revisão](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-windows-operating-systems) os sistemas de operativos do Windows suportados pelo agente de dependência.
[Revisão](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-linux-operating-systems) a lista de sistemas de operativos Linux suportados pelo agente de dependência.

### <a name="can-i-visualize-dependencies-in-azure-migrate-for-more-than-an-hour"></a>Posso visualizar dependências no Azure Migrate para mais de uma hora?
Não, não é possível visualizar dependências para até uma hora. Pode voltar para uma data específica na história, até o mês passado, mas a duração máxima para a visualização é uma hora. Por exemplo, pode usar o tempo de duração do mapa de dependência para ver as dependências de ontem, mas pode apenas visualizá-lo para uma janela de uma hora. No entanto, pode utilizar os registos do Azure Monitor para [consultar dados de dependência](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) ao longo de um período mais longo.

### <a name="is-dependency-visualization-supported-for-groups-with-more-than-ten-vms"></a>Visualização de dependência é suportada para grupos com mais de dez VMs?
Pode [visualize as dependências para grupos](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) com até dez VMs. Se tiver um grupo com mais de dez VMs, recomendamos a dividir o grupo em grupos mais pequenos e, em seguida, visualize as dependências.

## <a name="azure-migrate-server-migration"></a>Azure Migrate: Migração do servidor

### <a name="how-is-azure-migrate-server-migration-different-from-azure-site-recovery"></a>Como é o Azure Migrate: Migração de servidor diferente do Azure Site Recovery?

Azure Migrate: Migração do servidor que tira partido do motor de replicação do Site Recovery para a migração baseada em agente de servidores para o Azure.
## <a name="next-steps"></a>Passos Seguintes
Leia o [descrição geral do Azure Migrate](migrate-services-overview.md)
