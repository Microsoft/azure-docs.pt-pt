---
title: Apoio à descoberta física e avaliação em Azure Migrate
description: Saiba mais sobre o apoio à descoberta física e avaliação com a Azure Migrate Discovery e a avaliação
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: 09110bb15f853f20b0be9d8c4c6f5088e7a55d8b
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/05/2021
ms.locfileid: "106382985"
---
# <a name="support-matrix-for-physical-server-discovery-and-assessment"></a>Matriz de suporte para descoberta e avaliação de servidores físicos 

Este artigo resume os pré-requisitos e requisitos de suporte quando avalia os servidores físicos para a migração para Azure, utilizando a ferramenta [Azure Migrate: Discovery and assessment.](migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool) Se quiser migrar servidores físicos para Azure, reveja a matriz de suporte à [migração](migrate-support-matrix-physical-migration.md).

Para avaliar servidores físicos, cria um projeto e adiciona o Azure Migrate: Discovery e ferramenta de avaliação ao projeto. Depois de a ferramenta ser adicionada, coloca o [aparelho Azure Migrate](migrate-appliance.md). O aparelho descobre continuamente servidores no local e envia metadados e dados de desempenho para o Azure. Após a descoberta estar completa, você recolhe servidores descobertos em grupos, e executar uma avaliação para um grupo.

## <a name="limitations"></a>Limitações

