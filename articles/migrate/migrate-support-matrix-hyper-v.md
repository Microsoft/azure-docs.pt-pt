---
title: Suporte para avaliação de Hiper-V em Azure Migrate
description: Saiba mais sobre o suporte para a avaliação do Hiper-V com a Azure Migrate Discovery e avaliação
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: 8531d0b2252e6ddff75509046b5a4576b99d339f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104870794"
---
# <a name="support-matrix-for-hyper-v-assessment"></a>Matriz de suporte para avaliação de Hiper-V

Este artigo resume os pré-requisitos e requisitos de suporte quando descobre e avalia os servidores no local que estão a funcionar num ambiente Hiper-V para migração para Azure, utilizando a ferramenta [Azure Migrate: Discovery and assessment.](migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool) Se pretender migrar servidores em funcionamento em Hyper-V para Azure, reveja a matriz de suporte à [migração](migrate-support-matrix-hyper-v-migration.md).

Para configurar a descoberta e avaliação dos servidores em execução no Hiper-V, cria-se um projeto e adiciona-se a ferramenta Azure Migrate: Discovery and assessment ao projeto. Depois de a ferramenta ser adicionada, coloca o [aparelho Azure Migrate](migrate-appliance.md). O aparelho descobre continuamente servidores no local e envia metadados do servidor e dados de desempenho para o Azure. Após a descoberta estar completa, você recolhe servidores descobertos em grupos, e executar uma avaliação para um grupo.

## <a name="limitations"></a>Limitações

