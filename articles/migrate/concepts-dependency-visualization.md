---
title: Visualização de dependência no Azure Migrate
description: Fornece uma visão geral dos cálculos de avaliação no serviço de avaliação do servidor em Azure Migrate
ms.topic: conceptual
ms.date: 02/24/2020
ms.openlocfilehash: f24656d02e19f422ff26e6b06d1631a9128dff43
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78362206"
---
# <a name="dependency-visualization"></a>Visualização de dependência

Este artigo descreve a visualização da dependência em Azure Migrate:Server Assessment.

## <a name="what-is-dependency-visualization"></a>O que é a visualização da dependência?

A visualização da dependência ajuda-o a identificar dependências entre máquinas no local que pretende avaliar e migrar para Azure. 

- Em Azure Migrate:Server Assessment, você recolhe máquinas em um grupo, e depois avalia o grupo. A visualização da dependência ajuda-o a agrupar as máquinas com mais precisão, com alta confiança para a avaliação.
- A visualização da dependência permite identificar máquinas que devem ser migradas em conjunto. Pode identificar se as máquinas estão a ser utilizadas, ou se podem ser desativadas em vez de emigrarem.
- Visualizar dependências ajuda a garantir que nada é deixado para trás, e evitar interrupções surpresa durante a migração.
- A visualização é especialmente útil se não tiver a certeza se as máquinas fazem parte de uma implementação de aplicações que pretende migrar para o Azure.


> [!NOTE]
> A visualização da dependência não está disponível no Governo de Azure.

## <a name="agent-basedagentless-visualization"></a>Visualização baseada em agente/agente

Existem duas opções para implementar a visualização da dependência:

- Baseado no **agente:** A visualização da dependência baseada no agente requer que os agentes sejam instalados em cada máquina no local que pretende analisar.
- **Agente:** Com esta opção, não precisa de instalar agentes em máquinas que pretende cruzar. Esta opção encontra-se atualmente em pré-visualização e só está disponível para VMware VMs.


## <a name="agent-based-visualization"></a>Visualização baseada em agente

