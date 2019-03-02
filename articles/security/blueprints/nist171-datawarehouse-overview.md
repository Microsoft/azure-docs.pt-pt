---
title: Segurança do Azure e o esquema de conformidade – armazém de dados para SP do NIST 800-171
description: Segurança do Azure e o esquema de conformidade – armazém de dados para SP do NIST 800-171
services: security
author: jomolesk
ms.assetid: dbc9cafe-115d-4965-b0d4-fcf235a064c8
ms.service: security
ms.topic: article
ms.date: 07/31/2018
ms.author: jomolesk
ms.openlocfilehash: a1850ecfbb21eb9495bb0e6de362dc8dee3026a2
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57242365"
---
# <a name="azure-security-and-compliance-blueprint---data-warehouse-for-nist-sp-800-171"></a>Segurança do Azure e o esquema de conformidade – armazém de dados para SP do NIST 800-171

## <a name="overview"></a>Descrição geral
[Publicação especial do NIST 800-171](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) fornece diretrizes para proteger a informação de não controlada (CUI) que residem nos sistemas de informação nonfederal e organizações. SP do NIST 800-171 estabelece 14 famílias de requisitos de segurança para proteger a confidencialidade das CUI.

Este Azure no Security and Compliance Blueprint disponibiliza orientações para ajudar os clientes a implementar uma arquitetura de armazém de dados no Azure que implementa um subconjunto de NIST SP 800-171 controles. Esta solução demonstra maneiras em que os clientes podem satisfazer requisitos de conformidade e de segurança específicos. Ele também serve como uma base para os clientes criar e configurar suas próprias soluções de armazém de dados no Azure.

Esta arquitetura de referência, o guia de implementação associados e o modelo de risco destinam-se a servir de base para os clientes para se adaptar às suas necessidades específicas. Não deve ser usados como-está num ambiente de produção. Os clientes são responsáveis pela realização de avaliações de conformidade de qualquer solução criados com esta arquitetura e segurança adequada. Requisitos podem variar com base nas especificidades da implementação de cada cliente.

## <a name="architecture-diagram-and-components"></a>Diagrama de arquitetura e componentes
Esta solução fornece uma arquitetura de referência que implementa um armazém de dados de cloud seguro e de alto desempenho. Nesta arquitetura, existem duas camadas de dados separado. Uma camada é onde os dados são importados, armazenada e de teste num ambiente em cluster do SQL. Outra camada destina-se o SQL data warehouse. Com esta camada, os dados são carregados com uma ferramenta de extract-transform-load (ETL) (por exemplo, [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase) consultas de T-SQL) para processamento. Depois de dados são armazenados no SQL Data Warehouse, podem executar análises em grande escala.

O Azure oferece uma variedade de serviços de relatórios e análise do cliente. Esta solução inclui o SQL Server Reporting Services para a criação rápida de relatórios do armazém de dados SQL. Todo o tráfego SQL é encriptado com SSL através da inclusão de certificados autoassinados. Como melhor prática, recomendamos a utilização de uma autoridade de certificação fidedigna para uma maior segurança.

O Azure SQL Data Warehouse armazena dados em tabelas relacionais do armazenamento em colunas. Este formato reduz significativamente os custos de armazenamento de dados enquanto melhora o desempenho de consulta. Dependendo dos requisitos de utilização, os recursos de computação de armazém de dados do SQL podem ser aumentados vertical ou horizontalmente ou desativar completamente se não existem processos ativos exigirem recursos de computação.

Um balanceador de carga do SQL Server gere o tráfego SQL para garantir um desempenho elevado. Implementar a todas as máquinas virtuais (VMs) nesta arquitetura de referência num conjunto de disponibilidade com instâncias do SQL Server configuradas num grupo de disponibilidade Always On. Esta configuração fornece capacidades de elevada disponibilidade e recuperação após desastre.

Esta arquitetura de referência do armazém de dados também inclui uma camada de Active Directory para gestão de recursos dentro da arquitetura. A sub-rede do Active Directory permite a adoção fácil numa estrutura de floresta do Active Directory maior. Dessa forma, o ambiente pode operar continuamente, mesmo quando o acesso para a floresta maior está indisponível. Todas as VMs têm um domínio associado para o escalão do Active Directory. Utilizam políticas de grupo do Active Directory para impor configurações de segurança e conformidade ao nível do sistema operativo.

