---
title: Segurança do Azure e o esquema de conformidade – armazém de dados de PCI DSS
description: Segurança do Azure e o esquema de conformidade – armazém de dados de PCI DSS
services: security
author: meladie
ms.assetid: 6d29e5bf-6cd0-4cda-bbd8-59f283b0c86c
ms.service: security
ms.topic: article
ms.date: 07/03/2018
ms.author: meladie
ms.openlocfilehash: 7704d9124846c0c6c82728031327990f8f9b1cf6
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57243521"
---
# <a name="azure-security-and-compliance-blueprint-data-warehouse-for-pci-dss"></a>Azure Security and Compliance Blueprint: Armazém de dados de PCI DSS

## <a name="overview"></a>Descrição geral

Este Azure no Security and Compliance Blueprint fornece orientações para a implementação de uma arquitetura de armazém de dados no Azure que ajuda a com os requisitos de normas de segurança do dados de setor de cartão de pagamento (PCI DSS 3.2). Ele demonstra uma arquitetura de referência comuns e demonstra o processamento adequado dos dados de cartão de crédito (incluindo dados de número, a expiração e a verificação do cartão) num ambiente seguro, em conformidade, com várias camado. Este esquema demonstra maneiras em que os clientes podem satisfazer os requisitos específicos de segurança e conformidade e serve como uma base para os clientes criar e configurar suas próprias soluções de armazém de dados no Azure.

Esta arquitetura de referência, o guia de implementação e o modelo de ameaças fornecem uma Fundação para os clientes estão em conformidade com PCI DSS 3.2 requisitos. Esta solução fornece uma linha de base para ajudar os clientes a implementar cargas de trabalho no Azure numa maneira em conformidade com PCI DSS 3.2. No entanto, esta solução não deve ser utilizada como-está num ambiente de produção, porque é necessária configuração adicional.

Alcançando a conformidade do PCI DSS requer que um Assessor acreditada de segurança qualificado (QSA) certificar uma solução de cliente de produção. Os clientes são responsáveis pela realização de segurança adequados e avaliações de conformidade de qualquer solução criada com esta arquitetura, como os requisitos podem variar com base nas especificidades da implementação de cada cliente.

## <a name="architecture-diagram-and-components"></a>Diagrama de arquitetura e componentes

Esta solução fornece uma arquitetura de referência que implementa um armazém de dados de cloud seguro e de alto desempenho. Existem duas camadas de dados separado nesta arquitetura: uma em que dados são importados, armazenados e de teste num ambiente em cluster do SQL, e outro para o Azure SQL Data Warehouse em que os dados são carregados usando uma extrair, transformar, carregar ferramenta (por exemplo, [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase) consultas de T-SQL) para processamento. Assim que os dados são armazenados no armazém de dados SQL do Azure, podem executar análises em grande escala.

O Azure oferece uma variedade de serviços de relatórios e análise do cliente. Esta solução inclui o SQL Server Reporting Services (SSRS) para a criação rápida de relatórios do armazém de dados SQL do Azure. Todo o tráfego SQL é encriptado com SSL através da inclusão de certificados autoassinados. Como melhor prática, o Azure recomenda a utilização de uma autoridade de certificação fidedigna para uma maior segurança.

Dados no armazém de dados SQL do Azure são armazenados em tabelas relacionais com o armazenamento em colunas, um formato que reduz significativamente os custos de armazenamento de dados enquanto melhora o desempenho da consulta. Dependendo dos requisitos de utilização, os recursos de computação do Azure SQL Data Warehouse podem ser aumentados vertical ou horizontalmente ou desativar completamente se existirem não existem processos ativos que exigem recursos de computação.

Um balanceador de carga de SQL gere o tráfego SQL, garantindo alto desempenho. Implementar todas as máquinas virtuais nesta arquitetura de referência num conjunto com instâncias do SQL Server configuradas num grupo de disponibilidade Always On para capacidades de elevada disponibilidade e recuperação após desastre de disponibilidade.

Esta arquitetura de referência do armazém de dados também inclui uma camada de Active Directory para gestão de recursos dentro da arquitetura. A sub-rede do Active Directory permite a adoção fácil numa estrutura de floresta do Active Directory maior, permitindo que o ambiente da operação contínua, mesmo quando o acesso para a floresta maior não está disponível. Todas as máquinas virtuais estão associados a um domínio para o escalão do Active Directory e utilize políticas de grupo do Active Directory para impor configurações de segurança e conformidade ao nível do sistema operativo.

