---
title: Suporte de avaliação VMware em Azure Migrate
description: Saiba mais sobre o suporte para a avaliação de VMware VM com avaliação do servidor Azure Migrate.
ms.topic: conceptual
ms.date: 11/10/2020
ms.openlocfilehash: 6e033bdf0f1492d6cbb4c41192cca8206816917d
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94444950"
---
# <a name="support-matrix-for-vmware-assessment"></a>Matriz de suporte para avaliação de VMware 

Este artigo resume os pré-requisitos e requisitos de suporte quando descobre e avalia VMware VMs para migração para Azure, utilizando a ferramenta [Azure Migrate:Server Assessment.](migrate-services-overview.md#azure-migrate-server-assessment-tool) Para avaliar vMware VMs, cria um projeto Azure Migrate e adiciona a ferramenta de Avaliação do Servidor ao projeto. Depois de a ferramenta ser adicionada, insuja o aparelho Azure Migrate. O aparelho descobre continuamente máquinas no local e envia metadados de máquinas e dados de desempenho para o Azure. Após a descoberta estar completa, recolhe-se máquinas descobertas em grupos e faz uma avaliação para um grupo. 

Se quiser migrar VMware VMs para Azure, reveja a matriz de suporte à [migração](migrate-support-matrix-vmware-migration.md).



## <a name="limitations"></a>Limitações

**Suporte** | **Detalhes**
--- | ---
**Limites do projeto** | Pode criar vários projetos numa subscrição do Azure.<br/><br/> Você pode descobrir e avaliar até 35.000 VMware VMs em um único [projeto](migrate-support-matrix.md#azure-migrate-projects). Um projeto também pode incluir servidores físicos, e Hiper-VMs, até os limites de avaliação para cada um.
**Deteção** | O aparelho Azure Migrate pode descobrir até 10.000 VMware VMs num servidor vCenter.
**Avaliação** | Pode adicionar até 35.000 máquinas num único grupo.<br/><br/> Pode avaliar até 35.000 VMs numa única avaliação.

[Saiba mais](concepts-assessment-calculation.md) sobre avaliações.


## <a name="vmware-requirements"></a>Requisitos de VMware

**VMware** | **Detalhes**
--- | ---
**vCenter Server** | As máquinas que pretende descobrir e avaliar devem ser geridas pela versão 5.5, 6.0, 6.5, 6.7 ou 7.0 do vCenter Server.<br/><br/> A descoberta de VMware VMs fornecendo detalhes do anfitrião ESXi no aparelho não é suportada.
**Permissões** | A Avaliação do Servidor necessita de uma conta de leitura do vCenter Server apenas para a descoberta e avaliação.<br/><br/> Se pretender fazer a descoberta de aplicações ou visualização de dependência, a conta necessita de privilégios para operações **de hóspedes de máquinas virtuais.**  >  **Guest Operations**

## <a name="vm-requirements"></a>Requisitos de VM
**VMware** | **Detalhes**
--- | ---
**VMs VMware** | Todos os sistemas operativos podem ser avaliados para migração. 
**Armazenamento** | Os discos ligados aos controladores SCSI, IDE e SATA são suportados.


## <a name="azure-migrate-appliance-requirements"></a>Requisitos de aplicação do Azure Migrate

A Azure Migrate utiliza o [aparelho Azure Migrate](migrate-appliance.md) para ser descoberto e avaliado. Pode implantar o aparelho como VMware VM usando um modelo OVA, importado para o vCenter Server, ou utilizando um [script PowerShell](deploy-appliance-script.md).

- Saiba mais sobre [os requisitos do aparelho](migrate-appliance.md#appliance---vmware) para o VMware.
- No Governo de Azure, deve utilizar o aparelho [utilizando o guião](deploy-appliance-script-government.md).
- Reveja os URLs a que o aparelho precisa de aceder em nuvens [públicas](migrate-appliance.md#public-cloud-urls) e [governamentais.](migrate-appliance.md#government-cloud-urls)


## <a name="port-access-requirements"></a>Requisitos de acesso portuário

**Dispositivo** | **Ligação**
--- | ---
**Aparelho** | Ligações de entrada na porta TCP 3389 para permitir ligações remotas de ambiente de trabalho ao aparelho.<br/><br/> Ligações de entrada na porta 44368 para aceder remotamente à aplicação de gestão do aparelho utilizando o URL: ```https://<appliance-ip-or-name>:44368``` <br/><br/>Ligações de saída na porta 443 (HTTPS), para enviar metadados de descoberta e desempenho para Azure Migrate.
**vCenter Server** | Ligações de entrada na porta TCP 443 para permitir que o aparelho recolha metadados de configuração e desempenho para avaliações. <br/><br/> O aparelho liga-se ao vCenter na porta 443 por defeito. Se o servidor vCenter ouvir numa porta diferente, pode modificar a porta quando configurar a descoberta.
**Anfitriões ESXi** | Se pretender fazer [a descoberta de aplicações](how-to-discover-applications.md), ou [análise de dependência sem agentes,](concepts-dependency-visualization.md#agentless-analysis)então o aparelho conecta-se aos anfitriões ESXi na porta TCP 443, para descobrir aplicações, para e executar a visualização de dependência sem agente em VMs.

## <a name="application-discovery-requirements"></a>Requisitos de descoberta de aplicações

Além de descobrir máquinas, a Avaliação do Servidor pode descobrir apps, funções e funcionalidades em execução em máquinas. Descobrir o seu inventário de aplicações permite identificar e planear um caminho de migração adaptado para as suas cargas de trabalho no local. 

**Suporte** | **Detalhes**
--- | ---
**Máquinas suportadas** | Atualmente suportado apenas para VMware VMs. Pode descobrir aplicações instaladas em até 10000 VMware VMs, de cada aparelho Azure Migrate.
**Sistemas operativos** | Suporte para VMs executando todas as versões Windows e Linux.
**Requisitos de VM** | As ferramentas VMware devem ser instaladas e em execução em VMs nas quais pretende descobrir aplicações. <br/><br/> A versão VMware tools deve ser mais tarde do que 10.2.0.<br/><br/> Os VMs devem ter a versão PowerShell 2.0 ou posteriormente instalada.
**Deteção** | As informações sobre aplicações instaladas num VM são retiradas do servidor vCenter, utilizando ferramentas VMware instaladas no VM. O aparelho recolhe as informações da aplicação a partir do servidor vCenter, utilizando APIs vSphere. A descoberta de aplicações é sem agente. Nada está instalado em VMs e o aparelho não se liga diretamente aos VMs. O WMI/SSH deve ser ativado e disponível em VMs.
**vCenter** | A conta de leitura vCenter Server utilizada para avaliação, necessita de privilégios habilitados para operações **de hóspedes de máquinas virtuais,**  >  **Guest Operations** de forma a interagir com o VM para a descoberta de aplicações.
**Acesso VM** | A descoberta de aplicações necessita de uma conta de utilizador local no VM para a descoberta de aplicações.<br/><br/> A Azure Migrate suporta atualmente o uso de uma credencial para todos os servidores windows, e uma credencial para todos os servidores Linux.<br/><br/> Cria uma conta de utilizador para vMs do Windows e uma conta de utilizador regular/normal (acesso não sudo) para todos os VMs Do Linux.
**Acesso portuário** | O aparelho Azure Migrate deve ser capaz de ligar à porta TCP 443 nos anfitriões ESXi que executam VMs nos quais pretende descobrir aplicações. O vCenter Server devolve uma ligação de anfitrião ESXI para descarregar o ficheiro que contém as informações da aplicação.



## <a name="dependency-analysis-requirements-agentless"></a>Requisitos de análise de dependência (sem agente)

[A análise da dependência](concepts-dependency-visualization.md) ajuda-o a identificar dependências entre máquinas no local que pretende avaliar e migrar para Azure. A tabela resume os requisitos para a criação de uma análise de dependência sem agentes.

**Requisito** | **Detalhes**
--- | --- 
**Máquinas suportadas** | Atualmente suportado apenas para VMware VMs.
**VMs do Windows** | Windows Server 2016<br/> Windows Server 2012 R2<br/> Windows Server 2012<br/> Windows Server 2008 R2 (64 bits).<br/>Microsoft Windows Server 2008 (32 bits). 
**VMs do Linux** | Red Hat Enterprise Linux 7, 6, 5<br/> Ubuntu Linux 14.04, 16.04<br/> Debian 7, 8<br/> Oráculo Linux 6, 7<br/> CentOS 5, 6, 7.<br/> SUSE Linux Enterprise Server 11 e mais tarde.
**Requisitos de VM** | As Ferramentas VMware (mais tarde de 10.2.0) devem ser instaladas e em funcionamento em VMs que pretende analisar.<br/><br/> Os VMs devem ter a versão PowerShell 2.0 ou posteriormente instalada.
**Método de descoberta** |  As informações de dependência entre VMs são recolhidas a partir do servidor vCenter, utilizando ferramentas VMware instaladas no VM. O aparelho recolhe as informações do servidor vCenter, utilizando APIs vSphere. A descoberta é sem agente. Nada está instalado no VM e o aparelho não se liga diretamente aos VM. O WMI/SSH deve ser ativado e disponível em VMs.
**conta vCenter** | A conta apenas de leitura utilizada pela Azure Migrate para a avaliação necessita de privilégios habilitados para **máquinas virtuais > Operações de Hóspedes.**
**Permissões do Windows VM** |  Uma conta (administração local ou domínio) com permissões de administração local em VMs.
**Conta Linux** | Conta de utilizador raiz, ou uma conta com estas permissões em /bin/netstat e /bin/ls ficheiros: CAP_DAC_READ_SEARCH e CAP_SYS_PTRACE.<br/><br/> Desardene estas capacidades utilizando os seguintes comandos: <br/><br/> sudo setcap CAP_DAC_READ_SEARCH,CAP_SYS_PTRACE=ep /bin/ls<br/><br/> sudo setcap CAP_DAC_READ_SEARCH,CAP_SYS_PTRACE=ep /bin/netstat
**Acesso portuário** | O aparelho Azure Migrate deve ser capaz de ligar à porta TCP 443 em anfitriões ESXI que executam os VM cujas dependências pretende descobrir. O vCenter Server devolve uma ligação de anfitrião ESXI para descarregar o ficheiro que contém as informações de dependência.


## <a name="dependency-analysis-requirements-agent-based"></a>Requisitos de análise de dependência (baseados em agente)

[A análise da dependência](concepts-dependency-visualization.md) ajuda-o a identificar dependências entre máquinas no local que pretende avaliar e migrar para Azure. O quadro resume os requisitos para a criação de uma análise de dependência baseada em agentes. 

**Requisito** | **Detalhes** 
--- | --- 
**Antes da implantação** | Deverá ter um projeto Azure Migrate no local, com a ferramenta Azure Migrate: Server Assessment adicionada ao projeto.<br/><br/>  Implementa visualização de dependência após a instalação de um aparelho Azure Migrate para descobrir as suas máquinas no local<br/><br/> [Aprenda](create-manage-projects.md) a criar um projeto pela primeira vez.<br/> [Saiba como](how-to-assess.md) adicionar uma ferramenta de avaliação a um projeto existente.<br/> Saiba como configurar o aparelho Azure Migrate para avaliação de [Hiper-V,](how-to-set-up-appliance-hyper-v.md) [VMware](how-to-set-up-appliance-vmware.md)ou servidores físicos.
**Máquinas suportadas** | Suportado para todas as máquinas.
**Azure Government** | A visualização da dependência não está disponível no Governo de Azure.
**Log Analytics** | A Azure Migrate utiliza a solução [de Mapa de Serviço](../azure-monitor/insights/service-map.md) nos [registos do Monitor Azure](../azure-monitor/log-query/log-query-overview.md) para visualização da dependência.<br/><br/> Associa um novo ou existente espaço de trabalho log analytics a um projeto Azure Migrate. O espaço de trabalho para um projeto Azure Migrate não pode ser modificado depois de ser adicionado. <br/><br/> O espaço de trabalho deve estar na mesma subscrição que o projeto Azure Migrate.<br/><br/> O espaço de trabalho deve residir nas regiões do Leste dos EUA, Sudeste Asiático ou Europa Ocidental. Espaços de trabalho noutras regiões não podem ser associados a um projeto.<br/><br/> O espaço de trabalho deve estar numa região em que [o Mapa de Serviços é suportado.](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions)<br/><br/> No Log Analytics, o espaço de trabalho associado ao Azure Migrate está marcado com a chave do Projeto migratório e o nome do projeto.
**Agentes necessários** | Em cada máquina que pretende analisar, instale os seguintes agentes:<br/><br/> O [agente de monitorização da Microsoft (MMA)](../azure-monitor/platform/agent-windows.md).<br/> O [agente de dependência.](../azure-monitor/platform/agents-overview.md#dependency-agent)<br/><br/> Se as máquinas no local não estiverem ligadas à internet, é necessário descarregar e instalar o Gateway Log Analytics nelas.<br/><br/> Saiba mais sobre a instalação do [agente Desadependante](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) e [MMA.](how-to-create-group-machine-dependencies.md#install-the-mma)
**Log Analytics espaço de trabalho** | O espaço de trabalho deve estar na mesma subscrição que o projeto Azure Migrate.<br/><br/> Azure Migrate apoia espaços de trabalho residentes nas regiões do Leste dos EUA, Sudeste Asiático e Europa Ocidental.<br/><br/>  O espaço de trabalho deve estar numa região em que [o Mapa de Serviços é suportado.](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions)<br/><br/> O espaço de trabalho para um projeto Azure Migrate não pode ser modificado depois de ser adicionado.
**Custos** | A solução Mapa de Serviço não incorre em quaisquer encargos nos primeiros 180 dias (a partir do dia em que associar o espaço de trabalho log Analytics ao projeto Azure Migrate)/<br/><br/> Após 180 dias, aplicar-se-ão as cobranças padrão do Log Analytics.<br/><br/> A utilização de qualquer solução que não o Mapa de Serviços no espaço de trabalho associado do Log Analytics incorrerá em [taxas padrão](https://azure.microsoft.com/pricing/details/log-analytics/) para o Log Analytics.<br/><br/> Quando o projeto Azure Migrate é eliminado, o espaço de trabalho não é apagado juntamente com ele. Após a eliminação do projeto, o uso do Mapa de Serviço não é gratuito, e cada nó será cobrado de acordo com o nível pago do espaço de trabalho Log Analytics/<br/><br/>Se tiver projetos que criou antes da disponibilidade geral da Azure Migrate (GA- 28 de fevereiro de 2018), poderá ter incorrido em taxas adicionais do Mapa de Serviços. Para garantir o pagamento após apenas 180 dias, recomendamos que crie um novo projeto, uma vez que os espaços de trabalho existentes antes da AG ainda são exejáveis.
**Gestão** | Quando regista agentes no espaço de trabalho, utiliza o ID e a chave fornecida pelo projeto Azure Migrate.<br/><br/> Pode utilizar o espaço de trabalho Log Analytics fora de Azure Migrate.<br/><br/> Se eliminar o projeto Azure Migrate associado, o espaço de trabalho não é apagado automaticamente. [Elimine-o manualmente.](../azure-monitor/platform/manage-access.md)<br/><br/> Não elimine o espaço de trabalho criado pela Azure Migrate, a menos que elimine o projeto Azure Migrate. Se o fizer, a funcionalidade de visualização de dependência não funcionará como esperado.
**Conectividade Internet** | Se as máquinas não estiverem ligadas à internet, é necessário instalar o gateway Do Log Analytics nelas.
**Azure Government** | A análise da dependência baseada em agentes não é suportada.


## <a name="next-steps"></a>Passos seguintes

- [Reveja as](best-practices-assessment.md) melhores práticas para criar avaliações.
- Prepare-se para a avaliação [da VMware.](./tutorial-discover-vmware.md)