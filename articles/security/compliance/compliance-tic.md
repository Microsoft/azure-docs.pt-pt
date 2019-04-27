---
title: Orientação de ligações de Internet fidedigna para o Azure
description: Orientações de ligações à Internet fidedigna para os serviços do Azure e o SaaS
services: security
author: dlapiduz
ms.assetid: 09511e03-a862-4443-81ac-ede815bdaf25
ms.service: security
ms.topic: article
ms.date: 06/20/2018
ms.author: dlap
ms.openlocfilehash: bb186ab2700b147bee3a7dd81474409ccafb76fc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60607947"
---
# <a name="trusted-internet-connections-guidance"></a>Documentação de orientação de ligações de Internet fidedigna

Este artigo aborda como agências do Governo podem usar o Microsoft Azure Government para ajudar a obter a conformidade com a iniciativa de ligações de Internet fidedigna (TIC). O artigo descreve como utilizar o Azure Government em toda a infraestrutura do Azure como um serviço (IaaS) e a plataforma do Azure como um ofertas de serviço (PaaS).

## <a name="trusted-internet-connections-overview"></a>Descrição geral de ligações de Internet fidedigna

O objetivo da iniciativa TIC é otimizar e padronizar a segurança de conexões de rede externa individuais que são utilizados por agências federais. A política é descrita na [Office de gestão e de orçamento (OMB) Memorandum M-08-05](https://georgewbush-whitehouse.archives.gov/omb/memoranda/fy2008/m08-05.pdf).

Em Novembro de 2007, o OMB estabelecido o programa de classificação TIC para melhorar a segurança de perímetro de rede federal e funções de resposta a incidentes. Classificação TIC foi concebido para executar a análise de rede de todo o tráfego de entrada e saída gov para identificar assinaturas específicas e com base no padrão de dados. Classificação TIC detectam anomalias comportamentais, como a atividade do botnet. Agências são impostas para consolidar suas conexões de rede externa e encaminhar todo o tráfego através de dispositivos de deteção e prevenção de intrusões conhecidos como EINSTEIN. Os dispositivos são hospedados num número limitado de pontos finais de rede, que são denominados _fidedigno ligações à internet_.

O objetivo da classificação TIC destina-se a agências saber:
- Quem é na minha rede (autorizado ou não autorizado)?
- Quando a minha rede é acedido e por quê?
- Quais recursos são acessados?

Todas as ligações externas de agência atualmente devem encaminhar através de uma classificação TIC OMB aprovado. Agências federais são necessárias para participar no programa de classificação TIC como um provedor de acesso de classificação TIC (TICAP) ou por contratar os serviços com um dos fornecedores de serviços de internet principais camada 1. Esses provedores são referidos como fornecedores de geridos fidedigno Internet Protocol Service (MTIPS). Classificação TIC inclui capacidades fundamentais obrigatórias, que são executadas pela agências e fornecedor MTIPS. Na versão atual do TIC, a detecção de intrusão EINSTEIN versão 2 e a versão de EINSTEIN 3 acelerada (3A), dispositivos de prevenção de intrusões são implementados em cada TICAP e MTIPS. A Agência estabelece um "Memorandum de conhecimento" com o departamento de Homeland Security (DHS) para implementar recursos EINSTEIN em sistemas federais.

Como parte das suas responsabilidades na proteção da rede. gov, DHS requer os feeds de dados não processados de dados de fluxo net Agência correlacionar incidentes em toda a empresa federal e efetuar análises, utilizando ferramentas especializadas. Routers DHS fornecem a capacidade de recolher o tráfego de rede IP como ele entra ou sai uma interface. Os administradores de rede podem analisar os dados de fluxo de rede para determinar a origem e destino do tráfego, a classe de serviço e assim por diante. Dados de fluxo de rede são considerados como estando "dados não-content" semelhante para o cabeçalho IP de origem, IP de destino e assim por diante. Dados de conteúdo não permite DHS saber mais sobre o conteúdo: que estava fazendo o que e como há muito tempo.

A iniciativa também inclui as políticas de segurança, diretrizes e estruturas que partem do princípio de infraestrutura no local. Como mover organismos públicos para a cloud para alcançar poupanças de custos, a eficiência operacional e a inovação, os requisitos de implementação de classificação TIC podem abrandar o tráfego de rede. A velocidade e agilidade com o qual government, os usuários podem acessar seus dados com base na cloud é limitada como resultado.

## <a name="azure-networking-options"></a>Opções de redes do Azure

Existem três opções principais para ligar aos serviços do Azure:

- Ligação à internet direta: Ligar aos serviços do Azure diretamente através de uma conexão de internet aberta. A média e a ligação são públicos. Aplicação e a encriptação de nível de transporte são confiadas para garantir a privacidade. Largura de banda é limitada pela conectividade de um site com a internet. Utilize mais de um fornecedor de Active Directory para garantir a resiliência.
- Rede privada virtual (VPN): Ligar à sua rede virtual do Azure em privado com um gateway de VPN.
A média é pública, porque ele percorre a ligação de internet padrão de um site, mas a ligação é encriptada num túnel para garantir a privacidade. Largura de banda é limitada consoante os dispositivos VPN e da configuração escolhida. Ligações de ponto a site do Azure são, normalmente, limitadas para 100 Mbps e ligações site a site estão limitadas a 1.25 Gbps.
- O Azure ExpressRoute: O ExpressRoute é uma ligação direta aos serviços da Microsoft. Como a conectividade é através de um canal de fibra isolado, a ligação pode ser público ou privado, dependendo da configuração que é utilizada. A largura de banda é normalmente limitada a um máximo de 10 Gbps.

Existem várias formas para cumprir os requisitos de classificação TIC apêndice H (considerações sobre a Cloud), conforme especificado no departamento de segurança interna do, "Documento de arquitetura de referência de ligações (TIC) Internet fidedigna, versão 2.0." Neste artigo, orientações de DHS TIC é referida como **TIC 2.0**.

Para ativar a ligação entre o **(D/A) de agência ou departamento** para o Azure ou Office 365, sem encaminhamento de tráfego através de D/A TIC, D/R tem de utilizar um túnel encriptado ou uma ligação dedicada para o fornecedor de serviços cloud (CSP). Os serviços CSP podem garantir a conectividade com os ativos de D/A cloud não é oferecida para a internet pública para acesso de pessoal de agência direto.

Office 365 está em conformidade com classificação TIC 2.0 apêndice H ao utilizar o ExpressRoute com [Peering da Microsoft](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings) ativada ou uma ligação à internet, que criptografa todo o tráfego através da utilização de TLS 1.2. Os utilizadores finais de D/R na rede D/A, pode ligar-se por meio de sua rede de agência e a infraestrutura de classificação TIC através da internet. Todos os acessos de internet remoto para o Office 365 é bloqueado e encaminha por meio da Agência. D/R também pode ligar ao Office 365 através de uma ligação do ExpressRoute com Peering da Microsoft (um tipo de peering público) ativada.  

Para o Azure, a segunda opção (VPN) e a terceira opção (ExpressRoute) podem satisfazer estes requisitos quando eles são usados em conjunto com os serviços que limitam o acesso à internet.

![Microsoft fidedignos ligação à internet (TIC)](media/tic-diagram-a.png)

## <a name="azure-infrastructure-as-a-service-offerings"></a>Infraestrutura como um ofertas de serviço do Azure

Conformidade com a política de classificação TIC através da utilização de IaaS do Azure é relativamente simples, porque os clientes do Azure gere os seus próprios encaminhamento da rede virtual.

O principal requisito para o ajudar a garantir a conformidade com a arquitetura de referência do PEDI é garantir que a rede virtual é uma extensão privada da rede D/A. Para ser um _privada_ extensão, a política requer tráfego não sai da sua rede, exceto no local através de ligação de rede de classificação TIC. Este processo é conhecido como _imposição de túnel_. Para a conformidade de classificação TIC, o processo encaminha todo o tráfego de qualquer sistema no ambiente do CSP através de um gateway no local na rede de uma organização saída à internet através da classificação TIC.

Conformidade de classificação TIC de IaaS do Azure é dividida em dois passos principais:

- Passo 1: Configuração.
- Passo 2: A auditoria.

### <a name="azure-iaas-tic-compliance-configuration"></a>Conformidade de classificação TIC de IaaS do Azure: Configuração

Para configurar uma arquitetura TIC compatíveis com o Azure, tem de impedir primeiro acesso direto à internet à sua rede virtual e, em seguida, forçar o tráfego de internet através da rede no local.

#### <a name="prevent-direct-internet-access"></a>Impedir o acesso direto à internet

Redes de IaaS do Azure é realizada por meio de redes virtuais que são compostas por sub-redes para os quais os controladores de interface de rede (NICs) de máquinas virtuais estão associados.

O cenário mais simples para suportar a conformidade de classificação TIC é garantir que uma máquina virtual ou uma coleção de máquinas virtuais, não é possível ligar a quaisquer recursos externos. Garantir a desativação de redes externas através de grupos de segurança de rede (NSGs). Utilize NSGs para controlar o tráfego para um ou mais NICs ou sub-redes na rede virtual. Um NSG contém as regras de controlo de acesso que permitem ou negam tráfego com base na direção, protocolo, endereço e porta de origem e endereço e porta de destino do tráfego. Pode alterar as regras de um NSG a qualquer momento e as alterações são aplicadas a todas as instâncias associadas. Para saber mais sobre como criar um NSG, consulte [filtrar o tráfego de rede com um grupo de segurança de rede](https://docs.microsoft.com/azure/virtual-network/virtual-networks-create-nsg-arm-pportal).

#### <a name="force-internet-traffic-through-an-on-premises-network"></a>Forçar o tráfego de internet através de uma rede no local

O Azure cria automaticamente as rotas do sistema e atribui-as a cada sub-rede numa rede virtual. Não é possível criar ou remover as rotas do sistema, mas pode substituir algumas por rotas personalizadas. O Azure cria rotas de sistema para cada sub-rede de predefinição. O Azure adiciona rotas predefinidas opcionais a sub-redes específicas ou todas as sub-redes, quando utiliza capacidades do Azure específicas. Este tipo de encaminhamento garante:
- O tráfego que é destinado dentro da rede virtual permanece na rede virtual.
- Espaços de endereço privado designado pelo IANA como 10.0.0.0/8 são ignorados, a menos que eles estão incluídos no espaço de endereços de rede virtual.
- "Último recurso" encaminhamento de 0.0.0.0/0 para o ponto final de internet de rede virtual.

![Classificação TIC imposição de túnel](media/tic-diagram-c.png)

Todo o tráfego que sai da rede virtual tem de encaminhar através da ligação no local, para garantir que todo o tráfego atravessa o TIC D/A. Pode criar rotas personalizadas mediante a criação de rotas definidas pelo utilizador ou por meio da troca de rotas de protocolo BGP (Border Gateway) entre o gateway de rede no local e um gateway de VPN do Azure. Para obter mais informações sobre as rotas definidas pelo utilizador, consulte [encaminhamento de tráfego de rede Virtual: Rotas definidas pelo utilizador](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#user-defined). Para obter mais informações sobre o BGP, consulte [encaminhamento de tráfego de rede Virtual: Border Gateway Protocol](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#border-gateway-protocol).

#### <a name="add-user-defined-routes"></a>Adicionar rotas definidas pelo utilizador

Se utilizar um gateway de rede virtual baseado na rota, pode forçar o túnel no Azure. Adicione uma rota definida pelo utilizador (UDR) que define o tráfego de 0.0.0.0/0 para encaminhar para um **próximo salto** do seu gateway de rede virtual. Azure dá prioridade rotas definidas pelo utilizador através de rotas definidas pelo sistema. Todo o tráfego de rede não virtuais é enviado para o gateway de rede virtual, que, em seguida, pode encaminhar o tráfego para o local. Depois de definir o UDR, associe a rota com sub-redes existentes ou novas sub-redes dentro de todas as redes virtuais no seu ambiente do Azure.

![rotas definidas pelo utilizador e classificação TIC](media/tic-diagram-d.png)

#### <a name="use-the-border-gateway-protocol"></a>Utilizar o protocolo BGP

Se utilizar o ExpressRoute ou de um gateway de rede virtual preparada para BGP, o BGP é o mecanismo preferencial para o anúncio de rotas. Para uma rota anunciada BGP de 0.0.0.0/0, gateways de rede virtual com reconhecimento de BGP de ExpressRoute e certifique-se que a rota predefinida se aplica a todas as sub-redes em suas redes virtuais.

### <a name="azure-iaas-tic-compliance-auditing"></a>Conformidade de classificação TIC de IaaS do Azure: Auditoria

O Azure oferece várias formas de auditar a conformidade de classificação TIC.

#### <a name="view-effective-routes"></a>Ver as rotas efetivas

Confirme que a rota predefinida será propagada ao observar a _rotas efetivas_ para uma máquina virtual específica, uma NIC específica ou uma tabela de rotas definidas pelo utilizador na [portal do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-routes-troubleshoot-portal#diagnose-using-azure-portal) ou no [ O Azure PowerShell](https://docs.microsoft.com/azure/virtual-network/virtual-network-routes-troubleshoot-powershell#diagnose-using-powershell). O **rotas efetivas** mostrar as rotas definidas pelo utilizador relevantes, BGP anunciados rotas e rotas de sistema que se aplicam à entidade relevante, conforme mostrado na figura a seguir:

![Rotas efetivas](media/tic-screen-1.png)

> [!NOTE]
> Não é possível ver as rotas efetivas para uma NIC, a menos que a NIC está associada uma máquina virtual em execução.

#### <a name="use-azure-network-watcher"></a>Utilizar o observador de rede do Azure

O observador de rede do Azure oferece várias ferramentas para auditar a conformidade de classificação TIC. Para obter mais informações, consulte [esta descrição geral sobre o observador de rede](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview).

##### <a name="capture-network-security-group-flow-logs"></a>Capture registos de fluxo de grupo de segurança de rede 

Utilize o observador de rede para capturar os registos de fluxo de rede que indicam os metadados que envolve o tráfego IP. Os registos de fluxo de rede contêm os endereços de origem e de destino de destinos e outros dados. Pode usar esses dados com os registos do gateway de rede virtual, no local de borda dispositivos ou TIC, para monitorizar o que todo o tráfego de rotas no local. 

## <a name="azure-platform-as-a-service-offerings"></a>Plataforma do Azure como ofertas de serviço

Serviços de PaaS do Azure, como o armazenamento do Azure, estão acessíveis através de um URL acessível pela interne. Qualquer pessoa com credenciais aprovadas pode aceder ao recurso, como uma conta de armazenamento, de qualquer local sem atravessa um TIC. Por esse motivo, muitos clientes do Governo concluir incorretamente que os serviços PaaS do Azure não estão em conformidade com as políticas de classificação TIC. Muitos serviços de PaaS do Azure podem estar em conformidade com a política de classificação TIC. Um serviço está em conformidade quando a arquitetura é o mesmo que o ambiente de IaaS em conformidade com a classificação TIC ([conforme descrito anteriormente](https://docs.microsoft.com/azure/security/compliance/compliance-tic#azure-infrastructure-as-a-service-offerings)) e o serviço está ligado a uma rede virtual do Azure.

Quando os serviços PaaS do Azure estão integrados uma rede virtual, o serviço é particularmente acessível a partir dessa rede virtual. Pode aplicar o encaminhamento personalizado para 0.0.0.0/0 através de rotas definidas pelo utilizador ou do BGP. Encaminhamento personalizado garante que todo o tráfego vinculado à internet encaminha no local para atravessar o TIC. Integre os serviços do Azure em redes virtuais, utilizando os seguintes padrões:

- **Implementar uma instância dedicada de um serviço**: Um número crescente de serviços de PaaS é implementável como instâncias dedicadas com pontos finais ligados à rede virtual. Pode implementar um ambiente de serviço de aplicações do PowerApps no modo de "Isolated" para permitir que o ponto final de rede restrito a uma rede virtual. O ambiente de serviço de aplicações, em seguida, pode alojar vários serviços de PaaS do Azure, como aplicações Web do Azure, gestão de API do Azure e as funções do Azure.
- **Utilizar pontos finais de serviço de rede virtual**: Um número crescente de serviços de PaaS permitem que a opção de mover o ponto de extremidade para um IP privado da rede virtual em vez de um endereço público.

Serviços que suportam a implementação de instâncias dedicadas numa rede virtual ou o uso de pontos finais de serviço, a partir de Maio de 2018, são apresentados nas tabelas seguintes.

> [!Note]
> O estado de disponibilidade corresponde aos serviços do Azure que estão disponíveis no mercado. O estado de disponibilidade para serviços do Azure no Azure Government está pendente.

### <a name="support-for-service-endpoints"></a>Suporte para pontos finais de serviço

|Serviço                        |Disponibilidade      |
|-------------------------------|------------------|
|Azure Key Vault                | Pré-visualização privada  |
|Azure Cosmos DB                | Pré-visualização privada  |
|Serviços de identidade              | Pré-visualização privada  |
|Azure Data Lake                | Pré-visualização privada  |
|Base de Dados do Azure para PostgreSQL  | Pré-visualização privada  |
|Base de Dados do Azure para MySQL       | Pré-visualização privada  |
|Azure SQL Data Warehouse       | Pré-visualização pública   |
|Base de Dados SQL do Azure             | Disponibilidade geral (GA) |
|Storage do Azure                  | GA               |

### <a name="support-for-virtual-network-injection"></a>Suporte para a injeção de rede virtual

|Serviço                               |Disponibilidade      |
|--------------------------------------|------------------|
|Instância Gerida da Base de Dados SQL do Azure   | Pré-visualização pública   |
|Serviço de Kubernetes do Azure (AKS)        | Pré-visualização pública   |
|Azure Service Fabric                  | GA               |
|API Management do Azure                  | GA               |
|Azure Active Directory                | GA               |
|Azure Batch                           | GA               |
|Ambiente do Serviço de Aplicações               | GA               |
|Cache do Azure para Redis                     | GA               |
|Azure HDInsight                       | GA               |
|Conjuntos de dimensionamento de máquinas virtuais             | GA               |
|Cloud Services do Azure                  | GA               |


### <a name="virtual-network-integration-details"></a>Detalhes de integração da rede virtual

O diagrama seguinte mostra o fluxo geral de rede para o acesso aos serviços PaaS. Acesso é apresentado da injeção de rede virtual e de túnel do serviço de rede virtual. Para obter mais informações sobre gateways do serviço de rede, redes virtuais e as etiquetas de serviço, consulte [rede e os grupos de segurança de aplicação: Etiquetas de serviço](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

![Opções de conectividade de PaaS para classificação TIC](media/tic-diagram-e.png)

1. Uma ligação privada é feita para o Azure com o ExpressRoute. Peering privado do ExpressRoute com túnel forçado é usado para forçar todo tráfego de rede de virtual de outros clientes através do ExpressRoute e de volta para o local. Peering da Microsoft não é necessária.
2. Gateway de VPN do Azure, quando utilizado em conjunto com o ExpressRoute e o Peering da Microsoft, pode sobrepor a encriptação de IPSec de ponto a ponto entre a rede virtual do cliente e o limite de no local. 
3. Conectividade de rede para a rede virtual do cliente é controlada através dos NSGs que permitem que os clientes permitir/recusar baseado em IP, porta e protocolo.
4. Expande a rede virtual do cliente para o serviço de PaaS através da criação de um ponto final de serviço para o serviço do cliente.
5. O ponto de final de serviço de PaaS é protegido para **predefinição recusar tudo** e para permitir apenas o acesso a partir de sub-redes especificados dentro da rede virtual do cliente. A predefinição nega também inclui ligações que têm origem na internet.
6. Outros serviços do Azure que precisam de aceder a recursos dentro da rede virtual do cliente devem ser:  
   - Implementados diretamente para a rede virtual.
   - Seletivamente permitida, com base nas orientações do respetivo serviço do Azure.

#### <a name="option-a-deploy-a-dedicated-instance-of-a-service-virtual-network-injection"></a>Opção a: Implementar uma instância dedicada de um serviço (injeção de rede virtual)

Injeção de rede virtual permite aos clientes implementar seletivamente instâncias dedicadas de um determinado serviço do Azure, como o HDInsight, em sua própria rede virtual. Instâncias de serviço são implementadas numa sub-rede dedicada na rede virtual de um cliente. Injeção de rede virtual permite o acesso aos recursos de serviço por meio dos endereços de encaminháveis não internet. Instâncias locais utilizam o ExpressRoute ou uma VPN de site a site para aceder diretamente a instâncias de serviço por meio do espaço de endereços de rede virtual, em vez de abrir uma firewall para o espaço de endereços de internet pública. Quando uma instância dedicada é anexada a um ponto final, pode usar as mesmas estratégias quanto à conformidade de IaaS PEDI. Encaminhamento predefinido garante que o tráfego da internet será redirecionado para um gateway de rede virtual que está vinculado a no local. Ainda mais pode controlar o acesso de entrada e saído através de NSGs para determinada sub-rede.

![Descrição geral de injeção de rede virtual](media/tic-diagram-f.png)

#### <a name="option-b-use-virtual-network-service-endpoints-service-tunnel"></a>Opção b: Utilizar pontos finais do serviço de rede virtual (túnel do serviço)

Um número crescente de serviços de multi-inquilinos do Azure oferece "pontos finais de serviço." Pontos finais de serviço são um método alternativo para a integração para redes virtuais do Azure. Pontos finais de serviço de rede virtual expandem o seu espaço de endereços IP de rede virtual e da identidade da rede virtual para o serviço através de uma ligação direta. Tráfego de rede virtual para o serviço do Azure permanece sempre na rede backbone do Azure. 

Depois de ativar um ponto final de serviço para um serviço, utilize políticas expostas pelo serviço para restringir ligações para o serviço para essa rede virtual. Verificações de acesso são impostas na plataforma pelo serviço do Azure. Acesso a um recurso de bloqueado é concedido apenas se o pedido tem origem da rede virtual permitido ou sub-rede, ou nos dois IPs que são utilizados para identificar o tráfego no local, se utilizar o ExpressRoute. Utilize este método para de fato impedem que o tráfego de entrada/saída de sair diretamente o serviço de PaaS.

![Descrição geral de pontos finais de serviço](media/tic-diagram-g.png)

## <a name="cloud-native-tools-for-network-situational-awareness"></a>Ferramentas nativas da cloud para situacional de rede

O Azure disponibiliza ferramentas nativas da cloud para ajudar a garantir que tem a percepção situacional necessárias para compreender os fluxos de tráfego da sua rede. As ferramentas não são necessárias para conformidade com a política de classificação TIC. As ferramentas podem melhorar bastante as capacidades de segurança.

### <a name="azure-policy"></a>Azure Policy

[O Azure Policy](../../governance/policy/overview.md) é um serviço do Azure que fornece a sua organização com melhor capacidade de auditoria e impor as iniciativas de conformidade. Os clientes podem planear e testar as suas regras de política do Azure agora para garantir a conformidade de classificação TIC futura.

Política do Azure destina-se ao nível da subscrição. O serviço fornece uma interface centralizada, onde pode realizar tarefas de conformidade, incluindo:
- Gerir iniciativas
- Configurar definições de política
- Auditar a conformidade
- Impor a conformidade
- Gerir exceções

Juntamente com muitas definições incorporadas, os administradores podem definir suas próprias definições personalizadas ao utilizar modelos JSON simples. A Microsoft recomenda a priorização de auditoria através de imposição, sempre que possível.

As políticas de exemplo seguintes podem ser utilizadas para cenários de conformidade de classificação TIC:

|Política  |Cenário de exemplo  |Modelo  |
|---------|---------|---------|
|Impor a tabela de rotas definidas pelo utilizador. | Certifique-se de que a rota padrão em todas as redes virtuais aponta para um gateway de rede virtual aprovados para o encaminhamento para o local.    | Introdução a isso [modelo](../../governance/policy/samples/no-user-defined-route-table.md). |
|Se o observador de rede não está ativado para uma região de auditoria.  | Certifique-se de que o observador de rede está ativado para todas as regiões de utilizada.  | Introdução a isso [modelo](../../governance/policy/samples/network-watcher-not-enabled.md). |
|NSG x em cada sub-rede.  | Certifique-se de que um NSG (ou um conjunto de NSGs aprovados) com o tráfego de internet bloqueado é aplicado a todas as sub-redes em cada rede virtual. | Introdução a isso [modelo](../../governance/policy/samples/nsg-on-subnet.md). |
|NSG x em cada NIC. | Certifique-se de que um NSG com tráfego de internet bloqueado é aplicado a todos os NICs em todas as máquinas virtuais. | Introdução a isso [modelo](../../governance/policy/samples/nsg-on-nic.md). |
|Utilize uma rede virtual aprovada para interfaces de rede de máquina virtual.  | Certifique-se de que todas as NICs estão numa rede virtual aprovada. | Introdução a isso [modelo](../../governance/policy/samples/use-approved-vnet-vm-nics.md). |
|Localizações permitidas. | Certifique-se de que todos os recursos são implementados em regiões com redes virtuais em conformidade e a configuração de observador de rede.  | Introdução a isso [modelo](../../governance/policy/samples/allowed-locations.md). |
|Não permitido tipos de recursos, como **PublicIPs**. | Proibir a implementação de tipos de recursos que não tenha um plano de conformidade. Utilize esta política para proibir a implementação de recursos de endereço IP público. Enquanto as regras do NSG podem ser utilizadas para bloquear o tráfego de internet de entrada com eficiência, evitar o uso de IPs públicos ainda mais reduz a superfície de ataque.   | Introdução a isso [modelo](../../governance/policy/samples/not-allowed-resource-types.md).  |

### <a name="network-watcher-traffic-analytics"></a>Análise de tráfego do observador de rede

Observador de rede [análise de tráfego](https://azure.microsoft.com/blog/traffic-analytics-in-preview/) consumir dados de registo de fluxo e outros registos para fornecer uma visão geral de tráfego de rede. Os dados são útil para auditoria de conformidade de classificação TIC e para identificar pontos problemáticos. Pode utilizar o dashboard de alto nível para o ecrã rapidamente as máquinas virtuais que estão a comunicar com a internet e obter uma lista de foco para o encaminhamento do PEDI.

![Análise de tráfego](media/tic-traffic-analytics-1.png)

Utilize o **mapa geográfico** a identificar rapidamente os destinos prováveis físicos do tráfego de internet das suas máquinas virtuais. Pode identificar e fazer a triagem localizações suspeitas ou alteração de padrões:

![Mapa geográfico](media/tic-traffic-analytics-2.png)

Utilize o **topologia de redes virtuais** para redes virtuais existentes de pesquisa rapidamente:

![Mapa de topologia de rede](media/tic-traffic-analytics-3.png)

### <a name="network-watcher-next-hop-tests"></a>Testes de salto seguintes do observador de rede

Redes em regiões que são monitorizados pelo observador de rede podem realizar testes de salto seguintes. No portal do Azure, pode introduzir uma origem e destino para um fluxo de rede de exemplo para o observador de rede resolver o destino de salto seguinte. Executar este teste em máquinas virtuais e endereços de internet de exemplo garantir que o destino de salto seguinte é o gateway de virtual de rede esperado.

![Testes de salto seguintes](media/tic-network-watcher.png)

## <a name="conclusions"></a>Conclusões

Pode configurar facilmente o acesso para o Microsoft Azure, Office 365 e Dynamics 365 ajudar a estar em conformidade com diretrizes de classificação TIC 2.0 apêndice H, como escrito e definidos Maio de 2018. A Microsoft reconhece que a documentação de orientação de classificação TIC está sujeitas a alterações. Microsoft empreendimentos ajudar os clientes a cumprir as diretrizes de forma atempada conforme novas diretrizes são lançadas.

## <a name="appendix-trusted-internet-connections-patterns-for-common-workloads"></a>Apêndice: Padrões de ligações de Internet fidedignas para cargas de trabalho comuns

| Category | Carga de trabalho | IaaS | Dedicado PaaS / injeção de rede Virtual  | Pontos finais de serviço  |
|---------|---------|---------|---------|--------|
| Computação | Máquinas virtuais do Linux do Azure | Sim | | |
| Computação | Máquinas virtuais do Windows Azure | Sim | | |
| Computação | Conjuntos de dimensionamento de máquinas virtuais | Sim | | |
| Computação | Funções do Azure | | Ambiente do Serviço de Aplicações | |
| Web e dispositivos móveis | Aplicação web interna | | Ambiente do Serviço de Aplicações| |
| Web e dispositivos móveis | Aplicação móvel interna | | Ambiente do Serviço de Aplicações | |
| Web e dispositivos móveis | Aplicações de API | | Ambiente do Serviço de Aplicações | |
| Contentores | Azure Container Service | | | Sim |
| Contentores | Serviço Kubernetes do Azure (AKS) \* | | | Sim |
| Base de Dados | Base de Dados SQL do Azure | | Instância gerida da base de dados SQL do Azure \* | SQL do Azure |
| Base de Dados | Base de Dados do Azure para MySQL | | | Sim |
| Base de Dados | Base de Dados do Azure para PostgreSQL | | | Sim |
| Base de Dados | Azure SQL Data Warehouse | | | Sim |
| Base de Dados | Azure Cosmos DB | | | Sim |
| Base de Dados | Cache do Azure para Redis | | Sim | |
| Armazenamento | Armazenamento de Blobs do Azure | Sim | | |
| Armazenamento | Ficheiros do Azure | Sim | | |
| Armazenamento | Armazenamento de filas do Azure | Sim | | |
| Armazenamento | Armazenamento de Tabelas do Azure | Sim | | |
| Armazenamento | Armazenamento de disco do Azure | Sim | | |

\* Pré-visualização pública no Azure Government, Maio de 2018.
