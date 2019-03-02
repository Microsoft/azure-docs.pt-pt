---
title: Segurança do Azure e o esquema de conformidade - aplicação Web de PaaS para SP do NIST 800-171
description: Segurança do Azure e o esquema de conformidade - PaaS Web-SP aplicação NIST 800-171
services: security
author: jomolesk
ms.assetid: eea21a0a-5930-43e8-937f-5419c20744c9
ms.service: security
ms.topic: article
ms.date: 07/31/2018
ms.author: jomolesk
ms.openlocfilehash: f9773c3b372ab22cbcd99828e147d23c185c4eb6
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57244626"
---
# <a name="azure-security-and-compliance-blueprint---paas-web-application-for-nist-special-publication-800-171"></a>Segurança do Azure e o esquema de conformidade - aplicação Web de PaaS para publicação especial do NIST 800-171

## <a name="overview"></a>Descrição geral
[Publicação especial do NIST 800-171](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) fornece diretrizes para proteger a informação de não controlada (CUI) que residem nos sistemas de informação nonfederal e organizações. SP do NIST 800-171 estabelece 14 famílias de requisitos de segurança para proteger a confidencialidade das CUI.

Este Azure no Security and Compliance Blueprint disponibiliza orientações para ajudar os clientes a implementar uma plataforma como um aplicativo web do serviço (PaaS) no Azure que implementa um subconjunto de NIST SP 800-171 controles. Esta solução demonstra maneiras em que os clientes podem satisfazer requisitos de conformidade e de segurança específicos. Ele também serve como uma base para os clientes criar e configurar o seu próprio aplicativo da web no Azure.

Esta arquitetura de referência, o guia de implementação associados e o modelo de risco destinam-se a servir de base para os clientes para se adaptar às suas necessidades específicas. Não deve ser usados como-está num ambiente de produção. Implementar esta arquitetura sem modificação não é suficiente para atender completamente aos requisitos de SP do NIST 800-171. Os clientes são responsáveis pela realização de avaliações de conformidade de qualquer solução criados com esta arquitetura e segurança adequada. Requisitos podem variar com base nas especificidades da implementação de cada cliente.

## <a name="architecture-diagram-and-components"></a>Diagrama de arquitetura e componentes

Este Azure no Security and Compliance Blueprint fornece uma arquitetura de referência para um aplicativo da web de PaaS com um back-end de base de dados do Azure SQL. O aplicativo web é hospedado num ambiente de serviço de aplicações isolado, o que é um ambiente privado e dedicado num Datacenter do Azure. A carga de ambiente equilibra o tráfego para a aplicação web em máquinas virtuais (VMs) geridas pelo Azure. Esta arquitetura também inclui os grupos de segurança de rede (NSGs), um gateway de aplicação do Azure, o DNS do Azure e o Balanceador de carga do Azure.

Para análise avançada e relatórios, bases de dados SQL do Azure podem ser configurados com índices columnstore. Bases de dados SQL do Azure podem ser aumentados vertical ou horizontalmente ou desativar completamente em resposta a utilização do cliente. Todo o tráfego SQL é encriptado com SSL através da inclusão de certificados autoassinados. Como melhor prática, o Azure recomenda a utilização de uma autoridade de certificação fidedigna para uma maior segurança.

A solução utiliza contas de armazenamento do Azure, o que os clientes podem configurar para utilizar a encriptação do serviço de armazenamento para manter a confidencialidade dos dados em repouso. Azure armazena três cópias dos dados no Centro de dados selecionados de um cliente para resiliência. Armazenamento georredundante garante que os dados são replicados para um centro de dados secundária centenas de quilómetros de distância e armazenados, novamente, como três cópias dentro desse Datacenter. Essa organização impede que um evento adverso no Centro de dados principal do cliente resulta numa perda de dados.

Para maior segurança, todos os recursos nesta solução são geridos como um grupo de recursos através do Gestor de recursos do Azure. O Azure Active Directory, controlo de acesso baseado em funções (Azure AD) (RBAC) é utilizado para controlar o acesso a recursos implementados. Esses recursos incluem as chaves de cliente no Azure Key Vault. Estado de funcionamento do sistema é monitorizado através do Azure Monitor. Os clientes configurar este serviço de monitoramento para capturar os registos. Estado de funcionamento do sistema é exibido num único dashboard, é fácil de usar.

