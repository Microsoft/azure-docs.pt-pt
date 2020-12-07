---
title: Suporte para avaliação do servidor físico em Azure Migrate
description: Saiba mais sobre o suporte para avaliação do servidor físico com a Avaliação do Servidor Azure Migrate
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 06/03/2020
ms.openlocfilehash: cfbbc1d218f590241fab804e389acd689c009dac
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/07/2020
ms.locfileid: "96754016"
---
# <a name="support-matrix-for-physical-server-assessment"></a>Matriz de suporte para avaliação do servidor físico 

Este artigo resume os pré-requisitos e requisitos de suporte quando avalia os servidores físicos para a migração para Azure, utilizando a ferramenta [Azure Migrate:Server Assessment.](migrate-services-overview.md#azure-migrate-server-assessment-tool) Se quiser migrar servidores físicos para Azure, reveja a matriz de suporte à [migração](migrate-support-matrix-physical-migration.md).


Para avaliar servidores físicos, cria um projeto Azure Migrate e adiciona a ferramenta de Avaliação do Servidor ao projeto. Depois de a ferramenta ser adicionada, coloca o [aparelho Azure Migrate](migrate-appliance.md). O aparelho descobre continuamente máquinas no local e envia metadados de máquinas e dados de desempenho para o Azure. Após a descoberta estar completa, recolhe-se máquinas descobertas em grupos e faz uma avaliação para um grupo.


## <a name="limitations"></a>Limitações

**Suporte** | **Detalhes**
--- | ---
**Limites de avaliação** | Você pode descobrir e avaliar até 35.000 servidores físicos em um único [projeto Azure Migrate](migrate-support-matrix.md#azure-migrate-projects).
**Limites do projeto** | Pode criar vários projetos numa subscrição do Azure. Além dos servidores físicos, um projeto pode incluir VMware VMs e Hiper-VMs, até os limites de avaliação para cada um.
**Deteção** | O aparelho Azure Migrate pode descobrir até 1000 servidores físicos.
**Avaliação** | Pode adicionar até 35.000 máquinas num único grupo.<br/><br/> Pode avaliar até 35.000 máquinas numa única avaliação.

[Saiba mais](concepts-assessment-calculation.md) sobre avaliações.

## <a name="physical-server-requirements"></a>Requisitos do servidor físico

**Implementação do servidor físico:** O servidor físico pode ser autónomo ou implantado num cluster.

**Sistema operativo:** Todos os sistemas operativos Windows e Linux podem ser avaliados para migração.

**Permissões:**
- Para os servidores do Windows, utilize uma conta de domínio para máquinas unidas a domínios e uma conta local para máquinas que não estejam unidas ao domínio. A conta de utilizador deve ser adicionada a estes grupos: Utilizadores de Gestão Remota, Utilizadores do Monitor de Desempenho e Utilizadores do Registo de Desempenho.
- Nos servidores Linux, precisa de uma conta de superutilizador nos servidores Linux que deseja detetar. Alternadamente, pode definir uma conta não raiz com as capacidades necessárias utilizando os seguintes comandos:

**Comando** | **Objetivo**
--- | --- |
setcap CAP_DAC_READ_SEARCH+eip /usr/sbin/fdisk <br></br> setcap CAP_DAC_READ_SEARCH+eip /sbin/fdisk _(se /usr/sbin/fdisk não estiver presente)_ | Para recolher dados de configuração de disco
setcap "cap_dac_override,cap_dac_read_search,cap_fowner,cap_fsetid,cap_setuid,<br>cap_setpcap,cap_net_bind_service cap_net_admin cap_sys_chroot cap_sys_admin<br>cap_sys_resource,cap_audit_control,cap_setfcap=+eip" /sbin/lvm | Para recolher dados de desempenho do disco
setcap CAP_DAC_READ_SEARCH+eip /usr/sbin/dmidecode | Para recolher número de série BIOS
chmod a+r /sys/class/dmi/id/product_uuid | Para recolher BIOS GUID



## <a name="azure-migrate-appliance-requirements"></a>Requisitos de aplicação do Azure Migrate

A Azure Migrate utiliza o [aparelho Azure Migrate](migrate-appliance.md) para ser descoberto e avaliado. O aparelho para servidores físicos pode funcionar com um VM ou uma máquina física. 

- Saiba mais sobre [os requisitos do aparelho](migrate-appliance.md#appliance---physical) para servidores físicos.
- Saiba mais sobre URLs que o aparelho precisa de aceder em nuvens [públicas](migrate-appliance.md#public-cloud-urls) e [governamentais.](migrate-appliance.md#government-cloud-urls)
- Configura o aparelho utilizando um [script PowerShell](how-to-set-up-appliance-physical.md) que descarrega a partir do portal Azure.
No Governo Azure, utilize o aparelho [utilizando este script](deploy-appliance-script-government.md).

## <a name="port-access"></a>Acesso portuário

O quadro seguinte resume os requisitos portuários para avaliação.

**Dispositivo** | **Ligação**
--- | ---
**Aparelho** | Ligações de entrada na porta TCP 3389, para permitir ligações remotas de ambiente de trabalho ao aparelho.<br/><br/> Ligações de entrada na porta 44368, para aceder remotamente à aplicação de gestão do aparelho utilizando o URL: ``` https://<appliance-ip-or-name>:44368 ```<br/><br/> Ligações de saída nas portas 443 (HTTPS), para enviar metadados de descoberta e desempenho para Azure Migrate.
**Servidores físicos** | **Janelas:** Ligação de entrada na porta WinRM 5985 (HTTP) para puxar metadados de configuração e desempenho dos servidores do Windows. <br/><br/> **Linux:**  Ligações de entrada na porta 22 (TCP), para puxar metadados de configuração e desempenho dos servidores Linux. |

## <a name="agent-based-dependency-analysis-requirements"></a>Requisitos de análise de dependência baseados em agentes

[A análise da dependência](concepts-dependency-visualization.md) ajuda-o a identificar dependências entre máquinas no local que pretende avaliar e migrar para Azure. O quadro resume os requisitos para a criação de uma análise de dependência baseada em agentes. Atualmente, apenas a análise de dependência baseada em agentes é suportada para servidores físicos.

**Requisito** | **Detalhes** 
--- | --- 
**Antes da implantação** | Deverá ter um projeto Azure Migrate no local, com a ferramenta de Avaliação do Servidor adicionada ao projeto.<br/><br/>  Implementa visualização de dependência após a instalação de um aparelho Azure Migrate para descobrir as suas máquinas no local<br/><br/> [Aprenda](create-manage-projects.md) a criar um projeto pela primeira vez.<br/> [Saiba como](how-to-assess.md) adicionar uma ferramenta de avaliação a um projeto existente.<br/> Saiba como configurar o aparelho Azure Migrate para avaliação de [Hiper-V,](how-to-set-up-appliance-hyper-v.md) [VMware](how-to-set-up-appliance-vmware.md)ou servidores físicos.
**Azure Government** | A visualização da dependência não está disponível no Governo de Azure.
**Log Analytics** | A Azure Migrate utiliza a solução [de Mapa de Serviço](../azure-monitor/insights/service-map.md) nos [registos do Monitor Azure](../azure-monitor/log-query/log-query-overview.md) para visualização da dependência.<br/><br/> Associa um novo ou existente espaço de trabalho log analytics a um projeto Azure Migrate. O espaço de trabalho para um projeto Azure Migrate não pode ser modificado depois de ser adicionado. <br/><br/> O espaço de trabalho deve estar na mesma subscrição que o projeto Azure Migrate.<br/><br/> O espaço de trabalho deve residir nas regiões do Leste dos EUA, Sudeste Asiático ou Europa Ocidental. Espaços de trabalho noutras regiões não podem ser associados a um projeto.<br/><br/> O espaço de trabalho deve estar numa região em que [o Mapa de Serviços é suportado.](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions)<br/><br/> No Log Analytics, o espaço de trabalho associado ao Azure Migrate está marcado com a chave do Projeto migratório e o nome do projeto.
**Agentes necessários** | Em cada máquina que pretende analisar, instale os seguintes agentes:<br/><br/> O [agente de monitorização da Microsoft (MMA)](../azure-monitor/platform/agent-windows.md).<br/> O [agente de dependência.](../azure-monitor/platform/agents-overview.md#dependency-agent)<br/><br/> Se as máquinas no local não estiverem ligadas à internet, é necessário descarregar e instalar o Gateway Log Analytics nelas.<br/><br/> Saiba mais sobre a instalação do [agente Desadependante](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) e [MMA.](how-to-create-group-machine-dependencies.md#install-the-mma)
**Log Analytics espaço de trabalho** | O espaço de trabalho deve estar na mesma subscrição que o projeto Azure Migrate.<br/><br/> Azure Migrate apoia espaços de trabalho residentes nas regiões do Leste dos EUA, Sudeste Asiático e Europa Ocidental.<br/><br/>  O espaço de trabalho deve estar numa região em que [o Mapa de Serviços é suportado.](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions)<br/><br/> O espaço de trabalho para um projeto Azure Migrate não pode ser modificado depois de ser adicionado.
**Custos** | A solução Mapa de Serviço não incorre em quaisquer encargos nos primeiros 180 dias (a partir do dia em que associar o espaço de trabalho log Analytics ao projeto Azure Migrate)/<br/><br/> Após 180 dias, aplicar-se-ão as cobranças padrão do Log Analytics.<br/><br/> A utilização de qualquer solução que não o Mapa de Serviços no espaço de trabalho associado do Log Analytics incorrerá em [taxas padrão](https://azure.microsoft.com/pricing/details/log-analytics/) para o Log Analytics.<br/><br/> Quando o projeto Azure Migrate é eliminado, o espaço de trabalho não é apagado juntamente com ele. Após a eliminação do projeto, o uso do Mapa de Serviço não é gratuito, e cada nó será cobrado de acordo com o nível pago do espaço de trabalho Log Analytics/<br/><br/>Se tiver projetos que criou antes da disponibilidade geral da Azure Migrate (GA- 28 de fevereiro de 2018), poderá ter incorrido em taxas adicionais do Mapa de Serviços. Para garantir o pagamento após apenas 180 dias, recomendamos que crie um novo projeto, uma vez que os espaços de trabalho existentes antes da AG ainda são exejáveis.
**Gestão** | Quando regista agentes no espaço de trabalho, utiliza o ID e a chave fornecida pelo projeto Azure Migrate.<br/><br/> Pode utilizar o espaço de trabalho Log Analytics fora de Azure Migrate.<br/><br/> Se eliminar o projeto Azure Migrate associado, o espaço de trabalho não é apagado automaticamente. [Elimine-o manualmente.](../azure-monitor/platform/manage-access.md)<br/><br/> Não elimine o espaço de trabalho criado pela Azure Migrate, a menos que elimine o projeto Azure Migrate. Se o fizer, a funcionalidade de visualização de dependência não funcionará como esperado.
**Conectividade Internet** | Se as máquinas não estiverem ligadas à internet, é necessário instalar o gateway Do Log Analytics nelas.
**Azure Government** | A análise da dependência baseada em agentes não é suportada.

## <a name="next-steps"></a>Passos seguintes

[Prepare-se para a avaliação do servidor físico.](./tutorial-discover-physical.md)