A solução utiliza contas de armazenamento do Azure, o que os clientes podem configurar para utilizar a encriptação do serviço de armazenamento para manter a confidencialidade dos dados em repouso. Azure armazena três cópias dos dados no Centro de dados selecionados de um cliente para resiliência. Armazenamento georredundante garante que os dados são replicados para um centro de dados secundária centenas de quilómetros de distância e novamente armazenadas como três cópias dentro desse Datacenter. Essa organização impede que um evento adverso no Centro de dados principal do cliente resulta numa perda de dados.

Para maior segurança, todos os recursos nesta solução são geridos como um grupo de recursos através do Gestor de recursos do Azure. O Azure Active Directory, controlo de acesso baseado em funções (Azure AD) (RBAC) é utilizado para controlar o acesso a recursos implementados. Esses recursos incluem as chaves de cliente no Azure Key Vault. Estado de funcionamento do sistema é monitorizado através do Centro de segurança do Azure e o Azure Monitor. Os clientes configurar ambos os serviços de monitorização para capturar os registos. Estado de funcionamento do sistema é exibido num único dashboard, é fácil de usar.

Uma VM funciona como um anfitrião de bastião de gestão. Ele fornece uma ligação segura para os administradores de recursos de acesso implementado. O carregamento dos dados para a área de teste por meio deste anfitrião de bastião de gestão. *Recomendamos que configure uma ligação VPN ou Azure ExpressRoute para importação de dados e de gestão para a sub-rede de arquitetura de referência.*

![Armazém de dados para NIST SP 800-171 diagrama da arquitetura de referência](images/nist171-dw-architecture.png "do armazém de dados para NIST SP 800-171 diagrama da arquitetura de referência")

