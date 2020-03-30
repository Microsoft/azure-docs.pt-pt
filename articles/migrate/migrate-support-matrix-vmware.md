---
title: Suporte de avaliação vmware em Azure Migrate
description: Saiba mais sobre o suporte para avaliação vmware VM com avaliação do servidor de migração Azure.
ms.topic: conceptual
ms.date: 03/23/2020
ms.openlocfilehash: 03d07adb6f19346901286bdae148f95e68290e4e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336883"
---
# <a name="support-matrix-for-vmware-assessment"></a>Matriz de suporte para avaliação vmware 

Este artigo resume os requisitos pré-requisitos e requisitos de apoio para a avaliação dos VMware VMem em preparação para a migração para O. Se quiser migrar VMware VMs para Azure, reveja a matriz de apoio à [migração](migrate-support-matrix-vmware-migration.md).

Avalia os servidores físicos com a ferramenta de avaliação do [servidor Azure Migrate:Server.](migrate-services-overview.md#azure-migrate-server-assessment-tool) Cria-se um projeto Azure Migrate e, em seguida, adiciona-se a ferramenta ao projeto. Depois de adicionada a ferramenta, implante o [aparelho Migratório Azure](migrate-appliance.md). O aparelho descobre continuamente as máquinas no local e envia metadados de máquinas e dados de desempenho para o Azure. Após a descoberta da máquina, você reúne máquinas descobertas em grupos, e faz uma avaliação para um grupo.

## <a name="limitations"></a>Limitações

**Suporte** | **Detalhes**
--- | ---
**Limites do projeto** | Pode criar vários projetos numa subscrição do Azure.<br/><br/> Você pode descobrir e avaliar até 35.000 VMware VMs em um único [projeto](migrate-support-matrix.md#azure-migrate-projects). Um projeto pode incluir VMware VMs, servidores físicos e VMs Hiper-V, até os limites de avaliação para cada um.
**Descoberta** | O aparelho Azure Migrate pode descobrir até 10.000 VMware VMware vMware num servidor vCenter.
**Avaliação** | Pode adicionar até 35.000 máquinas num único grupo.<br/><br/> Você pode avaliar até 35.000 VMs numa única avaliação.

[Saiba mais](concepts-assessment-calculation.md) sobre avaliações.


## <a name="application-discovery"></a>Deteção de aplicações

Além de descobrir máquinas, o Azure Migrate: Server Assessment pode descobrir aplicações, papéis e funcionalidades em máquinas. Descobrir o inventário da sua aplicação permite identificar e planear um caminho de migração adaptado para as suas cargas de trabalho no local. 

**Suporte** | **Detalhes**
--- | ---
**Máquinas suportadas** | A tualmente, a descoberta de aplicações é suportada apenas para VMs no local.
**Descoberta** | A descoberta de aplicativos é sem agente. Utiliza credenciais de hóspedes de máquinas e acede remotamente a máquinas utilizando chamadas WMI e SSH.
**Suporte vm** | A descoberta de aplicativos é suportada para todas as versões Windows e Linux.
**credenciais vCenter** | A descoberta de aplicações necessita de uma conta vCenter Server com acesso apenas para leitura, e privilégios habilitados para Máquinas Virtuais > Operações de Hóspedes.
**Credenciais VM** | Atualmente, a descoberta da aplicação suporta o uso de uma credencial para todos os servidores do Windows, e uma credencial para todos os servidores Linux.<br/><br/> Cria uma conta de utilizador convidado para VMs do Windows e uma conta de utilizador regular/normal (acesso não sudo) para todos os VMs Linux.
**Ferramentas VMware** | As ferramentas VMware devem ser instaladas e em funcionamento em VMs que pretende descobrir. <br/> A versão das ferramentas VMware deve ser superior a 10.2.0.
**PowerShell** | Os VMs devem ter a versão PowerShell 2.0 ou posteriormente instalada.
**Acesso portuário** | Nos anfitriões ESXi que executam VMs que pretende descobrir, o aparelho Azure Migrate deve poder ligar-se à porta 443 do TCP.
**Limites** | Para a descoberta de aplicações, pode descobrir até 10000 VMs em cada aparelho Azure Migrate.



## <a name="vmware-requirements"></a>Requisitos vMware

**VMware** | **Detalhes**
--- | ---
**VMs VMware** | A avaliação é suportada para todos os sistemas operativos Windows e Linux.
**vCenter Server** | As máquinas que pretende descobrir e avaliar devem ser geridas pela versão vCenter Server 5.5, 6.0, 6.5 ou 6.7.
**Permissões (avaliação)** | conta de leitura vCenter Server.
**Permissões (descoberta de apps)** | conta vCenter Server com acesso apenas para leitura, e privilégios habilitados para **máquinas virtuais > Operações de Hóspedes**.
**Permissões (visualização da dependência)** | Conta Do Servidor Central com acesso apenas para leitura, e privilégios habilitados para > **operações de hóspedes**de **máquinas virtuais.**


## <a name="azure-migrate-appliance-requirements"></a>Requisitos de aplicação do Azure Migrate

A Azure Migrate utiliza o [aparelho Migratório Azure](migrate-appliance.md) para descoberta e avaliação. O aparelho para VMware é implantado com um modelo OVA, importado para o VCenter Server. 

- Conheça os [requisitos](migrate-appliance.md#appliance---vmware) do aparelho para vMware.
- Saiba mais sobre [os URLs](migrate-appliance.md#url-access) a que o aparelho precisa de aceder.

## <a name="port-access"></a>Acesso portuário

**Dispositivo** | **Conexão**
--- | ---
Aparelho | Ligações de entrada na porta TCP 3389 para permitir ligações remotas ao aparelho.<br/><br/> Ligações de entrada na porta 44368 para aceder remotamente à aplicação de gestão de aparelhos utilizando o URL:```https://<appliance-ip-or-name>:44368``` <br/><br/>Ligações de saída na porta 443 (HTTPS), para enviar metadados de descoberta e desempenho para o Azure Migrate.
vCenter Server | Ligações de entrada na porta TCP 443 para permitir que o aparelho recolha metadados de configuração e desempenho para avaliações. <br/><br/> O aparelho liga-se ao vCenter na porta 443 por defeito. Se o servidor vCenter ouvir uma porta diferente, pode modificar a porta quando configurar a descoberta.
Anfitriões ESXi (análise de dependência sem agente/descoberta de aplicativos) | Se quiser fazer a descoberta de [apps](how-to-discover-applications.md) ou a análise da [dependência sem agente,](concepts-dependency-visualization.md#agentless-analysis)então o aparelho liga-se aos anfitriões ESXi na porta 443 da TCP, para descobrir aplicações, para e executar visualização de dependência sem agente em VMs.

## <a name="agentless-dependency-analysis-requirements"></a>Requisitos de análise de dependência sem agente

[A análise da dependência](concepts-dependency-visualization.md) ajuda-o a identificar dependências entre máquinas no local que pretende avaliar e migrar para Azure. A tabela resume os requisitos para a criação de uma análise de dependência sem agente. 

**Requisito** | **Detalhes**
--- | --- 
**Antes da implantação** | Deverá ter um projeto Azure Migrate em vigor, com a ferramenta Azure Migrate: Server Assessment adicionada ao projeto.<br/><br/>  Implementa a visualização da dependência depois de configurar um aparelho Azure Migrate para descobrir as suas máquinas VMWare no local.<br/><br/> [Aprenda](create-manage-projects.md) a criar um projeto pela primeira vez.<br/> [Aprenda](how-to-assess.md) a adicionar uma ferramenta de avaliação a um projeto existente.<br/> [Aprenda](how-to-set-up-appliance-vmware.md) a configurar o aparelho Azure Migrate para avaliação de VMware VMs.
**Suporte vm** | Atualmente suportado apenas para VMware VMs.
**VMs do Windows** | Windows Server 2016<br/> Windows Server 2012 R2<br/> Windows Server 2012<br/> Windows Server 2008 R2 (64-bit).
**Conta do Windows** |  Para análise de dependência, o aparelho Azure Migrate necessita de uma conta local ou de administrador de domínio para aceder a VMs windows.
**VMs do Linux** | Red Hat Enterprise Linux 7, 6, 5<br/> Ubuntu Linux 14.04, 16.04<br/> Debiano 7, 8<br/> Oracle Linux 6, 7<br/> Centos 5, 6, 7.
**Conta Linux** | Para análise de dependência, nas máquinas Linux o aparelho Azure Migrate precisa de uma conta de utilizador com privilégio raiz.<br/><br/> Alternadamente, a conta de utilizador necessita destas permissões em ficheiros /bin/netstat e /bin/ls: CAP_DAC_READ_SEARCH e CAP_SYS_PTRACE.
**Agentes necessários** | Não é necessário um agente nas máquinas que queira analisar.
**Ferramentas VMware** | As Ferramentas VMware (mais tardar 10.2) devem ser instaladas e em funcionamento em cada VM que pretende analisar.
**credenciais vCenter Server** | A visualização da dependência necessita de uma conta vCenter Server com acesso apenas para leitura, e privilégios habilitados para Máquinas Virtuais > Operações de Hóspedes. 
**PowerShell** | Os VMs devem ter a versão PowerShell 2.0 ou superior instalada.
**Acesso portuário** | Nos anfitriões ESXi que executam VMs que pretende analisar, o aparelho Azure Migrate deve poder ligar-se à porta 443 do TCP.

## <a name="agent-based-dependency-analysis-requirements"></a>Requisitos de análise da dependência baseados em agentes

[A análise da dependência](concepts-dependency-visualization.md) ajuda-o a identificar dependências entre máquinas no local que pretende avaliar e migrar para Azure. A tabela resume os requisitos para a criação de uma análise de dependência baseada no agente. 

**Requisito** | **Detalhes** 
--- | --- 
**Antes da implantação** | Deverá ter um projeto Azure Migrate em vigor, com a ferramenta Azure Migrate: Server Assessment adicionada ao projeto.<br/><br/>  Implementa a visualização da dependência depois de criar um aparelho Azure Migrate para descobrir as suas máquinas no local<br/><br/> [Aprenda](create-manage-projects.md) a criar um projeto pela primeira vez.<br/> [Aprenda](how-to-assess.md) a adicionar uma ferramenta de avaliação a um projeto existente.<br/> Aprenda a configurar o aparelho Azure Migrate para avaliação de [Hiper-V,](how-to-set-up-appliance-hyper-v.md) [VMware](how-to-set-up-appliance-vmware.md)ou servidores físicos.
**Azure Government** | A visualização da dependência não está disponível no Governo de Azure.
**Log Analytics** | A Azure Migrate utiliza a solução [Service Map](../operations-management-suite/operations-management-suite-service-map.md) em [registos do Monitor Azure](../log-analytics/log-analytics-overview.md) para visualização da dependência.<br/><br/> Associa um novo ou existente espaço de trabalho log analytics a um projeto Azure Migrate. O espaço de trabalho para um projeto Azure Migrate não pode ser modificado depois de adicionado. <br/><br/> O espaço de trabalho deve estar na mesma subscrição que o projeto Azure Migrate.<br/><br/> O espaço de trabalho deve residir nas regiões leste dos EUA, sudeste asiático ou na Europa Ocidental. Espaços de trabalho noutras regiões não podem ser associados a um projeto.<br/><br/> O espaço de trabalho deve estar numa região em que o Mapa de [Serviços é apoiado.](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites)<br/><br/> No Log Analytics, o espaço de trabalho associado ao Azure Migrate está marcado com a chave do Projeto migração e o nome do projeto.
**Agentes necessários** | Em cada máquina que pretende analisar, instale os seguintes agentes:<br/><br/> O agente de monitorização da [Microsoft (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).<br/> O [agente da Dependência.](../azure-monitor/platform/agents-overview.md#dependency-agent)<br/><br/> Se as máquinas no local não estiverem ligadas à internet, é necessário descarregar e instalar o gateway Log Analytics.<br/><br/> Saiba mais sobre a instalação do [agente dependency](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) e [do MMA](how-to-create-group-machine-dependencies.md#install-the-mma).
**Área de trabalho do Log Analytics** | O espaço de trabalho deve estar na mesma subscrição que o projeto Azure Migrate.<br/><br/> A Azure Migrate apoia espaços de trabalho residentes nas regiões leste dos EUA, Sudeste Asiático e Europa Ocidental.<br/><br/>  O espaço de trabalho deve estar numa região em que o Mapa de [Serviços é apoiado.](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites)<br/><br/> O espaço de trabalho para um projeto Azure Migrate não pode ser modificado depois de adicionado.
**Custos** | A solução Service Map não incorre em quaisquer encargos nos primeiros 180 dias (a partir do dia em que associa o espaço de trabalho log Analytics ao projeto Azure Migrate)/<br/><br/> Após 180 dias, aplicar-se-ão as cobranças padrão do Log Analytics.<br/><br/> A utilização de qualquer outra solução que não o Mapa de Serviço sintetizado no espaço de trabalho associado do Log Analytics incorrerá em [encargos padrão](https://azure.microsoft.com/pricing/details/log-analytics/) para o Log Analytics.<br/><br/> Quando o projeto Azure Migrate é eliminado, o espaço de trabalho não é eliminado juntamente com ele. Após a exclusão do projeto, o uso do Mapa de Serviço sem graça, e cada nó será cobrado de acordo com o nível pago do espaço de trabalho Log Analytics/<br/><br/>Se tiver projetos que criou antes da disponibilidade geral da Azure Migrate (GA- 28 de fevereiro de 2018), poderá ter incorrido em custos adicionais do Mapa de Serviços. Para garantir o pagamento após 180 dias, recomendamos que crie um novo projeto, uma vez que os espaços de trabalho existentes antes da GA ainda são responsáveis.
**Gestão** | Quando regista agentes no espaço de trabalho, utiliza a identificação e a chave fornecidas pelo projeto Azure Migrate.<br/><br/> Pode utilizar o espaço de trabalho log Analytics fora do Azure Migrate.<br/><br/> Se eliminar o projeto azure migrate associado, o espaço de trabalho não é eliminado automaticamente. [Elimine-o manualmente](../azure-monitor/platform/manage-access.md).<br/><br/> Não elimine o espaço de trabalho criado pela Azure Migrate, a menos que apague o projeto Azure Migrate. Se o fizer, a funcionalidade de visualização da dependência não funcionará como esperado.
**Conectividade Internet** | Se as máquinas não estiverem ligadas à internet, é necessário instalar a porta de entrada log analytics.


## <a name="next-steps"></a>Passos seguintes

- [Reveja](best-practices-assessment.md) as melhores práticas para a criação de avaliações.
- [Prepare-se para a avaliação vMware.](tutorial-prepare-vmware.md)
