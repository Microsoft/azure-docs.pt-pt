---
title: Segurança do Azure e o esquema de conformidade - aplicação Web de IaaS de três camadas para UK OFFICIAL
description: Segurança do Azure e o esquema de conformidade - aplicação Web de IaaS de três camadas para UK OFFICIAL
services: security
author: jomolesk
ms.assetid: 9c32e836-0564-4906-9e15-f070d2707e63
ms.service: security
ms.topic: article
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: 13ea2b68027c81bca7b43cef62cf7039aa0ea8dd
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57443475"
---
# <a name="azure-security-and-compliance-blueprint---three-tier-iaas-web-application-for-uk-official"></a>Segurança do Azure e o esquema de conformidade - aplicação Web de IaaS de três camadas para UK OFFICIAL

## <a name="overview"></a>Descrição geral

 Este artigo fornece orientações e automação de scripts para fornecer uma arquitetura de web de três camadas com base do Microsoft Azure, adequada para processar cargas de trabalho muitos classificadas como oficial no Reino Unido.

 Utilize uma infraestrutura como código abordagem, o conjunto de [do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) modelos implementam um ambiente que se alinha para do Reino Unido National Cyber Security Centre (NCSC) 14 [princípios de segurança de Cloud](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) o Centro de segurança de Internet (CIS) e [controlos de segurança críticas](https://www.cisecurity.org/critical-controls.cfm).

 O NCSC recomendar seus princípios de segurança de Cloud ser utilizado pelos clientes para avaliar as propriedades de segurança do serviço e para ajudar a compreender a divisão de responsabilidade entre o cliente e fornecedor. Fornecemos informações em relação a cada um desses princípios para ajudar a compreender a divisão das responsabilidades.

 Esta arquitetura e modelos do Azure Resource Manager correspondente são compatíveis com o White Paper [utilizar o Microsoft Azure na cloud de 14 controlos de segurança de Cloud para o RU](https://gallery.technet.microsoft.com/14-Cloud-Security-Controls-670292c1). Este documento catálogos de serviços como o Azure se alinham com do Reino Unido NCSC 14 Cloud princípios de segurança, deste modo, que permite às organizações fast track sua capacidade de atender às suas obrigações reguladoras de conformidade com serviços baseados na nuvem, globalmente e no Reino Unido, o Microsoft Azure na cloud.

 Este modelo implementa a infraestrutura para a carga de trabalho. Código do aplicativo e o suporte de camada de negócio e o software de camada de dados devem ser instalados e configurados. Estão disponíveis instruções de implementação detalhados [aqui](https://aka.ms/ukwebappblueprintrepo).

 Se não tiver uma subscrição do Azure, em seguida, pode inscrever-se rapidamente e facilmente - [introdução ao Azure](https://azure.microsoft.com/get-started/).

## <a name="architecture-diagram-and-components"></a>Diagrama de arquitetura e componentes

 Os modelos do Azure fornecem uma arquitetura de aplicativos web de três camadas num ambiente de cloud do Azure que suporta cargas de trabalho do Reino Unido oficial. A arquitetura proporciona um ambiente de híbrida segura que expande uma rede no local para Azure que permite baseada na web cargas de trabalho para ser acedidos em segurança por utilizadores da empresa ou da internet.

![Aplicação Web de IaaS de três camadas para o diagrama de arquitetura de referência do Reino Unido oficial](images/ukofficial-iaaswa-architecture.png?raw=true "aplicativo Web de IaaS de três camadas para o diagrama de arquitetura de referência oficial do Reino Unido")

 Esta solução utiliza os seguintes serviços do Azure. Detalhes da arquitetura de implementação estão localizadas no [arquitetura de implantação](#deployment-architecture) secção.

Rede virtual do /16 (1) - VNet operacional
- sub-redes /24 (3) - 3 camadas (dados da Web, Biz)
- (1) /27 adiciona de sub-rede -
- sub-rede /27 (1) - sub-rede do Gateway
- sub-rede /29 (1) - sub-rede do Gateway de aplicação
- Utiliza predefinido DNS (fornecida pelo Azure)
- Peering ativado a gestão de VNet
- Grupo de segurança da rede (NSG) para o gerenciamento de fluxo de tráfego

Rede virtual do /24 (1) - VNet de gestão
- sub-rede de /27 (1)
- Utiliza DNS adiciona (2) e entradas de DNS do Azure (1)
- Peering ativado a VNet operacional
- Grupo de segurança da rede (NSG) para o gerenciamento de fluxo de tráfego

(1) Gateway de aplicação
- WAF - ativada
- Modo de WAF - prevenção
- Conjunto de regras: OWASP 3.0
- Serviço de escuta de HTTP na porta 80
- Conectividade/tráfego regulado por meio do NSG
- Ponto final de endereço IP público definido (Azure)

(1) VPN - baseado na rota, túnel VPN IPSec de 2 de Site a Site
- Ponto final de endereço IP público definido (Azure)
- Conectividade/tráfego regulado por meio do NSG
- (1) gateway de rede local (ponto de extremidade no local)
- (1) gateway de rede do azure (ponto final do Azure)

(9) as máquinas virtuais - todas as VMs são implementadas com configurações de DSC de Antimalware de IaaS do Azure

- (2) Active Directory Domain Services Domain Controllers (Windows Server 2012 R2)
  - (2) funções de servidor DNS - 1 por VM
  - (2) as NICs ligadas a VNet operacional - 1 por VM
  - Ambos são associados a um domínio no domínio definidas no modelo
    - Domínio criado como parte da implementação


- (1) VM de gerenciamento de Jumpbox (anfitrião de bastião)
  - 1 NIC na VNet em modo de gestão com o endereço IP público
    - NSG é utilizado para limitar o tráfego (entrada/saída) para fontes específicas
  - Não associado a um domínio


- (2) VMs de camada web
  - (2) funções de servidor IIS - 1 por VM
  - (2) as NICs ligadas a VNet operacional - 1 por VM
  - Não associado a um domínio


- (2) VMs de camada de biz
  - (2) as NICs ligadas a VNet operacional - 1 por VM
  - Não associado a um domínio


- (2) VMs de camada de dados
  - (2) as NICs ligadas a VNet operacional - 1 por VM
  - Não associado a um domínio

Conjuntos de Disponibilidade
- (1) definir o controlador de domínio Active Directory do VM - 2 VMs
- (1) definir a camada de web VM - 2 VMs
- (1) defina a VM de escalão biz - 2 VMs
- (1) defina a VM de escalão de dados - 2 VMs

Load balancer
- (1) Balanceador de carga de camada web
- (1) Balanceador de carga de camada de biz
- (1) Balanceador de carga de camada de dados

Armazenamento
- (14) contas de armazenamento total
  - Conjunto de disponibilidade de controlador de domínio do Active Directory
    - (2) contas de armazenamento localmente redundante (LRS) primárias - 1 para cada VM  
    - (1) o diagnóstico conta de armazenamento localmente redundante (LRS) para adiciona conjunto de disponibilidade
  - VM da Jumpbox de gestão
    - (1) primário armazenamento localmente redundante (LRS) da conta para a VM da Jumpbox
    - (1) diagnóstico conta de armazenamento localmente redundante (LRS) para a VM da Jumpbox
  - VMs de camada de Web
    - (2) contas de armazenamento localmente redundante (LRS) primárias - 1 para cada VM  
    - (1) o diagnóstico conta de armazenamento localmente redundante (LRS) para o conjunto de disponibilidade de escalão na Web
  - VMs de camada de Biz
    - (2) contas de armazenamento localmente redundante (LRS) primárias - 1 para cada VM  
    - (1) o diagnóstico conta de armazenamento localmente redundante (LRS) para o conjunto de disponibilidade de escalão do Biz
  - VMs de camada de dados
    - (2) contas de armazenamento localmente redundante (LRS) primárias - 1 para cada VM  
    - (1) o diagnóstico conta de armazenamento localmente redundante (LRS) para o conjunto de disponibilidade de dados de camada

### <a name="deployment-architecture"></a>Arquitetura de implantação:

**Rede no local**: Uma rede de área local privada implementada numa organização.

**VNet de produção**: A produção [VNet](https://docs.microsoft.com/azure/Virtual-Network/virtual-networks-overview) (rede Virtual) que aloja a aplicação e outros recursos operacionais em execução no Azure. Cada VNet pode conter várias sub-redes que são utilizadas para isolar e gestão de tráfego de rede.

**Camada da Web**: Processa pedidos HTTP recebidos. As respostas são devolvidas por meio desta camada.

**Camada de negócio**: Os processos de negócios implementa e outra lógica funcional para o sistema.

**Camada de base de dados**: Fornece armazenamento de dados persistente, usando [SQL Server grupos de Disponibilidade AlwaysOn](https://msdn.microsoft.com/library/hh510230.aspx) para elevada disponibilidade. Os clientes podem utilizar [base de dados do Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview) como uma alternativa de PaaS.

**Gateway**: O [Gateway de VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) fornece conectividade entre os routers na rede no local e a VNet de produção.

**Gateway de Internet e o endereço IP público**: O gateway de internet expõe serviços da aplicação aos utilizadores através da internet. Aceder a estes serviços de tráfego protegido com um [Gateway de aplicação](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) oferecem capacidades com a proteção do web application firewall (WAF) de balanceamento de carga e encaminhamento de camada 7.

**VNet de gestão**: Isso [VNet](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) contém recursos que implementam a gestão e monitorização de recursos para as cargas de trabalho em execução na VNet de produção.

**Jumpbox**: Também chamado de um [anfitrião de bastião](https://en.wikipedia.org/wiki/Bastion_host), que é uma VM segura na rede que os administradores utilizam para ligar a VMs de VNet de produção. A jumpbox tem um NSG que permite tráfego remoto apenas a partir de endereços IP públicos numa lista segura. Para permitir o tráfego de (protocolo RDP) de área de trabalho remoto, a origem do tráfego deve ser definido no NSG. Gestão de recursos de produção é via RDP com uma VM protegida da Jumpbox.

**Rotas definidas pelo utilizador**: [Rotas definidas pelo utilizador](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) são utilizados para definir o fluxo de tráfego IP nas Azure VNets.

**VNETs em modo de Peering de rede**: A produção e VNets de gestão estão ligados através de [VNet peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).
Nestas VNets continuam a ser geridas como recursos separados, mas aparecem como uma única para todos os fins de conectividade para estas máquinas virtuais. Estas redes comunicarem entre si diretamente através de endereços IP privados. VNet peering está sujeito a VNets a ser na mesma região do Azure.

**Grupos de segurança de rede**: [NSGs](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) contêm listas de controlo de acesso que permitem ou negam o tráfego numa VNet. Podem ser utilizados NSGs para proteger o tráfego numa sub-rede ou o nível VM individual.

**Serviços de domínio do Active Directory (AD DS)**: Esta arquitetura fornece um dedicado [serviços de domínio do Active Directory](https://technet.microsoft.com/library/hh831484.aspx) implementação.

**Registo e auditoria**: [Registo de atividades do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) operações de capturas de tomar os recursos na sua subscrição, tais como quem a iniciou a operação, aquando da ocorrência da operação, o estado da operação e os valores de outras propriedades que podem ajudá-lo a pesquisar o operação. Registo de atividades do Azure é um serviço de plataforma do Azure que captura todas as ações numa assinatura. Os registos podem ser arquivados ou exportados se necessário.

**Rede de monitorização e alertas**: [O observador de rede do Azure](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) é um serviço de plataforma fornece a captura de pacotes de rede, registo do fluxo, ferramentas de topologia e diagnósticos para tráfegos de rede nas suas VNets.

## <a name="guidance-and-recommendations"></a>Orientações e recomendações

### <a name="business-continuity"></a>Continuidade do Negócio

**Elevada disponibilidade**: Cargas de trabalho do servidor são agrupadas num [conjunto de disponibilidade](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para ajudar a garantir a elevada disponibilidade de máquinas virtuais no Azure. Esta configuração ajuda a garantir que durante um evento de manutenção planeada ou, pelo menos, uma máquina virtual irá estar disponível e cumpre 99,95% do SLA do Azure.

### <a name="logging-and-audit"></a>Registo e auditoria

**Monitorização**: [O Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started) é o serviço de plataforma que fornece uma única origem para monitorizar o registo de atividades, métricas e registos de diagnóstico de todos os recursos do Azure. O Azure Monitor pode ser configurado para visualizar, consultar, encaminhar, arquivar e agir relativamente a métricas e registos provenientes de recursos no Azure. Recomenda-se que o controlo de acesso baseado em recursos é utilizado para proteger o registo de auditoria para ajudar a garantir que os utilizadores não têm a capacidade de modificar os registos.

**Registos de atividades**: Configurar [registos de atividades do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) para fornecer informações sobre as operações executadas nos recursos na sua subscrição.

**Os registos de diagnóstico**: [Os registos de diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) são emitidos por um recurso de todos os registos. Estes registos podem incluir registos de sistema de eventos do Windows, blob, tabela e registos de fila.

**Registos de firewall**: Gateway de aplicação fornece registos de diagnóstico e de acesso completos. Os registos de firewall estão disponíveis para recursos de gateway de aplicações que tenham a WAF ativada.

**Arquivo de log**: Armazenamento de dados de registo pode ser configurado para escrever para uma conta de armazenamento do Azure centralizado para arquivamento e um período de retenção definida. Os registos podem ser processados através de registos do Azure Monitor ou por sistemas SIEM de terceiros.

### <a name="identity"></a>Identidade

**Serviços de domínio do Active Directory**: Esta arquitetura fornece uma implementação de serviços de domínio do Active Directory no Azure. Para obter recomendações específicas sobre a implementação do Active Directory no Azure, veja os artigos seguintes:

[Expandir os serviços de domínio do Active Directory (AD DS) para o Azure](https://docs.microsoft.com/azure/guidance/guidance-identity-adds-extend-domain).

[Diretrizes para implementar o Windows Server Active Directory em máquinas virtuais do Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx).

**Integração do Active Directory**: Como alternativa para uma arquitetura dedicada do AD DS, os clientes podem querer por utilizar [do Azure Active Directory](https://docs.microsoft.com/azure/guidance/guidance-ra-identity) integração ou [do Active Directory no Azure associado a uma floresta no local](https://docs.microsoft.com/azure/guidance/guidance-ra-identity).

### <a name="security"></a>Segurança

**Segurança da gestão**: Este esquema permite aos administradores ligar para a gestão de VNet e a Jumpbox através de RDP de uma origem fidedigna. Tráfego de rede para a gestão de VNet é controlado com NSGs. Acesso à porta 3389 é restrito para o tráfego a partir de um intervalo IP fidedigno que pode aceder a sub-rede que contém a Jumpbox.

Os clientes também podem considerar utilizar um [modelo administrativo de maior segurança](https://technet.microsoft.com/windows-server-docs/security/securing-privileged-access/securing-privileged-access) para proteger o ambiente ao ligar-se para a gestão de VNet e a Jumpbox. Recomenda-se que para uma maior segurança os clientes utilizem uma [estação de trabalho de acesso privilegiado](https://technet.microsoft.com/windows-server-docs/security/securing-privileged-access/privileged-access-workstations#what-is-a-privileged-access-workstation-paw) e a configuração do gateway de RD. A utilização de aplicações virtuais de rede e públicas/privadas DMZs permite-lhe ainda mais melhorias de segurança.

**Proteger a rede**: [Grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) (NSGs) são recomendados para cada sub-rede fornecer um segundo nível de proteção contra o tráfego de entrada ao ignorar um gateway desativado ou incorretamente configurado. Exemplo - [modelo do Resource Manager para implementar um NSG](https://github.com/mspnp/template-building-blocks/tree/v1.0.0/templates/buildingBlocks/networkSecurityGroups).

**Proteger pontos finais públicos**: O gateway de internet expõe serviços da aplicação aos utilizadores através da internet. Aceder a estes serviços de tráfego protegido com um [Gateway de aplicação](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction), que fornece um gerenciamento de protocolo de Firewall de aplicações Web e HTTPS.

**Intervalos de IP**: Os intervalos IP na arquitetura são sugeridos intervalos. Os clientes são aconselhados a considerar seu próprio ambiente e utilizar intervalos adequados.

**Conectividade híbrida**: As cargas de trabalho com base na cloud estão ligadas ao centro de dados no local através de VPN IPSEC com o Gateway de VPN do Azure. Os clientes devem certificar-se de que estão usando um Gateway de VPN apropriados para ligar ao Azure. Exemplo - [modelo de Gestor de recursos de Gateway de VPN](https://github.com/mspnp/template-building-blocks/tree/v1.0.0/templates/buildingBlocks/vpn-gateway-vpn-connection). Os clientes em execução em grande escala, as cargas de trabalho críticas de missão requisitos de grandes volumes de dados podem desejar considerar um através de arquitetura de rede do híbrida [ExpressRoute](https://docs.microsoft.com/azure/guidance/guidance-hybrid-network-expressroute) para conectividade de rede privada para a Microsoft de serviços cloud.

**Separação das preocupações**: Esta arquitetura de referência separa as VNets para operações de gestão e operações comerciais. Separado VNets e sub-redes permitem a gestão de tráfego, incluindo restrições de entrada e saída de tráfego, através dos NSGs entre segmentos de rede a seguir [segurança de rede e serviços de nuvem do Microsoft](https://docs.microsoft.com/azure/best-practices-network-security) melhores práticas.

**Gestão de recursos**: Recursos do Azure como VMs, VNets e Balanceadores de carga são geridos ao agrupá-los em [grupos de recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). Em seguida, podem ser atribuídas a funções de controlo de acesso baseado em recursos para cada grupo de recursos para restringir o acesso a utilizadores autorizados apenas.

**Restrições de controlo de acesso**: Uso [controlo de acesso baseado em funções](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) (RBAC) para gerir os recursos na sua aplicação utilizando [funções personalizadas](https://docs.microsoft.com/azure/role-based-access-control/custom-roles) RBAC pode ser utilizado para restringir as operações que DevOps pode realizar em cada camada. Ao conceder permissões, utilize o [princípio do menor privilégio](https://msdn.microsoft.com/library/hdb58b2f(v=vs.110).aspx#Anchor_1). Registe todas as operações administrativas e realize auditorias regulares para garantir que as alterações de configuração foram planeadas.

**Acesso à Internet**: Esta arquitetura de referência utiliza [Gateway de aplicação Azure](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) como Balanceador de carga e de gateway com acesso à internet. Alguns clientes podem também considerar a utilização de aplicações virtuais de rede de terceiros para camadas adicionais de redes de segurança como uma alternativa para o [Gateway de aplicação Azure](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction).

**Centro de segurança do Azure**: O [Centro de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro) fornece uma visão central do Estado de segurança de recursos na subscrição e fornece recomendações que ajudam a evitar recursos comprometidos. Também pode ser utilizado para ativar as políticas mais granulares. Por exemplo, as políticas podem ser aplicadas a grupos de recurso específico, que permite que a empresa a criar a sua postura de risco. Recomenda-se que os clientes ativarem o Centro de segurança do Azure na respetiva subscrição do Azure.

## <a name="ncsc-cloud-security-principles-compliance-documentation"></a>Documentação de conformidade de princípios de segurança de Cloud NCSC

O serviço comercial Crown (uma agência de que funciona para melhorar a atividade comercial e compra pelo Governo) renovado a classificação de serviços cloud da Microsoft no âmbito enterprise para v6 G-Cloud, que abrange todas as suas ofertas ao nível OFFICIAL. Detalhes do Azure e G-Cloud podem ser encontrados no [resumo de avaliação de segurança do Azure UK G-Cloud](https://www.microsoft.com/en-us/trustcenter/compliance/uk-g-cloud).

Este esquema alinha os princípios de segurança de 14 cloud documentados no NCSC [princípios de segurança de Cloud](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) para ajudar a garantir um ambiente que suporta cargas de trabalho classificadas como UK-OFFICIAL.

O [matriz de responsabilidade do cliente](https://aka.ms/ukofficial-crm) (livro do Excel) apresenta uma lista de todos os princípios de segurança de 14 cloud e, para cada princípio (ou subpart princípio), a matriz indica se a implementação de princípio é da responsabilidade do Microsoft, o cliente, ou partilhados entre os dois.

O [matriz de implementação de princípio](https://aka.ms/ukofficial-iaaswa-pim) denota de todos os princípios de segurança de 14 cloud e a matriz de listas de (livro do Excel), para cada princípio (ou subpart princípio) que é designado uma responsabilidade do cliente no cliente Matriz de responsabilidades, 1) se a automação de esquema implementa o princípio e 2) uma descrição de como a implementação se alinha com os seguintes requisitos de princípio.

Além disso, a Cloud Security Alliance (CSA) publicado a matriz de controle de Cloud para suportar clientes na avaliação de fornecedores de serviços cloud e para identificar questões que devem ser abordadas antes de mudar para serviços cloud. Em resposta, o Microsoft Azure respondidas o questionário de iniciativa de avaliação de consenso CSA ([CSA CAIQ](https://www.microsoft.com/en-us/TrustCenter/Compliance/CSA)), que descreve como a Microsoft lida com os princípios sugeridos.

## <a name="deploy-the-solution"></a>Implementar a solução

Existem dois métodos que os utilizadores de implantação podem utilizar para implementar esta automatização de esquema. O primeiro método usa um script do PowerShell, ao passo que o segundo método utiliza o portal do Azure para implementar a arquitetura de referência. Estão disponíveis instruções de implementação detalhados [aqui](https://aka.ms/ukofficial-iaaswa-repo).

## <a name="disclaimer"></a>Exclusão de Responsabilidade

 - Este documento é apenas para fins informativos. A MICROSOFT FAZ NÃO OFERECE QUAISQUER GARANTIAS, EXPRESSAS, IMPLÍCITAS OU ESTATUTÁRIAS QUANTO ÀS INFORMAÇÕES CONTIDAS NESTE DOCUMENTO. Este documento é fornecido "como-está." Informações e opiniões expressados neste documento, incluindo URLs e outras referências de Web site da Internet, podem ser alteradas sem aviso prévio. Os clientes a ler este documento assume o risco de usá-lo.
 - Este documento não fornece aos clientes quaisquer direitos de propriedade intelectual em qualquer produto da Microsoft ou soluções.
 - Os clientes podem copiar e utilizar este documento para efeitos de referência interna.
 - Algumas recomendações neste documento podem resultar em maior de dados, a rede ou a utilização de recursos de computação no Azure e podem aumentar os custos de licenciamento ou de subscrição do Azure num cliente.
 - Esta arquitetura deve ser usado como uma base para os clientes que se ajustar às suas necessidades específicas e não devem ser utilizada como-está num ambiente de produção.
 - Este documento é desenvolvido como uma referência e não deve ser usado para definir todos os meios pelos quais um cliente pode atender aos requisitos de conformidade e normas. Os clientes procuram legal suporte da sua organização em implementações do cliente aprovadas.
