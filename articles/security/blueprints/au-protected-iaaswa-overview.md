---
title: Aplicativo Web Blueprint de Segurança e Conformidade do Azure-IaaS para a Austrália protegida
description: Aplicativo Web Blueprint de Segurança e Conformidade do Azure-IaaS para a Austrália protegida
services: security
author: meladie
ms.assetid: f53a25c4-1c75-42d6-a0e7-a91661673891
ms.service: security
ms.topic: article
ms.date: 08/23/2018
ms.author: meladie
ms.openlocfilehash: 50e410fc439be7b3a5f4c1e8d4bab5d60c3c4f52
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/10/2019
ms.locfileid: "68946929"
---
# <a name="azure-security-and-compliance-blueprint---iaas-web-application-for-australia-protected"></a>Aplicativo Web Blueprint de Segurança e Conformidade do Azure-IaaS para a Austrália protegida

## <a name="overview"></a>Descrição geral
Este Blueprint de Segurança e Conformidade do Azure fornece diretrizes para a implantação de um ambiente de infraestrutura como serviço (IaaS) adequado para a coleta, o armazenamento e a recuperação de dados governamentais protegidos por AU que estão em conformidade com os objetivos do o ISM (manual de segurança de informações do governo da Austrália) produzido pela Directorate de sinais australiano (ASD). Este projeto demonstra uma arquitetura de referência comum e ajuda a demonstrar o tratamento adequado de dados do governo confidenciais em um ambiente seguro, em conformidade e de várias camadas.

Essa arquitetura de referência, guia de implementação e modelo de ameaça fornecem uma base para os clientes empreenderem seus próprios processos de certificação de planejamento e sistema, ajudando os clientes a implantar cargas de trabalho no Azure de maneira incompatível com ASD. Os clientes podem optar por implementar um gateway de VPN do Azure ou o ExpressRoute para usar serviços federados e para integrar recursos locais com recursos do Azure. Os clientes devem considerar as implicações de segurança do uso de recursos locais. A configuração adicional é necessária para atender a todos os requisitos, pois eles podem variar com base nas especificidades da implementação de cada cliente.

A obtenção de incompatibilidade de ASD exige que uma segurança de informações registrada assessor audita o sistema. Os clientes são responsáveis por realizar avaliações apropriadas de segurança e conformidade de qualquer solução criada usando essa arquitetura, já que os requisitos podem variar com base nas especificidades da implementação de cada cliente.

## <a name="architecture-diagram-and-components"></a>Diagrama e componentes da arquitetura
Esta solução implanta uma arquitetura de referência para um aplicativo Web IaaS com um back-end SQL Server. A arquitetura inclui uma camada da Web, camada de dados, Active Directory infraestrutura, gateway de aplicativo e Load Balancer. As máquinas virtuais implantadas na Web e nas camadas de dados são configuradas em um conjunto de disponibilidade e SQL Server instâncias são configuradas em um grupo de disponibilidade Always On para alta disponibilidade. As máquinas virtuais são ingressadas no domínio e Active Directory políticas de grupo são usadas para impor as configurações de segurança e conformidade no nível do sistema operacional. Um host de bastiões de gerenciamento fornece uma conexão segura para os administradores acessarem recursos implantados.

A arquitetura pode fornecer um ambiente híbrido seguro que estende uma rede local para o Azure, permitindo que cargas de trabalho baseadas na Web sejam acessadas com segurança por usuários corporativos da rede de área local privada da organização ou pela Internet. Para soluções locais, o cliente é responsável por conta própria e por todos os aspectos de segurança, operações e conformidade.

Os recursos do Azure incluídos nesta solução podem se conectar a uma rede local ou a um recurso de colocalização do datacenter (por exemplo, CDC no Camberra) por meio de VPN IPSec usando o gateway de VPN do Azure ou por meio do ExpressRoute. A decisão de utilizar uma VPN deve ser feita com a classificação dos dados transmitidos e o caminho de rede em mente. Os clientes que executam cargas de trabalho de grande escala e de missão crítica com requisitos de Big Data devem considerar uma arquitetura de rede híbrida usando o ExpressRoute para conectividade de rede privada aos serviços do Azure. Consulte a seção diretrizes e recomendações para obter mais detalhes sobre mecanismos de conexão com o Azure.

