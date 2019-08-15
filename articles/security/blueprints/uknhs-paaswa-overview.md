---
title: Aplicativo Web Blueprint de Segurança e Conformidade do Azure-PaaS para o Reino Unido NHS
description: Aplicativo Web Blueprint de Segurança e Conformidade do Azure-PaaS para o Reino Unido NHS
services: security
author: jomolesk
ms.assetid: fe409add-6d09-4062-b3c8-d74574747739
ms.service: security
ms.topic: article
ms.date: 06/15/2018
ms.author: jomolesk
ms.openlocfilehash: 5de89fc5300580d6e07d15858510bfd2a7be7db9
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/10/2019
ms.locfileid: "68946553"
---
# <a name="azure-security-and-compliance-blueprint-paas-web-application-for-uk-nhs"></a>Esquema de Segurança e Conformidade do Azure: Aplicativo Web PaaS para NHS do Reino Unido

## <a name="overview"></a>Descrição geral

Este Blueprint de Segurança e Conformidade do Azure fornece uma arquitetura de referência e uma orientação para uma solução de PaaS (plataforma como serviço) adequada para a coleta, o armazenamento e a recuperação de dados de saúde. Esta solução demonstra maneiras em que os clientes podem obedecer às diretrizes fornecidas no guia de práticas recomendadas de [segurança na nuvem](https://digital.nhs.uk/data-and-information/looking-after-information/data-security-and-information-governance/nhs-and-social-care-data-off-shoring-and-the-use-of-public-cloud-services/health-and-social-care-cloud-security-good-practice-guide) publicadas pelo [NHS digital](https://digital.nhs.uk/), um parceiro do departamento de saúde e do DHSC (setor de ti) do Reino Unido. O guia de práticas recomendadas de segurança na nuvem baseia-se nos 14 [princípios de segurança de nuvem](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) publicados pelo ncsc (National Cyber Cloud Security Centre) do Reino Unido.

Essa arquitetura de referência, guia de implementação e modelo de risco são destinados a servir como base para os clientes se ajustarem aos requisitos específicos e não devem ser usados no estado em que se encontram em um ambiente de produção sem configuração adicional. Os clientes são responsáveis por realizar avaliações apropriadas de segurança e conformidade de qualquer solução criada usando essa arquitetura, já que os requisitos podem variar com base nas especificidades da implementação de cada cliente.

## <a name="architecture-diagram-and-components"></a>Diagrama e componentes da arquitetura

Essa solução fornece uma arquitetura de referência para um aplicativo Web de PaaS com um back-end do banco de dados SQL do Azure. O aplicativo Web é hospedado em um Ambiente do Serviço de Aplicativo do Azure isolado, que é um ambiente privado dedicado em um datacenter do Azure. A carga do ambiente equilibra o tráfego para o aplicativo Web em máquinas virtuais gerenciadas pelo Azure. Todas as conexões externas exigem o TLSv 1.2. Essa arquitetura também inclui grupos de segurança de rede, um gateway de aplicativo, um DNS do Azure e um Load Balancer.

A solução usa contas de armazenamento do Azure, que os clientes podem configurar para usar o Criptografia do Serviço de Armazenamento para manter a confidencialidade dos dados em repouso. O Azure armazena três cópias de dados no datacenter selecionado de um cliente para fins de resiliência. O armazenamento com redundância geográfica garante que os dados serão replicados em um datacenter secundário a centenas de quilômetros de distância e armazenados novamente como três cópias dentro desse datacenter, impedindo que um evento adverso na data center primária do cliente resulte em uma perda de dado.

Para aumentar a segurança, todos os recursos nesta solução são gerenciados como um grupo de recursos por meio de Azure Resource Manager. Azure Active Directory controle de acesso baseado em função é usado para controlar o acesso a recursos e chaves implantados no Azure Key Vault. A integridade do sistema é monitorada por meio da central de segurança do Azure e Azure Monitor. Os clientes configuram ambos os serviços de monitoramento para capturar logs e exibir a integridade do sistema em um único painel fácil e navegável. Aplicativo Azure Gateway está configurado como um firewall no modo de prevenção e não permite o tráfego que não seja o TLSv 1.2. A solução utiliza Aplicativo Azure ambiente de serviço v2 para isolar a camada da Web em um ambiente sem vários locatários.

**A Microsoft recomenda configurar uma conexão VPN ou ExpressRoute para gerenciamento e importação de dados na sub-rede da arquitetura de referência.**

![Aplicativo Web PaaS para o diagrama da arquitetura de referência do NHS do Reino Unido](images/uknhs-paaswa-architecture.png?raw=true "Aplicativo Web PaaS para o diagrama da arquitetura de referência do NHS do Reino Unido")

Essa solução usa os seguintes serviços do Azure. Os detalhes da arquitetura de implantação estão na seção [arquitetura de implantação](#deployment-architecture) .

- Gateway de Aplicação
    - Firewall de aplicações Web
        - Modo de firewall: prevenção
        - Conjunto de regras: OWASP
        - Porta do ouvinte: 443
- Azure Active Directory
- Ambiente de serviço do Aplicativo Azure v2
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
    - Grupos de segurança de rede
- Aplicação Web do Azure

## <a name="deployment-architecture"></a>Arquitetura de implantação

A seção a seguir detalha os elementos de implantação e implementação.

**Azure Resource Manager**: [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) permite que os clientes trabalhem com os recursos da solução como um grupo. Os clientes podem implantar, atualizar ou excluir todos os recursos da solução em uma única operação coordenada. Os clientes usam um modelo para implantação e esse modelo pode funcionar para ambientes diferentes, como teste, preparo e produção. O Gerenciador de recursos fornece recursos de segurança, auditoria e marcação para ajudar os clientes a gerenciar seus recursos após a implantação.

**Ambiente do serviço de aplicativo v2**: O Ambiente do Serviço de Aplicativo do Azure é um recurso do serviço de aplicativo que fornece um ambiente totalmente isolado e dedicado para a execução segura de aplicativos do serviço de aplicativo em alta escala.

A Ambiente do Serviço de Aplicativo é isolada para executar apenas um único aplicativo e é sempre implantada em uma rede virtual. Esse recurso de isolamento permite que a arquitetura de referência tenha isolamento de locatário completo, removendo-o do ambiente multilocatário do Azure. Esse recurso de isolamento é necessário para atender aos requisitos do NHS princípio 3 do Reino Unido. Os clientes têm controle refinado sobre o tráfego de rede do aplicativo de entrada e saída, e os aplicativos podem estabelecer conexões seguras de alta velocidade em redes virtuais para recursos corporativos locais. Os clientes podem "dimensionar automaticamente" com Ambiente do Serviço de Aplicativo com base nas métricas de carga, no orçamento disponível ou em um agendamento definido.


O uso de Ambiente do Serviço de Aplicativo para essa arquitetura permite os seguintes controles/configurações:

- Hospedar dentro de uma rede virtual do Azure protegida e regras de segurança de rede
- Certificado de balanceador de carga interno autoassinado para comunicação HTTPS. Como prática recomendada, a Microsoft recomenda o uso de uma autoridade de certificação confiável para aumentar a segurança.
- [Modo de balanceamento de carga interno](../../app-service/environment/app-service-environment-with-internal-load-balancer.md)
- Desabilitar [TLS 1,0](../../app-service/environment/app-service-app-service-environment-custom-settings.md)
- Alterar a [codificação TLS](../../app-service/environment/app-service-app-service-environment-custom-settings.md)
- Controlar [portas N/W de tráfego de entrada](../../app-service/environment/app-service-app-service-environment-control-inbound-traffic.md)
- [Firewall do aplicativo Web – restringir dados](../../app-service/environment/app-service-app-service-environment-web-application-firewall.md)
- Permitir [tráfego do banco de dados SQL do Azure](../../app-service/environment/app-service-app-service-environment-network-architecture-overview.md)

**Aplicativo Web do Azure**: [Azure app serviço](https://docs.microsoft.com/azure/app-service/) permite que os clientes criem e hospedem aplicativos Web na linguagem de programação de sua escolha sem gerenciar a infraestrutura. Este serviço garante dimensionamento automático e elevada disponibilidade, suporta Windows e Linux e permite implementações automáticas a partir do GitHub, DevOps do Azure ou de qualquer repositório de Git.

### <a name="virtual-network"></a>Rede virtual

A arquitetura define uma rede virtual privada com um espaço de endereço de 10.200.0.0/16.

**Grupos de segurança de rede**: Os [grupos de segurança de rede](../../virtual-network/virtual-network-vnet-plan-design-arm.md) contêm listas de controle de acesso que permitem ou negam o tráfego em uma rede virtual. Os grupos de segurança de rede podem ser usados para proteger o tráfego em uma sub-rede ou em um nível de máquina virtual individual. Existem os seguintes grupos de segurança de rede:

- 1 grupo de segurança de rede para o gateway de aplicativo
- 1 grupo de segurança de rede para Ambiente do Serviço de Aplicativo
- 1 grupo de segurança de rede para o banco de dados SQL do Azure

Cada um dos grupos de segurança de rede tem portas e protocolos específicos abertos para que a solução possa funcionar de forma segura e correta. Além disso, as seguintes configurações estão habilitadas para cada grupo de segurança de rede:

- [Os logs e eventos de diagnóstico](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) são habilitados e armazenados em uma conta de armazenamento
- Os logs de Azure Monitor estão conectados aos [logs de&#39;diagnóstico s do grupo de segurança de rede](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Sub-redes**: Cada sub-rede é associada ao seu grupo de segurança de rede correspondente.

**DNS do Azure**: O sistema de nomes de domínio, ou DNS, é responsável por converter (ou resolver) um site ou nome de serviço para seu endereço IP. O [DNS do Azure](https://docs.microsoft.com/azure/dns/dns-overview) é um serviço de hospedagem para domínios DNS que fornece resolução de nomes usando a infraestrutura do Azure. Ao hospedar domínios no Azure, os usuários podem gerenciar registros DNS usando as mesmas credenciais, APIs, ferramentas e cobrança que outros serviços do Azure. O DNS do Azure também dá suporte a domínios DNS privados.

**Azure Load Balancer**: [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) permite que os clientes dimensionem seus aplicativos e criem alta disponibilidade para os serviços. O Load Balancer dá suporte a cenários de entrada e saída e fornece baixa latência, alta taxa de transferência e escala verticalmente para milhões de fluxos para todos os aplicativos TCP e UDP.

### <a name="data-in-transit"></a>Dados em trânsito

Por padrão, o Azure criptografa todas as comunicações entre os data centers do Azure. Todas as transações para o armazenamento do Azure por meio do portal do Azure ocorrem via HTTPS.

### <a name="data-at-rest"></a>Dados inativos

A arquitetura protege os dados em repouso por meio de criptografia, auditoria de banco e outras medidas.

**Armazenamento do Azure**: Para atender aos requisitos de dados criptografados em repouso, todo o [armazenamento do Azure](https://azure.microsoft.com/services/storage/) usa [criptografia do serviço de armazenamento](../../storage/common/storage-service-encryption.md). Isso ajuda a proteger e proteger os dados em suporte aos compromissos de segurança organizacional e aos requisitos de conformidade definidos pelo NHS digital.

**Azure Disk Encryption**: [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) aproveita o recurso BitLocker do Windows para fornecer criptografia de volume para discos de dados. A solução se integra com Azure Key Vault para ajudar a controlar e gerenciar as chaves de criptografia de disco.

**Banco de dados SQL do Azure**: A instância do banco de dados SQL do Azure usa as seguintes medidas de segurança de banco de dados:

- [Active Directory autenticação e autorização](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) permitem o gerenciamento de identidades de usuários de banco de dados e outros serviços da Microsoft em um local central.
- A [auditoria do banco de dados SQL](../../sql-database/sql-database-auditing.md) rastreia eventos de banco de dados e os grava em um log de auditoria em uma conta de armazenamento do Azure.
- O banco de dados SQL do Azure está configurado para usar a Transparent [Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), que executa criptografia e descriptografia em tempo real do banco dados, backups associados e arquivos de log de transações para proteger informações em repouso. A Transparent Data Encryption fornece garantia de que os dados armazenados não estão sujeitos a acesso não autorizado.
- [As regras de firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) impedem todo o acesso a servidores de banco de dados até que sejam concedidas permissões adequadas A firewall concede acesso a bases de dados com base no endereço IP de origem de cada pedido.
- A [detecção de ameaças do SQL](../../sql-database/sql-database-threat-detection.md) permite a detecção e a resposta a ameaças potenciais à medida que elas ocorrem, fornecendo alertas de segurança para atividades suspeitas de banco de dados, vulnerabilidades potenciais, ataques de injeção de SQL e padrões de acesso de banco de dados anormais
- As [colunas criptografadas](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) garantem que os dados confidenciais nunca apareçam como texto sem formatação no sistema de banco de dados. Depois de habilitar a criptografia de dados, somente aplicativos cliente ou servidores de aplicativos com acesso às chaves podem acessar dados de texto sem formatação.
- O mascaramento de [dados dinâmicos do SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) limita a exposição de dados confidenciais mascarando os dados para usuários ou aplicativos sem privilégios. A máscara de dados dinâmicos pode descobrir dados potencialmente confidenciais automaticamente e sugerir que as máscaras apropriadas sejam aplicadas. Isso ajuda a identificar e reduzir o acesso a dados de modo que ele não saia do banco por acesso não autorizado. Os clientes são responsáveis por ajustar as configurações de mascaramento de dados dinâmicos para aderir ao seu esquema de banco de dado.

### <a name="identity-management"></a>Gestão de identidades

As tecnologias a seguir fornecem recursos para gerenciar o acesso aos dados no ambiente do Azure:

- [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) é o&#39;serviço de gerenciamento de identidade e diretório multilocatário baseado em nuvem da Microsoft. Todos os usuários desta solução são criados no Azure Active Directory, incluindo usuários que acessam o banco de dados SQL do Azure.
- A autenticação para o aplicativo é executada usando Azure Active Directory. Para obter mais informações, consulte [integrando aplicativos com Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). Além disso, a criptografia de coluna de banco de dados usa Azure Active Directory para autenticar o aplicativo no banco de dados SQL do Azure. Para obter mais informações, Confira como [proteger dados confidenciais no Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
- O [controle de acesso baseado em função do Azure](../../role-based-access-control/role-assignments-portal.md) permite que os administradores definam permissões de acesso refinadas para conceder apenas a quantidade de acesso que os usuários precisam para executar seus trabalhos. Em vez de fornecer a cada usuário permissão irrestrita para recursos do Azure, os administradores podem permitir apenas determinadas ações para acessar dados. O acesso à assinatura é limitado ao administrador da assinatura.
- [Azure Active Directory Privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md) permite que os clientes minimizem o número de usuários que têm acesso a determinadas informações. Os administradores podem usar Azure Active Directory Privileged Identity Management para descobrir, restringir e monitorar identidades com privilégios e seu acesso aos recursos. Essa funcionalidade também pode ser usada para impor acesso administrativo sob demanda e Just-in-time quando necessário.
- [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview.md) detecta possíveis vulnerabilidades que afetam as&#39;identidades de uma organização, configura as respostas automatizadas para detectar ações suspeitas&#39;relacionadas a identidades de uma organização e investiga incidentes suspeitos para tomar as medidas apropriadas para resolvê-los.

### <a name="security"></a>Segurança

**Gerenciamento de segredos**: A solução usa [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) para o gerenciamento de chaves e segredos. O Cofre de Chaves do Azure ajuda a salvaguardar as chaves criptográficas e os segredos utilizados pelas aplicações em cloud e pelos serviços. Os recursos de Azure Key Vault a seguir ajudam os clientes a proteger e acessar esses dados:

- As políticas de acesso avançadas são configuradas de acordo com a necessidade.
- Key Vault políticas de acesso são definidas com as permissões mínimas necessárias para chaves e segredos.
- Todas as chaves e segredos em Key Vault têm datas de expiração.
- Todas as chaves em Key Vault são protegidas por módulos de segurança de hardware especializados. O tipo de chave é um módulo de segurança de hardware protegido chave RSA de 2048 bits.
- Todos os usuários e identidades recebem as permissões mínimas necessárias usando o controle de acesso baseado em função.
- Os logs de diagnóstico para Key Vault estão habilitados com um período de retenção de pelo menos 365 dias.
- As operações de criptografia permitidas para chaves são restritas às necessárias.

**Central de segurança do Azure**: Com a [central de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro), essa solução pode aplicar e gerenciar centralmente políticas de segurança entre cargas de trabalho, limitar a exposição a ameaças e detectar e responder a ataques. Além disso, a central de segurança do Azure acessa as configurações existentes dos serviços do Azure para fornecer recomendações de configuração e de serviço para ajudar a melhorar a postura de segurança e proteger os dados.

A central de segurança do Azure usa uma variedade de recursos de detecção para alertar os clientes sobre possíveis ataques visando seus ambientes. Estes alertas contêm informações valiosas sobre o que acionou o alerta, os recursos afetados e a origem do ataque. A central de segurança do Azure tem um conjunto de [alertas de segurança](https://docs.microsoft.com/azure/security-center/security-center-alerts-type)predefinidos, que são disparados quando ocorre uma ameaça ou atividade suspeita. [As regras de alerta personalizadas](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) na central de segurança do Azure permitem que os clientes definam novos alertas de segurança com base nos dados que já foram coletados de seu ambiente.

A central de segurança do Azure fornece incidentes e alertas de segurança priorizados, tornando mais simples descobrir e resolver problemas potenciais de segurança. Um [relatório de inteligência contra ameaças](https://docs.microsoft.com/azure/security-center/security-center-threat-report) é gerado para cada ameaça detectada para ajudar as equipes de resposta a incidentes na investigação e na correção de ameaças.

**Gateway de aplicativo Azure**: A arquitetura reduz o risco de vulnerabilidades de segurança usando um gateway de Aplicativo Azure com um firewall de aplicativo Web configurado e o conjunto de regras OWASP habilitado. Os recursos adicionais incluem:

- [SSL de ponta a ponta](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Habilitar [descarregamento SSL](../../application-gateway/create-ssl-portal.md)
- Desabilitar [TLS v 1.0 e v 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Firewall do aplicativo Web](../../application-gateway/waf-overview.md) (modo de prevenção)
- [Modo de prevenção](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) com conjunto de regras OWASP 3,0
- Habilitar [log de diagnóstico](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)
- [Investigações de integridade personalizadas](../../application-gateway/quick-create-portal.md)
- A [central de segurança do Azure](https://azure.microsoft.com/services/security-center) e o [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) fornecem proteção e notificações adicionais. A central de segurança do Azure também fornece um sistema de reputação.

### <a name="logging-and-auditing"></a>Registro em log e auditoria

Os serviços do Azure registram extensivamente a atividade do sistema e do usuário, bem como a integridade do sistema:
- **Logs de atividade**: [Os logs de atividade](../../azure-monitor/platform/activity-logs-overview.md) fornecem informações sobre as operações executadas nos recursos em uma assinatura. Os logs de atividades podem ajudar a determinar o iniciador de uma operação, a hora de ocorrência e o status.
- **Logs de diagnóstico**: Os [logs de diagnóstico](../../azure-monitor/platform/diagnostic-logs-overview.md) incluem todos os logs emitidos por cada recurso. Esses logs incluem logs do sistema de eventos do Windows, logs de armazenamento do Azure, Key Vault logs de auditoria e acesso do gateway de aplicativo e logs de firewall. Todos os logs de diagnóstico gravam em uma conta de armazenamento do Azure centralizada e criptografada para arquivamento. A retenção é configurável pelo usuário, até 730 dias, para atender aos requisitos de retenção específicos da organização.

**Logs de Azure monitor**: Esses logs são consolidados em [logs de Azure monitor](https://azure.microsoft.com/services/log-analytics/) para processamento, armazenamento e relatórios de Dashboard. Depois de recolhidos, os dados são organizados em tabelas separadas para cada tipo de dados, que permite que todos os dados sejam analisados em conjunto, independentemente da respetiva origem. Além disso, a central de segurança do Azure integra-se aos logs de Azure Monitor, permitindo que os clientes usem consultas Kusto para acessar seus dados de eventos de segurança e combiná-los com dados de outros serviços.

As seguintes [soluções de monitoramento](../../monitoring/monitoring-solutions.md) do Azure estão incluídas como parte dessa arquitetura:
-   [Avaliação do Active Directory](../../azure-monitor/insights/ad-assessment.md): A solução de verificação de integridade Active Directory avalia o risco e a integridade dos ambientes de servidor em um intervalo regular e fornece uma lista priorizada de recomendações específicas para a infraestrutura de servidor implantada.
- [Avaliação do SQL](../../azure-monitor/insights/sql-assessment.md): A solução de verificação de integridade do SQL avalia o risco e a integridade dos ambientes de servidor em um intervalo regular e fornece aos clientes uma lista priorizada de recomendações específicas para a infraestrutura de servidor implantada.
- [Integridade do agente](../../monitoring/monitoring-solution-agenthealth.md): A solução Integridade do Agente relata Quantos agentes são implantados e sua distribuição geográfica, bem como Quantos agentes que não respondem e o número de agentes que estão enviando dados operacionais.
-   [Análise do log de atividades](../../azure-monitor/platform/collect-activity-logs.md): A solução Análise do Log de Atividades auxilia na análise dos logs de atividade do Azure em todas as assinaturas do Azure para um cliente.

**Automação do Azure**: A [automação do Azure](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) armazena, executa e gerencia runbooks. Nesta solução, os runbooks ajudam a coletar logs do banco de dados SQL do Azure. A solução de [controle de alterações](../../automation/change-tracking.md) de automação permite que os clientes identifiquem facilmente as alterações no ambiente.

**Azure monitor**: [Azure monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) ajuda os usuários a controlar o desempenho, manter a segurança e identificar tendências, permitindo que as organizações façam auditoria, criem alertas e arquivem dados, incluindo o acompanhamento de chamadas de API em seus recursos do Azure.

## <a name="threat-model"></a>Modelo de ameaça

O diagrama de fluxo de dados para esta arquitetura de referência está disponível para [Download](https://aka.ms/uknhs-paaswa-tm) ou pode ser encontrado abaixo. Esse modelo pode ajudar os clientes a entender os pontos de risco potencial na infraestrutura do sistema ao fazer modificações.

![Aplicativo Web de PaaS para o modelo de ameaça do Reino Unido NHS](images/uknhs-paaswa-threat-model.png?raw=true "Aplicativo Web de PaaS para o modelo de ameaça do Reino Unido NHS")

## <a name="compliance-documentation"></a>Documentação de conformidade

A [matriz de responsabilidade do cliente do Blueprint de segurança E conformidade do Azure NHS – Reino Unido](https://aka.ms/uknhs-crm) lista todos os requisitos do NHS do Reino Unido. Essa matriz detalha se a implementação de cada princípio é a responsabilidade da Microsoft, do cliente ou do compartilhamento entre os dois.

A [matriz de implementação de aplicativo Web do Blueprint de segurança e conformidade do Azure – UK NHS PaaS](https://aka.ms/uknhs-paaswa-cim) fornece informações sobre quais requisitos do NHS do Reino Unido são tratados pela arquitetura do aplicativo Web de PaaS, incluindo descrições detalhadas de como o a implementação atende aos requisitos de cada princípio coberto.

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
