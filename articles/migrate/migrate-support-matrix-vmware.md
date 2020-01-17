---
title: Suporte de avaliação do VMware nas migrações para Azure
description: Saiba mais sobre o suporte de avaliação do VMware nas migrações para Azure.
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 74dae71404fe827c9e19d5e3042afd2f98a7a5dd
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76154691"
---
# <a name="support-matrix-for-vmware-assessment"></a>Matriz de suporte para avaliação do VMware 

Este artigo resume as configurações de suporte e as limitações para avaliar as VMs do VMware com as [migrações para Azure: avaliação do servidor](migrate-services-overview.md#azure-migrate-server-migration-tool). Se você estiver procurando informações sobre como migrar VMs VMware para o Azure, examine a [matriz de suporte de migração](migrate-support-matrix-vmware-migration.md).

## <a name="overview"></a>Visão geral

Para avaliar as máquinas locais para migração para o Azure com este artigo, você adiciona a ferramenta migrações do Azure: Server Assessment a um projeto de migrações para Azure. Você implanta o [dispositivo migrações para Azure](migrate-appliance.md). O dispositivo descobre continuamente computadores locais e envia dados de configuração e desempenho para o Azure. Após a descoberta de máquina, você coleta computadores descobertos em grupos e executa uma avaliação de um grupo.


## <a name="limitations"></a>Limitações

**Suporte** | **Detalhes**
--- | ---
**Limites de avaliação**| Descubra e avalie até 35.000 VMs VMware em um único [projeto](migrate-support-matrix.md#azure-migrate-projects).
**Limites do projeto** | Você pode criar vários projetos em uma assinatura do Azure. Um projeto pode incluir VMs do VMware, VMs do Hyper-V e servidores físicos, até os limites de avaliação.
**Deteção** | O dispositivo de migrações para Azure pode descobrir até 10.000 VMs VMware em um vCenter Server.
**Avaliação** | Você pode adicionar até 35.000 computadores em um único grupo.<br/><br/> Você pode avaliar até 35.000 VMs em uma única avaliação.

[Saiba mais](concepts-assessment-calculation.md) sobre as avaliações.


## <a name="application-discovery"></a>Deteção de aplicações

Além de descobrir computadores, migrações para Azure: a avaliação do servidor pode descobrir aplicativos, funções e recursos em execução em computadores. Descobrir seu inventário de aplicativos permite que você identifique e planeje um caminho de migração adaptado para suas cargas de trabalho locais. 

**Suporte** | **Detalhes**
--- | ---
**Deteção** | A descoberta é sem agente, usando credenciais de convidado do computador e acessando remotamente computadores usando chamadas de WMI e SSH.
**Computadores com suporte** | VMs VMware locais.
**Sistema operacional do computador** | Todas as versões do Windows e Linux.
**credenciais do vCenter** | Uma conta vCenter Server com acesso somente leitura e privilégios habilitados para máquinas virtuais > operações de convidado.
**Credenciais da VM** | Atualmente, o dá suporte ao uso de uma credencial para todos os servidores Windows e uma credencial para todos os servidores Linux.<br/><br/> Você cria uma conta de usuário convidado para VMs do Windows e uma conta de usuário regular/normal (acesso não sudo) para todas as VMs do Linux.
**Ferramentas do VMware** | As ferramentas do VMware devem ser instaladas e executadas em VMs que você deseja descobrir.
**Acesso à porta** | Em hosts ESXi que executam VMs que você deseja descobrir, o dispositivo de migrações para Azure deve ser capaz de se conectar à porta TCP 443.
**Limites** | Para descoberta de aplicativos, você pode descobrir até 10000 por dispositivo. 

## <a name="vmware-requirements"></a>Requisitos do VMware

**VMware** | **Detalhes**
--- | ---
**vCenter Server** | Os computadores que você deseja descobrir e avaliar devem ser gerenciados por vCenter Server versão 5,5, 6,0, 6,5 ou 6,7.
**Permissões (avaliação)** | vCenter Server conta somente leitura.
**Permissões (descoberta de aplicativo)** | vCenter Server conta com acesso somente leitura e privilégios habilitados para máquinas virtuais > operações de convidado.
**Permissões (visualização de dependência)** | Conta do servidor central com acesso somente leitura e privilégios habilitados para **máquinas virtuais** > **operações de convidado**.


## <a name="azure-migrate-appliance-requirements"></a>Requisitos do dispositivo de migrações para Azure

As migrações para Azure usam o [dispositivo de migrações para Azure](migrate-appliance.md) para descoberta e avaliação. O dispositivo para VMware é implantado usando um modelo OVA, importado para o vCenter Server. 

- Saiba mais sobre [os requisitos de dispositivo](migrate-appliance.md#appliance---vmware) para VMware.
- Saiba mais sobre [URLs](migrate-appliance.md#url-access) que o dispositivo precisa acessar.

## <a name="port-access"></a>Acesso à porta

**Dispositivo** | **ligação**
--- | ---
Baseado | Conexões de entrada na porta TCP 3389 para permitir conexões de área de trabalho remota para o dispositivo.<br/><br/> Conexões de entrada na porta 44368 para acessar remotamente o aplicativo de gerenciamento de dispositivo usando a URL: ```https://<appliance-ip-or-name>:44368``` <br/><br/>Conexões de saída na porta 443, 5671 e 5672 para enviar metadados de descoberta e desempenho para migrações para Azure.
vCenter Server | Conexões de entrada na porta TCP 443 para permitir que o dispositivo colete metadados de configuração e desempenho para avaliações. <br/><br/> O dispositivo se conecta ao vCenter na porta 443 por padrão. Se o servidor vCenter escutar em uma porta diferente, você poderá modificar a porta ao configurar a descoberta.

## <a name="agent-based-dependency-visualization"></a>Visualização de dependência baseada em agente

A [visualização de dependência](concepts-dependency-visualization.md) ajuda a Visualizar dependências entre computadores que você deseja avaliar e migrar. Para visualização baseada em agente, requisitos e limitações são resumidos na tabela a seguir


**Requisito** | **Detalhes**
--- | ---
**Implementação** | Antes de implantar a visualização de dependência, você deve ter um projeto de migrações para Azure em vigor, com a ferramenta migrações para Azure: Server Assessment adicionada ao projeto. Você implanta a visualização de dependência depois de configurar um dispositivo de migrações para Azure para descobrir seus computadores locais.<br/><br/> A visualização de dependência não está disponível no Azure governamental.
**Mapa do Serviço** | A visualização de dependência baseada em agente usa a solução [mapa do serviço](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) em [logs de Azure monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview).<br/><br/> Para implantar, você associa um espaço de trabalho de Log Analytics novo ou existente a um projeto de migrações para Azure.
**Área de trabalho do Log Analytics** | O espaço de trabalho deve estar na mesma assinatura que o projeto de migrações para Azure.<br/><br/> As migrações para Azure dão suporte a espaços de trabalho que residem nas regiões leste dos EUA, Sudeste Asiático e Europa Ocidental.<br/><br/>  O espaço de trabalho deve estar em uma região na qual [mapa do serviço tem suporte](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites).<br/><br/> O espaço de trabalho para um projeto de migrações para Azure não pode ser modificado após ser adicionado.
**Cobranças** | A solução Mapa do Serviço não incorrerá em cobranças pelos primeiros 180 dias (a partir do dia em que você associou o espaço de trabalho Log Analytics com o projeto de migrações para Azure).<br/><br/> Após 180 dias, os encargos de Log Analytics padrão serão aplicados.<br/><br/> Usar qualquer solução que não seja Mapa do Serviço no espaço de trabalho Log Analytics associado incorrerá em encargos de Log Analytics padrão.<br/><br/> Se você excluir o projeto de migrações para Azure, o espaço de trabalho não será excluído com ele. Depois de excluir o projeto, o Mapa do Serviço não é gratuito e cada nó será cobrado de acordo com a camada paga do espaço de trabalho Log Analytics.
**Representantes** | A visualização de dependência baseada em agente requer que dois agentes sejam instalados em cada computador que você deseja analisar.<br/><br/> - [MMA (Microsoft Monitoring Agent)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)<br/><br/> - [agente de dependência](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview#dependency-agent). 
**Ligação à Internet** | Se os computadores não estiverem conectados à Internet, você precisará instalar o Log Analytics gateway neles.


## <a name="agentless-dependency-visualization"></a>Visualização de dependência sem agente

Esta opção está atualmente em pré-visualização. [Saiba mais](how-to-create-group-machine-dependencies-agentless.md). Os requisitos são resumidos na tabela a seguir.

**Requisito** | **Detalhes**
--- | ---
**Implementação** | Antes de implantar a visualização de dependência, você deve ter um projeto de migrações para Azure em vigor, com a ferramenta migrações para Azure: Server Assessment adicionada ao projeto. Você implanta a visualização de dependência depois de configurar um dispositivo de migrações para Azure para descobrir seus computadores locais.
**Suporte de VM** | Atualmente com suporte somente para VMs VMware.
**VMs do Windows** | Windows Server 2016<br/> Windows Server 2012 R2<br/> Windows Server 2012<br/> Windows Server 2008 R2 (64 bits)
**VMs do Linux** | Red Hat Enterprise Linux 7, 6, 5<br/> Ubuntu Linux 14, 4, 16, 4<br/> Debian 7, 8<br/> Oracle Linux 6, 7<br/> CentOS 5, 6, 7.
**Conta do Windows** |  A visualização precisa de uma conta de usuário com acesso de convidado.
**Conta do Linux** | A visualização precisa de uma conta de usuário com privilégio de raiz.<br/><br/> Como alternativa, a conta de usuário precisa dessas permissões em arquivos/bin/netstat e/bin/ls: CAP_DAC_READ_SEARCH e CAP_SYS_PTRACE.
**Agentes de VM** | Nenhum agente é necessário nas VMs.
**Ferramentas do VMware** | As ferramentas do VMware devem ser instaladas e executadas em VMs que você deseja analisar.
**credenciais do vCenter** | Uma conta vCenter Server com acesso somente leitura e privilégios habilitados para máquinas virtuais > operações de convidado.
**Acesso à porta** | Em hosts ESXi que executam VMs que você deseja analisar, o dispositivo de migrações para Azure deve ser capaz de se conectar à porta TCP 443.



## <a name="next-steps"></a>Passos seguintes

- [Examine](best-practices-assessment.md) as práticas recomendadas para a criação de avaliações.
- [Prepare-se para a avaliação do VMware](tutorial-prepare-vmware.md) .
