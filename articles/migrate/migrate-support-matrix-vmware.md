---
title: Suporte de avaliação VMware em Azure Migrate
description: Saiba mais sobre o suporte de avaliação para servidores em execução em ambiente VMware com Azure Migrate Discovery e avaliação
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/17/2021
ms.openlocfilehash: 943223ee99838b2b6d6de7eecb3c3b8c06d3562c
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104773594"
---
# <a name="support-matrix-for-vmware-assessment"></a>Matriz de suporte para avaliação de VMware 

Este artigo resume os pré-requisitos e requisitos de suporte quando descobre e avalia os servidores em execução em ambiente VMware para migração para Azure, utilizando a ferramenta [Azure Migrate: Discovery and assessment.](migrate-services-overview.md#azure-migrate-server-assessment-tool) Para avaliar os servidores, cria um projeto, que adiciona o Azure Migrate: Discovery e ferramenta de avaliação ao projeto. Depois de a ferramenta ser adicionada, insuja o aparelho Azure Migrate. O aparelho descobre continuamente servidores no local e envia metadados de configuração e desempenho para o Azure. Após a descoberta estar completa, você recolhe servidores descobertos em grupos, e executar uma avaliação para um grupo.

Se pretender migrar servidores VMware para Azure, reveja a matriz de suporte à [migração](migrate-support-matrix-vmware-migration.md).



## <a name="limitations"></a>Limitações

**Requisito** | **Detalhes**
--- | ---
**Limites do projeto** | Pode criar vários projetos numa subscrição do Azure.<br/><br/> Você pode descobrir e avaliar até 50.000 servidores do ambiente VMware em um único [projeto](migrate-support-matrix.md#azure-migrate-projects). Um projeto também pode incluir servidores físicos, e servidores do ambiente Hiper-V, até os limites de avaliação.
**Deteção** | O aparelho Azure Migrate pode descobrir até 10.000 servidores num servidor vCenter.
**Avaliação** | Pode adicionar até 35.000 servidores num único grupo.<br/><br/> Pode avaliar até 35.000 servidores numa única avaliação.

[Saiba mais](concepts-assessment-calculation.md) sobre avaliações.


## <a name="vmware-requirements"></a>Requisitos de VMware

**VMware** | **Detalhes**
--- | ---
**vCenter Server** | Os servidores que pretende descobrir e avaliar devem ser geridos pela versão 5.5, 6.0, 6.5, 6.7 ou 7.0 do vCenter Server.<br/><br/> A descoberta de servidores fornecendo detalhes do anfitrião ESXi no aparelho não é suportada.
**Permissões** | Azure Migrate: A descoberta e a avaliação precisam de uma conta de leitura do vCenter Server apenas para a descoberta e avaliação.<br/><br/> Se pretender realizar a descoberta de inventário de software e análise de dependência sem agentes, a conta necessita de privilégios habilitados para operações **de hóspedes de máquinas virtuais.**  >  

## <a name="server-requirements"></a>Requisitos de servidor
**VMware** | **Detalhes**
--- | ---
**OS apoiado** | Todos os sistemas operativos Windows e Linux podem ser avaliados para migração.
**Armazenamento** | Os discos ligados aos controladores SCSI, IDE e SATA são suportados.


## <a name="azure-migrate-appliance-requirements"></a>Requisitos de aplicação do Azure Migrate

A Azure Migrate utiliza o [aparelho Azure Migrate](migrate-appliance.md) para ser descoberto e avaliado. Pode implantar o aparelho como servidor no seu ambiente VMware utilizando um modelo OVA, importado para o vCenter Server, ou utilizando um [script PowerShell](deploy-appliance-script.md).

- Saiba mais sobre [os requisitos do aparelho](migrate-appliance.md#appliance---vmware) para o VMware.
- No Governo de Azure, deve utilizar o aparelho [utilizando o guião](deploy-appliance-script-government.md).
- Reveja os URLs a que o aparelho precisa de aceder em nuvens [públicas](migrate-appliance.md#public-cloud-urls) e [governamentais.](migrate-appliance.md#government-cloud-urls)


## <a name="port-access-requirements"></a>Requisitos de acesso portuário

**Dispositivo** | **Ligação**
--- | ---
**Aparelho** | Ligações de entrada na porta TCP 3389 para permitir ligações remotas de ambiente de trabalho ao aparelho.<br/><br/> Ligações de entrada na porta 44368 para aceder remotamente à aplicação de gestão do aparelho utilizando o URL: ```https://<appliance-ip-or-name>:44368``` <br/><br/>Ligações de saída na porta 443 (HTTPS), para enviar metadados de descoberta e desempenho para Azure Migrate.
**vCenter Server** | Ligações de entrada na porta TCP 443 para permitir que o aparelho recolha metadados de configuração e desempenho para avaliações. <br/><br/> O aparelho liga-se ao vCenter na porta 443 por defeito. Se o servidor vCenter ouvir numa porta diferente, pode modificar a porta quando configurar a descoberta.
**Anfitriões ESXi** | Se pretender realizar a [descoberta do inventário de software](how-to-discover-applications.md), ou análise de dependência sem [agentes](concepts-dependency-visualization.md#agentless-analysis), o aparelho conecta-se aos anfitriões ESXi na porta TCP 443, para descobrir o inventário de software e dependências dos servidores.

## <a name="application-discovery-requirements"></a>Requisitos de descoberta de aplicações

Além de descobrir Servers, Azure Migrate: Discovery e assessment podem descobrir o inventário de software em execução em servidores. A descoberta de aplicações permite identificar e planear um caminho de migração adaptado para as suas cargas de trabalho no local.

**Suporte** | **Detalhes**
--- | ---
**Servidores suportados** | Atualmente suportado apenas para servidores no seu ambiente VMware. Pode realizar a descoberta de aplicações em até 10000 servidores, de cada aparelho Azure Migrate.
**Sistemas operativos** | Os servidores que executam todas as versões Windows e Linux são suportados.
**Requisitos de VM** | Para realizar a descoberta do inventário de software, as ferramentas VMware devem ser instaladas e em execução em servidores. <br/><br/> A versão VMware tools deve ser mais tarde do que 10.2.0.<br/><br/> Os servidores do Windows devem ter a versão PowerShell 2.0 ou posteriormente instalada.
**Deteção** | A descoberta de aplicações nos servidores é realizada a partir do servidor vCenter, utilizando ferramentas VMware instaladas nos servidores. O aparelho recolhe as informações sobre o inventário de software do servidor vCenter, utilizando APIs vSphere. A descoberta da aplicação é sem agente. Nenhum agente é instalado no servidor e o aparelho não se liga diretamente aos servidores. O WMI e o SSH devem estar ativados e disponíveis nos servidores Windows e Linux, respectivamente.
**conta de utilizador vCenter Server** | A conta de leitura vCenter Server utilizada para avaliação, necessita de privilégios habilitados para operações **de hóspedes de máquinas virtuais,**  >  de forma a interagir com os servidores para a descoberta de aplicações.
**Acesso ao servidor** | Pode adicionar várias credenciais de domínio e não domínio (Windows/Linux) no gestor de configuração do aparelho para a descoberta de aplicações.<br/><br/> Precisa de uma conta de utilizador para servidores Windows e de uma conta de utilizador regular/normal (acesso não sudo) para todos os servidores Do Linux.
**Acesso portuário** | O aparelho Azure Migrate deve ser capaz de ligar à porta TCP 443 nos anfitriões ESXi que executam servidores nos quais pretende realizar a descoberta de aplicações. O vCenter Server devolve uma ligação de anfitrião ESXi, para descarregar o ficheiro que contém os detalhes do inventário de software.

## <a name="requirements-for-discovery-of-sql-server-instances-and-databases"></a>Requisitos para a descoberta de instâncias e bases de dados do SQL Server

> [!Note]
> A descoberta e avaliação de instâncias e bases de dados do SQL Server em execução no seu ambiente VMware está agora em pré-visualização. Para experimentar esta funcionalidade, utilize [**este link**](https://aka.ms/AzureMigrate/SQL) para criar um projeto na região **Leste da Austrália**. Se já tiver um projeto no Leste da Austrália e quiser experimentar esta funcionalidade, verifique se concluiu estes [**pré-requisitos**](how-to-discover-sql-existing-project.md) no portal.

[A descoberta da aplicação](how-to-discover-applications.md) identifica as instâncias do SQL Server. Utilizando estas informações, o aparelho tenta ligar-se às respetivas instâncias do SQL Server através da autenticação do Windows ou das credenciais de autenticação do SQL Server fornecidas no aparelho. Uma vez ligado, o aparelho recolhe dados de configuração e desempenho de instâncias e bases de dados do SQL Server. Os dados de configuração do SQL Server são atualizados uma vez a cada 24 horas e os dados de desempenho são capturados a cada 30 segundos.

**Suporte** | **Detalhes**
--- | ---
**Servidores suportados** | Atualmente suportado para SQL Servers apenas no seu ambiente VMware. Pode descobrir até 300 casos de SQL Server ou bases de dados de 6000 SQL, o que for mais baixo.
**Servidores Windows** | O Windows Server 2008 e o windows 2008 e acima são suportados.
**Servidores Linux** | Não suportado atualmente.
**Mecanismo de autenticação** | A autenticação do Windows e do SQL Server é suportada. Pode fornecer credenciais de ambos os tipos de autenticação no gestor de configuração do aparelho.
**Acesso do SQL Server** | O Azure Migrate requer uma conta de utilizador do Windows que seja membro da função de servidor sysadmin.
**Versões do SQL Server** | O SQL Server 2008 e acima são suportados.
**Edições SQL Server** | As edições Enterprise, Standard, Developer e Express são suportadas.
**Configuração SQL suportada** | Atualmente, a descoberta de apenas instâncias autónomas do SQL Server e bases de dados correspondentes é suportada.<br/> A identificação dos grupos failover e sempre na disponibilidade não é suportada.
**Serviços SQL apoiados** | Apenas o motor de base de dados do servidor SQL é suportado. <br/> A descoberta dos Serviços de Relato de Servidores SQL (SSRS), dos Serviços de Integração de Servidores SQL (SSIS) e dos Serviços de Análise de Servidores SQL (SSAS) não é suportada.

> [!Note]
> A Azure Migrate encriptará a comunicação entre o aparelho Azure Migrate e as instâncias do SQL Server de origem (com a propriedade de ligação encriptada definida para TRUE). Estas ligações são encriptadas com [**TrustServerCertificate**](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlconnectionstringbuilder.trustservercertificate) (definido para TRUE); a camada de transporte utilizará o SSL para encriptar o canal e contornar a cadeia de certificados para validar a confiança. O servidor do aparelho deve ser criado para [**confiar na autoridade principal do certificado**](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine).<br/>
Se não tiver sido fornectado nenhum certificado no servidor quando este começa, o SQL Server gera um certificado auto-assinado que é utilizado para encriptar pacotes de login. [**Saiba mais**](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine).

## <a name="dependency-analysis-requirements-agentless"></a>Requisitos de análise de dependência (sem agente)

[A análise da dependência](concepts-dependency-visualization.md) ajuda-o a identificar dependências entre servidores no local que pretende avaliar e migrar para Azure. A tabela resume os requisitos para a criação de uma análise de dependência sem agentes.

**Suporte** | **Detalhes**
--- | --- 
**Servidores suportados** | Atualmente suportado apenas para servidores no seu ambiente VMware.
**Servidores Windows** | Windows Server 2016<br/> Windows Server 2012 R2<br/> Windows Server 2012<br/> Windows Server 2008 R2 (64 bits).<br/>Microsoft Windows Server 2008 (32 bits).
**Servidores Linux** | Red Hat Enterprise Linux 7, 6, 5<br/> Ubuntu Linux 14.04, 16.04<br/> Debian 7, 8<br/> Oráculo Linux 6, 7<br/> CentOS 5, 6, 7.<br/> SUSE Linux Enterprise Server 11 e mais tarde.
**Requisitos de servidor** | As Ferramentas VMware (mais tarde de 10.2.0) devem ser instaladas e em execução nos servidores que pretende analisar.<br/><br/> Os servidores devem ter a versão PowerShell 2.0 ou posteriormente instalada.
**Método de descoberta** |  As informações de dependência entre servidores são recolhidas a partir do servidor vCenter, utilizando ferramentas VMware instaladas no servidor. O aparelho recolhe as informações do servidor vCenter, utilizando APIs vSphere. Nenhum agente é instalado no servidor e o aparelho não se liga diretamente aos servidores. O WMI e o SSH devem estar ativados e disponíveis nos servidores Windows e Linux, respectivamente.
**conta vCenter** | A conta apenas de leitura utilizada pela Azure Migrate para a avaliação necessita de privilégios habilitados para **máquinas virtuais > Operações de Hóspedes.**
**Permissões do servidor do Windows** |  Uma conta de utilizador (local ou domínio) com permissões administrativas nos servidores.
**Conta Linux** | Conta de utilizador raiz, ou uma conta com estas permissões em /bin/netstat e /bin/ls ficheiros: CAP_DAC_READ_SEARCH e CAP_SYS_PTRACE.<br/><br/> Desardene estas capacidades utilizando os seguintes comandos: <br/><br/> sudo setcap CAP_DAC_READ_SEARCH,CAP_SYS_PTRACE=ep /bin/ls<br/><br/> sudo setcap CAP_DAC_READ_SEARCH,CAP_SYS_PTRACE=ep /bin/netstat
**Acesso portuário** | O aparelho Azure Migrate deve ser capaz de ligar à porta TCP 443 nos anfitriões ESXi que executam os servidores cujas dependências pretende descobrir. O vCenter Server devolve uma ligação de anfitrião ESXi para descarregar o ficheiro que contém os dados de dependência.


## <a name="dependency-analysis-requirements-agent-based"></a>Requisitos de análise de dependência (baseados em agente)

[A análise da dependência](concepts-dependency-visualization.md) ajuda-o a identificar dependências entre servidores no local que pretende avaliar e migrar para Azure. O quadro resume os requisitos para a criação de uma análise de dependência baseada em agentes.

**Requisito** | **Detalhes** 
--- | --- 
**Antes da implantação** | Deverá ter um projeto em vigor, com a ferramenta Azure Migrate: Discovery e assessment a adicionar ao projeto.<br/><br/>  Implementa a visualização de dependência depois de configurar um aparelho Azure Migrate para descobrir os seus servidores no local.<br/><br/> [Aprenda](create-manage-projects.md) a criar um projeto pela primeira vez.<br/> [Saiba como](how-to-assess.md) adicionar uma ferramenta de descoberta e avaliação a um projeto existente.<br/> Saiba como configurar o aparelho Azure Migrate para avaliação de [Hiper-V,](how-to-set-up-appliance-hyper-v.md) [VMware](how-to-set-up-appliance-vmware.md)ou servidores físicos.
**Servidores suportados** | Suportado para todos os servidores no seu ambiente no local.
**Log Analytics** | A Azure Migrate utiliza a solução [de Mapa de Serviço](../azure-monitor/insights/service-map.md) nos [registos do Monitor Azure](../azure-monitor/log-query/log-query-overview.md) para visualização da dependência.<br/><br/> Associa um novo ou existente espaço de trabalho do Log Analytics a um projeto. O espaço de trabalho para um projeto não pode ser modificado depois de ser adicionado. <br/><br/> O espaço de trabalho deve estar na mesma subscrição que o projeto.<br/><br/> O espaço de trabalho deve residir nas regiões do Leste dos EUA, Sudeste Asiático ou Europa Ocidental. Espaços de trabalho noutras regiões não podem ser associados a um projeto.<br/><br/> O espaço de trabalho deve estar numa região em que [o Mapa de Serviços é suportado.](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions)<br/><br/> No Log Analytics, o espaço de trabalho associado ao Azure Migrate está marcado com a chave do Projeto e o nome do projeto.
**Agentes necessários** | Em cada servidor que pretende analisar, instale os seguintes agentes:<br/><br/> O [agente de monitorização da Microsoft (MMA)](../azure-monitor/platform/agent-windows.md).<br/> O [agente de dependência.](../azure-monitor/platform/agents-overview.md#dependency-agent)<br/><br/> Se os servidores no local não estiverem ligados à internet, é necessário descarregar e instalar o Gateway Log Analytics nos mesmos.<br/><br/> Saiba mais sobre a instalação do [agente Desadependante](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) e [MMA.](how-to-create-group-machine-dependencies.md#install-the-mma)
**Log Analytics espaço de trabalho** | O espaço de trabalho deve estar na mesma subscrição que o projeto.<br/><br/> Azure Migrate apoia espaços de trabalho residentes nas regiões do Leste dos EUA, Sudeste Asiático e Europa Ocidental.<br/><br/>  O espaço de trabalho deve estar numa região em que [o Mapa de Serviços é suportado.](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions)<br/><br/> O espaço de trabalho para um projeto não pode ser modificado depois de ser adicionado.
**Custo** | A solução Mapa de Serviço não incorre em quaisquer encargos nos primeiros 180 dias (a partir do dia em que associar o espaço de trabalho Log Analytics ao projeto)/<br/><br/> Após 180 dias, aplicar-se-ão as cobranças padrão do Log Analytics.<br/><br/> A utilização de qualquer solução que não o Mapa de Serviços no espaço de trabalho associado do Log Analytics incorrerá em [taxas padrão](https://azure.microsoft.com/pricing/details/log-analytics/) para o Log Analytics.<br/><br/> Quando o projeto é eliminado, o espaço de trabalho não é apagado juntamente com ele. Após a eliminação do projeto, o uso do Mapa de Serviço não é gratuito, e cada nó será cobrado de acordo com o nível pago do espaço de trabalho Log Analytics/<br/><br/>Se tiver projetos que criou antes da disponibilidade geral da Azure Migrate (GA- 28 de fevereiro de 2018), poderá ter incorrido em taxas adicionais do Mapa de Serviços. Para garantir o pagamento após apenas 180 dias, recomendamos que crie um novo projeto, uma vez que os espaços de trabalho existentes antes da AG ainda são exejáveis.
**Gestão** | Quando regista agentes no espaço de trabalho, utiliza o ID e a chave fornecida pelo projeto.<br/><br/> Pode utilizar o espaço de trabalho Log Analytics fora de Azure Migrate.<br/><br/> Se eliminar o projeto associado, o espaço de trabalho não é apagado automaticamente. [Elimine-o manualmente.](../azure-monitor/platform/manage-access.md)<br/><br/> Não elimine o espaço de trabalho criado pela Azure Migrate, a menos que elimine o projeto. Se o fizer, a funcionalidade de visualização de dependência não funcionará como esperado.
**Conectividade Internet** | Se os servidores não estiverem ligados à internet, é necessário instalar o gateway Do Registo Analytics neles.
**Azure Government** | A análise da dependência baseada em agentes não é suportada.


## <a name="next-steps"></a>Passos seguintes

- [Reveja as](best-practices-assessment.md) melhores práticas para criar avaliações.
- Prepare-se para a avaliação [da VMware.](./tutorial-discover-vmware.md)