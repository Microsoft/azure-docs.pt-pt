---
title: Blueprint de Segurança e Conformidade do Azure-data warehouse para NIST SP 800-171
description: Blueprint de Segurança e Conformidade do Azure-data warehouse para NIST SP 800-171
services: security
author: jomolesk
ms.assetid: dbc9cafe-115d-4965-b0d4-fcf235a064c8
ms.service: security
ms.topic: article
ms.date: 07/31/2018
ms.author: jomolesk
ms.openlocfilehash: d5c7ab3cc8d4de788da5d18f31c6aacb31b5d551
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71259884"
---
# <a name="azure-security-and-compliance-blueprint---data-warehouse-for-nist-sp-800-171"></a>Blueprint de Segurança e Conformidade do Azure-data warehouse para NIST SP 800-171

## <a name="overview"></a>Descrição geral
A [publicação especial do NIST 800-171](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) fornece diretrizes para proteger as informações não classificadas (CUI) controladas que residem em sistemas de informações e organizações não federais. O NIST SP 800-171 estabelece 14 famílias de requisitos de segurança para proteger a confidencialidade da CUI.

Este Blueprint de Segurança e Conformidade do Azure fornece diretrizes para ajudar os clientes a implantar uma arquitetura de data warehouse no Azure que implementa um subconjunto de controles NIST SP 800-171. Esta solução demonstra maneiras em que os clientes podem atender aos requisitos específicos de segurança e conformidade. Ele também serve como base para os clientes criarem e configurarem suas próprias soluções de data warehouse no Azure.

Essa arquitetura de referência, o guia de implementação associado e o modelo de ameaça devem servir como base para os clientes se adaptarem aos seus requisitos específicos. Eles não devem ser usados no estado em que se encontram em um ambiente de produção. Os clientes são responsáveis por realizar avaliações apropriadas de segurança e conformidade de qualquer solução criada usando essa arquitetura. Os requisitos podem variar com base nas especificidades da implementação de cada cliente.

