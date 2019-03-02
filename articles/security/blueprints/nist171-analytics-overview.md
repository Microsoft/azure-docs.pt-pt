---
title: Segurança do Azure e o esquema de conformidade - análise de dados para SP do NIST 800-171
description: Segurança do Azure e o esquema de conformidade - análise de dados para SP do NIST 800-171
services: security
author: jomolesk
ms.assetid: ca75d2a8-4e20-489b-9a9f-3a61e74b032d
ms.service: security
ms.topic: article
ms.date: 07/31/2018
ms.author: jomolesk
ms.openlocfilehash: f79ba9ae60454d4e73c914fc1c8af675a6d07d5d
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57245833"
---
# <a name="azure-security-and-compliance-blueprint---data-analytics-for-nist-sp-800-171"></a>Segurança do Azure e o esquema de conformidade - análise de dados para SP do NIST 800-171

## <a name="overview"></a>Descrição geral
[Publicação especial do NIST 800-171](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) fornece diretrizes para proteger a informação de não controlada (CUI) que residem nos sistemas de informação nonfederal e organizações. SP do NIST 800-171 estabelece 14 famílias de requisitos de segurança para proteger a confidencialidade das CUI.

Este Azure no Security and Compliance Blueprint disponibiliza orientações para ajudar os clientes a implementar uma arquitetura de análise de dados no Azure que implementa um subconjunto de NIST SP 800-171 controles. Esta solução demonstra maneiras em que os clientes podem satisfazer requisitos de conformidade e de segurança específicos. Ele também serve como uma base para os clientes criar e configurar suas próprias soluções de análise de dados no Azure.

Esta arquitetura de referência, o guia de implementação associados e o modelo de risco destinam-se a servir de base para os clientes para se adaptar às suas necessidades específicas. Não deve ser usados como-está num ambiente de produção. Implementar esta arquitetura sem modificação não é suficiente para atender completamente aos requisitos de SP do NIST 800-171. Os clientes são responsáveis pela realização de segurança adequados e avaliações de conformidade de qualquer solução criado que utiliza esta arquitetura. Requisitos podem variar com base nas especificidades da implementação de cada cliente.

## <a name="architecture-diagram-and-components"></a>Diagrama de arquitetura e componentes
Esta solução oferece uma plataforma de análise sobre a qual os clientes podem criar suas próprias ferramentas de análise. A arquitetura de referência descreve um caso de uso de genéricos. Os clientes podem utilizá-lo para os dados por meio de importações de dados em massa pelo administrador do SQL/dados de entrada. Eles também podem utilizá-lo para os dados por meio de atualizações de dados operacionais por meio de um utilizador operacional de entrada. Ambos os workstreams incorporar as funções do Azure para importar dados para a base de dados do Azure SQL. As funções do Azure tem de ser configuradas pelo cliente através do portal do Azure para lidar com as tarefas de importação exclusivas aos requisitos de análise do cliente.

O Azure oferece uma variedade de serviços de relatórios e análise do cliente. Esta solução utiliza os serviços do Azure Machine Learning e a base de dados SQL para navegar rapidamente por dados e fornecer resultados mais rápidos com modelagem mais inteligente de dados. Machine Learning destina-se para aumentar a velocidade de consulta ao detetar relações novas entre conjuntos de dados. Inicialmente, os dados são preparados através de várias funções estatísticas. Em seguida, até sete de consulta adicionais conjuntos podem ser sincronizados com os mesmos modelos em tabela para distribuir a carga de trabalho de consulta e reduzir os tempos de resposta. O servidor de cliente apresenta o total de conjuntos de consulta para oito.

Para análise avançada e relatórios, base de dados SQL pode ser configurado com os índices de arquivo de colunas. Machine Learning e a base de dados SQL podem ser aumentados vertical ou horizontalmente ou desativar completamente em resposta a utilização do cliente. Todo o tráfego SQL é encriptado com SSL através da inclusão de certificados autoassinados. Como melhor prática, recomendamos a utilização de uma autoridade de certificação fidedigna para uma maior segurança.

Depois de dados são carregados no banco de dados do SQL e treinados do Machine Learning, é digerida pelo utilizador operacional e o administrador de dados do SQL com o Power BI. O Power BI apresenta dados de forma intuitiva e reúne informações em vários conjuntos de dados para organizar melhor maior. O Power BI tem um alto grau de capacidade de adaptação e integra-se facilmente com a base de dados SQL. Os clientes podem configurá-lo para processar uma grande variedade de cenários que são necessários para suas necessidades de negócios.