Base de dados SQL normalmente é gerido através do SQL Server Management Studio. Ele é executado de um computador local que está configurado para aceder à base de dados SQL através de uma ligação segura de VPN ou Azure ExpressRoute.

O Application Insights fornece gestão de desempenho de aplicações em tempo real e análises através de registos do Azure Monitor *a Microsoft recomenda que configure uma ligação VPN ou ExpressRoute para importação de dados e de gestão para a referência sub-rede de arquitetura.*

![Aplicação Web de PaaS para o diagrama de arquitetura de referência de SP do NIST 800-171](images/nist171-paaswa-architecture.png "aplicativo da Web de PaaS para o diagrama de arquitetura de referência de SP do NIST 800-171")

Esta solução utiliza os seguintes serviços do Azure. Para obter mais informações, consulte a [arquitetura de implantação](#deployment-architecture) secção.

- Máquinas Virtuais do Azure
    - (1) gestão/bastion (Windows Server 2016 Datacenter)
- Rede Virtual do Azure
    - rede de /16 (1)
    - redes de /24 (4)
    - (4) grupos de segurança de rede
- Gateway de Aplicação do Azure
    - Firewall de aplicação Web
        - Modo de firewall: prevenção
        - Conjunto de regras: OWASP
        - Porta do serviço de escuta: 443
- Application Insights
- Azure Active Directory
- Ambiente de serviço de aplicações v2
- Automatização do Azure
- DNS do Azure
- Azure Key Vault
- Azure Load Balancer
- O Azure Monitor (logs)
- Azure Resource Manager
- Centro de Segurança do Azure
- Base de Dados SQL do Azure
- Storage do Azure
- Automatização do Azure
- Aplicações Web do Azure

## <a name="deployment-architecture"></a>Arquitetura de implantação
A secção seguinte fornece detalhes sobre os elementos de implantação e a implementação.

**Azure Resource Manager**: [Gestor de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) pode ser utilizado pelos clientes para trabalhar com os recursos da solução como um grupo. Os clientes podem implementar, atualizar ou eliminar todos os recursos para a solução numa operação única e coordenada. Os clientes utilizar um modelo para a implementação. O modelo pode funcionar para ambientes diferentes, como teste, preparação e produção. O Resource Manager proporciona segurança, auditoria e etiquetagem recursos para ajudar os clientes a gerir os seus recursos após a implementação.

**Anfitrião de bastião**: O anfitrião de bastião é o único ponto de entrada que os utilizadores podem utilizar para aceder os recursos implementados neste ambiente. O anfitrião de bastião fornece uma ligação segura a recursos implementados, permitindo apenas tráfego remoto de endereços IP públicos numa lista segura. Para permitir o tráfego de ambiente de trabalho remoto, tem de ser definida a origem do tráfego no NSG.

Esta solução cria uma VM como um anfitrião de bastião associados a um domínio com as seguintes configurações:
-   [Extensão de antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware).
-   [Extensão de diagnóstico do Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template).
-   [O Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) com o Key Vault.
-   Uma [política de encerramento automático](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) para reduzir o consumo de recursos VM quando não está em utilização.
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) está ativada para que as credenciais e outros segredos executar num ambiente protegido, que está isolado do sistema operativo em execução.