## <a name="architecture-diagram-and-components"></a>Diagrama e componentes da arquitetura
Essa solução fornece uma arquitetura de referência que implementa uma data warehouse de nuvem segura e de alto desempenho. Há duas camadas de dados separadas nessa arquitetura. Uma camada é onde os dados são importados, armazenados e preparados em um ambiente SQL clusterizado. Outra camada é para o data warehouse do SQL. Com essa camada, os dados são carregados usando uma ferramenta ETL (extração de transformação/carregamento) (por exemplo, consultas T-SQL do [polybase](https://docs.microsoft.com/azure/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase) ) para processamento. Depois que os dados são armazenados no SQL Data Warehouse, a análise pode ser executada em grande escala.

O Azure oferece uma variedade de relatórios e serviços de análise para o cliente. Essa solução inclui SQL Server Reporting Services para a criação rápida de relatórios do SQL data warehouse. Todo o tráfego do SQL é criptografado com SSL por meio da inclusão de certificados autoassinados. Como prática recomendada, recomendamos o uso de uma autoridade de certificação confiável para aumentar a segurança.

O Azure SQL Data Warehouse armazena dados em tabelas relacionais com armazenamento de coluna. Esse formato reduz significativamente os custos de armazenamento de dados enquanto melhora o desempenho da consulta. Dependendo dos requisitos de uso, SQL Data Warehouse recursos de computação poderão ser escalados verticalmente ou reduzidos ou desligados completamente se nenhum processo ativo exigir recursos de computação.

Um balanceador de carga SQL Server gerencia o tráfego do SQL para garantir o alto desempenho. Todas as VMs (máquinas virtuais) nessa arquitetura de referência são implantadas em um conjunto de disponibilidade com SQL Server instâncias configuradas em um grupo de disponibilidade Always On. Essa configuração fornece recursos de alta disponibilidade e de recuperação de desastres.

Essa data warehouse arquitetura de referência também inclui uma camada de Active Directory para o gerenciamento de recursos dentro da arquitetura. A sub-rede Active Directory permite uma adoção fácil em uma estrutura de floresta de Active Directory maior. Dessa forma, o ambiente pode operar continuamente, mesmo quando o acesso à floresta maior não está disponível. Todas as VMs são ingressadas no domínio na camada de Active Directory. Eles usam Active Directory políticas de grupo para impor configurações de segurança e conformidade no nível do sistema operacional.

A solução usa contas de armazenamento do Azure, que os clientes podem configurar para usar o Criptografia do Serviço de Armazenamento para manter a confidencialidade dos dados em repouso. O Azure armazena três cópias de dados dentro de um data center selecionado do cliente para fins de resiliência. O armazenamento com redundância geográfica garante que os dados sejam replicados para um secundário data center a centenas de quilômetros de distância e armazenados novamente como três cópias dentro dessa data center. Essa organização impede que um evento adverso no data center principal do cliente resulte em uma perda de dados.

Para aumentar a segurança, todos os recursos nesta solução são gerenciados como um grupo de recursos por meio de Azure Resource Manager. O RBAC (controle de acesso baseado em função) do Azure Active Directory (Azure AD) é usado para controlar o acesso aos recursos implantados. Esses recursos incluem as chaves do cliente no Azure Key Vault. A integridade do sistema é monitorada por meio da central de segurança do Azure e Azure Monitor. Os clientes configuram ambos os serviços de monitoramento para capturar logs. A integridade do sistema é exibida em um único painel que é fácil de usar.

Uma VM serve como um host de bastiões de gerenciamento. Ele fornece uma conexão segura para os administradores acessarem os recursos implantados. Os dados são carregados na área de preparo por meio desse host de bastiões de gerenciamento. *Recomendamos que você configure uma conexão VPN ou Azure ExpressRoute para gerenciamento e importação de dados na sub-rede da arquitetura de referência.*

![Data warehouse para o diagrama da arquitetura de referência do NIST SP 800-171](images/nist171-dw-architecture.png "Data warehouse para o diagrama da arquitetura de referência do NIST SP 800-171")

Essa solução usa os seguintes serviços do Azure. Para obter mais informações, consulte a seção [arquitetura de implantação](#deployment-architecture) .

- Conjuntos de disponibilidade
    - Active Directory controladores de domínio
    - SQL Server de nós de cluster e testemunha
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
- Cofre de Serviços de Recuperação
- SQL Data Warehouse
- Serviços de Relatórios do SQL Server

## <a name="deployment-architecture"></a>Arquitetura de implantação
A seção a seguir detalha os elementos de implantação e implementação.

**SQL data warehouse do Azure**: [SQL data warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) é uma data warehouse empresarial que aproveita o processamento maciçomente paralelo para executar rapidamente consultas complexas em petabytes de dados. Os usuários podem usar consultas T-SQL simples do polybase para importar Big Data para o SQL data warehouse e usar o poder do processamento maciçomente paralelo para executar análises de alto desempenho.

**SQL Server Reporting Services**: [SQL Server Reporting Services](https://docs.microsoft.com/sql/reporting-services/report-data/sql-azure-connection-type-ssrs) fornece criação rápida de relatórios com tabelas, gráficos, mapas, medidores, matrizes e muito mais para SQL data warehouse.

**Catálogo de dados do Azure**: O [Catálogo de dados](../../data-catalog/overview.md) facilita a descoberta e a compreensão das fontes de dados pelos usuários que gerenciam os dados. As fontes de dados comuns podem ser registradas, marcadas e pesquisadas em busca de dados. Os dados permanecem em seu local existente, mas uma cópia de seus metadados é adicionada ao catálogo de dados. Uma referência ao local da fonte de dados está incluída. Os metadados são indexados para facilitar a descoberta de cada fonte de dados por meio de pesquisa. A indexação também o torna compreensível para os usuários que o descobrirem.

**Host bastião**: O host de bastiões é o ponto único de entrada que os usuários podem usar para acessar os recursos implantados nesse ambiente. O host bastião fornece uma conexão segura para recursos implantados, permitindo apenas o tráfego remoto de endereços IP públicos em uma lista segura. Para permitir o tráfego de área de trabalho remota, a origem do tráfego deve ser definida no grupo de segurança de rede.

Essa solução cria uma VM como um host de bastiões ingressado no domínio com as seguintes configurações:
-   [Extensão Antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware).
-   [Extensão de diagnóstico do Azure](../../virtual-machines/windows/extensions-diagnostics-template.md).
-   [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) usando Key Vault.
-   Uma [política de desligamento automático](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) para reduzir o consumo de recursos da VM quando não estiver em uso.
-   O [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) está habilitado para que as credenciais e outros segredos sejam executados em um ambiente protegido isolado do sistema operacional em execução.

### <a name="virtual-network"></a>Rede virtual
Essa arquitetura de referência define uma rede virtual privada com um espaço de endereço de 10.0.0.0/16.

**Grupos de segurança de rede**: [Grupos de segurança de rede](../../virtual-network/virtual-network-vnet-plan-design-arm.md) (NSGs) contém listas de controle de acesso que permitem ou negam o tráfego em uma rede virtual. NSGs pode ser usado para proteger o tráfego em uma sub-rede ou nível de VM individual. Os seguintes NSGs existem:
  - Um NSG para a camada de dados (SQL Server clusters, SQL Server testemunha e o balanceador de carga do SQL)
  - Um NSG para o host de bastiões de gerenciamento
  - Um NSG para Active Directory
  - Um NSG para SQL Server Reporting Services

Cada um dos NSGs tem portas e protocolos específicos abertos para que a solução possa funcionar de forma segura e correta. Além disso, as seguintes configurações estão habilitadas para cada NSG:
  - [Os logs e eventos de diagnóstico](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) são habilitados e armazenados em uma conta de armazenamento.
  - Os logs de Azure Monitor estão conectados ao [diagnóstico do NSG](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json).

**Sub-redes**: Cada sub-rede é associada ao seu NSG correspondente.

### <a name="data-at-rest"></a>Dados inativos
A arquitetura protege os dados em repouso por meio de várias medidas. Essas medidas incluem criptografia e auditoria de banco de dados.

**Armazenamento do Azure**: Para atender aos requisitos de dados criptografados em repouso, todo o [armazenamento](https://azure.microsoft.com/services/storage/) usa [criptografia do serviço de armazenamento](../../storage/common/storage-service-encryption.md). Esse recurso ajuda a proteger e proteger os dados para dar suporte a compromissos de segurança organizacional e requisitos de conformidade.

**Azure Disk Encryption**: A [criptografia de disco](../azure-security-disk-encryption-overview.md) usa o recurso BitLocker do Windows para fornecer criptografia de volume para o sistema operacional e discos de dados. A solução se integra com Key Vault para ajudar a controlar e gerenciar as chaves de criptografia de disco.

**Banco de dados SQL do Azure**: A instância do banco de dados SQL usa as seguintes medidas de segurança de banco de dados:
-   [Active Directory autenticação e autorização](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) permitem o gerenciamento de identidades de usuários de banco de dados e outros serviços da Microsoft em um local central.
-   A [auditoria do banco de dados SQL](../../sql-database/sql-database-auditing.md) rastreia eventos de banco de dados e os grava em um log de auditoria em uma conta de armazenamento do Azure.
-   O banco de dados SQL está configurado para usar a [Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql). Ele executa criptografia e descriptografia em tempo real do banco de dados, backups associados e arquivos de log de transações para proteger informações em repouso. A Transparent Data Encryption fornece garantia de que os dados armazenados não estão sujeitos a acesso não autorizado.
-   [As regras de firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) impedem todo o acesso a servidores de banco de dados até que sejam concedidas permissões adequadas A firewall concede acesso a bases de dados com base no endereço IP de origem de cada pedido.
-   A [detecção de ameaças do SQL](../../sql-database/sql-database-threat-detection.md) permite a detecção e a resposta a ameaças potenciais à medida que elas ocorrem. Ele fornece alertas de segurança para atividades suspeitas de banco de dados, possíveis vulnerabilidades, ataques de injeção de SQL e padrões de acesso de banco de dados anormais.
-   As [colunas criptografadas](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) garantem que os dados confidenciais nunca apareçam como texto sem formatação no sistema de banco de dados. Depois que a criptografia de dados é habilitada, somente aplicativos cliente ou servidores de aplicativos com acesso às chaves podem acessar dados de texto sem formatação.
- [As propriedades estendidas](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql) podem ser usadas para interromper o processamento de entidades de dados. Os usuários podem adicionar propriedades personalizadas a objetos de banco de dados. Eles também podem marcar dados como "descontinuados" para dar suporte à lógica do aplicativo para evitar o processamento de dados financeiros associados.
- A [segurança em nível de linha](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) permite que os usuários definam políticas para restringir o acesso aos dados para o processamento descontinuado.
- O [mascaramento de dados dinâmicos do Database do SQL](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) limita a exposição de dados confidenciais mascarando os dados para usuários ou aplicativos sem privilégios. Ele pode descobrir dados potencialmente confidenciais automaticamente e sugerir as máscaras apropriadas a serem aplicadas. O mascaramento de dados dinâmicos ajuda a reduzir o acesso para que os dados confidenciais não saiam do banco por meio de acesso não autorizado. *Os clientes são responsáveis por ajustar as configurações para aderir ao seu esquema de banco de dados.*

### <a name="identity-management"></a>Gestão de identidades
As tecnologias a seguir fornecem recursos para gerenciar o acesso aos dados no ambiente do Azure:
-   O [Azure ad](https://azure.microsoft.com/services/active-directory/) é o serviço de gerenciamento de identidade e diretório baseado em nuvem multilocatário da Microsoft. Todos os usuários dessa solução são criados no Azure AD e incluem os usuários que acessam o banco de dados SQL.
-   A autenticação para o aplicativo é executada usando o Azure AD. Para obter mais informações, consulte como [integrar aplicativos com o Azure ad](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). A criptografia de coluna de banco de dados também usa o Azure AD para autenticar o aplicativo no banco de dados SQL. Para obter mais informações, Confira como [proteger dados confidenciais no SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   O [RBAC do Azure](../../role-based-access-control/role-assignments-portal.md) pode ser usado por administradores para definir permissões de acesso refinadas. Com ele, eles podem conceder apenas a quantidade de acesso que os usuários precisam para executar seus trabalhos. Em vez de fornecer a cada usuário acesso irrestrito aos recursos do Azure, os administradores podem permitir apenas determinadas ações para acessar recursos e dados. O acesso à assinatura é limitado ao administrador da assinatura.
- [Azure Active Directory Privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md) pode ser usado por clientes para minimizar o número de usuários que têm acesso a determinadas informações, como dados. Os administradores podem usar Azure AD Privileged Identity Management para descobrir, restringir e monitorar identidades com privilégios e seu acesso aos recursos. Essa funcionalidade também pode ser usada para impor acesso administrativo sob demanda e Just-in-time quando necessário.
- [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview.md) detecta possíveis vulnerabilidades que afetam as identidades de uma organização. Ele configura as respostas automatizadas para detectar ações suspeitas relacionadas às identidades de uma organização. Ele também investiga incidentes suspeitos para tomar as medidas apropriadas para resolvê-los.

### <a name="security"></a>Segurança
**Gerenciamento de segredos**: A solução usa [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) para o gerenciamento de chaves e segredos. Key Vault ajuda a proteger chaves criptográficas e segredos usados por aplicativos e serviços em nuvem. Os recursos de Key Vault a seguir ajudam os clientes a proteger os dados:
- As políticas de acesso avançadas são configuradas de acordo com a necessidade.
- Key Vault políticas de acesso são definidas com as permissões mínimas necessárias para chaves e segredos.
- Todas as chaves e segredos em Key Vault têm datas de expiração.
- Todas as chaves em Key Vault são protegidas por módulos de segurança de hardware especializados. O tipo de chave é uma chave RSA de segurança de hardware-módulo protegido de 2048 bits.
- Todos os usuários e identidades recebem as permissões mínimas necessárias usando o RBAC.
- Os logs de diagnóstico para Key Vault estão habilitados com um período de retenção de pelo menos 365 dias.
- As operações de criptografia permitidas para chaves são restritas às necessárias.

**Gerenciamento de patches**: As VMs do Windows implantadas como parte dessa arquitetura de referência são configuradas por padrão para receber atualizações automáticas do serviço Windows Update. Essa solução também inclui o serviço de [automação do Azure](https://docs.microsoft.com/azure/automation/automation-intro) por meio do qual implantações atualizadas podem ser criadas para corrigir as VMs quando necessário.

**Proteção contra malware**: [O Microsoft Antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware) para VMs fornece recursos de proteção em tempo real que ajudam a identificar e remover vírus, spyware e outros softwares mal-intencionados. Os clientes podem configurar alertas que geram quando softwares mal-intencionados ou indesejados conhecidos tentam ser instalados ou executados em VMs protegidas.

**Central de segurança do Azure**: Com a [central de segurança](https://docs.microsoft.com/azure/security-center/security-center-intro), os clientes podem aplicar e gerenciar centralmente políticas de segurança entre cargas de trabalho, limitar a exposição a ameaças e detectar e responder a ataques. A central de segurança também acessa as configurações existentes dos serviços do Azure para fornecer recomendações de configuração e serviço para ajudar a melhorar a postura de segurança e proteger os dados.

A central de segurança usa uma variedade de recursos de detecção para alertar os clientes sobre possíveis ataques direcionados a seus ambientes. Estes alertas contêm informações valiosas sobre o que acionou o alerta, os recursos afetados e a origem do ataque. A central de segurança tem um conjunto de [alertas de segurança predefinidos](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) que são disparados quando ocorre uma ameaça ou atividade suspeita. Os clientes podem usar [regras de alerta personalizadas](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) para definir novos alertas de segurança com base nos dados que já foram coletados de seu ambiente.

A central de segurança fornece incidentes e alertas de segurança priorizados. A central de segurança torna mais simples para os clientes descobrir e resolver problemas potenciais de segurança. Um [relatório de inteligência contra ameaças](https://docs.microsoft.com/azure/security-center/security-center-threat-report) é gerado para cada ameaça detectada. As equipes de resposta a incidentes podem usar os relatórios ao investigar e corrigir ameaças.

Essa arquitetura de referência também usa o recurso de [avaliação de vulnerabilidade](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations) na central de segurança. Depois de configurado, um agente de parceiro (por exemplo, Qualys) relata dados de vulnerabilidade para a plataforma de gerenciamento do parceiro. Por sua vez, a plataforma de gestão do parceiro fornece dados de vulnerabilidades e de estado de funcionamento de volta ao Centro de Segurança. Os clientes podem usar essas informações para identificar rapidamente VMs vulneráveis.

### <a name="business-continuity"></a>Continuidade do negócio
**Elevada disponibilidade**: As cargas de trabalho do servidor são agrupadas em um [conjunto de disponibilidade](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para ajudar a garantir a alta disponibilidade de VMs no Azure. Pelo menos uma VM está disponível durante um evento de manutenção planejado ou não planejado, que atende ao SLA de 99,95% do Azure.

**Cofre dos serviços de recuperação**: O [cofre dos serviços de recuperação](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) hospeda dados de backup e protege todas as configurações de VMs nessa arquitetura. Com um cofre dos serviços de recuperação, os clientes podem restaurar arquivos e pastas de uma VM IaaS sem restaurar toda a VM. Esse processo acelera os tempos de restauração.

### <a name="logging-and-auditing"></a>Registo e auditoria

Os serviços do Azure registram extensivamente a atividade do sistema e do usuário, bem como a integridade do sistema:
- **Logs de atividade**: [Os logs de atividade](../../azure-monitor/platform/activity-logs-overview.md) fornecem informações sobre as operações executadas nos recursos em uma assinatura. Os logs de atividades podem ajudar a determinar o iniciador de uma operação, a hora de ocorrência e o status.
- **Logs de diagnóstico**: Os [logs de diagnóstico](../../azure-monitor/platform/resource-logs-overview.md) incluem todos os logs emitidos por cada recurso. Esses logs incluem logs do sistema de eventos do Windows, logs de armazenamento, Key Vault logs de auditoria e Aplicativo Azure acesso ao gateway e logs de firewall. Todos os logs de diagnóstico gravam em uma conta de armazenamento do Azure centralizada e criptografada para arquivamento. Os usuários podem configurar o período de retenção, até 730 dias, para atender a seus requisitos específicos.

**Logs de Azure monitor**: Esses logs são consolidados em [logs de Azure monitor](https://azure.microsoft.com/services/log-analytics/) para processamento, armazenamento e relatórios de Dashboard. Depois que os dados são coletados, eles são organizados em tabelas separadas para cada tipo de dados dentro dos espaços de trabalho Log Analytics. Dessa forma, todos os dados podem ser analisados juntos, independentemente de sua fonte original. A central de segurança se integra aos logs de Azure Monitor. Os clientes podem usar consultas Kusto para acessar seus dados de eventos de segurança e combiná-los com dados de outros serviços.

As seguintes [soluções de monitoramento](../../monitoring/monitoring-solutions.md) do Azure estão incluídas como parte dessa arquitetura:
-   [Avaliação de Active Directory](../../azure-monitor/insights/ad-assessment.md): A solução de verificação de integridade Active Directory avalia o risco e a integridade de ambientes de servidor em intervalos regulares. Ele fornece uma lista priorizada de recomendações específicas para a infraestrutura de servidor implantada.
- [Avaliação do SQL](../../azure-monitor/insights/sql-assessment.md): A solução de verificação da integridade do SQL avalia o risco e a integridade de ambientes de servidor em intervalos regulares. Ele fornece aos clientes uma lista priorizada de recomendações específicas para a infraestrutura de servidor implantada.
- [Integridade do agente](../../monitoring/monitoring-solution-agenthealth.md): A solução Integridade do Agente relata Quantos agentes são implantados e sua distribuição geográfica. Ele também relata Quantos agentes não respondem e o número de agentes que enviam dados operacionais.
-   [Análise do log de atividades](../../azure-monitor/platform/collect-activity-logs.md): A solução Análise do Log de Atividades auxilia na análise dos logs de atividade do Azure em todas as assinaturas do Azure para um cliente.

**Automação do Azure**: A [automação](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) armazena, executa e gerencia runbooks. Nesta solução, os runbooks ajudam a coletar logs do banco de dados SQL. Os clientes podem usar a solução de [controle de alterações](../../automation/change-tracking.md) de automação para identificar facilmente as alterações no ambiente.

**Azure monitor**: O [Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) ajuda os usuários a controlar o desempenho, manter a segurança e identificar tendências. As organizações podem usá-lo para auditar, criar alertas e arquivar dados. Eles também podem acompanhar chamadas de API em seus recursos do Azure.

## <a name="threat-model"></a>Modelo de ameaça

O diagrama de fluxo de dados para esta arquitetura de referência está disponível para [Download](https://aka.ms/nist171-dw-tm) ou pode ser encontrado aqui. Esse modelo pode ajudar os clientes a entender os pontos de risco potencial na infraestrutura do sistema quando fazem modificações.

![Data warehouse para o modelo de ameaça NIST SP 800-171](images/nist171-dw-threat-model.png "Data warehouse para o modelo de ameaça NIST SP 800-171")

## <a name="compliance-documentation"></a>Documentação de conformidade
A [matriz de responsabilidade do cliente Blueprint de segurança e conformidade do Azure – NIST SP 800-171](https://aka.ms/nist171-crm) lista todos os controles de segurança exigidos pelo NIST SP 800-171. Essa matriz detalha se a implementação de cada controle é de responsabilidade da Microsoft, do cliente ou do compartilhamento entre os dois.

A [matriz de implementação de controle do Blueprint de segurança e conformidade do Azure-NIST sp 800-171 data warehouse](https://aka.ms/nist171-dw-cim) fornece informações sobre quais controles do NIST SP 800-171 são cobertos pela arquitetura de data warehouse. Ele inclui descrições detalhadas de como a implementação atende aos requisitos de cada controle abordado.

## <a name="guidance-and-recommendations"></a>Diretrizes e recomendações

### <a name="vpn-and-expressroute"></a>VPN e ExpressRoute
Um túnel VPN seguro ou o [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) deve ser configurado para estabelecer com segurança uma conexão com os recursos implantados como parte dessa data warehouse arquitetura de referência. Ao configurar adequadamente uma VPN ou ExpressRoute, os clientes podem adicionar uma camada de proteção para os dados em trânsito.

Ao implementar um túnel VPN seguro com o Azure, uma conexão privada virtual entre uma rede local e uma rede virtual do Azure pode ser criada. Esta conexão ocorre pela Internet. Os clientes podem usar essa conexão para obter informações de "túnel" com segurança dentro de um link criptografado entre a rede do cliente e o Azure. A VPN site a site é uma tecnologia segura e madura, que foi implantada por empresas de todos os tamanhos por décadas. O [modo de encapsulamento IPSec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) é usado nessa opção como um mecanismo de criptografia.

Como o tráfego no túnel VPN atravessa a Internet com uma VPN site a site, a Microsoft oferece outra opção de conexão ainda mais segura. O ExpressRoute é um link WAN dedicado entre o Azure e um local ou um provedor de hospedagem do Exchange. As conexões do ExpressRoute se conectam diretamente ao provedor de telecomunicação do cliente. Como resultado, os dados não viajam pela Internet e não são expostos a ele. Essas conexões oferecem mais confiabilidade, velocidades mais rápidas, latências menores e maior segurança do que as conexões típicas.

As práticas recomendadas para implementar uma rede híbrida segura que estende uma rede local para o Azure estão [disponíveis](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

### <a name="extract-transform-load-process"></a>Processo de extração-transformação-carregamento
O [polybase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) pode carregar dados em SQL data warehouse sem a necessidade de um ETL separado ou uma ferramenta de importação. O polybase permite o acesso a dados por meio de consultas T-SQL. O Microsoft business intelligence e a pilha de análise e ferramentas de terceiros que são compatíveis com SQL Server podem ser usadas com o polybase.

### <a name="azure-ad-setup"></a>Instalação do Azure AD
O [Azure ad](../../active-directory/fundamentals/active-directory-whatis.md) é essencial para gerenciar a implantação e o acesso de provisionamento à equipe que interage com o ambiente. Os Active Directory locais podem ser integrados ao Azure AD em [quatro cliques](../../active-directory/hybrid/how-to-connect-install-express.md). Os clientes também podem vincular a infraestrutura de Active Directory implantada (controladores de domínio) ao Azure AD. Para fazer isso, torne a infraestrutura de Active Directory implantada um subdomínio de uma floresta do Azure AD.

### <a name="optional-services"></a>Serviços opcionais
O Azure oferece uma variedade de serviços para ajudar no armazenamento e no preparo de dados formatados e não formatados. Os serviços a seguir podem ser adicionados a essa arquitetura de referência com base nos requisitos do cliente:
-   O [Azure data Factory](https://docs.microsoft.com/azure/data-factory/introduction) é um serviço de nuvem gerenciado criado para projetos complexos de ETL híbrido, extração de carga e transformação de dados. Data Factory tem recursos para ajudar a rastrear e localizar dados. As ferramentas de visualização e monitoramento identificam quando os dados chegaram e de onde vieram. Os clientes podem criar e agendar fluxos de trabalho orientados a dados, chamados de pipelines, que ingerirão dados de armazenamentos de dados diferentes. Eles podem usar os pipelines para ingerir dados de fontes internas e externas. Os clientes podem processar e transformar os dados para saída em armazenamentos de dados, como SQL Data Warehouse.
- Os clientes podem preparar dados não estruturados em [Azure data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) para capturar dados de qualquer tamanho, tipo e velocidade de ingestão em um único lugar para análise operacional e exploratório. Azure Data Lake tem recursos que permitem a extração e conversão de dados. Data Lake Store é compatível com a maioria dos componentes de software livre no ecossistema do Hadoop. Ele também se integra perfeitamente com outros serviços do Azure, como SQL Data Warehouse.

## <a name="disclaimer"></a>Exclusão de Responsabilidade

 - Este documento é apenas para fins informativos. A MICROSOFT NÃO OFERECE NENHUMA GARANTIA, EXPRESSA, IMPLÍCITA OU ESTATUTÁRIA, QUANTO ÀS INFORMAÇÕES CONTIDAS NESTE DOCUMENTO. Este documento é fornecido "no estado em que se encontra". As informações e as exibições expressas neste documento, incluindo URLs e outras referências de site da Internet, podem ser alteradas sem aviso prévio. Os clientes que lêem este documento trazem o risco de usá-lo.
 - Este documento não fornece aos clientes nenhum direito legal sobre qualquer propriedade intelectual em qualquer produto ou solução da Microsoft.
 - Os clientes podem copiar e usar este documento para fins de referência interna.
 - Determinadas recomendações neste documento podem resultar em maior uso de recursos de computação, rede ou dados no Azure, e podem aumentar os custos de assinatura ou de licença do Azure do cliente.
 - Essa arquitetura destina-se a servir como base para os clientes se ajustarem aos requisitos específicos e não devem ser usados no estado em que se encontram em um ambiente de produção.
 - Este documento é desenvolvido como uma referência e não deve ser usado para definir todos os meios pelos quais um cliente pode atender a requisitos e regulamentos específicos de conformidade. Os clientes devem buscar o suporte legal de sua organização em implementações aprovadas do cliente.
