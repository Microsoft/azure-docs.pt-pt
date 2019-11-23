---
title: Proteção contra DDoS do Azure-criando soluções resilientes | Microsoft Docs
description: Saiba mais sobre como você pode usar os dados de log para obter informações aprofundadas sobre seu aplicativo.
services: security
author: barclayn
manager: RKarlin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/18/2018
ms.author: barclayn
ms.openlocfilehash: ac36a4c59dbec8bf27850de1565e86b78643148a
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2019
ms.locfileid: "72595428"
---
# <a name="azure-ddos-protection---designing-resilient-solutions"></a>Proteção contra DDoS do Azure-criando soluções resilientes

Este artigo destina-se aos tomadores de decisões de ti e à equipe de segurança. Espera-se que você esteja familiarizado com o Azure, a rede e a segurança.
DDoS é um tipo de ataque que tenta esgotar os recursos do aplicativo. O objetivo é afetar a disponibilidade do aplicativo e sua capacidade de lidar com solicitações legítimas. Os ataques estão se tornando mais sofisticados e maiores em tamanho e impacto. Os ataques de DDoS podem ser direcionadas para qualquer ponto final que esteja publicamente acessível através da internet. A criação de resiliência de DDoS (negação de serviço distribuído) requer planejamento e design para uma variedade de modos de falha. O Azure fornece proteção contínua contra ataques de DDoS. Essa proteção é integrada à plataforma do Azure por padrão e sem nenhum custo adicional.

