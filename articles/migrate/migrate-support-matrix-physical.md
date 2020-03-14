---
title: Suporte para avaliação do servidor físico com a Migração Azure
description: Saiba mais sobre o suporte para avaliação do servidor físico com o Azure Migrate.
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: f2698d0ff046147599a8c5c791a0980a54090932
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79269526"
---
# <a name="support-matrix-for-physical-server-assessment"></a>Matriz de suporte para avaliação do servidor físico 

Pode utilizar o [serviço Azure Migrate](migrate-overview.md) para avaliar e migrar máquinas para a nuvem Microsoft Azure. Este artigo resume as definições de suporte e limitações para avaliar e migrar no local servidores físicos.


## <a name="overview"></a>Descrição geral

Para avaliar as máquinas no local para migração para Azure com este artigo, adicione a ferramenta Azure Migrate: Server Assessment a um projeto Azure Migrate. Implante o [aparelho Azure Migrate](migrate-appliance.md). O aparelho descobre continuamente as máquinas no local e envia dados de configuração e desempenho para o Azure. Após a descoberta da máquina, você reúne máquinas descobertas em grupos, e faz uma avaliação para um grupo

## <a name="limitations"></a>Limitações

**Suporte** | **Detalhes**
--- | ---
**Limites de avaliação**| Descubra e avalie até 35.000 servidores físicos num único [projeto.](migrate-support-matrix.md#azure-migrate-projects)
**Limites do projeto** | Pode criar vários projetos numa subscrição do Azure. Um projeto pode incluir VMware VMs, Hiper-V VMs e servidores físicos, até aos limites de avaliação.
**Descoberta** | O aparelho Azure Migrate pode descobrir até 250 servidores físicos.
**Avaliação** | Pode adicionar até 35.000 máquinas num único grupo.<br/><br/> Pode avaliar até 35.000 máquinas numa única avaliação.

[Saiba mais](concepts-assessment-calculation.md) sobre avaliações.




## <a name="physical-server-requirements"></a>Requisitos físicos do servidor

| **Suporte**                | **Detalhes**               
| :-------------------       | :------------------- |
| **Implementação física do servidor**       | O servidor físico pode ser autónomo ou implantado num cluster. |
| **Permissões**           | **Janelas:** Configurar uma conta de utilizador local ou de domínio em todos os servidores do Windows que pretende incluir na descoberta. A conta de utilizador precisa de ser adicionada a estes grupos-Utilizadores de Ambiente de Trabalho Remoto, Utilizadores do Monitor de Desempenho e utilizadores de Registo de Desempenho. <br/> **Linux:** Precisa de uma conta de raiz nos servidores Linux que pretende descobrir. |
| **Sistema operativo** | Todos os sistemas operativos [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) e [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) são suportados, exceto os seguintes:<br/> Windows Server 2003 <br/> SUSE Linux|


## <a name="azure-migrate-appliance-requirements"></a>Requisitos de aplicação do Azure Migrate

A Azure Migrate utiliza o [aparelho Migratório Azure](migrate-appliance.md) para descoberta e avaliação. O aparelho para servidores físicos pode funcionar num VM ou numa máquina física. Configura-o utilizando um script PowerShell que descarrega a partir do portal Azure.

- Conheça os [requisitos](migrate-appliance.md#appliance---physical) do aparelho para servidores físicos.
- Saiba mais sobre [os URLs](migrate-appliance.md#url-access) a que o aparelho precisa de aceder.

## <a name="port-access"></a>Acesso portuário

O quadro seguinte resume os requisitos do porto para avaliação.

**Dispositivo** | **Conexão**
--- | ---
**Aparelho** | Ligações de entrada na porta TCP 3389 para permitir ligações remotas ao aparelho.<br/> Ligações de entrada na porta 44368 para aceder remotamente à aplicação de gestão de aparelhos utilizando o URL: ``` https://<appliance-ip-or-name>:44368 ```<br/> Ligações de saída nas portas 443 (HTTPS), 5671 e 5672 (AMQP) para enviar metadados de descoberta e desempenho para o Azure Migrate.
**Servidores físicos** | **Janelas:** Ligações de entrada nas portas WinRM 5985 (HTTP) e 5986 (HTTPS) para retirar metadados de configuração e desempenho dos servidores do Windows. <br/> **Linux:**  Ligações de entrada na porta 22 (UDP) para puxar os metadados de configuração e desempenho dos servidores Linux. |

## <a name="agent-based-dependency-visualization"></a>Visualização da dependência baseada em agente

[A visualização da dependência](concepts-dependency-visualization.md) ajuda-o a visualizar dependências através de máquinas que pretende avaliar e migrar. Para visualização, requisitos e limitações baseados em agentes são resumidos na tabela seguinte.


**Requisito** | **Detalhes**
--- | ---
**Implementação** | Antes de implementar a visualização da dependência, deverá ter um projeto Azure Migrate no local, com a ferramenta Azure Migrate: Server Assessment adicionada ao projeto. Implementa a visualização da dependência depois de instalar um aparelho Azure Migrate para descobrir as suas máquinas no local.<br/><br/> A visualização da dependência não está disponível no Governo de Azure.
**Mapa do Serviço** | A visualização da dependência baseada no agente utiliza a solução [Service Map](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) em [registos do Monitor Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview).<br/><br/> Para implementar, associa um novo ou existente espaço de trabalho log analytics com um projeto Azure Migrate.
**Área de trabalho do Log Analytics** | O espaço de trabalho deve estar na mesma subscrição que o projeto Azure Migrate.<br/><br/> A Azure Migrate apoia espaços de trabalho residentes nas regiões leste dos EUA, Sudeste Asiático e Europa Ocidental.<br/><br/>  O espaço de trabalho deve estar numa região em que o Mapa de [Serviços é apoiado.](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites)<br/><br/> O espaço de trabalho para um projeto Azure Migrate não pode ser modificado depois de adicionado.
**Encargos** | A solução Service Map não incorre em quaisquer encargos nos primeiros 180 dias (desde o dia em que associou o espaço de trabalho log Analytics ao projeto Azure Migrate).<br/><br/> Após 180 dias, serão aplicados os encargos padrão do Log Analytics.<br/><br/> A utilização de qualquer outra solução que não o Mapa de Serviço sintetizado no espaço de trabalho associado do Log Analytics incorrerá em cargas padrão de Log Analytics.<br/><br/> Se eliminar o projeto Azure Migrate, o espaço de trabalho não é eliminado com ele. Após a exclusão do projeto, o Service Map não é gratuito e cada nó será cobrado de acordo com o nível pago de log analytics espaço de trabalho.
**Agentes** | A visualização da dependência baseada no agente requer que sejam instalados dois agentes em cada máquina que pretende analisar.<br/><br/> - [o agente de monitorização da Microsoft (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)<br/><br/> [- agente de dependência.](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview#dependency-agent) 
**Ligação à Internet** | Se as máquinas não estiverem ligadas à internet, é necessário instalar a porta de entrada log analytics.

## <a name="next-steps"></a>Passos seguintes

[Prepare-se para avaliação física do servidor](tutorial-prepare-physical.md).