**Suporte** | **Detalhes**
--- | ---
**Limites de avaliação** | Você pode descobrir e avaliar até 35.000 servidores em um único [projeto](migrate-support-matrix.md#project).
**Limites do projeto** | Pode criar vários projetos numa subscrição do Azure. Além dos servidores em Hiper-V, um projeto pode incluir servidores em VMware e servidores físicos, até aos limites de avaliação para cada um.
**Deteção** | O aparelho Azure Migrate pode descobrir até 5000 servidores em funcionamento no Hyper-V.<br/><br/> O aparelho pode ligar até 300 anfitriões Hiper-V.
**Avaliação** | Pode adicionar até 35.000 servidores num único grupo.<br/><br/> Pode avaliar até 35.000 servidores numa única avaliação para um grupo.

[Saiba mais](concepts-assessment-calculation.md) sobre avaliações.

## <a name="hyper-v-host-requirements"></a>Requisitos do hospedeiro Hiper-V

| **Suporte**                | **Detalhes**
| :-------------------       | :------------------- |
| **Anfitrião Hyper-V**       | O hospedeiro Hyper-V pode ser autónomo ou implantado num cluster.<br/><br/> O anfitrião Hyper-V pode executar o Windows Server 2019, o Windows Server 2016 ou o Windows Server 2012 R2. As instalações centrais do servidor destes sistemas operativos também são suportadas. <br/>Não é possível avaliar os servidores localizados em anfitriões Hiper-V que executam o Windows Server 2012.
| **Permissões**           | Precisa de permissões de administrador no anfitrião Do Hiper-V. <br/> Se não quiser atribuir permissões de Administrador, crie uma conta de utilizador local ou de domínio e adicione a conta de utilizador a estes grupos- Utilizadores de Gestão Remota, Administradores de Hiper-V e Utilizadores do Monitor de Desempenho. |
| **Comunicação remota do PowerShell**   | [A remoing PowerShell](/powershell/module/microsoft.powershell.core/enable-psremoting) deve ser ativada em cada hospedeiro Hiper-V. |
| **Réplica do Hyper-V**       | Se utilizar a Réplica Hyper-V (ou tiver vários servidores com os mesmos identificadores de servidores) e descobrir tanto os servidores originais como replicados utilizando o Azure Migrate, a avaliação gerada pelo Azure Migrate pode não ser exata. |

## <a name="server-requirements"></a>Requisitos de servidor

| **Suporte**                  | **Detalhes**
| :----------------------------- | :------------------- |
| **Sistema operativo** | Todos os sistemas operativos podem ser avaliados para migração.  |
| **Serviços de Integração**       | [Os Serviços de Integração Hiper-V](/virtualization/hyper-v-on-windows/reference/integration-services) devem estar a funcionar em servidores que avalia, a fim de capturar informações do sistema operativo. |
| **Armazenamento** | Disco local, DAS, JBOD, Espaços de Armazenamento, CSV, SMB. Estes armazenamentos do anfitrião Hyper-V nos quais o VHD/VHDX está armazenado são suportados. <br/> Os controladores virtuais IDE e SCSI são suportados|

## <a name="azure-migrate-appliance-requirements"></a>Requisitos de aplicação do Azure Migrate

A Azure Migrate utiliza o [aparelho Azure Migrate](migrate-appliance.md) para ser descoberto e avaliado. Pode implantar o aparelho utilizando um Hiper-V VHD comprimido que descarrega a partir do portal ou utilizando um [script PowerShell](deploy-appliance-script.md).

- Saiba mais sobre [os requisitos do aparelho](migrate-appliance.md#appliance---hyper-v) para o Hyper-V.
- Saiba mais sobre URLs que o aparelho precisa de aceder em nuvens [públicas](migrate-appliance.md#public-cloud-urls) e [governamentais.](migrate-appliance.md#government-cloud-urls)
- No Governo de Azure, deve utilizar o aparelho [utilizando o guião](deploy-appliance-script-government.md).

## <a name="port-access"></a>Acesso portuário

O quadro seguinte resume os requisitos portuários para avaliação.

**Dispositivo** | **Ligação**
--- | ---
**Aparelho** | Ligações de entrada na porta TCP 3389 para permitir ligações remotas de ambiente de trabalho ao aparelho.<br/><br/> Ligações de entrada na porta 44368 para aceder remotamente à aplicação de gestão do aparelho utilizando o URL: ``` https://<appliance-ip-or-name>:44368 ```<br/><br/> Ligações de saída nas portas 443 (HTTPS), para enviar metadados de descoberta e desempenho para Azure Migrate.
**Hospedeiro/cluster hiper-V** | Ligação de entrada na porta WinRM 5985 (HTTP) ou 5986 (HTTPS) para retirar metadados e dados de desempenho para servidores em Hiper-V, utilizando uma sessão de Modelo de Informação Comum (CIM).

## <a name="agent-based-dependency-analysis-requirements"></a>Requisitos de análise de dependência baseados em agentes

[A análise da dependência](concepts-dependency-visualization.md) ajuda-o a identificar dependências entre servidores no local que pretende avaliar e migrar para Azure. O quadro resume os requisitos para a criação de uma análise de dependência baseada em agentes. Atualmente, o Hiper-V apenas suporta a visualização da dependência baseada no agente.

**Requisito** | **Detalhes**
--- | --- 
**Antes da implantação** | Deverá ter um projeto em vigor, com a ferramenta Azure Migrate: Discovery e assessment a adicionar ao projeto.<br/><br/>  Implementa a visualização de dependência depois de configurar um aparelho Azure Migrate para descobrir os seus servidores no local.<br/><br/> [Aprenda](create-manage-projects.md) a criar um projeto pela primeira vez.<br/> [Saiba como](how-to-assess.md) adicionar Azure Migrate: Descoberta e ferramenta de avaliação a um projeto existente.<br/> Saiba como configurar o aparelho para a descoberta e avaliação dos [servidores no Hyper-V](how-to-set-up-appliance-hyper-v.md).
**Azure Government** | A visualização da dependência não está disponível no Governo de Azure.
**Log Analytics** | A Azure Migrate utiliza a solução [de Mapa de Serviço](../azure-monitor/vm/service-map.md) nos [registos do Monitor Azure](../azure-monitor/logs/log-query-overview.md) para visualização da dependência.<br/><br/> Associa um novo ou existente espaço de trabalho do Log Analytics a um projeto. O espaço de trabalho para um projeto não pode ser modificado depois de ser adicionado. <br/><br/> O espaço de trabalho deve estar na mesma subscrição que o projeto.<br/><br/> O espaço de trabalho deve residir nas regiões do Leste dos EUA, Sudeste Asiático ou Europa Ocidental. Espaços de trabalho noutras regiões não podem ser associados a um projeto.<br/><br/> O espaço de trabalho deve estar numa região em que [o Mapa de Serviços é suportado.](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions)<br/><br/> No Log Analytics, o espaço de trabalho associado ao Azure Migrate está marcado com a chave do Projeto migratório e o nome do projeto.
**Agentes necessários** | Em cada servidor que pretende analisar, instale os seguintes agentes:<br/><br/> O [agente de monitorização da Microsoft (MMA)](../azure-monitor/agents/agent-windows.md).<br/> O [agente de dependência.](../azure-monitor/agents/agents-overview.md#dependency-agent)<br/><br/> Se os servidores no local não estiverem ligados à internet, é necessário descarregar e instalar o Gateway Log Analytics nos mesmos.<br/><br/> Saiba mais sobre a instalação do [agente Desadependante](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) e [MMA.](how-to-create-group-machine-dependencies.md#install-the-mma)
**Log Analytics espaço de trabalho** | O espaço de trabalho deve estar na mesma subscrição que o projeto.<br/><br/> Azure Migrate apoia espaços de trabalho residentes nas regiões do Leste dos EUA, Sudeste Asiático e Europa Ocidental.<br/><br/>  O espaço de trabalho deve estar numa região em que [o Mapa de Serviços é suportado.](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions)<br/><br/> O espaço de trabalho para um projeto não pode ser modificado depois de ser adicionado.
**Custos** | A solução Mapa de Serviço não incorre em quaisquer encargos nos primeiros 180 dias (a partir do dia em que associar o espaço de trabalho Log Analytics ao projeto)/<br/><br/> Após 180 dias, aplicar-se-ão as cobranças padrão do Log Analytics.<br/><br/> A utilização de qualquer solução que não o Mapa de Serviços no espaço de trabalho associado do Log Analytics incorrerá em [taxas padrão](https://azure.microsoft.com/pricing/details/log-analytics/) para o Log Analytics.<br/><br/> Quando o projeto é eliminado, o espaço de trabalho não é apagado juntamente com ele. Após a eliminação do projeto, o uso do Mapa de Serviço não é gratuito, e cada nó será cobrado de acordo com o nível pago do espaço de trabalho Log Analytics/<br/><br/>Se tiver projetos que criou antes da disponibilidade geral da Azure Migrate (GA- 28 de fevereiro de 2018), poderá ter incorrido em taxas adicionais do Mapa de Serviços. Para garantir o pagamento após apenas 180 dias, recomendamos que crie um novo projeto, uma vez que os espaços de trabalho existentes antes da AG ainda são exejáveis.
**Gestão** | Quando regista agentes no espaço de trabalho, utiliza o ID e a chave fornecida pelo projeto.<br/><br/> Pode utilizar o espaço de trabalho Log Analytics fora de Azure Migrate.<br/><br/> Se eliminar o projeto associado, o espaço de trabalho não é apagado automaticamente. [Elimine-o manualmente.](../azure-monitor/logs/manage-access.md)<br/><br/> Não elimine o espaço de trabalho criado pela Azure Migrate, a menos que elimine o projeto. Se o fizer, a funcionalidade de visualização de dependência não funcionará como esperado.
**Conectividade Internet** | Se os servidores não estiverem ligados à internet, é necessário instalar o gateway Do Registo Analytics neles.
**Azure Government** | A análise da dependência baseada em agentes não é suportada.

## <a name="next-steps"></a>Passos seguintes

[Prepare-se para avaliação dos servidores em execução no Hyper-V](./tutorial-discover-hyper-v.md).