**Aplicações Web**: [Aplicações Web](https://docs.microsoft.com/azure/app-service/) é uma funcionalidade do serviço de aplicações do Azure. Os clientes podem usá-lo para criar e alojar aplicações web na linguagem de programação de sua preferência sem ter de gerir infraestrutura. Ele oferece o dimensionamento automático e elevada disponibilidade. Ele suporta Windows e Linux e permite implementações automáticas a partir do GitHub, do Azure DevOps ou qualquer repositório de Git.

**Ambiente de serviço de aplicações**: [Ambiente de serviço de aplicações](https://docs.microsoft.com/azure/app-service/environment/intro) é uma funcionalidade do serviço de aplicações. Ele fornece um ambiente totalmente isolado e dedicado para uma execução segura de aplicativos de serviço de aplicações a uma escala elevada.

O ambiente de serviço de aplicações é isolado para executar apenas uma única aplicação. Ela sempre é implantada numa rede virtual. Devido a funcionalidade de isolamento, a arquitetura de referência tem isolamento de inquilino completo e este é removido do ambiente de multi-inquilino do Azure. Os clientes têm um controle refinado sobre o tráfego de rede aplicação de entrada e saída. As aplicações podem estabelecer conexões seguras de alta velocidade nas redes virtuais para recursos da empresa no local. Os clientes podem "dimensionamento automático" com o ambiente de serviço de aplicações baseadas em métricas de carga, o orçamento disponível ou uma agenda definida.

Utilização do ambiente de serviço de aplicações para esta arquitetura fornece os controles e configurações seguintes:

- Anfitriões dentro de uma rede virtual do Azure protegida e regras de segurança de rede.
- Certificado de Balanceador de carga interno com assinatura automática para comunicação HTTPS. Como melhor prática, a Microsoft recomenda a utilização de uma autoridade de certificação fidedigna para uma maior segurança.
- [Modo de balanceamento de carga interno](https://docs.microsoft.com/azure/app-service-web/app-service-environment-with-internal-load-balancer) (modo 3).
- Desativar [TLS 1.0](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings).
- Alteração [cifras TLS](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings).
- Controle [portas de N/W de tráfego de entrada](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-control-inbound-traffic).
- [Firewall de aplicações Web – restringir dados](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-web-application-firewall).
- Permitir [tráfego de base de dados do Azure SQL](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-network-architecture-overview).

### <a name="virtual-network"></a>Rede virtual
A arquitetura define uma rede privada virtual com um espaço de endereços de 10.200.0.0/16.

**Grupos de segurança de rede**: [NSGs](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) contêm listas de controlo de acesso que permitem ou negam o tráfego dentro de uma rede virtual. Podem ser utilizados NSGs para proteger o tráfego numa sub-rede ou o nível VM individual. Existem os NSGs seguintes:
- Um NSG para o Gateway de aplicação
- Um NSG para ambiente de serviço de aplicações
- Um NSG para a base de dados SQL
- Um NSG para o anfitrião de bastião

Cada um dos NSGs tem portas específicas e protocolos abra para que a solução possam funcionar corretamente e de forma segura. Além disso, as seguintes configurações são habilitadas para cada NSG:
  - [Eventos e registos de diagnóstico](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) forem ativadas e armazenados numa conta de armazenamento.
  - Registos de Monitor do Azure está ligada a [diagnóstico do NSG](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json).

**Sub-redes**: Cada sub-rede está associado a seu NSG correspondente.

**O DNS do Azure**: O sistema de nomes de domínio (DNS) é responsável por converter (ou resolver) um nome de Web site ou serviço para o endereço IP. [O DNS do Azure](https://docs.microsoft.com/azure/dns/dns-overview) é um serviço de alojamento dos domínios DNS que fornece resolução de nomes através da infraestrutura do Azure. Ao alojar os domínios no Azure, os utilizadores podem gerir registos DNS ao utilizar as mesmas credenciais, APIs, ferramentas e faturação dos outros serviços do Azure. O DNS do Azure também suporta domínios DNS privados.

**Balanceador de carga do Azure**: [Balanceador de carga](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) pode ser utilizado pelos clientes para dimensionar seus aplicativos e criar alta disponibilidade para serviços. Load Balancer suporta cenários de entrada e saídos. Ele oferece baixa latência e alto débito e aumenta até milhões de fluxos para todas as aplicações TCP e UDP.

### <a name="data-in-transit"></a>Dados em trânsito
Azure encripta todas as comunicações de e para os centros de dados do Azure por predefinição. A ocorrência de todas as transações para o armazenamento do Azure através do portal do Azure através de HTTPS.

### <a name="data-at-rest"></a>Dados inativos

A arquitetura protege os dados em descanso através da encriptação, a auditoria de base de dados e outras medidas.

**O armazenamento do Azure**: Para cumprir os requisitos para os dados encriptados em inatividade, todos os [armazenamento](https://azure.microsoft.com/services/storage/) utiliza [Storage Service Encryption](https://docs.microsoft.com/azure/storage/storage-service-encryption). Esta funcionalidade ajuda a proteger e salvaguardar os dados para oferecer suporte a requisitos de conformidade definidos pelo SP do NIST 800-171 e de compromissos de segurança organizacional.

**Azure Disk Encryption**: [Encriptação de disco](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) utiliza a funcionalidade BitLocker do Windows para fornecer encriptação de volume para discos de dados. A solução se integra com o Key Vault para ajudar a controlar e gerir as chaves de encriptação de disco.

**Base de dados SQL do Azure**: A instância de base de dados SQL utiliza as seguintes medidas de segurança da base de dados:
-   [Autenticação do Active Directory e a autorização](https://docs.microsoft.com/azure/sql-database/sql-database-AAD-authentication) permite a gestão de identidades de utilizadores de base de dados e outros serviços da Microsoft num local central.
-   [A auditoria de base de dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) faixas de base de dados eventos e escreve-os para uma auditoria registo numa conta de armazenamento do Azure.
-   Base de dados SQL está configurado para utilizar [encriptação de dados transparente](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql). Ele executa criptografia em tempo real e a descriptografia da base de dados, cópias de segurança associadas e ficheiros de registo de transação para proteger as informações em repouso. Encriptação de dados transparente fornece garantia de que são armazenados os dados não tem sido sujeita a acesso não autorizado.
-   [Regras de firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) impedir o acesso de todos os servidores de base de dados até que sejam concedidas as permissões apropriadas. A firewall concede acesso a bases de dados com base no endereço IP de origem de cada pedido.
-   [Deteção de ameaças SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) permite a deteção e resposta a ameaças potenciais à medida que ocorrem. Ele fornece alertas de segurança para atividades suspeitas da base de dados, potenciais vulnerabilidades, ataques de injeção de SQL e padrões de acesso de base de dados anómalas.
-   [Encriptados colunas](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) Certifique-se de que os dados confidenciais nunca é apresentada como texto simples dentro do sistema de base de dados. Depois de é ativada a encriptação de dados, apenas as aplicações de cliente ou servidores de aplicações com acesso às chaves podem aceder a dados de texto sem formatação.
- [Máscara de dados dinâmicos](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) limita a exposição de dados confidenciais ao mascará os dados de utilizadores não privilegiados ou aplicações. É, automaticamente, pode detetar dados potencialmente confidenciais e sugerir as máscaras de apropriado a ser aplicado. Máscara de dados dinâmica ajuda a reduzir o acesso, de modo a que dados confidenciais não sair da base de dados por meio do acesso não autorizado. *Os clientes são responsáveis por ajustar as definições de cumprir para seu esquema de base de dados.*

### <a name="identity-management"></a>Gestão de identidades
As seguintes tecnologias fornecem capacidades para gerir o acesso aos dados no ambiente do Azure:
-   [O Azure AD](https://azure.microsoft.com/services/active-directory/) é multi-inquilino com base na cloud diretório e identidade do serviço de gestão da Microsoft. Todos os utilizadores para esta solução são criados no Azure AD e incluem os usuários que acessam o banco de dados SQL.
-   Autenticação para a aplicação é executada ao utilizar o Azure AD. Para obter mais informações, consulte como [integrar aplicações com o Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). A encriptação de coluna de base de dados também utiliza o Azure AD para autenticar a aplicação para a base de dados SQL. Para obter mais informações, consulte como [proteger dados confidenciais na base de dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [RBAC do Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) podem ser utilizadas pelos administradores para definir permissões de acesso detalhado. Com ele, eles podem conceder apenas a quantidade de acesso que os utilizadores precisam realizar seus trabalhos. Em vez de dar a cada acesso de utilizador sem restrições para recursos do Azure, os administradores podem permitir apenas certas ações para aceder a recursos e dados. Acesso à subscrição está limitado ao administrador da subscrição.
- [O Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) pode ser utilizado pelos clientes para minimizar o número de utilizadores que têm acesso a determinadas informações. Os administradores podem utilizar o Azure AD Privileged Identity Management para detetar, restringir e monitorizar identidades privilegiadas e respetivo acesso a recursos. Esta funcionalidade também pode ser utilizada para impor acesso administrativo a pedido e just-in-time quando necessário.
- [O Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) Deteta potenciais vulnerabilidades que afetam as identidades de uma organização. Configura as respostas automáticas para detetado ações suspeitas relacionadas com identidades de uma organização. Também é mostrada a incidentes suspeitos para tomar as medidas adequadas para resolvê-los.

### <a name="security"></a>Segurança
**Gestão de segredos**: A solução utiliza [Key Vault](https://azure.microsoft.com/services/key-vault/) para o gerenciamento de chaves e segredos. Key Vault ajuda a salvaguardar as chaves criptográficas e segredos utilizados pelas aplicações e serviços cloud. As seguintes capacidades de Key Vault ajudam os clientes a proteger os dados:
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

**Gateway de aplicação do Azure**: A arquitetura reduz o risco de vulnerabilidades de segurança através da utilização de um gateway de aplicação com uma firewall de aplicações web configuradas e o conjunto de regras OWASP ativada. Capacidades adicionais incluem:

- [End-to-end-SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).
- Ativar [descarga de SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal).
- Desativar [TLS versões 1.0 e 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).
- [Firewall de aplicações Web](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (modo de prevenção).
- [Modo de prevenção](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) com OWASP 3.0 conjunto de regra.
- Ativar [registo de diagnósticos](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).
- [Sondas de estado de funcionamento personalizado](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-gateway-portal).
- [Centro de segurança](https://azure.microsoft.com/services/security-center) e [Assistente do Azure](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) fornecer proteção adicional e notificações. Centro de segurança também fornece um sistema de reputação.

### <a name="logging-and-auditing"></a>Registro e auditoria

Serviços do Azure extensivamente de registo do sistema e a atividade do utilizador, bem como o estado de funcionamento do sistema:
- **Registos de atividades**: [Registos de atividades](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) fornecem informações sobre as operações executadas em recursos numa subscrição. Registos de atividades podem ajudar a determinar o iniciador de uma operação, hora da ocorrência e o estado.
- **Os registos de diagnóstico**: [Os registos de diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) incluem todos os registos emitidos por cada recurso. Estes registos incluem registos de sistema de eventos do Windows, registos de armazenamento, registos de auditoria do Cofre de chaves e os registos de acesso e de firewall do Gateway de aplicação. Todos os registos de diagnóstico escrevem para uma conta de armazenamento do Azure centralizado e criptografado para arquivamento. Os utilizadores podem configurar o período de retenção, até e 730 dias, para satisfazer os seus requisitos específicos.

**Registos de Monitor do Azure**: Os registos são consolidados no [registos do Azure Monitor](https://azure.microsoft.com/services/log-analytics/) para processamento, armazenamento e relatórios do dashboard. Depois de recolhidos, os dados são organizado em tabelas separadas para cada tipo de dados nas áreas de trabalho do Log Analytics. Dessa forma, todos os dados podem ser analisados em conjunto, independentemente de sua fonte original. Centro de segurança integra-se com os registos do Azure Monitor. Os clientes podem utilizar consultas de Kusto para aceder aos respetivos dados de eventos de segurança e combiná-los com dados de outros serviços.

O Azure seguinte [soluções de monitorização](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) são incluídos como parte desta arquitetura:
-   [Avaliação do Active Directory](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): A solução de verificação de estado de funcionamento do Active Directory avalia o risco e estado de funcionamento dos ambientes de servidor num intervalo regular. Ele fornece uma lista prioritária de recomendações específicas para a infraestrutura de servidor implementado.
- [Avaliação do SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): A solução de verificação de estado de funcionamento do SQL avalia o risco e estado de funcionamento dos ambientes de servidor num intervalo regular. Fornece aos clientes com uma lista prioritária de recomendações específicas para a infraestrutura de servidor implementado.
- [Estado de funcionamento do agente](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): A solução de estado de funcionamento do agente relata o número de agentes é implementado e a respetiva distribuição geográfica. Também relata o número de agentes é sem resposta e o número de agentes que submeter dados operacionais.
-   [Log Analytics da atividade](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): A solução Log Analytics da atividade auxilia com uma análise de registos de atividade do Azure em todas as subscrições do Azure para um cliente.

**A automatização do Azure**: [Automatização](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) armazena, executa e gere runbooks. Nesta solução, runbooks ajudam a recolher registos de base de dados SQL. Os clientes podem usar a automação [controlo de alterações](https://docs.microsoft.com/azure/automation/automation-change-tracking) solução para o identificar facilmente as alterações no ambiente.

**Azure Monitor**: [Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) ajuda os usuários a acompanhar o desempenho, manter a segurança e identificar tendências. As organizações podem usá-lo de auditoria, criar alertas e arquivar dados. Também pode controlar as chamadas de API em seus recursos do Azure.

**O Application Insights**: [O Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) é um serviço de gestão do desempenho de aplicações extensível para desenvolvedores da web em várias plataformas. O Application Insights Deteta anomalias de desempenho. Os clientes podem usá-lo para monitorizar a aplicação web em direto. O Application Insights inclui ferramentas de análise poderosas para ajudar os clientes a diagnosticar problemas e compreender o que os utilizadores fazem com a sua aplicação. -&#39;s concebido para ajudar os clientes a melhorar continuamente o desempenho e a usabilidade.

## <a name="threat-model"></a>Modelo de risco

O diagrama de fluxo de dados para esta arquitetura de referência está disponível para [transferir](https://aka.ms/nist171-paaswa-tm) ou podem ser encontradas aqui. Esse modelo pode ajudar os clientes a compreender os pontos de risco potencial na infra-estrutura do sistema quando eles fazer modificações.

![Aplicação Web de PaaS para o modelo de risco de SP do NIST 800-171](images/nist171-paaswa-threat-model.png "aplicativo da Web de PaaS para o modelo de risco de SP do NIST 800-171")

## <a name="compliance-documentation"></a>Documentação de conformidade
O [Azure no Security and Compliance Blueprint - matriz de responsabilidade do cliente de SP do NIST 800-171](https://aka.ms/nist171-crm) apresenta uma lista de todos os controlos de segurança exigidos pelo SP do NIST 800-171. Essa matriz se a implementação de cada controle é da responsabilidade da Microsoft, o cliente, os detalhes ou partilhados entre os dois.

O [Azure no Security and Compliance Blueprint - matriz de implementação de controlo Web aplicação SP do NIST 800-171 PaaS](https://aka.ms/nist171-paaswa-cim) fornece informações sobre quais SP NIST 800-171 controles são resolvidas à arquitetura de aplicativo da web de PaaS. Ele inclui descrições detalhadas de como a implementação cumpre os requisitos de cada controle coberta.

## <a name="guidance-and-recommendations"></a>Orientações e recomendações
### <a name="vpn-and-expressroute"></a>ExpressRoute e VPN
Um túnel VPN seguro ou [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) tem de ser configurado com segurança estabelecer uma ligação para os recursos implementados como parte desta arquitetura de referência de aplicação de web de PaaS. Configurando adequadamente uma VPN ou ExpressRoute, os clientes podem adicionar uma camada de proteção para dados em trânsito.

Ao implementar um túnel VPN seguro com o Azure, é possível criar uma ligação privada virtual entre uma rede no local e uma rede virtual do Azure. Essa conexão ocorre através da Internet e permite que os clientes com segurança as informações de "encapsulamento" dentro de uma ligação encriptada entre a rede do cliente e o Azure. VPN site a site é uma tecnologia madura e segura que tenha sido implementada por empresas de todos os tamanhos há décadas. O [modo de encapsulamento IPsec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) é utilizada nesta opção como um mecanismo de criptografia.

Uma vez que o tráfego no túnel VPN atravessa a Internet com uma VPN de site a site, a Microsoft oferece outra opção de ligação ainda mais segura. O ExpressRoute é uma WAN dedicada ligação entre o Azure e uma localização no local ou um fornecedor de alojamento do Exchange. As ligações ExpressRoute ligar-se diretamente ao fornecedor de telecomunicação de um cliente. Como resultado, os dados não viajam através da Internet e não estão expostos à mesma. Estas ligações oferecem mais fiabilidade, velocidades mais rápidas, latências mais baixas e maior segurança do que as ligações normais.

Melhores práticas para implementar uma rede híbrida segura que expande uma rede no local para o Azure são [disponível](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## <a name="disclaimer"></a>Exclusão de Responsabilidade

 - Este documento é apenas para fins informativos. A MICROSOFT FAZ NÃO OFERECE QUAISQUER GARANTIAS, EXPRESSAS, IMPLÍCITAS OU ESTATUTÁRIAS QUANTO ÀS INFORMAÇÕES CONTIDAS NESTE DOCUMENTO. Este documento é fornecido "como-está." Informações e opiniões expressados neste documento, incluindo URLs e outras referências de Web site da Internet, podem ser alteradas sem aviso prévio. Os clientes a ler este documento assume o risco de usá-lo.
 - Este documento não fornece aos clientes quaisquer direitos de propriedade intelectual em qualquer produto da Microsoft ou soluções.
 - Os clientes podem copiar e utilizar este documento para efeitos de referência interna.
 - Algumas recomendações neste documento podem resultar em maior de dados, a rede ou a utilização de recursos de computação no Azure e podem aumentar os custos de licenciamento ou de subscrição do Azure num cliente.
 - Esta arquitetura deve ser usado como uma base para os clientes que se ajustar às suas necessidades específicas e não devem ser utilizada como-está num ambiente de produção.
 - Este documento é desenvolvido como uma referência e não deve ser usado para definir todos os meios pelos quais um cliente pode atender aos requisitos de conformidade e normas. Os clientes procuram legal suporte da sua organização em implementações do cliente aprovadas.
