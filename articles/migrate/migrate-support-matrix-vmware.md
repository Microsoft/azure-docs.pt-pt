---
title: Suporte de avaliação do VMware nas migrações para Azure
description: Saiba mais sobre o suporte de avaliação do VMware nas migrações para Azure.
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 2a9c5504d99f439723a250b619b9f9b660ea9c59
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "76029014"
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
Deteção | A descoberta é sem agente, usando credenciais de convidado do computador e acessando remotamente computadores usando chamadas de WMI e SSH.
Computadores com suporte | VMs VMware locais.
Sistema operacional do computador | Todas as versões do Windows e Linux
Credenciais | Atualmente, o dá suporte ao uso de uma credencial para todos os servidores Windows e uma credencial para todos os servidores Linux.<br/><br/> Você cria uma conta de usuário convidado para VMs do Windows e uma conta de usuário regular/normal (acesso não sudo) para todas as VMs do Linux.
Limites | Para descoberta de aplicativos, você pode descobrir até 10000 por dispositivo. 

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

## <a name="dependency-visualization"></a>Visualização de dependência

A visualização de dependência ajuda a Visualizar dependências entre computadores que você deseja avaliar e migrar. Normalmente, você usa o mapeamento de dependência quando deseja avaliar computadores com níveis mais altos de confiança. Para VMs VMware, há suporte para a visualização de dependência da seguinte maneira:

- **Visualização de dependência sem agente**: essa opção está atualmente em visualização. Ele não exige a instalação de agentes em computadores.
    - Ele funciona capturando os dados de conexão TCP de computadores para os quais está habilitado. Depois que a descoberta de dependência é iniciada, o dispositivo reúne dados de computadores em um intervalo de sondagem de cinco minutos.
    - Os seguintes dados são coletados:
        - Ligações TCP
        - Nomes de processos que têm conexões ativas
        - Nomes de aplicativos instalados que executam os processos acima
        - Não. de conexões detectadas em cada intervalo de sondagem
- **Visualização de dependência baseada em agente**: para usar a visualização de dependência baseada em agente, você precisa baixar e instalar os agentes a seguir em cada computador local que você deseja analisar.
    - Instale o MMA (Microsoft Monitoring Agent) em cada computador. [Saiba mais](how-to-create-group-machine-dependencies.md#install-the-mma) sobre como instalar o agente do MMA.
    - Instale o Dependency Agent em cada computador. [Saiba mais](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) sobre como instalar o Dependency Agent.
    - Além disso, caso tenha computadores sem conectividade à Internet, terá de transferir e instalar o gateway do Log Analytics.


## <a name="next-steps"></a>Passos seguintes

- [Examine](best-practices-assessment.md) as práticas recomendadas para a criação de avaliações.
- [Prepare-se para a avaliação do VMware](tutorial-prepare-vmware.md) .
