---
title: Suporte de avaliação do VMware nas migrações para Azure
description: Saiba mais sobre o suporte de avaliação do VMware nas migrações para Azure.
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 1b3e81653ba934b209755391f08e60ef603e645a
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2020
ms.locfileid: "77086773"
---
# <a name="support-matrix-for-vmware-assessment"></a>Matriz de suporte para avaliação do VMware 

Este artigo resume as definições de suporte e limitações para avaliar VMware VMs com [Azure Migrate: Server Assessment](migrate-services-overview.md#azure-migrate-server-migration-tool). Se procura informações sobre VMware migratório para Azure, reveja a matriz de apoio à [migração](migrate-support-matrix-vmware-migration.md).

## <a name="overview"></a>Descrição geral

Para avaliar as máquinas locais para migração para o Azure com este artigo, você adiciona a ferramenta migrações do Azure: Server Assessment a um projeto de migrações para Azure. Implante o [aparelho Azure Migrate](migrate-appliance.md). O dispositivo descobre continuamente computadores locais e envia dados de configuração e desempenho para o Azure. Após a descoberta de máquina, você coleta computadores descobertos em grupos e executa uma avaliação de um grupo.


## <a name="limitations"></a>Limitações

**Suporte** | **Detalhes**
--- | ---
**Limites de avaliação**| Descubra e avalie até 35.000 VMs VMware num único [projeto.](migrate-support-matrix.md#azure-migrate-projects)
**Limites do projeto** | Você pode criar vários projetos em uma assinatura do Azure. Um projeto pode incluir VMs do VMware, VMs do Hyper-V e servidores físicos, até os limites de avaliação.
**Descoberta** | O dispositivo de migrações para Azure pode descobrir até 10.000 VMs VMware em um vCenter Server.
**Avaliação** | Você pode adicionar até 35.000 computadores em um único grupo.<br/><br/> Você pode avaliar até 35.000 VMs em uma única avaliação.

[Saiba mais](concepts-assessment-calculation.md) sobre avaliações.


## <a name="application-discovery"></a>Deteção de aplicações

Além de descobrir computadores, migrações para Azure: a avaliação do servidor pode descobrir aplicativos, funções e recursos em execução em computadores. Descobrir seu inventário de aplicativos permite que você identifique e planeje um caminho de migração adaptado para suas cargas de trabalho locais. 

**Suporte** | **Detalhes**
--- | ---
**Descoberta** | A descoberta é sem agente, usando credenciais de convidado do computador e acessando remotamente computadores usando chamadas de WMI e SSH.
**Máquinas suportadas** | VMs VMware locais.
**Sistema operativo de máquinas** | Todas as versões do Windows e Linux.
**credenciais vCenter** | Uma conta vCenter Server com acesso somente leitura e privilégios habilitados para máquinas virtuais > operações de convidado.
**Credenciais VM** | Atualmente, o dá suporte ao uso de uma credencial para todos os servidores Windows e uma credencial para todos os servidores Linux.<br/><br/> Você cria uma conta de usuário convidado para VMs do Windows e uma conta de usuário regular/normal (acesso não sudo) para todas as VMs do Linux.
**Ferramentas VMware** | As ferramentas do VMware devem ser instaladas e executadas em VMs que você deseja descobrir.
**Acesso portuário** | Em hosts ESXi que executam VMs que você deseja descobrir, o dispositivo de migrações para Azure deve ser capaz de se conectar à porta TCP 443.
**Limites** | Para descoberta de aplicativos, você pode descobrir até 10000 por dispositivo. 

## <a name="vmware-requirements"></a>Requisitos do VMware

**VMware** | **Detalhes**
--- | ---
**vCenter Server** | Os computadores que você deseja descobrir e avaliar devem ser gerenciados por vCenter Server versão 5,5, 6,0, 6,5 ou 6,7.
**Permissões (avaliação)** | vCenter Server conta somente leitura.
**Permissões (descoberta de apps)** | conta vCenter Server com acesso apenas para leitura, e privilégios habilitados para **máquinas virtuais > Operações de Hóspedes**.
**Permissões (visualização da dependência)** | Conta Do Servidor Central com acesso apenas para leitura, e privilégios habilitados para **máquinas virtuais** > **Operações de Hóspedes.**


## <a name="azure-migrate-appliance-requirements"></a>Requisitos de aplicação do Azure Migrate

A Azure Migrate utiliza o [aparelho Migratório Azure](migrate-appliance.md) para descoberta e avaliação. O dispositivo para VMware é implantado usando um modelo OVA, importado para o vCenter Server. 

- Conheça os [requisitos](migrate-appliance.md#appliance---vmware) do aparelho para vMware.
- Saiba mais sobre [os URLs](migrate-appliance.md#url-access) a que o aparelho precisa de aceder.

## <a name="port-access"></a>Acesso à porta

**Dispositivo** | **Conexão**
--- | ---
Baseado | Conexões de entrada na porta TCP 3389 para permitir conexões de área de trabalho remota para o dispositivo.<br/><br/> Ligações de entrada na porta 44368 para aceder remotamente à aplicação de gestão de aparelhos utilizando o URL: ```https://<appliance-ip-or-name>:44368``` <br/><br/>Ligações de saída na porta 443 (HTTPS), 5671 e 5672 (AMQP) para enviar metadados de descoberta e desempenho para o Azure Migrate.
vCenter Server | Conexões de entrada na porta TCP 443 para permitir que o dispositivo colete metadados de configuração e desempenho para avaliações. <br/><br/> O dispositivo se conecta ao vCenter na porta 443 por padrão. Se o servidor vCenter escutar em uma porta diferente, você poderá modificar a porta ao configurar a descoberta.
Anfitriões ESXi | **Necessário apenas para a descoberta de [aplicações](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#application-discovery) e visualização de [dependência sem agente](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#agentless-dependency-visualization)** <br/><br/> O aparelho liga-se aos anfitriões ESXi na porta 443 da TCP para descobrir aplicações e executar visualização de dependência sem agentes nos VMs que funcionam nos anfitriões.

## <a name="agent-based-dependency-visualization"></a>Visualização de dependência baseada em agente

[A visualização da dependência](concepts-dependency-visualization.md) ajuda-o a visualizar dependências através de máquinas que pretende avaliar e migrar. Para visualização baseada em agente, requisitos e limitações são resumidos na tabela a seguir


**Requisito** | **Detalhes**
--- | ---
**Implementação** | Antes de implementar a visualização da dependência, deverá ter um projeto Azure Migrate no local, com a ferramenta Azure Migrate: Server Assessment adicionada ao projeto. Implementa a visualização da dependência depois de instalar um aparelho Azure Migrate para descobrir as suas máquinas no local.<br/><br/> A visualização da dependência não está disponível no Governo de Azure.
**Mapa do Serviço** | A visualização da dependência baseada no agente utiliza a solução [Service Map](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) em [registos do Monitor Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview).<br/><br/> Para implantar, você associa um espaço de trabalho de Log Analytics novo ou existente a um projeto de migrações para Azure.
**Área de trabalho do Log Analytics** | O espaço de trabalho deve estar na mesma assinatura que o projeto de migrações para Azure.<br/><br/> A Azure Migrate apoia espaços de trabalho residentes nas regiões leste dos EUA, Sudeste Asiático e Europa Ocidental.<br/><br/>  O espaço de trabalho deve estar numa região em que o Mapa de [Serviços é apoiado.](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites)<br/><br/> O espaço de trabalho para um projeto Azure Migrate não pode ser modificado depois de adicionado.
**Encargos** | A solução Mapa do Serviço não incorrerá em cobranças pelos primeiros 180 dias (a partir do dia em que você associou o espaço de trabalho Log Analytics com o projeto de migrações para Azure).<br/><br/> Após 180 dias, serão aplicados os encargos padrão do Log Analytics.<br/><br/> Usar qualquer solução que não seja Mapa do Serviço no espaço de trabalho Log Analytics associado incorrerá em encargos de Log Analytics padrão.<br/><br/> Se você excluir o projeto de migrações para Azure, o espaço de trabalho não será excluído com ele. Depois de excluir o projeto, o Mapa do Serviço não é gratuito e cada nó será cobrado de acordo com a camada paga do espaço de trabalho Log Analytics.
**Agentes** | A visualização de dependência baseada em agente requer que dois agentes sejam instalados em cada computador que você deseja analisar.<br/><br/> - [o agente de monitorização da Microsoft (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)<br/><br/> [- agente de dependência.](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview#dependency-agent) 
**Ligação à Internet** | Se os computadores não estiverem conectados à Internet, você precisará instalar o Log Analytics gateway neles.


## <a name="agentless-dependency-visualization"></a>Visualização da dependência sem agente

Esta opção está atualmente em pré-visualização. [Saiba mais](how-to-create-group-machine-dependencies-agentless.md). Os requisitos são resumidos na tabela a seguir.

**Requisito** | **Detalhes**
--- | ---
**Implementação** | Antes de implementar a visualização da dependência, deverá ter um projeto Azure Migrate no local, com a ferramenta Azure Migrate: Server Assessment adicionada ao projeto. Implementa a visualização da dependência depois de instalar um aparelho Azure Migrate para descobrir as suas máquinas no local.
**Suporte vm** | Atualmente com suporte somente para VMs VMware.
**VMs do Windows** | Windows Server 2016<br/> Windows Server 2012 R2<br/> Windows Server 2012<br/> Windows Server 2008 R2 (64-bit)
**VMs do Linux** | Red Hat Enterprise Linux 7, 6, 5<br/> Ubuntu Linux 14, 4, 16, 4<br/> Debian 7, 8<br/> Oracle Linux 6, 7<br/> CentOS 5, 6, 7.
**Conta windows** |  A visualização precisa de uma conta local ou de administrador de domínio.
**Conta Linux** | A visualização precisa de uma conta de usuário com privilégio de raiz.<br/><br/> Alternadamente, a conta de utilizador necessita destas permissões em ficheiros /bin/netstat e /bin/ls: CAP_DAC_READ_SEARCH e CAP_SYS_PTRACE.
**Agentes VM** | Nenhum agente é necessário nas VMs.
**Ferramentas VMware** | As ferramentas do VMware devem ser instaladas e executadas em VMs que você deseja analisar. <br/> Se a sua versão de ferramentas VMware estiver entre 9.10 e 10.2.0, certifique-se de que atualiza para além de 10.2.0.
**credenciais vCenter** | Uma conta vCenter Server com acesso somente leitura e privilégios habilitados para máquinas virtuais > operações de convidado.
**Acesso portuário** | Em hosts ESXi que executam VMs que você deseja analisar, o dispositivo de migrações para Azure deve ser capaz de se conectar à porta TCP 443.



## <a name="next-steps"></a>Passos seguintes

- [Reveja](best-practices-assessment.md) as melhores práticas para a criação de avaliações.
- [Prepare-se para a avaliação vMware.](tutorial-prepare-vmware.md)
