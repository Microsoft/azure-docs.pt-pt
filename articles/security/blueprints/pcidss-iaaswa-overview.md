---
title: Aplicativo Web Blueprint de Segurança e Conformidade do Azure-IaaS para PCI DSS
description: Aplicativo Web Blueprint de Segurança e Conformidade do Azure-IaaS para PCI DSS
services: security
author: meladie
ms.assetid: 9c825380-2abe-4fdd-800c-59d1fed1780b
ms.service: security
ms.topic: article
ms.date: 07/03/2018
ms.author: meladie
ms.openlocfilehash: e8e9faa16474335463cbdccd9fe7b3d749be0f25
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/05/2019
ms.locfileid: "68781014"
---
# <a name="azure-security-and-compliance-blueprint-iaas-web-application-for-pci-dss"></a>Esquema de Segurança e Conformidade do Azure: Aplicativo Web IaaS para PCI DSS

## <a name="overview"></a>Descrição geral

Este Blueprint de Segurança e Conformidade do Azure fornece diretrizes para a implantação de um ambiente de infraestrutura como serviço (IaaS) compatível com os padrões de segurança de dados do setor de cartão de pagamento (PCI DSS 3,2) adequado para a coleta, o armazenamento e a recuperação de dados do titular do cartão. Ele apresenta uma arquitetura de referência comum e demonstra o tratamento adequado de dados de cartão de crédito (incluindo o número do cartão, a expiração e os dados de verificação) em um ambiente seguro, em conformidade e de várias camadas. Este projeto ilustra uma solução de ponta a ponta para atender às necessidades de organizações que buscam uma abordagem baseada em nuvem para reduzir a carga e o custo da implantação.

Essa arquitetura de referência, guia de implementação e modelo de ameaça fornecem uma base para os clientes cumprirem os requisitos de PCI DSS 3,2. Essa solução fornece uma linha de base para ajudar os clientes a implantar cargas de trabalho no Azure de uma maneira compatível com o PCI DSS 3,2; no entanto, essa solução não deve ser usada no estado em que se encontra em um ambiente de produção, pois é necessária uma configuração adicional.

A obtenção de PCI DSS-conformidade exige que um QSA (Assessornt Security) qualificado certificada de uma solução de cliente de produção. Os clientes são responsáveis por realizar avaliações apropriadas de segurança e conformidade de qualquer solução criada usando essa arquitetura, já que os requisitos podem variar com base nas especificidades da implementação de cada cliente.

## <a name="architecture-diagram-and-components"></a>Diagrama e componentes da arquitetura

Esta solução implanta uma arquitetura de referência para um aplicativo Web IaaS com um back-end SQL Server. A arquitetura inclui uma camada da Web, camada de dados, Active Directory infraestrutura, gateway de aplicativo e Load Balancer. As máquinas virtuais implantadas na Web e nas camadas de dados são configuradas em um conjunto de disponibilidade e SQL Server instâncias são configuradas em um grupo de disponibilidade Always On para alta disponibilidade. As máquinas virtuais são ingressadas no domínio e Active Directory políticas de grupo são usadas para impor as configurações de segurança e conformidade no nível do sistema operacional. Um host de bastiões de gerenciamento fornece uma conexão segura para os administradores acessarem recursos implantados. **O Azure recomenda configurar uma conexão VPN ou ExpressRoute para gerenciamento e importação de dados na sub-rede da arquitetura de referência.**

![Diagrama de arquitetura de referência de aplicativo Web IaaS para PCI DSS](images/pcidss-iaaswa-architecture.png "Diagrama de arquitetura de referência de aplicativo Web IaaS para PCI DSS")

