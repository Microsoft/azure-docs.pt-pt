---
title: Aplicativo Web Blueprint de Segurança e Conformidade do Azure-PaaS para FedRAMP
description: Aplicativo Web Blueprint de Segurança e Conformidade do Azure-PaaS para FedRAMP
services: security
author: jomolesk
ms.assetid: 41570fc1-4d74-48ed-afb0-ef1be857029e
ms.service: security
ms.topic: article
ms.date: 06/01/2018
ms.author: jomolesk
ms.openlocfilehash: b74373201db26405653584fc4000993b9d6fdf43
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/10/2019
ms.locfileid: "68946818"
---
# <a name="azure-security-and-compliance-blueprint-paas-web-application-for-fedramp"></a>Esquema de Segurança e Conformidade do Azure: Aplicativo Web de PaaS para FedRAMP

## <a name="overview"></a>Descrição geral

O [Federal Risk and Authorization Management Program (FedRAMP)](https://www.fedramp.gov/) é um programa Estados Unidos de todo o governo que fornece uma abordagem padronizada para avaliação de segurança, autorização e monitoramento contínuo para produtos e serviços de nuvem. Este Blueprint de Segurança e Conformidade do Azure fornece diretrizes sobre como fornecer uma arquitetura PaaS (plataforma como serviço) Microsoft Azure que ajuda a implementar um subconjunto de controles altos do FedRAMP. Esta solução fornece orientação sobre a implantação e a configuração de recursos do Azure para uma arquitetura de referência comum, demonstrando maneiras nas quais os clientes podem atender aos requisitos específicos de segurança e conformidade e serve como base para os clientes Crie e configure suas próprias soluções no Azure.

Essa arquitetura de referência, os guias de implementação de controle associados e os modelos de ameaça são destinados a servir como base para os clientes se ajustarem aos requisitos específicos e não devem ser usados no estado em que se encontram em um ambiente de produção. Implantar um aplicativo nesse ambiente sem modificação é insuficiente para atender totalmente aos requisitos da linha de base FedRAMP alta. Tenha em atenção o seguinte:
- A arquitetura fornece uma linha de base para ajudar os clientes a implantar cargas de trabalho no Azure de maneira compatível com FedRAMP.
- Os clientes são responsáveis por realizar avaliações apropriadas de segurança e conformidade de qualquer solução criada usando essa arquitetura, já que os requisitos podem variar com base nas especificidades da implementação de cada cliente.

## <a name="architecture-diagram-and-components"></a>Diagrama e componentes da arquitetura
Essa solução fornece uma arquitetura de referência para um aplicativo Web de PaaS com um back-end do banco de dados SQL do Azure. O aplicativo Web é hospedado em um Ambiente do Serviço de Aplicativo do Azure isolado, que é um ambiente privado dedicado em um datacenter do Azure. A carga do ambiente equilibra o tráfego para o aplicativo Web entre VMs gerenciadas pelo Azure. Essa arquitetura também inclui grupos de segurança de rede, um gateway de aplicativo, um DNS do Azure e um Load Balancer. Além disso, o Azure Monitor fornece análise em tempo real da integridade do sistema. **O Azure recomenda configurar uma conexão VPN ou ExpressRoute para gerenciamento e importação de dados na sub-rede da arquitetura de referência.**

![Aplicativo Web PaaS para diagrama de arquitetura de referência do FedRAMP](images/fedramp-paaswa-architecture.png?raw=true "Aplicativo Web PaaS para diagrama de arquitetura de referência do FedRAMP")

Essa solução usa os seguintes serviços do Azure. Os detalhes da arquitetura de implantação estão localizados na seção [arquitetura de implantação](#deployment-architecture) .

- Azure Active Directory
- Azure Key Vault
- Base de Dados SQL do Azure
- Gateway de Aplicação
    - (1) Firewall do aplicativo Web
        - Modo de firewall: prevenção
        - Conjunto de regras: OWASP 3.0
        - Ouvinte: porta 443
- Rede virtual do Azure
- Grupos de segurança de rede
- DNS do Azure
- Storage do Azure
- Azure Monitor
- Ambiente do Serviço de Aplicativo v2
- Balanceador de Carga do Azure
- Aplicação Web do Azure
- Azure Resource Manager

## <a name="deployment-architecture"></a>Arquitetura de implantação
A seção a seguir detalha os elementos de implantação e implementação.

**Azure Resource Manager**: [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) permite que os clientes trabalhem com os recursos da solução como um grupo. Os clientes podem implantar, atualizar ou excluir todos os recursos da solução em uma única operação coordenada. Os clientes usam um modelo para implantação e esse modelo pode funcionar para ambientes diferentes, como teste, preparo e produção. O Gerenciador de recursos fornece recursos de segurança, auditoria e marcação para ajudar os clientes a gerenciar seus recursos após a implantação.

**Ambiente do serviço de aplicativo v2**: O [ambiente do serviço de aplicativo do Azure (ase)](https://docs.microsoft.com/azure/app-service/environment/intro) é um recurso do serviço de aplicativo que fornece um ambiente totalmente isolado e dedicado para a execução segura de aplicativos do serviço de aplicativo em alta escala.

Os ASEs são isolados para executar apenas aplicativos de um único cliente e sempre são implantados em uma rede virtual. Os clientes têm controle refinado sobre o tráfego de rede do aplicativo de entrada e saída, e os aplicativos podem estabelecer conexões seguras de alta velocidade em redes virtuais para recursos corporativos locais.

O uso de ASEs para essa arquitetura é permitido para os seguintes controles/configurações:

- Hospedar dentro de uma rede virtual do Azure protegida e regras de segurança de rede
- ASE configurado com certificado ILB autoassinado para comunicação HTTPS
- [Modo de balanceamento de carga interno](../../app-service/environment/app-service-environment-with-internal-load-balancer.md)
- Desabilitar [TLS 1,0](../../app-service/environment/app-service-app-service-environment-custom-settings.md)
- Alterar a [codificação TLS](../../app-service/environment/app-service-app-service-environment-custom-settings.md)
- Controlar [portas N/W de tráfego de entrada](../../app-service/environment/app-service-app-service-environment-control-inbound-traffic.md)
- [Firewall do aplicativo Web – restringir dados](../../app-service/environment/app-service-app-service-environment-web-application-firewall.md)
- Permitir [tráfego do banco de dados SQL do Azure](../../app-service/environment/app-service-app-service-environment-network-architecture-overview.md)

A seção [diretrizes e recomendações](#guidance-and-recommendations) contém informações adicionais sobre o ases.

**Aplicativo Web do Azure**: [Azure app serviço](https://docs.microsoft.com/azure/app-service/) permite que os clientes criem e hospedem aplicativos Web na linguagem de programação de sua escolha sem gerenciar a infraestrutura. Este serviço garante dimensionamento automático e elevada disponibilidade, suporta Windows e Linux e permite implementações automáticas a partir do GitHub, DevOps do Azure ou de qualquer repositório de Git.

### <a name="virtual-network"></a>Rede virtual
A arquitetura define uma rede virtual privada com um espaço de endereço de 10.200.0.0/16.

**Grupos de segurança de rede**: [NSGs (grupos de segurança de rede)](../../virtual-network/virtual-network-vnet-plan-design-arm.md) contêm listas de controle de acesso que permitem ou negam o tráfego em uma rede virtual. NSGs pode ser usado para proteger o tráfego em uma sub-rede ou nível de VM individual. Os seguintes NSGs existem:
- 1 NSG para o gateway de aplicativo
- 1 NSG para Ambiente do Serviço de Aplicativo
- 1 NSG para o banco de dados SQL do Azure

Cada um dos NSGs tem portas e protocolos específicos abertos para que a solução possa funcionar de forma segura e correta. Além disso, as seguintes configurações estão habilitadas para cada NSG:
  - [Os logs e eventos de diagnóstico](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) são habilitados e armazenados em uma conta de armazenamento
  - Os logs de Azure Monitor estão conectados ao [diagnóstico do NSG](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Sub-redes**: Cada sub-rede é associada ao seu NSG correspondente.

**DNS do Azure**: O sistema de nomes de domínio, ou DNS, é responsável por converter (ou resolver) um site ou nome de serviço para seu endereço IP. O [DNS do Azure](https://docs.microsoft.com/azure/dns/dns-overview) é um serviço de hospedagem para domínios DNS que fornece resolução de nomes usando a infraestrutura do Azure. Ao hospedar domínios no Azure, os usuários podem gerenciar registros DNS usando as mesmas credenciais, APIs, ferramentas e cobrança que outros serviços do Azure. O DNS do Azure também dá suporte a domínios DNS privados.

**Azure Load Balancer**: [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) permite que os clientes dimensionem seus aplicativos e criem alta disponibilidade para os serviços. O Load Balancer dá suporte a cenários de entrada e saída e fornece baixa latência, alta taxa de transferência e escala verticalmente para milhões de fluxos para todos os aplicativos TCP e UDP.

### <a name="data-in-transit"></a>Dados em trânsito
Por padrão, o Azure criptografa todas as comunicações entre os data centers do Azure. Todas as transações para o armazenamento do Azure por meio do portal do Azure ocorrem via HTTPS.

### <a name="data-at-rest"></a>Dados inativos
A arquitetura protege os dados em repouso por meio de criptografia, auditoria de banco e outras medidas.

**Armazenamento do Azure**: Para atender aos requisitos de dados criptografados em repouso, todo o [armazenamento do Azure](https://azure.microsoft.com/services/storage/) usa [criptografia do serviço de armazenamento](../../storage/common/storage-service-encryption.md).

AzureDiskEncryption
[Azure Disk Encryption](../azure-security-disk-encryption-overview.md) aproveita o recurso BitLocker do Windows para fornecer criptografia de volume para discos de dados. A solução se integra com Azure Key Vault para ajudar a controlar e gerenciar as chaves de criptografia de disco.

**Banco de dados SQL do Azure**: A instância do banco de dados SQL do Azure usa as seguintes medidas de segurança de banco de dados:
-   A [autenticação e a autorização do AD](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) permitem o gerenciamento de identidades de usuários de banco de dados e outros serviços da Microsoft em um local central.
-   A [auditoria do banco de dados SQL](../../sql-database/sql-database-auditing.md) rastreia eventos de banco de dados e os grava em um log de auditoria em uma conta de armazenamento do Azure.
-   O banco de dados SQL do Azure está configurado para usar o [Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), que executa criptografia e descriptografia em tempo real do banco de dados, backups associados e arquivos de log de transações para proteger informações em repouso.
-   [As regras de firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) impedem todo o acesso a servidores de banco de dados até que sejam concedidas permissões adequadas A firewall concede acesso a bases de dados com base no endereço IP de origem de cada pedido.
-   A [detecção de ameaças do SQL](../../sql-database/sql-database-threat-detection.md) permite a detecção e a resposta a ameaças potenciais à medida que elas ocorrem, fornecendo alertas de segurança para atividades suspeitas de banco de dados, vulnerabilidades potenciais, ataques de injeção de SQL e padrões de acesso de banco de dados anormais
-   [Always Encrypted colunas](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) garantem que os dados confidenciais nunca apareçam como texto não criptografado no sistema de banco de dados. Depois de habilitar a criptografia de dados, somente aplicativos cliente ou servidores de aplicativos com acesso às chaves podem acessar dados de texto sem formatação.
- A [segurança em nível de linha](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) permite que os usuários definam políticas para restringir o acesso aos dados para o processamento descontinuado.
- O mascaramento de [dados dinâmicos do banco SQL](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) pode ser feito após a implantação da arquitetura de referência. Os clientes precisarão ajustar as configurações de mascaramento de dados dinâmicos para aderir ao seu esquema de banco de dado.

### <a name="identity-management"></a>Gestão de identidades
As tecnologias a seguir fornecem recursos de gerenciamento de identidade no ambiente do Azure:
-   [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) é o serviço de gerenciamento de identidade e diretório multilocatário baseado em nuvem da Microsoft. Todos os usuários desta solução são criados no AAD, incluindo usuários que acessam o banco de dados SQL do Azure.
-   A autenticação para o aplicativo é executada usando o AAD. Para obter mais informações, consulte [integrando aplicativos com Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). Além disso, a criptografia de coluna de banco de dados usa Azure Active Directory para autenticar o aplicativo no banco de dados SQL do Azure. Para obter mais informações, Confira como [proteger dados confidenciais no Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   O [controle de acesso baseado em função do Azure](../../role-based-access-control/role-assignments-portal.md) permite o gerenciamento de acesso precisamente focalizado para o Azure. O acesso à assinatura é limitado ao administrador da assinatura e o acesso aos recursos pode ser limitado com base na função de usuário.
- [Azure Active Directory Privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md) permite que os clientes minimizem o número de usuários que têm acesso a determinadas informações.  Os administradores podem usar Privileged Identity Management do AAD para descobrir, restringir e monitorar identidades com privilégios e seu acesso aos recursos. Essa funcionalidade também pode ser usada para impor acesso administrativo sob demanda e Just-in-time quando necessário.
- [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview.md) detecta possíveis vulnerabilidades que afetam as identidades de uma organização, configura as respostas automatizadas para detectar ações suspeitas relacionadas às identidades de uma organização e investiga incidentes suspeitos para tomar as medidas apropriadas para resolvê-los.

### <a name="security"></a>Segurança
**Gerenciamento de segredos** A solução usa [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) para o gerenciamento de chaves e segredos. O Cofre de Chaves do Azure ajuda a salvaguardar as chaves criptográficas e os segredos utilizados pelas aplicações em cloud e pelos serviços. Os recursos de Azure Key Vault a seguir ajudam os clientes a proteger dados e acesso a esses dados:
- As políticas de acesso avançadas são configuradas de acordo com a necessidade.
- Key Vault políticas de acesso são definidas com as permissões mínimas necessárias para chaves e segredos.
- Todas as chaves e segredos em Key Vault têm datas de expiração.
- Todas as chaves em Key Vault são protegidas por HSMs (módulos de segurança de hardware) especializados. O tipo de chave é uma chave RSA de 2048 bits protegida por HSM.
- Todos os usuários e identidades recebem as permissões mínimas necessárias usando o controle de acesso baseado em função.
- Os logs de diagnóstico para Key Vault estão habilitados com um período de retenção de pelo menos 365 dias.
- As operações de criptografia permitidas para chaves são restritas às necessárias.

**Gateway de aplicativo** A arquitetura reduz o risco de vulnerabilidades de segurança usando um gateway de aplicativo com o Firewall do aplicativo Web e o conjunto de regras OWASP habilitado. Os recursos adicionais incluem:
- [End-to-End-SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Habilitar [descarregamento SSL](../../application-gateway/create-ssl-portal.md)
- Desabilitar [TLS v 1.0 e v 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Firewall do aplicativo Web](../../application-gateway/waf-overview.md)
- [Modo de prevenção](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) com conjunto de regras OWASP 3,0
- Habilitar [log de diagnóstico](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)
- [Investigações de integridade personalizadas](../../application-gateway/quick-create-portal.md)
- A [central de segurança do Azure](https://azure.microsoft.com/services/security-center) e o [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) fornecem proteção e notificações adicionais. A central de segurança do Azure também fornece um sistema de reputação.

### <a name="logging-and-auditing"></a>Registro em log e auditoria
O Azure Monitor fornece log extensivo de atividade do sistema e do usuário, bem como a integridade do sistema. Ele coleta e analisa os dados gerados pelos recursos no Azure e em ambientes locais.
- **Logs de atividade**: [Os logs de atividade](../../azure-monitor/platform/activity-logs-overview.md) fornecem informações sobre as operações executadas nos recursos em uma assinatura. Os logs de atividades podem ajudar a determinar o iniciador de uma operação, a hora de ocorrência e o status.
- **Logs de diagnóstico**: Os [logs de diagnóstico](../../azure-monitor/platform/diagnostic-logs-overview.md) incluem todos os logs emitidos por cada recurso. Esses logs incluem logs do sistema de eventos do Windows, logs de armazenamento do Azure, Key Vault logs de auditoria e acesso do gateway de aplicativo e logs de firewall.
- **Arquivamento de log**: Todos os logs de diagnóstico gravam em uma conta de armazenamento do Azure centralizada e criptografada para arquivamento. A retenção é configurável pelo usuário, até 730 dias, para atender aos requisitos de retenção específicos da organização. Esses logs se conectam a logs de Azure Monitor para processamento, armazenamento e relatórios de painel.

Além disso, as soluções de monitoramento a seguir são incluídas como parte dessa arquitetura:
-   [Avaliação do Active Directory](../../azure-monitor/insights/ad-assessment.md): A solução de verificação de integridade Active Directory avalia o risco e a integridade dos ambientes de servidor em um intervalo regular e fornece uma lista priorizada de recomendações específicas para a infraestrutura de servidor implantada.
-   [Avaliação de antimalware](../../security-center/security-center-install-endpoint-protection.md): A solução antimalware relata sobre malware, ameaças e status de proteção.
-   [Automação do Azure](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): A solução de automação do Azure armazena, executa e gerencia runbooks. Nesta solução, os runbooks ajudam a coletar logs do Application Insights e do banco de dados SQL do Azure.
-   [Segurança e auditoria](../../security-center/security-center-intro.md): O painel de Segurança e Auditoria fornece uma percepção de alto nível do estado de segurança dos recursos, fornecendo métricas sobre domínios de segurança, problemas notáveis, detecções, inteligência contra ameaças e consultas de segurança comuns.
-   [Avaliação do SQL](../../azure-monitor/insights/sql-assessment.md): A solução de verificação de integridade do SQL avalia o risco e a integridade dos ambientes de servidor em um intervalo regular e fornece aos clientes uma lista priorizada de recomendações específicas para a infraestrutura de servidor implantada.
-   [Gerenciamento de atualizações](../../automation/automation-update-management.md): A solução Gerenciamento de Atualizações permite o gerenciamento de clientes das atualizações de segurança do sistema operacional, incluindo o status das atualizações disponíveis e o processo de instalação das atualizações necessárias.
-   [Integridade do agente](../../monitoring/monitoring-solution-agenthealth.md): A solução Integridade do Agente relata Quantos agentes são implantados e sua distribuição geográfica, bem como Quantos agentes que não respondem e o número de agentes que estão enviando dados operacionais.
-   [Logs de atividades do Azure](../../azure-monitor/platform/collect-activity-logs.md): A solução Análise do Log de Atividades auxilia na análise dos logs de atividade do Azure em todas as assinaturas do Azure para um cliente.
-   [Controle de alterações](../../automation/change-tracking.md): A solução Controle de Alterações permite que os clientes identifiquem facilmente as alterações no ambiente.

Azuremonitor
[Azure monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) ajuda os usuários a controlar o desempenho, manter a segurança e identificar tendências habilitando as organizações a auditar, criar alertas e arquivar dados, incluindo o acompanhamento de chamadas à API nos recursos do Azure dos clientes.

## <a name="threat-model"></a>Modelo de ameaça

O diagrama de fluxo de dados para esta arquitetura de referência está disponível para [Download](https://aka.ms/fedrampPaaSWebAppDFD) ou pode ser encontrado abaixo. Esse modelo pode ajudar os clientes a entender os pontos de risco potencial na infraestrutura do sistema ao fazer modificações.

![Aplicativo Web de PaaS para modelo de ameaça FedRAMP](images/fedramp-paaswa-threat-model.png?raw=true "Aplicativo Web de PaaS para modelo de ameaça FedRAMP")

## <a name="compliance-documentation"></a>Documentação de conformidade
A [matriz de responsabilidade do cliente do Blueprint de segurança e conformidade do Azure-FedRAMP alta](https://aka.ms/blueprinthighcrm) lista todos os controles de segurança exigidos pela linha de base alta do FedRAMP. A matriz denota se a implementação de cada controle é de responsabilidade da Microsoft, do cliente ou do compartilhamento entre os dois.

A [matriz Blueprint de segurança e conformidade do Azure de implementação de controle alto de FedRAMP de PaaS webapp](https://aka.ms/fedrampPaaSWebAppCIM) lista todos os controles de segurança exigidos pela linha de base FedRAMP alta. A matriz fornece informações sobre quais controles são cobertos pela arquitetura do aplicativo Web PaaS, incluindo descrições detalhadas de como a implementação atende aos requisitos de cada controle abordado.

## <a name="guidance-and-recommendations"></a>Diretrizes e recomendações
### <a name="vpn-and-expressroute"></a>VPN e ExpressRoute
Um túnel VPN seguro ou o [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) precisa ser configurado para estabelecer com segurança uma conexão com os recursos implantados como parte dessa arquitetura de referência do aplicativo Web PaaS. Ao configurar adequadamente uma VPN ou ExpressRoute, os clientes podem adicionar uma camada de proteção para os dados em trânsito.

Ao implementar um túnel VPN seguro com o Azure, uma conexão VPN entre uma rede local e uma rede virtual do Azure pode ser criada. Essa conexão ocorre pela Internet e permite aos clientes "encapsular" informações com segurança dentro de um link criptografado entre a rede do cliente e o Azure. A VPN site a site é uma tecnologia segura e madura, que foi implantada por empresas de todos os tamanhos por décadas. O [modo de encapsulamento IPSec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) é usado nessa opção como um mecanismo de criptografia.

Como o tráfego dentro do túnel VPN atravessa a Internet com uma VPN site a site, a Microsoft oferece outra opção de conexão ainda mais segura. O Azure ExpressRoute é um link WAN dedicado entre o Azure e um local ou um provedor de hospedagem do Exchange. Como as conexões do ExpressRoute não passam pela Internet, essas conexões oferecem mais confiabilidade, velocidades mais rápidas, latências menores e mais segurança do que as conexões típicas pela Internet. Além disso, como essa é uma conexão direta do provedor de telecomunicação do cliente, os dados não viajam pela Internet e, portanto, não são expostos a ele.

As práticas recomendadas para implementar uma rede híbrida segura que estende uma rede local para o Azure estão [disponíveis](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## <a name="disclaimer"></a>Exclusão de Responsabilidade

 - Este documento é apenas para fins informativos. A MICROSOFT NÃO OFERECE NENHUMA GARANTIA, EXPRESSA, IMPLÍCITA OU ESTATUTÁRIA, QUANTO ÀS INFORMAÇÕES CONTIDAS NESTE DOCUMENTO. Este documento é fornecido "no estado em que se encontra". As informações e as exibições expressas neste documento, incluindo URLs e outras referências de site da Internet, podem ser alteradas sem aviso prévio. Os clientes que lêem este documento trazem o risco de usá-lo.
 - Este documento não fornece aos clientes nenhum direito legal sobre qualquer propriedade intelectual em qualquer produto ou solução da Microsoft.
 - Os clientes podem copiar e usar este documento para fins de referência interna.
 - Determinadas recomendações neste documento podem resultar em maior uso de recursos de computação, rede ou dados no Azure, e podem aumentar os custos de assinatura ou de licença do Azure do cliente.
 - Essa arquitetura destina-se a servir como base para os clientes se ajustarem aos requisitos específicos e não devem ser usados no estado em que se encontram em um ambiente de produção.
 - Este documento é desenvolvido como uma referência e não deve ser usado para definir todos os meios pelos quais um cliente pode atender a requisitos e regulamentos específicos de conformidade. Os clientes devem buscar o suporte legal de sua organização em implementações aprovadas do cliente.
