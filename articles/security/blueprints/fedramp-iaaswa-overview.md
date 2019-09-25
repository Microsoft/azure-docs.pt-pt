---
title: Aplicativo Web Blueprint de Segurança e Conformidade do Azure-IaaS para FedRAMP
description: Aplicativo Web Blueprint de Segurança e Conformidade do Azure-IaaS para FedRAMP
services: security
documentationcenter: na
author: jomolesk
manager: barbkess
editor: tomsh
ms.assetid: 8fe47cff-9c24-49e0-aa11-06ff9892a468
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/08/2018
ms.author: jomolesk
ms.openlocfilehash: bcce4dcac35b783efefe81abc2090506502e9931
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71257287"
---
# <a name="azure-security-and-compliance-blueprint-iaas-web-application-for-fedramp"></a>Esquema de Segurança e Conformidade do Azure: Aplicativo Web IaaS para FedRAMP

## <a name="overview"></a>Descrição geral

O [Federal Risk and Authorization Management Program (FedRAMP)](https://www.fedramp.gov) é um programa de todo o governo dos EUA que fornece uma abordagem padronizada para avaliação de segurança, autorização e monitoramento contínuo para produtos e serviços de nuvem. Essa Blueprint de Segurança e Conformidade do Azure automação fornece diretrizes para a implantação de um ambiente de IaaS (infraestrutura como serviço) compatível com FedRAMP, adequado para um aplicativo Web simples voltado para a Internet. Essa solução automatiza a implantação e a configuração de recursos do Azure para uma arquitetura de referência comum, demonstrando maneiras nas quais os clientes podem atender aos requisitos específicos de segurança e conformidade e serve como base para os clientes criarem e configure suas próprias soluções no Azure. A solução implementa um subconjunto de controles da linha de base FedRAMP alta, com base no NIST SP 800-53. Para obter mais informações sobre os requisitos de FedRAMP e essa solução, consulte a [documentação de conformidade](#compliance-documentation).
> [!NOTE]
> Esta solução é implantada no Azure governamental.

Essa Blueprint de Segurança e Conformidade do Azure automação implanta automaticamente uma arquitetura de referência de aplicativo Web IaaS com controles de segurança pré-configurados para ajudar os clientes a obter conformidade com os requisitos de FedRAMP. A solução consiste em modelos de Azure Resource Manager e scripts do PowerShell que guiam a implantação e a configuração de recursos.

Essa arquitetura destina-se a servir como base para os clientes se ajustarem aos requisitos específicos e não devem ser usados no estado em que se encontram em um ambiente de produção. Implantar um aplicativo nesse ambiente sem modificação não é suficiente para atender completamente aos requisitos da linha de base FedRAMP alta. Tenha em atenção o seguinte:
- Essa arquitetura fornece uma linha de base para ajudar os clientes a usar o Azure de maneira compatível com FedRAMP.
- Os clientes são responsáveis por realizar a avaliação apropriada de segurança e conformidade de qualquer solução criada usando essa arquitetura, pois os requisitos podem variar com base nas especificidades da implementação de cada cliente.

Para obter uma visão geral rápida de como essa solução funciona, Assista a este [vídeo](https://aka.ms/fedrampblueprintvideo) explicando e demonstrando sua implantação.

Clique [aqui](https://aka.ms/fedrampblueprintrepo) para obter instruções de implantação.

## <a name="architecture-diagram-and-components"></a>Diagrama e componentes da arquitetura
Esta solução implanta uma arquitetura de referência para um aplicativo Web IaaS com um back-end SQL Server. A arquitetura inclui uma camada da Web, camada de dados, Active Directory infraestrutura, gateway de aplicativo e Load Balancer. As máquinas virtuais implantadas na Web e nas camadas de dados são configuradas em um conjunto de disponibilidade e SQL Server instâncias são configuradas em um grupo de disponibilidade AlwaysOn para alta disponibilidade. As máquinas virtuais são ingressadas no domínio e Active Directory políticas de grupo são usadas para impor as configurações de segurança e conformidade no nível do sistema operacional. Um host de bastiões fornece uma conexão segura para os administradores acessarem os recursos implantados. **O Azure recomenda configurar uma conexão VPN ou Azure ExpressRoute para gerenciamento e importação de dados na sub-rede da arquitetura de referência.**

![Aplicativo Web IaaS para diagrama de arquitetura de referência do FedRAMP](images/fedramp-iaaswa-architecture.png?raw=true "Aplicativo Web IaaS para diagrama de arquitetura de referência do FedRAMP")

Essa solução usa os seguintes serviços do Azure. Os detalhes da arquitetura de implantação estão localizados na seção [arquitetura de implantação](#deployment-architecture) .

- Máquinas Virtuais do Azure
    - (1) host de bastiões (Windows Server 2016 Datacenter)
    - (2) Active Directory controlador de domínio (Windows Server 2016 Datacenter)
    - (2) SQL Server nó de cluster (SQL Server 2017 no Windows Server 2016)
    - (2) Web/IIS (Windows Server 2016 Datacenter)
- Conjuntos de Disponibilidade
    - (1) Active Directory controladores de domínio
    - (1) nós de cluster do SQL
    - (1) Web/IIS
- Rede Virtual do Azure
    - (1)/16 redes virtuais
    - (5)/24 sub-redes
    - As configurações de DNS são definidas para ambos os controladores de domínio
- Balanceador de Carga do Azure
- Gateway de Aplicação do Azure
    - (1) WAF do gateway de aplicativo habilitado
        - Modo de firewall: prevenção
        - Conjunto de regras: OWASP 3.0
        - ouvinte: porta 443
- Storage do Azure
    - (7) contas de armazenamento com redundância geográfica
- Testemunha de nuvem do Azure
- Cofre de Serviços de Recuperação
- Azure Key Vault
- Azure Active Directory (Azure AD)
- Azure Resource Manager
- Azure Monitor (logs)

## <a name="deployment-architecture"></a>Arquitetura de implantação

A seção a seguir detalha os elementos de desenvolvimento e implementação.

**Host bastião**: O host de bastiões é o ponto único de entrada que fornece uma conexão segura para os administradores acessarem recursos implantados. O NSG do host de bastiões permite conexões somente na porta TCP 3389 para RDP. Os clientes podem configurar ainda mais o host de bastiões para atender aos requisitos de proteção do sistema da organização.

### <a name="virtual-network"></a>Rede virtual
A arquitetura define uma rede virtual privada com um espaço de endereço de 10.200.0.0/16.

**Grupos de segurança de rede**: Esta solução implanta recursos em uma arquitetura com uma sub-rede da Web, uma sub-rede de banco de dados, uma sub-rede Active Directory e uma sub-rede de gerenciamento separadas dentro de uma rede virtual. As sub-redes são separadas logicamente pelas regras do grupo de segurança de rede aplicadas às sub-redes individuais para restringir o tráfego entre sub-redes apenas para isso necessário para a funcionalidade de gerenciamento e do sistema.

Consulte a configuração de [grupos de segurança de rede](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) implantados com esta solução. Os clientes podem configurar grupos de segurança de rede editando o arquivo acima usando [esta documentação](../../virtual-network/virtual-network-vnet-plan-design-arm.md) como guia.

Cada uma das sub-redes tem um NSG (grupo de segurança de rede) dedicado:
- 1 NSG para gateway de aplicativo (LBNSG)
- 1 NSG para host bastião (MGTNSG)
- 1 NSG para os controladores de domínio primário e de backup (ADNSG)
- 1 NSG para SQL Servers (SQLNSG)
- 1 NSG para camada da Web (WEBNSG)

**Sub-redes**: Cada sub-rede é associada ao seu NSG correspondente.

### <a name="data-at-rest"></a>Dados inativos

A arquitetura protege dados em repouso usando várias medidas de criptografia.

**Armazenamento do Azure**: Para atender aos requisitos de criptografia de dados em repouso, todas as contas de armazenamento usam [criptografia do serviço de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).

**SQL Server**: SQL Server está configurado para usar [Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption), que executa criptografia e descriptografia em tempo real de arquivos de dados e de log para proteger informações em repouso. O TDE fornece garantia de que os dados armazenados não estão sujeitos a acesso não autorizado.

Os clientes também podem configurar as seguintes SQL Server medidas de segurança:
-   A [autenticação e a autorização do AD](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) permitem o gerenciamento de identidades de usuários de banco de dados e outros serviços da Microsoft em um local central.
-   A [auditoria do banco de dados SQL](../../sql-database/sql-database-auditing.md) rastreia eventos de banco de dados e os grava em um log de auditoria em uma conta de armazenamento do Azure.
-   [As regras de firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) impedem todo o acesso a servidores de banco de dados até que sejam concedidas permissões adequadas A firewall concede acesso a bases de dados com base no endereço IP de origem de cada pedido.
-   A [detecção de ameaças do SQL](../../sql-database/sql-database-threat-detection.md) permite a detecção e a resposta a ameaças potenciais à medida que elas ocorrem, fornecendo alertas de segurança para atividades suspeitas de banco de dados, vulnerabilidades potenciais, ataques de injeção de SQL e padrões de acesso de banco de dados anormais
-   [Always Encrypted colunas](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) garantem que os dados confidenciais nunca apareçam como texto não criptografado no sistema de banco de dados. Depois de habilitar a criptografia de dados, somente aplicativos cliente ou servidores de aplicativos com acesso às chaves podem acessar dados de texto sem formatação.
-   O [mascaramento de dados dinâmicos do banco SQL](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) pode ser feito após a implantação da arquitetura de referência. Os clientes precisarão ajustar as configurações de mascaramento de dados dinâmicos para aderir ao seu esquema de banco de dado.

**Azure Disk Encryption**: Azure Disk Encryption é usado para criptografar discos de máquina virtual IaaS do Windows. [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) aproveita o recurso BitLocker do Windows para fornecer criptografia de volume para o sistema operacional e discos de dados. A solução é integrada com Azure Key Vault para ajudar a controlar e gerenciar as chaves de criptografia de disco.

### <a name="identity-management"></a>Gestão de identidades

As tecnologias a seguir fornecem recursos de gerenciamento de identidade no ambiente do Azure:
- O [Azure Active Directory (AD do Azure)](https://azure.microsoft.com/services/active-directory/) é o serviço de gerenciamento de identidade e diretório multilocatário baseado em nuvem da Microsoft.
- A autenticação para um aplicativo Web implantado pelo cliente pode ser executada usando o Azure AD. Para obter mais informações, consulte [integrando aplicativos com Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md).  
- O [RBAC (controle de acesso baseado em função) do Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) permite o gerenciamento de acesso precisamente focalizado para o Azure. O acesso à assinatura é limitado ao administrador da assinatura e o acesso aos recursos pode ser limitado com base na função de usuário.
- Uma instância implantada de Active Directory de IaaS fornece gerenciamento de identidade no nível do sistema operacional para máquinas virtuais IaaS implantadas.

### <a name="security"></a>Segurança
**Gerenciamento de segredos**: A solução usa [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) para o gerenciamento de chaves e segredos. O Cofre de Chaves do Azure ajuda a salvaguardar as chaves criptográficas e os segredos utilizados pelas aplicações em cloud e pelos serviços. Azure Key Vault ajuda a gerenciar chaves de criptografia de disco de máquina virtual IaaS e segredos para essa arquitetura de referência.

**Gerenciamento de patches**: As máquinas virtuais do Windows implantadas por esse Blueprint de Segurança e Conformidade do Azure automação são configuradas por padrão para receber atualizações automáticas do serviço Windows Update. Essa solução também implanta a solução de automação do Azure por meio da qual implantações de atualização podem ser criadas para implantar patches em servidores Windows quando necessário.

**Proteção contra malware**: [O Microsoft Antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware) para máquinas virtuais fornece recursos de proteção em tempo real que ajudam a identificar e remover vírus, spyware e outros softwares mal-intencionados, com alertas configuráveis quando um software mal-intencionado ou indesejado conhecido tenta instalar ou executar em máquinas virtuais protegidas.

**Gateway de aplicativo**: A arquitetura reduz o risco de vulnerabilidades de segurança usando um gateway de aplicativo com o WAF (firewall do aplicativo Web) e o conjunto de regras do OWASP habilitado. Os recursos adicionais incluem:

- [End-to-End-SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Habilitar [descarregamento SSL](../../application-gateway/create-ssl-portal.md)
- Desabilitar [TLS v 1.0 e v 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Firewall do aplicativo Web](../../application-gateway/waf-overview.md) (Modo WAF)
- [Modo de prevenção](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) com conjunto de regras OWASP 3,0

### <a name="business-continuity"></a>Continuidade do negócio

**Elevada disponibilidade**: Pelo menos uma máquina virtual está disponível durante um evento de manutenção planejada ou não planejada, atendendo ao SLA de 99,95% do Azure. A solução implanta todas as máquinas virtuais da camada de dados e da camada da Web em um [conjunto de disponibilidade](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Os conjuntos de disponibilidade garantem que as máquinas virtuais sejam distribuídas entre vários clusters de hardware isolados para melhorar a disponibilidade. Além disso, essa solução implanta o SQL Server máquinas virtuais em um conjunto de disponibilidade como um [grupo de disponibilidade AlwaysOn](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview). O recurso de grupo de disponibilidade Always On fornece recursos de alta disponibilidade e recuperação de desastres.

**Cofre dos serviços de recuperação**: O [cofre dos serviços de recuperação](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) hospeda dados de backup e protege todas as configurações de máquinas virtuais do Azure nessa arquitetura. Com um cofre dos serviços de recuperação, os clientes podem restaurar arquivos e pastas de uma VM IaaS sem restaurar toda a VM, permitindo tempos de restauração mais rápidos.

**Testemunha de nuvem**: A [testemunha em nuvem](https://docs.microsoft.com/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness) é um tipo de testemunha de quorum de cluster de failover no Windows Server 2016 que aproveita o Azure como ponto de arbitragem. A testemunha de nuvem, como qualquer outra testemunha de quorum, obtém um voto e pode participar dos cálculos de quorum, mas usa o armazenamento de BLOBs do Azure disponível publicamente. Isso elimina a sobrecarga de manutenção extra das VMs hospedadas em uma nuvem pública.

### <a name="logging-and-auditing"></a>Registo e auditoria

Os logs de Azure Monitor fornecem log extensivo de atividade do sistema e do usuário, bem como a integridade do sistema. A solução de [logs de Azure monitor](../azure-security-disk-encryption-overview.md) coleta e analisa os dados gerados pelos recursos no Azure e em ambientes locais.

- **Logs de atividade:**  [Os logs de atividade](../../azure-monitor/platform/activity-logs-overview.md) fornecem informações sobre as operações executadas nos recursos em uma assinatura. Os logs de atividades podem ajudar a determinar o iniciador de uma operação, a hora de ocorrência e o status.
- **Logs de diagnóstico:**  Os [logs de diagnóstico](../../azure-monitor/platform/resource-logs-overview.md) são todos os logs emitidos por todos os recursos. Esses logs incluem logs do sistema de eventos do Windows, logs de armazenamento do Azure, Key Vault logs de auditoria e acesso do gateway de aplicativo e logs de firewall.
- **Arquivamento de log:**  Todos os logs de diagnóstico gravam em uma conta de armazenamento do Azure centralizada e criptografada para arquivamento. A retenção é configurável pelo usuário, até 730 dias, para atender aos requisitos de retenção específicos da organização. Esses logs se conectam a logs de Azure Monitor para processamento, armazenamento e relatórios de painel.

Além disso, as soluções de monitoramento a seguir são instaladas como parte dessa arquitetura. Observe que é responsabilidade do cliente configurar essas soluções para se alinhar com os controles de segurança FedRAMP:
-   [Avaliação do AD](../../azure-monitor/insights/ad-assessment.md): A solução de verificação de integridade Active Directory avalia o risco e a integridade dos ambientes de servidor em um intervalo regular e fornece uma lista priorizada de recomendações específicas para a infraestrutura de servidor implantada.
-   [Avaliação de antimalware](../../security-center/security-center-install-endpoint-protection.md): A solução antimalware relata sobre malware, ameaças e status de proteção.
-   [Automação do Azure](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): A solução de automação do Azure armazena, executa e gerencia runbooks.
-   [Segurança e auditoria](../../security-center/security-center-intro.md): O painel de Segurança e Auditoria fornece uma percepção de alto nível do estado de segurança dos recursos, fornecendo métricas sobre domínios de segurança, problemas notáveis, detecções, inteligência contra ameaças e consultas de segurança comuns.
-   [Avaliação do SQL](../../azure-monitor/insights/sql-assessment.md): A solução de verificação de integridade do SQL avalia o risco e a integridade dos ambientes de servidor em um intervalo regular e fornece aos clientes uma lista priorizada de recomendações específicas para a infraestrutura de servidor implantada.
-   [Gerenciamento de atualizações](../../automation/automation-update-management.md): A solução Gerenciamento de Atualizações permite o gerenciamento de clientes das atualizações de segurança do sistema operacional, incluindo o status das atualizações disponíveis e o processo de instalação das atualizações necessárias.
-   [Integridade do agente](../../monitoring/monitoring-solution-agenthealth.md): A solução Integridade do Agente relata Quantos agentes são implantados e sua distribuição geográfica, bem como Quantos agentes que não respondem e o número de agentes que estão enviando dados operacionais.
-   [Logs de atividades do Azure](../../azure-monitor/platform/collect-activity-logs.md): A solução Análise do Log de Atividades auxilia na análise dos logs de atividade do Azure em todas as assinaturas do Azure para um cliente.
-   [Controle de alterações](../../azure-monitor/platform/collect-activity-logs.md): A solução Controle de Alterações permite que os clientes identifiquem facilmente as alterações no ambiente.

Azuremonitor
[Azure monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) ajuda os usuários a controlar o desempenho, manter a segurança e identificar tendências habilitando as organizações a auditar, criar alertas e arquivar dados, incluindo o acompanhamento de chamadas à API nos recursos do Azure dos clientes.

## <a name="threat-model"></a>Modelo de ameaça
O diagrama de fluxo de dados para esta arquitetura de referência está disponível para [Download](https://aka.ms/fedrampWAdfd) ou pode ser encontrado abaixo. Esse modelo pode ajudar os clientes a entender os pontos de risco potencial na infraestrutura do sistema ao fazer modificações.

![Aplicativo Web IaaS para modelo de ameaça FedRAMP](images/fedramp-iaaswa-threat-model.png?raw=true "Aplicativo Web IaaS para modelo de ameaça FedRAMP")

## <a name="compliance-documentation"></a>Documentação de conformidade

A [matriz de responsabilidade do cliente do Blueprint de segurança e conformidade do Azure-FedRAMP alta](https://aka.ms/blueprinthighcrm) lista todos os controles de segurança exigidos pela linha de base alta do FedRAMP. A matriz denota se a implementação de cada controle é de responsabilidade da Microsoft, do cliente ou do compartilhamento entre os dois.

A [matriz de implementação de controle alto de aplicativo Web IaaS Blueprint de segurança e conformidade do Azure FedRAMP](https://aka.ms/blueprintwacim) lista todos os controles de segurança exigidos pela linha de base FedRAMP alta. A matriz fornece informações sobre quais controles são cobertos pela arquitetura do aplicativo Web IaaS, incluindo descrições detalhadas de como a implementação atende aos requisitos de cada controle abordado.

## <a name="deploy-the-solution"></a>Implementar a solução

Essa Blueprint de Segurança e Conformidade do Azure automação é composta por arquivos de configuração JSON e scripts do PowerShell que são manipulados pelo serviço de API do Azure Resource Manager para implantar recursos no Azure. Instruções de implantação detalhadas estão disponíveis [aqui](https://aka.ms/fedrampblueprintrepo).
> [!NOTE]
> Esta solução é implantada no Azure governamental.

#### <a name="quickstart"></a>Início Rápido
1. Clone ou baixe [este](https://aka.ms/fedrampblueprintrepo) repositório GitHub em sua estação de trabalho local.

2. Execute o script de pré-implantação do PowerShell: Azure-Blueprint/PreDeploy/Orchestration_InitialSetup. ps1.

3. Clique no botão abaixo, entre no portal do Azure, insira os parâmetros de modelo ARM necessários e clique em **comprar**.

    [![Implementar no Azure](https://azuredeploy.net/AzureGov.png)](https://portal.azure.us/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Ffedramp-iaas-webapp%2Fmaster%2Fazuredeploy.json)

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