A solução inteira é construída de armazenamento do Azure, o que os clientes configurar no portal do Azure. Armazenamento encripta todos os dados com o Storage Service Encryption para manter a confidencialidade dos dados em repouso. Armazenamento georredundante garante que um evento adverso no Centro de dados principal do cliente não resulta numa perda de dados. Uma segunda cópia é armazenada numa localização separada centenas de quilómetros de distância.

Para maior segurança, todos os recursos nesta solução são geridos como um grupo de recursos através do Gestor de recursos do Azure. O Azure Active Directory, controlo de acesso baseado em funções (Azure AD) (RBAC) é utilizado para controlar o acesso a recursos implementados. Esses recursos incluem as chaves de cliente no Azure Key Vault. Estado de funcionamento do sistema é monitorizado através do Centro de segurança do Azure e o Azure Monitor. Os clientes configurar ambos os serviços de monitorização para capturar os registos. Estado de funcionamento do sistema é exibido num único dashboard, é fácil de usar.

Base de dados SQL normalmente é gerido através do SQL Server Management Studio. Ele é executado a partir de uma máquina local configurada para aceder à base de dados SQL através de uma VPN segura ou uma ligação do ExpressRoute do Azure. *Recomendamos que configure uma ligação VPN ou ExpressRoute para importação de dados e gestão no grupo de recursos*.

![Análise de dados do diagrama de arquitetura de referência de SP do NIST 800-171](images/nist171-analytics-architecture.png "análise de dados do diagrama de arquitetura de referência de SP do NIST 800-171")

