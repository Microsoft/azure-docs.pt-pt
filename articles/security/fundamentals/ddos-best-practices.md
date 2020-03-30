---
title: Conceber soluções resilientes com proteção DDoS Azure
description: Saiba como pode usar dados de registo para obter informações profundas sobre a sua aplicação.
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
ms.openlocfilehash: 8d3fc809999508bf3d49c3765c90017e89e80fa7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624045"
---
# <a name="azure-ddos-protection---designing-resilient-solutions"></a>Azure DDoS Protection - Conceção de soluções resilientes

Este artigo é para decisores de TI e pessoal de segurança. Espera que esteja familiarizado com azure, networking e segurança.
DDoS é um tipo de ataque que tenta esgotar os recursos de aplicação. O objetivo é afetar a disponibilidade da aplicação e a sua capacidade de lidar com pedidos legítimos. Os ataques estão a tornar-se mais sofisticados e maiores em tamanho e impacto. Os ataques de DDoS podem ser direcionadas para qualquer ponto final que esteja publicamente acessível através da internet. Conceber para a negação distribuída de serviço (DDoS) resiliência requer planeamento e conceção para uma variedade de modos de falha. O Azure fornece proteção contínua contra ataques DDoS. Esta proteção está integrada na plataforma Azure por padrão e sem custos adicionais.