A solução utiliza contas de armazenamento do Azure, o que os clientes podem configurar para utilizar a encriptação do serviço de armazenamento para manter a confidencialidade dos dados em repouso. Azure armazena três cópias dos dados num datacenter selecionados de um cliente para resiliência. Armazenamento com redundância geográfico garante que os dados vão ser replicados para um datacenter secundário centenas de quilómetros de distância e novamente armazenados como três cópias dentro desse datacenter, impedir que um evento adverso no Centro de dados principal do cliente resulta numa perda de dados.

Para maior segurança, todos os recursos nesta solução são geridos como um grupo de recursos através do Gestor de recursos do Azure. O Azure Active Directory, o controlo de acesso baseado em funções é utilizado para controlar o acesso ao implementar recursos, incluindo as chaves no Azure Key Vault. Estado de funcionamento do sistema é monitorizado através do Centro de segurança do Azure e o Azure Monitor. Os clientes configurar ambos os serviços de monitorização para capturar os registos e exibir o estado de funcionamento do sistema num dashboard único, navegável facilmente.

Uma máquina virtual funciona como um anfitrião de bastião de gestão, fornecendo uma ligação segura para os administradores de recursos de acesso implementado. O carregamento dos dados para a área de teste por meio deste anfitrião de bastião de gestão. **A Microsoft recomenda configurar uma ligação VPN ou Azure ExpressRoute para importação de dados e de gestão para a sub-rede de arquitetura de referência.**

![Armazém de dados para o diagrama de arquitetura de referência de PCI DSS](images/pcidss-dw-architecture.png "armazém de dados para o diagrama de arquitetura de referência de PCI DSS")

