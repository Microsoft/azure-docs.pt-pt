---
title: Segurança do Azure e o esquema de conformidade – armazém de dados para a automatização de FedRAMP
description: Segurança do Azure e o esquema de conformidade – armazém de dados para a automatização de FedRAMP
services: security
author: jomolesk
ms.assetid: 834d1ff6-8369-455f-b052-1ef301e3d7e6
ms.service: security
ms.topic: article
ms.date: 05/02/2018
ms.author: jomolesk
ms.openlocfilehash: 3c78aed2f30ea85f5bc16a8c0fb270bb1c761be8
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57539518"
---
# <a name="azure-security-and-compliance-blueprint-data-warehouse-for-fedramp-automation"></a>Azure Security and Compliance Blueprint: Armazém de dados para a automatização de FedRAMP

## <a name="overview"></a>Descrição geral

O [Federal Risk and Authorization Management Program (FedRAMP)](https://www.fedramp.gov/) é um programa de toda a administração pública dos Estados Unidos que oferece uma abordagem padronizada à avaliação de segurança, autorização e monitorização contínua para cloud produtos e serviços. Este Azure no Security and Compliance Blueprint fornece orientações sobre como fornecer uma arquitetura de armazém de dados do Microsoft Azure que ajuda a implementar um subconjunto de controles do FedRAMP elevado. Esta solução fornece orientações sobre a implementação e configuração de recursos do Azure para uma arquitetura de referência comuns, que demonstram formas em que os clientes podem satisfazer os requisitos específicos de segurança e conformidade e serve como uma base para os clientes criar e configurar suas próprias soluções de armazém de dados no Azure.

Esta arquitetura de referência, guias de implementação do controlo associado e modelos de ameaça são deve ser usado como uma base para os clientes que se ajustar às suas necessidades específicas e não devem ser utilizados como-está num ambiente de produção. Implementar uma aplicação para este ambiente sem modificação não é suficiente para atender completamente aos requisitos da linha de base FedRAMP elevado. Tenha em atenção o seguinte:
- A arquitetura fornece uma linha de base para ajudar os clientes a implementar cargas de trabalho para o Azure de forma em conformidade com FedRAMP.
- Os clientes são responsáveis pela realização de segurança adequados e avaliações de conformidade de qualquer solução criada com esta arquitetura, como os requisitos podem variar com base nas especificidades da implementação de cada cliente.

## <a name="architecture-diagram-and-components"></a>Diagrama de arquitetura e componentes

Esta solução fornece uma arquitetura de referência do armazém de dados que implementa um armazém de dados de cloud seguro e de alto desempenho. Existem duas camadas de dados separado nesta arquitetura: uma em que os dados são importados, armazenados e de teste dentro de um ambiente de cluster de SQL e outro para o Azure SQL Data Warehouse em que os dados são carregados com uma ferramenta ETL (por exemplo, [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase)Consultas de T-SQL) para processamento. Assim que os dados são armazenados no armazém de dados SQL do Azure, podem executar análises em grande escala.

O Microsoft Azure oferece uma variedade de serviços de relatórios e análise do cliente. Esta solução inclui o SQL Server Reporting Services (SSRS) para a criação rápida de relatórios do armazém de dados SQL do Azure. Todo o tráfego SQL é encriptado com SSL através da inclusão de certificados autoassinados. Como melhor prática, o Azure recomenda a utilização de uma autoridade de certificação fidedigna para uma maior segurança.

Dados no armazém de dados SQL do Azure são armazenados em tabelas relacionais com o armazenamento em colunas, um formato que reduz significativamente os custos de armazenamento de dados enquanto melhora o desempenho da consulta.  Dependendo dos requisitos de utilização, os recursos de computação do Azure SQL Data Warehouse podem ser aumentados vertical ou horizontalmente ou desativar completamente se existirem não existem processos ativos que exigem recursos de computação.

Um balanceador de carga SQL gere o tráfego SQL, garantindo alto desempenho. Implementar todas as máquinas virtuais nesta arquitetura de referência num conjunto com instâncias do SQL Server configuradas num grupo de Disponibilidade AlwaysOn para capacidades de elevada disponibilidade e recuperação após desastre de disponibilidade.

Esta arquitetura de referência do armazém de dados também inclui uma camada do Active Directory (AD) para a gestão de recursos dentro da arquitetura. A sub-rede do Active Directory permite a adoção fácil numa estrutura de floresta de AD maior, permitindo que o ambiente da operação contínua, mesmo quando o acesso para a floresta maior não está disponível. Todas as máquinas virtuais estão associados a um domínio para o escalão do Active Directory e utilize políticas de grupo do Active Directory para impor configurações de segurança e conformidade ao nível do sistema operativo.

Uma máquina virtual funciona como um anfitrião de bastião de gestão, fornecendo uma ligação segura para os administradores de recursos de acesso implementado. O carregamento dos dados para a área de teste por meio deste anfitrião de bastião de gestão. **Azure recomenda configurar uma ligação VPN ou Azure ExpressRoute para importação de dados e de gestão para a sub-rede de arquitetura de referência.**

![Armazém de dados para o diagrama de arquitetura de referência do FedRAMP](images/fedramp-datawarehouse-architecture.png?raw=true "armazém de dados para o diagrama de arquitetura de referência do FedRAMP")

Esta solução utiliza os seguintes serviços do Azure. Os detalhes a arquitetura de implementação estão no [arquitetura de implantação](#deployment-architecture) secção.

Máquinas Virtuais do Azure
-   (1) anfitrião de bastião
-   (2) controlador de domínio do as Active Directory
-   (2) nó de Cluster de servidor SQL
-   (1) o SQL Server testemunha

Conjuntos de Disponibilidade
-   (1) controladores de domínio o active Directory
-   (1) nós de cluster do SQL e testemunho

Rede Virtual
-   (4) sub-redes
-   (4) grupos de segurança de rede

SQL Data Warehouse

Serviços de Relatórios do SQL Server

Balanceador de carga SQL do Azure

Azure Active Directory

Cofre dos serviços de recuperação

Azure Key Vault

Registos de Monitor do Azure

## <a name="deployment-architecture"></a>Arquitetura de implantação

A secção seguinte fornece detalhes sobre os elementos de desenvolvimento e implementação.

**O SQL Data Warehouse**: [O SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) é um armazém de dados de empresarial (EDW) que tira partido Massivamente paralela de processamento (MPP) para executar rapidamente consultas complexas em petabytes de dados. Importe grandes volumes de dados para o SQL Data Warehouse com consultas simples de PolyBase T-SQL e utilize o poder do MPP para executar análises de elevado desempenho.

**SQL Server Reporting Services**: [SQL Server Reporting Services](https://docs.microsoft.com/sql/reporting-services/report-data/sql-azure-connection-type-ssrs) permite a rápida criação de relatórios com tabelas, gráficos, mapas, medidores, matrizes e mais para o Azure SQL Data Warehouse.

**Anfitrião de bastião**: O anfitrião de bastião é o único ponto de entrada que permite aos utilizadores aceder os recursos implementados neste ambiente. O anfitrião de bastião fornece uma ligação segura a recursos implementados, permitindo apenas tráfego remoto a partir de endereços IP públicos numa lista segura. Para permitir o tráfego de (protocolo RDP) de área de trabalho remoto, a origem do tráfego precisa de ser definidos no grupo de segurança de rede (NSG).

Uma máquina virtual foi criada como um anfitrião de bastião associados a um domínio com as seguintes configurações:
-   [Extensão de antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [O Azure Monitor registos de extensão](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-oms)
-   [Extensão de diagnóstico do Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [O Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) com o Azure Key Vault (respeita o Azure Government, DSS da PCI, HIPAA e outros requisitos)
-   Uma [política de encerramento automático](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) para reduzir o consumo de recursos de máquina virtual quando não está em utilização
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) ativado para que as credenciais e outros segredos executar num ambiente protegido, que está isolado do sistema operativo em execução

### <a name="virtual-network"></a>Rede virtual
Esta arquitetura de referência define uma rede privada virtual com um espaço de endereços de 10.0.0.0/16.

**Grupos de segurança de rede**: [NSGs](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) contêm o controle de listas de acesso (ACLs) que permitem ou negam o tráfego numa VNet. Podem ser utilizados NSGs para proteger o tráfego numa sub-rede ou o nível VM individual. Existem os NSGs seguintes:
  - Um NSG para a camada de dados (Clusters do SQL Server, SQL Server testemunha e Balanceador de carga SQL)
  - Um NSG para o anfitrião de bastião de gestão
  - Um NSG para o Active Directory
  - Um NSG para o SQL Server Reporting Services

Cada um dos NSGs têm portas específicas e protocolos abrir para que a solução pode trabalhar de forma segura e corretamente. Além disso, as seguintes configurações são habilitadas para cada NSG:
  - [Eventos e registos de diagnóstico](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) forem ativadas e armazenados numa conta de armazenamento
  - Registos de Monitor do Azure está ligada a [diagnósticos do NSG](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Sub-redes**: Cada sub-rede está associado a seu NSG correspondente.

### <a name="data-at-rest"></a>Dados inativos
A arquitetura protege os dados em descanso através da encriptação, a auditoria de base de dados e outras medidas.

**O armazenamento do Azure** de acordo com os dados encriptados em requisitos de rest, todos os [armazenamento do Azure](https://azure.microsoft.com/services/storage/) utiliza [Storage Service Encryption](https://docs.microsoft.com/azure/storage/storage-service-encryption).

**O Azure Disk Encryption**
[Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) aproveita a funcionalidade BitLocker do Windows para fornecer encriptação de volume para o SO e discos de dados. A solução se integra com o Azure Key Vault para ajudar a controlar e gerir as chaves de encriptação de disco.

**Base de dados SQL do Azure** instância de base de dados de SQL do Azure utiliza as seguintes medidas de segurança da base de dados:
-   [Autenticação e autorização AD](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) permite a gestão de identidades de utilizadores de base de dados e outros serviços da Microsoft num local central.
-   [A auditoria de base de dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) faixas de base de dados eventos e escreve-os para uma auditoria registo numa conta de armazenamento do Azure.
-   Base de dados SQL está configurado para utilizar [encriptação de dados transparente (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), que realiza a encriptação em tempo real e desencriptação de dados e arquivos de log para proteger as informações em repouso. TDE fornece garantia de que são armazenados os dados não tiver sido sujeita a acesso não autorizado.
-   [Regras de firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) impedir o acesso de todos os servidores de base de dados até que sejam concedidas as permissões apropriadas. A firewall concede acesso a bases de dados com base no endereço IP de origem de cada pedido.
-   [Deteção de ameaças SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) permite a deteção e resposta a ameaças potenciais à medida que ocorrem ao fornecer alertas de segurança para atividades suspeitas da base de dados, potenciais vulnerabilidades, ataques de injeção de SQL e acesso de base de dados anómalas padrões.
-   [Sempre encriptado colunas](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) Certifique-se de que os dados confidenciais nunca é apresentada como texto não criptografado dentro do sistema de base de dados. Depois de ativar a encriptação de dados, apenas as aplicações de cliente ou servidores de aplicações com acesso às chaves podem aceder aos dados de texto sem formatação.
-   [Máscara de dados dinâmicos da base de dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) pode ser feito depois de implementa a arquitetura de referência. Os clientes terão de ajustar as definições de cumprir para seu esquema de base de dados de máscara de dados dinâmicos.

### <a name="business-continuity"></a>Continuidade do negócio
**Elevada disponibilidade**: Cargas de trabalho do servidor são agrupadas numa [conjunto de disponibilidade](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para ajudar a garantir a elevada disponibilidade de máquinas virtuais no Azure. Pelo menos uma máquina virtual está disponível durante um evento de manutenção não planeada ou não planeada, atendendo a 99,95% do SLA do Azure.

**Cofre de serviços de recuperação**: O [cofre dos Recovery Services](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) hospeda os dados de cópia de segurança e protege todas as configurações de máquinas virtuais do Azure nesta arquitetura. Com um cofre de serviços de recuperação, os clientes podem restaurar ficheiros e pastas a partir de uma VM de IaaS sem restaurar a VM inteira, permitindo que os tempos de restauro mais rápidos.

### <a name="logging-and-audit"></a>Registo e auditoria
[Registos de Monitor do Azure](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) fornece o registo extensivo de atividade do sistema e do usuário, bem como o estado de funcionamento do sistema. O [registos do Azure Monitor](https://azure.microsoft.com/services/log-analytics/) solução recolhe e analisa os dados gerados pelos recursos no Azure e ambientes no local.
- **Registos de atividades**: [Registos de atividades](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) fornecem informações sobre as operações executadas em recursos numa subscrição.
- **Os registos de diagnóstico**: [Os registos de diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) incluem todos os registos emitidos por cada recurso. Estes registos incluem registos de sistema de eventos do Windows e armazenamento de Blobs do Azure, tabelas e registos de fila.
- **Registos de firewall**: O Gateway de aplicação fornece diagnóstico completo e aceder aos registos. Registos da firewall estão disponíveis para recursos de Gateway de aplicação WAF ativada.
- **Arquivo de log**: Todos os registos de diagnóstico escrevem para uma conta de armazenamento do Azure centralizado e criptografado para arquivamento com um período de retenção definida de 2 dias. Estes registos ligar-se aos registos do Azure Monitor para processamento, armazenamento e relatórios do dashboard.

Além disso, as seguintes soluções de monitorização são incluídas como parte desta arquitetura:
-   [Avaliação do AD](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): A solução de verificação de estado de funcionamento do Active Directory avalia o risco e estado de funcionamento dos ambientes de servidor num intervalo regular e fornece uma lista prioritária de recomendações específicas para a infraestrutura de servidor implementado.
-   [Avaliação de antimalware](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): A solução Antimalware relatórios sobre o estado de malware, ameaças e a proteção.
-   [A automatização do Azure](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): A solução de automatização do Azure armazena, executa e gere runbooks.
-   [Segurança e auditoria](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): O dashboard de segurança e auditoria fornece uma alto nível visão do Estado de segurança de recursos, fornecendo as métricas em domínios de segurança, problemas relevantes, deteções, informações sobre ameaças e consultas de segurança comuns.
-   [Avaliação do SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): A solução de verificação de estado de funcionamento do SQL avalia o risco e estado de funcionamento dos ambientes de servidor num intervalo regular e fornece aos clientes uma lista prioritária de recomendações específicas para a infraestrutura de servidor implementado.
-   [Gestão de atualizações](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): A solução de gestão de atualizações permite a gestão de cliente de atualizações de segurança do sistema operativo, incluindo o processo de instalação de atualizações necessárias e o estado das atualizações disponíveis.
-   [Estado de funcionamento do agente](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): A solução de estado de funcionamento do agente relata o número de agentes é implementado e a distribuição geográfica, como número de agentes que não respondem e o número de agentes que está a enviar dados operacionais.
-   [Registos de atividades do Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): A solução Log Analytics da atividade auxilia com uma análise de registos de atividade do Azure em todas as subscrições do Azure para um cliente.
-   [Controlo de alterações](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): A solução de controlo de alterações permite aos clientes identificar facilmente as alterações no ambiente.

### <a name="identity-management"></a>Gestão de identidades
As seguintes tecnologias fornecem capacidades de gestão no ambiente do Azure de identidade:
-   [Active Directory (AD)](https://azure.microsoft.com/services/active-directory/) pode ser multi-inquilino com base na cloud diretório e identidade do serviço de gestão da Microsoft. Todos os utilizadores para a solução foram criados no Azure Active Directory, incluindo os utilizadores que acedem a base de dados SQL.
-   Autenticação para a aplicação é executada com o Azure AD. Para obter mais informações, consulte [integrar aplicações com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Além disso, a encriptação de coluna de base de dados utiliza o Azure AD para autenticar a aplicação à base de dados do Azure SQL. Para obter mais informações, consulte como [proteger dados confidenciais na base de dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [O Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) Deteta potenciais vulnerabilidades que afetam as identidades da organização, configura as respostas automáticas para detetado ações suspeitas relacionadas com identidades de uma organização e investiga incidentes suspeitos para tomar as medidas adequadas para resolvê-los.
-   [Do Azure com base em função de controlo de acesso (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) permite voltada para a gestão de acesso para o Azure. Acesso à subscrição está limitado ao administrador da subscrição.

Para saber mais sobre como utilizar os recursos de segurança da base de dados do Azure SQL, veja a [aplicação de demonstração Contoso Clinic](https://github.com/Microsoft/azure-sql-security-sample) exemplo.

### <a name="security"></a>Segurança
**Gestão de segredos**: A solução utiliza [do Azure Key Vault](https://azure.microsoft.com/services/key-vault/) para o gerenciamento de chaves e segredos. O Cofre de Chaves do Azure ajuda a salvaguardar as chaves criptográficas e os segredos utilizados pelas aplicações em cloud e pelos serviços.

**Proteção contra Malware**: [O Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) para máquinas virtuais fornece a capacidade de proteção em tempo real que ajuda a identificar e remover vírus, spyware e outro software malicioso, com alertas configuráveis quando conhecido software malicioso ou indesejável tenta instalar ou executar nas máquinas virtuais protegidas.

**Gerenciamento de patches**: Máquinas de virtuais do Windows implementadas como parte desta arquitetura de referência estão configuradas por predefinição para receber atualizações automáticas do serviço de atualização do Windows. Esta solução também inclui a [automatização do Azure](https://docs.microsoft.com/azure/automation/automation-intro) serviço por meio do qual as implementações atualizadas podem ser criadas para máquinas de virtuais de patch quando necessário.


## <a name="guidance-and-recommendations"></a>Orientações e recomendações
### <a name="expressroute-and-vpn"></a>ExpressRoute e VPN
[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou de um túnel VPN seguro tem de ser configurado com segurança estabelecer uma ligação para os recursos implementados como parte desta arquitetura de referência do armazém de dados. Como as ligações ExpressRoute não entram na Internet, estas ligações oferecem mais fiabilidade, velocidades mais rápidas, latências mais baixas e maior segurança do que as ligações normais pela Internet. Configurando adequadamente uma VPN ou ExpressRoute, os clientes podem adicionar uma camada de proteção para dados em trânsito.

### <a name="extract-transform-load-etl-process"></a>Processo de Extract-Transform-Load (ETL)
[O PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) pode carregar dados para o Azure SQL Data Warehouse sem a necessidade de um ETL separado ou importar a ferramenta. O PolyBase permite o acesso aos dados através de consultas de T-SQL. Da Microsoft inteligência comercial e pilha de análise, bem como ferramentas de terceiros compatíveis com o SQL Server, podem ser utilizadas com o PolyBase.

### <a name="azure-active-directory-setup"></a>Configuração do Active Directory do Azure
[O Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) é essencial para a gestão da implementação e aprovisionamento do acesso ao pessoal de interação com o ambiente. Um Windows Server Active Directory existente pode ser integrado com o AAD no [quatro cliques](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express). Os clientes podem associar a infraestrutura de Active Directory implementada (controladores de domínio) para um AAD existente, tornando a infraestrutura de Active Directory implementada um subdomínio de uma floresta do AAD.

### <a name="additional-services"></a>Serviços adicionais
Embora esta arquitetura de armazém de dados, não se destina a ser implementação para o [Azure Commercial](https://azure.microsoft.com/overview/what-is-azure/) ambiente, objetivos semelhante podem ser alcançados através de serviços descritos nesta arquitetura de referência, que serviços adicionais disponíveis apenas no ambiente Azure comercial. Tenha em atenção que a Azure Commercial mantém um P-ATO do JAB FedRAMP no nível de impacto moderado, permitindo que as agências governamentais e parceiros para implementar a informações confidenciais moderada para a cloud ao tirar partido do ambiente do Azure Commercial.

O Azure comercial oferece que uma ampla variedade de serviços esse identificador formatado e o armazenamento de dados não formatados e o teste a ser utilizado no armazenamento de dados, incluindo:
-   [O Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) é um serviço cloud gerido criado para híbridos complexos extract-transform-load (ETL), extract-load-transform (ELT) e dados de projetos de integração. Com o Azure Data Factory, os clientes podem criar e agendar fluxos de trabalho condicionados por dados denominados pipelines ingerir dados de arquivos de dados diferentes. Os clientes podem, em seguida, processar e transformar os dados de saída em arquivos de dados, como o Azure SQL Data Warehouse.
-   [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) permite a captura de dados de qualquer tamanho, tipo e velocidade de ingestão num único local para análises exploratórias e operacionais. Azure Data Lake Store é compatível com mais componentes de origem no ecossistema do Hadoop e integra-se perfeitamente com outros serviços do Azure, como o Azure SQL Data Warehouse.

## <a name="threat-model"></a>Modelo de risco

O diagrama de fluxo de dados (DFD) para esta arquitetura de referência está disponível para [transferir](https://aka.ms/blueprintdwthreatmodel) ou podem ser encontradas abaixo:

![Armazém de dados para o modelo de risco do FedRAMP](images/fedramp-datawarehouse-threat-model.png?raw=true "armazém de dados para o modelo de risco do FedRAMP")

## <a name="compliance-documentation"></a>Documentação de conformidade
O [Azure no Security and Compliance Blueprint – matriz de responsabilidade de cliente elevada do FedRAMP](https://aka.ms/blueprinthighcrm) apresenta uma lista de todos os controlos de segurança necessários para a linha de base do FedRAMP elevado. Da mesma forma, o [Azure no Security and Compliance Blueprint – FedRAMP moderado cliente responsabilidade matriz](https://aka.ms/blueprintcrmmod) apresenta uma lista de todos os controlos de segurança necessários para a linha de base do FedRAMP moderado. Ambos os documentos especificar se a implementação de cada controle é da responsabilidade da Microsoft, o cliente, ou partilhados entre os dois.

O [Azure no Security and Compliance Blueprint - matriz de implementação de controle elevada do FedRAMP](https://aka.ms/blueprintdwcimhigh) e o [Azure no Security and Compliance Blueprint - matriz de implementação de controle FedRAMP moderado](https://aka.ms/blueprintdwcimmod) fornecer informações em que os controles estão abrangidos pela arquitetura do armazém de dados para cada linha de base do FedRAMP, incluindo descrições detalhadas de como a implementação cumpre os requisitos de cada controle coberta.

## <a name="disclaimer"></a>Exclusão de Responsabilidade

 - Este documento é apenas para fins informativos. A MICROSOFT FAZ NÃO OFERECE QUAISQUER GARANTIAS, EXPRESSAS, IMPLÍCITAS OU ESTATUTÁRIAS QUANTO ÀS INFORMAÇÕES CONTIDAS NESTE DOCUMENTO. Este documento é fornecido "como-está." Informações e opiniões expressados neste documento, incluindo URLs e outras referências de Web site da Internet, podem ser alteradas sem aviso prévio. Os clientes a ler este documento assume o risco de usá-lo.
 - Este documento não fornece aos clientes quaisquer direitos de propriedade intelectual em qualquer produto da Microsoft ou soluções.
 - Os clientes podem copiar e utilizar este documento para efeitos de referência interna.
 - Algumas recomendações neste documento podem resultar em maior de dados, a rede ou a utilização de recursos de computação no Azure e podem aumentar os custos de licenciamento ou de subscrição do Azure num cliente.
 - Esta arquitetura deve ser usado como uma base para os clientes que se ajustar às suas necessidades específicas e não devem ser utilizada como-está num ambiente de produção.
 - Este documento é desenvolvido como uma referência e não deve ser usado para definir todos os meios pelos quais um cliente pode atender aos requisitos de conformidade e normas. Os clientes procuram legal suporte da sua organização em implementações do cliente aprovadas.
