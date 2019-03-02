---
title: Segurança do Azure e o esquema de conformidade - análise de dados do PCI DSS
description: Segurança do Azure e o esquema de conformidade - análise de dados do PCI DSS
services: security
author: meladie
ms.assetid: 6c48cd8e-c548-44e8-a21a-17316c152cbb
ms.service: security
ms.topic: article
ms.date: 07/03/2018
ms.author: meladie
ms.openlocfilehash: 6a2ffef9a1384ac2e2a678404171f33312b5d63a
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57247448"
---
# <a name="azure-security-and-compliance-blueprint-analytics-for-pci-dss"></a>Azure Security and Compliance Blueprint: Análise do PCI DSS

## <a name="overview"></a>Descrição geral

Este Azure no Security and Compliance Blueprint fornece orientações para a implementação de uma arquitetura de análise de dados no Azure que ajuda a com os requisitos de normas de segurança do dados de setor de cartão de pagamento (PCI DSS 3.2). Ele demonstra uma arquitetura de referência comuns e demonstra o processamento adequado dos dados de cartão de crédito (incluindo dados de número, a expiração e a verificação do cartão) num ambiente seguro, em conformidade, com várias camado. Este esquema demonstra maneiras em que os clientes podem satisfazer requisitos de conformidade e de segurança específicas e serve como uma base para os clientes criar e configurar suas próprias soluções de análise de dados no Azure.

Esta arquitetura de referência, o guia de implementação e o modelo de ameaças fornecem uma Fundação para os clientes estão em conformidade com PCI DSS 3.2 requisitos. Esta solução fornece uma linha de base para ajudar os clientes a implementar cargas de trabalho no Azure numa maneira em conformidade com PCI DSS 3.2. No entanto, esta solução não deve ser utilizada como-está num ambiente de produção, porque é necessária configuração adicional.

Alcançando a conformidade do PCI DSS requer que um Assessor acreditada de segurança qualificado (QSA) certificar uma solução de cliente de produção. Os clientes são responsáveis pela realização de segurança adequados e avaliações de conformidade de qualquer solução criada com esta arquitetura, como os requisitos podem variar com base nas especificidades da implementação de cada cliente.

## <a name="architecture-diagram-and-components"></a>Diagrama de arquitetura e componentes

Este Azure no Security and Compliance Blueprint fornece uma plataforma de análise sobre a qual os clientes podem criar suas próprias ferramentas de análise. A arquitetura de referência descreve um caso de utilização genérica, onde os dados por meio de importações de dados em massa pelo administrador de dados do SQL ou por meio de atualizações de dados operacionais por meio de um utilizador operacionais de entrada clientes. Ambos os fluxos de trabalho incorporam as funções do Azure para importar dados para a base de dados do Azure SQL. As funções do Azure tem de ser configuradas pelo cliente através do portal do Azure para lidar com as tarefas de importação exclusivas para do cada cliente próprio requisitos de análise.

O Azure oferece uma variedade de serviços de relatórios e análise para os clientes. Esta solução incorpora serviços do Azure Machine Learning em conjunto com a base de dados do Azure SQL para navegar rapidamente por dados e fornecer resultados mais rapidamente por meio de modelagem de forma mais inteligente. O Azure Machine Learning aumenta as velocidades de consulta ao detetar relações novas entre conjuntos de dados. Depois dos dados foi treinados por meio de várias funções de estatísticas, até 7 de consulta adicionais conjuntos (8 total, incluindo o servidor de cliente) podem ser sincronizados com os mesmos modelos em tabela para distribuir as cargas de trabalho de consulta e reduzir os tempos de resposta.

Para análise avançada e relatórios, bases de dados SQL do Azure podem ser configurados com índices columnstore. Bases de dados do Azure Machine Learning e o SQL do Azure podem ser aumentados vertical ou horizontalmente ou desativar completamente em resposta a utilização do cliente. Todo o tráfego SQL é encriptado com SSL através da inclusão de certificados autoassinados. Como melhor prática, o Azure recomenda a utilização de uma autoridade de certificação fidedigna para uma maior segurança.

Assim que os dados são carregados para a base de dados do SQL do Azure e formação do Azure Machine Learning, é digerida, o utilizador operacional e o administrador de dados do SQL com o Power BI. O Power BI apresenta dados de forma intuitiva e reúne informações em vários conjuntos de dados para organizar melhor maior. O alto grau de capacidade de adaptação e uma integração fácil com a base de dados SQL do Azure garante que os clientes podem configurá-lo para processar uma grande variedade de cenários, conforme exigido pelas suas necessidades de negócios.