A Federação com Azure Active Directory deve ser usada para permitir que os usuários se autentiquem usando credenciais locais e acessem todos os recursos na nuvem usando uma infraestrutura de Serviços de Federação do Active Directory (AD FS) local. Serviços de Federação do Active Directory (AD FS) pode fornecer recursos de logon único da Web e de Federação de identidades simplificados e seguros para esse ambiente híbrido. Consulte a seção diretrizes e recomendações para obter mais detalhes Azure Active Directory configuração.

A solução usa contas de armazenamento do Azure, que os clientes podem configurar para usar o Criptografia do Serviço de Armazenamento para manter a confidencialidade dos dados em repouso. O Azure armazena três cópias de dados na região selecionada de um cliente para fins de resiliência. As regiões do Azure são implantadas em pares de regiões resilientes e o armazenamento com redundância geográfica garante que os dados serão replicados para a segunda região com três cópias também. Isso impede um evento adverso no local de dados primário do cliente, resultando em uma perda de dados.

Para aumentar a segurança, todos os recursos nesta solução são gerenciados como um grupo de recursos por meio de Azure Resource Manager. Azure Active Directory controle de acesso baseado em função é usado para controlar o acesso a recursos e chaves implantados no Azure Key Vault. A integridade do sistema é monitorada por meio da central de segurança do Azure e Azure Monitor. Os clientes configuram ambos os serviços de monitoramento para capturar logs e exibir a integridade do sistema em um único painel fácil e navegável. Aplicativo Azure Gateway está configurado como um firewall no modo de prevenção e requer o tráfego TLS com uma versão mínima de 1,2.

![Aplicativo Web IaaS para arquitetura de referência protegida por au](images/au-protected-iaaswa-architecture.png?raw=true "Aplicativo Web IaaS para o diagrama da arquitetura de referência protegida por au") 