Esta solução utiliza os seguintes serviços do Azure. Para obter mais informações, consulte a [arquitetura de implantação](#deployment-architecture) secção.

- Application Insights
- Azure Active Directory
- Catálogo de Dados do Azure
- Azure Disk Encryption
- Azure Event Grid
- Funções do Azure
- Azure Key Vault
- Azure Machine Learning
- O Azure Monitor (logs)
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

**Azure Event Grid**: Com o [Event Grid](https://docs.microsoft.com/azure/event-grid/overview), os clientes podem criar facilmente aplicações com arquiteturas baseadas em eventos. Os utilizadores selecionam o recurso do Azure que pretende subscrever. Em seguida, dê o manipulador de eventos ou um ponto de extremidade para enviar o evento de webhook. Os clientes possam proteger pontos finais de webhook ao adicionar parâmetros de consulta para o URL do webhook quando criam uma subscrição de evento. Event Grid suporta apenas pontos finais HTTPS de webhook. Com o Event Grid, os clientes podem controlar o nível de acesso concedido a utilizadores diferentes de fazer várias operações de gestão. Os utilizadores podem listar subscrições de eventos, criar novos e gerar as chaves. Grelha de eventos utiliza o RBAC do Azure.

**As funções do Azure**: [As funções do Azure](https://docs.microsoft.com/azure/azure-functions/functions-overview) é um serviço de computação sem servidor que executa o código a pedido. Não tem de aprovisionar ou gerir a infraestrutura explicitamente. Utilize as Funções do Azure para executar um script ou fragmento de código em resposta a uma variedade de eventos.

**O serviço do Azure Machine Learning**: [Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/) é uma técnica de ciência de dados que permite aos computadores utilizar dados existentes para prever futuros comportamentos, resultados e tendências.

**Catálogo de dados do Azure**: [Catálogo de dados](https://docs.microsoft.com/azure/data-catalog/data-catalog-what-is-data-catalog) facilita a detetar e compreender pelos utilizadores que gerem os dados de origens de dados. Podem ser registadas, marcadas e está à procura dados de origens de dados comuns. Os dados permanecem na localização existente, mas uma cópia dos respetivos metadados é adicionada ao catálogo de dados. Uma referência para a localização de origem de dados está incluída. Os metadados são indexados para tornar cada origem de dados fácil de descobrir através da pesquisa. Indexação também torna compreensível para os utilizadores que a detetarem.

### <a name="virtual-network"></a>Rede virtual
Esta arquitetura de referência define uma rede privada virtual com um espaço de endereços de 10.0.0.0/16.

**Grupos de segurança de rede**: [Grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) (NSGs) contêm listas de controlo de acesso que permitem ou negam o tráfego dentro de uma rede virtual. Podem ser utilizados NSGs para proteger o tráfego numa sub-rede ou o nível de máquina virtual individual. Existem os NSGs seguintes:
  - Um NSG para o Active Directory
  - Um NSG para a carga de trabalho

Cada um dos NSGs tem portas específicas e protocolos abra para que a solução possam funcionar corretamente e de forma segura. Além disso, as seguintes configurações são habilitadas para cada NSG:
  - [Eventos e registos de diagnóstico](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) forem ativadas e armazenados numa conta de armazenamento
  - Registos de Monitor do Azure está ligada a [diagnósticos do NSG](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Sub-redes**: Cada sub-rede está associado a seu NSG correspondente.

### <a name="data-in-transit"></a>Dados em trânsito
Azure encripta todas as comunicações de e para os centros de dados do Azure por predefinição. A ocorrência de todas as transações para o armazenamento através do portal do Azure através de HTTPS.

### <a name="data-at-rest"></a>Dados inativos

A arquitetura protege os dados em descanso através da encriptação, a auditoria de base de dados e outras medidas.

**O armazenamento do Azure**: Para cumprir os requisitos para os dados encriptados em inatividade, todos os [armazenamento](https://azure.microsoft.com/services/storage/) utiliza [Storage Service Encryption](https://docs.microsoft.com/azure/storage/storage-service-encryption). Esta funcionalidade ajuda a proteger e salvaguardar os dados para oferecer suporte a requisitos de conformidade definidos pelo SP do NIST 800-171 e de compromissos de segurança organizacional.

**Azure Disk Encryption**: [Encriptação de disco](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) utiliza a funcionalidade BitLocker do Windows para fornecer encriptação de volume para discos de dados. A solução se integra com o Azure Key Vault para ajudar a controlar e gerir as chaves de encriptação de disco.

**Base de dados SQL do Azure**: A instância de base de dados SQL utiliza as seguintes medidas de segurança da base de dados:
-   [Autenticação do Active Directory e a autorização](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) permite a gestão de identidades de utilizadores de base de dados e outros serviços da Microsoft num local central.
-   [A auditoria de base de dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) faixas de base de dados eventos e escreve-os para uma auditoria registo numa conta de armazenamento do Azure.
-   Base de dados SQL está configurado para utilizar [encriptação de dados transparente](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql). Ele executa criptografia em tempo real e a descriptografia da base de dados, cópias de segurança associadas e ficheiros de registo de transação para proteger as informações em repouso. Encriptação de dados transparente fornece garantia de que são armazenados os dados não tem sido sujeita a acesso não autorizado.
-   [Regras de firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) impedir o acesso de todos os servidores de base de dados até que sejam concedidas as permissões apropriadas. A firewall concede acesso a bases de dados com base no endereço IP de origem de cada pedido.
-   [Deteção de ameaças SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) permite a deteção e resposta a ameaças potenciais à medida que ocorrem. Ele fornece alertas de segurança para atividades suspeitas da base de dados, potenciais vulnerabilidades, ataques de injeção de SQL e padrões de acesso de base de dados anómalas.
-   [Encriptados colunas](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) Certifique-se de que os dados confidenciais nunca é apresentada como texto simples dentro do sistema de base de dados. Depois de é ativada a encriptação de dados, apenas as aplicações de cliente ou servidores de aplicações com acesso às chaves podem aceder a dados de texto sem formatação.
- [Máscara de dados dinâmicos da base de dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) limita a exposição de dados confidenciais ao mascará os dados de utilizadores não privilegiados ou aplicações. É, automaticamente, pode detetar dados potencialmente confidenciais e sugerir as máscaras de apropriado a ser aplicado. Máscara de dados dinâmica ajuda a reduzir o acesso, de modo a que dados confidenciais não sair da base de dados por meio do acesso não autorizado. *Os clientes são responsáveis por ajustar as definições de cumprir para seu esquema de base de dados.*

### <a name="identity-management"></a>Gestão de identidades
As seguintes tecnologias fornecem capacidades para gerir o acesso aos dados no ambiente do Azure:
-   [O Azure AD](https://azure.microsoft.com/services/active-directory/) é o Microsoft multi-inquilino com base na cloud diretório e identidade do serviço de gestão. Todos os utilizadores para esta solução são criados no Azure AD e incluem os utilizadores que aceder à base de dados SQL.
-   Autenticação para a aplicação é executada ao utilizar o Azure AD. Para obter mais informações, consulte como [integrar aplicações com o Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). A encriptação de coluna de base de dados também utiliza o Azure AD para autenticar a aplicação para a base de dados SQL. Para obter mais informações, consulte como [proteger dados confidenciais na base de dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [RBAC do Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) podem ser utilizadas pelos administradores para definir permissões de acesso detalhado. Com ele, eles podem conceder apenas a quantidade de acesso que os utilizadores precisam realizar seus trabalhos. Em vez de dar todas as permissões de utilizador sem restrições para recursos do Azure, os administradores podem permitir apenas certas ações para aceder aos dados. Acesso à subscrição está limitado ao administrador da subscrição.
- [O Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) pode ser utilizado pelos clientes para minimizar o número de utilizadores que têm acesso a determinadas informações, tais como os dados. Os administradores podem utilizar o Azure AD Privileged Identity Management para detetar, restringir e monitorizar identidades privilegiadas e respetivo acesso a recursos. Esta funcionalidade também pode ser utilizada para impor acesso administrativo a pedido e just-in-time quando necessário.
-   [O Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) Deteta potenciais vulnerabilidades que afetam as identidades de uma organização. Configura as respostas automáticas para detetado ações suspeitas relacionadas com identidades de uma organização. Também é mostrada a incidentes suspeitos para tomar as medidas adequadas para resolvê-los.

### <a name="security"></a>Segurança
**Gestão de segredos**: A solução utiliza [do Azure Key Vault](https://azure.microsoft.com/services/key-vault/) para o gerenciamento de chaves e segredos. Key Vault ajuda a salvaguardar as chaves criptográficas e segredos utilizados pelas aplicações e serviços cloud. As seguintes capacidades de Key Vault ajudam os clientes a proteger os dados:
- Políticas de acesso avançadas são configuradas de forma necessidade.
- Políticas de acesso do Cofre de chaves são definidas com o mínimo permissões necessárias para chaves e segredos.
- Todas as chaves e segredos no Key Vault tem datas de expiração.
- Todas as chaves no Cofre de chaves estão protegidas por módulos de segurança de hardware especializado. O tipo de chave é uma chave RSA de 2048 bits segurança--protegidas por módulo de hardware.
- Todos os utilizadores e identidades são concedidas as permissões necessárias mínimas utilizando o RBAC.
- Registos de diagnóstico para o Cofre de chaves estão ativados com um período de retenção de, pelo menos, 365 dias.
- Operações de criptografia permitidas para as chaves estão limitadas aos obrigatórios.

**Centro de segurança do Azure**: Com o [Centro de segurança](https://docs.microsoft.com/azure/security-center/security-center-intro), os clientes podem centralmente aplicam-se e gerir políticas de segurança em cargas de trabalho, limitar a exposição a ameaças e detetar e responder a ataques. Centro de segurança também acessa as configurações existentes dos serviços do Azure para fornecer a configuração e recomendações de serviço para o ajudar a melhorar a postura de segurança e proteger os dados.

 Centro de segurança utiliza uma variedade de capacidades de deteção para alertar os clientes de potenciais ataques que visam seus ambientes. Estes alertas contêm informações valiosas sobre o que acionou o alerta, os recursos afetados e a origem do ataque. Centro de segurança tem um conjunto de [predefinidos alertas de segurança](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) que são acionados quando uma ameaça ou atividade suspeita ocorre. Os clientes podem usar [regras de alerta personalizadas](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) para definir novos alertas de segurança com base nos dados que já são recolhidos do seu ambiente.

 Centro de segurança fornece alertas de segurança priorizados e incidentes. Centro de segurança torna mais simples para os clientes detetar e resolver potenciais problemas de segurança. R [relatório de inteligência de ameaças](https://docs.microsoft.com/azure/security-center/security-center-threat-report) é gerado para cada detetadas ameaças. As equipes de resposta a incidentes podem utilizar os relatórios quando investigar e corrigir as ameaças.

### <a name="logging-and-auditing"></a>Registro e auditoria

Serviços do Azure extensivamente de registo do sistema e a atividade do utilizador, bem como o estado de funcionamento do sistema:
- **Registos de atividades**: [Registos de atividades](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) fornecem informações sobre as operações executadas em recursos numa subscrição. Registos de atividades podem ajudar a determinar o iniciador de uma operação, hora da ocorrência e o estado.
- **Os registos de diagnóstico**: [Os registos de diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) incluem todos os registos emitidos por cada recurso. Estes registos incluem registos de sistema de eventos do Windows, registos de armazenamento, registos de auditoria do Cofre de chaves e os registos de acesso e de firewall do Gateway de aplicação do Azure. Todos os registos de diagnóstico escrevem para uma conta de armazenamento do Azure centralizado e criptografado para arquivamento. Os utilizadores podem configurar o período de retenção, até e 730 dias, para satisfazer os seus requisitos específicos.

**Registos de Monitor do Azure**: Os registos são consolidados no [registos do Azure Monitor](https://azure.microsoft.com/services/log-analytics/) para processamento, armazenamento e relatórios do dashboard. Depois de recolhidos, os dados são organizado em tabelas separadas para cada tipo de dados nas áreas de trabalho do Log Analytics. Dessa forma, todos os dados podem ser analisados em conjunto, independentemente de sua fonte original. Centro de segurança integra-se com os registos do Azure Monitor. Os clientes podem utilizar consultas de Kusto para aceder aos respetivos dados de eventos de segurança e combiná-los com dados de outros serviços.

O Azure seguinte [soluções de monitorização](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) são incluídos como parte desta arquitetura:
-   [Avaliação do Active Directory](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): A solução de verificação de estado de funcionamento do Active Directory avalia o risco e estado de funcionamento dos ambientes de servidor num intervalo regular. Ele fornece uma lista prioritária de recomendações específicas para a infraestrutura de servidor implementado.
- [Avaliação do SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): A solução de verificação de estado de funcionamento do SQL avalia o risco e estado de funcionamento dos ambientes de servidor num intervalo regular. Fornece aos clientes com uma lista prioritária de recomendações específicas para a infraestrutura de servidor implementado.
- [Estado de funcionamento do agente](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): A solução de estado de funcionamento do agente relata o número de agentes é implementado e a respetiva distribuição geográfica. Também informa sobre quantos agentes estão sem resposta e o número de agentes que enviam dados operacionais.
-   [Log Analytics da atividade](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): A solução Log Analytics da atividade auxilia com uma análise de registos de atividade do Azure em todas as subscrições do Azure para um cliente.

**A automatização do Azure**: [Automatização](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) armazena, executa e gere runbooks. Nesta solução, runbooks ajudam a recolher registos de base de dados SQL. Os clientes podem usar a automação [controlo de alterações](https://docs.microsoft.com/azure/automation/automation-change-tracking) solução para o identificar facilmente as alterações no ambiente.

**Azure Monitor**: [Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) ajuda os usuários a acompanhar o desempenho, manter a segurança e identificar tendências. As organizações podem usá-lo de auditoria, criar alertas e arquivar dados. Também pode controlar as chamadas de API em seus recursos do Azure.

**O Application Insights**: [O Application Insights](https://docs.microsoft.com/azure/application-insights/) é um serviço de gestão de desempenho de aplicações extensível para desenvolvedores da web em várias plataformas. Ele Deteta anomalias de desempenho e inclui ferramentas de análise poderosas. As ferramentas de ajudar a diagnosticar problemas e ajudar os clientes a compreender o que os utilizadores fazem com a aplicação. Foi concebido para ajudar os usuários a melhorar continuamente o desempenho e a usabilidade.

## <a name="threat-model"></a>Modelo de risco

O diagrama de fluxo de dados para esta arquitetura de referência está disponível para [transferir](https://aka.ms/nist171-analytics-tm) ou podem ser encontradas aqui. Esse modelo pode ajudar os clientes a compreender os pontos de risco potencial na infra-estrutura do sistema quando eles fazer modificações.

![Análise de dados do modelo de risco de SP do NIST 800-171](images/nist171-analytics-threat-model.png "análise de dados do modelo de risco de SP do NIST 800-171")

## <a name="compliance-documentation"></a>Documentação de conformidade
O [Azure no Security and Compliance Blueprint - matriz de responsabilidade do cliente de SP do NIST 800-171](https://aka.ms/nist171-crm) apresenta uma lista de todos os controlos de segurança exigidos pelo SP do NIST 800-171. Essa matriz se a implementação de cada controle é da responsabilidade da Microsoft, o cliente, os detalhes ou partilhados entre os dois.

O [Azure no Security and Compliance Blueprint - matriz de implementação de controlo de análise de dados de SP do NIST 800-171](https://aka.ms/nist171-analytics-cim) fornece informações sobre quais SP NIST 800-171 controles são resolvidas à arquitetura de análise de dados. Ele inclui descrições detalhadas de como a implementação cumpre os requisitos de cada controle coberta.


## <a name="guidance-and-recommendations"></a>Orientações e recomendações
### <a name="vpn-and-expressroute"></a>ExpressRoute e VPN
Um túnel VPN seguro ou [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) tem de ser configurado com segurança estabelecer uma ligação para os recursos implementados como parte desta arquitetura de referência de análise de dados. Configurando adequadamente uma VPN ou ExpressRoute, os clientes podem adicionar uma camada de proteção para dados em trânsito.

Ao implementar um túnel VPN seguro com o Azure, é possível criar uma ligação privada virtual entre uma rede no local e uma rede virtual do Azure. Esta ligação ocorre através da Internet. Os clientes podem utilizar a ligação para informações de "encapsulamento" em segurança dentro de uma ligação encriptada entre a sua rede e o Azure. VPN site a site é uma tecnologia madura e segura que tenha sido implementada por empresas de todos os tamanhos há décadas. O [modo de encapsulamento IPsec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) é utilizada nesta opção como um mecanismo de criptografia.

Uma vez que o tráfego no túnel VPN atravessa a Internet com uma VPN de site a site, a Microsoft oferece outra opção de ligação ainda mais segura. O ExpressRoute é uma WAN dedicada ligação entre o Azure e uma localização no local ou um fornecedor de alojamento do Exchange. As ligações ExpressRoute ligar-se diretamente ao fornecedor de telecomunicação de um cliente. Como resultado, os dados não viajam através da Internet e não estão expostos à mesma. Estas ligações oferecem mais fiabilidade, velocidades mais rápidas, latências mais baixas e maior segurança do que as ligações normais. 

Melhores práticas para implementar uma rede híbrida segura que expande uma rede no local para o Azure são [disponível](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

### <a name="extract-transform-load-process"></a>Processo de extrair-transformar-carregar
[O PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) pode carregar dados na base de dados SQL sem a necessidade de um separado extrair-transformar-carregar ou importar a ferramenta. O PolyBase permite o acesso aos dados através de consultas de T-SQL. A pilha de inteligência e análise de negócios da Microsoft e ferramentas de terceiros que são compatíveis com o SQL Server podem ser utilizadas com o PolyBase.

### <a name="azure-ad-setup"></a>Configuração do Azure AD
[O Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) é essencial para a gestão da implementação e aprovisionamento do acesso a pessoas que interagem com o ambiente. No local do Active Directory pode ser integrado com o Azure AD na [quatro cliques](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express). Os clientes também podem vincular a infra-estrutura do Active Directory implementada (controladores de domínio) para o Azure AD. Para fazer isso, verifique a infra-estrutura do Active Directory implementada um subdomínio de uma floresta de AD do Azure.

## <a name="disclaimer"></a>Exclusão de Responsabilidade

 - Este documento é apenas para fins informativos. A MICROSOFT FAZ NÃO OFERECE QUAISQUER GARANTIAS, EXPRESSAS, IMPLÍCITAS OU ESTATUTÁRIAS QUANTO ÀS INFORMAÇÕES CONTIDAS NESTE DOCUMENTO. Este documento é fornecido "como-está." Informações e opiniões expressados neste documento, incluindo URLs e outras referências de Web site da Internet, podem ser alteradas sem aviso prévio. Os clientes a ler este documento assume o risco de usá-lo.
 - Este documento não fornece aos clientes quaisquer direitos de propriedade intelectual em qualquer produto da Microsoft ou soluções.
 - Os clientes podem copiar e utilizar este documento para efeitos de referência interna.
 - Algumas recomendações neste documento podem resultar em maior de dados, a rede ou a utilização de recursos de computação no Azure e podem aumentar os custos de licenciamento ou de subscrição do Azure num cliente.
 - Esta arquitetura deve ser usado como uma base para os clientes que se ajustar às suas necessidades específicas e não devem ser utilizada como-está num ambiente de produção.
 - Este documento é desenvolvido como uma referência e não deve ser usado para definir todos os meios pelos quais um cliente pode atender aos requisitos de conformidade e normas. Os clientes procuram legal suporte da sua organização em implementações do cliente aprovadas.
