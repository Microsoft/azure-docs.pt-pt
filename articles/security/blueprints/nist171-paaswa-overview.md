---
title: Aplicativo Web Blueprint de Segurança e Conformidade do Azure-PaaS para NIST SP 800-171
description: Blueprint de Segurança e Conformidade do Azure-aplicativo Web de PaaS SP 800-171 NIST
services: security
author: jomolesk
ms.assetid: eea21a0a-5930-43e8-937f-5419c20744c9
ms.service: security
ms.topic: article
ms.date: 07/31/2018
ms.author: jomolesk
ms.openlocfilehash: 22b18988406beb5bed491d7101f376340d35f623
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71259789"
---
# <a name="azure-security-and-compliance-blueprint---paas-web-application-for-nist-special-publication-800-171"></a>Aplicativo Web Blueprint de Segurança e Conformidade do Azure-PaaS para a publicação especial do NIST 800-171

## <a name="overview"></a>Descrição geral
A [publicação especial do NIST 800-171](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) fornece diretrizes para proteger as informações não classificadas (CUI) controladas que residem em sistemas de informações e organizações não federais. O NIST SP 800-171 estabelece 14 famílias de requisitos de segurança para proteger a confidencialidade da CUI.

Este Blueprint de Segurança e Conformidade do Azure fornece diretrizes para ajudar os clientes a implantar um aplicativo Web de PaaS (plataforma como serviço) no Azure que implementa um subconjunto de controles NIST SP 800-171. Esta solução demonstra maneiras em que os clientes podem atender aos requisitos específicos de segurança e conformidade. Ele também serve como base para os clientes criarem e configurarem seu próprio aplicativo Web no Azure.

Essa arquitetura de referência, o guia de implementação associado e o modelo de ameaça devem servir como base para os clientes se adaptarem aos seus requisitos específicos. Eles não devem ser usados no estado em que se encontram em um ambiente de produção. Implantar essa arquitetura sem modificação é insuficiente para atender totalmente aos requisitos do NIST SP 800-171. Os clientes são responsáveis por realizar avaliações apropriadas de segurança e conformidade de qualquer solução criada usando essa arquitetura. Os requisitos podem variar com base nas especificidades da implementação de cada cliente.

## <a name="architecture-diagram-and-components"></a>Diagrama e componentes da arquitetura

Este Blueprint de Segurança e Conformidade do Azure fornece uma arquitetura de referência para um aplicativo Web de PaaS com um back-end do banco de dados SQL do Azure. O aplicativo Web é hospedado em um ambiente de serviço de aplicativo isolado, que é um ambiente privado dedicado em uma data center do Azure. A carga do ambiente equilibra o tráfego para o aplicativo Web em VMs (máquinas virtuais) gerenciadas pelo Azure. Essa arquitetura também inclui NSGs (grupos de segurança de rede), um gateway de aplicativo do Azure, um DNS do Azure e um Azure Load Balancer.

Para análise e relatórios aprimorados, os bancos de dados SQL do Azure podem ser configurados com índices columnstore. Os bancos de dados SQL do Azure podem ser escalados verticalmente ou desligados completamente em resposta ao uso do cliente. Todo o tráfego do SQL é criptografado com SSL por meio da inclusão de certificados autoassinados. Como prática recomendada, o Azure recomenda o uso de uma autoridade de certificação confiável para aumentar a segurança.

A solução usa contas de armazenamento do Azure, que os clientes podem configurar para usar o Criptografia do Serviço de Armazenamento para manter a confidencialidade dos dados em repouso. O Azure armazena três cópias de dados dentro de um data center selecionado do cliente para fins de resiliência. O armazenamento com redundância geográfica garante que os dados sejam replicados para um secundário data center a centenas de quilômetros de distância e armazenados novamente como três cópias dentro dessa data center. Essa organização impede que um evento adverso no data center principal do cliente resulte em uma perda de dados.

