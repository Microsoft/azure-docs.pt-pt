---
title: Análise de dados Blueprint de Segurança e Conformidade do Azure para NIST SP 800-171
description: Análise de dados Blueprint de Segurança e Conformidade do Azure para NIST SP 800-171
services: security
author: jomolesk
ms.assetid: ca75d2a8-4e20-489b-9a9f-3a61e74b032d
ms.service: security
ms.topic: article
ms.date: 07/31/2018
ms.author: jomolesk
ms.openlocfilehash: 2bd2510b3b7aa72ac5e66ac9910f1c941f276564
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71259905"
---
# <a name="azure-security-and-compliance-blueprint---data-analytics-for-nist-sp-800-171"></a>Análise de dados Blueprint de Segurança e Conformidade do Azure para NIST SP 800-171

## <a name="overview"></a>Descrição geral
A [publicação especial do NIST 800-171](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) fornece diretrizes para proteger as informações não classificadas (CUI) controladas que residem em sistemas de informações e organizações não federais. O NIST SP 800-171 estabelece 14 famílias de requisitos de segurança para proteger a confidencialidade da CUI.

Este Blueprint de Segurança e Conformidade do Azure fornece diretrizes para ajudar os clientes a implantar uma arquitetura de análise de dados no Azure que implementa um subconjunto de controles NIST SP 800-171. Esta solução demonstra maneiras em que os clientes podem atender aos requisitos específicos de segurança e conformidade. Ele também serve como base para os clientes criarem e configurarem suas próprias soluções de análise de dados no Azure.

Essa arquitetura de referência, o guia de implementação associado e o modelo de ameaça devem servir como base para os clientes se adaptarem aos seus requisitos específicos. Eles não devem ser usados no estado em que se encontram em um ambiente de produção. Implantar essa arquitetura sem modificação é insuficiente para atender totalmente aos requisitos do NIST SP 800-171. Os clientes são responsáveis por realizar avaliações apropriadas de segurança e conformidade de qualquer solução criada que use essa arquitetura. Os requisitos podem variar com base nas especificidades da implementação de cada cliente.

## <a name="architecture-diagram-and-components"></a>Diagrama e componentes da arquitetura
Essa solução fornece uma plataforma de análise na qual os clientes podem criar suas próprias ferramentas de análise. A arquitetura de referência descreve um caso de uso genérico. Os clientes podem usá-lo para inserir dados por meio de importações de dados em massa pelo administrador do SQL/Data. Eles também podem usá-lo para inserir dados por meio de atualizações de dados operacionais por meio de um usuário operacional. Ambos os workstreams incorporam Azure Functions para importar dados para o Azure SQL Database. Azure Functions deve ser configurado pelo cliente por meio do portal do Azure para lidar com as tarefas de importação exclusivas dos requisitos de análise do cliente.

O Azure oferece uma variedade de relatórios e serviços de análise para o cliente. Essa solução usa os serviços de Azure Machine Learning e o banco de dados SQL para navegar rapidamente pelos dados e fornecer resultados mais rápidos por meio da modelagem mais inteligente de dados. A Machine Learning destina-se a aumentar as velocidades de consulta, descobrindo novas relações entre conjuntos de os. Inicialmente, os dados são treinados por meio de várias funções estatísticas. Depois disso, até sete pools de consulta adicionais podem ser sincronizados com os mesmos modelos de tabela para espalhar a carga de trabalho de consulta e reduzir os tempos de resposta. O servidor do cliente leva o total de pools de consulta a oito.

Para análise e relatórios aprimorados, o banco de dados SQL pode ser configurado com índices de repositório de coluna. Machine Learning e o banco de dados SQL podem ser escalados ou reduzidos verticalmente ou desligados completamente em resposta ao uso do cliente. Todo o tráfego do SQL é criptografado com SSL por meio da inclusão de certificados autoassinados. Como prática recomendada, recomendamos o uso de uma autoridade de certificação confiável para aumentar a segurança.

Depois que os dados são carregados no banco de dado SQL e treinados pelo Machine Learning, ele é resumido pelo usuário operacional e pelo administrador do SQL/data com Power BI. Power BI exibe os dados de maneira intuitiva e reúne informações em vários conjuntos de dados para extrair mais insights. Power BI tem um alto grau de adaptabilidade e integra-se facilmente com o banco de dados SQL. Os clientes podem configurá-lo para lidar com uma ampla gama de cenários que são necessários para suas necessidades comerciais.