**Requisito** | **Detalhes** | **Saiba mais**
--- | --- | ---
**Antes da implantação** | Deverá ter um projeto Azure Migrate em vigor, com a ferramenta Azure Migrate: Server Assessment adicionada ao projeto.<br/><br/>  Implementa a visualização da dependência depois de instalar um aparelho Azure Migrate para descobrir as suas máquinas no local. | [Aprenda](create-manage-projects.md) a criar um projeto pela primeira vez.<br/><br/> [Aprenda](how-to-assess.md) a adicionar uma ferramenta de avaliação a um projeto existente.<br/><br/> Aprenda a configurar o aparelho Azure Migrate para avaliação de [Hiper-V,](how-to-set-up-appliance-hyper-v.md) [VMware](how-to-set-up-appliance-vmware.md)ou servidores físicos.
**Agentes necessários** | Em cada máquina que pretende analisar, instale os seguintes agentes:<br/><br/> O agente de monitorização da [Microsoft (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).<br/><br/> O [agente da Dependência.](../azure-monitor/platform/agents-overview.md#dependency-agent)<br/><br/> Se as máquinas no local não estiverem ligadas à internet, é necessário descarregar e instalar o gateway Log Analytics. | Saiba mais sobre a instalação do [agente dependency](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) e [do MMA](how-to-create-group-machine-dependencies.md#install-the-mma).
**Log Analytics** | A Azure Migrate utiliza a solução [Service Map](../operations-management-suite/operations-management-suite-service-map.md) em [registos do Monitor Azure](../log-analytics/log-analytics-overview.md) para visualização da dependência.<br/><br/> Associa um novo ou existente espaço de trabalho log analytics a um projeto Azure Migrate. O espaço de trabalho para um projeto Azure Migrate não pode ser modificado depois de adicionado. <br/><br/> O espaço de trabalho deve estar na mesma subscrição que o projeto Azure Migrate.<br/><br/> O espaço de trabalho deve residir nas regiões leste dos EUA, sudeste asiático ou na Europa Ocidental. Espaços de trabalho noutras regiões não podem ser associados a um projeto.<br/><br/> O espaço de trabalho deve estar numa região em que o Mapa de [Serviços é apoiado.](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites)<br/><br/> No Log Analytics, o espaço de trabalho associado ao Azure Migrate está marcado com a chave do Projeto migração e o nome do projeto.
**Custos** | A solução Service Map não incorre em quaisquer encargos nos primeiros 180 dias (a partir do dia em que associa o espaço de trabalho log Analytics ao projeto Azure Migrate)/<br/><br/> Após 180 dias, serão aplicados os encargos padrão do Log Analytics.<br/><br/> A utilização de qualquer outra solução que não o Mapa de Serviço sintetizado no espaço de trabalho associado do Log Analytics incorrerá em [encargos padrão](https://azure.microsoft.com/pricing/details/log-analytics/) para o Log Analytics.<br/><br/> Quando o projeto Azure Migrate é eliminado, o espaço de trabalho não é eliminado juntamente com ele. Após a exclusão do projeto, o uso do Mapa de Serviço sem graça, e cada nó será cobrado de acordo com o nível pago do espaço de trabalho Log Analytics/<br/><br/>Se tiver projetos que criou antes da disponibilidade geral da Azure Migrate (GA- 28 de fevereiro de 2018), poderá ter incorrido em custos adicionais do Mapa de Serviços. Para garantir o pagamento após 180 dias, recomendamos que crie um novo projeto, uma vez que os espaços de trabalho existentes antes da GA ainda são responsáveis.
**Gestão** | Quando regista agentes no espaço de trabalho, utiliza a identificação e a chave fornecidas pelo projeto Azure Migrate.<br/><br/> Pode utilizar o espaço de trabalho log Analytics fora do Azure Migrate.<br/><br/> Se eliminar o projeto azure migrate associado, o espaço de trabalho não é eliminado automaticamente. [Elimine-o manualmente](../azure-monitor/platform/manage-access.md).<br/><br/> Não elimine o espaço de trabalho criado pela Azure Migrate, a menos que apague o projeto Azure Migrate. Se o fizer, a funcionalidade de visualização da dependência não funcionará como esperado.

## <a name="agentless-visualization"></a>Visualização sem agente


**Requisito** | **Detalhes** | **Saiba mais**
--- | --- | ---
**Antes da implantação** | Deverá ter um projeto Azure Migrate em vigor, com a ferramenta Azure Migrate: Server Assessment adicionada ao projeto.<br/><br/>  Implementa a visualização da dependência depois de configurar um aparelho Azure Migrate para descobrir as suas máquinas VMWare no local. | [Aprenda](create-manage-projects.md) a criar um projeto pela primeira vez.<br/><br/> [Aprenda](how-to-assess.md) a adicionar uma ferramenta de avaliação a um projeto existente.<br/><br/> Aprenda a configurar o aparelho Azure Migrate para avaliação de [VMware](how-to-set-up-appliance-vmware.md) VMs.
**Agentes necessários** | Não é necessário um agente nas máquinas que queira analisar.
**Sistemas operativos suportados** | Reveja os [sistemas operativos](migrate-support-matrix-vmware.md#agentless-dependency-visualization) suportados para visualização sem agente.
**VMs** | **Ferramentas VMware**: As ferramentas VMware devem ser instaladas e em funcionamento em VMs que pretende analisar.<br/><br/> **Conta**: No aparelho Azure Migrate, é necessário adicionar uma conta de utilizador que possa ser utilizada para aceder a VMs para análise.<br/><br/> **VMs do Windows**: A conta de utilizador tem de ser um administrador local ou de domínio na máquina.<br/><br/> **VMs Linux**: O privilégio de raiz é exigido na conta. Alternadamente, a conta de utilizador requer estas duas capacidades em ficheiros /bin/netstat e /bin/ls: CAP_DAC_READ_SEARCH e CAP_SYS_PTRACE. | [Saiba mais sobre](migrate-appliance.md) o aparelho Azure Migrate.
**VMware** | **vCenter**: O aparelho necessita de uma conta vCenter Server com acesso apenas de leitura, e privilégios habilitados para Máquinas Virtuais > Operações de Hóspedes.<br/><br/> **ANFITRIÕES ESXi**: Em ESXi acolhe VMs em execução que pretende analisar, o aparelho Azure Migrate deve ser capaz de ligar à porta TCP 443.
**Dados recolhidos** |  A visualização da dependência sem agente funciona capturando dados de ligação TCP a partir de máquinas para as quais está ativado. Após o início da descoberta da dependência, o aparelho recolhe estes dados a partir de máquinas através de sondagens a cada cinco minutos:<br/> - Ligações TCP.<br/> - Nomes de processos que têm ligações ativas.<br/> - Nomes de aplicações instaladas que executam o processo com ligações ativas.<br/> - O número de ligações detetadas em todos os intervalos de votação.


## <a name="next-steps"></a>Passos seguintes
- [Configurar a visualização da dependência](how-to-create-group-machine-dependencies.md)
- Experimente a [visualização de dependência sem agente](how-to-create-group-machine-dependencies-agentless.md) para VMware VMs.
- Reveja [questões comuns](common-questions-discovery-assessment.md#what-is-dependency-visualization) sobre visualização da dependência.


