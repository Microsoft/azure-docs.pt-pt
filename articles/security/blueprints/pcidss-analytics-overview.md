---
title: Blueprint de Segurança e Conformidade do Azure-análise de dados para PCI DSS
description: Blueprint de Segurança e Conformidade do Azure-análise de dados para PCI DSS
services: security
author: meladie
ms.assetid: 6c48cd8e-c548-44e8-a21a-17316c152cbb
ms.service: security
ms.topic: article
ms.date: 07/03/2018
ms.author: meladie
ms.openlocfilehash: 67870cdfcf92bb49d6e4f954b7001a8a705dcc42
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73496403"
---
# <a name="azure-security-and-compliance-blueprint-analytics-for-pci-dss"></a>Blueprint de Segurança e Conformidade do Azure: Analytics para PCI DSS

## <a name="overview"></a>Descrição geral

Este Blueprint de Segurança e Conformidade do Azure fornece diretrizes para a implantação de uma arquitetura de análise de dados no Azure que auxilia nos requisitos dos padrões de segurança de dados do setor de cartão de pagamento (PCI DSS 3,2). Ele apresenta uma arquitetura de referência comum e demonstra o tratamento adequado de dados de cartão de crédito (incluindo o número do cartão, a expiração e os dados de verificação) em um ambiente seguro, em conformidade e de várias camadas. Este projeto demonstra maneiras nas quais os clientes podem atender aos requisitos específicos de segurança e conformidade e serve como base para os clientes criarem e configurarem suas próprias soluções de análise de dados no Azure.

Essa arquitetura de referência, guia de implementação e modelo de ameaça fornecem uma base para os clientes cumprirem os requisitos de PCI DSS 3,2. Essa solução fornece uma linha de base para ajudar os clientes a implantar cargas de trabalho no Azure de uma maneira compatível com o PCI DSS 3,2; no entanto, essa solução não deve ser usada no estado em que se encontra em um ambiente de produção, pois é necessária uma configuração adicional.

A obtenção de PCI DSS-conformidade exige que um QSA (Assessornt Security) qualificado certificada de uma solução de cliente de produção. Os clientes são responsáveis por realizar avaliações apropriadas de segurança e conformidade de qualquer solução criada usando essa arquitetura, já que os requisitos podem variar com base nas especificidades da implementação de cada cliente.

## <a name="architecture-diagram-and-components"></a>Diagrama e componentes da arquitetura

Este Blueprint de Segurança e Conformidade do Azure fornece uma plataforma de análise na qual os clientes podem criar suas próprias ferramentas de análise. A arquitetura de referência descreve um caso de uso genérico em que os clientes de entrada são dados por meio de importações de dados em massa pelo administrador do SQL/data ou por meio de atualizações de dados operacionais por meio de um usuário operacional. Os dois fluxos de trabalho incorporam Azure Functions para importar dados para o banco de dado SQL do Azure. Azure Functions deve ser configurado pelo cliente por meio da portal do Azure para lidar com as tarefas de importação exclusivas dos requisitos de análise de cada cliente.

O Azure oferece uma variedade de relatórios e serviços de análise para os clientes. Essa solução incorpora Azure Machine Learning em conjunto com o banco de dados SQL do Azure para navegar rapidamente por data e fornecer resultados mais rápidos por meio da modelagem mais inteligente. Azure Machine Learning aumenta as velocidades de consulta por meio da descoberta de novas relações entre conjuntos de os. Depois que os dados tiverem sido treinados por meio de várias funções estatísticas, até 7 pools de consulta adicionais (8 total incluindo o servidor do cliente) podem ser sincronizados com os mesmos modelos de tabela para espalhar cargas de trabalho de consulta e reduzir os tempos de resposta.