A solução utiliza contas de armazenamento do Azure, o que os clientes podem configurar para utilizar a encriptação do serviço de armazenamento para manter a confidencialidade dos dados em repouso. Azure armazena três cópias dos dados num datacenter selecionados de um cliente para resiliência. Armazenamento com redundância geográfico garante que os dados vão ser replicados para um datacenter secundário centenas de quilómetros de distância e novamente armazenados como três cópias dentro desse datacenter, impedir que um evento adverso no Centro de dados principal do cliente resulta numa perda de dados.

Para maior segurança, todos os recursos nesta solução são geridos como um grupo de recursos através do Gestor de recursos do Azure. O Azure Active Directory, o controlo de acesso baseado em funções é utilizado para controlar o acesso ao implementar recursos, incluindo as chaves no Azure Key Vault. Estado de funcionamento do sistema é monitorizado através do Centro de segurança do Azure e o Azure Monitor. Os clientes configurar ambos os serviços de monitorização para capturar os registos e exibir o estado de funcionamento do sistema num dashboard único, navegável facilmente.

Base de dados SQL do Azure normalmente é gerido através do SQL Server Management Studio (SSMS), que é executado a partir de uma máquina local configurada para aceder a base de dados do SQL do Azure através de uma ligação segura de VPN ou ExpressRoute. **A Microsoft recomenda configurar uma ligação VPN ou ExpressRoute para dados de gestão e importar para o grupo de recursos de arquitetura de referência**.

![Análise do diagrama de arquitetura de referência de PCI DSS](images/pcidss-analytics-architecture.png "Analytics para o diagrama de arquitetura de referência de PCI DSS")

