---
title: Blueprint de Segurança e Conformidade do Azure-data warehouse para a automação FedRAMP
description: Blueprint de Segurança e Conformidade do Azure-data warehouse para a automação FedRAMP
services: security
author: jomolesk
ms.assetid: 834d1ff6-8369-455f-b052-1ef301e3d7e6
ms.service: security
ms.topic: article
ms.date: 05/02/2018
ms.author: jomolesk
ms.openlocfilehash: ada041640cb66f756f8976fa5290592f11ff1cad
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/05/2019
ms.locfileid: "68778917"
---
# <a name="azure-security-and-compliance-blueprint-data-warehouse-for-fedramp-automation"></a>Esquema de Segurança e Conformidade do Azure: data warehouse para a automação do FedRAMP

## <a name="overview"></a>Descrição geral

O [Federal Risk and Authorization Management Program (FedRAMP)](https://www.fedramp.gov/) é um programa Estados Unidos de todo o governo que fornece uma abordagem padronizada para avaliação de segurança, autorização e monitoramento contínuo para produtos e serviços de nuvem. Este Blueprint de Segurança e Conformidade do Azure fornece diretrizes sobre como fornecer uma arquitetura de data warehouse de Microsoft Azure que ajuda a implementar um subconjunto de controles altos de FedRAMP. Esta solução fornece orientação sobre a implantação e a configuração de recursos do Azure para uma arquitetura de referência comum, demonstrando maneiras nas quais os clientes podem atender aos requisitos específicos de segurança e conformidade e serve como base para os clientes Crie e configure suas próprias soluções de data warehouse no Azure.

Essa arquitetura de referência, os guias de implementação de controle associados e os modelos de ameaça são destinados a servir como base para os clientes se ajustarem aos requisitos específicos e não devem ser usados no estado em que se encontram em um ambiente de produção. Implantar um aplicativo nesse ambiente sem modificação é insuficiente para atender totalmente aos requisitos da linha de base FedRAMP alta. Tenha em atenção o seguinte:
- A arquitetura fornece uma linha de base para ajudar os clientes a implantar cargas de trabalho no Azure de maneira compatível com FedRAMP.
- Os clientes são responsáveis por realizar avaliações apropriadas de segurança e conformidade de qualquer solução criada usando essa arquitetura, já que os requisitos podem variar com base nas especificidades da implementação de cada cliente.

## <a name="architecture-diagram-and-components"></a>Diagrama e componentes da arquitetura

Essa solução fornece uma arquitetura de referência data warehouse que implementa um data warehouse de nuvem segura e de alto desempenho. Há duas camadas de dados separadas nesta arquitetura: uma em que os dados são importados, armazenados e preparados em um ambiente SQL clusterizado e outro para o Azure SQL Data Warehouse onde os dados são carregados usando uma ferramenta ETL (por exemplo, consultas T-SQL do [polybase](https://docs.microsoft.com/azure/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase) ) para processamento. Depois que os dados são armazenados no Azure SQL Data Warehouse, a análise pode ser executada em grande escala.

O Microsoft Azure oferece uma variedade de relatórios e serviços de análise para o cliente. Essa solução inclui SQL Server Reporting Services (SSRS) para a criação rápida de relatórios da SQL Data Warehouse do Azure. Todo o tráfego do SQL é criptografado com SSL por meio da inclusão de certificados autoassinados. Como prática recomendada, o Azure recomenda o uso de uma autoridade de certificação confiável para aumentar a segurança.

Os dados na SQL Data Warehouse do Azure são armazenados em tabelas relacionais com armazenamento de coluna, um formato que reduz significativamente os custos de armazenamento de dados ao mesmo tempo em que melhora o desempenho da consulta.  Dependendo dos requisitos de uso, os recursos de computação do Azure SQL Data Warehouse podem ser escalados ou reduzidos verticalmente ou desligados completamente se não houver nenhum processo ativo exigindo recursos de computação.

Um balanceador de carga do SQL gerencia o tráfego do SQL, garantindo alto desempenho. Todas as máquinas virtuais nessa arquitetura de referência são implantadas em um conjunto de disponibilidade com SQL Server instâncias configuradas em um grupo de disponibilidade AlwaysOn para recursos de alta disponibilidade e recuperação de desastres.

Essa data warehouse arquitetura de referência também inclui uma camada de Active Directory (AD) para o gerenciamento de recursos dentro da arquitetura. A sub-rede Active Directory permite uma adoção fácil em uma estrutura de floresta do AD maior, permitindo a operação contínua do ambiente, mesmo quando o acesso à floresta maior não está disponível. Todas as máquinas virtuais são ingressadas no domínio na camada de Active Directory e usam Active Directory políticas de grupo para impor configurações de segurança e conformidade no nível do sistema operacional.

Uma máquina virtual serve como um host de bastiões de gerenciamento, fornecendo uma conexão segura para os administradores acessarem recursos implantados. Os dados são carregados na área de preparo por meio desse host de bastiões de gerenciamento. **O Azure recomenda configurar uma conexão VPN ou Azure ExpressRoute para gerenciamento e importação de dados na sub-rede da arquitetura de referência.**

![Data warehouse para diagrama de arquitetura de referência FedRAMP](images/fedramp-datawarehouse-architecture.png?raw=true "Data warehouse para diagrama de arquitetura de referência FedRAMP")

Essa solução usa os seguintes serviços do Azure. Os detalhes da arquitetura de implantação estão na seção [arquitetura de implantação](#deployment-architecture) .

Máquinas Virtuais do Azure
-   (1) host de bastiões
-   (2) Active Directory controlador de domínio
-   (2) SQL Server nó de cluster
-   (1) SQL Server testemunha

Conjuntos de Disponibilidade
-   (1) Active Directory controladores de domínio
-   (1) nós de cluster do SQL e testemunha

Rede virtual
-   (4) sub-redes
-   (4) grupos de segurança de rede

SQL Data Warehouse

Serviços de Relatórios do SQL Server

Load Balancer do SQL do Azure

Azure Active Directory

Cofre dos serviços de recuperação

Azure Key Vault

Registos do Azure Monitor

## <a name="deployment-architecture"></a>Arquitetura de implantação

A seção a seguir detalha os elementos de desenvolvimento e implementação.

**SQL data warehouse**: A [SQL data warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) é uma EDW (Enterprise Data warehouse) que aproveita o MPP (processamento paralelo maciço) para executar rapidamente consultas complexas em petabytes de dados. Importe Big Data para SQL Data Warehouse com consultas T-SQL simples do polybase e use o poder do MPP para executar análises de alto desempenho.

**SQL Server Reporting Services**: [SQL Server Reporting Services](https://docs.microsoft.com/sql/reporting-services/report-data/sql-azure-connection-type-ssrs) permite a criação rápida de relatórios com tabelas, gráficos, mapas, medidores, matrizes e muito mais para o Azure SQL data warehouse.

**Host bastião**: O host de bastiões é o ponto único de entrada que permite aos usuários acessar os recursos implantados nesse ambiente. O host de bastiões fornece uma conexão segura com os recursos implantados permitindo apenas o tráfego remoto de endereços IP públicos em uma lista segura. Para permitir o tráfego de área de trabalho remota (RDP), a origem do tráfego precisa ser definida no grupo de segurança de rede (NSG).

Uma máquina virtual foi criada como um host de bastiões ingressado no domínio com as seguintes configurações:
-   [Extensão de antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware)
-   [Extensão de logs de Azure Monitor](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-oms)
-   [Extensão de Diagnóstico do Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) usando Azure Key Vault (respeita o Azure governamental, PCI DSS, HIPAA e outros requisitos)
-   Uma [política](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) de desligamento automático para reduzir o consumo de recursos de máquina virtual quando não estiver em uso
-   O [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) habilitado para que as credenciais e outros segredos sejam executados em um ambiente protegido isolado do sistema operacional em execução

### <a name="virtual-network"></a>Rede virtual
Essa arquitetura de referência define uma rede virtual privada com um espaço de endereço de 10.0.0.0/16.

**Grupos de segurança de rede**: [NSGs](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) contêm ACLs (listas de controle de acesso) que permitem ou negam o tráfego em uma VNet. NSGs pode ser usado para proteger o tráfego em uma sub-rede ou nível de VM individual. Os seguintes NSGs existem:
  - Um NSG para a camada de dados (SQL Server clusters, SQL Server testemunha e SQL Load Balancer)
  - Um NSG para o host de bastiões de gerenciamento
  - Um NSG para Active Directory
  - Um NSG para SQL Server Reporting Services

Cada um dos NSGs tem portas e protocolos específicos abertos para que a solução possa funcionar de forma segura e correta. Além disso, as seguintes configurações estão habilitadas para cada NSG:
  - [Os logs e eventos de diagnóstico](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) são habilitados e armazenados em uma conta de armazenamento
  - Os logs de Azure Monitor estão conectados ao [diagnóstico do NSG](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Sub-redes**: Cada sub-rede é associada ao seu NSG correspondente.

### <a name="data-at-rest"></a>Dados inativos
A arquitetura protege os dados em repouso por meio de criptografia, auditoria de banco e outras medidas.

**Armazenamento do Azure** Para atender aos requisitos de dados criptografados em repouso, todo o [armazenamento do Azure](https://azure.microsoft.com/services/storage/) usa [criptografia do serviço de armazenamento](https://docs.microsoft.com/azure/storage/storage-service-encryption).

AzureDiskEncryption
[Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) aproveita o recurso BitLocker do Windows para fornecer criptografia de volume para o sistema operacional e discos de dados. A solução se integra com Azure Key Vault para ajudar a controlar e gerenciar as chaves de criptografia de disco.

**Banco de dados SQL do Azure** A instância do banco de dados SQL do Azure usa as seguintes medidas de segurança de banco de dados:
-   A [autenticação e a autorização do AD](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) permitem o gerenciamento de identidades de usuários de banco de dados e outros serviços da Microsoft em um local central.
-   A [auditoria do banco de dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) rastreia eventos de banco de dados e os grava em um log de auditoria em uma conta de armazenamento do Azure.
-   O banco de dados SQL está configurado para usar o [Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), que executa criptografia e descriptografia em tempo real de arquivos de log e data para proteger informações em repouso. O TDE fornece garantia de que os dados armazenados não estão sujeitos a acesso não autorizado.
-   [As regras de firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) impedem todo o acesso a servidores de banco de dados até que sejam concedidas permissões adequadas A firewall concede acesso a bases de dados com base no endereço IP de origem de cada pedido.
-   A [detecção de ameaças do SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) permite a detecção e a resposta a ameaças potenciais à medida que elas ocorrem, fornecendo alertas de segurança para atividades suspeitas de banco de dados, vulnerabilidades potenciais, ataques de injeção de SQL e padrões de acesso de banco de dados anormais
-   [Always Encrypted colunas](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) garantem que os dados confidenciais nunca apareçam como texto não criptografado no sistema de banco de dados. Depois de habilitar a criptografia de dados, somente aplicativos cliente ou servidores de aplicativos com acesso às chaves podem acessar dados de texto sem formatação.
-   O mascaramento de [dados dinâmicos do banco SQL](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) pode ser feito após a implantação da arquitetura de referência. Os clientes precisarão ajustar as configurações de mascaramento de dados dinâmicos para aderir ao seu esquema de banco de dado.

### <a name="business-continuity"></a>Continuidade do negócio
**Elevada disponibilidade**: As cargas de trabalho do servidor são agrupadas em um [conjunto de disponibilidade](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para ajudar a garantir a alta disponibilidade de máquinas virtuais no Azure. Pelo menos uma máquina virtual está disponível durante um evento de manutenção planejada ou não planejada, atendendo ao SLA de 99,95% do Azure.

**Cofre dos serviços de recuperação**: O [cofre dos serviços de recuperação](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) hospeda dados de backup e protege todas as configurações de máquinas virtuais do Azure nessa arquitetura. Com um cofre dos serviços de recuperação, os clientes podem restaurar arquivos e pastas de uma VM IaaS sem restaurar toda a VM, permitindo tempos de restauração mais rápidos.

### <a name="logging-and-audit"></a>Registro em log e auditoria
[Os logs de Azure monitor](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) fornecem log extensivo de atividade do sistema e do usuário, bem como a integridade do sistema. A solução de [logs de Azure monitor](https://azure.microsoft.com/services/log-analytics/) coleta e analisa os dados gerados pelos recursos no Azure e em ambientes locais.
- **Logs de atividade**: [Os logs de atividade](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) fornecem informações sobre as operações executadas nos recursos em uma assinatura.
- **Logs de diagnóstico**: Os [logs de diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) incluem todos os logs emitidos por cada recurso. Esses logs incluem logs do sistema de eventos do Windows e armazenamento de BLOBs do Azure, tabelas e logs de fila.
- **Logs de firewall**: O gateway de aplicativo fornece logs completos de diagnóstico e acesso. Os logs de firewall estão disponíveis para recursos de gateway de aplicativo habilitados para WAF.
- **Arquivamento de log**: Todos os logs de diagnóstico gravam em uma conta de armazenamento do Azure centralizada e criptografada para arquivamento com um período de retenção definido de 2 dias. Esses logs se conectam a logs de Azure Monitor para processamento, armazenamento e relatórios de painel.

Além disso, as soluções de monitoramento a seguir são incluídas como parte dessa arquitetura:
-   [Avaliação do AD](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): A solução de verificação de integridade Active Directory avalia o risco e a integridade dos ambientes de servidor em um intervalo regular e fornece uma lista priorizada de recomendações específicas para a infraestrutura de servidor implantada.
-   [Avaliação de antimalware](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): A solução antimalware relata sobre malware, ameaças e status de proteção.
-   [Automação do Azure](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): A solução de automação do Azure armazena, executa e gerencia runbooks.
-   [Segurança e auditoria](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): O painel de Segurança e Auditoria fornece uma percepção de alto nível do estado de segurança dos recursos, fornecendo métricas sobre domínios de segurança, problemas notáveis, detecções, inteligência contra ameaças e consultas de segurança comuns.
-   [Avaliação do SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): A solução de verificação de integridade do SQL avalia o risco e a integridade dos ambientes de servidor em um intervalo regular e fornece aos clientes uma lista priorizada de recomendações específicas para a infraestrutura de servidor implantada.
-   [Gerenciamento de atualizações](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): A solução Gerenciamento de Atualizações permite o gerenciamento de clientes das atualizações de segurança do sistema operacional, incluindo o status das atualizações disponíveis e o processo de instalação das atualizações necessárias.
-   [Integridade do agente](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): A solução Integridade do Agente relata Quantos agentes são implantados e sua distribuição geográfica, bem como Quantos agentes que não respondem e o número de agentes que estão enviando dados operacionais.
-   [Logs de atividades do Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): A solução Análise do Log de Atividades auxilia na análise dos logs de atividade do Azure em todas as assinaturas do Azure para um cliente.
-   [Controle de alterações](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): A solução Controle de Alterações permite que os clientes identifiquem facilmente as alterações no ambiente.

### <a name="identity-management"></a>Gestão de identidades
As tecnologias a seguir fornecem recursos de gerenciamento de identidade no ambiente do Azure:
-   O [Active Directory (AD)](https://azure.microsoft.com/services/active-directory/) pode ser o serviço de gerenciamento de identidade e diretório multilocatário baseado em nuvem da Microsoft. Todos os usuários da solução foram criados no Azure Active Directory, incluindo usuários que acessam o banco de dados SQL.
-   A autenticação para o aplicativo é executada usando o Azure AD. Para obter mais informações, consulte [integrando aplicativos com Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Além disso, a criptografia de coluna de banco de dados usa o Azure AD para autenticar o aplicativo no banco de dados SQL do Azure. Para obter mais informações, Confira como [proteger dados confidenciais no SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) detecta possíveis vulnerabilidades que afetam as identidades de uma organização, configura as respostas automatizadas para detectar ações suspeitas relacionadas às identidades de uma organização e investiga incidentes suspeitos para tomar as medidas apropriadas para resolvê-los.
-   O [RBAC (controle de acesso baseado em função) do Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) permite o gerenciamento de acesso focado para o Azure. O acesso à assinatura é limitado ao administrador da assinatura.

Para saber mais sobre como usar os recursos de segurança do banco de dados SQL do Azure, consulte o exemplo de [aplicativo de demonstração da Contoso Clinic](https://github.com/Microsoft/azure-sql-security-sample) .

### <a name="security"></a>Segurança
**Gerenciamento de segredos**: A solução usa [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) para o gerenciamento de chaves e segredos. O Cofre de Chaves do Azure ajuda a salvaguardar as chaves criptográficas e os segredos utilizados pelas aplicações em cloud e pelos serviços.

**Proteção contra malware**: [O Microsoft Antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware) para máquinas virtuais fornece recursos de proteção em tempo real que ajudam a identificar e remover vírus, spyware e outros softwares mal-intencionados, com alertas configuráveis quando um software mal-intencionado ou indesejado conhecido tenta instalar ou executar em máquinas virtuais protegidas.

**Gerenciamento**de patches: As máquinas virtuais do Windows implantadas como parte dessa arquitetura de referência são configuradas por padrão para receber atualizações automáticas do serviço Windows Update. Essa solução também inclui o serviço de [automação do Azure](https://docs.microsoft.com/azure/automation/automation-intro) por meio do qual implantações atualizadas podem ser criadas para aplicar patch às máquinas virtuais quando necessário.


## <a name="guidance-and-recommendations"></a>Diretrizes e recomendações
### <a name="expressroute-and-vpn"></a>ExpressRoute e VPN
O [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou um túnel VPN seguro precisa ser configurado para estabelecer com segurança uma conexão com os recursos implantados como parte dessa data warehouse arquitetura de referência. Como as conexões do ExpressRoute não passam pela Internet, essas conexões oferecem mais confiabilidade, velocidades mais rápidas, latências menores e mais segurança do que as conexões típicas pela Internet. Ao configurar adequadamente o ExpressRoute ou uma VPN, os clientes podem adicionar uma camada de proteção para os dados em trânsito.

### <a name="extract-transform-load-etl-process"></a>Processo de extração de transformação/carregamento (ETL)
O [polybase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) pode carregar dados no Azure SQL data warehouse sem a necessidade de uma ferramenta de importação ou ETL separada. O polybase permite o acesso a dados por meio de consultas T-SQL. A business intelligence e a pilha de análise da Microsoft, bem como ferramentas de terceiros compatíveis com o SQL Server, podem ser usadas com o polybase.

### <a name="azure-active-directory-setup"></a>Azure Active Directory configuração
[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) é essencial para gerenciar a implantação e o provisionamento de acesso à equipe interagindo com o ambiente. Um Active Directory do Windows Server existente pode ser integrado com o AAD em [quatro cliques](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express). Os clientes também podem vincular a infraestrutura de Active Directory implantada (controladores de domínio) a um AAD existente, tornando a infraestrutura de Active Directory implantada um subdomínio de uma floresta do AAD.

### <a name="additional-services"></a>Serviços adicionais
Embora essa arquitetura de data warehouse não seja destinada à implantação no ambiente [comercial do Azure](https://azure.microsoft.com/overview/what-is-azure/) , objetivos semelhantes podem ser obtidos por meio dos serviços descritos nesta arquitetura de referência, bem como serviços adicionais disponíveis somente no ambiente comercial do Azure. Observe que o Azure Commercial mantém um FedRAMP JAB P-ATO no nível de impacto moderado, permitindo que agências governamentais e parceiros implantem informações moderadamente confidenciais na nuvem, aproveitando o ambiente comercial do Azure.

O Azure Commercial oferece uma ampla variedade de serviços que manipulam o armazenamento de dados formatados e não formatados e o preparo a ser usado em data warehousing, incluindo:
-   O [Azure data Factory](https://docs.microsoft.com/azure/data-factory/introduction) é um serviço de nuvem gerenciado criado para projetos complexos de extração de transformação e carregamento (ETL), de extração/carregamento/transformação (ELT) e de integração de dados. Usando Azure Data Factory, os clientes podem criar e agendar fluxos de trabalho controlados por dados chamados pipelines que insomem dados de armazenamentos de dados diferentes. Os clientes podem processar e transformar os dados para saída em armazenamentos de dados, como o Azure SQL Data Warehouse.
-   [Azure data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) permite a captura de dados de qualquer tamanho, tipo e velocidade de ingestão em um único lugar para análise operacional e exploratório. Azure Data Lake Store é compatível com a maioria dos componentes de software livre no ecossistema do Hadoop e se integra perfeitamente com outros serviços do Azure, como o SQL Data Warehouse do Azure.

## <a name="threat-model"></a>Modelo de ameaça

O DFD (diagrama de fluxo de dados) para esta arquitetura de referência está disponível para [Download](https://aka.ms/blueprintdwthreatmodel) ou pode ser encontrado abaixo:

![Data warehouse para o modelo de ameaça FedRAMP](images/fedramp-datawarehouse-threat-model.png?raw=true "Data warehouse para o modelo de ameaça FedRAMP")

## <a name="compliance-documentation"></a>Documentação de conformidade
A [matriz de responsabilidade do cliente Blueprint de segurança e conformidade do Azure – FedRAMP alta](https://aka.ms/blueprinthighcrm) lista todos os controles de segurança exigidos pela linha de base FedRAMP alta. Da mesma forma, a [matriz de responsabilidade do cliente Blueprint de segurança e conformidade do Azure – FedRAMP moderada](https://aka.ms/blueprintcrmmod) lista todos os controles de segurança exigidos pela linha de base moderada FedRAMP. Os dois documentos detalham se a implementação de cada controle é de responsabilidade da Microsoft, do cliente ou do compartilhamento entre os dois.

A [matriz de implementação de controle alto de Blueprint de segurança e conformidade do Azure FedRAMP](https://aka.ms/blueprintdwcimhigh) e a [matriz de implementação de controle moderado Blueprint de segurança e conformidade do Azure-FedRAMP](https://aka.ms/blueprintdwcimmod) fornecem informações sobre quais controles são cobertos por a arquitetura de data warehouse para cada linha de base FedRAMP, incluindo descrições detalhadas de como a implementação atende aos requisitos de cada controle abordado.

## <a name="disclaimer"></a>Exclusão de Responsabilidade

 - Este documento é apenas para fins informativos. A MICROSOFT NÃO OFERECE NENHUMA GARANTIA, EXPRESSA, IMPLÍCITA OU ESTATUTÁRIA, QUANTO ÀS INFORMAÇÕES CONTIDAS NESTE DOCUMENTO. Este documento é fornecido "no estado em que se encontra". As informações e as exibições expressas neste documento, incluindo URLs e outras referências de site da Internet, podem ser alteradas sem aviso prévio. Os clientes que lêem este documento trazem o risco de usá-lo.
 - Este documento não fornece aos clientes nenhum direito legal sobre qualquer propriedade intelectual em qualquer produto ou solução da Microsoft.
 - Os clientes podem copiar e usar este documento para fins de referência interna.
 - Determinadas recomendações neste documento podem resultar em maior uso de recursos de computação, rede ou dados no Azure, e podem aumentar os custos de assinatura ou de licença do Azure do cliente.
 - Essa arquitetura destina-se a servir como base para os clientes se ajustarem aos requisitos específicos e não devem ser usados no estado em que se encontram em um ambiente de produção.
 - Este documento é desenvolvido como uma referência e não deve ser usado para definir todos os meios pelos quais um cliente pode atender a requisitos e regulamentos específicos de conformidade. Os clientes devem buscar o suporte legal de sua organização em implementações aprovadas do cliente.