Para análise e relatórios aprimorados, os bancos de dados SQL do Azure podem ser configurados com índices columnstore. Tanto o Azure Machine Learning quanto os bancos de dados SQL do Azure podem ser escalados ou reduzidos verticalmente ou desligados completamente em resposta ao uso do cliente. Todo o tráfego do SQL é criptografado com SSL por meio da inclusão de certificados autoassinados. Como prática recomendada, o Azure recomenda o uso de uma autoridade de certificação confiável para aumentar a segurança.

Depois que os dados são carregados no banco de dado SQL do Azure e treinados pelo Azure Machine Learning, eles são resumidos pelo usuário operacional e pelo administrador do SQL/data com Power BI. Power BI exibe os dados de maneira intuitiva e reúne informações em vários conjuntos de dados para extrair mais insights. Seu alto grau de adaptabilidade e fácil integração com o banco de dados SQL do Azure garante que os clientes possam configurá-lo para lidar com uma ampla gama de cenários conforme exigido por suas necessidades comerciais.

A solução usa contas de armazenamento do Azure, que os clientes podem configurar para usar o Criptografia do Serviço de Armazenamento para manter a confidencialidade dos dados em repouso. O Azure armazena três cópias de dados no datacenter selecionado de um cliente para fins de resiliência. O armazenamento com redundância geográfica garante que os dados serão replicados em um datacenter secundário a centenas de quilômetros de distância e, novamente, armazenados como três cópias dentro desse datacenter, impedindo que um evento adverso na data center primária do cliente resulte em uma perda de dado.

Para aumentar a segurança, todos os recursos nesta solução são gerenciados como um grupo de recursos por meio de Azure Resource Manager. Azure Active Directory controle de acesso baseado em função é usado para controlar o acesso a recursos implantados, incluindo suas chaves no Azure Key Vault. A integridade do sistema é monitorada por meio da central de segurança do Azure e Azure Monitor. Os clientes configuram ambos os serviços de monitoramento para capturar logs e exibir a integridade do sistema em um único painel fácil e navegável.

O banco de dados SQL do Azure é normalmente gerenciado por meio do SQL Server Management Studio (SSMS), que é executado de um computador local configurado para acessar o banco de dados SQL do Azure por meio de uma conexão VPN ou ExpressRoute segura. **A Microsoft recomenda configurar uma conexão VPN ou ExpressRoute para gerenciamento e importação de dados no grupo de recursos arquitetura de referência**.

![Análise para PCI DSS diagrama de arquitetura de referência](images/pcidss-analytics-architecture.png "Análise para PCI DSS diagrama de arquitetura de referência")