A solução inteira é criada com base no armazenamento do Azure, que os clientes configuram do portal do Azure. O armazenamento criptografa todos os dados com Criptografia do Serviço de Armazenamento para manter a confidencialidade dos dados em repouso. O armazenamento com redundância geográfica garante que um evento adverso na data center principal do cliente não resulte em perda de dados. Uma segunda cópia é armazenada em um local separado a centenas de quilômetros de distância.

Para aumentar a segurança, todos os recursos nesta solução são gerenciados como um grupo de recursos por meio de Azure Resource Manager. O RBAC (controle de acesso baseado em função) do Azure Active Directory (Azure AD) é usado para controlar o acesso aos recursos implantados. Esses recursos incluem as chaves do cliente no Azure Key Vault. A integridade do sistema é monitorada por meio da central de segurança do Azure e Azure Monitor. Os clientes configuram ambos os serviços de monitoramento para capturar logs. A integridade do sistema é exibida em um único painel que é fácil de usar.

O banco de dados SQL é normalmente gerenciado por meio de SQL Server Management Studio. Ele é executado de um computador local configurado para acessar o banco de dados SQL por meio de uma VPN segura ou uma conexão do Azure ExpressRoute. *Recomendamos que você configure uma conexão VPN ou ExpressRoute para gerenciamento e importação de dados para o grupo de recursos*.

![Diagrama de arquitetura de referência de análise de dados para NIST SP 800-171](images/nist171-analytics-architecture.png "Diagrama de arquitetura de referência de análise de dados para NIST SP 800-171")

