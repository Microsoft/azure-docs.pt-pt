---
title: Azure Migrate - perguntas mais frequentes (FAQ) | Documentos da Microsoft
description: Endereços de perguntas mais frequentes sobre o Azure Migrate
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: snehaa
ms.openlocfilehash: 9577e2c8eea153b38475bf5126a41641b3642ae9
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56991330"
---
# <a name="azure-migrate---frequently-asked-questions-faq"></a>Azure Migrate - perguntas mais frequentes (FAQ)

Este artigo contém perguntas mais frequentes sobre o Azure Migrate. Se tiver quaisquer consultas adicionais depois de ler este artigo, publique o [fórum do Azure Migrate](https://aka.ms/AzureMigrateForum).

## <a name="general"></a>Geral

### <a name="does-azure-migrate-support-assessment-of-only-vmware-workloads"></a>O Azure Migrate suporta a avaliação de apenas as cargas de trabalho VMware?

Sim, do Azure Migrate só suporta atualmente a avaliação das cargas de trabalho do VMware. Suporte para Hyper-V e servidores físicos será ativado no futuro.

### <a name="does-azure-migrate-need-vcenter-server-to-discover-a-vmware-environment"></a>Azure Migrate precisa vCenter Server para detetar um ambiente do VMware?

Sim, o Azure Migrate requer o vCenter Server para detetar um ambiente de VMware. Não suporta a deteção de anfitriões ESXi que não são geridos por um vCenter Server.

### <a name="how-is-azure-migrate-different-from-azure-site-recovery"></a>Como é o Azure Migrate diferente do Azure Site Recovery?

O Azure Migrate é um serviço de avaliação que ajuda a detetar as suas cargas de trabalho no local e planeie a migração para o Azure. [O Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure), juntamente com a ser uma solução de recuperação após desastre, ajuda-o a migrar cargas de trabalho no local para VMs de IaaS no Azure.

### <a name="whats-the-difference-between-using-azure-migrate-for-assessments-and-the-map-toolkit"></a>O que é a diferença entre a utilização do Azure Migrate para avaliações e o Map Toolkit?

[O Azure Migrate](migrate-overview.md) fornece avaliação de migração especificamente para ajudar na preparação da migração e avaliação das cargas de trabalho no local para o Azure. [Microsoft Assessment and Planning (MAP) Toolkit](https://www.microsoft.com/en-us/download/details.aspx?id=7826) tem outras funcionalidades, como para as versões mais recentes de sistemas de operativos de cliente e servidor do Windows e controlo de utilização do software de planeamento de migração. Para esses cenários, continue a usar o MAP Toolkit.


### <a name="how-is-azure-migrate-different-from-azure-site-recovery-deployment-planner"></a>Como é o Azure Migrate diferente do Azure Site Recovery Deployment Planner?

O Azure Migrate é uma ferramenta de planejamento de migração e do Azure Site Recovery Deployment Planner é uma recuperação após desastre (DR), ferramenta de planejamento.

**Migração do VMware para Azure**: Se pretender migrar as cargas de trabalho no local para o Azure, utilize o Azure Migrate para planeamento da migração. O Azure Migrate avalia cargas de trabalho no local e fornece orientações, informações e mecanismos para ajudá-lo a migrar para o Azure. Assim que estiver pronto com o seu plano de migração, pode utilizar serviços como o Azure Site Recovery e o serviço de migração de base de dados do Azure para migrar as máquinas para o Azure.

**Migração do Hyper-V para o Azure**: O Azure Migrate só suporta atualmente avaliação das máquinas de virtuais de VMware para migração para o Azure. Suporte para Hyper-V está nas previsões para o Azure Migrate. Até lá, pode usar o Site Recovery Deployment Planner. Assim que o suporte de Hyper-V está ativado no Azure Migrate, pode utilizar o Azure Migrate para planejamento de migração de cargas de trabalho do Hyper-V.

**Recuperação após desastre do VMware/Hyper-V para o Azure**: Se pretende fazer a recuperação após desastre (DR) no Azure com o Azure Site Recovery (recuperação de sites), utilize o Site Recovery Deployment Planner para planejamento de DR. Site Recovery Deployment Planner faz uma avaliação profunda ASR específicos do seu ambiente no local. Fornece recomendações que são necessários pelo Site Recovery para operações bem-sucedidas de DR, como replicação, ativação pós-falha das suas máquinas virtuais.  

### <a name="which-azure-geographies-are-supported-by-azure-migrate"></a>Quais as geografias do Azure são suportadas pelo Azure Migrate?

O Azure Migrate suporta atualmente na Europa, Estados Unidos e o Azure Government como as geografias do projeto. Mesmo que só pode criar projetos de migração nestes geografias, ainda pode avaliar as máquinas para [várias localizações dos destinos](https://docs.microsoft.com/azure/migrate/how-to-modify-assessment#edit-assessment-properties). A geografia do projeto só é utilizada para armazenar os metadados detetados.

**Geografia** | **Localização de armazenamento de metadados**
--- | ---
Azure Government | Gov (US) - Virginia
Ásia | Sudeste Asiático
Europa | Europa do Norte ou da Europa Ocidental
Une Estados | E.U.A. Leste ou oeste dos E.U.A.

### <a name="how-does-the-on-premises-site-connect-to-azure-migrate"></a>Como ligar o site no local para o Azure Migrate?

A ligação pode ser através da internet ou utilizar o ExpressRoute com peering público.

### <a name="can-i-harden-the-vm-set-up-with-the-ova-template"></a>Pode proteger a VM que configurar com o modelo de OVA?

Componentes adicionais (por exemplo, software antivírus) podem ser adicionados no modelo de OVA, desde que as regras de comunicação e de firewall necessárias para a aplicação do Azure Migrate trabalhar são deixadas como está.   

### <a name="to-harden-the-azure-migrate-appliance-what-are-the-recommended-antivirus-av-exclusions"></a>Para proteger a aplicação do Azure Migrate, quais são as exclusões do antivírus (AV) recomendada?

Tem de excluir as seguintes pastas na aplicação para verificação antivírus:

- Pasta que tenha os binários do serviço do Azure Migrate. Exclua todas as subpastas.
  %ProgramFiles%\ProfilerService  
- Azure Migrate a aplicação Web. Exclua todas as subpastas.
  %SystemDrive%\inetpub\wwwroot
- Cache local para a base de dados e arquivos de log. Do Azure migrate precisa de serviço de acesso RW para esta pasta.
  %SystemDrive%\Profiler

## <a name="discovery"></a>Deteção

### <a name="what-data-is-collected-by-azure-migrate"></a>Que dados são recolhidos pelo Azure Migrate?

O Azure Migrate suporta dois tipos de deteção, a deteção com base na aplicação e a deteção com base no agente.
A deteção com base na aplicação recolhe metadados sobre as VMs no local, a lista completa de metadados recolhidos pela aplicação da esteja listada abaixo:

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
- Utilização de memória
- Para cada disco anexado à VM:
  - Débito de leitura de disco
  - Débito de escritas de disco
  - Operações por seg de leitura de disco
  - Operações por segundo de escritas de disco
- Para cada adaptador de rede ligado à VM:
  - Entrada de rede
  - Saída de rede

A deteção com base no agente é uma opção disponível além da deteção com base na aplicação e ajuda os clientes a [visualizarem as dependências](how-to-create-group-machine-dependencies.md) das VMs no local. Os agentes de dependência recolhem detalhes como FQDN, SO, endereço IP, endereço MAC, processos em execução dentro da VM e as ligações de TCP de entrada/saída da VM. A deteção baseada em agente é opcional e pode optar por não instalar os agentes se não pretender visualizar as dependências das VMs.

### <a name="would-there-be-any-performance-impact-on-the-analyzed-esxi-host-environment"></a>Deve haver nenhum impacto no desempenho no ambiente de anfitrião do ESXi analisado?

No caso do [uma abordagem de deteção de tempo](https://docs.microsoft.com/azure/migrate/concepts-collector), para recolher os dados de desempenho, o nível de estatísticas no vCenter server tinha que ser definido como 3. Defini-la para este nível recolheria uma grande quantidade de dados, que seriam armazenados na base de dados do servidor vCenter de resolução de problemas. Portanto, poderão resultar em alguns problemas de desempenho no vCenter Server. Deve haver impacto mínimo no anfitrião ESXi.

Introduzimos de criação de perfis contínua de dados de desempenho (o que estão em pré-visualização). Com a criação de perfis contínua, já não existe a necessidade de alterar o nível de estatísticas do servidor para executar uma avaliação com base no desempenho do vCenter. A aplicação recoletora vai agora criar perfis para as máquinas no local para medir os dados de desempenho das máquinas virtuais. Isso ter um impacto de desempenho de quase zero nos anfitriões ESXi, bem como no vCenter Server.

### <a name="where-is-the-collected-data-stored-and-for-how-long"></a>Em que são os dados recolhidos armazenados e como há muito tempo?

Os dados coletados pela aplicação recoletora são armazenados na localização do Azure que especificar ao criar o projeto de migração. Os dados são armazenados em segurança numa subscrição Microsoft e são eliminados quando o utilizador elimina o projeto do Azure Migrate.

Para a visualização de dependência, se instalar agentes nas VMs, os dados recolhidos pelos agentes do dependência são armazenados nos EUA numa área de trabalho do Log Analytics criada na subscrição do utilizador. Estes dados são eliminados quando eliminar a área de trabalho do Log Analytics na sua subscrição. [Saiba mais](https://docs.microsoft.com/azure/migrate/concepts-dependency-visualization).

### <a name="what-is-the-volume-of-data-which-is-uploaded-by-azure-migrate-in-the-case-of-continuous-profiling"></a>O que é o volume de dados que são carregados pelo Azure Migrate, no caso de criação de perfis contínua?

O volume de dados que são enviados para o Azure Migrate seria variam consoante vários parâmetros. Para dar um indicador número, um projeto com dez máquinas (cada ter um disco e uma NIC), enviaria cerca de 50 MB por dia. Este é um valor aproximado e seria mudar com base no número de pontos de dados para os NICs e os discos (os dados enviados seria não-lineares se aumentar o número de máquinas, NICs ou discos). 

### <a name="is-the-data-encrypted-at-rest-and-while-in-transit"></a>Os dados são encriptados em descanso e em trânsito?

Sim, os dados recolhidos são encriptados em inatividade tanto em trânsito. Os metadados recolhidos pela aplicação da são enviados de forma segura para o serviço Azure Migrate pela internet através de https. Os metadados recolhidos são armazenados na [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest) e, na [armazenamento de Blobs do Azure](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) numa subscrição Microsoft e são encriptados em descanso.

Os dados recolhidos pelos agentes do dependência é também criptografado em trânsito (canal de https seguro) e são armazenados numa área de trabalho do Log Analytics na subscrição do utilizador. Também são encriptado em inatividade.

### <a name="how-does-the-collector-communicate-with-the-vcenter-server-and-the-azure-migrate-service"></a>Como o recoletor se comunique com o vCenter Server e o serviço Azure Migrate?

A aplicação recoletor liga-se ao vCenter Server (porta 443) utilizando as credenciais fornecidas pelo utilizador na aplicação. Ele consulta o vCenter Server com o VMware PowerCLI para recolher os metadados sobre as VMs geridas pelo vCenter Server. Recolhe os dados de configuração acerca das VMs (núcleos, memória, discos, etc. NIC), bem como o histórico de desempenho de cada VM para o último mês do vCenter Server. Os metadados recolhidos, em seguida, é enviado para o serviço Azure Migrate (através da internet através de https) para avaliação. [Saiba mais](concepts-collector.md)

### <a name="can-i-connect-the-same-collector-appliance-to-multiple-vcenter-servers"></a>Pode ligar a aplicação recoletora mesmo para vários servidores vCenter?

Sim, uma aplicação de recoletor único pode ser utilizada para detetar o vCenter vários servidores, mas não em simultâneo. Terá de executar a deteção de um após o outro.

### <a name="is-the-ova-template-used-by-site-recovery-integrated-with-the-ova-used-by-azure-migrate"></a>O modelo de OVA utilizado pelo Site Recovery é integrado com o OVA utilizado pelo Azure Migrate?

Atualmente, não há nenhuma integração. A. Modelo de OVA no Site Recovery é utilizado para configurar um servidor de configuração do Site Recovery para a replicação de servidor de VM de VMware/físico. A. OVA utilizados pelo Azure Migrate é utilizado para detetar VMs de VMware, gerido por um vCenter server, para fins de avaliação da migração.

### <a name="i-changed-my-machine-size-can-i-rerun-the-assessment"></a>Alterei a minha tamanho da máquina. Pode executar novamente a avaliação?

Se alterar as definições numa VM que pretende avaliar, o acionador detetar novamente com a aplicação recoletora. A aplicação, utilize o **iniciar novamente a recolha** opção para fazer isso. Depois de terminar a coleção, selecione a opção **Recalcular** para a avaliação no portal, para obter os resultados da avaliação atualizada.

### <a name="how-can-i-discover-a-multi-tenant-environment-in-azure-migrate"></a>Como posso descobrir um ambiente de multi-inquilino no Azure Migrate?

Se tiver um ambiente que é partilhado em inquilinos e não pretender detetar as VMs de um inquilino na subscrição de outro inquilino, pode utilizar o campo de âmbito em que a aplicação recoletora para definir o âmbito de deteção. Se os inquilinos estão a partilhar anfitriões, criar uma credencial que tem acesso só de leitura para apenas as VMs que pertencem ao inquilino específico e, em seguida, utilize esta credencial a aplicação recoletora e especifique o âmbito como host para fazer a deteção. Em alternativa, também pode criar pastas no vCenter Server (Digamos Pasta1 para tenant1 e folder2 para tenant2), em anfitriões partilhado, mover as VMs para tenant1 em Pasta1 e para tenant2 para folder2 e, em seguida, definir âmbito as deteções no coletor de em conformidade ao especificar a pasta apropriada.

### <a name="how-many-virtual-machines-can-be-discovered-in-a-single-migration-project"></a>Número de máquinas virtuais possam ser detetado num projeto de migração única?

Pode descobrir 1500 máquinas de virtuais num projeto de migração única. Se tiver mais máquinas no seu ambiente no local, [Saiba mais](how-to-scale-assessment.md) sobre como é possível descobrir um ambiente de grandes dimensões no Azure Migrate.


## <a name="assessment"></a>Avaliação

### <a name="does-azure-migrate-support-enterprise-agreement-ea-based-cost-estimation"></a>Suporte do Azure Migrate Enterprise Agreement (EA) com base custa estimativa?

O Azure Migrate atualmente não suporta a estimativa de custos para [oferta Enterprise Agreement](https://azure.microsoft.com/offers/enterprise-agreement-support/). A solução é especificar pay as you go, como a oferta e a especificação manual a percentagem de desconto (aplicável à subscrição) no campo "De desconto" de propriedades de avaliação.

  ![Desconto](./media/resources-faq/discount.png)

### <a name="what-is-the-difference-between-as-on-premises-sizing-and-performance-based-sizing"></a>O que é a diferença entre o dimensionamento como no local e o dimensionamento com base no desempenho?

Quando especificar o critério de tamanho para ser como no local de dimensionamento, do Azure Migrate não considera os dados de desempenho das VMs e os tamanhos de VMs com base na configuração no local. Se o critério de dimensionamento for baseado no desempenho, o dimensionamento é feito com base nos dados de utilização. Por exemplo, se houver uma VM no local com 4 núcleos e 8 GB de memória com 50% de utilização de CPU e utilização da memória de 50%. Se o critério de dimensionamento é que um SKU de VM do Azure com 4 núcleos de dimensionamento no local e 8GB de memória é recomendado, no entanto, se o critério de dimensionamento for baseado no desempenho como SKU de VM de 2 núcleos e 4 GB teria de ser recomendada como a percentagem de utilização é considerada enquanto Recomendamos o tamanho. Da mesma forma, para discos, o dimensionamento do disco depende de duas propriedades de avaliação - tipo de armazenamento e de critério de dimensionamento. Se o critério de dimensionamento for baseado no desempenho e o tipo de armazenamento for automático, os valores de IOPS e de débito do disco são considerados para identificar o tipo de disco de destino (Standard ou Premium). Se o critério de dimensionamento for baseado no desempenho e o tipo de armazenamento for premium, é recomendado um disco premium e o SKU de disco premium no Azure é selecionado com base no tamanho do disco no local. É utilizada a mesma lógica para o dimensionamento do disco quando o critério de dimensionamento for como o dimensionamento no local e o tipo de armazenamento for standard ou premium.

### <a name="what-impact-does-performance-history-and-percentile-utilization-have-on-the-size-recommendations"></a>O impacto que o utilização de percentil e de histórico de desempenho tem as recomendações de tamanho?

Estas propriedades só são aplicáveis ao dimensionamento baseado no desempenho. O Azure Migrate recolhe o histórico de desempenho das máquinas no local e utiliza-o para recomendar o tipo de disco e o tamanho da VM no Azure. A aplicação de recoletor cria continuamente perfis do ambiente no local para recolher dados de utilização em tempo real a cada 20 segundos. A aplicação acumula os exemplos de 20 segundos e cria um ponto de dados individual para cada 15 minutos. Para criar o ponto de dados individual, a aplicação seleciona o valor de pico de todos os exemplos de 20 segundos e envia-o para o Azure. Quando cria uma avaliação no Azure, com base na duração do desempenho e no valor de percentil do histórico de desempenho, o Azure Migrate calcula o valor de utilização eficiente e utiliza-o no dimensionamento. Por exemplo, se tiver definido a duração do desempenho ser 1 dia e o valor de percentil para o percentil 95, do Azure Migrate utiliza o exemplo de 15 minutos pontos enviados pelo recoletor para o último dia, classifica-os por ordem ascendente e escolhe o valor de percentil 95 como o ut eficaz ilization. O valor de percentil 95 garante que está a ignorar quaisquer valores atípicos que podem ser se escolher percentil de 99. Se quiser escolher a utilização de pico para o período e não quiser perder quaisquer valores atípicos, deve selecionar o percentil 99.

## <a name="dependency-visualization"></a>Visualização de dependência

> [!NOTE]
> A funcionalidade de visualização de dependência não está disponível no Azure Government.

### <a name="what-is-dependency-visualization"></a>O que é a visualização de dependências?

Visualização de dependência permite-lhe avaliar de grupos de VMs para a migração com mais confiança por verificação de dependências de máquina antes de executar uma avaliação. Visualização de dependências ajuda-o a garantir que nada seja deixado, evitar falhas inesperadas, ao migrar para o Azure. O Azure Migrate utiliza a solução mapa de serviço nos registos do Azure Monitor para ativar a visualização de dependência.

### <a name="do-i-need-to-pay-to-use-the-dependency-visualization-feature"></a>Preciso de pagar para utilizar a funcionalidade de visualização de dependências?

Não. Saiba mais sobre os preços do Azure Migrate [aqui](https://azure.microsoft.com/pricing/details/azure-migrate/).

### <a name="do-i-need-to-install-anything-for-dependency-visualization"></a>É necessário instalar nada para visualização de dependências?

Para utilizar a visualização de dependência, terá de transferir e instalar agentes em cada máquina no local que pretende avaliar.

- [Microsoft Monitoring agent(MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows) tem de ser instalado em cada máquina.
- O [agente de dependência](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure) tem de ser instalado em cada máquina.
- Além disso, se tiver máquinas sem conectividade internet, terá de transferir e instalar o gateway de Log Analytics nos mesmos.

Não precisa destes agentes nas máquinas que pretende avaliar a menos que está usando a visualização de dependência.

### <a name="can-i-use-an-existing-workspace-for-dependency-visualization"></a>Pode utilizar uma área de trabalho existente para visualização de dependências?

Sim, do Azure Migrate agora permite-lhe ligar uma área de trabalho existente para o projeto de migração e tirar partido dos mesmos para visualização de dependência. [Saiba mais](https://docs.microsoft.com/azure/migrate/concepts-dependency-visualization#how-does-it-work).

### <a name="can-i-export-the-dependency-visualization-report"></a>Pode exportar o relatório de visualização de dependências?

Não, a visualização de dependência não pode ser exportada. No entanto, uma vez que o Azure Migrate utiliza o mapa de serviço para visualização de dependências, pode utilizar o [APIs de REST de mapa de serviço](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections) para obter as dependências no formato json.

### <a name="how-can-i-automate-the-installation-of-microsoft-monitoring-agent-mma-and-dependency-agent"></a>Como posso automatizar a instalação do Microsoft Monitoring Agent (MMA) e o agente de dependência?

[Aqui](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#installation-script-examples) é um script que pode utilizar para a instalação do agente de dependência. [Aqui](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) são obter instruções sobre como instalar o MMA com a linha de comandos ou métodos automatizados. Para o MMA, também pode aproveitar um script disponível [aqui](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab) no Technet.

Além de scripts, também pode aproveitar, como o System Center Configuration Manager (SCCM), as ferramentas de implantação [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration) etc. para implantar os agentes.

### <a name="what-are-the-operating-systems-supported-by-mma"></a>Quais são os sistemas operativos suportados por MMA?

É a lista de sistemas de operativos do Windows suportados pelo MMA [aqui](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems).
É a lista de sistemas de operativos Linux suportados pelo MMA [aqui](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems).

### <a name="what-are-the-operating-systems-supported-by-dependency-agent"></a>Quais são os sistemas operativos suportados pelo agente de dependência?

A lista de sistemas de operativos do Windows suportados pelo agente de dependência é [aqui](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-windows-operating-systems).
A lista dos sistemas de operativos Linux suportados pelo agente de dependência é [aqui](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-linux-operating-systems).

### <a name="can-i-visualize-dependencies-in-azure-migrate-for-more-than-one-hour-duration"></a>Posso visualizar dependências no Azure Migrate para obter mais informações à duração de uma hora?
Não, a Azure Migrate permite que visualize as dependências de duração do até uma hora. O Azure Migrate permite-lhe voltar atrás para uma data específica na história para até o último mês, mas a duração máxima para o qual é possível visualizar as dependências é até uma hora. Por exemplo, pode usar a funcionalidade de duração de tempo do mapa de dependência, para ver as dependências de ontem, mas pode apenas visualizá-lo para uma janela de uma hora. No entanto, pode utilizar os registos do Azure Monitor para [consultar os dados de dependência](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies#query-dependency-data-from-log-analytics) ao longo de um período mais longo.

### <a name="is-dependency-visualization-supported-for-groups-with-more-than-10-vms"></a>Visualização de dependência é suportada para grupos com mais de 10 VMs?
Pode [visualize as dependências para grupos](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) que tenham até 10 VMs. Se tiver um grupo com mais de 10 VMs, recomendamos que dividir o grupo em grupos mais pequenos e visualize as dependências.


## <a name="next-steps"></a>Passos Seguintes

- Leia o [descrição geral do Azure Migrate](migrate-overview.md)
- Saiba como pode [detetar e avaliar](tutorial-assessment-vmware.md) um ambiente de VMware