Essa solução usa os seguintes serviços do Azure. Os detalhes da arquitetura de implantação estão na seção [arquitetura de implantação](#deployment-architecture) .

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
- Armazenamento do Azure
- Rede Virtual do Azure
    - (1)/16 rede
    - (2)/24 redes
    - (2) grupos de segurança de rede
- Dashboard do Power BI

## <a name="deployment-architecture"></a>Arquitetura de implantação

A seção a seguir detalha os elementos de implantação e implementação.

**Grade de eventos do Azure**: a [grade de eventos do Azure](https://docs.microsoft.com/azure/event-grid/overview) permite que os clientes criem facilmente aplicativos com arquiteturas baseadas em eventos. Os usuários selecionam o recurso do Azure para o qual desejam assinar e concedemos ao manipulador de eventos ou webhook um ponto de extremidade para o qual enviar o evento. Os clientes podem proteger pontos de extremidade de webhook adicionando parâmetros de consulta à URL do webhook ao criar uma assinatura de evento. A grade de eventos do Azure só dá suporte a pontos de extremidade de webhook HTTPS. A grade de eventos do Azure permite que os clientes controlem o nível de acesso fornecido a diferentes usuários para realizar várias operações de gerenciamento, como listar assinaturas de evento, criar novos e gerar chaves. A grade de eventos utiliza o controle de acesso baseado em função do Azure.

**Azure Functions**: o [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) é um serviço de computação sem servidor que permite que os usuários executem o código sob demanda independentemente de precisar provisionar ou gerenciar explicitamente a infraestrutura. Utilize as Funções do Azure para executar um script ou fragmento de código em resposta a uma variedade de eventos.

**Azure Machine Learning**: [Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/) é uma técnica de ciência de dados que permite que os computadores usem dados existentes para prever comportamentos, resultados e tendências futuros.

**Catálogo de dados do Azure**: o [Catálogo de dados](../../data-catalog/overview.md) torna as fontes de dados facilmente detectáveis e compreensíveis pelos usuários que gerenciam os dados. As fontes de dados comuns podem ser registradas, marcadas e pesquisadas para dados financeiros. Os dados permanecem em seu local existente, mas uma cópia de seus metadados é adicionada ao catálogo de dados, juntamente com uma referência ao local da fonte de dados. Os metadados também são indexados para tornar cada origem de dados facilmente detetável através da pesquisa e compreensível para os utilizadores que a detetarem.

### <a name="virtual-network"></a>Rede virtual

A arquitetura define uma rede virtual privada com um espaço de endereço de 10.200.0.0/16.

**Grupos de segurança de rede**: os [grupos de segurança de rede](../../virtual-network/virtual-network-vnet-plan-design-arm.md) contêm listas de controle de acesso que permitem ou negam o tráfego em uma rede virtual. Os grupos de segurança de rede podem ser usados para proteger o tráfego em uma sub-rede ou nível de VM individual. Existem os seguintes grupos de segurança de rede:

  - Um grupo de segurança de rede para Active Directory
  - Um grupo de segurança de rede para a carga de trabalho

Cada um dos grupos de segurança de rede tem portas e protocolos específicos abertos para que a solução possa funcionar de forma segura e correta. Além disso, as seguintes configurações estão habilitadas para cada grupo de segurança de rede:

- [Os logs e eventos de diagnóstico](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) são habilitados e armazenados em uma conta de armazenamento
- Os logs de Azure Monitor estão conectados aos [logs de&#39;diagnóstico s do grupo de segurança de rede](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

Sub- **redes**: cada sub-rede é associada ao seu grupo de segurança de rede correspondente.

### <a name="data-in-transit"></a>Dados em trânsito

Por padrão, o Azure criptografa todas as comunicações entre os data centers do Azure. Todas as transações para o armazenamento do Azure por meio do portal do Azure ocorrem via HTTPS.

### <a name="data-at-rest"></a>Dados inativos

A arquitetura protege os dados em repouso por meio de criptografia, auditoria de banco e outras medidas.

**Armazenamento do Azure**: para atender aos requisitos de dados criptografados em repouso, todo o [armazenamento do Azure](https://azure.microsoft.com/services/storage/) usa [criptografia do serviço de armazenamento](../../storage/common/storage-service-encryption.md).  Isso ajuda a proteger e proteger os dados do titular do cartão para dar suporte a compromissos de segurança organizacional e requisitos de conformidade definidos pelo PCI DSS 3,2.

**Azure Disk Encryption**: [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) aproveita o recurso BitLocker do Windows para fornecer criptografia de volume para discos de dados. A solução se integra com Azure Key Vault para ajudar a controlar e gerenciar as chaves de criptografia de disco.

**Banco de dados SQL do Azure**: a instância do banco de dados SQL do Azure usa as seguintes medidas de segurança:

- [Active Directory autenticação e autorização](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) permitem o gerenciamento de identidades de usuários de banco de dados e outros serviços da Microsoft em um local central.
- A [auditoria do banco de dados SQL](../../sql-database/sql-database-auditing.md) rastreia eventos de banco de dados e os grava em um log de auditoria em uma conta de armazenamento do Azure.
- O banco de dados SQL do Azure está configurado para usar a [Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), que executa criptografia e descriptografia em tempo real do banco dados, backups associados e arquivos de log de transações para proteger informações em repouso. A Transparent Data Encryption fornece garantia de que os dados armazenados não estão sujeitos a acesso não autorizado.
- [As regras de firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) impedem todo o acesso a servidores de banco de dados até que sejam concedidas permissões adequadas A firewall concede acesso a bases de dados com base no endereço IP de origem de cada pedido.
- A [detecção de ameaças do SQL](../../sql-database/sql-database-threat-detection.md) permite a detecção e a resposta a ameaças potenciais à medida que elas ocorrem, fornecendo alertas de segurança para atividades suspeitas de banco de dados, vulnerabilidades potenciais, ataques de injeção de SQL e padrões de acesso de banco de dados anormais
- As [colunas criptografadas](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) garantem que os dados confidenciais nunca apareçam como texto sem formatação no sistema de banco de dados. Depois de habilitar a criptografia de dados, somente aplicativos cliente ou servidores de aplicativos com acesso às chaves podem acessar dados de texto sem formatação.
- [As propriedades estendidas](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql) podem ser usadas para interromper o processamento de entidades de dados, pois ela permite que os usuários adicionem Propriedades personalizadas a objetos de banco de dados e marquem como "descontinuados" para dar suporte à lógica do aplicativo para evitar o processamento de finanças financeiras associadas dado.
- A [segurança em nível de linha](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) permite que os usuários definam políticas para restringir o acesso aos dados para o processamento descontinuado.
- O [mascaramento de dados dinâmicos do SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) limita a exposição de dados confidenciais mascarando os dados para usuários ou aplicativos sem privilégios. A máscara de dados dinâmicos pode descobrir dados potencialmente confidenciais automaticamente e sugerir que as máscaras apropriadas sejam aplicadas. Isso ajuda a identificar e reduzir o acesso a dados de modo que ele não saia do banco por acesso não autorizado. Os clientes são responsáveis por ajustar as configurações de mascaramento de dados dinâmicos para aderir ao seu esquema de banco de dado.

### <a name="identity-management"></a>Gestão de identidades

As tecnologias a seguir fornecem recursos para gerenciar o acesso aos dados no ambiente do Azure:

- [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) é o&#39;serviço de gerenciamento de identidade e diretório multilocatário baseado em nuvem da Microsoft. Todos os usuários desta solução são criados no Azure Active Directory, incluindo usuários que acessam o banco de dados SQL do Azure.
- A autenticação para o aplicativo é executada usando Azure Active Directory. Para obter mais informações, consulte [integrando aplicativos com Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). Além disso, a criptografia de coluna de banco de dados usa Azure Active Directory para autenticar o aplicativo no banco de dados SQL do Azure. Para obter mais informações, Confira como [proteger dados confidenciais no Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
- O [controle de acesso baseado em função do Azure](../../role-based-access-control/role-assignments-portal.md) permite que os administradores definam permissões de acesso refinadas para conceder apenas a quantidade de acesso que os usuários precisam para executar seus trabalhos. Em vez de fornecer a cada usuário permissão irrestrita para recursos do Azure, os administradores podem permitir apenas determinadas ações para acessar dados. O acesso à assinatura é limitado ao administrador da assinatura.
- [Azure Active Directory Privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md) permite que os clientes minimizem o número de usuários que têm acesso a determinadas informações. Os administradores podem usar Azure Active Directory Privileged Identity Management para descobrir, restringir e monitorar identidades com privilégios e seu acesso aos recursos. Essa funcionalidade também pode ser usada para impor acesso administrativo sob demanda e Just-in-time quando necessário.
- [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview.md) detecta possíveis vulnerabilidades que afetam as&#39;identidades de uma organização, configura as respostas automatizadas para detectar ações suspeitas&#39;relacionadas a identidades de uma organização e investiga incidentes suspeitos para tomar as medidas apropriadas para resolvê-los.

### <a name="security"></a>Segurança

**Gerenciamento de segredos**: a solução usa [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) para o gerenciamento de chaves e segredos. O cofre de chave do Azure ajuda a salvaguardar as chaves criptográficas e os segredos utilizados pelas aplicações em nuvem e pelos serviços. Os recursos de Azure Key Vault a seguir ajudam os clientes a proteger e acessar esses dados:

- As políticas de acesso avançadas são configuradas de acordo com a necessidade.
- Key Vault políticas de acesso são definidas com as permissões mínimas necessárias para chaves e segredos.
- Todas as chaves e segredos em Key Vault têm datas de expiração.
- Todas as chaves em Key Vault são protegidas por módulos de segurança de hardware especializados. O tipo de chave é uma chave RSA de 2048 bits protegida por HSM.
- Todos os usuários e identidades recebem as permissões mínimas necessárias usando o controle de acesso baseado em função.
- Os logs de diagnóstico para Key Vault estão habilitados com um período de retenção de pelo menos 365 dias.
- As operações de criptografia permitidas para chaves são restritas às necessárias.

**Central de segurança do Azure**: com a [central de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro), os clientes podem aplicar e gerenciar centralmente políticas de segurança entre cargas de trabalho, limitar a exposição a ameaças e detectar e responder a ataques. Além disso, a central de segurança do Azure acessa as configurações existentes dos serviços do Azure para fornecer recomendações de configuração e de serviço para ajudar a melhorar a postura de segurança e proteger os dados.

A central de segurança do Azure usa uma variedade de recursos de detecção para alertar os clientes sobre possíveis ataques visando seus ambientes. Estes alertas contêm informações valiosas sobre o que acionou o alerta, os recursos afetados e a origem do ataque. A central de segurança do Azure tem um conjunto de [alertas de segurança predefinidos](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), que são disparados quando ocorre uma ameaça ou atividade suspeita. [As regras de alerta personalizadas](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) na central de segurança do Azure permitem que os clientes definam novos alertas de segurança com base nos dados que já foram coletados de seu ambiente.

A central de segurança do Azure fornece alertas e incidentes de segurança priorizados, tornando mais simples para os clientes descobrir e resolver problemas potenciais de segurança. Um [relatório de inteligência contra ameaças](https://docs.microsoft.com/azure/security-center/security-center-threat-report) é gerado para cada ameaça detectada para ajudar as equipes de resposta a incidentes na investigação e na correção de ameaças.

### <a name="logging-and-auditing"></a>Registo e auditoria

Os serviços do Azure registram extensivamente a atividade do sistema e do usuário, bem como a integridade do sistema:
- **Logs de atividade**: [os logs de atividade](../../azure-monitor/platform/activity-logs-overview.md) fornecem informações sobre as operações executadas nos recursos em uma assinatura. Os logs de atividades podem ajudar a determinar o iniciador de uma operação, a hora de ocorrência e o status.
- **Logs de diagnóstico**: os [logs de diagnóstico](../../azure-monitor/platform/resource-logs-overview.md) incluem todos os logs emitidos por cada recurso. Esses logs incluem logs do sistema de eventos do Windows, logs de armazenamento do Azure, Key Vault logs de auditoria e acesso do gateway de aplicativo e logs de firewall. Todos os logs de diagnóstico gravam em uma conta de armazenamento do Azure centralizada e criptografada para arquivamento. A retenção é configurável pelo usuário, até 730 dias, para atender aos requisitos de retenção específicos da organização.

**Logs de Azure monitor**: esses logs são consolidados em [logs de Azure monitor](https://azure.microsoft.com/services/log-analytics/) para processamento, armazenamento e relatórios de Dashboard. Depois de coletados, os dados são organizados em tabelas separadas para cada tipo de dados dentro de Log Analytics espaços de trabalho, o que permite que todos os dados sejam analisados juntos, independentemente de sua fonte original. Além disso, a central de segurança do Azure integra-se aos logs de Azure Monitor, permitindo que os clientes usem consultas Kusto para acessar seus dados de eventos de segurança e combiná-los com dados de outros serviços.

As seguintes [soluções de monitoramento](../../monitoring/monitoring-solutions.md) do Azure estão incluídas como parte dessa arquitetura:
-   [Avaliação do Active Directory](../../azure-monitor/insights/ad-assessment.md): a solução de verificação de integridade Active Directory avalia o risco e a integridade dos ambientes de servidor em um intervalo regular e fornece uma lista priorizada de recomendações específicas para a infraestrutura de servidor implantada.
- [Avaliação do SQL](../../azure-monitor/insights/sql-assessment.md): a solução de verificação da integridade do SQL avalia o risco e a integridade de ambientes de servidor em um intervalo regular e fornece aos clientes uma lista priorizada de recomendações específicas para a infraestrutura de servidor implantada.
- [Integridade do agente](../../monitoring/monitoring-solution-agenthealth.md): a solução integridade do agente relata Quantos agentes são implantados e sua distribuição geográfica, bem como Quantos agentes que não respondem e o número de agentes que estão enviando dados operacionais.
-   [Análise do log de atividades](../../azure-monitor/platform/collect-activity-logs.md): a solução de análise do log de atividades auxilia na análise dos logs de atividades do Azure em todas as assinaturas do Azure para um cliente.

**Automação do Azure**: a [automação do Azure](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) armazena, executa e gerencia runbooks. Nesta solução, os runbooks ajudam a coletar logs do banco de dados SQL do Azure. A solução de [controle de alterações](../../automation/change-tracking.md) de automação permite que os clientes identifiquem facilmente as alterações no ambiente.

**Azure monitor**: [Azure monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) ajuda os usuários a controlar o desempenho, manter a segurança e identificar tendências, permitindo que as organizações façam auditoria, criem alertas e arquivem dados, incluindo o acompanhamento de chamadas à API em seus recursos do Azure.

**Application insights**: [Application insights](https://docs.microsoft.com/azure/application-insights/) é um serviço de gerenciamento de desempenho de aplicativos (APM) extensível para desenvolvedores da Web em várias plataformas. Ele detecta anomalias de desempenho e inclui ferramentas de análise poderosas para ajudar a diagnosticar problemas e entender o que os usuários realmente fazem com o aplicativo. Ele foi projetado para ajudar os usuários a melhorar continuamente o desempenho e a usabilidade.

## <a name="threat-model"></a>Modelo de ameaça

O diagrama de fluxo de dados para esta arquitetura de referência está disponível para [Download](https://aka.ms/PCIAnalyticsTM) ou pode ser encontrado abaixo. Esse modelo pode ajudar os clientes a entender os pontos de risco potencial na infraestrutura do sistema ao fazer modificações.

![Análise para PCI DSS diagrama de arquitetura de referência](images/pcidss-analytics-threat-model.png "Análise para PCI DSS modelo de ameaça")

## <a name="compliance-documentation"></a>Documentação de conformidade
A [matriz de responsabilidade do cliente de Blueprint de Segurança e Conformidade do Azure PCI DSS](https://aka.ms/pcidss-analytics-tm) lista as responsabilidades de todos os requisitos de PCI DSS 3,2.

A [matriz de implementação de análise de dados de Blueprint de Segurança e Conformidade do Azure PCI DSS](https://aka.ms/pcidss-analytics-cim) fornece informações sobre quais requisitos de PCI DSS 3,2 são tratados pela arquitetura de análise de dados, incluindo descrições detalhadas de como o a implementação atende aos requisitos de cada controle abordado.


## <a name="guidance-and-recommendations"></a>Diretrizes e recomendações

### <a name="vpn-and-expressroute"></a>VPN e ExpressRoute

Um túnel VPN seguro ou o [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) precisa ser configurado para estabelecer com segurança uma conexão com os recursos implantados como parte dessa arquitetura de referência de análise de dados. Ao configurar adequadamente uma VPN ou ExpressRoute, os clientes podem adicionar uma camada de proteção para os dados em trânsito.

Ao implementar um túnel VPN seguro com o Azure, uma conexão privada virtual entre uma rede local e uma rede virtual do Azure pode ser criada. Essa conexão ocorre pela Internet e permite aos clientes "encapsular" informações com segurança dentro de um link criptografado entre a rede do cliente e o Azure. A VPN site a site é uma tecnologia segura e madura, que foi implantada por empresas de todos os tamanhos por décadas. O [modo de encapsulamento IPSec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) é usado nessa opção como um mecanismo de criptografia.

Como o tráfego dentro do túnel VPN atravessa a Internet com uma VPN site a site, a Microsoft oferece outra opção de conexão ainda mais segura. O Azure ExpressRoute é um link WAN dedicado entre o Azure e um local ou um provedor de hospedagem do Exchange. Como as conexões do ExpressRoute não passam pela Internet, essas conexões oferecem mais confiabilidade, velocidades mais rápidas, latências menores e mais segurança do que as conexões típicas pela Internet. Além disso, como essa é uma conexão direta do provedor de telecomunicação do cliente, os dados não viajam pela Internet e, portanto, não são expostos a ele.

As práticas recomendadas para implementar uma rede híbrida segura que estende uma rede local para o Azure estão [disponíveis](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

### <a name="extract-transform-load-process"></a>Processo de extração-transformação-carregamento
O [polybase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) pode carregar dados no banco de dado SQL do Azure sem a necessidade de uma ferramenta separada de extração, transformação, carregamento ou importação. O polybase permite o acesso a dados por meio de consultas T-SQL. A business intelligence e a pilha de análise da Microsoft, bem como ferramentas de terceiros compatíveis com o SQL Server, podem ser usadas com o polybase.

### <a name="azure-active-directory-setup"></a>Azure Active Directory configuração
[Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) é essencial para gerenciar a implantação e o provisionamento de acesso à equipe interagindo com o ambiente. Um Active Directory do Windows Server existente pode ser integrado com Azure Active Directory em [quatro cliques](../../active-directory/hybrid/how-to-connect-install-express.md). Os clientes também podem vincular a infraestrutura de Active Directory implantada (controladores de domínio) a um Azure Active Directory existente, tornando a infraestrutura de Active Directory implantada um subdomínio de uma floresta Azure Active Directory.

## <a name="disclaimer"></a>Exclusão de Responsabilidade

 - Este documento é apenas para fins informativos. A MICROSOFT NÃO OFERECE NENHUMA GARANTIA, EXPRESSA, IMPLÍCITA OU ESTATUTÁRIA, QUANTO ÀS INFORMAÇÕES CONTIDAS NESTE DOCUMENTO. Este documento é fornecido "no estado em que se encontra". As informações e as exibições expressas neste documento, incluindo URLs e outras referências de site da Internet, podem ser alteradas sem aviso prévio. Os clientes que lêem este documento trazem o risco de usá-lo.
 - Este documento não fornece aos clientes nenhum direito legal sobre qualquer propriedade intelectual em qualquer produto ou solução da Microsoft.
 - Os clientes podem copiar e usar este documento para fins de referência interna.
 - Determinadas recomendações neste documento podem resultar em maior uso de recursos de computação, rede ou dados no Azure, e podem aumentar os custos de assinatura ou de licença do Azure do cliente.
 - Essa arquitetura destina-se a servir como base para os clientes se ajustarem aos requisitos específicos e não devem ser usados no estado em que se encontram em um ambiente de produção.
 - Este documento é desenvolvido como uma referência e não deve ser usado para definir todos os meios pelos quais um cliente pode atender a requisitos e regulamentos específicos de conformidade. Os clientes devem buscar o suporte legal de sua organização em implementações aprovadas do cliente.