Essa solução usa os seguintes serviços do Azure. Os detalhes da arquitetura de implantação estão localizados na seção [arquitetura de implantação](#deployment-architecture) .

- Conjuntos de Disponibilidade
    - (1) Active Directory controladores de domínio
    - (1) nós de cluster do SQL
    - (1) Web/IIS
- Azure Active Directory
- Gateway de Aplicação do Azure
    - (1) Firewall do aplicativo Web
        - Modo de firewall: prevenção
        - Conjunto de regras: OWASP 3.0
        - Porta do ouvinte: 443
- Azure Key Vault
- Balanceador de Carga do Azure
- Azure Monitor
- Azure Resource Manager
- Centro de Segurança do Azure
- Storage do Azure
    - (7) contas de armazenamento com redundância geográfica
- Máquinas Virtuais do Azure
    - (1) gerenciamento/bastiões (Windows Server 2016 Datacenter)
    - (2) Active Directory controlador de domínio (Windows Server 2016 Datacenter)
    - (2) SQL Server nó de cluster (SQL Server 2017 no Windows Server 2016)
    - (2) Web/IIS (Windows Server 2016 Datacenter)
- Rede Virtual do Azure
    - (1)/16 rede
    - (5)/24 redes
    - (5) grupos de segurança de rede
- Testemunha de nuvem
- Cofre dos serviços de recuperação

## <a name="deployment-architecture"></a>Arquitetura de implantação

A seção a seguir detalha os elementos de implantação e implementação.

**Host bastião**: O host de bastiões é o ponto único de entrada que permite aos usuários acessar os recursos implantados nesse ambiente. O host de bastiões fornece uma conexão segura com os recursos implantados permitindo apenas o tráfego remoto de endereços IP públicos em uma lista segura. Para permitir o tráfego da área de trabalho remota (RDP), a origem do tráfego precisa ser definida no grupo de segurança de rede.

Essa solução cria uma máquina virtual como um host de bastiões ingressado no domínio com as seguintes configurações:
-   [Extensão de antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware)
-   [Extensão de Diagnóstico do Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) usando Azure Key Vault
-   Uma [política](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) de desligamento automático para reduzir o consumo de recursos de máquina virtual quando não estiver em uso
-   O [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) habilitado para que as credenciais e outros segredos sejam executados em um ambiente protegido isolado do sistema operacional em execução

### <a name="virtual-network"></a>Rede virtual

A arquitetura define uma rede virtual privada com um espaço de endereço de 10.200.0.0/16.

**Grupos de segurança de rede**: Esta solução implanta recursos em uma arquitetura com uma sub-rede da Web, uma sub-rede de banco de dados, uma sub-rede Active Directory e uma sub-rede de gerenciamento separadas dentro de uma rede virtual. As sub-redes são separadas logicamente pelas regras do grupo de segurança de rede aplicadas às sub-redes individuais para restringir o tráfego entre sub-redes apenas para isso necessário para a funcionalidade de gerenciamento e do sistema.

Consulte a configuração de [grupos de segurança de rede](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) implantados com esta solução. As organizações podem configurar grupos de segurança de rede editando o arquivo acima usando [esta documentação](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) como guia.

Cada uma das sub-redes tem um grupo de segurança de rede dedicado:
- 1 grupo de segurança de rede para o gateway de aplicativo (LBNSG)
- 1 grupo de segurança de rede para host bastião (MGTNSG)
- 1 grupo de segurança de rede para controladores de domínio primários e de backup (ADNSG)
- 1 grupo de segurança de rede para servidores SQL e testemunha de nuvem (SQLNSG)
- 1 grupo de segurança de rede para a camada da Web (WEBNSG)

### <a name="data-in-transit"></a>Dados em trânsito

Por padrão, o Azure criptografa todas as comunicações entre os data centers do Azure. Além disso, todas as transações para o armazenamento do Azure por meio do portal do Azure ocorrem via HTTPS.

### <a name="data-at-rest"></a>Dados inativos

A arquitetura protege os dados em repouso por meio de várias medidas, incluindo a criptografia e a auditoria do banco.

**Armazenamento do Azure**: Para atender aos requisitos de dados criptografados em repouso, todo o [armazenamento do Azure](https://azure.microsoft.com/services/storage/) usa [criptografia do serviço de armazenamento](https://docs.microsoft.com/azure/storage/storage-service-encryption). Isso ajuda a proteger e proteger os dados do titular do cartão para dar suporte a compromissos de segurança organizacional e requisitos de conformidade definidos pelo PCI DSS 3,2.

**Azure Disk Encryption**: Azure Disk Encryption é usado para criptografar discos de máquina virtual IaaS do Windows. [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) aproveita o recurso BitLocker do Windows para fornecer criptografia de volume para o sistema operacional e discos de dados. A solução é integrada com Azure Key Vault para ajudar a controlar e gerenciar as chaves de criptografia de disco.

**SQL Server**: A instância de SQL Server usa as seguintes medidas de segurança de banco de dados:
- [Active Directory autenticação e autorização](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) permitem o gerenciamento de identidades de usuários de banco de dados e outros serviços da Microsoft em um local central.
-   A [auditoria do banco de dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) rastreia eventos de banco de dados e os grava em um log de auditoria em uma conta de armazenamento do Azure.
-   O banco de dados SQL está configurado para usar [Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), que executa criptografia e descriptografia em tempo real do banco de dados, backups associados e arquivos de log de transações para proteger informações em repouso. Transparent Data Encryption fornece garantia de que os dados de titulares armazenados não estão sujeitos a acesso não autorizado.
-   [As regras de firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) impedem todo o acesso a servidores de banco de dados até que sejam concedidas permissões adequadas A firewall concede acesso a bases de dados com base no endereço IP de origem de cada pedido.
-   A [detecção de ameaças do SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) permite a detecção e a resposta a ameaças potenciais à medida que elas ocorrem, fornecendo alertas de segurança para atividades suspeitas de banco de dados, vulnerabilidades potenciais, ataques de injeção de SQL e padrões de acesso de banco de dados anormais
-   [Always Encrypted colunas](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) garantem que os dados confidenciais do titular do cartão nunca apareçam como texto sem formatação no sistema de banco de dados. Depois de habilitar a criptografia de dados, somente aplicativos cliente ou servidores de aplicativos com acesso às chaves podem acessar dados de texto sem formatação.
- O recurso de [Propriedades estendidas](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql) pode ser usado para descontinuar o processamento de entidades de dados, pois permite que os usuários adicionem Propriedades personalizadas a objetos de banco de dados e marquem como "descontinuados" para dar suporte à lógica do aplicativo para evitar o processamento de dados do titular do cartão.
- A [segurança em nível de linha](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) permite que os usuários definam políticas para restringir o acesso aos dados para o processamento descontinuado.
- O mascaramento de [dados dinâmicos do SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) limita a exposição de dados sensível ao titular do cartão mascarando os dados para usuários ou aplicativos sem privilégios. A máscara de dados dinâmicos pode descobrir dados potencialmente confidenciais automaticamente e sugerir que as máscaras apropriadas sejam aplicadas. Isso ajuda a identificar e reduzir o acesso a dados de titulares de cartão, de modo que ele não sai do banco por acesso não autorizado. Os clientes são responsáveis por ajustar as configurações de mascaramento de dados dinâmicos para aderir ao seu esquema de banco de dado.

### <a name="identity-management"></a>Gestão de identidades

As tecnologias a seguir fornecem recursos para gerenciar o acesso aos dados no ambiente do Azure:

- [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) é o&#39;serviço de gerenciamento de identidade e diretório multilocatário baseado em nuvem da Microsoft. Todos os usuários desta solução são criados no Azure Active Directory, incluindo usuários que acessam o banco de dados SQL do Azure.
- A autenticação para o aplicativo é executada usando Azure Active Directory. Para obter mais informações, consulte [integrando aplicativos com Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Além disso, a criptografia de coluna de banco de dados usa Azure Active Directory para autenticar o aplicativo no banco de dados SQL do Azure. Para obter mais informações, Confira como [proteger dados confidenciais no Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
- O [controle de acesso baseado em função do Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) permite que os administradores definam permissões de acesso refinadas para conceder apenas a quantidade de acesso que os usuários precisam para executar seus trabalhos. Em vez de fornecer a cada usuário permissão irrestrita para recursos do Azure, os administradores podem permitir apenas determinadas ações para acessar dados. O acesso à assinatura é limitado ao administrador da assinatura.
- [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) permite que os clientes minimizem o número de usuários que têm acesso a determinadas informações. Os administradores podem usar Azure Active Directory Privileged Identity Management para descobrir, restringir e monitorar identidades com privilégios e seu acesso aos recursos. Essa funcionalidade também pode ser usada para impor acesso administrativo sob demanda e Just-in-time quando necessário.
- [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) detecta possíveis vulnerabilidades que afetam as&#39;identidades de uma organização, configura as respostas automatizadas para detectar ações suspeitas&#39;relacionadas a identidades de uma organização e investiga incidentes suspeitos para tomar as medidas apropriadas para resolvê-los.

### <a name="security"></a>Segurança

**Gerenciamento de segredos**: A solução usa [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) para o gerenciamento de chaves e segredos. O Cofre de Chaves do Azure ajuda a salvaguardar as chaves criptográficas e os segredos utilizados pelas aplicações em cloud e pelos serviços. Os recursos de Azure Key Vault a seguir ajudam os clientes a proteger e acessar esses dados:

- As políticas de acesso avançadas são configuradas de acordo com a necessidade.
- Key Vault políticas de acesso são definidas com as permissões mínimas necessárias para chaves e segredos.
- Todas as chaves e segredos em Key Vault têm datas de expiração.
- Todas as chaves em Key Vault são protegidas por módulos de segurança de hardware especializados. O tipo de chave é uma chave RSA de 2048 bits protegida por HSM.
- Todos os usuários e identidades recebem as permissões mínimas necessárias usando o controle de acesso baseado em função.
- Os logs de diagnóstico para Key Vault estão habilitados com um período de retenção de pelo menos 365 dias.
- As operações de criptografia permitidas para chaves são restritas às necessárias.
- A solução é integrada com Azure Key Vault para gerenciar chaves e segredos de criptografia de disco de máquina virtual IaaS.

**Gerenciamento**de patches: As máquinas virtuais do Windows implantadas como parte dessa arquitetura de referência são configuradas por padrão para receber atualizações automáticas do serviço Windows Update. Essa solução também inclui o serviço de [automação do Azure](https://docs.microsoft.com/azure/automation/automation-intro) por meio do qual implantações atualizadas podem ser criadas para aplicar patch às máquinas virtuais quando necessário.

**Proteção contra malware**: [O Microsoft Antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware) para máquinas virtuais fornece recursos de proteção em tempo real que ajudam a identificar e remover vírus, spyware e outros softwares mal-intencionados, com alertas configuráveis quando um software mal-intencionado ou indesejado conhecido tenta instalar ou executar em máquinas virtuais protegidas.

**Central de segurança do Azure**: Com a [central de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro), os clientes podem aplicar e gerenciar centralmente políticas de segurança entre cargas de trabalho, limitar a exposição a ameaças e detectar e responder a ataques. Além disso, a central de segurança do Azure acessa as configurações existentes dos serviços do Azure para fornecer recomendações de configuração e de serviço para ajudar a melhorar a postura de segurança e proteger os dados.

A central de segurança do Azure usa uma variedade de recursos de detecção para alertar os clientes sobre possíveis ataques visando seus ambientes. Estes alertas contêm informações valiosas sobre o que acionou o alerta, os recursos afetados e a origem do ataque. A central de segurança do Azure tem um conjunto de [alertas de segurança](https://docs.microsoft.com/azure/security-center/security-center-alerts-type)predefinidos, que são disparados quando ocorre uma ameaça ou atividade suspeita. [As regras de alerta personalizadas](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) na central de segurança do Azure permitem que os clientes definam novos alertas de segurança com base nos dados que já foram coletados de seu ambiente.

A central de segurança do Azure fornece alertas e incidentes de segurança priorizados, tornando mais simples para os clientes descobrir e resolver problemas potenciais de segurança. Um [relatório de inteligência contra ameaças](https://docs.microsoft.com/azure/security-center/security-center-threat-report) é gerado para cada ameaça detectada para ajudar as equipes de resposta a incidentes na investigação e na correção de ameaças.

**Gateway de aplicativo Azure**: A arquitetura reduz o risco de vulnerabilidades de segurança usando um gateway de Aplicativo Azure com um firewall de aplicativo Web configurado e o conjunto de regras OWASP habilitado. Os recursos adicionais incluem:

- [SSL de ponta a ponta](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Habilitar [descarregamento SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Desabilitar [TLS v 1.0 e v 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Firewall do aplicativo Web](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (modo de prevenção)
- [Modo de prevenção](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) com conjunto de regras OWASP 3,0
- Habilitar [log de diagnóstico](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)
- [Investigações de integridade personalizadas](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-gateway-portal)
- A [central de segurança do Azure](https://azure.microsoft.com/services/security-center) e o [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) fornecem proteção e notificações adicionais. A central de segurança do Azure também fornece um sistema de reputação.

### <a name="business-continuity"></a>Continuidade do negócio

**Elevada disponibilidade**: A solução implanta todas as máquinas virtuais em um [conjunto de disponibilidade](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Os conjuntos de disponibilidade garantem que as máquinas virtuais sejam distribuídas entre vários clusters de hardware isolados para melhorar a disponibilidade. Pelo menos uma máquina virtual está disponível durante um evento de manutenção planejada ou não planejada, atendendo ao SLA de 99,95% do Azure.

**Cofre dos serviços de recuperação**: O [cofre dos serviços de recuperação](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) hospeda dados de backup e protege todas as configurações de máquinas virtuais do Azure nessa arquitetura. Com um cofre dos serviços de recuperação, os clientes podem restaurar arquivos e pastas de uma máquina virtual IaaS sem restaurar toda a máquina virtual, permitindo tempos de restauração mais rápidos.

**Testemunha de nuvem**: A [testemunha em nuvem](https://docs.microsoft.com/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness) é um tipo de testemunha de quorum de cluster de failover no Windows Server 2016 que aproveita o Azure como ponto de arbitragem. A testemunha de nuvem, como qualquer outra testemunha de quorum, obtém um voto e pode participar dos cálculos de quorum, mas usa o armazenamento de BLOBs do Azure disponível publicamente. Isso elimina a sobrecarga de manutenção extra das máquinas virtuais hospedadas em uma nuvem pública.

### <a name="logging-and-auditing"></a>Registro em log e auditoria

Os serviços do Azure registram extensivamente a atividade do sistema e do usuário, bem como a integridade do sistema:
- **Logs de atividade**: [Os logs de atividade](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) fornecem informações sobre as operações executadas nos recursos em uma assinatura. Os logs de atividades podem ajudar a determinar o iniciador de uma operação, a hora de ocorrência e o status.
- **Logs de diagnóstico**: Os [logs de diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) incluem todos os logs emitidos por cada recurso. Esses logs incluem logs do sistema de eventos do Windows, logs de armazenamento do Azure, Key Vault logs de auditoria e acesso do gateway de aplicativo e logs de firewall. Todos os logs de diagnóstico gravam em uma conta de armazenamento do Azure centralizada e criptografada para arquivamento. A retenção é configurável pelo usuário, até 730 dias, para atender aos requisitos de retenção específicos da organização.

**Logs de Azure monitor**: Esses logs são consolidados em [logs de Azure monitor](https://azure.microsoft.com/services/log-analytics/) para processamento, armazenamento e relatórios de Dashboard. Depois de coletados, os dados são organizados em tabelas separadas para cada tipo de dados dentro de Log Analytics espaços de trabalho, o que permite que todos os dados sejam analisados juntos, independentemente de sua fonte original. Além disso, a central de segurança do Azure integra-se aos logs de Azure Monitor, permitindo que os clientes usem consultas Kusto para acessar seus dados de eventos de segurança e combiná-los com dados de outros serviços.

As seguintes [soluções de monitoramento](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) do Azure estão incluídas como parte dessa arquitetura:
-   [Avaliação do Active Directory](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): A solução de verificação de integridade Active Directory avalia o risco e a integridade dos ambientes de servidor em um intervalo regular e fornece uma lista priorizada de recomendações específicas para a infraestrutura de servidor implantada.
- [Avaliação do SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): A solução de verificação de integridade do SQL avalia o risco e a integridade dos ambientes de servidor em um intervalo regular e fornece aos clientes uma lista priorizada de recomendações específicas para a infraestrutura de servidor implantada.
- [Integridade do agente](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): A solução Integridade do Agente relata Quantos agentes são implantados e sua distribuição geográfica, bem como Quantos agentes que não respondem e o número de agentes que estão enviando dados operacionais.
-   [Análise do log de atividades](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): A solução Análise do Log de Atividades auxilia na análise dos logs de atividade do Azure em todas as assinaturas do Azure para um cliente.

**Automação do Azure**: A [automação do Azure](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) armazena, executa e gerencia runbooks. Nesta solução, os runbooks ajudam a coletar logs do banco de dados SQL do Azure. A solução de [controle de alterações](https://docs.microsoft.com/azure/automation/automation-change-tracking) de automação permite que os clientes identifiquem facilmente as alterações no ambiente.

**Azure monitor**: [Azure monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) ajuda os usuários a controlar o desempenho, manter a segurança e identificar tendências, permitindo que as organizações façam auditoria, criem alertas e arquivem dados, incluindo o acompanhamento de chamadas de API em seus recursos do Azure.

## <a name="threat-model"></a>Modelo de ameaça

O DFD (diagrama de fluxo de dados) para esta arquitetura de referência está disponível para [Download](https://aka.ms/pcidss-iaaswa-tm) ou pode ser encontrado abaixo. Esse modelo pode ajudar os clientes a entender os pontos de risco potencial na infraestrutura do sistema ao fazer modificações.

![Diagrama de arquitetura de referência de aplicativo Web IaaS para PCI DSS](images/pcidss-iaaswa-threat-model.png "Diagrama de arquitetura de referência de aplicativo Web IaaS para PCI DSS")

## <a name="compliance-documentation"></a>Documentação de conformidade

A [matriz de responsabilidade do cliente de Blueprint de Segurança e Conformidade do Azure PCI DSS](https://aka.ms/pcidss-crm) lista as responsabilidades de todos os requisitos de PCI DSS 3,2.

A [matriz de implementação de aplicativo web Blueprint de Segurança e Conformidade do Azure PCI DSS IaaS](https://aka.ms/pcidss-iaaswa-cim) fornece informações sobre quais requisitos de PCI DSS 3,2 são tratados pela arquitetura do aplicativo Web IaaS, incluindo descrições detalhadas de como o a implementação atende aos requisitos de cada artigo abordado.

## <a name="guidance-and-recommendations"></a>Diretrizes e recomendações

### <a name="vpn-and-expressroute"></a>VPN e ExpressRoute

Um túnel VPN seguro ou o [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) precisa ser configurado para estabelecer com segurança uma conexão com os recursos implantados como parte dessa arquitetura de referência do aplicativo Web IaaS. Ao configurar adequadamente uma VPN ou ExpressRoute, os clientes podem adicionar uma camada de proteção para os dados em trânsito.

Ao implementar um túnel VPN seguro com o Azure, uma conexão privada virtual entre uma rede local e uma rede virtual do Azure pode ser criada. Essa conexão ocorre pela Internet e permite aos clientes "encapsular" informações com segurança dentro de um link criptografado entre a rede do cliente e o Azure. A VPN site a site é uma tecnologia segura e madura, que foi implantada por empresas de todos os tamanhos por décadas. O [modo de encapsulamento IPSec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) é usado nessa opção como um mecanismo de criptografia.

Como o tráfego dentro do túnel VPN atravessa a Internet com uma VPN site a site, a Microsoft oferece outra opção de conexão ainda mais segura. O Azure ExpressRoute é um link WAN dedicado entre o Azure e um local ou um provedor de hospedagem do Exchange. Como as conexões do ExpressRoute não passam pela Internet, essas conexões oferecem mais confiabilidade, velocidades mais rápidas, latências menores e mais segurança do que as conexões típicas pela Internet. Além disso, como essa é uma conexão direta do provedor de telecomunicação do cliente, os dados não viajam pela Internet e, portanto, não são expostos a ele.

As práticas recomendadas para implementar uma rede híbrida segura que estende uma rede local para o Azure estão [disponíveis](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## <a name="disclaimer"></a>Exclusão de Responsabilidade

- Este documento é apenas para fins informativos. A MICROSOFT NÃO OFERECE NENHUMA GARANTIA, EXPRESSA, IMPLÍCITA OU ESTATUTÁRIA, QUANTO ÀS INFORMAÇÕES CONTIDAS NESTE DOCUMENTO. Este documento é fornecido "no estado em que se encontra". As informações e as exibições expressas neste documento, incluindo URLs e outras referências de site da Internet, podem ser alteradas sem aviso prévio. Os clientes que lêem este documento trazem o risco de usá-lo.
- Este documento não fornece aos clientes nenhum direito legal sobre qualquer propriedade intelectual em qualquer produto ou solução da Microsoft.
- Os clientes podem copiar e usar este documento para fins de referência interna.
- Determinadas recomendações neste documento podem resultar em maior uso de recursos de computação, rede ou dados no Azure, e podem aumentar os custos de assinatura ou de licença do Azure do cliente.
- Essa arquitetura destina-se a servir como base para os clientes se ajustarem aos requisitos específicos e não devem ser usados no estado em que se encontram em um ambiente de produção.
- Este documento é desenvolvido como uma referência e não deve ser usado para definir todos os meios pelos quais um cliente pode atender a requisitos e regulamentos específicos de conformidade. Os clientes devem buscar o suporte legal de sua organização em implementações aprovadas do cliente.
