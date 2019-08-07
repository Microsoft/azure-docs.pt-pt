---
title: Aplicativo Web Blueprint de Segurança e Conformidade do Azure-IaaS para NIST SP 800-171
description: Blueprint de Segurança e Conformidade do Azure-NIST SP 800-171 do aplicativo Web IaaS
services: security
author: jomolesk
ms.assetid: 1f1ad27f-32c3-4e76-abb9-ea768d01747f
ms.service: security
ms.topic: article
ms.date: 07/31/2018
ms.author: jomolesk
ms.openlocfilehash: 9e5c894cedcbfd006d9406ce2c07fc0b17033d7c
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/05/2019
ms.locfileid: "68781038"
---
# <a name="azure-security-and-compliance-blueprint---iaas-web-application-for-nist-sp-800-171"></a>Aplicativo Web Blueprint de Segurança e Conformidade do Azure-IaaS para NIST SP 800-171

## <a name="overview"></a>Descrição geral
A [publicação especial do NIST 800-171](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) fornece diretrizes para proteger as informações não classificadas (CUI) controladas que residem em sistemas de informações e organizações não federais. O NIST SP 800-171 estabelece 14 famílias de requisitos de segurança para proteger a confidencialidade da CUI.

Este Blueprint de Segurança e Conformidade do Azure fornece diretrizes para ajudar os clientes a implantar uma arquitetura de aplicativo Web no Azure que implementa um subconjunto de controles NIST SP 800-171. Esta solução demonstra maneiras em que os clientes podem atender aos requisitos específicos de segurança e conformidade. Ele também serve como base para os clientes criarem e configurarem seus próprios aplicativos Web no Azure.

Essa arquitetura de referência, o guia de implementação associado e o modelo de ameaça devem servir como base para os clientes se adaptarem aos seus requisitos específicos. Eles não devem ser usados no estado em que se encontram em um ambiente de produção. Implantar essa arquitetura sem modificação é insuficiente para atender totalmente aos requisitos do NIST SP 800-171. Os clientes são responsáveis por realizar avaliações apropriadas de segurança e conformidade de qualquer solução criada usando essa arquitetura. Os requisitos podem variar com base nas especificidades da implementação de cada cliente.

## <a name="architecture-diagram-and-components"></a>Diagrama e componentes da arquitetura
Este Blueprint de Segurança e Conformidade do Azure implanta uma arquitetura de referência para um aplicativo Web IaaS com um back-end SQL Server. A arquitetura inclui uma camada da Web, camada de dados, Active Directory infraestrutura, Aplicativo Azure gateway e Azure Load Balancer. As máquinas virtuais (VMs) implantadas na Web e nas camadas de dados são configuradas em um conjunto de disponibilidade. SQL Server instâncias são configuradas em um grupo de disponibilidade Always On para alta disponibilidade. As VMs estão ingressadas no domínio. Active Directory diretivas de grupo impõem configurações de segurança e conformidade no nível do sistema operacional.

A solução inteira é criada com base no armazenamento do Azure, que os clientes configuram do portal do Azure. O armazenamento criptografa todos os dados com Criptografia do Serviço de Armazenamento para manter a confidencialidade dos dados em repouso. O armazenamento com redundância geográfica garante que um evento adverso na data center principal do cliente não resulte em perda de dados. Uma segunda cópia é armazenada em um local separado a centenas de quilômetros de distância.

Para aumentar a segurança, todos os recursos nesta solução são gerenciados como um grupo de recursos por meio de Azure Resource Manager. O RBAC (controle de acesso baseado em função) do Azure Active Directory (Azure AD) é usado para controlar o acesso a recursos e chaves implantados no Azure Key Vault. A integridade do sistema é monitorada por meio de Azure Monitor. Os clientes configuram ambos os serviços de monitoramento para capturar logs. A integridade do sistema é exibida em um único painel que é fácil de usar.

Um host de bastiões de gerenciamento fornece uma conexão segura para os administradores acessarem recursos implantados. *A Microsoft recomenda que você configure uma conexão VPN ou Azure ExpressRoute para gerenciamento e importação de dados na sub-rede da arquitetura de referência.*


![Diagrama de arquitetura de referência do aplicativo Web IaaS para NIST SP 800-171](images/nist171-iaaswa-architecture.png "Diagrama de arquitetura de referência do aplicativo Web IaaS para NIST SP 800-171")

