---
title: Conceber soluções resilientes com proteção DDoS Azure
description: Saiba como pode utilizar dados de registo para obter informações profundas sobre a sua aplicação.
services: security
author: terrylanfear
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
ms.author: terrylan
ms.openlocfilehash: e298cb0d1a2c510a096f8ead03f8af7e39c206a8
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498936"
---
# <a name="azure-ddos-protection---designing-resilient-solutions"></a>Proteção Azure DDoS - Conceber soluções resilientes

Este artigo é para decisores de TI e pessoal de segurança. Espera que esteja familiarizado com o Azure, networking e segurança.
DDoS é um tipo de ataque que tenta esgotar os recursos de aplicação. O objetivo é afetar a disponibilidade da aplicação e a sua capacidade de lidar com pedidos legítimos. Os ataques estão a tornar-se mais sofisticados e maiores em tamanho e impacto. Os ataques de DDoS podem ser direcionadas para qualquer ponto final que esteja publicamente acessível através da internet. A conceção da resiliência distribuída de negação de serviço (DDoS) requer planeamento e conceção para uma variedade de modos de falha. O Azure fornece proteção contínua contra ataques DDoS. Esta proteção está integrada na plataforma Azure por padrão e sem custos adicionais.

Além da proteção DDoS de núcleo na plataforma, a [Azure DDoS Protection Standard](https://azure.microsoft.com/services/ddos-protection/) fornece capacidades avançadas de mitigação do DDoS contra ataques de rede. Está automaticamente sintonizado para proteger os seus recursos específicos do Azure. A proteção é simples de permitir durante a criação de novas redes virtuais. Também pode ser feito após a criação e não requer nenhuma aplicação ou alterações de recursos.

![O papel da Azure DDoS Protection na proteção dos clientes e de uma rede virtual de um intruso](./media/ddos-best-practices/image1.png)


## <a name="fundamental-best-practices"></a>Melhores práticas fundamentais

As seguintes secções dão orientações prescritivas para a construção de serviços de resiliência do DDoS em Azure.

### <a name="design-for-security"></a>Conceber para segurança

Garantir que a segurança é uma prioridade ao longo de todo o ciclo de vida de uma aplicação, desde a conceção e implementação até à implantação e operações. As aplicações podem ter bugs que permitem um volume relativamente baixo de pedidos para usar uma quantidade excessiva de recursos, resultando numa paragem de serviço. 

Para ajudar a proteger um serviço em execução no Microsoft Azure, deverá ter uma boa compreensão da arquitetura da sua aplicação e focar-se nos [cinco pilares da qualidade do software.](/azure/architecture/guide/pillars)
Deve conhecer os volumes de tráfego típicos, o modelo de conectividade entre a aplicação e outras aplicações, e os pontos finais de serviço que estão expostos à internet pública.

Garantir que uma aplicação é resistente o suficiente para lidar com uma negação de serviço que é direcionada para a aplicação em si é o mais importante. A segurança e a privacidade são integradas na plataforma Azure, a começar pelo Ciclo de [Vida para o Desenvolvimento de Segurança (SDL).](https://www.microsoft.com/sdl/default.aspx) O SDL aborda a segurança em todas as fases de desenvolvimento e garante que o Azure é continuamente atualizado para torná-lo ainda mais seguro.

### <a name="design-for-scalability"></a>Design para escalabilidade

A escalabilidade é o quão bem um sistema pode lidar com o aumento da carga. Desenhe as suas aplicações para [escalar horizontalmente](/azure/architecture/guide/design-principles/scale-out) para satisfazer a procura de uma carga amplificada, especificamente em caso de ataque DDoS. Se a sua aplicação depender de uma única instância de um serviço, cria um único ponto de falha. A provisionar várias instâncias torna o seu sistema mais resistente e escalável.

Para [o Azure App Service](../../app-service/overview.md), selecione um plano de Serviço de [Aplicações](../../app-service/overview-hosting-plans.md) que oferece várias instâncias. Para a Azure Cloud Services, configuure cada um dos seus papéis para usar [várias instâncias](../../cloud-services/cloud-services-choose-me.md). Para [máquinas virtuais Azure,](../../virtual-machines/index.yml)certifique-se de que a sua arquitetura de máquina virtual (VM) inclui mais de um VM e que cada VM está incluído num [conjunto de disponibilidade.](../../virtual-machines/windows/tutorial-availability-sets.md) Recomendamos a utilização [de conjuntos de escala de máquina virtual](../../virtual-machine-scale-sets/overview.md) para capacidades de autoscalagem.

### <a name="defense-in-depth"></a>Defesa em profundidade

A ideia por trás da defesa em profundidade é gerir o risco usando diversas estratégias defensivas. Colocar as defesas de segurança numa aplicação reduz a hipótese de um ataque bem sucedido. Recomendamos que implemente designs seguros para as suas aplicações utilizando as capacidades incorporadas da plataforma Azure.

Por exemplo, o risco de ataque aumenta com o tamanho *(área de superfície)* da aplicação. Pode reduzir a área de superfície utilizando uma lista de aprovação para fechar o espaço de endereço IP exposto e portas de audição que não são necessárias nos equilibros de carga[(Azure Load Balancer](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) e [Azure Application Gateway).](../../application-gateway/application-gateway-create-probe-portal.md) [Os grupos de segurança da rede (NSGs)](../../virtual-network/network-security-groups-overview.md) são outra forma de reduzir a superfície de ataque.
Pode utilizar [etiquetas de serviço](../../virtual-network/network-security-groups-overview.md#service-tags) e grupos de segurança de [aplicações](../../virtual-network/network-security-groups-overview.md#application-security-groups) para minimizar a complexidade para criar regras de segurança e configurar a segurança da rede, como uma extensão natural da estrutura de uma aplicação.

Deve implementar os serviços Azure numa [rede virtual](../../virtual-network/virtual-networks-overview.md) sempre que possível. Esta prática permite que os recursos de serviço se comuniquem através de endereços IP privados. O tráfego de serviço Azure de uma rede virtual utiliza endereços IP públicos como endereços IP de origem por padrão. A utilização [de pontos finais](../../virtual-network/virtual-network-service-endpoints-overview.md) de serviço irá mudar o tráfego de serviço para utilizar endereços privados de rede virtual como endereços IP de origem quando estão a aceder ao serviço Azure a partir de uma rede virtual.

Vemos muitas vezes os recursos dos clientes no local a serem atacados juntamente com os seus recursos em Azure. Se estiver a ligar um ambiente no local ao Azure, recomendamos que minimize a exposição dos recursos no local à internet pública. Pode utilizar a escala e as capacidades avançadas de proteção DDoS da Azure, implantando as suas conhecidas entidades públicas em Azure. Como estas entidades acessíveis ao público são muitas vezes um alvo para ataques DDoS, colocá-los em Azure reduz o impacto nos seus recursos no local.

## <a name="azure-offerings-for-ddos-protection"></a>Ofertas Azure para proteção DDoS

A Azure tem duas ofertas de serviço DDoS que fornecem proteção contra ataques de rede (Camada 3 e 4): DDoS Protection Basic e DDoS Protection Standard. 

### <a name="ddos-protection-basic"></a>DDos Proteção Básica

A proteção básica é integrada no Azure por defeito sem custos adicionais. A escala e a capacidade da rede Azure implantada globalmente fornece defesa contra ataques comuns de camadas de rede através de monitorização de tráfego sempre on e mitigação em tempo real. A DDoS Protection Basic não requer configuração do utilizador nem alterações na aplicação. A DDoS Protection Basic ajuda a proteger todos os serviços da Azure, incluindo serviços PaaS como o Azure DNS.

![Representação do mapa da rede Azure, com o texto "Presença Global de Mitigação do DDoS" e "Capacidade de mitigação do DDoS líder"](./media/ddos-best-practices/image3.png)

A proteção DDoS básica em Azure consiste tanto em componentes de software como de hardware. Um avião de controlo de software decide quando, onde e que tipo de tráfego deve ser conduzido através de aparelhos de hardware que analisam e removem o tráfego de ataques. O avião de controlo toma esta decisão com base numa *política* de proteção do DDoS em toda a infraestrutura. Esta política é estática e aplicada universalmente a todos os clientes da Azure.

Por exemplo, a política de proteção do DDoS especifica em que volume de tráfego a proteção deve ser *ativada.* (Ou seja, o tráfego do inquilino deve ser encaminhado através de aparelhos de limpeza.) A política especifica então como os aparelhos de esfregar devem *atenuar* o ataque.

O serviço Azure DDoS Protection Basic destina-se à proteção da infraestrutura e proteção da plataforma Azure. Atenua o tráfego quando excede uma taxa tão significativa que pode afetar vários clientes num ambiente multitenant. Não fornece políticas personalizadas de alerta ou por cliente.

### <a name="ddos-protection-standard"></a>DDoS Protection Standard

A proteção padrão fornece funcionalidades de mitigação de DDoS melhoradas. É automaticamente sintonizado para ajudar a proteger os seus recursos Azure específicos numa rede virtual. A proteção é simples de permitir qualquer rede virtual nova ou existente, e não requer nenhuma aplicação ou alterações de recursos. Tem várias vantagens sobre o serviço básico, incluindo registo, alerta e telemetria. As secções seguintes descrevem as principais características do serviço Azure DDoS Protection Standard.

#### <a name="adaptive-real-time-tuning"></a>Afinação em tempo real adaptativo

O serviço Azure DDoS Protection Basic ajuda a proteger os clientes e a prevenir impactos para outros clientes. Por exemplo, se um serviço for prestado para um volume típico de tráfego legítimo de entrada que seja menor do que a taxa de *disparo* da política de proteção dDos em toda a infraestrutura, um ataque DDoS aos recursos desse cliente pode passar despercebido. De uma forma mais geral, a complexidade dos ataques recentes (por exemplo, DDoS multi-vectores) e os comportamentos específicos de aplicação dos inquilinos exigem políticas de proteção personalizadas por cliente. O serviço realiza esta personalização utilizando dois insights:

- Aprendizagem automática de padrões de tráfego por cliente (por IP) para as camadas 3 e 4.

- Minimizar falsos positivos, considerando que a escala de Azure permite absorver uma quantidade significativa de tráfego.

![Diagrama de como funciona a Norma de Proteção DDoS, com "Policy Generation" em círculo](./media/ddos-best-practices/image5.png)

#### <a name="ddos-protection-telemetry-monitoring-and-alerting"></a>Telemetria de Proteção DDoS, monitorização e alerta

A DDoS Protection Standard expõe telemetria rica via [Azure Monitor](../../azure-monitor/overview.md) durante a duração de um ataque DDoS. Pode configurar alertas para qualquer uma das métricas do Monitor Azure que o DDoS Protection utiliza. Pode integrar o registo com Splunk (Azure Event Hubs), registos Azure Monitor e Azure Storage para análise avançada através da interface Azure Monitor Diagnostics.

##### <a name="ddos-mitigation-policies"></a>Políticas de mitigação do DDoS

No portal Azure, **Monitor** selecione  >  **Monitor Metrics**. No painel **Métricas,** selecione o grupo de recursos, selecione um tipo de recurso de **Endereço IP Público** e selecione o seu endereço IP público Azure. As métricas DDoS são visíveis no painel **de métricas disponíveis.**

A DDoS Protection Standard aplica três políticas de mitigação autofinadas (TCP SYN, TCP e UDP) para cada IP público do recurso protegido, na rede virtual que tem DDoS ativado. Pode ver os limiares de política selecionando os **pacotes métricos de entrada para desencadear a mitigação do DDoS**.

![Gráfico de métricas e métricas disponíveis](./media/ddos-best-practices/image7.png)

Os limiares de política são configurados automaticamente através de perfis de tráfego de rede baseados em aprendizagem automática. A mitigação do DDoS ocorre para um endereço IP sob ataque apenas quando o limiar da apólice é ultrapassado.

##### <a name="metric-for-an-ip-address-under-ddos-attack"></a>Métrica para um endereço IP sob ataque DDoS

Se o endereço IP público estiver sob ataque, o valor da métrica sob ataque **DDoS ou não** muda para 1, uma vez que a DDoS Protection realiza mitigação no tráfego de ataque.

![Métrica e gráfico "Ataque DDoS ou não"](./media/ddos-best-practices/image8.png)

Recomendamos configurar um alerta nesta métrica. Em seguida, será notificado quando houver uma mitigação ativa do DDoS realizada no seu endereço IP público.

Para obter mais informações, consulte [a Norma de Proteção Azure DDoS utilizando o portal Azure](../../ddos-protection/manage-ddos-protection.md).

#### <a name="web-application-firewall-for-resource-attacks"></a>Firewall de aplicação web para ataques de recursos

Específico para ataques de recursos na camada de aplicação, você deve configurar uma firewall de aplicação web (WAF) para ajudar a proteger aplicações web. Um WAF inspeciona o tráfego web de entrada para bloquear injeções DE SQL, scripts de cross-site, DDoS e outros ataques da Camada 7. O Azure fornece [o WAF como uma característica do Gateway de Aplicação](../../web-application-firewall/ag/ag-overview.md) para proteção centralizada das suas aplicações web contra explorações e vulnerabilidades comuns. Existem outras ofertas waf disponíveis de parceiros Azure que podem ser mais adequados para as suas necessidades através do [Azure Marketplace.](https://azuremarketplace.microsoft.com/marketplace/apps?search=WAF&page=1)

Até as firewalls de aplicações web são suscetíveis a ataques volumosos e de exaustão do estado. Recomendamos vivamente que permita que o DDoS Protection Standard na rede virtual WAF ajude a proteger contra ataques volumosos e protocolares. Para mais informações, consulte a secção [de arquiteturas de referência da DDoS Protection.](#ddos-protection-reference-architectures)

### <a name="protection-planning"></a>Planeamento de proteção

O planeamento e preparação são cruciais para entender como um sistema irá funcionar durante um ataque DDoS. A elaboração de um plano de resposta à gestão de incidentes faz parte deste esforço.

Se tiver o DDoS Protection Standard, certifique-se de que está ativado na rede virtual de pontos finais virados para a Internet. Configurar alertas DDoS ajuda-o constantemente a observar eventuais ataques à sua infraestrutura. 

Monitorize as suas aplicações de forma independente. Entenda o comportamento normal de uma aplicação. Prepare-se para agir se a aplicação não se comportar como esperado durante um ataque DDoS.

#### <a name="testing-through-simulations"></a>Teste através de simulações

É uma boa prática testar as suas suposições sobre como os seus serviços vão responder a um ataque realizando simulações periódicas. Durante os testes, valide que os seus serviços ou aplicações continuam a funcionar como esperado e não há nenhuma perturbação na experiência do utilizador. Identificar lacunas do ponto de vista da tecnologia e do processo e incorporá-las na estratégia de resposta do DDoS. Recomendamos que realize tais testes em ambientes de preparação ou em horas não de ponta para minimizar o impacto no ambiente de produção.

Estabelecemos uma parceria com a [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) para construir uma interface onde os clientes do Azure podem gerar tráfego contra pontos finais públicos ativados pela DDoS Protection para simulações. Pode utilizar a simulação [da Cloud BreakingPoint](https://www.ixiacom.com/products/breakingpoint-cloud) para:

- Valide como a Azure DDoS Protection ajuda a proteger os seus recursos Azure de ataques DDoS.

- Otimize o seu processo de resposta a incidentes sob ataque DDoS.

- Conformidade do DDoS do documento.

- Treine as suas equipas de segurança de rede.

A cibersegurança requer uma inovação constante na defesa. A proteção Azure DDoS Standard é uma oferta de última geração com uma solução eficaz para mitigar ataques DDoS cada vez mais complexos.

## <a name="components-of-a-ddos-response-strategy"></a>Componentes de uma estratégia de resposta a DDoS

Um ataque DDoS que visa os recursos do Azure geralmente requer uma intervenção mínima do ponto de vista do utilizador. Ainda assim, incorporar a mitigação do DDoS como parte de uma estratégia de resposta a incidentes ajuda a minimizar o impacto na continuidade do negócio.

### <a name="microsoft-threat-intelligence"></a>Inteligência de ameaça da Microsoft

A Microsoft tem uma extensa rede de inteligência de ameaças. Esta rede utiliza o conhecimento coletivo de uma comunidade de segurança alargada que suporta serviços online da Microsoft, parceiros microsoft e relações dentro da comunidade de segurança da Internet. 

Como fornecedor de infraestruturas críticas, a Microsoft recebe avisos antecipados sobre ameaças. A Microsoft recolhe informações sobre ameaças dos seus serviços online e da sua base global de clientes. A Microsoft incorpora toda esta inteligência de ameaça de volta aos produtos Azure DDoS Protection.

Além disso, a Unidade de Crimes Digitais da Microsoft (DCU) executa estratégias ofensivas contra botnets. Botnets são uma fonte comum de comando e controlo para ataques DDoS.

### <a name="risk-evaluation-of-your-azure-resources"></a>Avaliação de risco dos seus recursos Azure

É imperativo entender o alcance do seu risco de um ataque DDoS numa base contínua. Pergunte-se periodicamente:

- Que novos recursos azure publicamente disponíveis precisam de proteção?

- Há um único ponto de falha no serviço? 

- Como é que os serviços podem ser isolados para limitar o impacto de um ataque, ao mesmo tempo que disponibilizam serviços a clientes válidos?

- Existem redes virtuais onde o DDoS Protection Standard deve ser ativado mas não é? 

- Os meus serviços estão ativos/ativos com falhas em várias regiões?

### <a name="customer-ddos-response-team"></a>Equipa de resposta DDoS do cliente

Criar uma equipa de resposta do DDoS é um passo fundamental para responder a um ataque de forma rápida e eficaz. Identifique os contactos na sua organização que supervisionarão o planeamento e a execução. Esta equipa de resposta DDoS deve compreender cuidadosamente o serviço Azure DDoS Protection Standard. Certifique-se de que a equipa pode identificar e mitigar um ataque coordenando com clientes internos e externos, incluindo a equipa de suporte da Microsoft.

Para a sua equipa de resposta DDoS, recomendamos que utilize exercícios de simulação como parte normal da disponibilidade do seu serviço e planeamento de continuidade. Estes exercícios devem incluir testes à escala.

### <a name="alerts-during-an-attack"></a>Alertas durante um ataque

A Azure DDoS Protection Standard identifica e atenua os ataques DDoS sem qualquer intervenção do utilizador. Para ser notificado quando há uma mitigação ativa para um IP público protegido, você pode [configurar um alerta](../../ddos-protection/manage-ddos-protection.md) sobre a métrica sob o **ataque DDoS ou não**. Pode optar por criar alertas para as outras métricas do DDoS para entender a escala do ataque, o tráfego a ser retirado e outros detalhes.

#### <a name="when-to-contact-microsoft-support"></a>Quando entrar em contato com o suporte da Microsoft

- Durante um ataque DDoS, você descobre que o desempenho do recurso protegido está severamente degradado, ou o recurso não está disponível.

- Acha que o serviço de proteção DDoS não se está a comportar como esperado. 

  O serviço de proteção DDoS só inicia a mitigação se a política de valor métrico **para desencadear a mitigação do DDoS (TCP/TCP SYN/UDP)** for inferior ao tráfego recebido no recurso IP público protegido.

- Está a planear um evento viral que aumentará significativamente o tráfego da sua rede.

- Um ator ameaçou lançar um ataque DDoS contra os seus recursos.

- Se precisar de permitir a lista de uma gama IP ou IP a partir da Norma de Proteção DDoS Azure. Um cenário comum é permitir a lista IP se o tráfego for encaminhado de uma nuvem externa WAF para Azure. 

Para ataques que tenham um impacto crítico no negócio, crie um bilhete de [apoio](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)de severidade-A .

### <a name="post-attack-steps"></a>Passos pós-ataque

É sempre uma boa estratégia fazer uma autópsia após um ataque e ajustar a estratégia de resposta do DDoS conforme necessário. Coisas a considerar:

- Houve alguma perturbação no serviço ou na experiência do utilizador devido à falta de arquitetura escalável?

- Quais aplicações ou serviços foram os que mais sofreram?

- Quão eficaz foi a estratégia de resposta do DDoS e como pode ser melhorada?

Se suspeita que está sob um ataque DDoS, aumente pelos seus canais normais de apoio ao Azure.

## <a name="ddos-protection-reference-architectures"></a>Arquiteturas de referência de proteção DDoS

A DDoS Protection Standard é concebida [para serviços implantados numa rede virtual.](../../virtual-network/virtual-network-for-azure-services.md) Para outros serviços, aplica-se o serviço básico de proteção DDoS predefinido. As seguintes arquiteturas de referência são organizadas por cenários, com padrões de arquitetura agrupados.

### <a name="virtual-machine-windowslinux-workloads"></a>Cargas de trabalho de máquina virtual (Windows/Linux)

#### <a name="application-running-on-load-balanced-vms"></a>Aplicação em execução em VMs equilibrados em carga

Esta arquitetura de referência mostra um conjunto de práticas comprovadas para executar vários VMs Windows em uma escala definida atrás de um equilibrador de carga, para melhorar a disponibilidade e escalabilidade. Esta arquitetura pode ser usada para qualquer carga de trabalho apátrida, como um servidor web.

![Diagrama da arquitetura de referência para uma aplicação em execução em VMs equilibrados em carga](./media/ddos-best-practices/image9.png)

Nesta arquitetura, uma carga de trabalho é distribuída por várias instâncias de VM. Existe um único endereço IP público, e o tráfego de internet é distribuído para os VMs através de um equilibrador de carga. A Norma de Proteção DDoS está ativada na rede virtual do equilibrador de carga Azure (internet) que tem o IP público associado.

O equilibrador de carga distribui os pedidos de entrada na Internet para as instâncias VM. Os conjuntos de escala de máquinas virtuais permitem que o número de VMs seja dimensionado manualmente ou eliminados manualmente, ou automaticamente com base em regras predefinidas. Isto é importante se o recurso estiver sob ataque DDoS. Para mais informações sobre esta arquitetura de referência, consulte [este artigo.](/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm)

#### <a name="application-running-on-windows-n-tier"></a>Aplicação em execução no windows n-tier

Existem várias formas de implementar uma arquitetura de N camadas. O diagrama seguinte mostra uma aplicação web típica de três níveis. Esta arquitetura baseia-se no artigo [Executar VMs equilibrados em carga para escalabilidade e disponibilidade.](/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm) As camadas Web e Business utilizam VMs com balanceamento de carga.

![Diagrama da arquitetura de referência para uma aplicação em execução no windows n-tier](./media/ddos-best-practices/image10.png)

Nesta arquitetura, a DDoS Protection Standard está ativada na rede virtual. Todos os IPs públicos da rede virtual obtêm proteção DDoS para as camadas 3 e 4. Para a proteção da camada 7, coloque o Gateway de aplicação no SKU WAF. Para mais informações sobre esta arquitetura de referência, consulte [este artigo.](/azure/architecture/reference-architectures/virtual-machines-windows/n-tier)

#### <a name="paas-web-application"></a>Aplicação web PaaS

Esta arquitetura de referência mostra executar uma aplicação Azure App Service numa única região. Esta arquitetura mostra um conjunto de práticas comprovadas para uma aplicação web que utiliza [o Azure App Service](https://azure.microsoft.com/documentation/services/app-service/) e a [Azure SQL Database](https://azure.microsoft.com/documentation/services/sql-database/).
Está criada uma região de prontidão para cenários de falhanço.

![Diagrama da arquitetura de referência para uma aplicação web PaaS](./media/ddos-best-practices/image11.png)

O Gestor de Tráfego da Azure encaminha os pedidos de entrada para o Gateway de Aplicação numa das regiões. Durante as operações normais, encaminha os pedidos para o Gateway de Aplicação na região ativa. Se essa região ficar indisponível, o Gestor de Tráfego falha no Application Gateway na região de espera.

Todo o tráfego da internet destinado à aplicação web é encaminhado para o [endereço IP público do Gateway de Aplicação](../../application-gateway/application-gateway-web-app-overview.md) através do Gestor de Tráfego. Neste cenário, o próprio serviço de aplicações (web app) não está diretamente virado para o exterior e está protegido pela App Gateway. 

Recomendamos que configuure o Gateway de Aplicação WAF SKU (modo de prevenção) para ajudar a proteger contra ataques da Camada 7 (HTTP/HTTPS/WebSocket). Além disso, as aplicações web estão configuradas para aceitar apenas o tráfego a partir do endereço [IP do Gateway de Aplicação.](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/)

Para mais informações sobre esta arquitetura de referência, consulte [este artigo.](/azure/architecture/reference-architectures/app-service-web-app/multi-region)

### <a name="mitigation-for-non-web-paas-services"></a>Mitigação para serviços PaaS não web

#### <a name="hdinsight-on-azure"></a>HDInsight em Azure

Esta arquitetura de referência mostra configurar o DDoS Protection Standard para um [cluster Azure HDInsight](../../hdinsight/index.yml). Certifique-se de que o cluster HDInsight está ligado a uma rede virtual e que a Proteção DDoS está ativada na rede virtual.

![Painéis "HDInsight" e "Configurações avançadas", com configurações de rede virtuais](./media/ddos-best-practices/image12.png)

![Seleção para permitir a proteção DDoS](./media/ddos-best-practices/image13.png)

Nesta arquitetura, o tráfego destinado ao cluster HDInsight a partir da internet é encaminhado para o IP público associado ao equilibrador de carga de gateway HDInsight. Em seguida, o balançador de carga de gateway envia o tráfego para os nós da cabeça ou o trabalhador acena diretamente. Como o DDoS Protection Standard está ativado na rede virtual HDInsight, todos os IPs públicos da rede virtual obtêm proteção DDoS para as Camadas 3 e 4. Esta arquitetura de referência pode ser combinada com as arquiteturas de referência N-Tier e multi-regiões.

Para obter mais informações sobre esta arquitetura de referência, consulte o [Extend Azure HDInsight utilizando uma documentação da Rede Virtual Azure.](../../hdinsight/hdinsight-plan-virtual-network-deployment.md?toc=%2fazure%2fvirtual-network%2ftoc.json)


> [!NOTE]
> O Azure App Service Environment for PowerApps ou a gestão de API numa rede virtual com um IP público não são ambos suportados de forma nativa.

## <a name="next-steps"></a>Passos seguintes

* [Responsabilidade partilhada na cloud](shared-responsibility.md)
* [Página de produto Azure DDoS Protection](https://azure.microsoft.com/services/ddos-protection/)
* [Documentação de proteção Azure DDoS](../../ddos-protection/ddos-protection-overview.md)