Esta solução utiliza os seguintes serviços do Azure. Os detalhes a arquitetura de implementação estão no [arquitetura de implantação](#deployment-architecture) secção.

- Application Insights
- Azure Active Directory
- Catálogo de Dados do Azure
- Azure Disk Encryption
- Azure Event Grid
- Funções do Azure
- Azure Key Vault
- Azure Machine Learning
- Azure Monitor
- Centro de Segurança do Azure
- Base de Dados SQL do Azure
- Storage do Azure
- Rede Virtual do Azure
    - (1) /16 de rede
    - (2) /24 redes
    - (2) grupos de segurança de rede
- Dashboard do Power BI

## <a name="deployment-architecture"></a>Arquitetura de implantação

A secção seguinte fornece detalhes sobre os elementos de implantação e a implementação.

**Azure Event Grid**: [O Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview) permite que os clientes podem criar facilmente aplicações com arquiteturas baseadas em eventos. Os utilizadores selecionam o recurso do Azure que gostariam de subscrever e dar o manipulador de eventos ou um ponto de extremidade para enviar o evento de webhook. Os clientes possam proteger pontos finais de webhook ao adicionar parâmetros de consulta para o URL do webhook durante a criação de uma subscrição de evento. O Azure Event Grid suporta apenas pontos finais do webhook HTTPS. O Azure Event Grid permite aos clientes controlar o nível de acesso que é atribuído aos usuários diferentes para fazer várias operações de gestão, tais como as subscrições de eventos da lista, criar novos e gerar as chaves. Grelha de eventos utiliza o controlo de acesso baseado em função do Azure.

**As funções do Azure**: [As funções do Azure](https://docs.microsoft.com/azure/azure-functions/functions-overview) é um serviço de computação sem servidor que permite aos utilizadores executar código a pedido sem ter de aprovisionar ou gerir a infraestrutura explicitamente. Utilize as Funções do Azure para executar um script ou fragmento de código em resposta a uma variedade de eventos.

**O serviço do Azure Machine Learning**: [O Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/) é uma técnica de ciência de dados que permite aos computadores utilizar dados existentes para prever futuros comportamentos, resultados e tendências.

**Catálogo de dados do Azure**: [Catálogo de dados](https://docs.microsoft.com/azure/data-catalog/data-catalog-what-is-data-catalog) torna os dados facilmente Detetáveis e compreensíveis pelos utilizadores que gerem os dados. Origens de dados comuns podem ser registadas, marcadas e dados financeiros está à procura. Os dados permanecem na localização existente, mas uma cópia dos respetivos metadados é adicionada ao catálogo de dados, juntamente com uma referência para a localização de origem de dados. Os metadados também são indexados para tornar cada origem de dados facilmente detetável através da pesquisa e compreensível para os utilizadores que a detetarem.

### <a name="virtual-network"></a>Rede virtual

A arquitetura define uma rede privada virtual com um espaço de endereços de 10.200.0.0/16.

**Grupos de segurança de rede**: [Grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) contêm listas de controlo de acesso que permitem ou negam o tráfego dentro de uma rede virtual. Grupos de segurança de rede podem ser utilizados para proteger o tráfego numa sub-rede ou o nível VM individual. Existem os seguintes grupos de segurança de rede:

  - Um grupo de segurança de rede para o Active Directory
  - Um grupo de segurança de rede para a carga de trabalho

Cada um dos grupos de segurança de rede têm portas específicas e protocolos abrir para que a solução possam funcionar corretamente e de forma segura. Além disso, as seguintes configurações são habilitadas para cada grupo de segurança de rede:

- [Eventos e registos de diagnóstico](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) forem ativadas e armazenados numa conta de armazenamento
- Registos de Monitor do Azure está ligada a [grupo de segurança de rede&#39;registos de diagnóstico de s](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Sub-redes**: Cada sub-rede está associado a seu grupo de segurança de rede correspondente.

### <a name="data-in-transit"></a>Dados em trânsito

Azure encripta todas as comunicações de e para os datacenters do Azure por predefinição. A ocorrência de todas as transações para o armazenamento do Azure através do portal do Azure através de HTTPS.

### <a name="data-at-rest"></a>Dados inativos

A arquitetura protege os dados em descanso através da encriptação, a auditoria de base de dados e outras medidas.

**O armazenamento do Azure**: Para atender aos dados criptografados em requisitos de rest, todos os [armazenamento do Azure](https://azure.microsoft.com/services/storage/) utiliza [Storage Service Encryption](https://docs.microsoft.com/azure/storage/storage-service-encryption).  Isto ajuda a proteger e salvaguardar os dados do titular do cartão para oferecer suporte a compromissos de segurança organizacional e requisitos de conformidade definidos pela PCI DSS 3.2.

**Azure Disk Encryption**: [O Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) aproveita a funcionalidade BitLocker do Windows para fornecer encriptação de volume para discos de dados. A solução se integra com o Azure Key Vault para ajudar a controlar e gerir as chaves de encriptação de disco.

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

**Centro de segurança do Azure**: Com o [Centro de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro), os clientes podem centralmente aplicam-se e gerir políticas de segurança em cargas de trabalho, limitar a exposição a ameaças e detetar e responder a ataques. Além disso, o Centro de segurança do Azure acessa as configurações existentes dos serviços do Azure para fornecer a configuração e recomendações de serviço para o ajudar a melhorar a postura de segurança e proteger os dados.

Centro de segurança do Azure utiliza uma variedade de capacidades de deteção para alertar os clientes de possíveis ataques direcionados aos respetivos ambientes. Estes alertas contêm informações valiosas sobre o que acionou o alerta, os recursos afetados e a origem do ataque. Centro de segurança do Azure tem um conjunto de [predefinidos alertas de segurança](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), que são acionados quando uma ameaça ou atividade suspeita tem lugar. [Regras de alerta personalizadas](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) no Centro de segurança do Azure permitem aos clientes definir novos alertas de segurança com base nos dados que já são recolhidos do seu ambiente.

Centro de segurança do Azure fornece alertas de segurança priorizados e incidentes, tornando mais simples para os clientes detetar e resolver potenciais problemas de segurança. R [relatório de inteligência de ameaças](https://docs.microsoft.com/azure/security-center/security-center-threat-report) é gerado para cada detetado contra ameaças para ajudar as equipes de resposta a incidentes a investigar e resolver ameaças.

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

**O Application Insights**: [O Application Insights](https://docs.microsoft.com/azure/application-insights/) é um serviço de gestão de desempenho de aplicações (APM) extensível para desenvolvedores da web em várias plataformas. Ele Deteta anomalias de desempenho e inclui ferramentas de análise poderosas para ajudar a diagnosticar problemas e para compreender o que os utilizadores fazem realmente com a aplicação. Foi concebido para ajudar os usuários a melhorar continuamente o desempenho e a usabilidade.

## <a name="threat-model"></a>Modelo de risco

O diagrama de fluxo de dados para esta arquitetura de referência está disponível para [transferir](https://aka.ms/PCIAnalyticsTM) ou podem ser encontradas abaixo. Esse modelo pode ajudar os clientes a compreender os pontos do potencial risco de na infraestrutura de sistema ao fazer modificações.

![Análise do diagrama de arquitetura de referência de PCI DSS](images/pcidss-analytics-threat-model.png "Analytics para o modelo de risco de PCI DSS")

## <a name="compliance-documentation"></a>Documentação de conformidade
O [Azure no Security and Compliance Blueprint - matriz de responsabilidade do PCI DSS cliente](https://aka.ms/pcidss-analytics-tm) apresenta uma lista de responsabilidades para todos os requisitos de PCI DSS 3.2.

O [Azure no Security and Compliance Blueprint - matriz de implementação de análise de dados DSS PCI](https://aka.ms/pcidss-analytics-cim) fornece informações sobre quais 3.2 de DSS da PCI requisitos são resolvidos à arquitetura de análise de dados, incluindo descrições detalhadas de como a implementação cumpre os requisitos de cada controle coberta.


## <a name="guidance-and-recommendations"></a>Orientações e recomendações

### <a name="vpn-and-expressroute"></a>ExpressRoute e VPN

Um túnel VPN seguro ou [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) tem de ser configurado com segurança estabelecer uma ligação para os recursos implementados como parte desses dados análise de arquitetura de referência. Configurando adequadamente uma VPN ou ExpressRoute, os clientes podem adicionar uma camada de proteção para dados em trânsito.

Ao implementar um túnel VPN seguro com o Azure, é possível criar uma ligação privada virtual entre uma rede no local e uma rede Virtual do Azure. Essa conexão ocorre através da Internet e permite que os clientes com segurança as informações de "encapsulamento" dentro de uma ligação encriptada entre a rede do cliente e o Azure. VPN site a Site é uma tecnologia madura e segura que tenha sido implementada por empresas de todos os tamanhos há décadas. O [modo de encapsulamento IPsec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) é utilizada nesta opção como um mecanismo de criptografia.

Porque o tráfego de túnel VPN atravessa a Internet com uma VPN de site a site, a Microsoft oferece a opção de ligação de outra, ainda mais seguro. O Azure ExpressRoute é uma WAN dedicada ligação entre o Azure e uma localização no local ou um fornecedor de alojamento do Exchange. Como as ligações ExpressRoute não entram na Internet, estas ligações oferecem mais fiabilidade, velocidades mais rápidas, latências mais baixas e maior segurança do que as ligações normais pela Internet. Além disso, como se trata de uma conexão direta do fornecedor de telecomunicação do cliente, os dados não percorre a Internet e, portanto, não são expostos ao mesmo.

Melhores práticas para implementar uma rede híbrida segura que expande uma rede no local para o Azure são [disponível](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

### <a name="extract-transform-load-process"></a>Processo de extrair-transformar-carregar
[O PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) podem carregar dados na base de dados do Azure SQL sem a necessidade de um separado extrair, transformar, carregar ou importar ferramenta. O PolyBase permite o acesso aos dados através de consultas de T-SQL. Da Microsoft inteligência comercial e pilha de análise, bem como ferramentas de terceiros compatíveis com o SQL Server, podem ser utilizadas com o PolyBase.

### <a name="azure-active-directory-setup"></a>Configuração do Active Directory do Azure
[O Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) é essencial para a gestão da implementação e aprovisionamento do acesso ao pessoal de interação com o ambiente. Num Windows Server Active Directory podem ser integrada no Azure Active Directory no [quatro cliques](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express). Os clientes podem associar a infraestrutura de Active Directory implementada (controladores de domínio) a um existente do Azure Active Directory, tornando a infraestrutura de Active Directory implementada um subdomínio de uma floresta do Active Directory do Azure.

## <a name="disclaimer"></a>Exclusão de Responsabilidade

 - Este documento é apenas para fins informativos. A MICROSOFT FAZ NÃO OFERECE QUAISQUER GARANTIAS, EXPRESSAS, IMPLÍCITAS OU ESTATUTÁRIAS QUANTO ÀS INFORMAÇÕES CONTIDAS NESTE DOCUMENTO. Este documento é fornecido "como-está." Informações e opiniões expressados neste documento, incluindo URLs e outras referências de Web site da Internet, podem ser alteradas sem aviso prévio. Os clientes a ler este documento assume o risco de usá-lo.
 - Este documento não fornece aos clientes quaisquer direitos de propriedade intelectual em qualquer produto da Microsoft ou soluções.
 - Os clientes podem copiar e utilizar este documento para efeitos de referência interna.
 - Algumas recomendações neste documento podem resultar em maior de dados, a rede ou a utilização de recursos de computação no Azure e podem aumentar os custos de licenciamento ou de subscrição do Azure num cliente.
 - Esta arquitetura deve ser usado como uma base para os clientes que se ajustar às suas necessidades específicas e não devem ser utilizada como-está num ambiente de produção.
 - Este documento é desenvolvido como uma referência e não deve ser usado para definir todos os meios pelos quais um cliente pode atender aos requisitos de conformidade e normas. Os clientes procuram legal suporte da sua organização em implementações do cliente aprovadas.
