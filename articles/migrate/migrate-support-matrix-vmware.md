---
title: Suporte de avaliação vmware em Azure Migrate
description: Saiba mais sobre o suporte de avaliação vMware em Azure Migrate.
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 19ed506228bac425ad05edee1586740e6c33f69e
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78362187"
---
# <a name="support-matrix-for-vmware-assessment"></a>Matriz de suporte para avaliação vmware 

Este artigo resume as definições de suporte e limitações para avaliar VMware VMs com [Azure Migrate: Server Assessment](migrate-services-overview.md#azure-migrate-server-migration-tool). Se procura informações sobre VMware migratório para Azure, reveja a matriz de apoio à [migração](migrate-support-matrix-vmware-migration.md).

## <a name="overview"></a>Descrição geral

Para avaliar as máquinas no local para migração para Azure com este artigo, adicione a ferramenta Azure Migrate: Server Assessment a um projeto Azure Migrate. Implante o [aparelho Azure Migrate](migrate-appliance.md). O aparelho descobre continuamente as máquinas no local e envia dados de configuração e desempenho para o Azure. Após a descoberta da máquina, você reúne máquinas descobertas em grupos, e faz uma avaliação para um grupo.


## <a name="limitations"></a>Limitações

**Suporte** | **Detalhes**
--- | ---
**Limites de avaliação**| Descubra e avalie até 35.000 VMs VMware num único [projeto.](migrate-support-matrix.md#azure-migrate-projects)
**Limites do projeto** | Pode criar vários projetos numa subscrição do Azure. Um projeto pode incluir VMware VMs, Hiper-V VMs e servidores físicos, até aos limites de avaliação.
**Descoberta** | O aparelho Azure Migrate pode descobrir até 10.000 VMware VMware vMware num servidor vCenter.
**Avaliação** | Pode adicionar até 35.000 máquinas num único grupo.<br/><br/> Você pode avaliar até 35.000 VMs numa única avaliação.

[Saiba mais](concepts-assessment-calculation.md) sobre avaliações.


## <a name="application-discovery"></a>Deteção de aplicações

Além de descobrir máquinas, o Azure Migrate: Server Assessment pode descobrir aplicações, papéis e funcionalidades em máquinas. Descobrir o inventário da sua aplicação permite identificar e planear um caminho de migração adaptado para as suas cargas de trabalho no local. 

**Suporte** | **Detalhes**
--- | ---
**Descoberta** | A Discovery é sem agente, usando credenciais de hóspedes de máquinas e acedendo remotamente a máquinas usando chamadas WMI e SSH.
**Máquinas suportadas** | VMs no local.
**Sistema operativo de máquinas** | Todas as versões Windows e Linux.
**credenciais vCenter** | Uma conta vCenter Server com acesso apenas para leitura, e privilégios habilitados para Máquinas Virtuais > Operações de Hóspedes.
**Credenciais VM** | Atualmente suporta a utilização de uma credencial para todos os servidores Windows, e uma credencial para todos os servidores Linux.<br/><br/> Cria uma conta de utilizador convidado para VMs do Windows e uma conta de utilizador regular/normal (acesso não sudo) para todos os VMs Linux.
**Ferramentas VMware** | As ferramentas VMware devem ser instaladas e em funcionamento em VMs que pretende descobrir. <br/> Se a sua versão de ferramentas VMware estiver entre 9.10 e 10.2.0, certifique-se de que atualiza para além de 10.2.0.
**Acesso portuário** | Nos anfitriões ESXi que executam VMs que pretende descobrir, o aparelho Azure Migrate deve poder ligar-se à porta 443 do TCP.
**Limites** | Para a descoberta de aplicativos pode descobrir até 10000 por aparelho. 

## <a name="vmware-requirements"></a>Requisitos vMware

**VMware** | **Detalhes**
--- | ---
**vCenter Server** | As máquinas que pretende descobrir e avaliar devem ser geridas pela versão vCenter Server 5.5, 6.0, 6.5 ou 6.7.
**Permissões (avaliação)** | conta de leitura vCenter Server.
**Permissões (descoberta de apps)** | conta vCenter Server com acesso apenas para leitura, e privilégios habilitados para **máquinas virtuais > Operações de Hóspedes**.
**Permissões (visualização da dependência)** | Conta Do Servidor Central com acesso apenas para leitura, e privilégios habilitados para **máquinas virtuais** > **Operações de Hóspedes.**


## <a name="azure-migrate-appliance-requirements"></a>Requisitos de aplicação do Azure Migrate

A Azure Migrate utiliza o [aparelho Migratório Azure](migrate-appliance.md) para descoberta e avaliação. O aparelho para VMware é implantado com um modelo OVA, importado para o VCenter Server. 

- Conheça os [requisitos](migrate-appliance.md#appliance---vmware) do aparelho para vMware.
- Saiba mais sobre [os URLs](migrate-appliance.md#url-access) a que o aparelho precisa de aceder.

## <a name="port-access"></a>Acesso portuário

**Dispositivo** | **Conexão**
--- | ---
Aparelho | Ligações de entrada na porta TCP 3389 para permitir ligações remotas ao aparelho.<br/><br/> Ligações de entrada na porta 44368 para aceder remotamente à aplicação de gestão de aparelhos utilizando o URL: ```https://<appliance-ip-or-name>:44368``` <br/><br/>Ligações de saída na porta 443 (HTTPS), 5671 e 5672 (AMQP) para enviar metadados de descoberta e desempenho para o Azure Migrate.
vCenter Server | Ligações de entrada na porta TCP 443 para permitir que o aparelho recolha metadados de configuração e desempenho para avaliações. <br/><br/> O aparelho liga-se ao vCenter na porta 443 por defeito. Se o servidor vCenter ouvir uma porta diferente, pode modificar a porta quando configurar a descoberta.
Anfitriões ESXi | **Necessário apenas para a descoberta de [aplicações](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#application-discovery) e visualização de [dependência sem agente](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#agentless-dependency-visualization)** <br/><br/> O aparelho liga-se aos anfitriões ESXi na porta 443 da TCP para descobrir aplicações e executar visualização de dependência sem agentes nos VMs que funcionam nos anfitriões.

## <a name="agent-based-dependency-visualization"></a>Visualização da dependência baseada em agente

[A visualização da dependência](concepts-dependency-visualization.md) ajuda-o a visualizar dependências através de máquinas que pretende avaliar e migrar. Para visualização, requisitos e limitações baseados em agentes são resumidos na tabela seguinte


**Requisito** | **Detalhes**
--- | ---
**Implementação** | Antes de implementar a visualização da dependência, deverá ter um projeto Azure Migrate no local, com a ferramenta Azure Migrate: Server Assessment adicionada ao projeto. Implementa a visualização da dependência depois de instalar um aparelho Azure Migrate para descobrir as suas máquinas no local.<br/><br/> A visualização da dependência não está disponível no Governo de Azure.
**Mapa do Serviço** | A visualização da dependência baseada no agente utiliza a solução [Service Map](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) em [registos do Monitor Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview).<br/><br/> Para implementar, associa um novo ou existente espaço de trabalho log analytics com um projeto Azure Migrate.
**Área de trabalho do Log Analytics** | O espaço de trabalho deve estar na mesma subscrição que o projeto Azure Migrate.<br/><br/> A Azure Migrate apoia espaços de trabalho residentes nas regiões leste dos EUA, Sudeste Asiático e Europa Ocidental.<br/><br/>  O espaço de trabalho deve estar numa região em que o Mapa de [Serviços é apoiado.](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites)<br/><br/> O espaço de trabalho para um projeto Azure Migrate não pode ser modificado depois de adicionado.
**Encargos** | A solução Service Map não incorre em quaisquer encargos nos primeiros 180 dias (desde o dia em que associou o espaço de trabalho log Analytics ao projeto Azure Migrate).<br/><br/> Após 180 dias, serão aplicados os encargos padrão do Log Analytics.<br/><br/> A utilização de qualquer outra solução que não o Mapa de Serviço sintetizado no espaço de trabalho associado do Log Analytics incorrerá em cargas padrão de Log Analytics.<br/><br/> Se eliminar o projeto Azure Migrate, o espaço de trabalho não é eliminado com ele. Após a exclusão do projeto, o Service Map não é gratuito e cada nó será cobrado de acordo com o nível pago de log analytics espaço de trabalho.
**Agentes** | A visualização da dependência baseada no agente requer que sejam instalados dois agentes em cada máquina que pretende analisar.<br/><br/> - [o agente de monitorização da Microsoft (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)<br/><br/> [- agente de dependência.](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview#dependency-agent) 
**Ligação à Internet** | Se as máquinas não estiverem ligadas à internet, é necessário instalar a porta de entrada log analytics.


## <a name="agentless-dependency-visualization"></a>Visualização da dependência sem agente

Esta opção está atualmente em pré-visualização. [Saiba mais](how-to-create-group-machine-dependencies-agentless.md). Os requisitos são resumidos na tabela seguinte.

**Requisito** | **Detalhes**
--- | ---
**Implementação** | Antes de implementar a visualização da dependência, deverá ter um projeto Azure Migrate no local, com a ferramenta Azure Migrate: Server Assessment adicionada ao projeto. Implementa a visualização da dependência depois de instalar um aparelho Azure Migrate para descobrir as suas máquinas no local.
**Suporte vm** | Atualmente suportado apenas para VMware VMs.
**VMs do Windows** | Windows Server 2016<br/> Windows Server 2012 R2<br/> Windows Server 2012<br/> Windows Server 2008 R2 (64-bit)
**VMs do Linux** | Red Hat Enterprise Linux 7, 6, 5<br/> Ubuntu Linux 14.04, 16.04<br/> Debian 7, 8<br/> Oracle Linux 6, 7<br/> Centos 5, 6, 7.
**Conta windows** |  A visualização precisa de uma conta local ou de administrador de domínio.
**Conta Linux** | A visualização precisa de uma conta de utilizador com privilégio raiz.<br/><br/> Alternadamente, a conta de utilizador necessita destas permissões em ficheiros /bin/netstat e /bin/ls: CAP_DAC_READ_SEARCH e CAP_SYS_PTRACE.
**Agentes VM** | Não é necessário um agente nos VMs.
**Ferramentas VMware** | As ferramentas VMware devem ser instaladas e em funcionamento em VMs que pretende analisar. <br/> Se a sua versão de ferramentas VMware estiver entre 9.10 e 10.2.0, certifique-se de que atualiza para além de 10.2.0.
**credenciais vCenter** | Uma conta vCenter Server com acesso apenas para leitura, e privilégios habilitados para Máquinas Virtuais > Operações de Hóspedes.
**Acesso portuário** | Nos anfitriões ESXi que executam VMs que pretende analisar, o aparelho Azure Migrate deve poder ligar-se à porta 443 do TCP.



## <a name="next-steps"></a>Passos seguintes

- [Reveja](best-practices-assessment.md) as melhores práticas para a criação de avaliações.
- [Prepare-se para a avaliação vMware.](tutorial-prepare-vmware.md)