Essa solução usa os seguintes serviços do Azure. Para obter mais informações, consulte a seção [arquitetura de implantação](#deployment-architecture) .

- Máquinas Virtuais do Azure
    - (1) gerenciamento/bastiões (Windows Server 2016 Datacenter)
    - (2) Active Directory controlador de domínio (Windows Server 2016 Datacenter)
    - (2) SQL Server nó de cluster (SQL Server 2017 no Windows Server 2016)
    - (2) Web/IIS (Windows Server 2016 Datacenter)
- Rede Virtual do Azure
    - (1)/16 rede
    - (5)/24 redes
    - (5) grupos de segurança de rede
- Conjuntos de disponibilidade
    - (1) Active Directory controladores de domínio
    - (1) nós de cluster do SQL
    - (1) Web/IIS
- Gateway de Aplicação do Azure
    - (1) Firewall do aplicativo Web
        - Modo de firewall: prevenção
        - Conjunto de regras: OWASP 3.0
        - Porta do ouvinte: 443
- Azure Active Directory
- Azure Key Vault
- Balanceador de Carga do Azure
- Azure Monitor (logs)
- Azure Resource Manager
- Centro de Segurança do Azure
- Storage do Azure
- Automatização do Azure
- Testemunha de nuvem
- Cofre dos Serviços de Recuperação

## <a name="deployment-architecture"></a>Arquitetura de implantação
A seção a seguir detalha os elementos de implantação e implementação.

**Host bastião**: O host de bastiões é o ponto único de entrada que os usuários podem usar para acessar os recursos implantados nesse ambiente. O host bastião fornece uma conexão segura para recursos implantados, permitindo apenas o tráfego remoto de endereços IP públicos em uma lista segura. Para permitir o tráfego de área de trabalho remota, a origem do tráfego deve ser definida no grupo de segurança de rede (NSG).

Essa solução cria uma VM como um host de bastiões ingressado no domínio com as seguintes configurações:
-   [Extensão Antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware).
-   [Extensão de diagnóstico do Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template).
-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) usando Key Vault.
-   Uma [política](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) de desligamento automático para reduzir o consumo de recursos da VM quando não estiver em uso.
-   O [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) está habilitado para que as credenciais e outros segredos sejam executados em um ambiente protegido isolado do sistema operacional em execução.

### <a name="virtual-network"></a>Rede virtual
A arquitetura define uma rede virtual privada com um espaço de endereço de 10.200.0.0/16.

**Grupos de segurança de rede**: Esta solução implanta recursos em uma arquitetura com sub-redes separadas para Web, banco de dados, Active Directory e gerenciamento dentro de uma rede virtual. As sub-redes são separadas logicamente por regras NSG aplicadas às sub-redes individuais. As regras restringem o tráfego entre sub-redes somente para isso necessário para a funcionalidade de gerenciamento e do sistema.

Consulte a configuração de [NSGs](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) implantada com esta solução. As organizações podem configurar o NSGs editando o arquivo anterior usando [esta documentação](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) como um guia.

Cada uma das sub-redes tem um NSG dedicado:
- Um NSG para o gateway de aplicativo (LBNSG)
- Um NSG para o Bastion host (MGTNSG)
- Um NSG para os controladores de domínio primário e de backup (ADNSG)
- Um NSG para SQL Servers e SQLNSG (testemunha de nuvem)
- Um NSG para a camada da Web (WEBNSG)

### <a name="data-in-transit"></a>Dados em trânsito
O Azure criptografa todas as comunicações de e para data centers do Azure por padrão. Além disso, todas as transações para armazenamento por meio do portal do Azure ocorrem via HTTPS.

### <a name="data-at-rest"></a>Dados inativos
A arquitetura protege os dados em repouso por meio de várias medidas. Essas medidas incluem criptografia e auditoria de banco de dados.