Essa solução usa os seguintes serviços do Azure. Para obter mais informações, consulte a seção [arquitetura de implantação](#deployment-architecture) .

- Application Insights
- Azure Active Directory
- Catálogo de Dados do Azure
- Azure Disk Encryption
- Azure Event Grid
- Funções do Azure
- Azure Key Vault
- Azure Machine Learning
- Azure Monitor (logs)
- Centro de Segurança do Azure
- Base de Dados SQL do Azure
- Storage do Azure
- Rede Virtual do Azure
    - (1)/16 rede
    - (2)/24 redes
    - (2) grupos de segurança de rede
- Dashboard do Power BI

## <a name="deployment-architecture"></a>Arquitetura de implantação
A seção a seguir detalha os elementos de implantação e implementação.

**Grade de eventos do Azure**: Com a [grade de eventos](https://docs.microsoft.com/azure/event-grid/overview), os clientes podem facilmente criar aplicativos com arquiteturas baseadas em eventos. Os usuários selecionam o recurso do Azure que deseja assinar. Em seguida, eles fornecem ao manipulador de eventos ou webhook um ponto de extremidade para o qual enviar o evento. Os clientes podem proteger pontos de extremidade de webhook adicionando parâmetros de consulta à URL do webhook ao criarem uma assinatura de evento. A grade de eventos dá suporte apenas a pontos de extremidade de webhook HTTPS. Com a grade de eventos, os clientes podem controlar o nível de acesso fornecido a diferentes usuários para realizar várias operações de gerenciamento. Os usuários podem listar as assinaturas de evento, criar novas e gerar chaves. A grade de eventos usa o RBAC do Azure.

**Azure Functions**: [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) é um serviço de computação sem servidor que executa o código sob demanda. Você não precisa provisionar ou gerenciar explicitamente a infraestrutura. Utilize as Funções do Azure para executar um script ou fragmento de código em resposta a uma variedade de eventos.

**Serviço de Azure Machine Learning**: [Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/) é uma técnica de ciência de dados que permite que os computadores usem os dados existentes para prever comportamentos, resultados e tendências futuros.

**Catálogo de dados do Azure**: O [Catálogo de dados](../../data-catalog/overview.md) facilita a descoberta e a compreensão das fontes de dados pelos usuários que gerenciam os dados. As fontes de dados comuns podem ser registradas, marcadas e pesquisadas em busca de dados. Os dados permanecem em seu local existente, mas uma cópia de seus metadados é adicionada ao catálogo de dados. Uma referência ao local da fonte de dados está incluída. Os metadados são indexados para facilitar a descoberta de cada fonte de dados por meio de pesquisa. A indexação também o torna compreensível para os usuários que o descobrirem.

### <a name="virtual-network"></a>Rede virtual
Essa arquitetura de referência define uma rede virtual privada com um espaço de endereço de 10.0.0.0/16.

**Grupos de segurança de rede**: [Grupos de segurança de rede](../../virtual-network/virtual-network-vnet-plan-design-arm.md) (NSGs) contém listas de controle de acesso que permitem ou negam o tráfego em uma rede virtual. NSGs pode ser usado para proteger o tráfego em uma sub-rede ou em um nível de máquina virtual individual. Os seguintes NSGs existem:
  - Um NSG para Active Directory
  - Um NSG para a carga de trabalho

Cada um dos NSGs tem portas e protocolos específicos abertos para que a solução possa funcionar de forma segura e correta. Além disso, as seguintes configurações estão habilitadas para cada NSG:
  - [Os logs e eventos de diagnóstico](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) são habilitados e armazenados em uma conta de armazenamento
  - Os logs de Azure Monitor estão conectados ao [diagnóstico do NSG](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Sub-redes**: Cada sub-rede é associada ao seu NSG correspondente.

### <a name="data-in-transit"></a>Dados em trânsito
O Azure criptografa todas as comunicações de e para data centers do Azure por padrão. Todas as transações para armazenamento por meio do portal do Azure ocorrem via HTTPS.

### <a name="data-at-rest"></a>Dados inativos

A arquitetura protege os dados em repouso por meio de criptografia, auditoria de banco e outras medidas.

**Armazenamento do Azure**: Para atender aos requisitos de dados criptografados em repouso, todo o [armazenamento](https://azure.microsoft.com/services/storage/) usa [criptografia do serviço de armazenamento](../../storage/common/storage-service-encryption.md). Esse recurso ajuda a proteger e proteger os dados para dar suporte a compromissos de segurança organizacional e requisitos de conformidade definidos pelo NIST SP 800-171.

**Azure Disk Encryption**: A [criptografia de disco](../azure-security-disk-encryption-overview.md) usa o recurso BitLocker do Windows para fornecer criptografia de volume para discos de dados. A solução se integra com Azure Key Vault para ajudar a controlar e gerenciar as chaves de criptografia de disco.

**Banco de dados SQL do Azure**: A instância do banco de dados SQL usa as seguintes medidas de segurança de banco de dados:
-   [Active Directory autenticação e autorização](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) permitem o gerenciamento de identidades de usuários de banco de dados e outros serviços da Microsoft em um local central.
-   A [auditoria do banco de dados SQL](../../sql-database/sql-database-auditing.md) rastreia eventos de banco de dados e os grava em um log de auditoria em uma conta de armazenamento do Azure.
-   O banco de dados SQL está configurado para usar a [Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql). Ele executa criptografia e descriptografia em tempo real do banco de dados, backups associados e arquivos de log de transações para proteger informações em repouso. A Transparent Data Encryption fornece garantia de que os dados armazenados não estão sujeitos a acesso não autorizado.
-   [As regras de firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) impedem todo o acesso a servidores de banco de dados até que sejam concedidas permissões adequadas A firewall concede acesso a bases de dados com base no endereço IP de origem de cada pedido.
-   A [detecção de ameaças do SQL](../../sql-database/sql-database-threat-detection.md) permite a detecção e a resposta a ameaças potenciais à medida que elas ocorrem. Ele fornece alertas de segurança para atividades suspeitas de banco de dados, possíveis vulnerabilidades, ataques de injeção de SQL e padrões de acesso de banco de dados anormais.
-   As [colunas criptografadas](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) garantem que os dados confidenciais nunca apareçam como texto sem formatação no sistema de banco de dados. Depois que a criptografia de dados é habilitada, somente aplicativos cliente ou servidores de aplicativos com acesso às chaves podem acessar dados de texto sem formatação.
- O [mascaramento de dados dinâmicos do Database do SQL](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) limita a exposição de dados confidenciais mascarando os dados para usuários ou aplicativos sem privilégios. Ele pode descobrir dados potencialmente confidenciais automaticamente e sugerir as máscaras apropriadas a serem aplicadas. O mascaramento de dados dinâmicos ajuda a reduzir o acesso para que os dados confidenciais não saiam do banco por meio de acesso não autorizado. *Os clientes são responsáveis por ajustar as configurações para aderir ao seu esquema de banco de dados.*

### <a name="identity-management"></a>Gestão de identidades
As tecnologias a seguir fornecem recursos para gerenciar o acesso aos dados no ambiente do Azure:
-   O [Azure ad](https://azure.microsoft.com/services/active-directory/) é o serviço de gerenciamento de identidade e diretório baseado em nuvem multilocatário da Microsoft. Todos os usuários dessa solução são criados no Azure AD e incluem os usuários que acessam o banco de dados SQL.
-   A autenticação para o aplicativo é executada usando o Azure AD. Para obter mais informações, consulte como [integrar aplicativos com o Azure ad](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). A criptografia de coluna de banco de dados também usa o Azure AD para autenticar o aplicativo no banco de dados SQL. Para obter mais informações, Confira como [proteger dados confidenciais no SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   O [RBAC do Azure](../../role-based-access-control/role-assignments-portal.md) pode ser usado por administradores para definir permissões de acesso refinadas. Com ele, eles podem conceder apenas a quantidade de acesso que os usuários precisam para executar seus trabalhos. Em vez de fornecer a cada usuário permissões irrestritas para recursos do Azure, os administradores podem permitir apenas determinadas ações para acessar dados. O acesso à assinatura é limitado ao administrador da assinatura.
- [Azure Active Directory Privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md) pode ser usado por clientes para minimizar o número de usuários que têm acesso a determinadas informações, como dados. Os administradores podem usar Azure AD Privileged Identity Management para descobrir, restringir e monitorar identidades com privilégios e seu acesso aos recursos. Essa funcionalidade também pode ser usada para impor acesso administrativo sob demanda e Just-in-time quando necessário.
-   [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview.md) detecta possíveis vulnerabilidades que afetam as identidades de uma organização. Ele configura as respostas automatizadas para detectar ações suspeitas relacionadas às identidades de uma organização. Ele também investiga incidentes suspeitos para tomar as medidas apropriadas para resolvê-los.

### <a name="security"></a>Segurança
**Gerenciamento de segredos**: A solução usa [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) para o gerenciamento de chaves e segredos. Key Vault ajuda a proteger chaves criptográficas e segredos usados por aplicativos e serviços em nuvem. Os recursos de Key Vault a seguir ajudam os clientes a proteger os dados:
- As políticas de acesso avançadas são configuradas de acordo com a necessidade.
- Key Vault políticas de acesso são definidas com as permissões mínimas necessárias para chaves e segredos.
- Todas as chaves e segredos em Key Vault têm datas de expiração.
- Todas as chaves em Key Vault são protegidas por módulos de segurança de hardware especializados. O tipo de chave é uma chave RSA de segurança de hardware-módulo protegido de 2048 bits.
- Todos os usuários e identidades recebem as permissões mínimas necessárias usando o RBAC.
- Os logs de diagnóstico para Key Vault estão habilitados com um período de retenção de pelo menos 365 dias.
- As operações de criptografia permitidas para chaves são restritas às necessárias.

**Central de segurança do Azure**: Com a [central de segurança](https://docs.microsoft.com/azure/security-center/security-center-intro), os clientes podem aplicar e gerenciar centralmente políticas de segurança entre cargas de trabalho, limitar a exposição a ameaças e detectar e responder a ataques. A central de segurança também acessa as configurações existentes dos serviços do Azure para fornecer recomendações de configuração e serviço para ajudar a melhorar a postura de segurança e proteger os dados.

 A central de segurança usa uma variedade de recursos de detecção para alertar os clientes sobre possíveis ataques direcionados a seus ambientes. Estes alertas contêm informações valiosas sobre o que acionou o alerta, os recursos afetados e a origem do ataque. A central de segurança tem um conjunto de [alertas de segurança predefinidos](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) que são disparados quando ocorre uma ameaça ou atividade suspeita. Os clientes podem usar [regras de alerta personalizadas](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) para definir novos alertas de segurança com base nos dados que já foram coletados de seu ambiente.

 A central de segurança fornece incidentes e alertas de segurança priorizados. A central de segurança torna mais simples para os clientes descobrir e resolver problemas potenciais de segurança. Um [relatório de inteligência contra ameaças](https://docs.microsoft.com/azure/security-center/security-center-threat-report) é gerado para cada ameaça detectada. As equipes de resposta a incidentes podem usar os relatórios ao investigar e corrigir ameaças.

### <a name="logging-and-auditing"></a>Registo e auditoria

Os serviços do Azure registram extensivamente a atividade do sistema e do usuário, bem como a integridade do sistema:
- **Logs de atividade**: [Os logs de atividade](../../azure-monitor/platform/activity-logs-overview.md) fornecem informações sobre as operações executadas nos recursos em uma assinatura. Os logs de atividades podem ajudar a determinar o iniciador de uma operação, a hora de ocorrência e o status.
- **Logs de diagnóstico**: Os [logs de diagnóstico](../../azure-monitor/platform/resource-logs-overview.md) incluem todos os logs emitidos por cada recurso. Esses logs incluem logs do sistema de eventos do Windows, logs de armazenamento, Key Vault logs de auditoria e Aplicativo Azure acesso ao gateway e logs de firewall. Todos os logs de diagnóstico gravam em uma conta de armazenamento do Azure centralizada e criptografada para arquivamento. Os usuários podem configurar o período de retenção, até 730 dias, para atender a seus requisitos específicos.

**Logs de Azure monitor**: Os logs são consolidados nos [logs de Azure monitor](https://azure.microsoft.com/services/log-analytics/) para processamento, armazenamento e relatórios de painel. Depois que os dados são coletados, eles são organizados em tabelas separadas para cada tipo de dados dentro dos espaços de trabalho Log Analytics. Dessa forma, todos os dados podem ser analisados juntos, independentemente de sua fonte original. A central de segurança se integra aos logs de Azure Monitor. Os clientes podem usar consultas Kusto para acessar seus dados de eventos de segurança e combiná-los com dados de outros serviços.

As seguintes [soluções de monitoramento](../../monitoring/monitoring-solutions.md) do Azure estão incluídas como parte dessa arquitetura:
-   [Avaliação de Active Directory](../../azure-monitor/insights/ad-assessment.md): A solução de verificação de integridade Active Directory avalia o risco e a integridade de ambientes de servidor em intervalos regulares. Ele fornece uma lista priorizada de recomendações específicas para a infraestrutura de servidor implantada.
- [Avaliação do SQL](../../azure-monitor/insights/sql-assessment.md): A solução de verificação da integridade do SQL avalia o risco e a integridade de ambientes de servidor em intervalos regulares. Ele fornece aos clientes uma lista priorizada de recomendações específicas para a infraestrutura de servidor implantada.
- [Integridade do agente](../../monitoring/monitoring-solution-agenthealth.md): A solução Integridade do Agente relata Quantos agentes são implantados e sua distribuição geográfica. Ele também relata Quantos agentes não respondem e o número de agentes que enviam dados operacionais.
-   [Análise do log de atividades](../../azure-monitor/platform/collect-activity-logs.md): A solução Análise do Log de Atividades auxilia na análise dos logs de atividade do Azure em todas as assinaturas do Azure para um cliente.

**Automação do Azure**: A [automação](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) armazena, executa e gerencia runbooks. Nesta solução, os runbooks ajudam a coletar logs do banco de dados SQL. Os clientes podem usar a solução de [controle de alterações](../../automation/change-tracking.md) de automação para identificar facilmente as alterações no ambiente.

**Azure monitor**: O [Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) ajuda os usuários a controlar o desempenho, manter a segurança e identificar tendências. As organizações podem usá-lo para auditar, criar alertas e arquivar dados. Eles também podem acompanhar chamadas de API em seus recursos do Azure.

**Application insights**: O [Application insights](https://docs.microsoft.com/azure/application-insights/) é um serviço de gerenciamento de desempenho de aplicativos extensível para desenvolvedores da Web em várias plataformas. Ele detecta anomalias de desempenho e inclui poderosas ferramentas de análise. As ferramentas ajudam a diagnosticar problemas e ajudam os clientes a entender o que os usuários fazem com o aplicativo. Ele foi projetado para ajudar os usuários a melhorar continuamente o desempenho e a usabilidade.

## <a name="threat-model"></a>Modelo de ameaça

O diagrama de fluxo de dados para esta arquitetura de referência está disponível para [Download](https://aka.ms/nist171-analytics-tm) ou pode ser encontrado aqui. Esse modelo pode ajudar os clientes a entender os pontos de risco potencial na infraestrutura do sistema quando fazem modificações.

![Análise de dados para o modelo de ameaça NIST SP 800-171](images/nist171-analytics-threat-model.png "Análise de dados para o modelo de ameaça NIST SP 800-171")

## <a name="compliance-documentation"></a>Documentação de conformidade
A [matriz de responsabilidade do cliente do Blueprint de segurança E conformidade do Azure NIST SP 800-171](https://aka.ms/nist171-crm) lista todos os controles de segurança exigidos pelo NIST SP 800-171. Essa matriz detalha se a implementação de cada controle é de responsabilidade da Microsoft, do cliente ou do compartilhamento entre os dois.

A [matriz de implementação de controle de análise de dados do Blueprint de segurança E conformidade do Azure NIST sp 800-171](https://aka.ms/nist171-analytics-cim) fornece informações sobre quais controles do nist SP 800-171 são tratados pela arquitetura de análise de dados. Ele inclui descrições detalhadas de como a implementação atende aos requisitos de cada controle abordado.


## <a name="guidance-and-recommendations"></a>Diretrizes e recomendações
### <a name="vpn-and-expressroute"></a>VPN e ExpressRoute
Um túnel VPN seguro ou o [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) deve ser configurado para estabelecer com segurança uma conexão com os recursos implantados como parte dessa arquitetura de referência da análise de dados. Ao configurar adequadamente uma VPN ou ExpressRoute, os clientes podem adicionar uma camada de proteção para os dados em trânsito.

Ao implementar um túnel VPN seguro com o Azure, uma conexão privada virtual entre uma rede local e uma rede virtual do Azure pode ser criada. Esta conexão ocorre pela Internet. Os clientes podem usar a conexão para "encapsular" informações com segurança dentro de um link criptografado entre sua rede e o Azure. A VPN site a site é uma tecnologia segura e madura, que foi implantada por empresas de todos os tamanhos por décadas. O [modo de encapsulamento IPSec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) é usado nessa opção como um mecanismo de criptografia.

Como o tráfego no túnel VPN atravessa a Internet com uma VPN site a site, a Microsoft oferece outra opção de conexão ainda mais segura. O ExpressRoute é um link WAN dedicado entre o Azure e um local ou um provedor de hospedagem do Exchange. As conexões do ExpressRoute se conectam diretamente ao provedor de telecomunicação do cliente. Como resultado, os dados não viajam pela Internet e não são expostos a ele. Essas conexões oferecem mais confiabilidade, velocidades mais rápidas, latências menores e maior segurança do que as conexões típicas. 

As práticas recomendadas para implementar uma rede híbrida segura que estende uma rede local para o Azure estão [disponíveis](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

### <a name="extract-transform-load-process"></a>Processo de extração-transformação-carregamento
O [polybase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) pode carregar os dados no SQL Database sem a necessidade de uma ferramenta separada de extração/transformação/carregamento ou importação. O polybase permite o acesso a dados por meio de consultas T-SQL. O Microsoft business intelligence e a pilha de análise e ferramentas de terceiros que são compatíveis com SQL Server podem ser usadas com o polybase.

### <a name="azure-ad-setup"></a>Instalação do Azure AD
O [Azure ad](../../active-directory/fundamentals/active-directory-whatis.md) é essencial para gerenciar a implantação e o provisionamento de acesso ao pessoal que interagem com o ambiente. Os Active Directory locais podem ser integrados ao Azure AD em [quatro cliques](../../active-directory/hybrid/how-to-connect-install-express.md). Os clientes também podem vincular a infraestrutura de Active Directory implantada (controladores de domínio) ao Azure AD. Para fazer isso, torne a infraestrutura de Active Directory implantada um subdomínio de uma floresta do Azure AD.

## <a name="disclaimer"></a>Exclusão de Responsabilidade

 - Este documento é apenas para fins informativos. A MICROSOFT NÃO OFERECE NENHUMA GARANTIA, EXPRESSA, IMPLÍCITA OU ESTATUTÁRIA, QUANTO ÀS INFORMAÇÕES CONTIDAS NESTE DOCUMENTO. Este documento é fornecido "no estado em que se encontra". As informações e as exibições expressas neste documento, incluindo URLs e outras referências de site da Internet, podem ser alteradas sem aviso prévio. Os clientes que lêem este documento trazem o risco de usá-lo.
 - Este documento não fornece aos clientes nenhum direito legal sobre qualquer propriedade intelectual em qualquer produto ou solução da Microsoft.
 - Os clientes podem copiar e usar este documento para fins de referência interna.
 - Determinadas recomendações neste documento podem resultar em maior uso de recursos de computação, rede ou dados no Azure, e podem aumentar os custos de assinatura ou de licença do Azure do cliente.
 - Essa arquitetura destina-se a servir como base para os clientes se ajustarem aos requisitos específicos e não devem ser usados no estado em que se encontram em um ambiente de produção.
 - Este documento é desenvolvido como uma referência e não deve ser usado para definir todos os meios pelos quais um cliente pode atender a requisitos e regulamentos específicos de conformidade. Os clientes devem buscar o suporte legal de sua organização em implementações aprovadas do cliente.