Além da proteção principal do DDoS na plataforma, a Norma de [Proteção DDoS Azure](https://azure.microsoft.com/services/ddos-protection/) fornece capacidades avançadas de mitigação de DDoS contra ataques de rede. É automaticamente sintonizado para proteger os seus recursos específicos do Azure. A proteção é simples de permitir durante a criação de novas redes virtuais. Também pode ser feito após a criação e não requer alterações na aplicação ou recursos.

![O papel da Proteção DDoS Azure na proteção de clientes e uma rede virtual de um intruso](./media/ddos-best-practices/image1.png)


## <a name="fundamental-best-practices"></a>Boas práticas fundamentais

As seguintes secções dão orientações prescritivas para a construção de serviços dDoS resilientes em Azure.

### <a name="design-for-security"></a>Conceber para segurança

Garantir que a segurança é uma prioridade ao longo de todo o ciclo de vida de uma aplicação, desde a conceção e implementação até à implantação e operações. As aplicações podem ter bugs que permitem que um volume relativamente baixo de pedidos utilizem uma quantidade desordenada de recursos, resultando numa interrupção de serviço. 

Para ajudar a proteger um serviço em execução no Microsoft Azure, deve ter uma boa compreensão da arquitetura da sua aplicação e focar-se nos [cinco pilares da qualidade do software.](/azure/architecture/guide/pillars)
Deve conhecer os volumes de tráfego típicos, o modelo de conectividade entre a aplicação e outras aplicações, e os pontos finais de serviço que estão expostos à internet pública.

Garantir que uma aplicação é suficientemente resistente para lidar com uma negação de serviço direcionada à aplicação em si é o mais importante. A segurança e a privacidade são integradas na plataforma Azure, começando pelo Ciclo de Vida de Desenvolvimento de [Segurança (SDL)](https://www.microsoft.com/sdl/default.aspx). O SDL aborda a segurança em todas as fases de desenvolvimento e garante que o Azure é continuamente atualizado para torná-lo ainda mais seguro.

### <a name="design-for-scalability"></a>Design para escalabilidade

A escalabilidade é o quão bem um sistema consegue lidar com o aumento da carga. Desenhe as suas aplicações para [escalar horizontalmente](/azure/architecture/guide/design-principles/scale-out) para satisfazer a procura de uma carga amplificada, especificamente em caso de ataque DDoS. Se a sua aplicação depender de uma única instância de um serviço, cria um único ponto de falha. O fornecimento de múltiplas instâncias torna o seu sistema mais resistente e escalável.

Para o [Azure App Service,](/azure/app-service/app-service-value-prop-what-is)selecione um plano de serviço de [aplicações](/azure/app-service/overview-hosting-plans) que ofereça várias instâncias. Para os Serviços Azure Cloud, configure cada uma das suas funções para utilizar [várias instâncias](/azure/cloud-services/cloud-services-choose-me). Para [máquinas virtuais Azure,](/azure/virtual-machines/virtual-machines-windows-about/?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)certifique-se de que a sua arquitetura de máquina virtual (VM) inclui mais de um VM e que cada VM está incluído num conjunto de [disponibilidade](/azure/virtual-machines/virtual-machines-windows-manage-availability). Recomendamos a utilização de [conjuntos de escala](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview) de máquinas virtuais para capacidades de autoscalcificação.

### <a name="defense-in-depth"></a>Defesa em profundidade

A ideia por trás da defesa em profundidade é gerir o risco utilizando diversas estratégias defensivas. Camadas de defesas de segurança numa aplicação reduz a hipótese de um ataque bem sucedido. Recomendamos que implemente designs seguros para as suas aplicações utilizando as capacidades incorporadas da plataforma Azure.

Por exemplo, o risco de ataque aumenta com o tamanho *(área*de superfície ) da aplicação. Pode reduzir a área de superfície utilizando a lista branca para fechar o espaço de endereçoIP exposto e as portas de audição que não são necessárias nos equilibradores de carga[(Azure Load Balancer](/azure/load-balancer/load-balancer-get-started-internet-portal) e [Azure Application Gateway).](/azure/application-gateway/application-gateway-create-probe-portal) Os grupos de segurança da [rede (NSGs)](/azure/virtual-network/security-overview) são outra forma de reduzir a superfície de ataque.
Pode utilizar etiquetas de serviço e [grupos](/azure/virtual-network/security-overview#service-tags) de segurança de [aplicações](/azure/virtual-network/security-overview#application-security-groups) para minimizar a complexidade para criar regras de segurança e configurar a segurança da rede, como uma extensão natural da estrutura de uma aplicação.

Deve implantar serviços Azure numa [rede virtual](/azure/virtual-network/virtual-networks-overview) sempre que possível. Esta prática permite que os recursos de serviço se comuniquem através de endereços IP privados. O tráfego de serviço saque a partir de uma rede virtual utiliza endereços IP públicos como endereços IP de origem por padrão. A utilização de [pontos finais](/azure/virtual-network/virtual-network-service-endpoints-overview) de serviço irá mudar o tráfego de serviços para utilizar endereços privados de rede virtual como endereços IP de origem quando estão a aceder ao serviço Azure a partir de uma rede virtual.

Vemos muitas vezes os recursos dos clientes no local a serem atacados juntamente com os seus recursos em Azure. Se estiver a ligar um ambiente no local ao Azure, recomendamos que minimize a exposição de recursos no local à internet pública. Pode utilizar a escala e as capacidades avançadas de proteção dDoS do Azure, implantando as suas conhecidas entidades públicas em Azure. Como estas entidades acessíveis ao público são muitas vezes um alvo para ataques DDoS, colocá-los em Azure reduz o impacto nos seus recursos no local.

## <a name="azure-offerings-for-ddos-protection"></a>Ofertas azure para proteção DDoS

O Azure tem duas ofertas de serviçoS DDoS que fornecem proteção contra ataques de rede (Camada 3 e 4): Norma de Proteção DDoS Básica e DDoS. 

### <a name="ddos-protection-basic"></a>Ddos Proteção Básica

A proteção básica é integrada no Azure por defeito sem custos adicionais. A escala e a capacidade da rede Azure globalmente implantada fornecem defesa contra ataques comuns em camadas de rede através de monitorização de tráfego sempre on-on e mitigação em tempo real. A Proteção DDoS Basic não requer alterações na configuração do utilizador ou na aplicação. A DDoS Protection Basic ajuda a proteger todos os serviços Azure, incluindo serviços PaaS como o Azure DNS.

![Mapa da representação da rede Azure, com o texto "Global DDoS mitigation presence" e "Leading DDoS mitigation capacity"](./media/ddos-best-practices/image3.png)

A proteção Básica dDoS em Azure consiste tanto em componentes de software como de hardware. Um plano de controlo de software decide quando, onde e que tipo de tráfego deve ser conduzido através de aparelhos de hardware que analisam e removem o tráfego de ataque. O plano de controlo toma esta decisão com base numa *política*de proteção de DDoS em toda a infraestrutura. Esta política é definida estáticamente e aplicada universalmente a todos os clientes Azure.

Por exemplo, a política de proteção do DDoS especifica em que volume de tráfego a proteção deve ser *ativada.* (Ou seja, o tráfego do inquilino deve ser encaminhado através de aparelhos de limpeza.) A política especifica então como os aparelhos de esfregar devem *atenuar* o ataque.

O serviço Deproteção Azure DDoS é direcionado para a proteção da infraestrutura e proteção da plataforma Azure. Atenua o tráfego quando excede uma taxa tão significativa que pode afetar vários clientes num ambiente multiarrendatário. Não fornece políticas personalizadas de alerta ou por cliente.

### <a name="ddos-protection-standard"></a>Norma de proteção ddos

A proteção padrão fornece características de mitigação dDoS melhoradas. É automaticamente sintonizado para ajudar a proteger os seus recursos Azure específicos numa rede virtual. A proteção é simples de permitir qualquer rede virtual nova ou existente, e não requer alterações de aplicação ou recursos. Tem várias vantagens sobre o serviço básico, incluindo a exploração madeireira, alerta e telemetria. As seguintes secções descrevem as principais características do serviço Azure DDoS Protection Standard.

#### <a name="adaptive-real-time-tuning"></a>Afinação em tempo real adaptativo

O serviço Azure DDoS Protection Basic ajuda a proteger os clientes e a prevenir impactos para outros clientes. Por exemplo, se um serviço for provisionado para um volume típico de tráfego legítimo que é menor do que a taxa de *desencadeamento* da política de proteção dDoS em toda a infraestrutura, um ataque DDoS aos recursos desse cliente pode passar despercebido. De uma forma mais geral, a complexidade dos ataques recentes (por exemplo, dDoS multi-vetoriais) e os comportamentos específicos da aplicação dos inquilinos exigem políticas de proteção personalizadas por cliente. O serviço realiza esta personalização utilizando duas insights:

- Aprendizagem automática dos padrões de tráfego por cliente (por IP) para as camadas 3 e 4.

- Minimizando falsos positivos, considerando que a escala de Azure lhe permite absorver uma quantidade significativa de tráfego.

![Diagrama de como funciona a Norma de Proteção DDoS, com "Geração política" em círculo](./media/ddos-best-practices/image5.png)

#### <a name="ddos-protection-telemetry-monitoring-and-alerting"></a>Telemetria, monitorização e alerta de proteção DDoS

DDoS Protection Standard expõe telemetria rica via [Monitor Azure](/azure/azure-monitor/overview) durante a duração de um ataque DDoS. Pode configurar alertas para qualquer uma das métricas do Monitor Azure que a DDoS Protection utiliza. Pode integrar a exploração madeireira com o Splunk (Azure Event Hubs), os registos do Azure Monitor e o Armazenamento Azure para análise avançada através da interface de Diagnóstico do Monitor Azure.

##### <a name="ddos-mitigation-policies"></a>Políticas de mitigação do DDoS

No portal Azure, selecione **Monitor** > **Metrics**. No painel **Métricas,** selecione o grupo de recursos, selecione um tipo de endereço **IP público**de recursos, e selecione o seu endereço IP público Azure. As métricas DDoS são visíveis no painel de **métricas disponíveis.**

A DDoS Protection Standard aplica três políticas de mitigação auto-ajustadas (TCP SYN, TCP e UDP) para cada IP público do recurso protegido, na rede virtual que tem DDoS ativado. Pode visualizar os limiares de política selecionando os pacotes de **entrada métricos para desencadear a mitigação do DDoS**.

![Gráfico de métricas e métricas disponíveis](./media/ddos-best-practices/image7.png)

Os limiares de política são configurados automaticamente através de perfis de tráfego de rede baseados em machine learning. A mitigação do DDoS ocorre para um endereço IP sob ataque apenas quando o limiar de política é ultrapassado.

##### <a name="metric-for-an-ip-address-under-ddos-attack"></a>Métrica para um endereço IP sob ataque DDoS

Se o endereço IP público estiver sob ataque, o valor para a métrica **sob ataque DDoS ou não** muda para 1, uma vez que a Proteção DDoS realiza mitigação no tráfego de ataque.

![Métrica e gráfico "sob ataque DDoS ou não"](./media/ddos-best-practices/image8.png)

Recomendamos configurar um alerta sobre esta métrica. Em seguida, será notificado quando houver uma mitigação ativa do DDoS realizada no seu endereço IP público.

Para mais informações, consulte [Manage Azure DDoS Protection Standard utilizando o portal Azure](/azure/virtual-network/ddos-protection-manage-portal).

#### <a name="web-application-firewall-for-resource-attacks"></a>Firewall de aplicação web para ataques de recursos

Específico para ataques de recursos na camada de aplicação, deve configurar uma firewall de aplicação web (WAF) para ajudar a proteger aplicações web. Um WAF inspeciona o tráfego web de entrada para bloquear injeções SQL, scripts transversais, DDoS e outros ataques da Camada 7. O Azure fornece o [WAF como uma característica do Application Gateway](/azure/application-gateway/application-gateway-web-application-firewall-overview) para proteção centralizada das suas aplicações web a partir de explorações e vulnerabilidades comuns. Existem outras ofertas WAF disponíveis de parceiros Azure que podem ser mais adequados para as suas necessidades através do [Azure Marketplace.](https://azuremarketplace.microsoft.com/marketplace/apps?search=WAF&page=1)

Mesmo as firewalls de aplicação web são suscetíveis a ataques de exaustão volutrôtrica e estatal. Recomendamos vivamente que a Norma de Proteção DDoS na rede virtual WAF ajude a proteger contra ataques volutrôricos e protocolares. Para mais informações, consulte a secção de arquiteturas de [referência dDoS Protection.](#ddos-protection-reference-architectures)

### <a name="protection-planning"></a>Planeamento de proteção

O planeamento e a preparação são cruciais para entender como um sistema irá funcionar durante um ataque dDoS. A conceção de um plano de resposta à gestão de incidentes faz parte deste esforço.

Se tiver o Padrão de Proteção DDoS, certifique-se de que está ativado na rede virtual de pontos finais virados para a Internet. Configurar alertas DDoS ajuda-o constantemente a observar eventuais ataques à sua infraestrutura. 

Monitorize as suas aplicações de forma independente. Compreenda o comportamento normal de uma aplicação. Prepare-se para agir se a aplicação não se comportar como esperado durante um ataque dDoS.

#### <a name="testing-through-simulations"></a>Teste através de simulações

É uma boa prática testar as suas suposições sobre como os seus serviços responderão a um ataque através da realização de simulações periódicas. Durante os testes, valide que os seus serviços ou aplicações continuem a funcionar como esperado e não há nenhuma perturbação na experiência do utilizador. Identifique lacunas tanto do ponto de vista da tecnologia como do processo e incorpore-as na estratégia de resposta do DDoS. Recomendamos que realize tais testes em ambientes de paragem ou em horas não-álteras para minimizar o impacto no ambiente de produção.

Estabelecemos uma parceria com a [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) para construir uma interface onde os clientes Azure possam gerar tráfego contra pontos finais públicos ativados pela DDoS Protection para simulações. Pode utilizar a simulação [da Nuvem de BreakingPoint](https://www.ixiacom.com/products/breakingpoint-cloud) para:

- Valide como a Proteção DDoS Azure ajuda a proteger os seus recursos Azure de ataques DDoS.

- Otimize o seu processo de resposta a incidentes durante o ataque do DDoS.

- Conformidade com o Documento DDoS.

- Treine as suas equipas de segurança da rede.

A cibersegurança requer uma inovação constante na defesa. A proteção padrão Azure DDoS é uma oferta de última geração com uma solução eficaz para mitigar ataques DDoS cada vez mais complexos.

## <a name="components-of-a-ddos-response-strategy"></a>Componentes de uma estratégia de resposta dDoS

Um ataque DDoS que visa os recursos do Azure requer geralmente uma intervenção mínima do ponto de vista do utilizador. Ainda assim, incorporar a mitigação do DDoS como parte de uma estratégia de resposta a incidentes ajuda a minimizar o impacto na continuidade do negócio.

### <a name="microsoft-threat-intelligence"></a>Inteligência de ameaça da Microsoft

A Microsoft tem uma extensa rede de inteligência de ameaças. Esta rede utiliza o conhecimento coletivo de uma comunidade de segurança alargada que suporta serviços online da Microsoft, parceiros microsoft e relações dentro da comunidade de segurança da Internet. 

Como fornecedor de infraestruturas crítica, a Microsoft recebe avisos precoces sobre ameaças. A Microsoft recolhe informações de ameaças dos seus serviços online e da sua base global de clientes. A Microsoft incorpora toda esta inteligência de ameaça de volta aos produtos de proteção De DDoS Azure.

Além disso, a Unidade de Crimes Digitais da Microsoft (DCU) realiza estratégias ofensivas contra botnets. Botnets são uma fonte comum de comando e controlo para ataques DDoS.

### <a name="risk-evaluation-of-your-azure-resources"></a>Avaliação de risco dos seus recursos Azure

É imperativo entender o alcance do seu risco de um ataque dDoS numa base contínua. Periodicamente pergunte a si mesmo:

- Que novos recursos azure disponíveis ao público precisam de proteção?

- Há um único ponto de falha no serviço? 

- Como é que os serviços podem ser isolados para limitar o impacto de um ataque, ao mesmo tempo que disponibilizam serviços a clientes válidos?

- Existem redes virtuais onde a Norma de Proteção DDoS deve ser ativada, mas não é? 

- Os meus serviços estão ativos/ativos com falhas em várias regiões?

### <a name="customer-ddos-response-team"></a>Equipa de resposta dDoS do cliente

Criar uma equipa de resposta DDoS é um passo fundamental para responder a um ataque de forma rápida e eficaz. Identifique contactos na sua organização que supervisionarão tanto o planeamento como a execução. Esta equipa de resposta DDoS deve compreender cuidadosamente o serviço Azure DDoS Protection Standard. Certifique-se de que a equipa pode identificar e mitigar um ataque coordenando com clientes internos e externos, incluindo a equipa de suporte da Microsoft.

Para a sua equipa de resposta DDoS, recomendamos que utilize exercícios de simulação como parte normal da disponibilidade do seu serviço e planeamento de continuidade. Estes exercícios devem incluir testes à escala.

### <a name="alerts-during-an-attack"></a>Alertas durante um ataque

A Norma de Proteção DDoS Azure identifica e atenua os ataques do DDoS sem qualquer intervenção do utilizador. Para ser notificado quando há uma mitigação ativa para um IP público protegido, pode [configurar um alerta](/azure/virtual-network/ddos-protection-manage-portal) sobre a métrica sob ataque **DDoS ou não**. Pode optar por criar alertas para as outras métricas do DDoS para compreender a escala do ataque, o tráfego a ser retirado e outros detalhes.

#### <a name="when-to-contact-microsoft-support"></a>Quando contactar o suporte da Microsoft

- Durante um ataque DDoS, você descobre que o desempenho do recurso protegido está severamente degradado, ou o recurso não está disponível.

- Acha que o serviço de proteção DDoS não se está a comportar como esperado. 

  O serviço de proteção DDoS só começa a atenuar se a Política de valor métrico **para desencadear a mitigação do DDoS (TCP/TCP SYN/UDP)** for inferior ao tráfego recebido no recurso IP público protegido.

- Está a planear um evento viral que aumentará significativamente o tráfego da sua rede.

- Um ator ameaçou lançar um ataque dDoS contra os seus recursos.

- Se precisar de permitir a lista de uma gama IP ou IP da Norma de Proteção DDoS Azure. Um cenário comum é permitir a lista IP se o tráfego for encaminhado de uma nuvem externa WAF para Azure. 

Para ataques que tenham um impacto crítico no negócio, crie um bilhete de [apoio](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)severity-A .

### <a name="post-attack-steps"></a>Passos pós-ataque

É sempre uma boa estratégia para fazer uma autópsia após um ataque e ajustar a estratégia de resposta do DDoS conforme necessário. Coisas a considerar:

- Houve alguma perturbação no serviço ou na experiência do utilizador por falta de arquitetura escalável?

- Quais as aplicações ou serviços que mais sofreram?

- Quão eficaz foi a estratégia de resposta do DDoS e como pode ser melhorada?

Se suspeitar que está sob ataque dDoS, aumente os seus canais normais de Suporte Azure.

## <a name="ddos-protection-reference-architectures"></a>Arquiteturas de referência de proteção DDoS

A Norma de Proteção DDoS foi concebida [para serviços que são implantados numa rede virtual.](/azure/virtual-network/virtual-network-for-azure-services) Para outros serviços, aplica-se o serviço básico de proteção DDoS predefinido. As seguintes arquiteturas de referência são organizadas por cenários, com padrões de arquitetura agrupados.

### <a name="virtual-machine-windowslinux-workloads"></a>Cargas de trabalho da máquina virtual (Windows/Linux)

#### <a name="application-running-on-load-balanced-vms"></a>Aplicação em execução em VMs equilibrados em carga

Esta arquitetura de referência mostra um conjunto de práticas comprovadas para executar vários VMs do Windows numa escala definida atrás de um equilibrador de carga, para melhorar a disponibilidade e escalabilidade. Esta arquitetura pode ser usada para qualquer carga de trabalho apátrida, como um servidor web.

![Diagrama da arquitetura de referência para uma aplicação em execução em VMs equilibrados em carga](./media/ddos-best-practices/image9.png)

Nesta arquitetura, uma carga de trabalho é distribuída por várias instâncias de VM. Existe um único endereço IP público, e o tráfego de internet é distribuído para os VMs através de um equilibrista de carga. A Norma de Proteção DDoS está ativada na rede virtual do equilibrador de carga Azure (internet) que tem o IP público associado ao mesmo.

O equilibrador de carga distribui pedidos de internet para os casos vm. Os conjuntos de escala de máquinavirtual permitem que o número de VMs seja escalado manualmente, ou automaticamente com base em regras predefinidas. Isto é importante se o recurso estiver sob ataque dDoS. Para mais informações sobre esta arquitetura de referência, consulte [este artigo.](/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm)

#### <a name="application-running-on-windows-n-tier"></a>Aplicação em execução no windows n-tier

Existem várias formas de implementar uma arquitetura de N camadas. O diagrama seguinte mostra uma aplicação web típica de três níveis. Esta arquitetura baseia-se no artigo [Executar VMs equilibrados em carga para escalabilidade e disponibilidade.](/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm) As camadas Web e Business utilizam VMs com balanceamento de carga.

![Diagrama da arquitetura de referência para uma aplicação em execução no windows n-tier](./media/ddos-best-practices/image10.png)

Nesta arquitetura, a DDoS Protection Standard está ativada na rede virtual. Todos os IPs públicos da rede virtual obtêm proteção DDoS para as camadas 3 e 4. Para a proteção da Camada 7, implemente o Gateway de Aplicação no WAF SKU. Para mais informações sobre esta arquitetura de referência, consulte [este artigo.](/azure/architecture/reference-architectures/virtual-machines-windows/n-tier)

#### <a name="paas-web-application"></a>Aplicação web PaaS

Esta arquitetura de referência mostra executar uma aplicação Azure App Service numa única região. Esta arquitetura mostra um conjunto de práticas comprovadas para uma aplicação web que utiliza o [Azure App Service](https://azure.microsoft.com/documentation/services/app-service/) e [a Azure SQL Database.](https://azure.microsoft.com/documentation/services/sql-database/)
Está criada uma região de prontidão para cenários de falhas.

![Diagrama da arquitetura de referência para uma aplicação web PaaS](./media/ddos-best-practices/image11.png)

O Gestor de Tráfego Azure encaminha os pedidos de entrada para o Application Gateway numa das regiões. Durante as operações normais, encaminha os pedidos para o Application Gateway na região ativa. Se essa região ficar indisponível, o Gestor de Tráfego falha na Aplicação Gateway na região de espera.

Todo o tráfego da internet destinado à aplicação web é encaminhado para o [endereço IP público](/azure/application-gateway/application-gateway-web-app-overview) do Application Gateway via Traffic Manager. Neste cenário, o serviço de aplicações (web app) em si não está diretamente virado para o exterior e está protegido pelo Application Gateway. 

Recomendamos que configure o Gateway de aplicação WAF SKU (modo de prevenção) para ajudar a proteger contra ataques da Camada 7 (HTTP/HTTPS/WebSocket). Além disso, as aplicações web são configuradas para aceitar apenas o tráfego a partir do endereço IP [do Gateway de aplicação.](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/)

Para mais informações sobre esta arquitetura de referência, consulte [este artigo.](/azure/architecture/reference-architectures/app-service-web-app/multi-region)

### <a name="mitigation-for-non-web-paas-services"></a>Mitigação de serviços PaaS não web

#### <a name="hdinsight-on-azure"></a>HDInsight em Azure

Esta arquitetura de referência mostra configurar o Padrão de Proteção DDoS para um [cluster Azure HDInsight](/azure/hdinsight/). Certifique-se de que o cluster HDInsight está ligado a uma rede virtual e que a Proteção DDoS está ativada na rede virtual.

!["HDInsight" e "Definições avançadas", com definições de rede virtuais](./media/ddos-best-practices/image12.png)

![Seleção para permitir a Proteção dDoS](./media/ddos-best-practices/image13.png)

Nesta arquitetura, o tráfego destinado ao cluster HDInsight da internet é encaminhado para o IP público associado ao equilíbrio de carga de gateway HDInsight. O equilibrador de carga de gateway envia então o tráfego para os nóóis da cabeça ou os nódos os remos dos trabalhadores diretamente. Uma vez que o Padrão de Proteção DDoS está ativado na rede virtual HDInsight, todos os IPs públicos da rede virtual obtêm proteção DDoS para a Camada 3 e 4. Esta arquitetura de referência pode ser combinada com as arquiteturas de referência n-tier e multi-regiões.

Para obter mais informações sobre esta arquitetura de referência, consulte o [Extend Azure HDInsight utilizando uma](/azure/hdinsight/hdinsight-extend-hadoop-virtual-network?toc=%2fazure%2fvirtual-network%2ftoc.json) documentação da Rede Virtual Azure.


> [!NOTE]
> O Azure App Service Environment for PowerApps ou a gestão de API numa rede virtual com um IP público não são ambos suportados de forma nativa.

## <a name="next-steps"></a>Passos seguintes

* [Responsabilidade partilhada na cloud](shared-responsibility.md)

* [Página de produto de proteção Azure DDoS](https://azure.microsoft.com/services/ddos-protection/)

* [Documentação de proteção de DDoS azure](/azure/virtual-network/ddos-protection-overview)
