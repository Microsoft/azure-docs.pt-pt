---
title: Blueprint de Segurança e Conformidade do Azure-data warehouse para o FFIEC
description: Blueprint de Segurança e Conformidade do Azure-data warehouse para o FFIEC
services: security
author: meladie
ms.assetid: eb841702-057b-4e48-8bc8-2873e155d10e
ms.service: security
ms.topic: article
ms.date: 06/20/2018
ms.author: meladie
ms.openlocfilehash: 28f0546889a67d106c5d8c9c1ca7d86fe56890c1
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/10/2019
ms.locfileid: "68946816"
---
# <a name="azure-security-and-compliance-blueprint-data-warehouse-for-ffiec-financial-services"></a>Esquema de Segurança e Conformidade do Azure: data warehouse para serviços financeiros da FFIEC

## <a name="overview"></a>Descrição geral

Este Blueprint de Segurança e Conformidade do Azure fornece diretrizes para a implantação de uma arquitetura de data warehouse no Azure adequada para coleta, armazenamento e recuperação de dados financeiros regulamentados pelo Conselho de exame da instituição financeira Federal (FFIEC).

Essa arquitetura de referência, guia de implementação e modelo de ameaça fornecem uma base para os clientes cumprirem os requisitos do FFIEC. Essa solução fornece uma linha de base para ajudar os clientes a implantar cargas de trabalho no Azure em uma maneira compatível com o FFIEC. no entanto, essa solução não deve ser usada no estado em que se encontra em um ambiente de produção, pois é necessária uma configuração adicional.

A obtenção de conformidade com o FFIEC exige que auditores qualificados certificam uma solução de cliente de produção. As auditorias são supervisionadas por examinadores das agências de membros do FFIEC, incluindo o Conselho de administração do sistema federal de reservas (FRB), o FDIC (Federal Deposit Insurance Corporation), o NCUA (administrador da União de crédito nacional), o escritório do Comptroller da moeda (OCC) e a central de proteção financeira do consumidor (CFPB). Esses examinadores certificam que as auditorias são concluídas por avaliadores que mantêm a independência da instituição auditada. Os clientes são responsáveis por realizar avaliações apropriadas de segurança e conformidade de qualquer solução criada usando essa arquitetura, já que os requisitos podem variar com base nas especificidades da implementação de cada cliente.

## <a name="architecture-diagram-and-components"></a>Diagrama e componentes da arquitetura

