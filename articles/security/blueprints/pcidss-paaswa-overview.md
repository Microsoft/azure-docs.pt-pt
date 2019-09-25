---
title: Aplicativo Web Blueprint de Segurança e Conformidade do Azure-PaaS para PCI DSS
description: Aplicativo Web Blueprint de Segurança e Conformidade do Azure-PaaS para PCI DSS
services: security
author: meladie
ms.assetid: 5ef64374-7b4e-4176-afe1-0724072f653c
ms.service: security
ms.topic: article
ms.date: 07/03/2018
ms.author: meladie
ms.openlocfilehash: 45c3e5da6cff51e14d22ed3a6a3ab526b96b50ef
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71259658"
---
# <a name="azure-security-and-compliance-blueprint-paas-web-application-for-pci-dss"></a>Esquema de Segurança e Conformidade do Azure: Aplicativo Web de PaaS para PCI DSS

## <a name="overview"></a>Descrição geral

Esta Blueprint de Segurança e Conformidade do Azure automação fornece diretrizes para a implantação de um ambiente de PaaS (plataforma como serviço) compatível com o setor de cartão de pagamento (PCI DSS 3,2) adequado para a coleta, o armazenamento e a recuperação de dados do titular do cartão. Essa solução automatiza a implantação e a configuração de recursos do Azure para uma arquitetura de referência comum, demonstrando maneiras nas quais os clientes podem atender aos requisitos específicos de segurança e conformidade e serve como base para os clientes criarem e configure suas próprias soluções no Azure. A solução implementa um subconjunto de requisitos do PCI DSS 3,2. Para obter mais informações sobre os requisitos do PCI DSS 3,2 e essa solução, consulte a seção [documentação de conformidade](#compliance-documentation) .

Essa Blueprint de Segurança e Conformidade do Azure automação implanta automaticamente uma arquitetura de referência de aplicativo Web de PaaS com controles de segurança pré-configurados para ajudar os clientes a obter conformidade com os requisitos de PCI DSS 3,2. A solução consiste em modelos de Azure Resource Manager e scripts do PowerShell que guiam a implantação e a configuração de recursos.

Essa arquitetura destina-se a servir como base para os clientes se ajustarem aos requisitos específicos e não devem ser usados no estado em que se encontram em um ambiente de produção. Implantar um aplicativo nesse ambiente sem modificação não é suficiente para atender completamente aos requisitos do PCI DSS 3,2. Tenha em atenção o seguinte:
- Essa arquitetura fornece uma linha de base para ajudar os clientes a usar o Azure de uma maneira compatível com o PCI DSS 3,2.
- Os clientes são responsáveis por realizar a avaliação apropriada de segurança e conformidade de qualquer solução criada usando essa arquitetura, pois os requisitos podem variar com base nas especificidades da implementação de cada cliente.

A obtenção de PCI DSS-conformidade exige que um QSA (Assessornt Security) qualificado certificada de uma solução de cliente de produção. Os clientes são responsáveis por realizar avaliações apropriadas de segurança e conformidade de qualquer solução criada usando essa arquitetura, já que os requisitos podem variar com base nas especificidades da implementação de cada cliente.

Clique [aqui](https://aka.ms/pcidss-paaswa-repo) para obter instruções de implantação.

## <a name="architecture-diagram-and-components"></a>Diagrama e componentes da arquitetura

Essa Blueprint de Segurança e Conformidade do Azure automação implanta uma arquitetura de referência para um aplicativo Web de PaaS com um back-end do banco de dados SQL do Azure. O aplicativo Web é hospedado em um Ambiente do Serviço de Aplicativo do Azure isolado, que é um ambiente privado dedicado em um datacenter do Azure. A carga do ambiente equilibra o tráfego para o aplicativo Web em máquinas virtuais gerenciadas pelo Azure. Essa arquitetura também inclui grupos de segurança de rede, um gateway de aplicativo, um DNS do Azure e um Load Balancer.

Para análise e relatórios aprimorados, os bancos de dados SQL do Azure podem ser configurados com índices columnstore. Os bancos de dados SQL do Azure podem ser escalados verticalmente ou desligados completamente em resposta ao uso do cliente. Todo o tráfego do SQL é criptografado com SSL por meio da inclusão de certificados autoassinados. Como prática recomendada, o Azure recomenda o uso de uma autoridade de certificação confiável para aumentar a segurança.

A solução usa contas de armazenamento do Azure, que os clientes podem configurar para usar o Criptografia do Serviço de Armazenamento para manter a confidencialidade dos dados em repouso. O Azure armazena três cópias de dados no datacenter selecionado de um cliente para fins de resiliência. O armazenamento com redundância geográfica garante que os dados serão replicados em um datacenter secundário a centenas de quilômetros de distância e armazenados novamente como três cópias dentro desse datacenter, impedindo que um evento adverso na data center primária do cliente resulte em uma perda de dado.

Para aumentar a segurança, todos os recursos nesta solução são gerenciados como um grupo de recursos por meio de Azure Resource Manager. Azure Active Directory controle de acesso baseado em função é usado para controlar o acesso a recursos implantados, incluindo suas chaves no Azure Key Vault. A integridade do sistema é monitorada por meio de Azure Monitor. Os clientes configuram ambos os serviços de monitoramento para capturar logs e exibir a integridade do sistema em um único painel fácil e navegável.

O banco de dados SQL do Azure é normalmente gerenciado por meio de SQL Server Management Studio, que é executado de um computador local configurado para acessar o banco de dados SQL do Azure por meio de uma conexão VPN ou ExpressRoute segura.

Além disso, o Application Insights fornece análise e gerenciamento de desempenho de aplicativos em tempo real por meio de logs de Azure Monitor. **A Microsoft recomenda configurar uma conexão VPN ou ExpressRoute para gerenciamento e importação de dados na sub-rede da arquitetura de referência.**

![Aplicativo Web de PaaS para PCI DSS diagrama de arquitetura de referência](images/pcidss-paaswa-architecture.png "Aplicativo Web de PaaS para PCI DSS diagrama de arquitetura de referência")

Essa solução usa os seguintes serviços do Azure. Os detalhes da arquitetura de implantação estão na seção [arquitetura de implantação](#deployment-architecture) .

- Ambiente do Serviço de Aplicativo v2
- Gateway de Aplicação
  - (1) Firewall do aplicativo Web
    - Modo de firewall: prevenção
    - Conjunto de regras: OWASP 3.0
    - Porta do ouvinte: 443
- Application Insights
- Azure Active Directory
- Automatização do Azure
- DNS do Azure
- Azure Key Vault
- Balanceador de Carga do Azure
- Azure Monitor
- Azure Resource Manager
- Centro de Segurança do Azure
- Base de Dados SQL do Azure
- Storage do Azure
- Rede Virtual do Azure
    - (1)/16 rede
    - (4)/24 redes
    - (4) grupos de segurança de rede
- Aplicação Web do Azure

## <a name="deployment-architecture"></a>Arquitetura de implantação

A seção a seguir detalha os elementos de implantação e implementação.

**Azure Resource Manager**: [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) permite que os clientes trabalhem com os recursos da solução como um grupo. Os clientes podem implantar, atualizar ou excluir todos os recursos da solução em uma única operação coordenada. Os clientes usam um modelo para implantação e esse modelo pode funcionar para ambientes diferentes, como teste, preparo e produção. O Gerenciador de recursos fornece recursos de segurança, auditoria e marcação para ajudar os clientes a gerenciar seus recursos após a implantação.

**Host bastião**: O host de bastiões é o ponto único de entrada que permite aos usuários acessar os recursos implantados nesse ambiente. O host de bastiões fornece uma conexão segura com os recursos implantados permitindo apenas o tráfego remoto de endereços IP públicos em uma lista segura. Para permitir o tráfego da área de trabalho remota (RDP), a origem do tráfego precisa ser definida no grupo de segurança de rede.

Essa solução cria uma máquina virtual como um host de bastiões ingressado no domínio com as seguintes configurações:
-   [Extensão de antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware)
-   [Extensão de Diagnóstico do Azure](../../virtual-machines/windows/extensions-diagnostics-template.md)
-   [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) usando Azure Key Vault
-   Uma [política de desligamento automático](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) para reduzir o consumo de recursos de máquina virtual quando não estiver em uso
-   O [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) habilitado para que as credenciais e outros segredos sejam executados em um ambiente protegido isolado do sistema operacional em execução

**Ambiente do serviço de aplicativo v2**: O Ambiente do Serviço de Aplicativo do Azure é um recurso do serviço de aplicativo que fornece um ambiente totalmente isolado e dedicado para a execução segura de aplicativos do serviço de aplicativo em alta escala. Esse recurso de isolamento é necessário para atender aos requisitos de conformidade de PCI.

Os ambientes do serviço de aplicativo são isolados para executar apenas aplicativos de um único cliente e são sempre implantados em uma rede virtual. Esse recurso de isolamento permite que a arquitetura de referência tenha isolamento de locatário completo, removendo-o do ambiente multilocatário do Azure proibindo esses multilocatários de enumerar os recursos de Ambiente do Serviço de Aplicativo implantados. Os clientes têm controle refinado sobre o tráfego de rede do aplicativo de entrada e saída, e os aplicativos podem estabelecer conexões seguras de alta velocidade em redes virtuais para recursos corporativos locais. Os clientes podem "dimensionar automaticamente" com Ambiente do Serviço de Aplicativo com base nas métricas de carga, no orçamento disponível ou em um agendamento definido.

Utilize ambientes de serviço de aplicativo para os seguintes controles/configurações:

- Hospedar dentro de uma rede virtual do Azure protegida e regras de segurança de rede
- Certificado ILB autoassinado para comunicação HTTPS
- [Modo de balanceamento de carga interno](../../app-service/environment/app-service-environment-with-internal-load-balancer.md)
- Desabilitar [TLS 1,0](../../app-service/environment/app-service-app-service-environment-custom-settings.md)
- Alterar a [codificação TLS](../../app-service/environment/app-service-app-service-environment-custom-settings.md)
- Controlar [portas N/W de tráfego de entrada](../../app-service/environment/app-service-app-service-environment-control-inbound-traffic.md)
- [Firewall do aplicativo Web – restringir dados](../../app-service/environment/app-service-app-service-environment-web-application-firewall.md)
- Permitir [tráfego do banco de dados SQL do Azure](../../app-service/environment/app-service-app-service-environment-network-architecture-overview.md)

**Aplicativo Web do Azure**: [Azure app serviço](https://docs.microsoft.com/azure/app-service/) permite que os clientes criem e hospedem aplicativos Web na linguagem de programação de sua escolha sem gerenciar a infraestrutura. Este serviço garante dimensionamento automático e elevada disponibilidade, suporta Windows e Linux e permite implementações automáticas a partir do GitHub, DevOps do Azure ou de qualquer repositório de Git.

### <a name="virtual-network"></a>Rede virtual

A arquitetura define uma rede virtual privada com um espaço de endereço de 10.200.0.0/16.

**Grupos de segurança de rede**: Os [grupos de segurança de rede](../../virtual-network/virtual-network-vnet-plan-design-arm.md) contêm ACLs (listas de controle de acesso) que permitem ou negam o tráfego em uma rede virtual. Os grupos de segurança de rede podem ser usados para proteger o tráfego em uma sub-rede ou nível de VM individual. Existem os seguintes grupos de segurança de rede:

- 1 grupo de segurança de rede para o gateway de aplicativo
- 1 grupo de segurança de rede para Ambiente do Serviço de Aplicativo
- 1 grupo de segurança de rede para o banco de dados SQL do Azure
- 1 grupo de segurança de rede para host bastião

Cada um dos grupos de segurança de rede tem portas e protocolos específicos abertos para que a solução possa funcionar de forma segura e correta. Além disso, as seguintes configurações estão habilitadas para cada grupo de segurança de rede:

- [Os logs e eventos de diagnóstico](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) são habilitados e armazenados em uma conta de armazenamento
- Os logs de Azure Monitor estão conectados ao [diagnóstico de&#39;s do grupo de segurança de rede](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Sub-redes**: Cada sub-rede é associada ao seu grupo de segurança de rede correspondente.

**DNS do Azure**: O sistema de nomes de domínio, ou DNS, é responsável por converter (ou resolver) um site ou nome de serviço para seu endereço IP. O [DNS do Azure](https://docs.microsoft.com/azure/dns/dns-overview) é um serviço de hospedagem para domínios DNS que fornece resolução de nomes usando a infraestrutura do Azure. Ao hospedar domínios no Azure, os usuários podem gerenciar registros DNS usando as mesmas credenciais, APIs, ferramentas e cobrança que outros serviços do Azure. O DNS do Azure também dá suporte a domínios DNS privados.

**Azure Load Balancer**: [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) permite que os clientes dimensionem seus aplicativos e criem alta disponibilidade para os serviços. O Load Balancer dá suporte a cenários de entrada e saída e fornece baixa latência, alta taxa de transferência e escala verticalmente para milhões de fluxos para todos os aplicativos TCP e UDP.

### <a name="data-in-transit"></a>Dados em trânsito

Por padrão, o Azure criptografa todas as comunicações entre os data centers do Azure. Todas as transações para o armazenamento do Azure por meio do portal do Azure ocorrem via HTTPS.

### <a name="data-at-rest"></a>Dados inativos

A arquitetura protege os dados em repouso por meio de criptografia, auditoria de banco e outras medidas.

**Armazenamento do Azure**: Para atender aos requisitos de dados criptografados em repouso, todo o [armazenamento do Azure](https://azure.microsoft.com/services/storage/) usa [criptografia do serviço de armazenamento](../../storage/common/storage-service-encryption.md). Isso ajuda a proteger e proteger os dados do titular do cartão para dar suporte a compromissos de segurança organizacional e requisitos de conformidade definidos pelo PCI DSS 3,2.

**Azure Disk Encryption**: [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) aproveita o recurso BitLocker do Windows para fornecer criptografia de volume para discos de dados. A solução se integra com Azure Key Vault para ajudar a controlar e gerenciar as chaves de criptografia de disco.

**Banco de dados SQL do Azure**: A instância do banco de dados SQL do Azure usa as seguintes medidas de segurança de banco de dados:

- [Active Directory autenticação e autorização](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) permitem o gerenciamento de identidades de usuários de banco de dados e outros serviços da Microsoft em um local central.
- A [auditoria do banco de dados SQL](../../sql-database/sql-database-auditing.md) rastreia eventos de banco de dados e os grava em um log de auditoria em uma conta de armazenamento do Azure.
- O banco de dados SQL do Azure está configurado para usar a [Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), que executa criptografia e descriptografia em tempo real do banco dados, backups associados e arquivos de log de transações para proteger informações em repouso. A Transparent Data Encryption fornece garantia de que os dados armazenados não estão sujeitos a acesso não autorizado.
- [As regras de firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) impedem todo o acesso a servidores de banco de dados até que sejam concedidas permissões adequadas A firewall concede acesso a bases de dados com base no endereço IP de origem de cada pedido.
- A [detecção de ameaças do SQL](../../sql-database/sql-database-threat-detection.md) permite a detecção e a resposta a ameaças potenciais à medida que elas ocorrem, fornecendo alertas de segurança para atividades suspeitas de banco de dados, vulnerabilidades potenciais, ataques de injeção de SQL e padrões de acesso de banco de dados anormais
- As [colunas criptografadas](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) garantem que os dados confidenciais nunca apareçam como texto sem formatação no sistema de banco de dados. Depois de habilitar a criptografia de dados, somente aplicativos cliente ou servidores de aplicativos com acesso às chaves podem acessar dados de texto sem formatação.
- O [mascaramento de dados dinâmicos do SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) limita a exposição de dados confidenciais mascarando os dados para usuários ou aplicativos sem privilégios. A máscara de dados dinâmicos pode descobrir dados potencialmente confidenciais automaticamente e sugerir que as máscaras apropriadas sejam aplicadas. Isso ajuda a identificar e reduzir o acesso a dados de modo que ele não saia do banco por acesso não autorizado. Os clientes são responsáveis por ajustar as configurações de mascaramento de dados dinâmicos para aderir ao seu esquema de banco de dado.

### <a name="identity-management"></a>Gestão de identidades

As tecnologias a seguir fornecem recursos para gerenciar o acesso aos dados do titular do cartão no ambiente do Azure:

- [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) é o&#39;serviço de gerenciamento de identidade e diretório multilocatário baseado em nuvem da Microsoft. Todos os usuários desta solução são criados no Azure Active Directory, incluindo usuários que acessam o banco de dados SQL do Azure.
- A autenticação para o aplicativo é executada usando Azure Active Directory. Para obter mais informações, consulte [integrando aplicativos com Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). Além disso, a criptografia de coluna de banco de dados usa Azure Active Directory para autenticar o aplicativo no banco de dados SQL do Azure. Para obter mais informações, Confira como [proteger dados confidenciais no Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
- O [controle de acesso baseado em função do Azure](../../role-based-access-control/role-assignments-portal.md) permite que os administradores definam permissões de acesso refinadas para conceder apenas a quantidade de acesso que os usuários precisam para executar seus trabalhos. Em vez de fornecer a cada usuário permissão irrestrita para recursos do Azure, os administradores podem permitir apenas determinadas ações para acessar dados de titulares de cartão. O acesso à assinatura é limitado ao administrador da assinatura.
- [Azure Active Directory Privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md) permite que os clientes minimizem o número de usuários que têm acesso a determinadas informações, como dados de titulares de cartão. Os administradores podem usar Azure Active Directory Privileged Identity Management para descobrir, restringir e monitorar identidades com privilégios e seu acesso aos recursos. Essa funcionalidade também pode ser usada para impor acesso administrativo sob demanda e Just-in-time quando necessário.
- [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview.md) detecta possíveis vulnerabilidades que afetam as&#39;identidades de uma organização, configura as respostas automatizadas para detectar ações suspeitas&#39;relacionadas a identidades de uma organização e investiga incidentes suspeitos para tomar as medidas apropriadas para resolvê-los.

### <a name="security"></a>Segurança

**Gerenciamento de segredos**: A solução usa [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) para o gerenciamento de chaves e segredos. O Cofre de Chaves do Azure ajuda a salvaguardar as chaves criptográficas e os segredos utilizados pelas aplicações em cloud e pelos serviços. Os recursos de Azure Key Vault a seguir ajudam os clientes a proteger e acessar esses dados:

- As políticas de acesso avançadas são configuradas de acordo com a necessidade.
- Key Vault políticas de acesso são definidas com as permissões mínimas necessárias para chaves e segredos.
- Todas as chaves e segredos em Key Vault têm datas de expiração.
- Todas as chaves em Key Vault são protegidas por módulos de segurança de hardware especializados. O tipo de chave é uma chave RSA de 2048 bits protegida por HSM.
- Todos os usuários e identidades recebem as permissões mínimas necessárias usando o controle de acesso baseado em função.
- Os logs de diagnóstico para Key Vault estão habilitados com um período de retenção de pelo menos 365 dias.
- As operações de criptografia permitidas para chaves são restritas às necessárias.

**Central de segurança do Azure**: Com a [central de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro), os clientes podem aplicar e gerenciar centralmente políticas de segurança entre cargas de trabalho, limitar a exposição a ameaças e detectar e responder a ataques. Além disso, a central de segurança do Azure acessa as configurações existentes dos serviços do Azure para fornecer recomendações de configuração e de serviço para ajudar a melhorar a postura de segurança e proteger os dados.

A central de segurança do Azure usa uma variedade de recursos de detecção para alertar os clientes sobre possíveis ataques visando seus ambientes. Estes alertas contêm informações valiosas sobre o que acionou o alerta, os recursos afetados e a origem do ataque. A central de segurança do Azure tem um conjunto de [alertas de segurança predefinidos](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), que são disparados quando ocorre uma ameaça ou atividade suspeita. [As regras de alerta personalizadas](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) na central de segurança do Azure permitem que os clientes definam novos alertas de segurança com base nos dados que já foram coletados de seu ambiente.

A central de segurança do Azure fornece alertas e incidentes de segurança priorizados, tornando mais simples para os clientes descobrir e resolver problemas potenciais de segurança. Um [relatório de inteligência contra ameaças](https://docs.microsoft.com/azure/security-center/security-center-threat-report) é gerado para cada ameaça detectada para ajudar as equipes de resposta a incidentes na investigação e na correção de ameaças.

**Gateway de aplicativo Azure**: A arquitetura reduz o risco de vulnerabilidades de segurança usando um gateway de Aplicativo Azure com um firewall de aplicativo Web configurado e o conjunto de regras OWASP habilitado. Os recursos adicionais incluem:

- [SSL de ponta a ponta](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Habilitar [descarregamento SSL](../../application-gateway/create-ssl-portal.md)
- Desabilitar [TLS v 1.0 e v 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Firewall do aplicativo Web](../../application-gateway/waf-overview.md) (modo de prevenção)
- [Modo de prevenção](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) com conjunto de regras OWASP 3,0
- Habilitar [log de diagnóstico](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)
- [Investigações de integridade personalizadas](../../application-gateway/quick-create-portal.md)
- A [central de segurança do Azure](https://azure.microsoft.com/services/security-center) e o [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) fornecem proteção e notificações adicionais. A central de segurança do Azure também fornece um sistema de reputação.

### <a name="logging-and-auditing"></a>Registo e auditoria

Os serviços do Azure registram extensivamente a atividade do sistema e do usuário, bem como a integridade do sistema:
- **Logs de atividade**: [Os logs de atividade](../../azure-monitor/platform/activity-logs-overview.md) fornecem informações sobre as operações executadas nos recursos em uma assinatura. Os logs de atividades podem ajudar a determinar o iniciador de uma operação, a hora de ocorrência e o status.
- **Logs de diagnóstico**: Os [logs de diagnóstico](../../azure-monitor/platform/resource-logs-overview.md) incluem todos os logs emitidos por cada recurso. Esses logs incluem logs do sistema de eventos do Windows, logs de armazenamento do Azure, Key Vault logs de auditoria e acesso do gateway de aplicativo e logs de firewall. Todos os logs de diagnóstico gravam em uma conta de armazenamento do Azure centralizada e criptografada para arquivamento. A retenção é configurável pelo usuário, até 730 dias, para atender aos requisitos de retenção específicos da organização.

**Logs de Azure monitor**: Esses logs são consolidados em [logs de Azure monitor](https://azure.microsoft.com/services/log-analytics/) para processamento, armazenamento e relatórios de Dashboard. Depois de coletados, os dados são organizados em tabelas separadas para cada tipo de dados dentro de Log Analytics espaços de trabalho, o que permite que todos os dados sejam analisados juntos, independentemente de sua fonte original. Além disso, a central de segurança do Azure integra-se aos logs de Azure Monitor, permitindo que os clientes usem consultas Kusto para acessar seus dados de eventos de segurança e combiná-los com dados de outros serviços.

As seguintes [soluções de monitoramento](../../monitoring/monitoring-solutions.md) do Azure estão incluídas como parte dessa arquitetura:
-   [Avaliação do Active Directory](../../azure-monitor/insights/ad-assessment.md): A solução de verificação de integridade Active Directory avalia o risco e a integridade dos ambientes de servidor em um intervalo regular e fornece uma lista priorizada de recomendações específicas para a infraestrutura de servidor implantada.
- [Avaliação do SQL](../../azure-monitor/insights/sql-assessment.md): A solução de verificação de integridade do SQL avalia o risco e a integridade dos ambientes de servidor em um intervalo regular e fornece aos clientes uma lista priorizada de recomendações específicas para a infraestrutura de servidor implantada.
- [Integridade do agente](../../monitoring/monitoring-solution-agenthealth.md): A solução Integridade do Agente relata Quantos agentes são implantados e sua distribuição geográfica, bem como Quantos agentes que não respondem e o número de agentes que estão enviando dados operacionais.
-   [Análise do log de atividades](../../azure-monitor/platform/collect-activity-logs.md): A solução Análise do Log de Atividades auxilia na análise dos logs de atividade do Azure em todas as assinaturas do Azure para um cliente.

**Automação do Azure**: A [automação do Azure](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) armazena, executa e gerencia runbooks. Nesta solução, os runbooks ajudam a coletar logs do banco de dados SQL do Azure. A solução de [controle de alterações](../../automation/change-tracking.md) de automação permite que os clientes identifiquem facilmente as alterações no ambiente.

**Azure monitor**: [Azure monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) ajuda os usuários a controlar o desempenho, manter a segurança e identificar tendências, permitindo que as organizações façam auditoria, criem alertas e arquivem dados, incluindo o acompanhamento de chamadas de API em seus recursos do Azure.

**Application insights**: O [Application insights](../../azure-monitor/app/app-insights-overview.md) é um serviço de gerenciamento de desempenho de aplicativos extensível para desenvolvedores da Web em várias plataformas. Application Insights detecta anomalias de desempenho e os clientes podem usá-lo para monitorar o aplicativo Web em tempo real. Ele inclui ferramentas de análise poderosas para ajudar os clientes a diagnosticar problemas e entender o que os usuários realmente fazem com seus aplicativos. Ele&#39;foi projetado para ajudar os clientes a melhorar continuamente o desempenho e a usabilidade.

## <a name="threat-model"></a>Modelo de ameaça

O diagrama de fluxo de dados para esta arquitetura de referência está disponível para [Download](https://aka.ms/pcidss-paaswa-tm) ou pode ser encontrado abaixo. Esse modelo pode ajudar os clientes a entender os pontos de risco potencial na infraestrutura do sistema ao fazer modificações.

![Aplicativo Web de PaaS para PCI DSS modelo de ameaça](images/pcidss-paaswa-threat-model.png "Aplicativo Web de PaaS para PCI DSS modelo de ameaça")

## <a name="compliance-documentation"></a>Documentação de conformidade

O [Blueprint de segurança e conformidade do Azure – PCI DSS matriz de responsabilidade do cliente](https://aka.ms/pcidss-crm) lista as responsabilidades do controlador e do processador para todos os requisitos do PCI DSS 3,2.

A [matriz de implementação de aplicativo web Blueprint de segurança e conformidade do Azure – PCI DSS PaaS](https://aka.ms/pcidss-paaswa-cim) fornece informações sobre quais requisitos de PCI DSS 3,2 são tratados pela arquitetura do aplicativo Web PaaS, incluindo descrições detalhadas de como o a implementação atende aos requisitos de cada artigo abordado.

## <a name="deploy-this-solution"></a>Implantar esta solução
Essa Blueprint de Segurança e Conformidade do Azure automação é composta por arquivos de configuração JSON e scripts do PowerShell que são manipulados pelo serviço de API do Azure Resource Manager para implantar recursos no Azure. Instruções de implantação detalhadas estão disponíveis [aqui](https://aka.ms/pcidss-paaswa-repo).

#### <a name="quickstart"></a>Início Rápido
1. Clone ou baixe [este](https://aka.ms/pcidss-paaswa-repo) repositório GitHub em sua estação de trabalho local.

2. Examine o 0-Setup-AdministrativeAccountAndPermission.md e execute os comandos fornecidos.

3. Implante uma solução de teste com dados de exemplo da Contoso ou pilote um ambiente de produção inicial.
   - 1A-ContosoWebStoreDemoAzureResources.ps1
     - Esse script implanta recursos do Azure para uma demonstração de uma webstore usando dados de exemplo da contoso.
   - 1-DeployAndConfigureAzureResources.ps1
     - Esse script implanta os recursos do Azure necessários para dar suporte a um ambiente de produção para um aplicativo Web de Propriedade do cliente. Esse ambiente deve ser personalizado ainda mais pelo cliente com base nos requisitos organizacionais.

## <a name="guidance-and-recommendations"></a>Diretrizes e recomendações

### <a name="vpn-and-expressroute"></a>VPN e ExpressRoute

Um túnel VPN seguro ou o [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) precisa ser configurado para estabelecer com segurança uma conexão com os recursos implantados como parte dessa arquitetura de referência do aplicativo Web PaaS. Ao configurar adequadamente uma VPN ou ExpressRoute, os clientes podem adicionar uma camada de proteção para os dados em trânsito.

Ao implementar um túnel VPN seguro com o Azure, uma conexão privada virtual entre uma rede local e uma rede virtual do Azure pode ser criada. Essa conexão ocorre pela Internet e permite aos clientes &quot;encapsular&quot; informações com segurança dentro de um link criptografado entre a&#39;rede do cliente e o Azure. A VPN site a site é uma tecnologia segura e madura, que foi implantada por empresas de todos os tamanhos por décadas. O [modo de encapsulamento IPSec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) é usado nessa opção como um mecanismo de criptografia.

Como o tráfego dentro do túnel VPN atravessa a Internet com uma VPN site a site, a Microsoft oferece outra opção de conexão ainda mais segura. O Azure ExpressRoute é um link WAN dedicado entre o Azure e um local ou um provedor de hospedagem do Exchange. Como as conexões do ExpressRoute não passam pela Internet, essas conexões oferecem mais confiabilidade, velocidades mais rápidas, latências menores e mais segurança do que as conexões típicas pela Internet. Além disso, como essa é uma conexão direta do&#39;provedor de telecomunicação do cliente, os dados não viajam pela Internet e, portanto, não são expostos a ele.

As práticas recomendadas para implementar uma rede híbrida segura que estende uma rede local para o Azure estão [disponíveis](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## <a name="disclaimer"></a>Exclusão de Responsabilidade

- Este documento é apenas para fins informativos. A MICROSOFT NÃO OFERECE NENHUMA GARANTIA, EXPRESSA, IMPLÍCITA OU ESTATUTÁRIA, QUANTO ÀS INFORMAÇÕES CONTIDAS NESTE DOCUMENTO. Este documento é fornecido &quot;no estado em que se encontra.&quot; As informações e as exibições expressas neste documento, incluindo URLs e outras referências de site da Internet, podem ser alteradas sem aviso prévio. Os clientes que lêem este documento trazem o risco de usá-lo.
- Este documento não fornece aos clientes nenhum direito legal sobre qualquer propriedade intelectual em qualquer produto ou solução da Microsoft.
- Os clientes podem copiar e usar este documento para fins de referência interna.
- Determinadas recomendações neste documento podem resultar em maior uso de recursos de computação, rede ou dados no Azure e podem aumentar os custos de&#39;licença ou assinatura do cliente do Azure.
- Essa arquitetura destina-se a servir como base para os clientes se ajustarem aos requisitos específicos e não devem ser usados no estado em que se encontram em um ambiente de produção.
- Este documento é desenvolvido como uma referência e não deve ser usado para definir todos os meios pelos quais um cliente pode atender a requisitos e regulamentos específicos de conformidade. Os clientes devem buscar o suporte legal de sua organização em implementações aprovadas do cliente.
