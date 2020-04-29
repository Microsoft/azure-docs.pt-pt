---
title: Suporte para avaliação de servidor físico em Azure Migrate
description: Conheça o suporte para avaliação do servidor físico com a Avaliação do Servidor Migratório Azure
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: ae76a6b570ec58e71a8a1728a2a601728030f58c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81538159"
---
# <a name="support-matrix-for-physical-server-assessment"></a>Matriz de suporte para avaliação do servidor físico 

Este artigo resume os requisitos pré-requisitos e requisitos de suporte quando avalia os servidores físicos para migração para O Azure, utilizando a ferramenta de avaliação do [servidor Azure Migrate:Server.](migrate-services-overview.md#azure-migrate-server-assessment-tool) Se quiser migrar servidores físicos para o Azure, reveja a matriz de suporte à [migração](migrate-support-matrix-physical-migration.md).


Para avaliar servidores físicos, cria um projeto Azure Migrate e adiciona a ferramenta de Avaliação do Servidor ao projeto. Depois de adicionada a ferramenta, implante o [aparelho Migratório Azure](migrate-appliance.md). O aparelho descobre continuamente as máquinas no local e envia metadados de máquinas e dados de desempenho para o Azure. Após a descoberta estar concluída, você reúne máquinas descobertas em grupos, e executa uma avaliação para um grupo.


## <a name="limitations"></a>Limitações

**Suporte** | **Detalhes**
--- | ---
**Limites de avaliação** | Você pode descobrir e avaliar até 35.000 servidores físicos em um único [projeto Azure Migrate](migrate-support-matrix.md#azure-migrate-projects).
**Limites do projeto** | Pode criar vários projetos numa subscrição do Azure. Além de servidores físicos, um projeto pode incluir VMware VMs e VMs Hiper-V, até os limites de avaliação para cada um.
**Descoberta** | O aparelho Azure Migrate pode descobrir até 250 servidores físicos.
**Avaliação** | Pode adicionar até 35.000 máquinas num único grupo.<br/><br/> Pode avaliar até 35.000 máquinas numa única avaliação.

[Saiba mais](concepts-assessment-calculation.md) sobre avaliações.

## <a name="physical-server-requirements"></a>Requisitos do servidor físico

| **Suporte**                | **Detalhes**               
| :-------------------       | :------------------- |
| **Implementação física do servidor**       | O servidor físico pode ser autónomo ou implantado num cluster. |
| **Permissões**           | **Janelas:** Precisa de uma conta de utilizador local ou de domínio em todos os servidores do Windows que pretende descobrir. A conta de utilizador deve ser adicionada a estes grupos: Utilizadores de desktop remoto, utilizadores do Monitor de Desempenho e utilizadores de Registo de Desempenho. <br/><br/> **Linux:** Precisa de uma conta de raiz nos servidores Linux que pretende descobrir. |
| **Sistema Operativo** | Todos os sistemas operativos [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) e [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) que são suportados pelo Azure, com exceção do Windows Server 2003, e do SUSE Linux.|


## <a name="azure-migrate-appliance-requirements"></a>Requisitos de aplicação do Azure Migrate

A Azure Migrate utiliza o [aparelho Migratório Azure](migrate-appliance.md) para descoberta e avaliação. O aparelho para servidores físicos pode funcionar num VM ou numa máquina física. Configura o aparelho utilizando um script PowerShell que descarrega a partir do portal Azure.

- Conheça os [requisitos](migrate-appliance.md#appliance---physical) do aparelho para servidores físicos.
- Saiba mais sobre os URLs que o aparelho precisa de aceder em nuvens [públicas](migrate-appliance.md#public-cloud-urls) e [governamentais.](migrate-appliance.md#government-cloud-urls)

## <a name="port-access"></a>Acesso portuário

O quadro seguinte resume os requisitos do porto para avaliação.

**Dispositivo** | **Conexão**
--- | ---
**Aparelho** | Ligações de entrada na porta TCP 3389, para permitir ligações remotas ao aparelho.<br/><br/> Ligações de entrada na porta 44368, para aceder remotamente à aplicação de gestão de aparelhos utilizando o URL:``` https://<appliance-ip-or-name>:44368 ```<br/><br/> Ligações de saída nas portas 443 (HTTPS), para enviar metadados de descoberta e desempenho para o Azure Migrate.
**Servidores físicos** | **Janelas:** Ligações de entrada nas portas WinRM 5985 (HTTP) e 5986 (HTTPS), para retirar metadados de configuração e desempenho dos servidores do Windows. <br/><br/> **Linux:**  Ligações de entrada na porta 22 (UDP), para puxar a configuração e os metadados de desempenho dos servidores Linux. |

## <a name="agent-based-dependency-analysis-requirements"></a>Requisitos de análise da dependência baseados em agentes

[A análise da dependência](concepts-dependency-visualization.md) ajuda-o a identificar dependências entre máquinas no local que pretende avaliar e migrar para Azure. A tabela resume os requisitos para a criação de uma análise de dependência baseada no agente. Atualmente, apenas a análise de dependência baseada em agentes é suportada para servidores físicos.

**Requisito** | **Detalhes** 
--- | --- 
**Antes da implantação** | Deverá ter um projeto Azure Migrate em vigor, com a ferramenta de Avaliação do Servidor adicionada ao projeto.<br/><br/>  Implementa a visualização da dependência depois de criar um aparelho Azure Migrate para descobrir as suas máquinas no local<br/><br/> [Aprenda](create-manage-projects.md) a criar um projeto pela primeira vez.<br/> [Aprenda](how-to-assess.md) a adicionar uma ferramenta de avaliação a um projeto existente.<br/> Aprenda a configurar o aparelho Azure Migrate para avaliação de [Hiper-V,](how-to-set-up-appliance-hyper-v.md) [VMware](how-to-set-up-appliance-vmware.md)ou servidores físicos.
**Azure Government** | A visualização da dependência não está disponível no Governo de Azure.
**Log Analytics** | A Azure Migrate utiliza a solução [Service Map](../operations-management-suite/operations-management-suite-service-map.md) em [registos do Monitor Azure](../log-analytics/log-analytics-overview.md) para visualização da dependência.<br/><br/> Associa um novo ou existente espaço de trabalho log analytics a um projeto Azure Migrate. O espaço de trabalho para um projeto Azure Migrate não pode ser modificado depois de adicionado. <br/><br/> O espaço de trabalho deve estar na mesma subscrição que o projeto Azure Migrate.<br/><br/> O espaço de trabalho deve residir nas regiões leste dos EUA, sudeste asiático ou na Europa Ocidental. Espaços de trabalho noutras regiões não podem ser associados a um projeto.<br/><br/> O espaço de trabalho deve estar numa região em que o Mapa de [Serviços é apoiado.](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites)<br/><br/> No Log Analytics, o espaço de trabalho associado ao Azure Migrate está marcado com a chave do Projeto migração e o nome do projeto.
**Agentes necessários** | Em cada máquina que pretende analisar, instale os seguintes agentes:<br/><br/> O agente de monitorização da [Microsoft (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).<br/> O [agente da Dependência.](../azure-monitor/platform/agents-overview.md#dependency-agent)<br/><br/> Se as máquinas no local não estiverem ligadas à internet, é necessário descarregar e instalar o gateway Log Analytics.<br/><br/> Saiba mais sobre a instalação do [agente dependency](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) e [do MMA](how-to-create-group-machine-dependencies.md#install-the-mma).
**Área de trabalho do Log Analytics** | O espaço de trabalho deve estar na mesma subscrição que o projeto Azure Migrate.<br/><br/> A Azure Migrate apoia espaços de trabalho residentes nas regiões leste dos EUA, Sudeste Asiático e Europa Ocidental.<br/><br/>  O espaço de trabalho deve estar numa região em que o Mapa de [Serviços é apoiado.](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites)<br/><br/> O espaço de trabalho para um projeto Azure Migrate não pode ser modificado depois de adicionado.
**Custos** | A solução Service Map não incorre em quaisquer encargos nos primeiros 180 dias (a partir do dia em que associa o espaço de trabalho log Analytics ao projeto Azure Migrate)/<br/><br/> Após 180 dias, aplicar-se-ão as cobranças padrão do Log Analytics.<br/><br/> A utilização de qualquer outra solução que não o Mapa de Serviço sintetizado no espaço de trabalho associado do Log Analytics incorrerá em [encargos padrão](https://azure.microsoft.com/pricing/details/log-analytics/) para o Log Analytics.<br/><br/> Quando o projeto Azure Migrate é eliminado, o espaço de trabalho não é eliminado juntamente com ele. Após a exclusão do projeto, o uso do Mapa de Serviço sem graça, e cada nó será cobrado de acordo com o nível pago do espaço de trabalho Log Analytics/<br/><br/>Se tiver projetos que criou antes da disponibilidade geral da Azure Migrate (GA- 28 de fevereiro de 2018), poderá ter incorrido em custos adicionais do Mapa de Serviços. Para garantir o pagamento após 180 dias, recomendamos que crie um novo projeto, uma vez que os espaços de trabalho existentes antes da GA ainda são responsáveis.
**Gestão** | Quando regista agentes no espaço de trabalho, utiliza a identificação e a chave fornecidas pelo projeto Azure Migrate.<br/><br/> Pode utilizar o espaço de trabalho log Analytics fora do Azure Migrate.<br/><br/> Se eliminar o projeto azure migrate associado, o espaço de trabalho não é eliminado automaticamente. [Elimine-o manualmente](../azure-monitor/platform/manage-access.md).<br/><br/> Não elimine o espaço de trabalho criado pela Azure Migrate, a menos que apague o projeto Azure Migrate. Se o fizer, a funcionalidade de visualização da dependência não funcionará como esperado.
**Conectividade Internet** | Se as máquinas não estiverem ligadas à internet, é necessário instalar a porta de entrada log analytics.
**Azure Government** | A análise da dependência baseada no agente não é apoiada.

## <a name="next-steps"></a>Passos seguintes

[Prepare-se para avaliação física do servidor](tutorial-prepare-physical.md).