Essa solução fornece uma arquitetura de referência que implementa uma data warehouse de nuvem segura e de alto desempenho. Há duas camadas de dados separadas nesta arquitetura: uma em que os dados são importados, armazenados e preparados em um ambiente SQL clusterizado e outro para o Azure SQL Data Warehouse onde os dados são carregados usando uma ferramenta de extração, transformação e carregamento (por exemplo, [polybase ](https://docs.microsoft.com/azure/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase)Consultas T-SQL) para processamento. Depois que os dados são armazenados no Azure SQL Data Warehouse, a análise pode ser executada em grande escala.

O Azure oferece uma variedade de relatórios e serviços de análise para o cliente. Essa solução inclui SQL Server Reporting Services (SSRS) para a criação rápida de relatórios da SQL Data Warehouse do Azure. Todo o tráfego do SQL é criptografado com SSL por meio da inclusão de certificados autoassinados. Como prática recomendada, o Azure recomenda o uso de uma autoridade de certificação confiável para aumentar a segurança.

Os dados na SQL Data Warehouse do Azure são armazenados em tabelas relacionais com armazenamento de coluna, um formato que reduz significativamente os custos de armazenamento de dados ao mesmo tempo em que melhora o desempenho da consulta. Dependendo dos requisitos de uso, os recursos de computação do Azure SQL Data Warehouse podem ser escalados ou reduzidos verticalmente ou desligados completamente se não houver nenhum processo ativo exigindo recursos de computação.

Um SQL Load Balancer gerencia o tráfego do SQL, garantindo alto desempenho. Todas as máquinas virtuais nessa arquitetura de referência são implantadas em um conjunto de disponibilidade com SQL Server instâncias configuradas em um grupo de disponibilidade Always On para recursos de alta disponibilidade e recuperação de desastres.

Essa data warehouse arquitetura de referência também inclui uma camada de Active Directory para o gerenciamento de recursos dentro da arquitetura. A sub-rede Active Directory permite uma adoção fácil em uma estrutura de floresta de Active Directory maior, permitindo a operação contínua do ambiente, mesmo quando o acesso à floresta maior não está disponível. Todas as máquinas virtuais são ingressadas no domínio na camada de Active Directory e usam Active Directory políticas de grupo para impor configurações de segurança e conformidade no nível do sistema operacional.

A solução usa contas de armazenamento do Azure, que os clientes podem configurar para usar o Criptografia do Serviço de Armazenamento para manter a confidencialidade dos dados em repouso. O Azure armazena três cópias de dados no datacenter selecionado de um cliente para fins de resiliência. O armazenamento com redundância geográfica garante que os dados serão replicados em um datacenter secundário a centenas de quilômetros de distância e, novamente, armazenados como três cópias dentro desse datacenter, impedindo que um evento adverso na data center primária do cliente resulte em uma perda de dado.

Para aumentar a segurança, todos os recursos nesta solução são gerenciados como um grupo de recursos por meio de Azure Resource Manager. Azure Active Directory controle de acesso baseado em função é usado para controlar o acesso a recursos implantados, incluindo suas chaves no Azure Key Vault. A integridade do sistema é monitorada por meio da central de segurança do Azure e Azure Monitor. Os clientes configuram ambos os serviços de monitoramento para capturar logs e exibir a integridade do sistema em um único painel fácil e navegável.

Uma máquina virtual serve como um host de bastiões de gerenciamento, fornecendo uma conexão segura para os administradores acessarem recursos implantados. Os dados são carregados na área de preparo por meio desse host de bastiões de gerenciamento. **A Microsoft recomenda configurar uma conexão VPN ou Azure ExpressRoute para gerenciamento e importação de dados na sub-rede da arquitetura de referência.**

![Data warehouse para o diagrama da arquitetura de referência do FFIEC](images/ffiec-dw-architecture.png "Data warehouse para o diagrama da arquitetura de referência do FFIEC")

Essa solução usa os seguintes serviços do Azure. Os detalhes da arquitetura de implantação estão na seção [arquitetura de implantação](#deployment-architecture) .

- Conjuntos de Disponibilidade
    - Controladores de Domínio do Active Directory
    - Testemunha e nós de cluster do SQL
- Azure Active Directory
- Catálogo de Dados do Azure
- Azure Key Vault
- Azure Monitor (logs)
- Centro de Segurança do Azure
- Balanceador de Carga do Azure
- Storage do Azure
- Máquinas Virtuais do Azure
    - (1) host de bastiões
    - (2) Active Directory controlador de domínio
    - (2) SQL Server nó de cluster
    - (1) SQL Server testemunha
- Rede Virtual do Azure
    - (1)/16 rede
    - (4)/24 redes
    - (4) grupos de segurança de rede
- Cofre dos serviços de recuperação
- SQL Data Warehouse
- Serviços de Relatórios do SQL Server

## <a name="deployment-architecture"></a>Arquitetura de implantação

A seção a seguir detalha os elementos de implantação e implementação.

**SQL data warehouse**: A [SQL data warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) é uma EDW (Enterprise Data warehouse) que aproveita o MPP (processamento paralelo maciço) para executar rapidamente consultas complexas em petabytes de dados, permitindo que os usuários identifiquem dados financeiros com eficiência. Os usuários podem usar consultas T-SQL simples do polybase para importar Big Data para o SQL Data Warehouse e utilizar o poder do MPP para executar análises de alto desempenho.

**SQL Server Reporting Services (SSRS)** : [SQL Server Reporting Services](https://docs.microsoft.com/sql/reporting-services/report-data/sql-azure-connection-type-ssrs) fornece criação rápida de relatórios com tabelas, gráficos, mapas, medidores, matrizes e muito mais para o Azure SQL data warehouse.

**Catálogo de dados**: O [Catálogo de dados](../../data-catalog/overview.md) torna as fontes de dados facilmente detectáveis e compreensíveis pelos usuários que gerenciam os dados. As fontes de dados comuns podem ser registradas, marcadas e pesquisadas para dados financeiros. Os dados permanecem em seu local existente, mas uma cópia de seus metadados é adicionada ao catálogo de dados, juntamente com uma referência ao local da fonte de dados. Os metadados também são indexados para tornar cada origem de dados facilmente detetável através da pesquisa e compreensível para os utilizadores que a detetarem.

**Host bastião**: O host de bastiões é o ponto único de entrada que permite aos usuários acessar os recursos implantados nesse ambiente. O host de bastiões fornece uma conexão segura com os recursos implantados permitindo apenas o tráfego remoto de endereços IP públicos em uma lista segura. Para permitir o tráfego da área de trabalho remota (RDP), a origem do tráfego precisa ser definida no grupo de segurança de rede.

Essa solução cria uma máquina virtual como um host de bastiões ingressado no domínio com as seguintes configurações:
-   [Extensão de antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware)
-   [Extensão de Diagnóstico do Azure](../../virtual-machines/windows/extensions-diagnostics-template.md)
-   [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) usando Azure Key Vault
-   Uma [política](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) de desligamento automático para reduzir o consumo de recursos de máquina virtual quando não estiver em uso
-   O [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) habilitado para que as credenciais e outros segredos sejam executados em um ambiente protegido isolado do sistema operacional em execução.

### <a name="virtual-network"></a>Rede virtual

Essa arquitetura de referência define uma rede virtual privada com um espaço de endereço de 10.0.0.0/16.

**Grupos de segurança de rede**: Os [grupos de segurança de rede](../../virtual-network/virtual-network-vnet-plan-design-arm.md) contêm listas de controle de acesso que permitem ou negam o tráfego em uma rede virtual. Os grupos de segurança de rede podem ser usados para proteger o tráfego em uma sub-rede ou em um nível de máquina virtual individual. Existem os seguintes grupos de segurança de rede:

  - Um grupo de segurança de rede para a camada de dados (clusters SQL Server, SQL Server testemunha e SQL Load Balancer)
  - Um grupo de segurança de rede para o host de bastiões de gerenciamento
  - Um grupo de segurança de rede para Active Directory
  - Um grupo de segurança de rede para SQL Server Reporting Services

Cada um dos grupos de segurança de rede tem portas e protocolos específicos abertos para que a solução possa funcionar de forma segura e correta. Além disso, as seguintes configurações estão habilitadas para cada grupo de segurança de rede:

- [Os logs e eventos de diagnóstico](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) são habilitados e armazenados em uma conta de armazenamento
- Os logs de Azure Monitor estão conectados aos [logs de&#39;diagnóstico s do grupo de segurança de rede](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Sub-redes**: Cada sub-rede é associada ao seu grupo de segurança de rede correspondente.

### <a name="data-at-rest"></a>Dados inativos

A arquitetura protege os dados em repouso por meio de várias medidas, incluindo a criptografia e a auditoria do banco.

**Armazenamento do Azure**: Para atender aos requisitos de dados criptografados em repouso, todo o [armazenamento do Azure](https://azure.microsoft.com/services/storage/) usa [criptografia do serviço de armazenamento](../../storage/common/storage-service-encryption.md). Isso ajuda a proteger e proteger os dados em suporte aos compromissos de segurança organizacional e aos requisitos de conformidade definidos pelo FFIEC.

**Azure Disk Encryption**: [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) aproveita o recurso BitLocker do Windows para fornecer criptografia de volume para discos de dados. A solução se integra com Azure Key Vault para ajudar a controlar e gerenciar as chaves de criptografia de disco.

**Banco de dados SQL do Azure**: A instância do banco de dados SQL do Azure usa as seguintes medidas de segurança de banco de dados:

- [Active Directory autenticação e autorização](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) permitem o gerenciamento de identidades de usuários de banco de dados e outros serviços da Microsoft em um local central.
- A [auditoria do banco de dados SQL](../../sql-database/sql-database-auditing.md) rastreia eventos de banco de dados e os grava em um log de auditoria em uma conta de armazenamento do Azure.
- O banco de dados SQL do Azure está configurado para usar a Transparent [Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), que executa criptografia e descriptografia em tempo real do banco dados, backups associados e arquivos de log de transações para proteger informações em repouso. A Transparent Data Encryption fornece garantia de que os dados armazenados não estão sujeitos a acesso não autorizado.
- [As regras de firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) impedem todo o acesso a servidores de banco de dados até que sejam concedidas permissões adequadas A firewall concede acesso a bases de dados com base no endereço IP de origem de cada pedido.
- A [detecção de ameaças do SQL](../../sql-database/sql-database-threat-detection.md) permite a detecção e a resposta a ameaças potenciais à medida que elas ocorrem, fornecendo alertas de segurança para atividades suspeitas de banco de dados, vulnerabilidades potenciais, ataques de injeção de SQL e padrões de acesso de banco de dados anormais
- As [colunas criptografadas](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) garantem que os dados confidenciais nunca apareçam como texto sem formatação no sistema de banco de dados. Depois de habilitar a criptografia de dados, somente aplicativos cliente ou servidores de aplicativos com acesso às chaves podem acessar dados de texto sem formatação.
- [As propriedades estendidas](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql) podem ser usadas para interromper o processamento de entidades de dados, pois ela permite que os usuários adicionem Propriedades personalizadas a objetos de banco de dados e marquem como "descontinuados" para dar suporte à lógica do aplicativo para evitar o processamento de finanças financeiras associadas dado.
- A [segurança em nível de linha](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) permite que os usuários definam políticas para restringir o acesso aos dados para o processamento descontinuado.
- O mascaramento de [dados dinâmicos do SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) limita a exposição de dados confidenciais mascarando os dados para usuários ou aplicativos sem privilégios. A máscara de dados dinâmicos pode descobrir dados potencialmente confidenciais automaticamente e sugerir que as máscaras apropriadas sejam aplicadas. Isso ajuda a identificar e reduzir o acesso a dados de modo que ele não saia do banco por acesso não autorizado. Os clientes são responsáveis por ajustar as configurações de mascaramento de dados dinâmicos para aderir ao seu esquema de banco de dado.

### <a name="identity-management"></a>Gestão de identidades

As tecnologias a seguir fornecem recursos para gerenciar o acesso aos dados no ambiente do Azure:

- [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) é o&#39;serviço de gerenciamento de identidade e diretório multilocatário baseado em nuvem da Microsoft. Todos os usuários desta solução são criados no Azure Active Directory, incluindo usuários que acessam o banco de dados SQL do Azure.
- A autenticação para o aplicativo é executada usando Azure Active Directory. Para obter mais informações, consulte [integrando aplicativos com Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). Além disso, a criptografia de coluna de banco de dados usa Azure Active Directory para autenticar o aplicativo no banco de dados SQL do Azure. Para obter mais informações, Confira como [proteger dados confidenciais no Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
- O [controle de acesso baseado em função do Azure](../../role-based-access-control/role-assignments-portal.md) permite que os administradores definam permissões de acesso refinadas para conceder apenas a quantidade de acesso que os usuários precisam para executar seus trabalhos. Em vez de fornecer a cada usuário permissão irrestrita para recursos do Azure, os administradores podem permitir apenas determinadas ações para acessar dados. O acesso à assinatura é limitado ao administrador da assinatura.
- [Azure Active Directory Privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md) permite que os clientes minimizem o número de usuários que têm acesso a determinadas informações. Os administradores podem usar Azure Active Directory Privileged Identity Management para descobrir, restringir e monitorar identidades com privilégios e seu acesso aos recursos. Essa funcionalidade também pode ser usada para impor acesso administrativo sob demanda e Just-in-time quando necessário.
- [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview.md) detecta possíveis vulnerabilidades que afetam as&#39;identidades de uma organização, configura as respostas automatizadas para detectar ações suspeitas&#39;relacionadas a identidades de uma organização e investiga incidentes suspeitos para tomar as medidas apropriadas para resolvê-los.

### <a name="security"></a>Segurança

**Gerenciamento de segredos**: A solução usa [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) para o gerenciamento de chaves e segredos. O Cofre de Chaves do Azure ajuda a salvaguardar as chaves criptográficas e os segredos utilizados pelas aplicações em cloud e pelos serviços. Os recursos de Azure Key Vault a seguir ajudam os clientes a proteger e acessar esses dados:

- As políticas de acesso avançadas são configuradas de acordo com a necessidade.
- Key Vault políticas de acesso são definidas com as permissões mínimas necessárias para chaves e segredos.
- Todas as chaves e segredos em Key Vault têm datas de expiração.
- Todas as chaves em Key Vault são protegidas por módulos de segurança de hardware especializados. O tipo de chave é uma chave RSA de 2048 bits protegida por HSM.
- Todos os usuários e identidades recebem as permissões mínimas necessárias usando o controle de acesso baseado em função.
- Os logs de diagnóstico para Key Vault estão habilitados com um período de retenção de pelo menos 365 dias.
- As operações de criptografia permitidas para chaves são restritas às necessárias.

**Gerenciamento**de patches: As máquinas virtuais do Windows implantadas como parte dessa arquitetura de referência são configuradas por padrão para receber atualizações automáticas do serviço Windows Update. Essa solução também inclui o serviço de [automação do Azure](https://docs.microsoft.com/azure/automation/automation-intro) por meio do qual implantações atualizadas podem ser criadas para aplicar patch às máquinas virtuais quando necessário.

**Proteção contra malware**: [O Microsoft Antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware) para máquinas virtuais fornece recursos de proteção em tempo real que ajudam a identificar e remover vírus, spyware e outros softwares mal-intencionados, com alertas configuráveis quando um software mal-intencionado ou indesejado conhecido tenta instalar ou executar em máquinas virtuais protegidas.

**Central de segurança do Azure**: Com a [central de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro), os clientes podem aplicar e gerenciar centralmente políticas de segurança entre cargas de trabalho, limitar a exposição a ameaças e detectar e responder a ataques. Além disso, a central de segurança do Azure acessa as configurações existentes dos serviços do Azure para fornecer recomendações de configuração e de serviço para ajudar a melhorar a postura de segurança e proteger os dados.

A central de segurança do Azure usa uma variedade de recursos de detecção para alertar os clientes sobre possíveis ataques visando seus ambientes. Estes alertas contêm informações valiosas sobre o que acionou o alerta, os recursos afetados e a origem do ataque. A central de segurança do Azure tem um conjunto de [alertas de segurança](https://docs.microsoft.com/azure/security-center/security-center-alerts-type)predefinidos, que são disparados quando ocorre uma ameaça ou atividade suspeita. [As regras de alerta personalizadas](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) na central de segurança do Azure permitem que os clientes definam novos alertas de segurança com base nos dados que já foram coletados de seu ambiente.

A central de segurança do Azure fornece alertas e incidentes de segurança priorizados, tornando mais simples para os clientes descobrir e resolver problemas potenciais de segurança. Um [relatório de inteligência contra ameaças](https://docs.microsoft.com/azure/security-center/security-center-threat-report) é gerado para cada ameaça detectada para ajudar as equipes de resposta a incidentes na investigação e na correção de ameaças.

### <a name="business-continuity"></a>Continuidade do negócio

**Elevada disponibilidade**: As cargas de trabalho do servidor são agrupadas em um [conjunto de disponibilidade](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para ajudar a garantir a alta disponibilidade de máquinas virtuais no Azure. Pelo menos uma máquina virtual está disponível durante um evento de manutenção planejada ou não planejada, atendendo ao SLA de 99,95% do Azure.

**Cofre dos serviços de recuperação**: O [cofre dos serviços de recuperação](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) hospeda dados de backup e protege todas as configurações de máquinas virtuais do Azure nessa arquitetura. Com um cofre dos serviços de recuperação, os clientes podem restaurar arquivos e pastas de uma máquina virtual IaaS sem restaurar toda a máquina virtual, permitindo tempos de restauração mais rápidos.

### <a name="logging-and-auditing"></a>Registro em log e auditoria

Os serviços do Azure registram extensivamente a atividade do sistema e do usuário, bem como a integridade do sistema:
- **Logs de atividade**: [Os logs de atividade](../../azure-monitor/platform/activity-logs-overview.md) fornecem informações sobre as operações executadas nos recursos em uma assinatura. Os logs de atividades podem ajudar a determinar o iniciador de uma operação, a hora de ocorrência e o status.
- **Logs de diagnóstico**: Os [logs de diagnóstico](../../azure-monitor/platform/diagnostic-logs-overview.md) incluem todos os logs emitidos por cada recurso. Esses logs incluem logs do sistema de eventos do Windows, logs de armazenamento do Azure, Key Vault logs de auditoria e acesso do gateway de aplicativo e logs de firewall. Todos os logs de diagnóstico gravam em uma conta de armazenamento do Azure centralizada e criptografada para arquivamento. A retenção é configurável pelo usuário, até 730 dias, para atender aos requisitos de retenção específicos da organização.

**Logs de Azure monitor**: Esses logs são consolidados em [logs de Azure monitor](https://azure.microsoft.com/services/log-analytics/) para processamento, armazenamento e relatórios de Dashboard. Depois de coletados, os dados são organizados em tabelas separadas para cada tipo de dados dentro de Log Analytics espaços de trabalho, o que permite que todos os dados sejam analisados juntos, independentemente de sua fonte original. Além disso, a central de segurança do Azure integra-se aos logs de Azure Monitor, permitindo que os clientes usem consultas Kusto para acessar seus dados de eventos de segurança e combiná-los com dados de outros serviços.

As seguintes [soluções de monitoramento](../../monitoring/monitoring-solutions.md) do Azure estão incluídas como parte dessa arquitetura:
-   [Avaliação do Active Directory](../../azure-monitor/insights/ad-assessment.md): A solução de verificação de integridade Active Directory avalia o risco e a integridade dos ambientes de servidor em um intervalo regular e fornece uma lista priorizada de recomendações específicas para a infraestrutura de servidor implantada.
- [Avaliação do SQL](../../azure-monitor/insights/sql-assessment.md): A solução de verificação de integridade do SQL avalia o risco e a integridade dos ambientes de servidor em um intervalo regular e fornece aos clientes uma lista priorizada de recomendações específicas para a infraestrutura de servidor implantada.
- [Integridade do agente](../../monitoring/monitoring-solution-agenthealth.md): A solução Integridade do Agente relata Quantos agentes são implantados e sua distribuição geográfica, bem como Quantos agentes que não respondem e o número de agentes que estão enviando dados operacionais.
-   [Análise do log de atividades](../../azure-monitor/platform/collect-activity-logs.md): A solução Análise do Log de Atividades auxilia na análise dos logs de atividade do Azure em todas as assinaturas do Azure para um cliente.

**Automação do Azure**: A [automação do Azure](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) armazena, executa e gerencia runbooks. Nesta solução, os runbooks ajudam a coletar logs do banco de dados SQL do Azure. A solução de [controle de alterações](../../automation/change-tracking.md) de automação permite que os clientes identifiquem facilmente as alterações no ambiente.

**Azure monitor**: [Azure monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) ajuda os usuários a controlar o desempenho, manter a segurança e identificar tendências, permitindo que as organizações façam auditoria, criem alertas e arquivem dados, incluindo o acompanhamento de chamadas de API em seus recursos do Azure.

## <a name="threat-model"></a>Modelo de ameaça

O diagrama de fluxo de dados para esta arquitetura de referência está disponível para [Download](https://aka.ms/ffiec-dw-tm/) ou pode ser encontrado abaixo. Esse modelo pode ajudar os clientes a entender os pontos de risco potencial na infraestrutura do sistema ao fazer modificações.

![Data warehouse para o modelo de ameaça do FFIEC](images/ffiec-dw-threat-model.png "Data warehouse para o modelo de ameaça do FFIEC")

## <a name="compliance-documentation"></a>Documentação de conformidade

A [matriz de responsabilidade do cliente Blueprint de segurança e conformidade do Azure – FFIEC](https://aka.ms/ffiec-crm) lista todos os objetivos exigidos pelo FFIEC. Essa matriz detalha se a implementação de cada objetivo é a responsabilidade da Microsoft, do cliente ou do compartilhamento entre os dois.

A [matriz de implementação Blueprint de segurança e conformidade do Azure – FFIEC data warehouse](https://aka.ms/ffiec-dw-cim) fornece informações sobre quais objetivos da FFIEC são tratados pela arquitetura de data warehouse, incluindo descrições detalhadas de como a implementação atende aos requisitos de cada objetivo coberto.

## <a name="guidance-and-recommendations"></a>Diretrizes e recomendações

### <a name="vpn-and-expressroute"></a>VPN e ExpressRoute

Um túnel VPN seguro ou o [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) precisa ser configurado para estabelecer com segurança uma conexão com os recursos implantados como parte dessa data warehouse arquitetura de referência. Ao configurar adequadamente uma VPN ou ExpressRoute, os clientes podem adicionar uma camada de proteção para os dados em trânsito.

Ao implementar um túnel VPN seguro com o Azure, uma conexão privada virtual entre uma rede local e uma rede virtual do Azure pode ser criada. Essa conexão ocorre pela Internet e permite aos clientes &quot;encapsular&quot; informações com segurança dentro de um link criptografado entre a&#39;rede do cliente e o Azure. A VPN site a site é uma tecnologia segura e madura, que foi implantada por empresas de todos os tamanhos por décadas. O [modo de encapsulamento IPSec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) é usado nessa opção como um mecanismo de criptografia.

Como o tráfego dentro do túnel VPN atravessa a Internet com uma VPN site a site, a Microsoft oferece outra opção de conexão ainda mais segura. O Azure ExpressRoute é um link WAN dedicado entre o Azure e um local ou um provedor de hospedagem do Exchange. Como as conexões do ExpressRoute não passam pela Internet, essas conexões oferecem mais confiabilidade, velocidades mais rápidas, latências menores e mais segurança do que as conexões típicas pela Internet. Além disso, como essa é uma conexão direta do&#39;provedor de telecomunicação do cliente, os dados não viajam pela Internet e, portanto, não são expostos a ele.

As práticas recomendadas para implementar uma rede híbrida segura que estende uma rede local para o Azure estão [disponíveis](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

### <a name="extract-transform-load-process"></a>Processo de extração-transformação-carregamento

O [polybase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) pode carregar dados no Azure SQL data warehouse sem a necessidade de uma ferramenta separada de extração, transformação, carregamento ou importação. O polybase permite o acesso a dados por meio de consultas T-SQL. A business intelligence e a pilha de análise da Microsoft, bem como ferramentas de terceiros compatíveis com o SQL Server, podem ser usadas com o polybase.

### <a name="azure-active-directory-setup"></a>Azure Active Directory configuração

[Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) é essencial para gerenciar a implantação e o provisionamento de acesso à equipe interagindo com o ambiente. Um Active Directory do Windows Server existente pode ser integrado com Azure Active Directory em [quatro cliques](../../active-directory/hybrid/how-to-connect-install-express.md). Os clientes também podem vincular a infraestrutura de Active Directory implantada (controladores de domínio) a um Azure Active Directory existente, tornando a infraestrutura de Active Directory implantada um subdomínio de uma floresta Azure Active Directory.

### <a name="optional-services"></a>Serviços opcionais

O Azure oferece uma variedade de serviços para ajudar no armazenamento e no preparo de dados formatados e não formatados. Os serviços a seguir podem ser adicionados a essa arquitetura de referência, dependendo dos requisitos do cliente:
-   O [Azure data Factory](https://docs.microsoft.com/azure/data-factory/introduction) é um serviço de nuvem gerenciado criado para projetos complexos de extração de transformação e carregamento de dados híbridos. O Azure Data Factory tem recursos para ajudar a rastrear e localizar dados financeiros, incluindo ferramentas de visualização e monitoramento para identificar quando os dados chegaram e de onde eles vieram. Usando Azure Data Factory, os clientes podem criar e agendar fluxos de trabalho controlados por dados chamados pipelines que insomem dados de armazenamentos de dados diferentes. Esses pipelines permitem que os clientes ingerirem dados de fontes internas e externas. Os clientes podem processar e transformar os dados para saída em armazenamentos de dados, como o Azure SQL Data Warehouse.
- Os clientes podem preparar dados não estruturados em [Azure data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview), o que permite a captura de dados de qualquer tamanho, tipo e velocidade de ingestão em um único lugar para análise operacional e exploratório.  Azure Data Lake tem recursos que permitem a extração e conversão de dados. Azure Data Lake Store é compatível com a maioria dos componentes de software livre no ecossistema do Hadoop e se integra perfeitamente com outros serviços do Azure, como o SQL Data Warehouse do Azure.

## <a name="disclaimer"></a>Exclusão de Responsabilidade

 - Este documento é apenas para fins informativos. A MICROSOFT NÃO OFERECE NENHUMA GARANTIA, EXPRESSA, IMPLÍCITA OU ESTATUTÁRIA, QUANTO ÀS INFORMAÇÕES CONTIDAS NESTE DOCUMENTO. Este documento é fornecido "no estado em que se encontra". As informações e as exibições expressas neste documento, incluindo URLs e outras referências de site da Internet, podem ser alteradas sem aviso prévio. Os clientes que lêem este documento trazem o risco de usá-lo.
 - Este documento não fornece aos clientes nenhum direito legal sobre qualquer propriedade intelectual em qualquer produto ou solução da Microsoft.
 - Os clientes podem copiar e usar este documento para fins de referência interna.
 - Determinadas recomendações neste documento podem resultar em maior uso de recursos de computação, rede ou dados no Azure, e podem aumentar os custos de assinatura ou de licença do Azure do cliente.
 - Essa arquitetura destina-se a servir como base para os clientes se ajustarem aos requisitos específicos e não devem ser usados no estado em que se encontram em um ambiente de produção.
 - Este documento é desenvolvido como uma referência e não deve ser usado para definir todos os meios pelos quais um cliente pode atender a requisitos e regulamentos específicos de conformidade. Os clientes devem buscar o suporte legal de sua organização em implementações aprovadas do cliente.