Além da proteção contra DDoS principal na plataforma, a [proteção contra DDoS do Azure Standard](https://azure.microsoft.com/services/ddos-protection/) oferece recursos avançados de mitigação de DDoS contra ataques à rede. Ele é ajustado automaticamente para proteger seus recursos específicos do Azure. A proteção é simples de habilitar durante a criação de novas redes virtuais. Ele também pode ser feito após a criação e não requer nenhuma alteração de aplicativo ou de recurso.

![A função da proteção contra DDoS do Azure na proteção de clientes e uma rede virtual de um invasor](./media/ddos-best-practices/image1.png)


## <a name="fundamental-best-practices"></a>Práticas recomendadas fundamentais

As seções a seguir fornecem orientação prescritiva para criar serviços resistentes a DDoS no Azure.

### <a name="design-for-security"></a>Conceber para segurança

Garanta que a segurança seja uma prioridade em todo o ciclo de vida de um aplicativo, desde o design e a implementação até a implantação e as operações. Os aplicativos podem ter bugs que permitem um volume relativamente baixo de solicitações para usar uma quantidade inordenada de recursos, resultando em uma interrupção do serviço. 

Para ajudar a proteger um serviço em execução no Microsoft Azure, você deve ter uma boa compreensão da arquitetura do aplicativo e concentrar-se nos [cinco pilares da qualidade do software](/azure/architecture/guide/pillars).
Você deve conhecer os volumes de tráfego típicos, o modelo de conectividade entre o aplicativo e outros aplicativos e os pontos de extremidade de serviço expostos à Internet pública.

Garantir que um aplicativo seja resiliente o suficiente para lidar com uma negação de serviço direcionada ao próprio aplicativo é o mais importante. A segurança e a privacidade são criadas na plataforma Azure, começando com o [Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl/default.aspx). O SDL aborda a segurança em cada fase de desenvolvimento e garante que o Azure seja atualizado continuamente para torná-lo ainda mais seguro.

### <a name="design-for-scalability"></a>Design para escalabilidade

A escalabilidade é o quão bem um sistema pode lidar com o aumento da carga. Projete seus aplicativos para [dimensionar horizontalmente](/azure/architecture/guide/design-principles/scale-out) para atender à demanda de uma carga amplificada, especificamente no caso de um ataque de DDoS. Se seu aplicativo depende de uma única instância de um serviço, ele cria um ponto único de falha. O provisionamento de várias instâncias torna seu sistema mais resiliente e mais escalonável.

Para [Azure app serviço](/azure/app-service/app-service-value-prop-what-is), selecione um [plano do serviço de aplicativo](/azure/app-service/overview-hosting-plans) que ofereça várias instâncias. Para os serviços de nuvem do Azure, configure cada uma de suas funções para usar [várias instâncias](/azure/cloud-services/cloud-services-choose-me). Para [máquinas virtuais do Azure](/azure/virtual-machines/virtual-machines-windows-about/?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), verifique se sua arquitetura de VM (máquina virtual) inclui mais de uma VM e se cada VM está incluída em um [conjunto de disponibilidade](/azure/virtual-machines/virtual-machines-windows-manage-availability). É recomendável usar [conjuntos de dimensionamento de máquinas virtuais](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview) para recursos de dimensionamento automático.

### <a name="defense-in-depth"></a>Defesa profunda

A ideia por trás da defesa profunda é gerenciar riscos usando estratégias de defesa diversas. A disposição em camadas de defesas de segurança em um aplicativo reduz a chance de um ataque bem-sucedido. Recomendamos que você implemente designs seguros para seus aplicativos usando os recursos internos da plataforma Azure.

Por exemplo, o risco de ataque aumenta com o tamanho (*área da superfície*) do aplicativo. Você pode reduzir a área da superfície usando a lista de permissões para fechar o espaço de endereço IP exposto e portas de escuta que não são necessárias nos balanceadores de carga ([Azure Load Balancer](/azure/load-balancer/load-balancer-get-started-internet-portal) e [aplicativo Azure gateway](/azure/application-gateway/application-gateway-create-probe-portal)). Os [NSGs (grupos de segurança de rede)](/azure/virtual-network/security-overview) são outra maneira de reduzir a superfície de ataque.
Você pode usar [marcas de serviço](/azure/virtual-network/security-overview#service-tags) e [grupos de segurança de aplicativo](/azure/virtual-network/security-overview#application-security-groups) para minimizar a complexidade para criar regras de segurança e configurar a segurança de rede, como uma extensão natural da estrutura de um aplicativo.

Você deve implantar os serviços do Azure em uma [rede virtual](/azure/virtual-network/virtual-networks-overview) sempre que possível. Essa prática permite que os recursos de serviço se comuniquem por meio de endereços IP privados. O tráfego de serviço do Azure de uma rede virtual usa endereços IP públicos como endereços IP de origem por padrão. O uso de [pontos de extremidade de serviço](/azure/virtual-network/virtual-network-service-endpoints-overview) alternará o tráfego de serviço para usar endereços privados de rede virtual como os endereços IP de origem quando eles estiverem acessando o serviço do Azure de uma rede virtual.

Frequentemente vemos os recursos locais dos clientes sendo atacados junto com seus recursos no Azure. Se você estiver conectando um ambiente local ao Azure, recomendamos que você minimize a exposição de recursos locais para a Internet pública. Você pode usar os recursos de escala e proteção contra DDoS avançada do Azure implantando suas entidades públicas conhecidas no Azure. Como essas entidades publicamente acessíveis são geralmente um alvo para ataques de DDoS, colocá-las no Azure reduz o impacto em seus recursos locais.

## <a name="azure-offerings-for-ddos-protection"></a>Ofertas do Azure para proteção contra DDoS

O Azure tem duas ofertas de serviço de DDoS que fornecem proteção contra ataques de rede (camada 3 e 4): proteção contra DDoS básica e proteção contra DDoS Standard. 

### <a name="ddos-protection-basic"></a>Proteção contra DDoS básica

A proteção básica é integrada ao Azure por padrão sem custo adicional. A escala e a capacidade da rede do Azure implantada globalmente fornecem defesa contra ataques comuns de camada de rede por meio do monitoramento de tráfego AlwaysOn e mitigação em tempo real. A proteção contra DDoS básica não requer nenhuma configuração de usuário ou alteração de aplicativo. A proteção contra DDoS básica ajuda a proteger todos os serviços do Azure, incluindo serviços PaaS, como o DNS do Azure.

![Mapeie a representação da rede do Azure, com o texto "presença de mitigação de DDoS global" e "capacidade de mitigação de DDoS líder"](./media/ddos-best-practices/image3.png)

A proteção básica contra DDoS no Azure consiste em componentes de software e hardware. Um plano de controle de software decide quando, onde e que tipo de tráfego deve ser direcionado por dispositivos de hardware que analisam e removem o tráfego de ataque. O plano de controle toma essa decisão com base em uma *política*de proteção contra DDoS de toda a infraestrutura. Essa política é definida estaticamente e universalmente aplicada a todos os clientes do Azure.

Por exemplo, a política de proteção contra DDoS especifica em qual volume de tráfego a proteção deve ser *disparada.* (Ou seja, o tráfego do locatário deve ser roteado por meio de dispositivos de depuração.) Em seguida, a política especifica como os dispositivos de depuração devem *mitigar* o ataque.

O serviço de proteção contra DDoS do Azure básico destina-se à proteção da infraestrutura e à proteção da plataforma Azure. Ele reduz o tráfego quando ele excede uma taxa tão significativa que pode afetar vários clientes em um ambiente multilocatário. Ele não fornece alertas ou políticas personalizadas por cliente.

### <a name="ddos-protection-standard"></a>Proteção contra DDoS Standard

A proteção padrão fornece recursos aprimorados de mitigação de DDoS. Ele é ajustado automaticamente para ajudar a proteger seus recursos específicos do Azure em uma rede virtual. A proteção é simples de habilitar em qualquer rede virtual nova ou existente e não requer nenhuma alteração de aplicativo ou recurso. Ele tem várias vantagens em relação ao serviço básico, incluindo registro em log, alertas e telemetria. As seções a seguir descrevem os principais recursos do serviço de proteção contra DDoS do Azure Standard.

#### <a name="adaptive-real-time-tuning"></a>Ajuste de tempo real adaptável

O serviço de proteção contra DDoS do Azure básico ajuda a proteger os clientes e a evitar impactos em outros clientes. Por exemplo, se um serviço for provisionado para um volume típico de tráfego de entrada legítimo que seja menor do que a *taxa de gatilho* da política de proteção contra DDoS de toda a infraestrutura, um ataque de DDoS nos recursos desse cliente poderá ser despercebido. Em geral, a complexidade dos ataques recentes (por exemplo, DDoS de vários vetores) e os comportamentos específicos do aplicativo de locatários são chamados para políticas de proteção personalizadas por cliente. O serviço realiza essa personalização usando duas informações:

- Aprendizado automático de padrões de tráfego por cliente (por IP) para a camada 3 e 4.

- Minimizando falsos positivos, Considerando que a escala do Azure permite que ele absorve uma quantidade significativa de tráfego.

![Diagrama de como funciona a proteção contra DDoS Standard, com o "geração de política" circulado](./media/ddos-best-practices/image5.png)

#### <a name="ddos-protection-telemetry-monitoring-and-alerting"></a>Telemetria, monitoramento e alertas de proteção contra DDoS

A proteção contra DDoS Standard expõe telemetria avançada por meio de [Azure monitor](/azure/azure-monitor/overview) durante um ataque de DDoS. Você pode configurar alertas para qualquer uma das métricas de Azure Monitor que a proteção contra DDoS usa. Você pode integrar o registro em log com Splunk (hubs de eventos do Azure), logs de Azure Monitor e armazenamento do Azure para análise avançada por meio da interface de diagnóstico de Azure Monitor.

##### <a name="ddos-mitigation-policies"></a>Políticas de mitigação de DDoS

Na portal do Azure, selecione **monitorar** **métricas**de > . No painel **métricas** , selecione o grupo de recursos, selecione um tipo de recurso de **endereço IP público**e selecione seu endereço IP público do Azure. As métricas de DDoS estão visíveis no painel de **métricas disponíveis** .

A proteção contra DDoS Standard aplica três políticas de mitigação autoajustadas (TCP SYN, TCP e UDP) para cada IP público do recurso protegido, na rede virtual com DDoS habilitado. Você pode exibir os limites da política selecionando os pacotes de entrada da métrica **para disparar a mitigação de DDoS**.

![Gráfico de métricas e métricas disponíveis](./media/ddos-best-practices/image7.png)

Os limites de política são configurados autoconfigurado por meio da criação de perfil de tráfego de rede baseada no Machine Learning. A mitigação de DDoS ocorre para um endereço IP em ataque somente quando o limite da política é excedido.

##### <a name="metric-for-an-ip-address-under-ddos-attack"></a>Métrica para um endereço IP sob ataque de DDoS

Se o endereço IP público estiver sob ataque, o valor da métrica **sob ataque de DDoS ou não** será alterado para 1 à medida que a proteção contra DDoS executar a mitigação no tráfego de ataque.

!["Com ataque de DDoS ou não" métrica e gráfico](./media/ddos-best-practices/image8.png)

É recomendável configurar um alerta nessa métrica. Você será notificado quando houver uma mitigação de DDoS ativa executada em seu endereço IP público.

Para obter mais informações, consulte [gerenciar a proteção contra DDoS do Azure Standard usando o portal do Azure](/azure/virtual-network/ddos-protection-manage-portal).

#### <a name="web-application-firewall-for-resource-attacks"></a>Firewall do aplicativo Web para ataques de recursos

Especificamente para ataques de recursos na camada de aplicativo, você deve configurar um WAF (firewall do aplicativo Web) para ajudar a proteger aplicativos Web. Um WAF inspeciona o tráfego da Web de entrada para bloquear injeções de SQL, scripts entre sites, DDoS e outros ataques de camada 7. O Azure fornece o [WAF como um recurso do gateway de aplicativo](/azure/application-gateway/application-gateway-web-application-firewall-overview) para proteção centralizada de seus aplicativos Web contra explorações e vulnerabilidades comuns. Há outras ofertas de WAF disponíveis de parceiros do Azure que podem ser mais adequadas para suas necessidades por meio do [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?search=WAF&page=1).

Até firewalls de aplicativo Web são suscetíveis a ataques de esgotamento de volumétricos e de estado. É altamente recomendável habilitar a proteção contra DDoS Standard na rede virtual WAF para ajudar a proteger contra ataques de volumétricos e de protocolo. Para obter mais informações, consulte a seção [arquiteturas de referência de proteção contra DDoS](#ddos-protection-reference-architectures) .

### <a name="protection-planning"></a>Planejamento de proteção

Planejamento e preparação são cruciais para entender como um sistema será executado durante um ataque de DDoS. Criar um plano de resposta de gerenciamento de incidentes faz parte desse esforço.

Se você tiver a proteção contra DDoS Standard, verifique se ela está habilitada na rede virtual de pontos de extremidade voltados para a Internet. A configuração de alertas de DDoS ajuda você a observar constantemente qualquer ataque em potencial na sua infraestrutura. 

Monitore seus aplicativos de forma independente. Entenda o comportamento normal de um aplicativo. Prepare-se para agir se o aplicativo não estiver se comportando conforme o esperado durante um ataque de DDoS.

#### <a name="testing-through-simulations"></a>Teste por meio de simulações

É uma boa prática testar suas suposições sobre como seus serviços responderão a um ataque por meio da condução de simulações periódicas. Durante o teste, valide que seus serviços ou aplicativos continuam a funcionar conforme o esperado e não há nenhuma interrupção na experiência do usuário. Identifique lacunas do ponto de vista de tecnologia e de processo e incorpore-as na estratégia de resposta a DDoS. Recomendamos que você execute esses testes em ambientes de preparo ou fora do horário de pico para minimizar o impacto no ambiente de produção.

Fizemos uma parceria com o [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) para criar uma interface em que os clientes do Azure possam gerar tráfego em relação aos pontos de extremidade públicos habilitados para proteção contra DDoS para simulações. Você pode usar a simulação de [nuvem do BreakingPoint](https://www.ixiacom.com/products/breakingpoint-cloud) para:

- Valide como a proteção contra DDoS do Azure ajuda a proteger os recursos do Azure contra ataques de DDoS.

- Otimize seu processo de resposta a incidentes enquanto estiver sob ataque de DDoS.

- Documente a conformidade com DDoS.

- Treine suas equipes de segurança de rede.

O segurança cibernética requer inovação constante na defesa. A proteção padrão do DDoS do Azure é uma oferta de ponta com uma solução eficaz para atenuar ataques de DDoS cada vez mais complexos.

## <a name="components-of-a-ddos-response-strategy"></a>Componentes de uma estratégia de resposta a DDoS

Um ataque de DDoS que visa os recursos do Azure geralmente requer pouca intervenção do ponto de vista do usuário. Ainda assim, incorporar a mitigação de DDoS como parte de uma estratégia de resposta a incidentes ajuda a minimizar o impacto na continuidade dos negócios.

### <a name="microsoft-threat-intelligence"></a>Inteligência contra ameaças da Microsoft

A Microsoft tem uma ampla rede de inteligência contra ameaças. Essa rede usa o conhecimento coletivo de uma comunidade de segurança estendida que dá suporte ao Microsoft serviços online, a parceiros da Microsoft e a relações na Comunidade de segurança da Internet. 

Como um provedor de infraestrutura crítico, a Microsoft recebe avisos antecipados sobre ameaças. A Microsoft reúne a inteligência contra ameaças de sua serviços online e de sua base de clientes global. A Microsoft incorpora toda essa inteligência contra ameaças de volta aos produtos de proteção contra DDoS do Azure.

Além disso, a DCU (unidade de crimes digitais da Microsoft) executa estratégias ofensivas contra botnets. Botnets são uma fonte comum de comando e controle para ataques de DDoS.

### <a name="risk-evaluation-of-your-azure-resources"></a>Avaliação de risco dos recursos do Azure

É imperativo entender o escopo do risco de um ataque de DDoS continuamente. Pergunte-se periodicamente:

- Quais novos recursos do Azure disponíveis publicamente precisam de proteção?

- Há um único ponto de falha no serviço? 

- Como os serviços podem ser isolados para limitar o impacto de um ataque e ainda disponibilizar serviços para clientes válidos?

- Há redes virtuais em que a proteção contra DDoS Standard deve ser habilitada, mas não é? 

- Meus serviços são ativos/ativos com failover em várias regiões?

### <a name="customer-ddos-response-team"></a>Equipe de resposta a DDoS do cliente

A criação de uma equipe de resposta a DDoS é uma etapa fundamental para responder a um ataque de maneira rápida e eficaz. Identifique os contatos em sua organização que supervisionarão o planejamento e a execução. Essa equipe de resposta a DDoS deve compreender totalmente o serviço Standard de proteção contra DDoS do Azure. Certifique-se de que a equipe possa identificar e atenuar um ataque coordenando-se com clientes internos e externos, incluindo a equipe de suporte da Microsoft.

Para sua equipe de resposta a DDoS, recomendamos que você use os exercícios de simulação como uma parte normal da disponibilidade do seu serviço e do planejamento de continuidade. Esses exercícios devem incluir o teste de escala.

### <a name="alerts-during-an-attack"></a>Alertas durante um ataque

A proteção contra DDoS do Azure Standard identifica e atenua ataques de DDoS sem nenhuma intervenção do usuário. Para ser notificado quando houver uma mitigação ativa para um IP público protegido, você pode [configurar um alerta](/azure/virtual-network/ddos-protection-manage-portal) na métrica **sob ataque de DDoS ou não**. Você pode optar por criar alertas para as outras métricas de DDoS para entender a escala do ataque, o tráfego que está sendo descartado e outros detalhes.

#### <a name="when-to-contact-microsoft-support"></a>Quando entrar em contato com o suporte da Microsoft

- Durante um ataque de DDoS, você descobre que o desempenho do recurso protegido está degradado gravemente ou o recurso não está disponível.

- Você acredita que o serviço de proteção contra DDoS não está se comportando conforme o esperado. 

  O serviço de proteção contra DDoS iniciará a mitigação somente se a política de valor de métrica **para disparar a mitigação de DDoS (TCP/TCP SYN/UDP)** for menor do que o tráfego recebido no recurso de IP público protegido.

- Você está planejando um evento viral que aumentará significativamente o tráfego de rede.

- Um ator tem ameaçado para iniciar um ataque de DDoS contra seus recursos.

- Se você precisar permitir que o liste um IP ou intervalo IP do padrão de proteção contra DDoS do Azure. Um cenário comum é permitir o IP da lista se o tráfego for roteado de um WAF de nuvem externo para o Azure. 

Para ataques que têm um impacto crítico nos negócios, crie um tíquete de [suporte](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)de gravidade a.

### <a name="post-attack-steps"></a>Etapas após o ataque

É sempre uma boa estratégia fazer um postmortem após um ataque e ajustar a estratégia de resposta a DDoS, conforme necessário. Coisas a considerar:

- Houve alguma interrupção na experiência do serviço ou do usuário devido à falta de arquitetura escalonável?

- Quais aplicativos ou serviços mais sofreram mais?

- Qual a eficácia da estratégia de resposta a DDoS e como ela pode ser melhorada?

Se você suspeitar que está sob um ataque de DDoS, passe pelos seus canais de suporte normais do Azure.

## <a name="ddos-protection-reference-architectures"></a>Arquiteturas de referência de proteção contra DDoS

A proteção contra DDoS Standard é projetada [para serviços que são implantados em uma rede virtual](/azure/virtual-network/virtual-network-for-azure-services). Para outros serviços, o serviço básico de proteção contra DDoS padrão se aplica. As arquiteturas de referência a seguir são organizadas por cenários, com padrões de arquitetura agrupados em conjunto.

### <a name="virtual-machine-windowslinux-workloads"></a>Cargas de trabalho de máquina virtual (Windows/Linux)

#### <a name="application-running-on-load-balanced-vms"></a>Aplicativo em execução em VMs com balanceamento de carga

Essa arquitetura de referência mostra um conjunto de práticas comprovadas para executar várias VMs do Windows em um conjunto de dimensionamento por trás de um balanceador de carga, para melhorar a disponibilidade e a escalabilidade. Essa arquitetura pode ser usada para qualquer carga de trabalho sem estado, como um servidor Web.

![Diagrama da arquitetura de referência para um aplicativo em execução em VMs com balanceamento de carga](./media/ddos-best-practices/image9.png)

Nesta arquitetura, uma carga de trabalho é distribuída por várias instâncias de VM. Há um único endereço IP público, e o tráfego de Internet é distribuído para as VMs por meio de um balanceador de carga. A proteção contra DDoS Standard está habilitada na rede virtual do balanceador de carga do Azure (Internet) que tem o IP público associado a ela.

O balanceador de carga distribui as solicitações de entrada da Internet para as instâncias de VM. Os conjuntos de dimensionamento de máquinas virtuais permitem que o número de VMs seja expandido ou dimensionado manualmente ou automaticamente com base em regras predefinidas. Isso é importante se o recurso estiver sob ataque de DDoS. Para obter mais informações sobre essa arquitetura de referência, consulte [Este artigo](/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm).

#### <a name="application-running-on-windows-n-tier"></a>Aplicativo em execução no Windows de N camadas

Existem várias formas de implementar uma arquitetura de N camadas. O diagrama a seguir mostra um aplicativo Web típico de três camadas. Essa arquitetura baseia-se no artigo [executar VMs com balanceamento de carga para escalabilidade e disponibilidade](/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm). As camadas Web e Business utilizam VMs com balanceamento de carga.

![Diagrama da arquitetura de referência para um aplicativo em execução no Windows de N camadas](./media/ddos-best-practices/image10.png)

Nessa arquitetura, a proteção contra DDoS Standard está habilitada na rede virtual. Todos os IPs públicos na rede virtual obtêm proteção contra DDoS para a camada 3 e 4. Para proteção de camada 7, implante o gateway de aplicativo no SKU do WAF. Para obter mais informações sobre essa arquitetura de referência, consulte [Este artigo](/azure/architecture/reference-architectures/virtual-machines-windows/n-tier).

#### <a name="paas-web-application"></a>Aplicação web PaaS

Essa arquitetura de referência mostra a execução de um aplicativo de serviço Azure App em uma única região. Essa arquitetura mostra um conjunto de práticas comprovadas para um aplicativo Web que usa [Azure app de serviço](https://azure.microsoft.com/documentation/services/app-service/) e [banco de dados SQL do Azure](https://azure.microsoft.com/documentation/services/sql-database/).
Uma região em espera é configurada para cenários de failover.

![Diagrama da arquitetura de referência para um aplicativo Web de PaaS](./media/ddos-best-practices/image11.png)

O Gerenciador de tráfego do Azure roteia as solicitações de entrada para o gateway de aplicativo em uma das regiões. Durante as operações normais, ele roteia as solicitações para o gateway de aplicativo na região ativa. Se essa região ficar indisponível, o Gerenciador de tráfego fará failover para o gateway de aplicativo na região em espera.

Todo o tráfego da Internet destinado ao aplicativo Web é roteado para o [endereço IP público do gateway de aplicativo](/azure/application-gateway/application-gateway-web-app-overview) por meio do Gerenciador de tráfego. Nesse cenário, o serviço de aplicativo (aplicativo Web) propriamente dito não está voltado diretamente e é protegido pelo gateway de aplicativo. 

Recomendamos que você configure o SKU do WAF do gateway de aplicativo (modo de prevenção) para ajudar a proteger contra ataques de camada 7 (HTTP/HTTPS/WebSocket). Além disso, os aplicativos Web são configurados para [aceitar somente o tráfego do endereço IP do gateway de aplicativo](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/) .

Para obter mais informações sobre essa arquitetura de referência, consulte [Este artigo](/azure/architecture/reference-architectures/app-service-web-app/multi-region).

### <a name="mitigation-for-non-web-paas-services"></a>Mitigação para serviços de PaaS não Web

#### <a name="hdinsight-on-azure"></a>HDInsight no Azure

Essa arquitetura de referência mostra a configuração da proteção contra DDoS Standard para um [cluster do Azure HDInsight](/azure/hdinsight/). Verifique se o cluster HDInsight está vinculado a uma rede virtual e se a proteção contra DDoS está habilitada na rede virtual.

![Painéis "HDInsight" e "configurações avançadas", com configurações de rede virtual](./media/ddos-best-practices/image12.png)

![Seleção para habilitar a proteção contra DDoS](./media/ddos-best-practices/image13.png)

Nessa arquitetura, o tráfego destinado ao cluster HDInsight da Internet é roteado para o IP público associado ao balanceador de carga do gateway do HDInsight. O balanceador de carga de gateway, em seguida, envia o tráfego para os nós de cabeçalho ou os nós de trabalho diretamente. Como a proteção contra DDoS Standard está habilitada na rede virtual do HDInsight, todos os IPs públicos na rede virtual obtêm proteção contra DDoS para a camada 3 e 4. Essa arquitetura de referência pode ser combinada com as arquiteturas de referência de N camadas e de várias regiões.

Para obter mais informações sobre essa arquitetura de referência, consulte a documentação [estender o Azure HDInsight usando uma rede virtual do Azure](/azure/hdinsight/hdinsight-extend-hadoop-virtual-network?toc=%2fazure%2fvirtual-network%2ftoc.json) .


> [!NOTE]
> Ambiente do Serviço de Aplicativo do Azure para o PowerApps ou gerenciamento de API em uma rede virtual com um IP público não têm suporte nativo.

## <a name="next-steps"></a>Passos seguintes

* [Responsabilidade compartilhada na nuvem](shared-responsibility.md)

* [Página do produto proteção contra DDoS do Azure](https://azure.microsoft.com/services/ddos-protection/)

* [Documentação da proteção contra DDoS do Azure](/azure/virtual-network/ddos-protection-overview)