**Suporte** | **Detalhes**
--- | ---
**Limites de avaliação** | Você pode descobrir e avaliar até 35.000 servidores físicos em um único [projeto](migrate-support-matrix.md#project).
**Limites do projeto** | Pode criar vários projetos numa subscrição do Azure. Além dos servidores físicos, um projeto pode incluir servidores em VMware e no Hyper-V, até aos limites de avaliação para cada um.
**Deteção** | O aparelho Azure Migrate pode descobrir até 1000 servidores físicos.
**Avaliação** | Pode adicionar até 35.000 servidores num único grupo.<br/><br/> Pode avaliar até 35.000 servidores numa única avaliação.

[Saiba mais](concepts-assessment-calculation.md) sobre avaliações.

## <a name="physical-server-requirements"></a>Requisitos do servidor físico

**Implementação do servidor físico:** O servidor físico pode ser autónomo ou implantado num cluster.

**Sistema operativo:** Todos os sistemas operativos Windows e Linux podem ser avaliados para migração.

**Permissões:**

- Para servidores windows, utilize uma conta de domínio para servidores unidos pelo domínio e uma conta local para servidores que não estejam unidos pelo domínio. A conta de utilizador deve ser adicionada a estes grupos: Utilizadores de Gestão Remota, Utilizadores do Monitor de Desempenho e Utilizadores do Registo de Desempenho.
    > [!Note]
    > Para o Windows Server 2008 e 2008 R2, certifique-se de que o WMF 3.0 está instalado nos servidores e que o domínio/conta local utilizado para aceder aos servidores é adicionado a estes grupos: Utilizadores do Monitor de Desempenho, Utilizadores de Registo de Desempenho e WinRMRemoteWMIUsers.
- Nos servidores Linux, precisa de uma conta de superutilizador nos servidores Linux que deseja detetar. Alternadamente, pode definir uma conta não raiz com as capacidades necessárias utilizando os seguintes comandos:

**Comando** | **Objetivo**
--- | --- |
setcap CAP_DAC_READ_SEARCH+eip /usr/sbin/fdisk <br></br> setcap CAP_DAC_READ_SEARCH+eip /sbin/fdisk _(se /usr/sbin/fdisk não estiver presente)_ | Para recolher dados de configuração de disco
setcap "cap_dac_override,cap_dac_read_search,cap_fowner,cap_fsetid,cap_setuid,<br>cap_setpcap,cap_net_bind_service cap_net_admin cap_sys_chroot cap_sys_admin<br>cap_sys_resource,cap_audit_control,cap_setfcap=+eip" /sbin/lvm | Para recolher dados de desempenho do disco
setcap CAP_DAC_READ_SEARCH+eip /usr/sbin/dmidecode | Para recolher número de série BIOS
chmod a+r /sys/class/dmi/id/product_uuid | Para recolher BIOS GUID

## <a name="azure-migrate-appliance-requirements"></a>Requisitos de aplicação do Azure Migrate

A Azure Migrate utiliza o [aparelho Azure Migrate](migrate-appliance.md) para ser descoberto e avaliado. O aparelho para servidores físicos pode funcionar num VM ou num servidor físico.

- Saiba mais sobre [os requisitos do aparelho](migrate-appliance.md#appliance---physical) para servidores físicos.
- Saiba mais sobre URLs que o aparelho precisa de aceder em nuvens [públicas](migrate-appliance.md#public-cloud-urls) e [governamentais.](migrate-appliance.md#government-cloud-urls)
- Configura o aparelho utilizando um [script PowerShell](how-to-set-up-appliance-physical.md) que descarrega a partir do portal Azure.
No Governo Azure, utilize o aparelho [utilizando este script](deploy-appliance-script-government.md).

## <a name="port-access"></a>Acesso portuário

O quadro seguinte resume os requisitos portuários para avaliação.

**Dispositivo** | **Ligação**
--- | ---
**Aparelho** | Ligações de entrada na porta TCP 3389, para permitir ligações remotas de ambiente de trabalho ao aparelho.<br/><br/> Ligações de entrada na porta 44368, para aceder remotamente à aplicação de gestão do aparelho utilizando o URL: ``` https://<appliance-ip-or-name>:44368 ```<br/><br/> Ligações de saída nas portas 443 (HTTPS), para enviar metadados de descoberta e desempenho para Azure Migrate.
**Servidores físicos** | **Janelas:** Ligação de entrada na porta WinRM 5985 (HTTP) ou 5986 (HTTPS) para puxar metadados de configuração e desempenho dos servidores do Windows. <br/><br/> **Linux:**  Ligações de entrada na porta 22 (TCP), para puxar metadados de configuração e desempenho dos servidores Linux. |

## <a name="agent-based-dependency-analysis-requirements"></a>Requisitos de análise de dependência baseados em agentes

[A análise da dependência](concepts-dependency-visualization.md) ajuda-o a identificar dependências entre servidores no local que pretende avaliar e migrar para Azure. O quadro resume os requisitos para a criação de uma análise de dependência baseada em agentes. Atualmente, apenas a análise de dependência baseada em agentes é suportada para servidores físicos.

**Requisito** | **Detalhes**
--- | ---
**Antes da implantação** | Deverá ter um projeto em vigor, com a ferramenta Azure Migrate: Discovery e assessment a adicionar ao projeto.<br/><br/>  Implementa visualização de dependência após configurar um aparelho Azure Migrate para descobrir os seus servidores no local<br/><br/> [Aprenda](create-manage-projects.md) a criar um projeto pela primeira vez.<br/> [Saiba como](how-to-assess.md) adicionar uma ferramenta de avaliação a um projeto existente.<br/> Saiba como configurar o aparelho Azure Migrate para avaliação de [Hiper-V,](how-to-set-up-appliance-hyper-v.md) [VMware](how-to-set-up-appliance-vmware.md)ou servidores físicos.
**Azure Government** | A visualização da dependência não está disponível no Governo de Azure.
**Log Analytics** | A Azure Migrate utiliza a solução [de Mapa de Serviço](../azure-monitor/vm/service-map.md) nos [registos do Monitor Azure](../azure-monitor/logs/log-query-overview.md) para visualização da dependência.<br/><br/> Associa um novo ou existente espaço de trabalho do Log Analytics a um projeto. O espaço de trabalho para um projeto não pode ser modificado depois de ser adicionado. <br/><br/> O espaço de trabalho deve estar na mesma subscrição que o projeto.<br/><br/> O espaço de trabalho deve residir nas regiões do Leste dos EUA, Sudeste Asiático ou Europa Ocidental. Espaços de trabalho noutras regiões não podem ser associados a um projeto.<br/><br/> O espaço de trabalho deve estar numa região em que [o Mapa de Serviços é suportado.](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions)<br/><br/> No Log Analytics, o espaço de trabalho associado ao Azure Migrate está marcado com a chave do Projeto migratório e o nome do projeto.
**Agentes necessários** | Em cada servidor que pretende analisar, instale os seguintes agentes:<br/><br/> O [agente de monitorização da Microsoft (MMA)](../azure-monitor/agents/agent-windows.md).<br/> O [agente de dependência.](../azure-monitor/agents/agents-overview.md#dependency-agent)<br/><br/> Se os servidores no local não estiverem ligados à internet, é necessário descarregar e instalar o Gateway Log Analytics nos mesmos.<br/><br/> Saiba mais sobre a instalação do [agente Desadependante](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) e [MMA.](how-to-create-group-machine-dependencies.md#install-the-mma)
**Log Analytics espaço de trabalho** | O espaço de trabalho deve estar na mesma subscrição de um projeto.<br/><br/> Azure Migrate apoia espaços de trabalho residentes nas regiões do Leste dos EUA, Sudeste Asiático e Europa Ocidental.<br/><br/>  O espaço de trabalho deve estar numa região em que [o Mapa de Serviços é suportado.](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions)<br/><br/> O espaço de trabalho para um projeto não pode ser modificado depois de ser adicionado.
**Custos** | A solução Mapa de Serviço não incorre em quaisquer encargos nos primeiros 180 dias (a partir do dia em que associar o espaço de trabalho Log Analytics ao projeto)/<br/><br/> Após 180 dias, aplicar-se-ão as cobranças padrão do Log Analytics.<br/><br/> A utilização de qualquer solução que não o Mapa de Serviços no espaço de trabalho associado do Log Analytics incorrerá em [taxas padrão](https://azure.microsoft.com/pricing/details/log-analytics/) para o Log Analytics.<br/><br/> Quando o projeto é eliminado, o espaço de trabalho não é apagado juntamente com ele. Após a eliminação do projeto, o uso do Mapa de Serviço não é gratuito, e cada nó será cobrado de acordo com o nível pago do espaço de trabalho Log Analytics/<br/><br/>Se tiver projetos que criou antes da disponibilidade geral da Azure Migrate (GA- 28 de fevereiro de 2018), poderá ter incorrido em taxas adicionais do Mapa de Serviços. Para garantir o pagamento após apenas 180 dias, recomendamos que crie um novo projeto, uma vez que os espaços de trabalho existentes antes da AG ainda são exejáveis.
**Gestão** | Quando regista agentes no espaço de trabalho, utiliza o ID e a chave fornecida pelo projeto.<br/><br/> Pode utilizar o espaço de trabalho Log Analytics fora de Azure Migrate.<br/><br/> Se eliminar o projeto associado, o espaço de trabalho não é apagado automaticamente. [Elimine-o manualmente.](../azure-monitor/logs/manage-access.md)<br/><br/> Não elimine o espaço de trabalho criado pela Azure Migrate, a menos que elimine o projeto. Se o fizer, a funcionalidade de visualização de dependência não funcionará como esperado.
**Conectividade Internet** | Se os servidores não estiverem ligados à internet, é necessário instalar o gateway Do Registo Analytics neles.
**Azure Government** | A análise da dependência baseada em agentes não é suportada.

## <a name="next-steps"></a>Passos seguintes

[Prepare-se para a Descoberta Física e avaliação.](./tutorial-discover-physical.md)