Esta solução utiliza os seguintes serviços do Azure. Os detalhes a arquitetura de implementação estão no [arquitetura de implantação](#deployment-architecture) secção.

- Conjuntos de Disponibilidade
    - Controladores de domínio do Active Directory
    - Nós de Cluster SQL e da testemunha
- Azure Active Directory
- Catálogo de Dados do Azure
- Azure Key Vault
- Azure Monitor
- Centro de Segurança do Azure
- Azure Load Balancer
- Storage do Azure
- Máquinas Virtuais do Azure
    - (1) anfitrião de bastião
    - (2) controlador de domínio do as Active Directory
    - (2) nó de Cluster de servidor SQL
    - (1) o SQL Server testemunha
- Rede Virtual do Azure
    - (1) /16 de rede
    - (4) redes /24
    - (4) grupos de segurança de rede
- Cofre dos serviços de recuperação
- SQL Data Warehouse
- Serviços de Relatórios do SQL Server

## <a name="deployment-architecture"></a>Arquitetura de implantação

A secção seguinte fornece detalhes sobre os elementos de implantação e a implementação.

**O SQL Data Warehouse**: [O SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) é uma empresa armazém de dados (EDW) que tira partido Massivamente paralela de processamento (MPP) para executar rapidamente consultas complexas em petabytes de dados, permitindo que os usuários com eficiência identificar dados financeiros. Os utilizadores podem utilizar consultas simples de PolyBase T-SQL para importar grandes quantidades de dados para o SQL Data Warehouse e utilizam o poder do MPP para executar análises de elevado desempenho.

**SQL Server Reporting Services (SSRS)**: [SQL Server Reporting Services](https://docs.microsoft.com/sql/reporting-services/report-data/sql-azure-connection-type-ssrs) fornece a rápida criação de relatórios com tabelas, gráficos, mapas, medidores, matrizes e mais para o Azure SQL Data Warehouse.

**Catálogo de dados**: [Catálogo de dados](https://docs.microsoft.com/azure/data-catalog/data-catalog-what-is-data-catalog) torna os dados facilmente Detetáveis e compreensíveis pelos utilizadores que gerem os dados. Origens de dados comuns podem ser registadas, marcadas e dados financeiros está à procura. Os dados permanecem na localização existente, mas uma cópia dos respetivos metadados é adicionada ao catálogo de dados, juntamente com uma referência para a localização de origem de dados. Os metadados também são indexados para tornar cada origem de dados facilmente detetável através da pesquisa e compreensível para os utilizadores que a detetarem.

**Anfitrião de bastião**: O anfitrião de bastião é o único ponto de entrada que permite aos utilizadores aceder os recursos implementados neste ambiente. O anfitrião de bastião fornece uma ligação segura a recursos implementados, permitindo apenas tráfego remoto a partir de endereços IP públicos numa lista segura. Para permitir o tráfego de (protocolo RDP) de área de trabalho remoto, a origem do tráfego deve ser definido no grupo de segurança de rede.

Esta solução cria uma máquina virtual como um anfitrião de bastião associados a um domínio com as seguintes configurações:
-   [Extensão de antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [Extensão de diagnóstico do Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [O Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) com o Azure Key Vault
-   Uma [política de encerramento automático](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) para reduzir o consumo de recursos de máquina virtual quando não está em utilização
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) ativado para que as credenciais e outros segredos executar num ambiente protegido, que está isolado do sistema operativo em execução.

### <a name="virtual-network"></a>Rede virtual

Esta arquitetura de referência define uma rede privada virtual com um espaço de endereços de 10.0.0.0/16.

**Grupos de segurança de rede**: [Grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) contêm listas de controlo de acesso que permitem ou negam o tráfego dentro de uma rede virtual. Grupos de segurança de rede podem ser utilizados para proteger o tráfego numa sub-rede ou o nível de máquina virtual individual. Existem os seguintes grupos de segurança de rede:

  - Um grupo de segurança de rede para a camada de dados (Clusters do SQL Server, SQL Server testemunha e Balanceador de carga de SQL)
  - Um grupo de segurança de rede para o anfitrião de bastião de gestão
  - Um grupo de segurança de rede para o Active Directory
  - Um grupo de segurança de rede do SQL Server Reporting Services

Cada um dos grupos de segurança de rede têm portas específicas e protocolos abrir para que a solução possam funcionar corretamente e de forma segura. Além disso, as seguintes configurações são habilitadas para cada grupo de segurança de rede:

- [Eventos e registos de diagnóstico](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) forem ativadas e armazenados numa conta de armazenamento
- Registos de Monitor do Azure está ligada a [grupo de segurança de rede&#39;registos de diagnóstico de s](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Sub-redes**: Cada sub-rede está associado a seu grupo de segurança de rede correspondente.

### <a name="data-at-rest"></a>Dados inativos

A arquitetura protege dados em repouso através de várias medidas, incluindo encriptação e a auditoria de base de dados.

**O armazenamento do Azure**: Para atender aos dados criptografados em requisitos de rest, todos os [armazenamento do Azure](https://azure.microsoft.com/services/storage/) utiliza [Storage Service Encryption](https://docs.microsoft.com/azure/storage/storage-service-encryption). Isto ajuda a proteger e salvaguardar os dados do titular do cartão para oferecer suporte a compromissos de segurança organizacional e requisitos de conformidade definidos pela PCI DSS 3.2.

**Azure Disk Encryption**: [O Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) aproveita a funcionalidade BitLocker do Windows para fornecer encriptação de volume para o SO e discos de dados. A solução se integra com o Azure Key Vault para ajudar a controlar e gerir as chaves de encriptação de disco.

**Base de dados SQL do Azure**: A instância de base de dados do Azure SQL usa as seguintes medidas de segurança da base de dados:

- [Autenticação do Active Directory e a autorização](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) permite a gestão de identidades de utilizadores de base de dados e outros serviços da Microsoft num local central.
- [A auditoria de base de dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) faixas de base de dados eventos e escreve-os para uma auditoria registo numa conta de armazenamento do Azure.
- Base de dados SQL do Azure está configurado para utilizar [encriptação de dados transparente](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), que realiza a encriptação em tempo real e a desencriptação da base de dados, cópias de segurança associadas e ficheiros de registo de transação para proteger as informações em rest. Encriptação de dados transparente fornece garantia de que são armazenados os dados não tiver sido sujeita a acesso não autorizado.
- [Regras de firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) impedir o acesso de todos os servidores de base de dados até que sejam concedidas as permissões apropriadas. A firewall concede acesso a bases de dados com base no endereço IP de origem de cada pedido.
- [Deteção de ameaças SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) permite a deteção e resposta a ameaças potenciais à medida que ocorrem ao fornecer alertas de segurança para atividades suspeitas da base de dados, potenciais vulnerabilidades, ataques de injeção de SQL e acesso de base de dados anómalas padrões.
- [Encriptados colunas](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) Certifique-se de que os dados confidenciais nunca é apresentada como texto não criptografado dentro do sistema de base de dados. Depois de ativar a encriptação de dados, apenas as aplicações de cliente ou servidores de aplicações com acesso às chaves podem aceder aos dados de texto sem formatação.
- [Propriedades estendidas](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql) pode ser utilizado para descontinuar o processamento de titulares dos dados, já que permite aos utilizadores adicionar propriedades personalizadas para objetos de base de dados e marcar os dados como "Discontinued" para suportar a lógica de aplicação para impedir que o processamento de associados dados financeiros.
- [Segurança ao nível da linha](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) permite aos utilizadores definir políticas para restringir o acesso aos dados de descontinuar o processamento.
- [Máscara de dados dinâmicos da base de dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) limita a exposição de dados confidenciais ao mascará os dados para aplicações ou utilizadores sem privilégios. Máscara de dados dinâmica pode detetar dados potencialmente confidenciais e sugerir as máscaras de apropriado a ser aplicada automaticamente. Isto ajuda a identificar e reduzir o acesso aos dados, de modo que ele não for fechada a base de dados por meio do acesso não autorizado. Os clientes são responsáveis por ajustar as definições de cumprir para seu esquema de base de dados de máscara de dados dinâmicos.

### <a name="identity-management"></a>Gestão de identidades

As seguintes tecnologias fornecem capacidades para gerir o acesso aos dados no ambiente do Azure:

- [O Azure Active Directory](https://azure.microsoft.com/services/active-directory/) é o Microsoft&#39;s multi-inquilino com base na cloud diretório e identidade do serviço de gestão. Todos os utilizadores para esta solução são criados no Azure Active Directory, incluindo os utilizadores que acedem a base de dados do SQL do Azure.
- Autenticação para a aplicação é executada com o Azure Active Directory. Para obter mais informações, consulte [integrar aplicações com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Além disso, a encriptação de coluna de base de dados utiliza o Azure Active Directory para autenticar a aplicação à base de dados do Azure SQL. Para obter mais informações, consulte como [proteger dados confidenciais na base de dados do Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
- [Controlo de acesso baseado em função do Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) permite aos administradores definir permissões de acesso detalhado para conceder apenas a quantidade de acesso que os utilizadores precisam realizar seus trabalhos. Em vez de dar a cada permissão de utilizador sem restrições para recursos do Azure, os administradores podem permitir apenas certas ações para aceder aos dados. Acesso à subscrição está limitado ao administrador da subscrição.
- [O Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) permite que os clientes minimizar o número de utilizadores que têm acesso a determinadas informações. Os administradores podem usar o Azure Active Directory Privileged Identity Management para detetar, restringir e monitorizar identidades privilegiadas e respetivo acesso a recursos. Esta funcionalidade também pode ser utilizada para impor acesso administrativo a pedido e just-in-time quando necessário.
- [O Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) Deteta potenciais vulnerabilidades que afetam uma organização&#39;identidades s, configura as respostas automáticas para detetado ações suspeitas relacionadas com uma organização&#39;identidades s e investiga os incidentes suspeitos para tomar as medidas adequadas para resolvê-los.

### <a name="security"></a>Segurança

**Gestão de segredos**: A solução utiliza [do Azure Key Vault](https://azure.microsoft.com/services/key-vault/) para o gerenciamento de chaves e segredos. O Cofre de Chaves do Azure ajuda a salvaguardar as chaves criptográficas e os segredos utilizados pelas aplicações em cloud e pelos serviços. As seguintes capacidades do Azure Key Vault ajudam os clientes a proteger e aceder a esses dados:

- Políticas de acesso avançadas são configuradas de forma necessidade.
- Políticas de acesso do Cofre de chaves são definidas com o mínimo permissões necessárias para chaves e segredos.
- Todas as chaves e segredos no Key Vault tem datas de expiração.
- Todas as chaves no Cofre de chaves estão protegidas por módulos de segurança de hardware especializado. O tipo de chave é uma chave RSA de 2048 protegidas de HSM bits.
- Todos os utilizadores e identidades são concedidas as permissões mínimas necessárias usando o controle de acesso baseado em funções.
- Registos de diagnóstico para o Cofre de chaves estão ativados com um período de retenção de, pelo menos, 365 dias.
- Operações de criptografia permitidas para as chaves estão limitadas aos obrigatórios.

**Gerenciamento de patches**: Máquinas de virtuais do Windows implementadas como parte desta arquitetura de referência estão configuradas por predefinição para receber atualizações automáticas do serviço de atualização do Windows. Esta solução também inclui a [automatização do Azure](https://docs.microsoft.com/azure/automation/automation-intro) serviço por meio do qual as implementações atualizadas podem ser criadas para máquinas de virtuais de patch quando necessário.

**Proteção contra Malware**: [O Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) para máquinas virtuais fornece a capacidade de proteção em tempo real que ajuda a identificar e remover vírus, spyware e outro software malicioso, com alertas configuráveis quando conhecido software malicioso ou indesejável tenta instalar ou executar nas máquinas virtuais protegidas.

**Centro de segurança do Azure**: Com o [Centro de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro), os clientes podem centralmente aplicam-se e gerir políticas de segurança em cargas de trabalho, limitar a exposição a ameaças e detetar e responder a ataques. Além disso, o Centro de segurança do Azure acessa as configurações existentes dos serviços do Azure para fornecer a configuração e recomendações de serviço para o ajudar a melhorar a postura de segurança e proteger os dados.

Centro de segurança do Azure utiliza uma variedade de capacidades de deteção para alertar os clientes de possíveis ataques direcionados aos respetivos ambientes. Estes alertas contêm informações valiosas sobre o que acionou o alerta, os recursos afetados e a origem do ataque. Centro de segurança do Azure tem um conjunto de [predefinidos alertas de segurança](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), que são acionados quando uma ameaça ou atividade suspeita tem lugar. [Regras de alerta personalizadas](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) no Centro de segurança do Azure permitem aos clientes definir novos alertas de segurança com base nos dados que já são recolhidos do seu ambiente.

Centro de segurança do Azure fornece alertas de segurança priorizados e incidentes, tornando mais simples para os clientes detetar e resolver potenciais problemas de segurança. R [relatório de inteligência de ameaças](https://docs.microsoft.com/azure/security-center/security-center-threat-report) é gerado para cada detetado contra ameaças para ajudar as equipes de resposta a incidentes a investigar e resolver ameaças.

### <a name="business-continuity"></a>Continuidade do negócio

**Elevada disponibilidade**: Cargas de trabalho do servidor são agrupadas numa [conjunto de disponibilidade](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para ajudar a garantir a elevada disponibilidade de máquinas virtuais no Azure. Pelo menos uma máquina virtual está disponível durante um evento de manutenção não planeada ou não planeada, atendendo a 99,95% do SLA do Azure.

**Cofre de serviços de recuperação**: O [cofre dos Recovery Services](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) hospeda os dados de cópia de segurança e protege todas as configurações de máquinas virtuais do Azure nesta arquitetura. Com um cofre de serviços de recuperação, os clientes podem restaurar ficheiros e pastas da máquina virtual IaaS sem restaurar toda a máquina virtual, permitindo que os tempos de restauro mais rápidos.

### <a name="logging-and-auditing"></a>Registro e auditoria

Serviços do Azure extensivamente de registo do sistema e a atividade do utilizador, bem como o estado de funcionamento do sistema:
- **Registos de atividades**: [Registos de atividades](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) fornecem informações sobre as operações executadas em recursos numa subscrição. Registos de atividades podem ajudar a determinar o iniciador de uma operação, hora da ocorrência e o estado.
- **Os registos de diagnóstico**: [Os registos de diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) incluem todos os registos emitidos por cada recurso. Estes registos incluem registos de sistema de eventos do Windows, registos de armazenamento do Azure, registos de auditoria do Cofre de chaves e os registos de acesso e de firewall do Gateway de aplicação. Todos os registos de diagnóstico escrevem para uma conta de armazenamento do Azure centralizado e criptografado para arquivamento. O período de retenção é configurável pelo utilizador, cópia de segurança e 730 dias, para atender aos requisitos de retenção de específicas da organização.

**Registos de Monitor do Azure**: Estes registos são consolidados no [registos do Azure Monitor](https://azure.microsoft.com/services/log-analytics/) para processamento, armazenamento e relatórios do dashboard. Depois de recolhidos, os dados são organizados em tabelas separadas para cada tipo de dados nas áreas de trabalho do Log Analytics, que permite que todos os dados sejam analisados em conjunto, independentemente de sua fonte original. Além disso, o Centro de segurança do Azure integra-se com os registos do Azure Monitor permitindo que os clientes utilizar consultas de Kusto para aceder aos respetivos dados de eventos de segurança e combiná-los com dados de outros serviços.

O Azure seguinte [soluções de monitorização](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) são incluídos como parte desta arquitetura:
-   [Avaliação do Active Directory](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): A solução de verificação de estado de funcionamento do Active Directory avalia o risco e estado de funcionamento dos ambientes de servidor num intervalo regular e fornece uma lista prioritária de recomendações específicas para a infraestrutura de servidor implementado.
- [Avaliação do SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): A solução de verificação de estado de funcionamento do SQL avalia o risco e estado de funcionamento dos ambientes de servidor num intervalo regular e fornece aos clientes uma lista prioritária de recomendações específicas para a infraestrutura de servidor implementado.
- [Estado de funcionamento do agente](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): A solução de estado de funcionamento do agente relata o número de agentes é implementado e a distribuição geográfica, como número de agentes que não respondem e o número de agentes que está a enviar dados operacionais.
-   [Log Analytics da atividade](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): A solução Log Analytics da atividade auxilia com uma análise de registos de atividade do Azure em todas as subscrições do Azure para um cliente.

**A automatização do Azure**: [A automatização do Azure](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) armazena, executa e gere runbooks. Nesta solução, runbooks ajudam a recolher registos de base de dados do Azure SQL. A automação [controlo de alterações](https://docs.microsoft.com/azure/automation/automation-change-tracking) solução permite aos clientes identificar facilmente as alterações no ambiente.

**Azure Monitor**: [O Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) ajuda os usuários a acompanhar o desempenho, manter a segurança e identificar tendências, permitindo que as organizações de auditoria, criar alertas e arquivar dados, incluindo o controlo de chamadas à API em seus recursos do Azure.

## <a name="threat-model"></a>Modelo de risco

O diagrama de fluxo de dados para esta arquitetura de referência está disponível para [transferir](https://aka.ms/pcidss-dw-tm) ou podem ser encontradas abaixo. Esse modelo pode ajudar os clientes a compreender os pontos do potencial risco de na infraestrutura de sistema ao fazer modificações.

![Armazém de dados para o diagrama de arquitetura de referência de PCI DSS](images/pcidss-dw-threat-model.png "aplicativo da Web de PaaS para o modelo de risco de PCI DSS")

## <a name="compliance-documentation"></a>Documentação de conformidade

O [Azure no Security and Compliance Blueprint – matriz de responsabilidade do PCI DSS cliente](https://aka.ms/pcidss-crm) apresenta uma lista de responsabilidades para todos os requisitos de PCI DSS 3.2.

O [Azure no Security and Compliance Blueprint - matriz de implementação do armazém de dados do PCI DSS](https://aka.ms/pcidss-dw-cim) fornece informações sobre quais 3.2 de DSS da PCI requisitos são resolvidos à arquitetura do armazém de dados, incluindo descrições detalhadas de como a implementação cumpre os requisitos de cada controle coberta.

## <a name="guidance-and-recommendations"></a>Orientações e recomendações

### <a name="vpn-and-expressroute"></a>ExpressRoute e VPN

Um túnel VPN seguro ou [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) tem de ser configurado com segurança estabelecer uma ligação para os recursos implementados como parte desta arquitetura de referência do armazém de dados. Configurando adequadamente uma VPN ou ExpressRoute, os clientes podem adicionar uma camada de proteção para dados em trânsito.

Ao implementar um túnel VPN seguro com o Azure, é possível criar uma ligação privada virtual entre uma rede no local e uma rede Virtual do Azure. Essa conexão ocorre através da Internet e permite que os clientes com segurança as informações de "encapsulamento" dentro de uma ligação encriptada entre a rede do cliente e o Azure. VPN site a Site é uma tecnologia madura e segura que tenha sido implementada por empresas de todos os tamanhos há décadas. O [modo de encapsulamento IPsec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) é utilizada nesta opção como um mecanismo de criptografia.

Porque o tráfego de túnel VPN atravessa a Internet com uma VPN de site a site, a Microsoft oferece a opção de ligação de outra, ainda mais seguro. O Azure ExpressRoute é uma WAN dedicada ligação entre o Azure e uma localização no local ou um fornecedor de alojamento do Exchange. Como as ligações ExpressRoute não entram na Internet, estas ligações oferecem mais fiabilidade, velocidades mais rápidas, latências mais baixas e maior segurança do que as ligações normais pela Internet. Além disso, como se trata de uma conexão direta do fornecedor de telecomunicação do cliente, os dados não percorre a Internet e, portanto, não são expostos ao mesmo.

Melhores práticas para implementar uma rede híbrida segura que expande uma rede no local para o Azure são [disponível](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

### <a name="extract-transform-load-process"></a>Processo de extrair-transformar-carregar

[O PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) podem carregar dados para o Azure SQL Data Warehouse sem a necessidade de um separado extrair, transformar, carregar ou importar ferramenta. O PolyBase permite o acesso aos dados através de consultas de T-SQL. Da Microsoft inteligência comercial e pilha de análise, bem como ferramentas de terceiros compatíveis com o SQL Server, podem ser utilizadas com o PolyBase.

### <a name="azure-active-directory-setup"></a>Configuração do Active Directory do Azure

[O Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) é essencial para a gestão da implementação e aprovisionamento do acesso ao pessoal de interação com o ambiente. Num Windows Server Active Directory podem ser integrada no Azure Active Directory no [quatro cliques](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express). Os clientes podem associar a infraestrutura de Active Directory implementada (controladores de domínio) a um existente do Azure Active Directory, tornando a infraestrutura de Active Directory implementada um subdomínio de uma floresta do Active Directory do Azure.

### <a name="optional-services"></a>Serviços opcionais

O Azure oferece uma variedade de serviços para ajudar com o armazenamento e a preparação de dados formatados e formatados. Os seguintes serviços podem ser adicionados a esta arquitetura de referência, consoante os requisitos dos clientes:
-   [O Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) é um serviço cloud gerido criado para complexos e híbridos extrair-transformar-carregar e projetos de integração de dados. O Azure Data Factory tem capacidades para ajudar a rastreamento e localizar transmitam dados, incluindo a visualização e ferramentas para identificar quando recebe dados e de onde vieram de monitorização. Com o Azure Data Factory, os clientes podem criar e agendar fluxos de trabalho condicionados por dados denominados pipelines ingerir dados de arquivos de dados diferentes. Esses pipelines permitem aos clientes ingerir dados de origens internas e externas. Os clientes podem, em seguida, processar e transformar os dados de saída em arquivos de dados, como o Azure SQL Data Warehouse.
- Os clientes podem testar dados não estruturados na [do Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview), que permite a captura de dados de qualquer tamanho, tipo, e velocidade de ingestão num único local para análises exploratórias e operacionais.  O Azure Data Lake tem capacidades que permitem a extração e a conversão de dados. Azure Data Lake Store é compatível com mais componentes de origem no ecossistema do Hadoop e integra-se perfeitamente com outros serviços do Azure, como o Azure SQL Data Warehouse.

## <a name="disclaimer"></a>Exclusão de Responsabilidade

 - Este documento é apenas para fins informativos. A MICROSOFT FAZ NÃO OFERECE QUAISQUER GARANTIAS, EXPRESSAS, IMPLÍCITAS OU ESTATUTÁRIAS QUANTO ÀS INFORMAÇÕES CONTIDAS NESTE DOCUMENTO. Este documento é fornecido "como-está." Informações e opiniões expressados neste documento, incluindo URLs e outras referências de Web site da Internet, podem ser alteradas sem aviso prévio. Os clientes a ler este documento assume o risco de usá-lo.
 - Este documento não fornece aos clientes quaisquer direitos de propriedade intelectual em qualquer produto da Microsoft ou soluções.
 - Os clientes podem copiar e utilizar este documento para efeitos de referência interna.
 - Algumas recomendações neste documento podem resultar em maior de dados, a rede ou a utilização de recursos de computação no Azure e podem aumentar os custos de licenciamento ou de subscrição do Azure num cliente.
 - Esta arquitetura deve ser usado como uma base para os clientes que se ajustar às suas necessidades específicas e não devem ser utilizada como-está num ambiente de produção.
 - Este documento é desenvolvido como uma referência e não deve ser usado para definir todos os meios pelos quais um cliente pode atender aos requisitos de conformidade e normas. Os clientes procuram legal suporte da sua organização em implementações do cliente aprovadas.
