---
title: Segurança do Azure e o esquema de conformidade - Analytics para FedRAMP
description: Segurança do Azure e o esquema de conformidade - Analytics para FedRAMP
services: security
author: jomolesk
ms.assetid: b4675715-668e-4557-9c1c-698aabf62ceb
ms.service: security
ms.topic: article
ms.date: 05/02/2018
ms.author: jomolesk
ms.openlocfilehash: e3bfc3d0f444bece0afe7b7f5bcdac343a693a13
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57243708"
---
# <a name="azure-security-and-compliance-blueprint-analytics-for-fedramp"></a>Azure Security and Compliance Blueprint: Análise para FedRAMP

## <a name="overview"></a>Descrição geral

O [Federal Risk and Authorization Management Program (FedRAMP)](https://www.fedramp.gov/) é um programa de toda a administração pública dos Estados Unidos que oferece uma abordagem padronizada à avaliação de segurança, autorização e monitorização contínua para cloud produtos e serviços. Este Azure no Security and Compliance Blueprint fornece orientações sobre como fornecer uma arquitetura de análise do Microsoft Azure que o ajuda a implementar um subconjunto de controles do FedRAMP elevado. Esta solução fornece orientações sobre a implementação e configuração de recursos do Azure para uma arquitetura de referência comuns, que demonstram formas em que os clientes podem satisfazer os requisitos específicos de segurança e conformidade e serve como uma base para os clientes criar e configurar suas próprias soluções de análise no Azure.

Esta arquitetura de referência, guias de implementação do controlo associado e modelos de ameaça são deve ser usado como uma base para os clientes que se ajustar às suas necessidades específicas e não devem ser utilizados como-está num ambiente de produção. Implementar uma aplicação para este ambiente sem modificação não é suficiente para atender completamente aos requisitos da linha de base FedRAMP elevado. Tenha em atenção o seguinte:
- A arquitetura fornece uma linha de base para ajudar os clientes a implementar cargas de trabalho para o Azure de forma em conformidade com FedRAMP.
- Os clientes são responsáveis pela realização de segurança adequados e avaliações de conformidade de qualquer solução criada com esta arquitetura, como os requisitos podem variar com base nas especificidades da implementação de cada cliente.

## <a name="architecture-diagram-and-components"></a>Diagrama de arquitetura e componentes

Esta solução oferece uma plataforma de análise sobre a qual os clientes podem criar suas próprias ferramentas de análise. A arquitetura de referência descreve um caso de utilização genérica, onde os dados por meio de importações de dados em massa pelo administrador de dados do SQL ou por meio de atualizações de dados operacionais por meio de um utilizador operacionais de entrada clientes. Ambos os incorporar de fluxos de trabalho [as funções do Azure](https://docs.microsoft.com/azure/azure-functions/functions-overview) para importar dados para a base de dados SQL. As funções do Azure tem de ser configuradas pelo cliente através do Portal do Azure para lidar com as tarefas de importação exclusivas para do cada cliente próprio requisitos de análise.

O Microsoft Azure oferece uma variedade de serviços de relatórios e análise do cliente; No entanto, esta solução incorpora o Azure Analysis Services em conjunto com a base de dados do Azure SQL para navegar rapidamente por dados e fornecer resultados mais rapidamente por meio de forma mais inteligente modelagem dos dados dos clientes. Serviços de análise do Azure é um formulário do machine learning que se destina a aumentar a velocidade de consulta ao detetar relações novas entre conjuntos de dados. Depois dos dados foi treinados por meio de várias funções de estatísticas, até 7 de consulta adicionais conjuntos (8 total, incluindo o servidor de cliente) podem ser sincronizados com os mesmos modelos em tabela para distribuir a carga de trabalho de consulta e reduzir os tempos de resposta.

Para análise avançada e relatórios, bases de dados SQL pode ser configurado com índices columnstore. Serviços de análise do Azure e bases de dados SQL podem ser aumentados vertical ou horizontalmente ou desativar completamente em resposta a utilização do cliente. Todo o tráfego SQL é encriptado com SSL através da inclusão de certificados autoassinados. Como melhor prática, o Azure recomenda a utilização de uma autoridade de certificação fidedigna para uma maior segurança.

Assim que os dados são carregados para a base de dados do SQL do Azure e preparados pelo Azure Analysis Services, é digerida, o utilizador operacional e o administrador de dados do SQL com o Power BI. O Power BI apresenta dados de forma intuitiva e reúne informações em vários conjuntos de dados para organizar melhor maior. O alto grau de capacidade de adaptação e uma integração fácil com a base de dados SQL do Azure garante que os clientes podem configurá-lo para processar uma grande variedade de cenários, conforme exigido pelas suas necessidades de negócios.

A solução inteira é construída num armazenamento do Azure que configurar clientes com uma conta no Portal do Azure. O armazenamento do Azure encripta todos os dados com o Storage Service Encryption para manter a confidencialidade dos dados em repouso.  Armazenamento com redundância geográfica (GRS) garante que um evento adverso no Centro de dados principal do cliente não resultará numa perda de dados como uma segunda cópia será armazenada numa localização separada centenas de quilómetros de distância.

Para maior segurança, esta arquitetura gere os recursos com o Azure Active Directory e o Azure Key Vault. Estado de funcionamento do sistema é monitorizado através do Azure Monitor. Os clientes configurar ambos os serviços de monitorização para capturar os registos e exibir o estado de funcionamento do sistema num dashboard único, navegável facilmente.

Base de dados SQL do Azure normalmente é gerido através do SQL Server Management Studio (SSMS), que é executado a partir de uma máquina local configurada para aceder a base de dados do SQL do Azure através de uma ligação segura de VPN ou ExpressRoute. **Azure recomenda configurar uma ligação VPN ou Azure ExpressRoute para importação de dados e de gestão para o grupo de recursos de arquitetura de referência.**

![Análise do diagrama de arquitetura de referência do FedRAMP](images/fedramp-analytics-reference-architecture.png?raw=true "Analytics para o diagrama de arquitetura de referência do FedRAMP")

### <a name="roles"></a>Funções
O plano gráfico de análise descreve um cenário com três tipos de usuário geral: o utilizador operacional, o administrador de dados do SQL e o engenheiro de sistemas. Do Azure com base em função de controlo de acesso (RBAC) permite a implementação de gestão de acesso precisa através de funções personalizadas incorporadas. Os recursos estão disponíveis para configurar [controlo de acesso baseado em funções](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) e que descreva e implementando [funções predefinidas](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles).

#### <a name="systems-engineer"></a>Engenheiro de sistemas
O engenheiro de sistemas proprietária da subscrição de cliente para o Azure e configura a implementação da solução através do Portal do Azure.

#### <a name="sqldata-administrator"></a>Administrador de dados do SQL
O administrador de dados do SQL estabelece a função de importação de dados em massa e a função de atualização de dados operacionais para carregar para a base de dados SQL do Azure. O administrador de dados do SQL não é responsável por quaisquer atualizações de dados operacionais na base de dados, mas vai poder ver os dados através do Power BI.

#### <a name="operational-user"></a>Utilizador operacional
O utilizador operacional atualiza os dados regularmente e possui a geração de dados diário. O utilizador operacional também irão interpretar os resultados através do Power BI.

### <a name="azure-services"></a>Serviços do Azure

Esta solução utiliza os seguintes serviços do Azure. Os detalhes a arquitetura de implementação estão no [arquitetura de implantação](#deployment-architecture) secção.
- Funções do Azure
- Base de Dados SQL do Azure
- Serviço de análise do Azure
- Azure Active Directory
- Azure Key Vault
- O Azure Monitor (logs)
- Storage do Azure
- Gateway ExpressRoute/VPN
- Dashboard do Power BI

## <a name="deployment-architecture"></a>Arquitetura de implantação
A secção seguinte fornece detalhes sobre os elementos de desenvolvimento e implementação.

![texto alternativo](images/fedramp-analytics-components.png?raw=true "Analytics para o diagrama de componentes do FedRAMP")

**As funções do Azure**: [As funções do Azure](https://docs.microsoft.com/azure/azure-functions/functions-overview) são soluções para a execução de pequenos fragmentos de código por meio da maioria das linguagens de programação na cloud. Integram funções nesta solução com o armazenamento do Azure para automaticamente extrair os dados de cliente para a cloud, facilita a integração com outros serviços do Azure. As funções são facilmente dimensionáveis e incorrem apenas um custo que são executados.

**Serviço de análise do Azure**: [O serviço de análise do Azure](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview) oferece modelação de dados da empresa e a integração com serviços de plataforma de dados do Azure. O serviço de análise do Azure acelera a navegar pelo enormes quantidades de dados ao combinar dados de várias origens para um modelo de dados individual.

**Power BI**: [Power BI](https://docs.microsoft.com/power-bi/service-azure-and-power-bi) fornece análises e funcionalidade de relatórios para os clientes a tentar extrair informações maior proveito de seus esforços de processamento de dados.

### <a name="networking"></a>Redes
**Grupos de segurança de rede**: [NSGs](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) são configuradas de forma a gerir o tráfego direcionado recursos implementados e os serviços. Grupos de segurança de rede estão definidos para um esquema de negar por padrão e permitir apenas o tráfego que está contido na lista pré-configuradas de controlo de acesso (ACL).

Cada um dos NSGs têm portas específicas e protocolos abrir para que a solução pode trabalhar de forma segura e corretamente. Além disso, as seguintes configurações são habilitadas para cada NSG:
  - [Eventos e registos de diagnóstico](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) forem ativadas e armazenados numa conta de armazenamento
  - [Registos de Monitor do Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics) está ligado a registos de diagnóstico do NSG.

### <a name="data-at-rest"></a>Dados inativos
A arquitetura protege os dados em descanso através da encriptação, a auditoria de base de dados e outras medidas.

**Replicação de dados** do Azure Government tem duas opções para [replicação de dados](https://docs.microsoft.com/azure/storage/common/storage-redundancy):
 - A configuração padrão para a replicação de dados está **armazenamento Georredundante (GRS)**, modo assíncrono que armazena os dados do cliente num Datacenter separado fora da região primária. Isto garante a recuperação de dados num evento de perda total para o Datacenter primário.
 - **Armazenamento localmente redundante (LRS)** em alternativa pode ser configurada a conta de armazenamento do Azure. Replica os dados dentro de uma unidade de escala de armazenamento, o que está alojado na mesma região em que o cliente cria a respetiva conta de LRS. Todos os dados são replicados em simultâneo, garantindo que não existem dados de cópia de segurança são perdidos numa falha de unidade de escala de armazenamento primário.

**O armazenamento do Azure** para atender aos dados criptografados em requisitos de rest, todos os serviços implementados neste aproveitamento de arquitetura de referência [armazenamento do Azure](https://azure.microsoft.com/services/storage/), que armazena os dados com [Storage Service Encryption](https://docs.microsoft.com/azure/storage/storage-service-encryption).

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

### <a name="logging-and-audit"></a>Registo e auditoria
[O Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started) gera uma apresentação de completo dos dados, incluindo registos de atividades, métricas e dados de diagnóstico, permitindo aos clientes criar uma visão geral do Estado de funcionamento do sistema de monitorização.  
[Registos de Monitor do Azure](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) fornece o registo extensivo de atividade do sistema e do usuário, bem como o estado de funcionamento do sistema. Ele recolhe e analisa os dados gerados pelos recursos no Azure e ambientes no local.
- **Registos de atividades**: [Registos de atividades](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) fornecem informações sobre as operações executadas em recursos numa subscrição.
- **Os registos de diagnóstico**: [Os registos de diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) incluem todos os registos emitidos por cada recurso. Estes registos incluem registos de sistema de eventos do Windows e armazenamento de Blobs do Azure, tabelas e registos de fila.
- **Registos de firewall**: O Gateway de aplicação fornece diagnóstico completo e aceder aos registos. Registos da firewall estão disponíveis para recursos de Gateway de aplicação WAF ativada.
- **Arquivo de log**: Todos os registos de diagnóstico escrevem para uma conta de armazenamento do Azure centralizado e criptografado para arquivamento com um período de retenção definida de 2 dias. Estes registos ligar-se aos registos do Azure Monitor para processamento, armazenamento e relatórios do dashboard.

Além disso, as seguintes soluções de monitorização são incluídas como parte desta arquitetura:
-   [A automatização do Azure](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): A solução de automatização do Azure armazena, executa e gere runbooks.
-   [Segurança e auditoria](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): O dashboard de segurança e auditoria fornece uma alto nível visão do Estado de segurança de recursos, fornecendo as métricas em domínios de segurança, problemas relevantes, deteções, informações sobre ameaças e consultas de segurança comuns.
-   [Avaliação do SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): A solução de verificação de estado de funcionamento do SQL avalia o risco e estado de funcionamento dos ambientes de servidor num intervalo regular e fornece aos clientes uma lista prioritária de recomendações específicas para a infraestrutura de servidor implementado.
-   [Registos de atividades do Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): A solução Log Analytics da atividade auxilia com uma análise de registos de atividade do Azure em todas as subscrições do Azure para um cliente.

### <a name="identity-management"></a>Gestão de identidades
-   Autenticação para a aplicação é executada com o Azure AD. Para obter mais informações, consulte [integrar aplicações com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Além disso, a encriptação de coluna de base de dados utiliza o Azure AD para autenticar a aplicação à base de dados do Azure SQL. Para obter mais informações, consulte como [proteger dados confidenciais na base de dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [O Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) Deteta potenciais vulnerabilidades que afetam as identidades da organização, configura as respostas automáticas para detetado ações suspeitas relacionadas com identidades de uma organização e investiga incidentes suspeitos para tomar as medidas adequadas para resolvê-los.
-   [Do Azure com base em função de controlo de acesso (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) permite voltada para a gestão de acesso para o Azure. Acesso à subscrição está limitado ao administrador da subscrição.

Para saber mais sobre como utilizar os recursos de segurança da base de dados do Azure SQL, veja a [aplicação de demonstração Contoso Clinic](https://github.com/Microsoft/azure-sql-security-sample) exemplo.

### <a name="security"></a>Segurança
**Gestão de segredos**: A solução utiliza [do Azure Key Vault](https://azure.microsoft.com/services/key-vault/) para o gerenciamento de chaves e segredos. O Cofre de Chaves do Azure ajuda a salvaguardar as chaves criptográficas e os segredos utilizados pelas aplicações em cloud e pelos serviços.

## <a name="guidance-and-recommendations"></a>Orientações e recomendações

### <a name="expressroute-and-vpn"></a>ExpressRoute e VPN
[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou de um túnel VPN seguro tem de ser configurado com segurança estabelecer uma ligação para os recursos implementados como parte desta arquitetura de referência de análise de dados. Como as ligações ExpressRoute não entram na Internet, estas ligações oferecem mais fiabilidade, velocidades mais rápidas, latências mais baixas e maior segurança do que as ligações normais pela Internet. Configurando adequadamente uma VPN ou ExpressRoute, os clientes podem adicionar uma camada de proteção para dados em trânsito.

### <a name="azure-active-directory-setup"></a>Configuração do Active Directory do Azure
[O Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) é essencial para a gestão da implementação e aprovisionamento do acesso ao pessoal de interação com o ambiente. Um Windows Server Active Directory existente pode ser integrado com o AAD no [quatro cliques](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express). Os clientes podem associar a infraestrutura de Active Directory implementada (controladores de domínio) para um AAD existente, tornando a infraestrutura de Active Directory implementada um subdomínio de uma floresta do AAD.

### <a name="additional-services"></a>Serviços adicionais
#### <a name="iaas---vm-vonsiderations"></a>IaaS - vonsiderations VM
Esta solução de PaaS não incorpora a quaisquer VMs de IaaS do Azure. Um cliente pode criar uma VM do Azure para executar muitos destes serviços PaaS. Neste caso, funcionalidades específicas e serviços para continuidade do negócio e registos do Azure Monitor podem ser aproveitados:

##### <a name="business-continuity"></a>Continuidade do negócio
- **Elevada disponibilidade**: Cargas de trabalho do servidor são agrupadas numa [conjunto de disponibilidade](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para ajudar a garantir a elevada disponibilidade de máquinas virtuais no Azure. Pelo menos uma máquina virtual está disponível durante um evento de manutenção não planeada ou não planeada, atendendo a 99,95% do SLA do Azure.

- **Cofre de serviços de recuperação**: O [cofre dos Recovery Services](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) hospeda os dados de cópia de segurança e protege todas as configurações de máquinas virtuais do Azure nesta arquitetura. Com um cofre de serviços de recuperação, os clientes podem restaurar ficheiros e pastas a partir de uma VM de IaaS sem restaurar a VM inteira, permitindo que os tempos de restauro mais rápidos.

##### <a name="monitoring-solutions"></a>Soluções de monitorização
-   [Avaliação do AD](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): A solução de verificação de estado de funcionamento do Active Directory avalia o risco e estado de funcionamento dos ambientes de servidor num intervalo regular e fornece uma lista prioritária de recomendações específicas para a infraestrutura de servidor implementado.
-   [Avaliação de antimalware](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): A solução Antimalware relatórios sobre o estado de malware, ameaças e a proteção.
-   [Gestão de atualizações](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): A solução de gestão de atualizações permite a gestão de cliente de atualizações de segurança do sistema operativo, incluindo o processo de instalação de atualizações necessárias e o estado das atualizações disponíveis.
-   [Estado de funcionamento do agente](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): A solução de estado de funcionamento do agente relata o número de agentes é implementado e a distribuição geográfica, como número de agentes que não respondem e o número de agentes que está a enviar dados operacionais.
-   [Controlo de alterações](https://docs.microsoft.com/azure/automation/automation-change-tracking): A solução de controlo de alterações permite aos clientes identificar facilmente as alterações no ambiente.

##### <a name="security"></a>Segurança
- **Proteção contra Malware**: [O Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) para máquinas virtuais fornece a capacidade de proteção em tempo real que ajuda a identificar e remover vírus, spyware e outro software malicioso, com alertas configuráveis quando conhecido software malicioso ou indesejável tenta instalar ou executar nas máquinas virtuais protegidas.
- **Gerenciamento de patches**: Máquinas de virtuais do Windows implementadas como parte desta arquitetura de referência estão configuradas por predefinição para receber atualizações automáticas do serviço de atualização do Windows. Esta solução também inclui a [automatização do Azure](https://docs.microsoft.com/azure/automation/automation-intro) serviço por meio do qual as implementações atualizadas podem ser criadas para máquinas de virtuais de patch quando necessário.

#### <a name="azure-commercial"></a>Azure Commercial
Embora esta arquitetura de análise de dados, não se destina a ser implementação para o [Azure Commercial](https://azure.microsoft.com/overview/what-is-azure/) ambiente, objetivos semelhante podem ser alcançados através de serviços descritos nesta arquitetura de referência, que serviços adicionais disponíveis apenas no ambiente Azure comercial. Tenha em atenção que a Azure Commercial mantém um P-ATO do JAB FedRAMP no nível de impacto moderado, permitindo que as agências governamentais e parceiros para implementar a informações confidenciais moderada para a cloud ao tirar partido do ambiente do Azure Commercial.

Comercial Azure oferece uma ampla variedade de serviços de análise para extrair informações fora de grandes quantidades de dados:
-   [O Azure Machine Learning Studio](https://docs.microsoft.com/azure/machine-learning/studio/what-is-ml-studio), um componente dos [Cortana Intelligence Suite](https://azure.microsoft.com/overview/ai-platform/), desenvolve um modelo de Análise Preditiva de um ou mais origens de dados. Funções estatísticas são utilizadas ao longo de várias iterações para gerar um modelo eficaz que aplicativos como o Power BI, em seguida, podem consumir.
-   [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) permite a captura de dados de qualquer tamanho, tipo e velocidade de ingestão num único local para análises exploratórias e operacionais. Azure Data Lake Store é compatível com mais componentes de origem no ecossistema do Hadoop e integra-se perfeitamente com outros serviços do Azure.

## <a name="threat-model"></a>Modelo de risco

O diagrama de fluxo de dados (DFD) para esta arquitetura de referência está disponível para [transferir](https://aka.ms/blueprintanalyticsthreatmodel) ou podem ser encontradas abaixo:

## <a name="compliance-documentation"></a>Documentação de conformidade
O [Azure no Security and Compliance Blueprint – matriz de responsabilidade de cliente elevada do FedRAMP](https://aka.ms/blueprinthighcrm) apresenta uma lista de todos os controlos de segurança necessários para a linha de base do FedRAMP elevado. Da mesma forma, o [Azure no Security and Compliance Blueprint – FedRAMP moderado cliente responsabilidade matriz](https://aka.ms/blueprintanalyticscimmod) apresenta uma lista de todos os controlos de segurança necessários para a linha de base do FedRAMP moderado. Ambos os documentos especificar se a implementação de cada controle é da responsabilidade da Microsoft, o cliente, ou partilhados entre os dois.

O [Azure no Security and Compliance Blueprint - matriz de implementação de controle elevada do FedRAMP](https://aka.ms/blueprintanalyticscimhigh) e o [Azure no Security and Compliance Blueprint - matriz de implementação de controle FedRAMP moderado](https://aka.ms/blueprintanalyticscimmod) fornecer informações em que os controles estão abrangidos pela arquitetura de análise para cada linha de base do FedRAMP, incluindo descrições detalhadas de como a implementação cumpre os requisitos de cada controle coberta.

## <a name="disclaimer"></a>Exclusão de Responsabilidade

 - Este documento é apenas para fins informativos. A MICROSOFT FAZ NÃO OFERECE QUAISQUER GARANTIAS, EXPRESSAS, IMPLÍCITAS OU ESTATUTÁRIAS QUANTO ÀS INFORMAÇÕES CONTIDAS NESTE DOCUMENTO. Este documento é fornecido "como-está." Informações e opiniões expressados neste documento, incluindo URLs e outras referências de Web site da Internet, podem ser alteradas sem aviso prévio. Os clientes a ler este documento assume o risco de usá-lo.
 - Este documento não fornece aos clientes quaisquer direitos de propriedade intelectual em qualquer produto da Microsoft ou soluções.
 - Os clientes podem copiar e utilizar este documento para efeitos de referência interna.
 - Algumas recomendações neste documento podem resultar em maior de dados, a rede ou a utilização de recursos de computação no Azure e podem aumentar os custos de licenciamento ou de subscrição do Azure num cliente.
 - Esta arquitetura deve ser usado como uma base para os clientes que se ajustar às suas necessidades específicas e não devem ser utilizada como-está num ambiente de produção.
 - Este documento é desenvolvido como uma referência e não deve ser usado para definir todos os meios pelos quais um cliente pode atender aos requisitos de conformidade e normas. Os clientes procuram legal suporte da sua organização em implementações do cliente aprovadas.
