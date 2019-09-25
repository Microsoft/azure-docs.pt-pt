---
title: Blueprint de Segurança e Conformidade do Azure-aplicativo Web IaaS de três camadas para o Reino Unido
description: Blueprint de Segurança e Conformidade do Azure-aplicativo Web IaaS de três camadas para o Reino Unido
services: security
author: jomolesk
ms.assetid: 9c32e836-0564-4906-9e15-f070d2707e63
ms.service: security
ms.topic: article
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: 4a30e496c96fcc90417e58b0f921717985b89693
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262804"
---
# <a name="azure-security-and-compliance-blueprint---three-tier-iaas-web-application-for-uk-official"></a>Blueprint de Segurança e Conformidade do Azure-aplicativo Web IaaS de três camadas para o Reino Unido

## <a name="overview"></a>Descrição geral

 Este artigo fornece orientações e scripts de automação para fornecer um Microsoft Azure arquitetura baseada na Web de três camadas apropriada para lidar com muitas cargas de trabalho classificadas como oficiais no Reino Unido.

 Usando uma infraestrutura como abordagem de código, o conjunto de modelos de [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) implanta um ambiente que se alinha aos [princípios de segurança de nuvem](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) do ncsc (National Cyber Cloud Security Centre) 14 e ao CIS (Center for Internet Security) [ Controles de segurança críticos](https://www.cisecurity.org/critical-controls.cfm).

 O NCSC recomenda que seus princípios de segurança de nuvem sejam usados pelos clientes para avaliar as propriedades de segurança do serviço e para ajudar a entender a divisão de responsabilidade entre o cliente e o fornecedor. Fornecemos informações sobre cada um desses princípios para ajudá-lo a entender a divisão de responsabilidades.

 Essa arquitetura e os modelos de Azure Resource Manager correspondentes têm suporte no White paper da Microsoft, [14 controles de segurança na nuvem para a nuvem do Reino Unido usando Microsoft Azure](https://gallery.technet.microsoft.com/14-Cloud-Security-Controls-670292c1). Este documento cataloga como os serviços do Azure se alinham aos princípios de segurança de nuvem do Reino Unido NCSC 14, permitindo que as organizações acompanhem rapidamente sua capacidade de atender às obrigações de conformidade usando serviços baseados em nuvem globalmente e no Reino Unido no Microsoft Azure nuvem.

 Este modelo implanta a infraestrutura para a carga de trabalho. O código do aplicativo e a camada de negócios de suporte e o software da camada de dados devem ser instalados e configurados. Instruções de implantação detalhadas estão disponíveis [aqui](https://aka.ms/ukwebappblueprintrepo).

 Se você não tiver uma assinatura do Azure, poderá se inscrever de forma rápida e fácil [com o Azure](https://azure.microsoft.com/get-started/).

## <a name="architecture-diagram-and-components"></a>Diagrama e componentes da arquitetura

 Os modelos do Azure fornecem uma arquitetura de aplicativo Web de três camadas em um ambiente de nuvem do Azure que dá suporte a cargas de trabalho oficiais do Reino Unido. A arquitetura fornece um ambiente híbrido seguro que estende uma rede local para o Azure, permitindo que cargas de trabalho baseadas na Web sejam acessadas com segurança por usuários corporativos ou pela Internet.

![Aplicativo Web IaaS de três camadas para o diagrama de arquitetura de referência oficial do Reino Unido](images/ukofficial-iaaswa-architecture.png?raw=true "Aplicativo Web IaaS de três camadas para o diagrama de arquitetura de referência oficial do Reino Unido")

 Essa solução usa os seguintes serviços do Azure. Os detalhes da arquitetura de implantação estão localizados na seção [arquitetura de implantação](#deployment-architecture) .

(1)/16 rede virtual-VNet operacional
- (3)/24 sub-redes-3-camadas (Web, Biz, dados)
- (1)/27 subnet – ADDS
- (1)/27 sub-rede de gateway de sub-rede
- (1)/29 sub-rede-sub-rede do gateway de aplicativo
- Usa o DNS padrão (fornecido pelo Azure)
- Emparelhamento habilitado para VNet de gerenciamento
- NSG (grupo de segurança de rede) para gerenciar o fluxo de tráfego

(1)/24 rede virtual-VNet de gerenciamento
- (1)/27 sub-rede
- Usa (2) adiciona DNS e (1) entradas DNS do Azure
- Emparelhamento habilitado para VNet operacional
- NSG (grupo de segurança de rede) para gerenciar o fluxo de tráfego

(1) gateway de aplicativo
- Habilitado para WAF
- Modo de WAF – prevenção
- Conjunto de regras: OWASP 3.0
- Ouvinte HTTP na porta 80
- Conectividade/tráfego regulamentado por meio de NSG
- Ponto de extremidade de endereço IP público definido (Azure)

(1) túnel VPN IPSec site a site baseado em rota de VPN
- Ponto de extremidade de endereço IP público definido (Azure)
- Conectividade/tráfego regulamentado por meio de NSG
- (1) gateway de rede local (ponto de extremidade no local)
- (1) gateway de rede do Azure (ponto de extremidade do Azure)

(9) máquinas virtuais-todas as VMs são implantadas com as configurações de DSC antimalware do Azure IaaS

- (2) Active Directory Domain Services controladores de domínio (Windows Server 2012 R2)
  - (2) funções de servidor DNS-1 por VM
  - (2) NICs conectadas à VNet operacional-1 por VM
  - Ambos são ingressados no domínio para o domínio definido no modelo
    - Domínio criado como parte da implantação


- (1) VM de gerenciamento Jumpbox (host de bastiões)
  - 1 NIC na VNet de gerenciamento com endereço IP público
    - NSG é usado para limitar o tráfego (entrada/saída) para fontes específicas
  - Não ingressado no domínio


- (2) VMs da camada da Web
  - (2) funções de servidor do IIS-1 por VM
  - (2) NICs conectadas à VNet operacional-1 por VM
  - Não ingressado no domínio


- (2) VMs de camada do Biz
  - (2) NICs conectadas à VNet operacional-1 por VM
  - Não ingressado no domínio


- (2) VMs da camada de dados
  - (2) NICs conectadas à VNet operacional-1 por VM
  - Não ingressado no domínio

Conjuntos de Disponibilidade
- (1) conjunto de VM do controlador Domínio do Active Directory-2 VMs
- (1) conjunto de VM de camada da Web-2 VMs
- (1) conjunto de VMs de camada do Biz-2 máquinas virtuais
- (1) conjunto de VM de camada de dados-2 VMs

Balanceador de Carga
- (1) Load Balancer da camada da Web
- (1) Load Balancer da camada do Biz
- (1) Load Balancer da camada de dados

Armazenamento
- (14) total de contas de armazenamento
  - Conjunto de disponibilidade do controlador de Domínio do Active Directory
    - (2) contas de LRS (armazenamento com redundância local) primárias-1 para cada VM  
    - (1) conta de LRS (armazenamento com redundância local) de diagnóstico para o adiciona o conjunto de disponibilidade
  - VM Jumpbox de gerenciamento
    - (1) conta de armazenamento com redundância local (LRS) primária para a VM Jumpbox
    - (1) conta de LRS (armazenamento com redundância local) de diagnóstico para a VM Jumpbox
  - VMs da camada da Web
    - (2) contas de LRS (armazenamento com redundância local) primárias-1 para cada VM  
    - (1) conta de LRS (armazenamento com redundância local) de diagnóstico para o conjunto de disponibilidade da camada da Web
  - VMs da camada do Biz
    - (2) contas de LRS (armazenamento com redundância local) primárias-1 para cada VM  
    - (1) conta de LRS (armazenamento com redundância local) de diagnóstico para o conjunto de disponibilidade da camada do Biz
  - VMs da camada de dados
    - (2) contas de LRS (armazenamento com redundância local) primárias-1 para cada VM  
    - (1) conta de LRS (armazenamento com redundância local) de diagnóstico para o conjunto de disponibilidade da camada de dados

### <a name="deployment-architecture"></a>Arquitetura de implantação:

**Rede local**: Uma rede de área local privada implementada em uma organização.

**VNet de produção**: A [VNet](https://docs.microsoft.com/azure/Virtual-Network/virtual-networks-overview) de produção (rede virtual) hospeda o aplicativo e outros recursos operacionais em execução no Azure. Cada VNet pode conter várias sub-redes que são usadas para isolar e gerenciar o tráfego de rede.

**Camada da Web**: Manipula solicitações HTTP de entrada. As respostas são retornadas por essa camada.

**Camada de negócios**: Implementa processos de negócios e outras lógicas funcionais para o sistema.

**Camada de banco de dados**: Fornece armazenamento de dados persistente, usando [SQL Server Always on grupos de disponibilidade](https://msdn.microsoft.com/library/hh510230.aspx) para alta disponibilidade. Os clientes podem usar o [banco de dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview) como uma alternativa de PaaS.

**Gateway**: O [Gateway de VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) fornece conectividade entre os roteadores na rede local e a VNet de produção.

**Gateway de Internet e endereço IP público**: O gateway de Internet expõe os serviços de aplicativo aos usuários pela Internet. O tráfego que acessa esses serviços é protegido usando um [Gateway de aplicativo](../../application-gateway/overview.md) que oferece recursos de balanceamento de carga e roteamento da camada 7 com proteção do WAF (firewall do aplicativo Web).

**VNet de gerenciamento**: Essa [VNet](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) contém recursos que implementam recursos de gerenciamento e monitoramento para as cargas de trabalho em execução na VNet de produção.

**Jumpbox**: Também chamado de [host de bastiões](https://en.wikipedia.org/wiki/Bastion_host), que é uma VM segura na rede que os administradores usam para se conectar a VMs na VNet de produção. A jumpbox tem um NSG que permite tráfego remoto apenas a partir de endereços IP públicos numa lista segura. Para permitir o tráfego de área de trabalho remota (RDP), a origem do tráfego precisa ser definida no NSG. O gerenciamento de recursos de produção é via RDP usando uma VM Jumpbox protegida.

**Rotas definidas pelo usuário**: As [rotas definidas pelo usuário](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) são usadas para definir o fluxo de tráfego IP no Azure VNets.

**VNETs emparelhadas de rede**: Os VNets de produção e de gerenciamento são conectados usando o [emparelhamento VNet](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).
Esses VNets ainda são gerenciados como recursos separados, mas aparecem como um para todas as finalidades de conectividade para essas máquinas virtuais. Essas redes se comunicam entre si diretamente usando endereços IP privados. O emparelhamento VNet está sujeito ao VNets que está na mesma região do Azure.

**Grupos de segurança de rede**: [NSGs](../../virtual-network/virtual-network-vnet-plan-design-arm.md) contêm listas de controle de acesso que permitem ou negam o tráfego em uma VNet. NSGs pode ser usado para proteger o tráfego em uma sub-rede ou nível de VM individual.

**Active Directory Domain Services (AD DS)** : Essa arquitetura fornece uma implantação de [Active Directory Domain Services](https://technet.microsoft.com/library/hh831484.aspx) dedicada.

**Registro em log e auditoria**: O [log de atividades do Azure](../../azure-monitor/platform/activity-logs-overview.md) captura as operações executadas nos recursos em sua assinatura, como quem iniciou a operação, quando a operação ocorreu, o status da operação e os valores de outras propriedades que podem ajudá-lo a Pesquisar a operação. O log de atividades do Azure é um serviço da plataforma Azure que captura todas as ações em uma assinatura. Os logs podem ser arquivados ou exportados, se necessário.

**Monitoramento e alertas de rede**: O [observador de rede do Azure](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) é um serviço de plataforma que fornece captura de pacotes de rede, registro em log de fluxo, ferramentas de topologia e diagnóstico para tráfegos de rede em seu VNets.

## <a name="guidance-and-recommendations"></a>Diretrizes e recomendações

### <a name="business-continuity"></a>Continuidade do Negócio

**Alta disponibilidade**: As cargas de trabalho do servidor são agrupadas em um [conjunto de disponibilidade](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para ajudar a garantir a alta disponibilidade de máquinas virtuais no Azure. Essa configuração ajuda a garantir que durante um evento de manutenção planejada ou não planejada pelo menos uma máquina virtual estará disponível e atenderá ao SLA de 99,95% do Azure.

### <a name="logging-and-audit"></a>Registro em log e auditoria

**Monitoramento**: [Azure monitor](../../azure-monitor/overview.md) é o serviço de plataforma que fornece uma única fonte para monitorar o log de atividades, as métricas e os logs de diagnóstico de todos os seus recursos do Azure. Azure Monitor pode ser configurado para visualizar, consultar, rotear, arquivar e agir sobre as métricas e os logs provenientes dos recursos no Azure. É recomendável que o controle de acesso baseado em recursos seja usado para proteger a trilha de auditoria para ajudar a garantir que os usuários não tenham a capacidade de modificar os logs.

**Logs de atividade**: Configure [os logs de atividades do Azure](../../azure-monitor/platform/activity-logs-overview.md) para fornecer informações sobre as operações que foram realizadas em recursos em sua assinatura.

**Logs de diagnóstico**: Os [logs de diagnóstico](../../azure-monitor/platform/resource-logs-overview.md) são todos os logs emitidos por um recurso. Esses logs podem incluir logs do sistema de eventos do Windows, BLOB, tabela e logs de fila.

**Logs de firewall**: O gateway de aplicativo fornece logs completos de diagnóstico e acesso. Os registos de firewall estão disponíveis para recursos de gateway de aplicações que tenham a WAF ativada.

**Arquivamento de log**: O armazenamento de dados de log pode ser configurado para gravar em uma conta de armazenamento centralizada do Azure para arquivamento e um período de retenção definido. Os logs podem ser processados usando logs de Azure Monitor ou por sistemas SIEM de terceiros.

### <a name="identity"></a>identidade

**Active Directory Domain Services**: Essa arquitetura fornece uma implantação Active Directory Domain Services no Azure. Para obter recomendações específicas sobre a implementação do Active Directory no Azure, veja os artigos seguintes:

[Estendendo Active Directory Domain Services (AD DS) para o Azure](/azure/architecture/reference-architectures/identity/adds-extend-domain).

[Diretrizes para implantar o Windows Server Active Directory em máquinas virtuais do Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx).

**Integração do Active Directory**: Como alternativa a uma arquitetura de AD DS dedicada, os clientes podem querer usar a integração [Azure Active Directory](/azure/architecture/reference-architectures/identity) ou [Active Directory no Azure ingressado em uma floresta local](/azure/architecture/reference-architectures/identity).

### <a name="security"></a>Segurança

**Segurança de gerenciamento**: Este plano gráfico permite que os administradores se conectem à VNet de gerenciamento e Jumpbox usando o RDP de uma fonte confiável. O tráfego de rede para a VNet de gerenciamento é controlado usando NSGs. O acesso à porta 3389 é restrito ao tráfego de um intervalo de IP confiável que pode acessar a sub-rede que contém o Jumpbox.

Os clientes também podem considerar o uso de um [modelo administrativo de segurança aprimorado](https://technet.microsoft.com/windows-server-docs/security/securing-privileged-access/securing-privileged-access) para proteger o ambiente ao se conectarem à VNet de gerenciamento e Jumpbox. É recomendável que, para os clientes de segurança avançada, usem uma [estação de trabalho com acesso privilegiado](https://technet.microsoft.com/windows-server-docs/security/securing-privileged-access/privileged-access-workstations#what-is-a-privileged-access-workstation-paw) e a configuração do RDGateway. O uso de soluções de virtualização de rede e DMZs pública/privada oferecerá aprimoramentos de segurança adicionais.

**Protegendo a rede**: [Grupos de segurança de rede](../../virtual-network/virtual-network-vnet-plan-design-arm.md) (NSGs) são recomendadas para cada sub-rede fornecer um segundo nível de proteção contra o tráfego de entrada, ignorando um gateway incorretamente configurado ou desabilitado. Exemplo – [modelo do Resource Manager para implantar um NSG](https://github.com/mspnp/template-building-blocks/tree/v1.0.0/templates/buildingBlocks/networkSecurityGroups).

**Protegendo pontos de extremidade públicos**: O gateway de Internet expõe os serviços de aplicativo aos usuários pela Internet. O tráfego que acessa esses serviços é protegido usando um [Gateway de aplicativo](../../application-gateway/overview.md), que fornece um firewall do aplicativo Web e o gerenciamento de protocolo HTTPS.

**Intervalos de IP**: Os intervalos de IP na arquitetura são intervalos sugeridos. Os clientes são aconselhados a considerar seu próprio ambiente e usar intervalos apropriados.

**Conectividade híbrida**: As cargas de trabalho baseadas em nuvem são conectadas ao datacenter local por meio de VPN IPSEC usando o gateway de VPN do Azure. Os clientes devem garantir que estejam usando um gateway de VPN apropriado para se conectar ao Azure. Exemplo- [modelo do Gerenciador de recursos do gateway de VPN](https://github.com/mspnp/template-building-blocks/tree/v1.0.0/templates/buildingBlocks/vpn-gateway-vpn-connection). Os clientes que executam cargas de trabalho de grande escala e de missão crítica com requisitos de Big Data talvez queiram considerar uma arquitetura de rede híbrida usando o [ExpressRoute](/azure/architecture/reference-architectures/hybrid-networking/expressroute) para conectividade de rede privada para os serviços de nuvem da Microsoft.

**Separação de preocupações**: Essa arquitetura de referência separa o VNets para operações de gerenciamento e de negócios. VNets e sub-redes separadas permitem o gerenciamento de tráfego, incluindo restrições de entrada e saída de tráfego, usando o NSGs entre os segmentos de rede seguindo os [serviços de nuvem da Microsoft e](/azure/architecture/vdc/networking-virtual-datacenter) as práticas recomendadas de segurança de rede.

**Gerenciamento de recursos**: Recursos do Azure, como VMs, VNets e balanceadores de carga, são gerenciados agrupando-os em [grupos de recursos do Azure](../../azure-resource-manager/resource-group-overview.md). As funções de controle de acesso baseadas em recursos podem ser atribuídas a cada grupo de recursos para restringir o acesso somente a usuários autorizados.

**Restrições de controle de acesso**: Use o RBAC ( [controle de acesso baseado em função](../../role-based-access-control/role-assignments-portal.md) ) para gerenciar os recursos em seu aplicativo usando [funções personalizadas](../../role-based-access-control/custom-roles.md) o RBAC pode ser usado para restringir as operações que o DevOps pode executar em cada camada. Ao conceder permissões, use o [princípio de privilégios mínimos](https://msdn.microsoft.com/library/hdb58b2f(v=vs.110).aspx#Anchor_1). Registe todas as operações administrativas e realize auditorias regulares para garantir que as alterações de configuração foram planeadas.

**Acesso à Internet**: Essa arquitetura de referência utiliza [aplicativo Azure gateway](../../application-gateway/overview.md) como o gateway voltado para a Internet e o balanceador de carga. Alguns clientes também podem considerar o uso de dispositivos virtuais de rede de terceiros para camadas adicionais de segurança de rede como uma alternativa ao [Gateway de aplicativo Azure](../../application-gateway/overview.md).

**Central de segurança do Azure**: A [central de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro) fornece uma exibição central do status de segurança dos recursos na assinatura e fornece recomendações que ajudam a evitar recursos comprometidos. Ele também pode ser usado para habilitar políticas mais granulares. Por exemplo, as políticas podem ser aplicadas a grupos de recursos específicos, o que permite que a empresa Personalize sua postura de risco. É recomendável que os clientes habilitem a central de segurança do Azure em sua assinatura do Azure.

## <a name="ncsc-cloud-security-principles-compliance-documentation"></a>Documentação de conformidade dos princípios de segurança do NCSC Cloud

O serviço comercial de coroa (uma agência que trabalha para melhorar a atividade comercial e de compras pelo governo) renovou a classificação dos serviços de nuvem corporativa em escopo da Microsoft para o G-Cloud V6, cobrindo todas as suas ofertas no nível oficial. Os detalhes do Azure e do G-Cloud podem ser encontrados no [Resumo da avaliação de segurança do Azure UK G-Cloud](https://www.microsoft.com/en-us/trustcenter/compliance/uk-g-cloud).

Este projeto se alinha aos 14 princípios de segurança na nuvem que estão documentados nos [princípios de segurança de nuvem](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) do ncsc para ajudar a garantir um ambiente que ofereça suporte a cargas de trabalho classificadas como ru-Official.

A [matriz de responsabilidade do cliente](https://aka.ms/ukofficial-crm) (pasta de trabalho do Excel) lista todos os 14 princípios de segurança da nuvem, e a matriz denota, para cada princípio (ou subitem de princípio), se a implementação do princípio é de responsabilidade da Microsoft, do cliente ou do compartilhado entre os dois.

A [matriz de implementação de princípio](https://aka.ms/ukofficial-iaaswa-pim) (pasta de trabalho do Excel) lista todos os 14 princípios de segurança de nuvem e a matriz denota, para cada princípio (ou subitem de princípio) que é designado uma responsabilidade do cliente na matriz de responsabilidades do cliente, 1) se a automação do Blueprint implementa o princípio e 2) uma descrição de como a implementação se alinha com os requisitos de princípio.

Além disso, a CSA (Cloud Security Alliance) publicou a matriz de controle de nuvem para dar suporte aos clientes na avaliação de provedores de nuvem e para identificar perguntas que devem ser respondidas antes de passar para os serviços de nuvem. Em resposta, Microsoft Azure respondeu ao questionário da iniciativa de avaliação de consenso do CSA ([CSA CAIQ](https://www.microsoft.com/en-us/TrustCenter/Compliance/CSA)), que descreve como a Microsoft aborda os princípios sugeridos.

## <a name="deploy-the-solution"></a>Implantar a solução

Há dois métodos que os usuários de implantação podem usar para implantar essa automação do Blueprint. O primeiro método usa um script do PowerShell, enquanto o segundo método utiliza o portal do Azure para implantar a arquitetura de referência. Instruções de implantação detalhadas estão disponíveis [aqui](https://aka.ms/ukofficial-iaaswa-repo).

## <a name="disclaimer"></a>Exclusão de Responsabilidade

 - Este documento é apenas para fins informativos. A MICROSOFT NÃO OFERECE NENHUMA GARANTIA, EXPRESSA, IMPLÍCITA OU ESTATUTÁRIA, QUANTO ÀS INFORMAÇÕES CONTIDAS NESTE DOCUMENTO. Este documento é fornecido "no estado em que se encontra". As informações e as exibições expressas neste documento, incluindo URLs e outras referências de site da Internet, podem ser alteradas sem aviso prévio. Os clientes que lêem este documento trazem o risco de usá-lo.
 - Este documento não fornece aos clientes nenhum direito legal sobre qualquer propriedade intelectual em qualquer produto ou solução da Microsoft.
 - Os clientes podem copiar e usar este documento para fins de referência interna.
 - Determinadas recomendações neste documento podem resultar em maior uso de recursos de computação, rede ou dados no Azure, e podem aumentar os custos de assinatura ou de licença do Azure do cliente.
 - Essa arquitetura destina-se a servir como base para os clientes se ajustarem aos requisitos específicos e não devem ser usados no estado em que se encontram em um ambiente de produção.
 - Este documento é desenvolvido como uma referência e não deve ser usado para definir todos os meios pelos quais um cliente pode atender a requisitos e regulamentos específicos de conformidade. Os clientes devem buscar o suporte legal de sua organização em implementações aprovadas do cliente.