Essa solução usa os seguintes serviços do Azure. Mais detalhes estão na seção [arquitetura de implantação](#deployment-architecture) .

- Conjuntos de Disponibilidade
    - (1) nós de cluster do SQL
    - (1) Web/IIS
- Azure Active Directory
- Gateway de Aplicação do Azure
    - (1) Firewall do aplicativo Web
        - Modo de firewall: prevenção
        - Conjunto de regras: OWASP 3.0
        - Porta do ouvinte: 443
- Testemunha de nuvem do Azure
- Azure Key Vault
- Balanceador de Carga do Azure
- Azure Monitor
- Azure Resource Manager
- Centro de Segurança do Azure
- Registos do Azure Monitor
- Storage do Azure
- Máquinas Virtuais do Azure
    - (1) gerenciamento/bastiões (Windows Server 2016 Datacenter)
    - (2) SQL Server nó de cluster (SQL Server 2017 no Windows Server 2016)
    - (2) Web/IIS (Windows Server 2016 Datacenter)
- Rede Virtual do Azure
    - (4) grupos de segurança de rede
    - Observador de rede do Azure
- Cofre dos serviços de recuperação

Este projeto contém serviços do Azure que não foram certificados para uso na classificação protegida pelo ACSC (centro de segurança de Cyber) australiano. Todos os serviços incluídos nessa arquitetura de referência foram certificados pelo ACSC no nível da DLM (inscrições de limitação de disseminação). A Microsoft recomenda que os clientes analisem os relatórios de segurança e auditoria publicados relacionados a esses serviços do Azure e usem sua estrutura de gerenciamento de riscos para determinar se o serviço do Azure é adequado para sua certificação interna e uso no Classificação protegida.

## <a name="deployment-architecture"></a>Arquitetura de implantação
A seção a seguir detalha os elementos de implantação e implementação.

**Host bastião**: O host de bastiões é o ponto único de entrada que permite aos usuários acessar os recursos implantados nesse ambiente. O host de bastiões fornece uma conexão segura com os recursos implantados permitindo apenas o tráfego remoto de endereços IP públicos em uma lista segura. Para permitir o tráfego da área de trabalho remota (RDP), a origem do tráfego precisa ser definida no grupo de segurança de rede.

Essa solução cria uma máquina virtual como um host de bastiões ingressado no domínio com as seguintes configurações:
-   [Extensão de antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware)
-   [Extensão de Diagnóstico do Azure](../../virtual-machines/windows/extensions-diagnostics-template.md)
-   [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) usando Azure Key Vault
-   Uma [política](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) de desligamento automático para reduzir o consumo de recursos de máquina virtual quando não estiver em uso

### <a name="virtual-network"></a>Rede virtual
A arquitetura define uma rede virtual privada com um espaço de endereço de 10.200.0.0/16.

**Grupos de segurança de rede**: Esta solução implanta recursos em uma arquitetura com uma sub-rede da Web, uma sub-rede de banco de dados, uma sub-rede Active Directory e uma sub-rede de gerenciamento separadas dentro de uma rede virtual. As sub-redes são separadas logicamente pelas regras do grupo de segurança de rede aplicadas às sub-redes individuais para restringir o tráfego entre sub-redes apenas para isso necessário para a funcionalidade de gerenciamento e do sistema.

Consulte a configuração de [grupos de segurança de rede](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) implantados com esta solução. As organizações podem configurar grupos de segurança de rede editando o arquivo acima usando [esta documentação](../../virtual-network/virtual-network-vnet-plan-design-arm.md) como guia.

Cada uma das sub-redes tem um grupo de segurança de rede dedicado:
- 1 grupo de segurança de rede para o gateway de aplicativo (LBNSG)
- 1 grupo de segurança de rede para host bastião (MGTNSG)
- 1 grupo de segurança de rede para servidores SQL e testemunha de nuvem (SQLNSG)
- 1 grupo de segurança de rede para a camada da Web (WEBNSG)

### <a name="data-in-transit"></a>Dados em trânsito
Por padrão, o Azure criptografa todas as comunicações entre os data centers do Azure. 

Para dados protegidos em trânsito de redes de Propriedade do cliente, a arquitetura usa a Internet ou o ExpressRoute com um gateway de VPN configurado com IPSEC.

Além disso, todas as transações do Azure por meio do portal de gerenciamento do Azure ocorrem por meio de HTTPS utilizando o TLS 1,2.

### <a name="data-at-rest"></a>Dados inativos
A arquitetura protege os dados em repouso por meio de criptografia, auditoria de banco e outras medidas.

**Armazenamento do Azure**: Para atender aos requisitos de dados criptografados em repouso, todo o [armazenamento do Azure](https://azure.microsoft.com/services/storage/) usa [criptografia do serviço de armazenamento](../../storage/common/storage-service-encryption.md). Isso ajuda a proteger e proteger os dados em suporte aos compromissos de segurança organizacional e aos requisitos de conformidade definidos pelo ISM do governo australiano.

**Azure Disk Encryption**: [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) aproveita o recurso BitLocker do Windows para fornecer criptografia de volume para discos de dados. A solução se integra com Azure Key Vault para ajudar a controlar e gerenciar as chaves de criptografia de disco.

**SQL Server**: A instância de SQL Server usa as seguintes medidas de segurança de banco de dados:
-   [SQL Server auditoria](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine?view=sql-server-2017) rastreia eventos de banco de dados e os grava em logs de auditoria.
-   A Transparent [Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) executa criptografia e descriptografia em tempo real do banco de dados, backups associados e arquivos de log de transações para proteger informações em repouso. A Transparent Data Encryption fornece garantia de que os dados armazenados não estão sujeitos a acesso não autorizado.
-   [As regras de firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) impedem todo o acesso a servidores de banco de dados até que sejam concedidas permissões adequadas A firewall concede acesso a bases de dados com base no endereço IP de origem de cada pedido.
-   As [colunas criptografadas](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-wizard?view=sql-server-2017) garantem que os dados confidenciais nunca apareçam como texto sem formatação no sistema de banco de dados. Depois de habilitar a criptografia de dados, somente aplicativos cliente ou servidores de aplicativos com acesso às chaves podem acessar dados de texto sem formatação.
- A [máscara de dados dinâmicos](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking?view=sql-server-2017) limita a exposição de dados confidenciais mascarando os dados para usuários ou aplicativos sem privilégios. A máscara de dados dinâmicos pode descobrir dados potencialmente confidenciais automaticamente e sugerir que as máscaras apropriadas sejam aplicadas. Isso ajuda a reduzir o acesso de modo que os dados confidenciais não saiam do banco por acesso não autorizado. **Os clientes são responsáveis por ajustar as configurações de mascaramento de dados dinâmicos para aderir ao seu esquema de banco de dado.**

### <a name="identity-management"></a>Gestão de identidades
Os clientes podem utilizar Active Directory serviços federados locais para federar com [Azure Active Directory](https://azure.microsoft.com/services/active-directory/), que é o serviço de gerenciamento de identidade e diretório multilocatário baseado em nuvem da Microsoft. [Azure Active Directory Connect](../../active-directory/hybrid/whatis-hybrid-identity.md) integra diretórios locais com Azure Active Directory. Todos os usuários nesta solução exigem contas de Azure Active Directory. Com a entrada da Federação, os usuários podem entrar no Azure Active Directory e autenticar os recursos do Azure usando credenciais locais.

Além disso, os seguintes recursos de Azure Active Directory ajudam a gerenciar o acesso aos dados no ambiente do Azure:
- A autenticação para o aplicativo é executada usando Azure Active Directory. Para obter mais informações, consulte [integrando aplicativos com Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md).
- O [controle de acesso baseado em função do Azure](../../role-based-access-control/role-assignments-portal.md) permite que os administradores definam permissões de acesso refinadas para conceder apenas a quantidade de acesso que os usuários precisam para executar seus trabalhos. Em vez de fornecer a cada usuário permissão irrestrita para recursos do Azure, os administradores podem permitir apenas determinadas ações para acessar dados. O acesso à assinatura é limitado ao administrador da assinatura.
- [Azure Active Directory Privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md) permite que os clientes minimizem o número de usuários que têm acesso a determinadas informações. Os administradores podem usar Azure Active Directory Privileged Identity Management para descobrir, restringir e monitorar identidades com privilégios e seu acesso aos recursos. Essa funcionalidade também pode ser usada para impor acesso administrativo sob demanda e Just-in-time quando necessário.
- [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview.md) detecta possíveis vulnerabilidades que afetam as&#39;identidades de uma organização, configura as respostas automatizadas para detectar ações suspeitas&#39;relacionadas a identidades de uma organização e investiga incidentes suspeitos para tomar as medidas apropriadas para resolvê-los.

**Autenticação multifator do Azure**: Para proteger identidades, a autenticação multifator deve ser implementada. A [autenticação multifator do Azure](https://azure.microsoft.com/services/multi-factor-authentication/) é uma solução fácil de usar, escalonável e confiável que fornece um segundo método de autenticação para proteger os usuários. A autenticação multifator do Azure usa o poder da nuvem e se integra com Active Directory e aplicativos personalizados locais. Essa proteção é estendida para cenários de alto volume e críticos de missão crítica.

### <a name="security"></a>Segurança
**Gerenciamento de segredos**: A solução usa [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) para o gerenciamento de chaves e segredos. O Cofre de Chaves do Azure ajuda a salvaguardar as chaves criptográficas e os segredos utilizados pelas aplicações em cloud e pelos serviços. Os recursos de Azure Key Vault a seguir ajudam os clientes a proteger e acessar esses dados:

- As políticas de acesso avançadas são configuradas de acordo com a necessidade.
- Key Vault políticas de acesso são definidas com as permissões mínimas necessárias para chaves e segredos.
- Todas as chaves e segredos em Key Vault têm datas de expiração.
- Todas as chaves em Key Vault são protegidas por módulos de segurança de hardware especializados. O tipo de chave é um módulo de segurança de hardware protegido chave RSA de 2048 bits.
- Todos os usuários e identidades recebem as permissões mínimas necessárias usando o controle de acesso baseado em função.
- Os logs de diagnóstico para Key Vault estão habilitados com um período de retenção de pelo menos 365 dias.
- As operações de criptografia permitidas para chaves são restritas às necessárias.
- A solução é integrada com Azure Key Vault para gerenciar chaves e segredos de criptografia de disco de máquina virtual IaaS.

**Gerenciamento**de patches: As máquinas virtuais do Windows implantadas como parte dessa arquitetura de referência são configuradas por padrão para receber atualizações automáticas do serviço Windows Update. Essa solução também inclui o serviço de [automação do Azure](https://docs.microsoft.com/azure/automation/automation-intro) por meio do qual implantações atualizadas podem ser criadas para aplicar patch às máquinas virtuais quando necessário.

**Proteção contra malware**: [O Microsoft Antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware) para máquinas virtuais fornece recursos de proteção em tempo real que ajudam a identificar e remover vírus, spyware e outros softwares mal-intencionados, com alertas configuráveis quando um software mal-intencionado ou indesejado conhecido tenta instalar ou executar em máquinas virtuais protegidas.

**Central de segurança do Azure**: Com a [central de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro), os clientes podem aplicar e gerenciar centralmente políticas de segurança entre cargas de trabalho, limitar a exposição a ameaças e detectar e responder a ataques. Além disso, a central de segurança do Azure acessa as configurações existentes dos serviços do Azure para fornecer recomendações de configuração e de serviço para ajudar a melhorar a postura de segurança e proteger os dados.

A central de segurança do Azure usa uma variedade de recursos de detecção para alertar os clientes sobre possíveis ataques visando seus ambientes. Estes alertas contêm informações valiosas sobre o que acionou o alerta, os recursos afetados e a origem do ataque. A central de segurança do Azure tem um conjunto de [alertas de segurança](https://docs.microsoft.com/azure/security-center/security-center-alerts-type)predefinidos, que são disparados quando ocorre uma ameaça ou atividade suspeita. [As regras de alerta personalizadas](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) na central de segurança do Azure permitem que os clientes definam novos alertas de segurança com base nos dados que já foram coletados de seu ambiente.

A central de segurança do Azure fornece alertas e incidentes de segurança priorizados, tornando mais simples para os clientes descobrir e resolver problemas potenciais de segurança. Um [relatório de inteligência contra ameaças](https://docs.microsoft.com/azure/security-center/security-center-threat-report) é gerado para cada ameaça detectada para ajudar as equipes de resposta a incidentes na investigação e na correção de ameaças.

Além disso, essa arquitetura de referência utiliza a [avaliação de vulnerabilidade](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations) na central de segurança do Azure. Uma vez configurado, um agente de parceiro (por exemplo, Qualys) relata dados de vulnerabilidade para a plataforma de gerenciamento do parceiro. Por sua vez, a plataforma de gerenciamento do parceiro fornece dados de monitoramento de vulnerabilidade e integridade de volta para a central de segurança do Azure, permitindo que os clientes identifiquem rapidamente as máquinas virtuais vulneráveis.

**Gateway de aplicativo Azure**: A arquitetura reduz o risco de vulnerabilidades de segurança usando um gateway de Aplicativo Azure com um firewall de aplicativo Web configurado e o conjunto de regras OWASP habilitado. Os recursos adicionais incluem:

- [SSL de ponta a ponta](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Habilitar [descarregamento SSL](../../application-gateway/create-ssl-portal.md)
- Desabilitar [TLS v 1.0 e v 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Firewall do aplicativo Web](../../application-gateway/waf-overview.md) (modo de prevenção)
- [Modo de prevenção](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) com conjunto de regras OWASP 3,0
- Habilitar [log de diagnóstico](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)
- [Investigações de integridade personalizadas](../../application-gateway/quick-create-portal.md)
- A [central de segurança do Azure](https://azure.microsoft.com/services/security-center) e o [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) fornecem proteção e notificações adicionais. A central de segurança do Azure também fornece um sistema de reputação.

### <a name="business-continuity"></a>Continuidade do negócio
**Elevada disponibilidade**: A solução implanta todas as máquinas virtuais em um [conjunto de disponibilidade](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Os conjuntos de disponibilidade garantem que as máquinas virtuais sejam distribuídas entre vários clusters de hardware isolados para melhorar a disponibilidade. Pelo menos uma máquina virtual está disponível durante um evento de manutenção planejada ou não planejada, atendendo ao SLA de 99,95% do Azure.

**Cofre dos serviços de recuperação**: O [cofre dos serviços de recuperação](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) hospeda dados de backup e protege todas as configurações de máquinas virtuais do Azure nessa arquitetura. Com um cofre dos serviços de recuperação, os clientes podem restaurar arquivos e pastas de uma máquina virtual IaaS sem restaurar toda a máquina virtual, permitindo tempos de restauração mais rápidos.

**Testemunha de nuvem**: A [testemunha em nuvem](https://docs.microsoft.com/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness) é um tipo de testemunha de quorum de cluster de failover no Windows Server 2016 que aproveita o Azure como ponto de arbitragem. A testemunha de nuvem, como qualquer outra testemunha de quorum, obtém um voto e pode participar dos cálculos de quorum, mas usa o armazenamento de BLOBs do Azure disponível publicamente. Isso elimina a sobrecarga de manutenção extra das máquinas virtuais hospedadas em uma nuvem pública.

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

**Automação do Azure**: A [automação do Azure](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) armazena, executa e gerencia runbooks. Nesta solução, os runbooks ajudam a coletar logs do Azure SQL Server. A solução de [controle de alterações](../../automation/change-tracking.md) de automação permite que os clientes identifiquem facilmente as alterações no ambiente.

**Azure monitor**: [Azure monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) ajuda os usuários a controlar o desempenho, manter a segurança e identificar tendências, permitindo que as organizações façam auditoria, criem alertas e arquivem dados, incluindo o acompanhamento de chamadas de API em seus recursos do Azure.

[Observador de rede do Azure](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview): O Observador de Rede do Azure oferece ferramentas para monitorizar, diagnosticar, ver métricas e ativar ou desativar registos de recursos numa rede virtual do Azure.  As entidades da Comunidade devem implementar logs de fluxo do observador de rede para NSGs e máquinas virtuais. Esses logs devem ser armazenados em uma conta de armazenamento dedicada que apenas os logs de segurança são armazenados e o acesso à conta de armazenamento deve ser protegido com controles de acesso baseados em função.

## <a name="threat-model"></a>Modelo de ameaça
O diagrama de fluxo de dados para esta arquitetura de referência está disponível para [Download](https://aka.ms/au-protected-iaaswa-tm) ou pode ser encontrado abaixo. Esse modelo pode ajudar os clientes a entender os pontos de risco potencial na infraestrutura do sistema ao fazer modificações.

![Aplicativo Web IaaS para modelo de ameaça protegido por au](images/au-protected-iaaswa-threat-model.png?raw=true "Aplicativo Web IaaS para diagrama de modelo de ameaça protegido por au")

## <a name="compliance-documentation"></a>Documentação de conformidade
Essa documentação de conformidade é produzida pela Microsoft com base em plataformas e serviços disponíveis na Microsoft. Devido à ampla variedade de implantações de clientes, esta documentação fornece uma abordagem generalizada para uma solução hospedada apenas no ambiente do Azure. Os clientes podem identificar e usar produtos e serviços alternativos com base em seus próprios ambientes operacionais e resultados de negócios. Os clientes que optam por usar recursos locais devem abordar a segurança e as operações para esses recursos locais. A solução documentada pode ser personalizada pelos clientes para atender aos seus requisitos de segurança e locais específicos.

O [Blueprint de segurança e conformidade do Azure – matriz de responsabilidade do cliente protegida por au](https://aka.ms/au-protected-crm) lista todos os controles de segurança necessários para o au protegido. Essa matriz detalha se a implementação de cada controle é de responsabilidade da Microsoft, do cliente ou do compartilhamento entre os dois.

A [matriz de implementação Blueprint de segurança e conformidade do Azure de aplicativo Web IaaS protegida por au](https://aka.ms/au-protected-iaaswa-cim) fornece informações sobre quais controles protegidos por au são endereçados pela arquitetura de aplicativo Web IaaS, incluindo descrições detalhadas de como a implementação atende aos requisitos de cada controle abordado.

## <a name="guidance-and-recommendations"></a>Diretrizes e recomendações
### <a name="vpn-and-expressroute"></a>VPN e ExpressRoute
Para obter informações classificadas, um túnel VPN IPSec seguro precisa ser configurado para estabelecer com segurança uma conexão com os recursos implantados como parte dessa arquitetura de referência do aplicativo Web IaaS. Ao configurar adequadamente uma VPN IPSec, os clientes podem adicionar uma camada de proteção para os dados em trânsito.

Ao implementar um túnel VPN IPSec seguro com o Azure, uma conexão privada virtual entre uma rede local e uma rede virtual do Azure pode ser criada. Essa conexão pode ocorrer pela Internet e permite aos clientes "encapsular" informações com segurança dentro de um link criptografado entre a rede do cliente e o Azure. A VPN site a site é uma tecnologia segura e madura, que foi implantada por empresas de todos os tamanhos por décadas. 

Como o tráfego dentro do túnel VPN atravessa a Internet com uma VPN site a site, a Microsoft oferece uma opção de conexão privada. O Azure ExpressRoute é um link dedicado entre o Azure e um local ou um provedor de hospedagem do Exchange e é considerado uma rede privada. Como as conexões do ExpressRoute não passam pela Internet, essas conexões oferecem mais confiabilidade, velocidades mais rápidas e latências menores do que conexões típicas pela Internet. Além disso, como essa é uma conexão direta do provedor de telecomunicação do cliente, os dados não viajam pela Internet e, portanto, não são expostos a ele.

As práticas recomendadas para implementar uma rede híbrida segura que estende uma rede local para o Azure estão [disponíveis](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid). 

Para ajudar a proteger os dados classificados, seja usando a Internet ou o Azure ExpressRoute, os clientes devem configurar sua VPN IPSec aplicando as seguintes configurações:

• O iniciador de VPN do cliente deve ser configurado para um tempo de vida de SA de não mais de 14400 segundos.
• O iniciador VPN do cliente deve desabilitar o modo IKEv1 agressivo.
• O iniciador de VPN do cliente deve configurar o sigilo de encaminhamento perfeito.
• O iniciador de VPN do cliente deve configurar o uso de HMAC-SHA256 ou superior.

As opções de configuração para dispositivos VPN e parâmetros de IPSec/IKE estão [disponíveis](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices) para revisão.

### <a name="azure-active-directory-setup"></a>Azure Active Directory configuração
[Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) é essencial para gerenciar a implantação e o provisionamento de acesso à equipe interagindo com o ambiente. Um Active Directory do Windows Server existente pode ser integrado com Azure Active Directory em [quatro cliques](../../active-directory/hybrid/how-to-connect-install-express.md).

Além disso, [Azure Active Directory Connect](../../active-directory/hybrid/whatis-hybrid-identity.md) permite que os clientes configurem a federação com [serviços de Federação do Active Directory (AD FS)]( ../../active-directory/hybrid/how-to-connect-fed-azure-adfs.md) e Azure Active Directory locais. Com a entrada da Federação, os clientes podem permitir que os usuários entrem em serviços baseados em Azure Active Directory com suas senhas locais e sem precisar inserir suas senhas novamente enquanto estiverem na rede corporativa. Usando a opção de Federação com Serviços de Federação do Active Directory (AD FS), você pode implantar uma nova instalação do Serviços de Federação do Active Directory (AD FS), ou pode especificar uma instalação existente em um farm do Windows Server 2012 R2.

Para impedir que os dados classificados sejam sincronizados com o Azure Active Directory, os clientes podem restringir os atributos que são replicados para Azure Active Directory aplicando as seguintes configurações no Azure Active Directory Connect:
- [Habilitar Filtragem](../../active-directory/hybrid/how-to-connect-sync-configure-filtering.md)
- [Desabilitar a sincronização de hash de senha](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)
- [Desabilitar Write-back de senha](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr)
- [Desabilitar Write-back de dispositivo](../../active-directory/hybrid/how-to-connect-device-writeback.md)
- Mantenha as configurações padrão para [impedir exclusões acidentais](../../active-directory/hybrid/how-to-connect-sync-feature-prevent-accidental-deletes.md) e [atualização automática](../../active-directory/hybrid/how-to-connect-install-automatic-upgrade.md)

## <a name="disclaimer"></a>Exclusão de Responsabilidade
- Este documento é apenas para fins informativos. A MICROSOFT NÃO OFERECE NENHUMA GARANTIA, EXPRESSA, IMPLÍCITA OU ESTATUTÁRIA, QUANTO ÀS INFORMAÇÕES CONTIDAS NESTE DOCUMENTO. Este documento é fornecido "no estado em que se encontra". As informações e as exibições expressas neste documento, incluindo URLs e outras referências de site da Internet, podem ser alteradas sem aviso prévio. Os clientes que lêem este documento trazem o risco de usá-lo.
- Este documento não fornece aos clientes nenhum direito legal sobre qualquer propriedade intelectual em qualquer produto ou solução da Microsoft.
- Os clientes podem copiar e usar este documento para fins de referência interna.
- Determinadas recomendações neste documento podem resultar em maior uso de recursos de computação, rede ou dados no Azure, e podem aumentar os custos de assinatura ou de licença do Azure do cliente.
- Essa arquitetura destina-se a servir como base para os clientes se ajustarem aos requisitos específicos e não devem ser usados no estado em que se encontram em um ambiente de produção.
- Este documento é desenvolvido como uma referência e não deve ser usado para definir todos os meios pelos quais um cliente pode atender a requisitos e regulamentos específicos de conformidade. Os clientes devem buscar o suporte legal de sua organização em implementações aprovadas do cliente.