Esta solução utiliza os seguintes serviços do Azure. Para obter mais informações, consulte a [arquitetura de implantação](#deployment-architecture) secção.

- Conjuntos de disponibilidade
    - Controladores de domínio do Active Directory
    - Nós de cluster do SQL Server e da testemunha
- Azure Active Directory
- Catálogo de Dados do Azure
- Azure Key Vault
- O Azure Monitor (logs)
- Centro de Segurança do Azure
- Azure Load Balancer
- Storage do Azure
- Máquinas Virtuais do Azure
    - (1) anfitrião de bastião
    - (2) controlador de domínio do as Active Directory
    - (2) nó de cluster o SQL Server
    - (1) testemunha o SQL Server
- Rede Virtual do Azure
    - rede de /16 (1)
    - redes de /24 (4)
    - (4) grupos de segurança de rede
- Cofre dos Serviços de Recuperação
- SQL Data Warehouse
- Serviços de Relatórios do SQL Server

## <a name="deployment-architecture"></a>Arquitetura de implantação
A secção seguinte fornece detalhes sobre os elementos de implantação e a implementação.

**Azure SQL Data Warehouse**: [O SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) é um armazém de dados empresarial que tira proveito do processamento paralelo em grande escala para executar rapidamente consultas complexas em petabytes de dados. Os utilizadores podem utilizar consultas simples de PolyBase T-SQL para importar grandes quantidades de dados para o SQL data warehouse e utilize o poder de processamento paralelo em grande escala para executar análises de elevado desempenho.

**SQL Server Reporting Services**: [SQL Server Reporting Services](https://docs.microsoft.com/sql/reporting-services/report-data/sql-azure-connection-type-ssrs) fornece a rápida criação de relatórios com tabelas, gráficos, mapas, medidores, matrizes e mais para o SQL Data Warehouse.

**Catálogo de dados do Azure**: [Catálogo de dados](https://docs.microsoft.com/azure/data-catalog/data-catalog-what-is-data-catalog) facilita a detetar e compreender pelos utilizadores que gerem os dados de origens de dados. Podem ser registadas, marcadas e está à procura dados de origens de dados comuns. Os dados permanecem na localização existente, mas uma cópia dos respetivos metadados é adicionada ao catálogo de dados. Uma referência para a localização de origem de dados está incluída. Os metadados são indexados para tornar cada origem de dados fácil de descobrir através da pesquisa. Indexação também torna compreensível para os utilizadores que a detetarem.

**Anfitrião de bastião**: O anfitrião de bastião é o único ponto de entrada que os utilizadores podem utilizar para aceder os recursos implementados neste ambiente. O anfitrião de bastião fornece uma ligação segura a recursos implementados, permitindo apenas tráfego remoto de endereços IP públicos numa lista segura. Para permitir o tráfego de ambiente de trabalho remoto, a origem do tráfego deve ser definida no grupo de segurança de rede.

Esta solução cria uma VM como um anfitrião de bastião associados a um domínio com as seguintes configurações:
-   [Extensão de antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware).
-   [Extensão de diagnóstico do Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template).
-   [O Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) com o Key Vault.
-   Uma [política de encerramento automático](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) para reduzir o consumo de recursos VM quando não está em utilização.
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) está ativada para que as credenciais e outros segredos executar num ambiente protegido isolada do sistema operativo em execução.

### <a name="virtual-network"></a>Rede virtual
Esta arquitetura de referência define uma rede privada virtual com um espaço de endereços de 10.0.0.0/16.

**Grupos de segurança de rede**: [Grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) (NSGs) contêm listas de controlo de acesso que permitem ou negam o tráfego dentro de uma rede virtual. Podem ser utilizados NSGs para proteger o tráfego numa sub-rede ou o nível VM individual. Existem os NSGs seguintes:
  - Um NSG para a camada de dados (clusters do SQL Server, testemunho do SQL Server e o Balanceador de carga SQL)
  - Um NSG para o anfitrião de bastião de gestão
  - Um NSG para o Active Directory
  - Um NSG para o SQL Server Reporting Services

Cada um dos NSGs tem portas específicas e protocolos abra para que a solução possam funcionar corretamente e de forma segura. Além disso, as seguintes configurações são habilitadas para cada NSG:
  - [Eventos e registos de diagnóstico](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) forem ativadas e armazenados numa conta de armazenamento.
  - Registos de Monitor do Azure está ligada a [diagnóstico do NSG](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json).

**Sub-redes**: Cada sub-rede está associado a seu NSG correspondente.

### <a name="data-at-rest"></a>Dados inativos
A arquitetura protege dados em repouso através de várias medidas. Estas medidas incluem a criptografia e auditoria de base de dados.

**O armazenamento do Azure**: Para cumprir os requisitos para os dados encriptados em inatividade, todos os [armazenamento](https://azure.microsoft.com/services/storage/) utiliza [Storage Service Encryption](https://docs.microsoft.com/azure/storage/storage-service-encryption). Esta funcionalidade ajuda a proteger e salvaguardar os dados para oferecer suporte a requisitos de conformidade e de compromissos de segurança organizacional.

**Azure Disk Encryption**: [Encriptação de disco](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) utiliza a funcionalidade BitLocker do Windows para fornecer a criptografia de volume de sistema operativo e discos de dados. A solução se integra com o Key Vault para ajudar a controlar e gerir as chaves de encriptação de disco.

**Base de dados SQL do Azure**: A instância de base de dados SQL utiliza as seguintes medidas de segurança da base de dados:
-   [Autenticação do Active Directory e a autorização](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) permite a gestão de identidades de utilizadores de base de dados e outros serviços da Microsoft num local central.
-   [Auditoria de base de dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) faixas de base de dados eventos e escreve-os para uma auditoria registo numa conta de armazenamento do Azure.
-   Base de dados SQL está configurado para utilizar [encriptação de dados transparente](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql). Ele executa criptografia em tempo real e a descriptografia da base de dados, cópias de segurança associadas e ficheiros de registo de transação para proteger as informações em repouso. Encriptação de dados transparente fornece garantia de que são armazenados os dados não tem sido sujeita a acesso não autorizado.
-   [Regras de firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) impedir o acesso de todos os servidores de base de dados até que sejam concedidas as permissões apropriadas. A firewall concede acesso a bases de dados com base no endereço IP de origem de cada pedido.
-   [Deteção de ameaças SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) permite a deteção e resposta a ameaças potenciais à medida que ocorrem. Ele fornece alertas de segurança para atividades suspeitas da base de dados, potenciais vulnerabilidades, ataques de injeção de SQL e padrões de acesso de base de dados anómalas.
-   [Encriptados colunas](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) Certifique-se de que os dados confidenciais nunca é apresentada como texto simples dentro do sistema de base de dados. Depois de é ativada a encriptação de dados, apenas as aplicações de cliente ou servidores de aplicações com acesso às chaves podem aceder a dados de texto sem formatação.
- [Propriedades estendidas](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql) pode ser utilizado para descontinuar o processamento de titulares dos dados. Os usuários podem adicionar propriedades personalizadas para objetos de base de dados. Eles também podem marcar os dados como "Discontinued" para suportar a lógica de aplicação para impedir que o processamento de dados financeiros associados.
- [Segurança ao nível da linha](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) permite aos utilizadores definir políticas para restringir o acesso aos dados de descontinuar o processamento.
- [Máscara de dados dinâmicos da base de dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) limita a exposição de dados confidenciais ao mascará os dados de utilizadores não privilegiados ou aplicações. É, automaticamente, pode detetar dados potencialmente confidenciais e sugerir as máscaras de apropriado a ser aplicado. Máscara de dados dinâmica ajuda a reduzir o acesso, de modo a que dados confidenciais não sair da base de dados por meio do acesso não autorizado. *Os clientes são responsáveis por ajustar as definições de cumprir para seu esquema de base de dados.*

### <a name="identity-management"></a>Gestão de identidades
As seguintes tecnologias fornecem capacidades para gerir o acesso aos dados no ambiente do Azure:
-   [O Azure AD](https://azure.microsoft.com/services/active-directory/) é o Microsoft multi-inquilino com base na cloud diretório e identidade do serviço de gestão. Todos os utilizadores para esta solução são criados no Azure AD e incluem os usuários que acessam o banco de dados SQL.
-   Autenticação para a aplicação é executada ao utilizar o Azure AD. Para obter mais informações, consulte como [integrar aplicações com o Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). A encriptação de coluna de base de dados também utiliza o Azure AD para autenticar a aplicação para a base de dados SQL. Para obter mais informações, consulte como [proteger dados confidenciais na base de dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [RBAC do Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) podem ser utilizadas pelos administradores para definir permissões de acesso detalhado. Com ele, eles podem conceder apenas a quantidade de acesso que os utilizadores precisam realizar seus trabalhos. Em vez de dar a cada acesso de utilizador sem restrições para recursos do Azure, os administradores podem permitir apenas certas ações para aceder a recursos e dados. Acesso à subscrição está limitado ao administrador da subscrição.
- [O Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) pode ser utilizado pelos clientes para minimizar o número de utilizadores que têm acesso a determinadas informações, tais como os dados. Os administradores podem utilizar o Azure AD Privileged Identity Management para detetar, restringir e monitorizar identidades privilegiadas e respetivo acesso a recursos. Esta funcionalidade também pode ser utilizada para impor acesso administrativo a pedido e just-in-time quando necessário.
- [O Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) Deteta potenciais vulnerabilidades que afetam as identidades de uma organização. Configura as respostas automáticas para detetado ações suspeitas relacionadas com identidades de uma organização. Também é mostrada a incidentes suspeitos para tomar as medidas adequadas para resolvê-los.

### <a name="security"></a>Segurança
**Gestão de segredos**: A solução utiliza [do Azure Key Vault](https://azure.microsoft.com/services/key-vault/) para o gerenciamento de chaves e segredos. Key Vault ajuda a salvaguardar as chaves criptográficas e segredos utilizados pelas aplicações e serviços cloud. As seguintes capacidades de Key Vault ajudam os clientes a proteger os dados:
- Políticas de acesso avançadas são configuradas de forma necessidade.
- Políticas de acesso do Cofre de chaves são definidas com o mínimo permissões necessárias para chaves e segredos.
- Todas as chaves e segredos no Key Vault tem datas de expiração.
- Todas as chaves no Cofre de chaves estão protegidas por módulos de segurança de hardware especializado. O tipo de chave é uma chave RSA de 2048 bits segurança--protegidas por módulo de hardware.
- Todos os utilizadores e identidades são concedidas as permissões necessárias mínimas utilizando o RBAC.
- Registos de diagnóstico para o Cofre de chaves estão ativados com um período de retenção de, pelo menos, 365 dias.
- Operações de criptografia permitidas para as chaves estão limitadas aos obrigatórios.

**Gerenciamento de patches**: VMs do Windows implementadas como parte desta arquitetura de referência estão configuradas por predefinição para receber atualizações automáticas do serviço de atualização do Windows. Esta solução também inclui a [automatização do Azure](https://docs.microsoft.com/azure/automation/automation-intro) serviço por meio do qual as implementações atualizadas podem ser criadas para o patch VMs quando necessário.

**Proteção contra Malware**: [O Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) para VMs fornece a capacidade de proteção em tempo real que ajuda a identifica e remove vírus, spyware e outro software malicioso. Os clientes podem configurar alertas que geram quando conhecido software malicioso ou indesejável se tentar instalar ou executar em VMs protegidas.

**Centro de segurança do Azure**: Com o [Centro de segurança](https://docs.microsoft.com/azure/security-center/security-center-intro), os clientes podem centralmente aplicam-se e gerir políticas de segurança em cargas de trabalho, limitar a exposição a ameaças e detetar e responder a ataques. Centro de segurança também acessa as configurações existentes dos serviços do Azure para fornecer a configuração e recomendações de serviço para o ajudar a melhorar a postura de segurança e proteger os dados.

Centro de segurança utiliza uma variedade de capacidades de deteção para alertar os clientes de potenciais ataques que visam seus ambientes. Estes alertas contêm informações valiosas sobre o que acionou o alerta, os recursos afetados e a origem do ataque. Centro de segurança tem um conjunto de [predefinidos alertas de segurança](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) que são acionados quando uma ameaça ou atividade suspeita ocorre. Os clientes podem usar [regras de alerta personalizadas](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) para definir novos alertas de segurança com base nos dados que já são recolhidos do seu ambiente.

Centro de segurança fornece alertas de segurança priorizados e incidentes. Centro de segurança torna mais simples para os clientes detetar e resolver potenciais problemas de segurança. R [relatório de inteligência de ameaças](https://docs.microsoft.com/azure/security-center/security-center-threat-report) é gerado para cada detetadas ameaças. As equipes de resposta a incidentes podem utilizar os relatórios quando investigar e corrigir as ameaças.

Esta arquitetura de referência também utiliza a [avaliação de vulnerabilidade](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations) capacidade no Centro de segurança. Depois de estar configurada, um agente de parceiro (por exemplo, Qualys) relatórios de dados de vulnerabilidade à plataforma de gestão do parceiro. Por sua vez, a plataforma de gestão do parceiro fornece dados de vulnerabilidades e de estado de funcionamento de volta ao Centro de Segurança. Os clientes podem utilizar estas informações para identificar rapidamente VMs vulneráveis.

### <a name="business-continuity"></a>Continuidade do negócio
**Elevada disponibilidade**: Cargas de trabalho do servidor são agrupadas numa [conjunto de disponibilidade](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para ajudar a garantir a elevada disponibilidade de VMs no Azure. Pelo menos uma VM está disponível durante um evento de manutenção não planeada ou não planeada, que cumpre 99,95% do SLA do Azure.

**Cofre dos Recovery Services**: O [cofre dos serviços de recuperação](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) hospeda os dados de cópia de segurança e protege todas as configurações de VMs nesta arquitetura. Com o Cofre de serviços de recuperação, os clientes podem restaurar ficheiros e pastas a partir de uma VM de IaaS sem restaurar toda a VM. Este processo acelera os tempos de restauro.

### <a name="logging-and-auditing"></a>Registro e auditoria

Serviços do Azure extensivamente de registo do sistema e a atividade do utilizador, bem como o estado de funcionamento do sistema:
- **Registos de atividades**: [Registos de atividades](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) fornecem informações sobre as operações executadas em recursos numa subscrição. Registos de atividades podem ajudar a determinar o iniciador de uma operação, hora da ocorrência e o estado.
- **Os registos de diagnóstico**: [Os registos de diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) incluem todos os registos emitidos por cada recurso. Estes registos incluem registos de sistema de eventos do Windows, registos de armazenamento, registos de auditoria do Cofre de chaves e os registos de acesso e de firewall do Gateway de aplicação do Azure. Todos os registos de diagnóstico escrevem para uma conta de armazenamento do Azure centralizado e criptografado para arquivamento. Os utilizadores podem configurar o período de retenção, até e 730 dias, para satisfazer os seus requisitos específicos.

**Registos de Monitor do Azure**: Estes registos são consolidados no [registos do Azure Monitor](https://azure.microsoft.com/services/log-analytics/) para processamento, armazenamento e relatórios do dashboard. Depois de recolhidos, os dados são organizado em tabelas separadas para cada tipo de dados nas áreas de trabalho do Log Analytics. Dessa forma, todos os dados podem ser analisados em conjunto, independentemente de sua fonte original. Centro de segurança integra-se com os registos do Azure Monitor. Os clientes podem utilizar consultas de Kusto para aceder aos respetivos dados de eventos de segurança e combiná-los com dados de outros serviços.

O Azure seguinte [soluções de monitorização](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) são incluídos como parte desta arquitetura:
-   [Avaliação do Active Directory](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): A solução de verificação de estado de funcionamento do Active Directory avalia o risco e estado de funcionamento dos ambientes de servidor num intervalo regular. Ele fornece uma lista prioritária de recomendações específicas para a infraestrutura de servidor implementado.
- [Avaliação do SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): A solução de verificação de estado de funcionamento do SQL avalia o risco e estado de funcionamento dos ambientes de servidor num intervalo regular. Fornece aos clientes com uma lista prioritária de recomendações específicas para a infraestrutura de servidor implementado.
- [Estado de funcionamento do agente](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): A solução de estado de funcionamento do agente relata o número de agentes é implementado e a respetiva distribuição geográfica. Também relata o número de agentes é sem resposta e o número de agentes que submeter dados operacionais.
-   [Log Analytics da atividade](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): A solução Log Analytics da atividade auxilia com uma análise de registos de atividade do Azure em todas as subscrições do Azure para um cliente.

**A automatização do Azure**: [Automatização](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) armazena, executa e gere runbooks. Nesta solução, runbooks ajudam a recolher registos de base de dados SQL. Os clientes podem usar a automação [controlo de alterações](https://docs.microsoft.com/azure/automation/automation-change-tracking) solução para o identificar facilmente as alterações no ambiente.

**Azure Monitor**: [Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) ajuda os usuários a acompanhar o desempenho, manter a segurança e identificar tendências. As organizações podem usá-lo de auditoria, criar alertas e arquivar dados. Também pode controlar as chamadas de API em seus recursos do Azure.

## <a name="threat-model"></a>Modelo de risco

O diagrama de fluxo de dados para esta arquitetura de referência está disponível para [transferir](https://aka.ms/nist171-dw-tm) ou podem ser encontradas aqui. Esse modelo pode ajudar os clientes a compreender os pontos de risco potencial na infra-estrutura do sistema quando eles fazer modificações.

![Armazém de dados para NIST SP 800-171 modelo de risco](images/nist171-dw-threat-model.png "do armazém de dados para NIST SP 800-171 modelo de risco")

## <a name="compliance-documentation"></a>Documentação de conformidade
O [Azure no Security and Compliance Blueprint – matriz de responsabilidade do cliente de SP do NIST 800-171](https://aka.ms/nist171-crm) apresenta uma lista de todos os controlos de segurança exigidos pelo SP do NIST 800-171. Essa matriz se a implementação de cada controle é da responsabilidade da Microsoft, o cliente, os detalhes ou partilhados entre os dois.

O [Azure no Security and Compliance Blueprint - matriz de implementação de controlo de armazém de dados de SP do NIST 800-171](https://aka.ms/nist171-dw-cim) fornece informações sobre quais SP NIST 800-171 controles estão abrangidas pela arquitetura do armazém de dados. Ele inclui descrições detalhadas de como a implementação cumpre os requisitos de cada controle coberta.

## <a name="guidance-and-recommendations"></a>Orientações e recomendações

### <a name="vpn-and-expressroute"></a>ExpressRoute e VPN
Um túnel VPN seguro ou [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) tem de ser configurado com segurança estabelecer uma ligação para os recursos implementados como parte desta arquitetura de referência do armazém de dados. Configurando adequadamente uma VPN ou ExpressRoute, os clientes podem adicionar uma camada de proteção para dados em trânsito.

Ao implementar um túnel VPN seguro com o Azure, é possível criar uma ligação privada virtual entre uma rede no local e uma rede virtual do Azure. Esta ligação ocorre através da Internet. Os clientes podem utilizar esta ligação para informações de "encapsulamento" em segurança dentro de uma ligação encriptada entre a rede do cliente e o Azure. VPN site a site é uma tecnologia madura e segura que tenha sido implementada por empresas de todos os tamanhos há décadas. O [modo de encapsulamento IPsec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) é utilizada nesta opção como um mecanismo de criptografia.

Uma vez que o tráfego no túnel VPN atravessa a Internet com uma VPN de site a site, a Microsoft oferece outra opção de ligação ainda mais segura. O ExpressRoute é uma WAN dedicada ligação entre o Azure e uma localização no local ou um fornecedor de alojamento do Exchange. As ligações ExpressRoute ligar diretamente ao fornecedor de telecomunicação do cliente. Como resultado, os dados não viajam através da Internet e não estão expostos à mesma. Estas ligações oferecem mais fiabilidade, velocidades mais rápidas, latências mais baixas e maior segurança do que as ligações normais.

Melhores práticas para implementar uma rede híbrida segura que expande uma rede no local para o Azure são [disponível](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

### <a name="extract-transform-load-process"></a>Processo de extrair-transformar-carregar
[O PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) pode carregar dados para o SQL Data Warehouse sem a necessidade de um ETL separado ou importar a ferramenta. O PolyBase permite o acesso aos dados através de consultas de T-SQL. A pilha de inteligência e análise de negócios da Microsoft e ferramentas de terceiros que são compatíveis com o SQL Server podem ser utilizadas com o PolyBase.

### <a name="azure-ad-setup"></a>Configuração do Azure AD
[O Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) é essencial para a gestão da implementação e aprovisionamento do acesso ao pessoal de interação com o ambiente. No local do Active Directory pode ser integrado com o Azure AD na [quatro cliques](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express). Os clientes também podem vincular a infra-estrutura do Active Directory implementada (controladores de domínio) para o Azure AD. Para fazer isso, verifique a infra-estrutura do Active Directory implementada um subdomínio de uma floresta de AD do Azure.

### <a name="optional-services"></a>Serviços opcionais
O Azure oferece uma variedade de serviços para ajudar com o armazenamento e a preparação de dados formatados e formatados. Os seguintes serviços podem ser adicionados a esta arquitetura de referência com base nos requisitos de cliente:
-   [O Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) é um serviço cloud gerido criado para projetos de integração de dados híbridos ETL, extract-load-transform e complexos. O Data Factory tem capacidades para ajudar a rastreamento e localizar dados. Visualização e ferramentas de monitorização identificam quando recebe dados e de onde vieram. Os clientes podem criar e agendar condicionada por dados fluxos de trabalho, denominados pipelines, que ingerir dados a partir de arquivos de dados diferentes. Eles podem usar os pipelines para ingerir dados de origens internas e externas. Os clientes podem, em seguida, processar e transformar os dados de saída em arquivos de dados, como o SQL Data Warehouse.
- Os clientes podem testar dados não estruturados na [do Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) capturar dados de qualquer tamanho, tipo e velocidade de ingestão num único local para análises exploratórias e operacionais. O Azure Data Lake tem capacidades que permitem a extração e a conversão de dados. Data Lake Store é compatível com mais componentes de origem no ecossistema do Hadoop. Ele também se integra perfeitamente com outros serviços do Azure, como o SQL Data Warehouse.

## <a name="disclaimer"></a>Exclusão de Responsabilidade

 - Este documento é apenas para fins informativos. A MICROSOFT FAZ NÃO OFERECE QUAISQUER GARANTIAS, EXPRESSAS, IMPLÍCITAS OU ESTATUTÁRIAS QUANTO ÀS INFORMAÇÕES CONTIDAS NESTE DOCUMENTO. Este documento é fornecido "como-está." Informações e opiniões expressados neste documento, incluindo URLs e outras referências de Web site da Internet, podem ser alteradas sem aviso prévio. Os clientes a ler este documento assume o risco de usá-lo.
 - Este documento não fornece aos clientes quaisquer direitos de propriedade intelectual em qualquer produto da Microsoft ou soluções.
 - Os clientes podem copiar e utilizar este documento para efeitos de referência interna.
 - Algumas recomendações neste documento podem resultar em maior de dados, a rede ou a utilização de recursos de computação no Azure e podem aumentar os custos de licenciamento ou de subscrição do Azure num cliente.
 - Esta arquitetura deve ser usado como uma base para os clientes que se ajustar às suas necessidades específicas e não devem ser utilizada como-está num ambiente de produção.
 - Este documento é desenvolvido como uma referência e não deve ser usado para definir todos os meios pelos quais um cliente pode atender aos requisitos de conformidade e normas. Os clientes procuram legal suporte da sua organização em implementações do cliente aprovadas.