Para aumentar a segurança, todos os recursos nesta solução são gerenciados como um grupo de recursos por meio de Azure Resource Manager. O RBAC (controle de acesso baseado em função) do Azure Active Directory (Azure AD) é usado para controlar o acesso aos recursos implantados. Esses recursos incluem as chaves do cliente no Azure Key Vault. A integridade do sistema é monitorada por meio de Azure Monitor. Os clientes configuram esse serviço de monitoramento para capturar logs. A integridade do sistema é exibida em um único painel que é fácil de usar.

O banco de dados SQL é normalmente gerenciado por meio de SQL Server Management Studio. Ele é executado de um computador local configurado para acessar o banco de dados SQL por meio de uma VPN segura ou conexão do Azure ExpressRoute.

Application Insights fornece análise e gerenciamento de desempenho de aplicativos em tempo real por meio de logs de Azure Monitor a *Microsoft recomenda que você configure uma conexão VPN ou ExpressRoute para gerenciamento e importação de dados para a arquitetura de referência sub-rede.*

![Diagrama de arquitetura de referência do aplicativo Web PaaS SP 800-171](images/nist171-paaswa-architecture.png "Diagrama de arquitetura de referência do aplicativo Web PaaS SP 800-171")

Essa solução usa os seguintes serviços do Azure. Para obter mais informações, consulte a seção [arquitetura de implantação](#deployment-architecture) .

- Máquinas Virtuais do Azure
    - (1) gerenciamento/bastiões (Windows Server 2016 Datacenter)
- Rede Virtual do Azure
    - (1)/16 rede
    - (4)/24 redes
    - (4) grupos de segurança de rede
- Gateway de Aplicação do Azure
    - Firewall de aplicações Web
        - Modo de firewall: prevenção
        - Conjunto de regras: OWASP
        - Porta do ouvinte: 443
- Application Insights
- Azure Active Directory
- Ambiente do Serviço de Aplicativo v2
- Automatização do Azure
- DNS do Azure
- Azure Key Vault
- Balanceador de Carga do Azure
- Azure Monitor (logs)
- Azure Resource Manager
- Centro de Segurança do Azure
- Base de Dados SQL do Azure
- Storage do Azure
- Automatização do Azure
- Aplicações Web do Azure

## <a name="deployment-architecture"></a>Arquitetura de implantação
A seção a seguir detalha os elementos de implantação e implementação.

**Azure Resource Manager**: O [Gerenciador de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) pode ser usado por clientes para trabalhar com os recursos na solução como um grupo. Os clientes podem implantar, atualizar ou excluir todos os recursos da solução em uma única operação coordenada. Os clientes usam um modelo para implantação. O modelo pode funcionar para ambientes diferentes, como teste, preparo e produção. O Gerenciador de recursos fornece recursos de segurança, auditoria e marcação para ajudar os clientes a gerenciar seus recursos após a implantação.

**Host bastião**: O host de bastiões é o ponto único de entrada que os usuários podem usar para acessar os recursos implantados nesse ambiente. O host bastião fornece uma conexão segura para recursos implantados, permitindo apenas o tráfego remoto de endereços IP públicos em uma lista segura. Para permitir o tráfego de área de trabalho remota, a origem do tráfego deve ser definida no NSG.

Essa solução cria uma VM como um host de bastiões ingressado no domínio com as seguintes configurações:
-   [Extensão Antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware).
-   [Extensão de diagnóstico do Azure](../../virtual-machines/windows/extensions-diagnostics-template.md).
-   [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) usando Key Vault.
-   Uma [política de desligamento automático](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) para reduzir o consumo de recursos da VM quando não estiver em uso.
-   O [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) está habilitado para que as credenciais e outros segredos sejam executados em um ambiente protegido isolado do sistema operacional em execução.

**Aplicativos Web**: Os [aplicativos Web](https://docs.microsoft.com/azure/app-service/) são um recurso de serviço Azure app. Os clientes podem usá-lo para criar e hospedar aplicativos Web na linguagem de programação de sua escolha sem gerenciar a infraestrutura. Ele oferece dimensionamento automático e alta disponibilidade. Ele dá suporte ao Windows e ao Linux e permite implantações automatizadas do GitHub, do Azure DevOps ou de qualquer repositório git.

**Ambiente do serviço de aplicativo**: [Ambiente do serviço de aplicativo](https://docs.microsoft.com/azure/app-service/environment/intro) é um recurso do serviço de aplicativo. Ele fornece um ambiente totalmente isolado e dedicado para a execução segura de aplicativos do serviço de aplicativo em alta escala.

O ambiente do serviço de aplicativo é isolado para executar apenas um único aplicativo. Ele é sempre implantado em uma rede virtual. Devido ao recurso de isolamento, a arquitetura de referência tem isolamento de locatário completo e é removida do ambiente multilocatário do Azure. Os clientes têm controle refinado sobre o tráfego de rede do aplicativo de entrada e saída. Os aplicativos podem estabelecer conexões seguras de alta velocidade em redes virtuais para recursos corporativos locais. Os clientes podem "dimensionar automaticamente" com Ambiente do Serviço de Aplicativo com base nas métricas de carga, no orçamento disponível ou em um agendamento definido.

O uso de Ambiente do Serviço de Aplicativo para essa arquitetura fornece os seguintes controles e configurações:

- Hospede dentro de uma rede virtual do Azure protegida e regras de segurança de rede.
- Certificado de balanceador de carga interno autoassinado para comunicação HTTPS. Como prática recomendada, a Microsoft recomenda o uso de uma autoridade de certificação confiável para aumentar a segurança.
- [Modo de balanceamento de carga interno](../../app-service/environment/app-service-environment-with-internal-load-balancer.md) (modo 3).
- Desabilite o [TLS 1,0](../../app-service/environment/app-service-app-service-environment-custom-settings.md).
- Altere a [codificação TLS](../../app-service/environment/app-service-app-service-environment-custom-settings.md).
- Controle [portas N/W de tráfego de entrada](../../app-service/environment/app-service-app-service-environment-control-inbound-traffic.md).
- [Firewall do aplicativo Web – restringir dados](../../app-service/environment/app-service-app-service-environment-web-application-firewall.md).
- Permitir o [tráfego do banco de dados SQL do Azure](../../app-service/environment/app-service-app-service-environment-network-architecture-overview.md).

### <a name="virtual-network"></a>Rede virtual
A arquitetura define uma rede virtual privada com um espaço de endereço de 10.200.0.0/16.

**Grupos de segurança de rede**: [NSGs](../../virtual-network/virtual-network-vnet-plan-design-arm.md) contêm listas de controle de acesso que permitem ou negam o tráfego em uma rede virtual. NSGs pode ser usado para proteger o tráfego em uma sub-rede ou nível de VM individual. Os seguintes NSGs existem:
- Um NSG para o gateway de aplicativo
- Um NSG para Ambiente do Serviço de Aplicativo
- Um NSG para o banco de dados SQL
- Um NSG para o host bastião

Cada um dos NSGs tem portas e protocolos específicos abertos para que a solução possa funcionar de forma segura e correta. Além disso, as seguintes configurações estão habilitadas para cada NSG:
  - [Os logs e eventos de diagnóstico](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) são habilitados e armazenados em uma conta de armazenamento.
  - Os logs de Azure Monitor estão conectados ao [diagnóstico do NSG](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json).

**Sub-redes**: Cada sub-rede é associada ao seu NSG correspondente.

**DNS do Azure**: O DNS (sistema de nomes de domínio) é responsável por converter (ou resolver) um site ou nome de serviço para seu endereço IP. O [DNS do Azure](https://docs.microsoft.com/azure/dns/dns-overview) é um serviço de hospedagem para domínios DNS que fornece resolução de nomes usando a infraestrutura do Azure. Ao hospedar domínios no Azure, os usuários podem gerenciar registros DNS usando as mesmas credenciais, APIs, ferramentas e cobrança que outros serviços do Azure. O DNS do Azure também dá suporte a domínios DNS privados.

**Azure Load Balancer**: [Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) pode ser usado por clientes para dimensionar seus aplicativos e para criar alta disponibilidade para serviços. O Load Balancer dá suporte a cenários de entrada e saída. Ele fornece baixa latência e alta taxa de transferência e escala até milhões de fluxos para todos os aplicativos TCP e UDP.

### <a name="data-in-transit"></a>Dados em trânsito
O Azure criptografa todas as comunicações de e para data centers do Azure por padrão. Todas as transações para o armazenamento do Azure por meio do portal do Azure ocorrem via HTTPS.

### <a name="data-at-rest"></a>Dados inativos

A arquitetura protege os dados em repouso por meio de criptografia, auditoria de banco e outras medidas.

**Armazenamento do Azure**: Para atender aos requisitos de dados criptografados em repouso, todo o [armazenamento](https://azure.microsoft.com/services/storage/) usa [criptografia do serviço de armazenamento](../../storage/common/storage-service-encryption.md). Esse recurso ajuda a proteger e proteger os dados para dar suporte a compromissos de segurança organizacional e requisitos de conformidade definidos pelo NIST SP 800-171.

**Azure Disk Encryption**: A [criptografia de disco](../azure-security-disk-encryption-overview.md) usa o recurso BitLocker do Windows para fornecer criptografia de volume para discos de dados. A solução se integra com Key Vault para ajudar a controlar e gerenciar as chaves de criptografia de disco.

**Banco de dados SQL do Azure**: A instância do banco de dados SQL usa as seguintes medidas de segurança de banco de dados:
-   [Active Directory autenticação e autorização](https://docs.microsoft.com/azure/sql-database/sql-database-AAD-authentication) permitem o gerenciamento de identidades de usuários de banco de dados e outros serviços da Microsoft em um local central.
-   A [auditoria do banco de dados SQL](../../sql-database/sql-database-auditing.md) rastreia eventos de banco de dados e os grava em um log de auditoria em uma conta de armazenamento do Azure.
-   O banco de dados SQL está configurado para usar a [Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql). Ele executa criptografia e descriptografia em tempo real do banco de dados, backups associados e arquivos de log de transações para proteger informações em repouso. A Transparent Data Encryption fornece garantia de que os dados armazenados não estão sujeitos a acesso não autorizado.
-   [As regras de firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) impedem todo o acesso a servidores de banco de dados até que sejam concedidas permissões adequadas A firewall concede acesso a bases de dados com base no endereço IP de origem de cada pedido.
-   A [detecção de ameaças do SQL](../../sql-database/sql-database-threat-detection.md) permite a detecção e a resposta a ameaças potenciais à medida que elas ocorrem. Ele fornece alertas de segurança para atividades suspeitas de banco de dados, possíveis vulnerabilidades, ataques de injeção de SQL e padrões de acesso de banco de dados anormais.
-   As [colunas criptografadas](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) garantem que os dados confidenciais nunca apareçam como texto sem formatação no sistema de banco de dados. Depois que a criptografia de dados é habilitada, somente aplicativos cliente ou servidores de aplicativos com acesso às chaves podem acessar dados de texto sem formatação.
- A [máscara de dados dinâmicos](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) limita a exposição de dados confidenciais mascarando os dados para usuários ou aplicativos sem privilégios. Ele pode descobrir dados potencialmente confidenciais automaticamente e sugerir as máscaras apropriadas a serem aplicadas. O mascaramento de dados dinâmicos ajuda a reduzir o acesso para que os dados confidenciais não saiam do banco por meio de acesso não autorizado. *Os clientes são responsáveis por ajustar as configurações para aderir ao seu esquema de banco de dados.*

### <a name="identity-management"></a>Gestão de identidades
As tecnologias a seguir fornecem recursos para gerenciar o acesso aos dados no ambiente do Azure:
-   O [Azure ad](https://azure.microsoft.com/services/active-directory/) é o serviço de gerenciamento de identidade e diretório multilocatário baseado em nuvem da Microsoft. Todos os usuários desta solução são criados no Azure AD e incluem usuários que acessam o banco de dados SQL.
-   A autenticação para o aplicativo é executada usando o Azure AD. Para obter mais informações, consulte como [integrar aplicativos com o Azure ad](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). A criptografia de coluna de banco de dados também usa o Azure AD para autenticar o aplicativo no banco de dados SQL. Para obter mais informações, Confira como [proteger dados confidenciais no SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   O [RBAC do Azure](../../role-based-access-control/role-assignments-portal.md) pode ser usado por administradores para definir permissões de acesso refinadas. Com ele, eles podem conceder apenas a quantidade de acesso que os usuários precisam para executar seus trabalhos. Em vez de fornecer a cada usuário acesso irrestrito aos recursos do Azure, os administradores podem permitir apenas determinadas ações para acessar recursos e dados. O acesso à assinatura é limitado ao administrador da assinatura.
- [Azure Active Directory Privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md) pode ser usado por clientes para minimizar o número de usuários que têm acesso a determinadas informações. Os administradores podem usar Azure AD Privileged Identity Management para descobrir, restringir e monitorar identidades com privilégios e seu acesso aos recursos. Essa funcionalidade também pode ser usada para impor acesso administrativo sob demanda e Just-in-time quando necessário.
- [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview.md) detecta possíveis vulnerabilidades que afetam as identidades de uma organização. Ele configura as respostas automatizadas para detectar ações suspeitas relacionadas às identidades de uma organização. Ele também investiga incidentes suspeitos para tomar as medidas apropriadas para resolvê-los.

### <a name="security"></a>Segurança
**Gerenciamento de segredos**: A solução usa [Key Vault](https://azure.microsoft.com/services/key-vault/) para o gerenciamento de chaves e segredos. Key Vault ajuda a proteger chaves criptográficas e segredos usados por aplicativos e serviços em nuvem. Os recursos de Key Vault a seguir ajudam os clientes a proteger os dados:
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

**Gateway de aplicativo Azure**: A arquitetura reduz o risco de vulnerabilidades de segurança usando um gateway de aplicativo com um firewall do aplicativo Web configurado e o conjunto de regras OWASP habilitado. Os recursos adicionais incluem:

- [SSL de ponta a ponta](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).
- Habilitar [descarregamento SSL](../../application-gateway/create-ssl-portal.md).
- Desabilite o [TLS v 1.0 e o v 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).
- [Firewall do aplicativo Web](../../application-gateway/waf-overview.md) (modo de prevenção).
- [Modo de prevenção](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) com conjunto de regras OWASP 3,0.
- Habilite o [log de diagnóstico](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).
- [Investigações de integridade personalizadas](../../application-gateway/quick-create-portal.md).
- A [central de segurança](https://azure.microsoft.com/services/security-center) e o [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) fornecem proteção e notificações adicionais. A central de segurança também fornece um sistema de reputação.

### <a name="logging-and-auditing"></a>Registo e auditoria

Os serviços do Azure registram extensivamente a atividade do sistema e do usuário, bem como a integridade do sistema:
- **Logs de atividade**: [Os logs de atividade](../../azure-monitor/platform/activity-logs-overview.md) fornecem informações sobre as operações executadas nos recursos em uma assinatura. Os logs de atividades podem ajudar a determinar o iniciador de uma operação, a hora de ocorrência e o status.
- **Logs de diagnóstico**: Os [logs de diagnóstico](../../azure-monitor/platform/resource-logs-overview.md) incluem todos os logs emitidos por cada recurso. Esses logs incluem logs do sistema de eventos do Windows, logs de armazenamento, Key Vault logs de auditoria e acesso do gateway de aplicativo e logs de firewall. Todos os logs de diagnóstico gravam em uma conta de armazenamento do Azure centralizada e criptografada para arquivamento. Os usuários podem configurar o período de retenção, até 730 dias, para atender a seus requisitos específicos.

**Logs de Azure monitor**: Os logs são consolidados nos [logs de Azure monitor](https://azure.microsoft.com/services/log-analytics/) para processamento, armazenamento e relatórios de painel. Depois que os dados são coletados, eles são organizados em tabelas separadas para cada tipo de dados dentro dos espaços de trabalho Log Analytics. Dessa forma, todos os dados podem ser analisados juntos, independentemente de sua fonte original. A central de segurança se integra aos logs de Azure Monitor. Os clientes podem usar consultas Kusto para acessar seus dados de eventos de segurança e combiná-los com dados de outros serviços.

As seguintes [soluções de monitoramento](../../monitoring/monitoring-solutions.md) do Azure estão incluídas como parte dessa arquitetura:
-   [Avaliação de Active Directory](../../azure-monitor/insights/ad-assessment.md): A solução de verificação de integridade Active Directory avalia o risco e a integridade de ambientes de servidor em intervalos regulares. Ele fornece uma lista priorizada de recomendações específicas para a infraestrutura de servidor implantada.
- [Avaliação do SQL](../../azure-monitor/insights/sql-assessment.md): A solução de verificação da integridade do SQL avalia o risco e a integridade de ambientes de servidor em intervalos regulares. Ele fornece aos clientes uma lista priorizada de recomendações específicas para a infraestrutura de servidor implantada.
- [Integridade do agente](../../monitoring/monitoring-solution-agenthealth.md): A solução Integridade do Agente relata Quantos agentes são implantados e sua distribuição geográfica. Ele também relata Quantos agentes não respondem e o número de agentes que enviam dados operacionais.
-   [Análise do log de atividades](../../azure-monitor/platform/collect-activity-logs.md): A solução Análise do Log de Atividades auxilia na análise dos logs de atividade do Azure em todas as assinaturas do Azure para um cliente.

**Automação do Azure**: A [automação](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) armazena, executa e gerencia runbooks. Nesta solução, os runbooks ajudam a coletar logs do banco de dados SQL. Os clientes podem usar a solução de [controle de alterações](../../automation/change-tracking.md) de automação para identificar facilmente as alterações no ambiente.

**Azure monitor**: O [Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) ajuda os usuários a controlar o desempenho, manter a segurança e identificar tendências. As organizações podem usá-lo para auditar, criar alertas e arquivar dados. Eles também podem acompanhar chamadas de API em seus recursos do Azure.

**Application insights**: O [Application insights](../../azure-monitor/app/app-insights-overview.md) é um serviço de gerenciamento de desempenho de aplicativos extensível para desenvolvedores da Web em várias plataformas. Application Insights detecta anomalias de desempenho. Os clientes podem usá-lo para monitorar o aplicativo Web em tempo real. O Application Insights inclui ferramentas de análise poderosas para ajudar os clientes a diagnosticar problemas e entender o que os usuários fazem com seus aplicativos. Ele&#39;foi projetado para ajudar os clientes a melhorar continuamente o desempenho e a usabilidade.

## <a name="threat-model"></a>Modelo de ameaça

O diagrama de fluxo de dados para esta arquitetura de referência está disponível para [Download](https://aka.ms/nist171-paaswa-tm) ou pode ser encontrado aqui. Esse modelo pode ajudar os clientes a entender os pontos de risco potencial na infraestrutura do sistema quando fazem modificações.

![Aplicativo Web PaaS para o modelo de ameaça NIST SP 800-171](images/nist171-paaswa-threat-model.png "Aplicativo Web PaaS para o modelo de ameaça NIST SP 800-171")

## <a name="compliance-documentation"></a>Documentação de conformidade
A [matriz de responsabilidade do cliente do Blueprint de segurança E conformidade do Azure NIST SP 800-171](https://aka.ms/nist171-crm) lista todos os controles de segurança exigidos pelo NIST SP 800-171. Essa matriz detalha se a implementação de cada controle é de responsabilidade da Microsoft, do cliente ou do compartilhamento entre os dois.

A [matriz de implementação de controle de aplicativo Web de PaaS do Blueprint de segurança e conformidade do Azure-NIST sp 800-171](https://aka.ms/nist171-paaswa-cim) fornece informações sobre quais controles do NIST SP 800-171 são tratados pela arquitetura do aplicativo Web PaaS. Ele inclui descrições detalhadas de como a implementação atende aos requisitos de cada controle abordado.

## <a name="guidance-and-recommendations"></a>Diretrizes e recomendações
### <a name="vpn-and-expressroute"></a>VPN e ExpressRoute
Um túnel VPN seguro ou o [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) deve ser configurado para estabelecer com segurança uma conexão com os recursos implantados como parte dessa arquitetura de referência do aplicativo Web PaaS. Ao configurar adequadamente uma VPN ou ExpressRoute, os clientes podem adicionar uma camada de proteção para os dados em trânsito.

Ao implementar um túnel VPN seguro com o Azure, uma conexão privada virtual entre uma rede local e uma rede virtual do Azure pode ser criada. Essa conexão ocorre pela Internet e permite aos clientes "encapsular" informações com segurança dentro de um link criptografado entre a rede do cliente e o Azure. A VPN site a site é uma tecnologia segura e madura, que foi implantada por empresas de todos os tamanhos por décadas. O [modo de encapsulamento IPSec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) é usado nessa opção como um mecanismo de criptografia.

Como o tráfego no túnel VPN atravessa a Internet com uma VPN site a site, a Microsoft oferece outra opção de conexão ainda mais segura. O ExpressRoute é um link WAN dedicado entre o Azure e um local ou um provedor de hospedagem do Exchange. As conexões do ExpressRoute se conectam diretamente ao provedor de telecomunicação do cliente. Como resultado, os dados não viajam pela Internet e não são expostos a ele. Essas conexões oferecem mais confiabilidade, velocidades mais rápidas, latências menores e maior segurança do que as conexões típicas.

As práticas recomendadas para implementar uma rede híbrida segura que estende uma rede local para o Azure estão [disponíveis](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## <a name="disclaimer"></a>Exclusão de Responsabilidade

 - Este documento é apenas para fins informativos. A MICROSOFT NÃO OFERECE NENHUMA GARANTIA, EXPRESSA, IMPLÍCITA OU ESTATUTÁRIA, QUANTO ÀS INFORMAÇÕES CONTIDAS NESTE DOCUMENTO. Este documento é fornecido "no estado em que se encontra". As informações e as exibições expressas neste documento, incluindo URLs e outras referências de site da Internet, podem ser alteradas sem aviso prévio. Os clientes que lêem este documento trazem o risco de usá-lo.
 - Este documento não fornece aos clientes nenhum direito legal sobre qualquer propriedade intelectual em qualquer produto ou solução da Microsoft.
 - Os clientes podem copiar e usar este documento para fins de referência interna.
 - Determinadas recomendações neste documento podem resultar em maior uso de recursos de computação, rede ou dados no Azure, e podem aumentar os custos de assinatura ou de licença do Azure do cliente.
 - Essa arquitetura destina-se a servir como base para os clientes se ajustarem aos requisitos específicos e não devem ser usados no estado em que se encontram em um ambiente de produção.
 - Este documento é desenvolvido como uma referência e não deve ser usado para definir todos os meios pelos quais um cliente pode atender a requisitos e regulamentos específicos de conformidade. Os clientes devem buscar o suporte legal de sua organização em implementações aprovadas do cliente.