**Armazenamento do Azure**: Para atender aos requisitos de dados criptografados em repouso, todo o [armazenamento](https://azure.microsoft.com/services/storage/) usa [criptografia do serviço de armazenamento](https://docs.microsoft.com/azure/storage/storage-service-encryption). Esse recurso ajuda a proteger e proteger os dados em suporte aos compromissos de segurança organizacional e aos requisitos de conformidade definidos pelo NIST SP 800-171.

**Azure Disk Encryption**: A criptografia de disco é usada para criptografar discos de VM IaaS do Windows. A [criptografia de disco](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) usa o recurso BitLocker do Windows para fornecer criptografia de volume para o sistema operacional e discos de dados. A solução é integrada com Key Vault para ajudar a controlar e gerenciar as chaves de criptografia de disco.

**SQL Server**: A instância de SQL Server usa as seguintes medidas de segurança de banco de dados:
-   [SQL Server auditoria](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine?view=sql-server-2017) rastreia eventos de banco de dados e os grava em logs de auditoria.
-   A Transparent [Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) executa criptografia e descriptografia em tempo real do banco de dados, backups associados e arquivos de log de transações para proteger informações em repouso. A Transparent Data Encryption fornece garantia de que os dados armazenados não estão sujeitos a acesso não autorizado.
-   [As regras de firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) impedem todo o acesso a servidores de banco de dados até que sejam concedidas permissões adequadas A firewall concede acesso a bases de dados com base no endereço IP de origem de cada pedido.
-   As [colunas criptografadas](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-wizard?view=sql-server-2017) garantem que os dados confidenciais nunca apareçam como texto sem formatação no sistema de banco de dados. Depois que a criptografia de dados é habilitada, somente aplicativos cliente ou servidores de aplicativos com acesso às chaves podem acessar dados de texto sem formatação.
- A [máscara de dados dinâmicos](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking?view=sql-server-2017) limita a exposição de dados confidenciais mascarando os dados para usuários ou aplicativos sem privilégios. Ele pode descobrir dados potencialmente confidenciais automaticamente e sugerir as máscaras apropriadas a serem aplicadas. O mascaramento de dados dinâmicos ajuda a reduzir o acesso para que os dados confidenciais não saiam do banco por meio de acesso não autorizado. *Os clientes são responsáveis por ajustar as configurações para aderir ao seu esquema de banco de dados.*

### <a name="identity-management"></a>Gestão de identidades
As tecnologias a seguir fornecem recursos para gerenciar o acesso aos dados no ambiente do Azure:
-   O [Azure ad](https://azure.microsoft.com/services/active-directory/) é o serviço de gerenciamento de identidade e diretório multilocatário baseado em nuvem da Microsoft. Todos os usuários dessa solução são criados no Azure AD e incluem os usuários que acessam a instância de SQL Server.
-   A autenticação para o aplicativo é executada usando o Azure AD. Para obter mais informações, consulte como [integrar aplicativos com o Azure ad](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).
-   O [RBAC do Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) pode ser usado por administradores para definir permissões de acesso refinadas para conceder apenas a quantidade de acesso que os usuários precisam para executar seus trabalhos. Em vez de fornecer a cada usuário permissões irrestritas para recursos do Azure, os administradores podem permitir apenas determinadas ações para acessar dados. O acesso à assinatura é limitado ao administrador da assinatura.
- [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) pode ser usado por clientes para minimizar o número de usuários que têm acesso a determinados recursos. Os administradores podem usar Azure AD Privileged Identity Management para descobrir, restringir e monitorar identidades com privilégios e seu acesso aos recursos. Essa funcionalidade também pode ser usada para impor acesso administrativo sob demanda e Just-in-time quando necessário.
- [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) detecta possíveis vulnerabilidades que afetam as identidades de uma organização. Ele configura as respostas automatizadas para detectar ações suspeitas relacionadas às identidades de uma organização. Ele também investiga incidentes suspeitos para tomar as medidas apropriadas para resolvê-los.

### <a name="security"></a>Segurança
**Gerenciamento de segredos**: A solução usa [Key Vault](https://azure.microsoft.com/services/key-vault/) para o gerenciamento de chaves e segredos. Key Vault ajuda a proteger chaves criptográficas e segredos usados por aplicativos e serviços em nuvem. Os recursos de Key Vault a seguir ajudam os clientes a proteger os dados:
- As políticas de acesso avançadas são configuradas de acordo com a necessidade.
- Key Vault políticas de acesso são definidas com as permissões mínimas necessárias para chaves e segredos.
- Todas as chaves e segredos em Key Vault têm datas de expiração.
- Todas as chaves em Key Vault são protegidas por módulos de segurança de hardware especializados. O tipo de chave é uma chave RSA de segurança de hardware-módulo protegido de 2048 bits.
- Todos os usuários e identidades recebem as permissões mínimas necessárias usando o RBAC.
- Os logs de diagnóstico para Key Vault estão habilitados com um período de retenção de pelo menos 365 dias.
- As operações de criptografia permitidas para chaves são restritas às necessárias.
- A solução é integrada com Key Vault para gerenciar chaves e segredos de criptografia de disco de VM IaaS.

**Gerenciamento**de patches: As VMs do Windows implantadas como parte dessa arquitetura de referência são configuradas por padrão para receber atualizações automáticas do serviço Windows Update. Essa solução também inclui o serviço de [automação do Azure](https://docs.microsoft.com/azure/automation/automation-intro) por meio do qual implantações atualizadas podem ser criadas para corrigir as VMs quando necessário.

**Proteção contra malware**: [O Microsoft Antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware) para VMs fornece recursos de proteção em tempo real que ajudam a identificar e remover vírus, spyware e outros softwares mal-intencionados. Os clientes podem configurar alertas que geram quando softwares mal-intencionados ou indesejados conhecidos tentam ser instalados ou executados em VMs protegidas.

**Central de segurança do Azure**: Com a [central de segurança](https://docs.microsoft.com/azure/security-center/security-center-intro), os clientes podem aplicar e gerenciar centralmente políticas de segurança entre cargas de trabalho, limitar a exposição a ameaças e detectar e responder a ataques. A central de segurança também acessa as configurações existentes dos serviços do Azure para fornecer recomendações de configuração e serviço para ajudar a melhorar a postura de segurança e proteger os dados.

A central de segurança usa uma variedade de recursos de detecção para alertar os clientes sobre possíveis ataques direcionados a seus ambientes. Estes alertas contêm informações valiosas sobre o que acionou o alerta, os recursos afetados e a origem do ataque. A central de segurança tem um conjunto de [alertas de segurança](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) predefinidos que são disparados quando ocorre uma ameaça ou atividade suspeita. Os clientes podem usar [regras de alerta personalizadas](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) para definir novos alertas de segurança com base nos dados que já foram coletados de seu ambiente.

A central de segurança fornece incidentes e alertas de segurança priorizados. A central de segurança torna mais simples para os clientes descobrir e resolver problemas potenciais de segurança. Um [relatório de inteligência contra ameaças](https://docs.microsoft.com/azure/security-center/security-center-threat-report) é gerado para cada ameaça detectada. As equipes de resposta a incidentes podem usar os relatórios ao investigar e corrigir ameaças.

Essa arquitetura de referência usa o recurso de [avaliação de vulnerabilidade](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations) na central de segurança. Depois de configurado, um agente de parceiro (por exemplo, Qualys) relata dados de vulnerabilidade para a plataforma de gerenciamento do parceiro. Por sua vez, a plataforma de gestão do parceiro fornece dados de vulnerabilidades e de estado de funcionamento de volta ao Centro de Segurança. Os clientes podem usar essas informações para identificar rapidamente VMs vulneráveis.

**Gateway de aplicativo Azure**: A arquitetura reduz o risco de vulnerabilidades de segurança usando um gateway de aplicativo com um firewall do aplicativo Web configurado e o conjunto de regras OWASP habilitado. Os recursos adicionais incluem:

- [SSL de ponta a ponta](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).
- Habilitar [descarregamento SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal).
- Desabilite o [TLS v 1.0 e o v 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).
- [Firewall do aplicativo Web](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (modo de prevenção).
- [Modo de prevenção](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) com conjunto de regras OWASP 3,0.
- Habilite o [log de diagnóstico](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).
- [Investigações de integridade personalizadas](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-gateway-portal).
- A [central de segurança](https://azure.microsoft.com/services/security-center) e o [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) fornecem proteção e notificações adicionais. A central de segurança também fornece um sistema de reputação.

### <a name="business-continuity"></a>Continuidade do negócio

**Elevada disponibilidade**: A solução implanta todas as VMs em um [conjunto de disponibilidade](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Os conjuntos de disponibilidade garantem que as VMs sejam distribuídas entre vários clusters de hardware isolados para melhorar a disponibilidade. Pelo menos uma VM está disponível durante um evento de manutenção planejado ou não planejado, que atende ao SLA de 99,95% do Azure.

**Cofre dos serviços de recuperação**: O [cofre dos serviços de recuperação](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) hospeda dados de backup e protege todas as configurações de máquinas virtuais do Azure nessa arquitetura. Com um cofre dos serviços de recuperação, os clientes podem restaurar arquivos e pastas de uma VM IaaS sem restaurar toda a VM. Esse processo acelera os tempos de restauração.

**Testemunha de nuvem**: A [testemunha em nuvem](https://docs.microsoft.com/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness) é um tipo de testemunha de quorum de cluster de failover no Windows Server 2016 que usa o Azure como ponto de arbitragem. A testemunha de nuvem, como qualquer outra testemunha de quorum, obtém um voto e pode participar dos cálculos de quorum. Ele usa o armazenamento de BLOBs padrão do Azure disponível publicamente. Essa organização elimina a sobrecarga de manutenção extra das VMs hospedadas em uma nuvem pública.

### <a name="logging-and-auditing"></a>Registro em log e auditoria

Os serviços do Azure registram extensivamente a atividade do sistema e do usuário, bem como a integridade do sistema:
- **Logs de atividade**: [Os logs de atividade](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) fornecem informações sobre as operações executadas nos recursos em uma assinatura. Os logs de atividades podem ajudar a determinar o iniciador de uma operação, a hora de ocorrência e o status.
- **Logs de diagnóstico**: Os [logs de diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) incluem todos os logs emitidos por cada recurso. Esses logs incluem logs do sistema de eventos do Windows, logs de armazenamento, Key Vault logs de auditoria e acesso do gateway de aplicativo e logs de firewall. Todos os logs de diagnóstico gravam em uma conta de armazenamento do Azure centralizada e criptografada para arquivamento. Os usuários podem configurar o período de retenção, até 730 dias, para atender a seus requisitos específicos.

**Logs de Azure monitor**: Esses logs são consolidados em [logs de Azure monitor](https://azure.microsoft.com/services/log-analytics/) para processamento, armazenamento e relatórios de Dashboard. Depois que os dados são coletados, eles são organizados em tabelas separadas para cada tipo de dados dentro dos espaços de trabalho Log Analytics. Dessa forma, todos os dados podem ser analisados juntos, independentemente de sua fonte original. A central de segurança se integra aos logs de Azure Monitor. Os clientes podem usar consultas Kusto para acessar seus dados de eventos de segurança e combiná-los com dados de outros serviços.

As seguintes [soluções de monitoramento](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) do Azure estão incluídas como parte dessa arquitetura:
-   [Avaliação de Active Directory](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): A solução de verificação de integridade Active Directory avalia o risco e a integridade de ambientes de servidor em intervalos regulares. Ele fornece uma lista priorizada de recomendações específicas para a infraestrutura de servidor implantada.
- [Avaliação do SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): A solução de verificação da integridade do SQL avalia o risco e a integridade de ambientes de servidor em intervalos regulares. Ele fornece aos clientes uma lista priorizada de recomendações específicas para a infraestrutura de servidor implantada.
- [Integridade do agente](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): A solução Integridade do Agente relata Quantos agentes são implantados e sua distribuição geográfica. Ele também relata Quantos agentes não respondem e o número de agentes que enviam dados operacionais.
-   [Análise do log de atividades](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): A solução Análise do Log de Atividades auxilia na análise dos logs de atividade do Azure em todas as assinaturas do Azure para um cliente.

**Automação do Azure**: A [automação](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) armazena, executa e gerencia runbooks. Nesta solução, os runbooks ajudam a coletar logs de SQL Server. Os clientes podem usar a solução de [controle de alterações](https://docs.microsoft.com/azure/automation/automation-change-tracking) de automação para identificar facilmente as alterações no ambiente.

**Azure monitor**: O [Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) ajuda os usuários a controlar o desempenho, manter a segurança e identificar tendências. As organizações podem usá-lo para auditar, criar alertas e arquivar dados. Eles também podem acompanhar chamadas de API em seus recursos do Azure.

## <a name="threat-model"></a>Modelo de ameaça

O diagrama de fluxo de dados para esta arquitetura de referência está disponível para [Download](https://aka.ms/nist171-iaaswa-tm) ou pode ser encontrado aqui. Esse modelo pode ajudar os clientes a entender os pontos de risco potencial na infraestrutura do sistema quando fazem modificações.

![Aplicativo Web IaaS para modelo de ameaça NIST SP 800-171](images/nist171-iaaswa-threat-model.png "Aplicativo Web IaaS para modelo de ameaça NIST SP 800-171")

## <a name="compliance-documentation"></a>Documentação de conformidade

A [matriz de responsabilidade do cliente do Blueprint de segurança E conformidade do Azure NIST SP 800-171](https://aka.ms/nist171-crm) lista todos os controles de segurança exigidos pelo NIST SP 800-171. Essa matriz detalha se a implementação de cada controle é de responsabilidade da Microsoft, do cliente ou do compartilhamento entre os dois.

A [matriz de implementação de controle de aplicativo Web IaaS sp 800-171 de Blueprint de segurança e conformidade do Azure-NIST](https://aka.ms/nist171-iaaswa-cim) fornece informações sobre quais controles do nist SP 800-171 são tratados pela arquitetura do aplicativo Web IaaS. Ele inclui descrições detalhadas de como a implementação atende aos requisitos de cada controle abordado.

## <a name="guidance-and-recommendations"></a>Diretrizes e recomendações
### <a name="vpn-and-expressroute"></a>VPN e ExpressRoute
Um túnel VPN seguro ou o [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) deve ser configurado para estabelecer com segurança uma conexão com os recursos implantados como parte dessa arquitetura de referência do aplicativo Web IaaS. Ao configurar adequadamente uma VPN ou ExpressRoute, os clientes podem adicionar uma camada de proteção para os dados em trânsito.

Ao implementar um túnel VPN seguro com o Azure, uma conexão privada virtual entre uma rede local e uma rede virtual do Azure pode ser criada. Esta conexão ocorre pela Internet. Os clientes podem usar essa conexão para obter informações de "túnel" com segurança dentro de um link criptografado entre a rede do cliente e o Azure. A VPN site a site é uma tecnologia segura e madura, que foi implantada por empresas de todos os tamanhos por décadas. O [modo de encapsulamento IPSec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) é usado nessa opção como um mecanismo de criptografia.

Como o tráfego no túnel VPN atravessa a Internet com uma VPN site a site, a Microsoft oferece outra opção de conexão ainda mais segura. O ExpressRoute é um link WAN dedicado entre o Azure e um local ou um provedor de hospedagem do Exchange. As conexões do ExpressRoute se conectam diretamente ao provedor de telecomunicação do cliente. Como resultado, os dados não viajam pela Internet e não são expostos a ele. Essas conexões oferecem mais confiabilidade, velocidades mais rápidas, latências menores e maior segurança do que as conexões típicas. 

As práticas recomendadas para implementar uma rede híbrida segura que estende uma rede local para o Azure estão [disponíveis](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## <a name="disclaimer"></a>Exclusão de Responsabilidade

- Este documento é apenas para fins informativos. A MICROSOFT NÃO OFERECE NENHUMA GARANTIA, EXPRESSA, IMPLÍCITA OU ESTATUTÁRIA, QUANTO ÀS INFORMAÇÕES CONTIDAS NESTE DOCUMENTO. Este documento é fornecido "no estado em que se encontra". As informações e as exibições expressas neste documento, incluindo URLs e outras referências de site da Internet, podem ser alteradas sem aviso prévio. Os clientes que lêem este documento trazem o risco de usá-lo. 
- Este documento não fornece aos clientes nenhum direito legal sobre qualquer propriedade intelectual em qualquer produto ou solução da Microsoft. 
- Os clientes podem copiar e usar este documento para fins de referência interna. 
- Determinadas recomendações neste documento podem resultar em maior uso de recursos de computação, rede ou dados no Azure, e podem aumentar os custos de assinatura ou de licença do Azure do cliente. 
- Essa arquitetura destina-se a servir como base para os clientes se ajustarem aos requisitos específicos e não devem ser usados no estado em que se encontram em um ambiente de produção.
- Este documento é desenvolvido como uma referência e não deve ser usado para definir todos os meios pelos quais um cliente pode atender a requisitos e regulamentos específicos de conformidade. Os clientes devem buscar o suporte legal de sua organização em implementações aprovadas do cliente.
