---
title: A proteção contra DDoS do Azure, melhores práticas e arquiteturas de referência | Documentos da Microsoft
description: Saiba mais sobre como pode utilizar dados de log para obter informações aprofundadas sobre a sua aplicação.
services: security
author: barclayn
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/06/2018
ms.author: barclayn
ms.openlocfilehash: 11f3dcefd283ada00e915c2d6cb8abf654590ec1
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57247346"
---
# <a name="azure-ddos-protection-best-practices-and-reference-architectures"></a>Proteção contra DDoS do Azure: Melhores práticas e arquiteturas de referência

Este artigo destina-se a decisores de TI e a equipe de segurança. Ele espera que esteja familiarizado com o Azure, redes e segurança.

Conceção para ataques denial of service (DDoS) resiliência requer planear e estruturar para uma variedade de modos de falha. Este artigo fornece as melhores práticas para criar aplicações no Azure para resiliência contra ataques DDoS.

## <a name="types-of-attacks"></a>Tipos de ataques

O DDoS é um tipo de ataque que tenta a esgotar os recursos de aplicativos. O objetivo é afetar a disponibilidade da aplicação e a sua capacidade de processar pedidos legítimos. Ataques estão se tornando mais sofisticados e maior tamanho e o impacto. Os ataques de DDoS podem ser direcionadas para qualquer ponto final que esteja publicamente acessível através da internet.

O Azure fornece proteção contínua contra ataques DDoS. Esta proteção está integrada na plataforma do Azure por predefinição e não custos adicionais. 

Além de proteção contra DDoS na plataforma do núcleo [Standard do Azure DDoS Protection](https://azure.microsoft.com/services/ddos-protection/) fornece capacidades avançadas de mitigação de DDoS contra ataques de rede. Ele automaticamente é ajustado para proteger os seus recursos do Azure específicos. Proteção é simples para ativar durante a criação de novas redes virtuais. Ele também pode ser feito após a criação e exige sem alterações de aplicação ou recurso.

![A função do Azure DDoS Protection na proteção dos clientes e uma rede virtual de um atacante](media/azure-ddos-best-practices/image1.png)

Ataques de DDoS podem ser classificados em três categorias: volumetric, protocolo e recursos.

### <a name="volumetric-attacks"></a>Ataques volumetric

Ataques volumetric são o tipo mais comum de ataque de DDoS. Volumetric ataques estão se força bruta que visam as camadas de transporte e de rede. Eles tentam esgotar os recursos, como ligações de rede. 

Esses ataques utilizam frequentemente vários sistemas infectados para inundar as camadas de rede com tráfego aparentemente legítimo. Eles usam os protocolos de camada de rede, como o controle de mensagem ICMP (Internet Protocol), o protocolo UDP (User Datagram) e o protocolo de controlo de transmissão (TCP).

Os ataques de DDoS de uso mais comum de camada de rede são SYN TCP inundação de eco ICMP, UDP inundação, DNS, e ataques de amplificação de NTP. Este tipo de ataque pode servir-se não só para interromper o serviço, mas também como um smokescreen detalhado de intrusão de rede mais possui conteúdo nefasto e direcionadas. Um exemplo de um ataque de volumetric recente é o [Memcached exploração](https://www.wired.com/story/github-ddos-memcached/) que estava a afectar GitHub. Este ataque direcionado a porta UDP 11211 e gerado 1.35 Tb/s do volume de ataque.

### <a name="protocol-attacks"></a>Ataques de protocolo

Protocolo de ataques de protocolos de aplicação de destino. Eles tentam utilizar todos os recursos disponíveis em dispositivos de infraestrutura como firewalls, servidores de aplicações e Balanceadores de carga. Ataques de protocolo utilizam pacotes de que tem um formato incorreto ou contêm anomalias de protocolo. Operam estes ataques através do envio de um grande número de pedidos abertos que servidores e outros dispositivos de comunicação respondem e aguardar por uma resposta de pacote. O destino tenta responder aos pedidos abertos, eventualmente, fazendo com que a falha no sistema.

O exemplo mais comum de um ataque de DDoS com base no protocolo é inundação de TCP SYN. Neste ataque, uma seqüência de pedidos de TCP SYN tenta a sobrecarregar um destino. O objetivo é fazer com que o destino não responde. A falha de Dyn de 2016, além de ser um ataque de camada de aplicativo, consistiu em TCP SYN floods essa porta de destino 53 dos servidores DNS do Dyn.

### <a name="resource-attacks"></a>Ataques de recursos

Ataques de recurso de destino da camada de aplicativo. Eles acionam processos de back-end num esforço para sobrecarregar um sistema. Recurso de ataques de tráfego de abuso que procura normal, mas que estiver a consulta de intensiva da CPU no servidor. O volume de tráfego necessário a esgotar os recursos é menor do que de outro tipo de ataques. O tráfego num ataque de recursos é indissociável do tráfego legítimo, tornando difícil de detetar. Os ataques de recursos mais comuns são em HTTP/HTTPS e serviços DNS.

## <a name="shared-responsibility-in-the-cloud"></a>Responsabilidade partilhada na nuvem

Uma estratégia de defesa em profundidade ajuda a combater a variedade cada vez maior e a sofisticação dos ataques. A segurança é uma responsabilidade partilhada entre o cliente e a Microsoft. A Microsoft chama isso uma [modelo de responsabilidade partilhada](https://azure.microsoft.com/blog/microsoft-incident-response-and-shared-responsibility-for-cloud-computing/). A figura seguinte mostra esta divisão de responsabilidade:

![Responsabilidades do cliente e do Azure](media/azure-ddos-best-practices/image2.png)

Os clientes do Azure beneficiam de rever as melhores práticas da Microsoft e globalmente a criação de aplicativos que são criados e testados para falha distribuídos.

## <a name="fundamental-best-practices"></a>Práticas recomendadas fundamentais

As secções seguintes fornecem uma orientação prescritiva para criar serviços DDoS resilientes no Azure.

### <a name="design-for-security"></a>Conceber para segurança

Certifique-se de que a segurança é uma prioridade ao longo de todo o ciclo de vida de um aplicativo, desde a conceção e implementação para implantação e operações. As aplicações podem ter bugs que permitem que um volume relativamente baixo de pedidos para utilizar uma enorme quantidade de recursos, resultando numa indisponibilidade do serviço. 

Para ajudar a proteger um serviço em execução no Microsoft Azure, deve ter uma boa compreensão da sua arquitetura de aplicativo e enfocar o [cinco pilares de qualidade de software](https://docs.microsoft.com/azure/architecture/guide/pillars).
Deve saber volumes de tráfego típico, o modelo de conectividade entre a aplicação e outras aplicações e os pontos finais de serviço são expostos à Internet pública.

É mais importante garantir que um aplicativo é resiliente para lidar com uma negação de serviço que é direcionada para a própria aplicação. Segurança e a privacidade são incorporadas na plataforma do Azure, começando com o [Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl/default.aspx). O SDL aborda a segurança em todas as fases de desenvolvimento e garante que o Azure é continuamente atualizado para que seja ainda mais seguro.

### <a name="design-for-scalability"></a>Conceção para escalabilidade

A escalabilidade é a eficiência com que um sistema pode processar o aumento de carga. Deve conceber as suas aplicações para [Dimensionar horizontalmente](https://docs.microsoft.com/azure/architecture/guide/design-principles/scale-out) para satisfazer a procura de uma carga amplified, especificamente no caso de um ataque DDoS. Se seu aplicativo depende de uma única instância de um serviço, ele cria um ponto único de falha. Várias instâncias de aprovisionamento torna o seu sistema, mais escalonável e mais resiliente.

Para [App Service do Azure](../app-service/app-service-value-prop-what-is.md), selecione um [plano do App Service](../app-service/overview-hosting-plans.md) que oferece várias instâncias. Serviços Cloud do Azure, configure cada uma das suas funções para utilizar [várias instâncias](../cloud-services/cloud-services-choose-me.md). Para [máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-about/?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), certifique-se de que a sua arquitetura de máquina virtual (VM) inclui mais de uma VM e que cada VM está incluído num [conjunto de disponibilidade](../virtual-machines/virtual-machines-windows-manage-availability.md). Recomendamos que utilize [conjuntos de dimensionamento de máquinas virtuais](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) para capacidades de dimensionamento automático.

### <a name="defense-in-depth"></a>Defesa em profundidade

É a idéia por trás de defesa em profundidade gerir riscos com diversas estratégias de defesa. Colocação de defesas de segurança num aplicativo reduz a chance de um ataque bem-sucedido. Recomendamos que implementar designs seguros para as suas aplicações ao utilizar as capacidades incorporadas da plataforma do Azure.

Por exemplo, o risco de ataque aumenta com o tamanho (*uma área de superfície*) do aplicativo. Pode reduzir a área de superfície ao utilizar listas de permissões para fechar o espaço de endereços IP exposto e escutar as portas que não são necessários em balanceadores de carga ([Azure Load Balancer](../load-balancer/load-balancer-get-started-internet-portal.md) e [oGatewaydeaplicaçãodoAzure](../application-gateway/application-gateway-create-probe-portal.md)). [Grupos de segurança (NSGs) de rede](../virtual-network/security-overview.md) são outra maneira de reduzir a superfície de ataque.
Pode usar [etiquetas de serviço](../virtual-network/security-overview.md#service-tags) e [grupos de segurança de aplicativo](../virtual-network/security-overview.md#application-security-groups) para minimizar a complexidade para criar as regras de segurança e configuração de segurança de rede, como uma extensão natural da estrutura de uma aplicação.

Deve implantar os serviços do Azure numa [rede virtual](../virtual-network/virtual-networks-overview.md) sempre que possível. Esta prática permite que os recursos de serviço para comunicar através de endereços IP privados. Por predefinição, o tráfego de serviço do Azure de uma rede virtual utiliza endereços IP públicos como endereços IP de origem. Usando [pontos finais de serviço](../virtual-network/virtual-network-service-endpoints-overview.md) irá mudar o tráfego de serviço para utilizar endereços privados da rede virtual como endereços IP de origem, quando estão a aceder ao serviço do Azure de uma rede virtual.

Vemos com freqüência dos clientes recursos no local ataques juntamente com seus recursos no Azure. Se estiver a ligar a um ambiente no local para o Azure, recomendamos que minimizar a exposição dos recursos no local para a internet pública. Pode utilizar o dimensionamento e capacidades avançadas de proteção contra DDoS do Azure ao implementar as entidades públicas bem conhecidas no Azure. Uma vez que estas entidades acessíveis ao público, muitas vezes, são um alvo de ataques de DDoS, colocando-os no Azure reduz o impacto nos seus recursos no local.

## <a name="azure-offerings-for-ddos-protection"></a>Ofertas do Azure para proteção contra DDoS

O Azure tem duas ofertas de serviço de DDoS que fornecem proteção contra ataques à rede (camada 3 e 4): Básico de proteção de DDoS e norma de proteção DDoS. 

### <a name="ddos-protection-basic"></a>Basic de proteção DDoS

Proteção básica é integrada do Azure por predefinição sem custos adicionais. O dimensionamento e a capacidade da rede implementada globalmente do Azure fornece defesa contra ataques comuns de camada de rede por meio de atenuação de monitorização e em tempo real de tráfego sempre ativa. DDoS Protection básica requer sem alterações de configuração ou aplicação do utilizador. Básico do DDoS Protection ajuda a proteger todos os serviços do Azure, incluindo serviços de PaaS como o DNS do Azure.

![Mapear a representação de rede do Azure, com o texto "Global DDoS atenuação presença" e "Levando a capacidade de mitigação de DDoS"](media/azure-ddos-best-practices/image3.png)

Proteção contra DDoS básica no Azure consiste em componentes de software e hardware. Um plano de controlo de software decide quando, onde, e que tipo de tráfego deve ser steered através de dispositivos de hardware que analisem e remover o tráfego de ataque. O plano de controlo torna essa decisão com base numa infraestrutura de toda a proteção contra DDoS *política*. Esta política é definida estaticamente e aplicada universalmente para todos os clientes do Azure.

Por exemplo, a política de proteção contra DDoS Especifica em qual o volume que o tráfego deve ser a proteção *acionada.* (Ou seja, o tráfego do inquilino deve ser encaminhado através de aparelhos de limpeza.) A política, em seguida, especifica como os dispositivos de limpeza devem *mitigar* o ataque.

O serviço Azure DDoS Protection básico é indicado para proteção da infraestrutura e a proteção da plataforma do Azure. Ele atenua o tráfego quando ele exceder uma taxa de tão significativa que poderá afetar vários clientes num ambiente multi-inquilino. Ele não fornece alertas ou políticas de personalizada de cada cliente.

### <a name="ddos-protection-standard"></a>Norma de proteção DDoS

Proteção padrão fornece recursos aprimorados de mitigação de DDoS. Ele é automaticamente ajustado para ajudar a proteger os recursos específicos do Azure numa rede virtual. Proteção é simples para ativar a qualquer rede virtual nova ou existente e requer sem alterações de aplicação ou recurso. Ele tem algumas vantagens em relação a serviço básico, incluindo o Registro em log, alertas e telemetria. As secções seguintes descrevem os principais recursos do serviço Azure DDoS Protection padrão.

#### <a name="adaptive-real-time-tuning"></a>Otimização adaptável em tempo real

O serviço Azure DDoS Protection básico ajuda a proteger os clientes e evitar impactos para outros clientes. Por exemplo, se um serviço é aprovisionado para um volume típico legítimo do tráfego de entrada que é menor do que o *taxa de Acionador* da política de proteção contra DDoS em toda a infraestrutura, pode ir um ataque de DDoS em recursos do cliente despercebidas. Geralmente, a complexidade dos ataques recentes (por exemplo, multivetorial DDoS) e os comportamentos específicos de aplicativo de inquilinos chamam para cada cliente, as políticas de proteção personalizada. O serviço realiza essa personalização através de duas informações:

- Aprendizagem automática de padrões de tráfego de cada cliente (por IP) de camada 3 e 4.

- Minimização de falsos positivos, Considerando que a escala do Azure permite que ele absorver uma quantidade significativa de tráfego.

![Diagrama de como DDoS Protection padrão funciona, com "Geração de política" circuladas](media/azure-ddos-best-practices/image5.png)

#### <a name="ddos-protection-telemetry-monitoring-and-alerting"></a>Telemetria de proteção contra DDoS, monitorização e alertas

Padrão de proteção de DDoS expõe telemetria avançada através de [do Azure Monitor](../azure-monitor/overview.md) durante o período de um ataque DDoS. Pode configurar alertas para qualquer uma das métricas do Azure Monitor que utiliza a proteção contra DDoS. Pode integrar o Registro em log com Splunk (Hubs de eventos do Azure), os registos do Azure Monitor e o armazenamento do Azure para análise avançada através da interface de diagnóstico de Monitor do Azure.

##### <a name="ddos-mitigation-policies"></a>Políticas de mitigação de DDoS

No portal do Azure, selecione **Monitor** > **métricas**. Na **métricas** painel, selecione o grupo de recursos, selecione um tipo de recurso de **endereço IP público**e selecione o seu endereço IP público do Azure. Métricas do DDoS são visíveis no **métricas disponíveis** painel.

Padrão de proteção de DDoS aplica-se três políticas de atenuação otimizados automaticamente (TCP SYN, TCP e UDP) para cada IP público do recurso protegido, na rede virtual que tenha o DDoS ativada. Pode ver os limiares de política ao selecionar a métrica **pacotes para acionar a mitigação de DDoS de entrada**.

![Gráfico de métricas e de métricas disponíveis](media/azure-ddos-best-practices/image7.png)

Os limiares de política são configurados automaticamente através de perfis de tráfego de rede baseados em aprendizagem do máquina. Mitigação de DDoS ocorre para um endereço IP sob ataque apenas quando é excedido o limiar de política.

##### <a name="metric-for-an-ip-address-under-ddos-attack"></a>Métrica para um endereço IP sob ataque de DDoS

Se o endereço IP público está sob ataque, o valor para a métrica **DDoS sob ataque ou não** é alterado para 1 como proteção contra DDoS executa atenuação sobre o tráfego de ataque.

![Gráfico e a métrica "Em DDoS ataques ou não"](media/azure-ddos-best-practices/image8.png)

Recomendamos configurar um alerta sobre esta métrica. Em seguida, será notificado quando houver uma mitigação de DDoS Active Directory executada no seu endereço IP público.

Para obter mais informações, consulte [gerir padrão do Azure DDoS Protection através do portal do Azure](../virtual-network/ddos-protection-manage-portal.md).

#### <a name="web-application-firewall-for-resource-attacks"></a>Firewall de aplicações Web de ataques de recursos

Especificamente para ataques de recursos na camada da aplicação, deve configurar uma firewall de aplicações web (WAF) para ajudar a proteger as aplicações web. Uma WAF inspeciona o tráfego de web de entrada para bloquear injeções de SQL, scripts, DDoS e outros ataques de camada 7 entre sites. O Azure disponibiliza [WAF como uma funcionalidade do Gateway de aplicação](../application-gateway/application-gateway-web-application-firewall-overview.md) para proteção centralizada das suas aplicações web de exploits e vulnerabilidades comuns. Existem outras ofertas de WAF de parceiros do Azure que poderão ser mais adequados para as suas necessidades através de [do Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?search=WAF&page=1).

Até mesmo firewalls de aplicações web são suscetíveis a ataques de esgotamento de volumetric e estado. Recomendamos vivamente que ative DDoS Protection padrão na rede virtual para ajudar a proteger contra volumetric WAF e ataques de protocolo. Para obter mais informações, consulte a [arquiteturas de referência de proteção contra DDoS](#ddos-protection-reference-architectures) secção.

### <a name="protection-planning"></a>Planear a proteção

Planejamento e preparação são cruciais para compreender como um sistema realizará durante um ataque DDoS. A criação de um plano de resposta de gestão de incidentes é a parte deste esforço.

Se tiver o padrão de proteção de DDoS, certifique-se de que está ativada na rede virtual de pontos finais de acesso à internet. Configurar alertas de DDoS ajuda-o a ver constantemente quaisquer potenciais ataques na sua infraestrutura. 

Deve monitorar seus aplicativos de forma independente. Compreenda o comportamento normal de um aplicativo. Prepare para funcionar se a aplicação não está se comportando como esperado durante um ataque DDoS.

#### <a name="testing-through-simulations"></a>Teste por meio de simulações

É uma boa prática para testar suas suposições sobre como os seus serviços irão responder a um ataque realizando simulações periódicas. Durante os testes, valide que seus serviços ou aplicações continuam a funcionar conforme esperado e não existe nenhuma interrupção para a experiência do usuário. Identificar lacunas de ponto de vista de tecnologia e de processo e incorporá-los a estratégia de resposta de DDoS. Recomendamos que realizar esses testes em ambientes de teste ou durante o horário de pico para minimizar o impacto no ambiente de produção.

Podemos ter uma parceria com a [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) para criar uma interface onde os clientes do Azure podem gerar tráfego em relação a ativar a proteção de DDoS pontos finais públicos para simulações. Pode utilizar o [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) simulação para:

- Valide a como o Azure DDoS Protection ajuda a proteger os seus recursos do Azure de ataques de DDoS.

- Otimize seu processo de resposta a incidentes mesmo sob ataque de DDoS.

- Documente a conformidade de DDoS.

- Prepare as suas equipes de segurança de rede.

Cibersegurança requer constante inovação em defesa. A proteção padrão DDoS do Azure é um-de-última geração da oferta com uma solução eficaz para mitigar ataques de DDoS cada vez mais complexos.

## <a name="components-of-a-ddos-response-strategy"></a>Componentes de uma estratégia de resposta de DDoS

Um ataque de DDoS que tenha como destino de recursos do Azure, normalmente, requer intervenção mínima de um ponto de vista do utilizador. Ainda assim, incorporando DDoS atenuação como parte de uma estratégia de resposta a incidentes ajuda a minimizar o impacto para a continuidade do negócio.

### <a name="microsoft-threat-intelligence"></a>Informação de ameaças da Microsoft

A Microsoft tem uma rede de inteligência de ameaças extensa. Esta rede utiliza o conhecimento coletivo de uma Comunidade de segurança expandida que oferece suporte a serviços online da Microsoft, parceiros da Microsoft e relações na Comunidade de segurança de internet. 

Como fornecedor de infraestrutura crítica, a Microsoft recebe avisos iniciais sobre ameaças. A Microsoft coleta informações sobre ameaças de seus serviços online e de sua base de clientes global. Microsoft incorpora todas as informações de ameaças volta para os produtos da proteção contra DDoS do Azure.

Além disso, a unidade Microsoft Crimes digitais (DCU) executa estratégias ofensivas contra botnets. Botnets são uma fonte comum de comando e controlo de ataques de DDoS.

### <a name="risk-evaluation-of-your-azure-resources"></a>Avaliação de risco dos seus recursos do Azure

É fundamental para compreender o escopo do seu risco de um ataque de DDoS de forma contínua. Periodicamente se pergunte: 
- Quais novos recursos do Azure disponíveis ao público precisam de proteção?

- Existe um ponto único de falha no serviço? 

- Como podem serviços ser isolados para limitar o impacto de um ataque enquanto ainda disponibilizar serviços aos clientes válidos?

- Existem redes virtuais em que DDoS Protection padrão deve ser ativada, mas não é? 

- São meus serviços ativo/ativo com a ativação pós-falha em várias regiões?

### <a name="customer-ddos-response-team"></a>Equipa de resposta do cliente DDoS

A criação de uma equipe de resposta de DDoS é uma etapa importante na resposta a um ataque de maneira rápida e eficaz. Identifica contatos na sua organização que irá supervisionam planejamento e execução. Esta equipe de resposta de DDoS deve compreender o serviço Azure DDoS Protection padrão. Certifique-se de que a equipe pode identificar e mitigar um ataque através da coordenação com clientes internos e externos, incluindo a equipa de suporte da Microsoft.

Para a sua equipa de resposta de DDoS, recomendamos que utilize exercícios de simulação como uma parte normal da sua disponibilidade do serviço e o planejamento da continuidade dos. Esses exercícios devem incluir testes de dimensionamento.

### <a name="alerts-during-an-attack"></a>Alertas durante um ataque

Padrão de proteção de DDoS do Azure identifica e atenua os ataques de DDoS sem qualquer intervenção do utilizador. Para ser notificado quando houver uma redução de Active Directory para um IP público protegido, pode [configurar um alerta](../virtual-network/ddos-protection-manage-portal.md) sobre a métrica **DDoS sob ataque ou não**. Pode optar por criar alertas para as outras métricas de DDoS compreender a escala do ataque, o tráfego a ser removido e outros detalhes.

#### <a name="when-to-contact-microsoft-support"></a>Quando contactar o suporte da Microsoft

- Durante um ataque de DDoS, constatar que o desempenho do recurso protegido é gravemente degradado ou o recurso não está disponível.

- Acha que o serviço de proteção contra DDoS não está se comportando como esperado. 

  O serviço de proteção contra DDoS é iniciado a atenuação apenas se o valor da métrica **política para acionar a mitigação de DDoS (SYN TCP/TCP/UDP)** é menor do que o tráfego recebido no recurso de IP público protegido.

- Estiver a planear um evento viral, que irá aumentar significativamente seu tráfego de rede.

- Um ator tem ameaçado lançar um ataque de DDoS em relação aos recursos.

Para ataques que tem um impacto comercial crítico, crie uma gravidade-A [pedido de suporte](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

### <a name="post-attack-steps"></a>Passos de pós-ataques

É sempre uma boa estratégia para fazer um postmortem após um ataque e ajustar a estratégia de resposta de DDoS, conforme necessário. Aspetos a considerar:

- Foi lá qualquer interrupção para o utilizador ou experiência devido à falta de arquitetura escalável?

- As aplicações ou serviços foram afetados por mais?

- A eficiência, foi a estratégia de resposta de DDoS e como pode ser melhorada?

Se suspeitar que tem menos de um ataque de DDoS, encaminhar através de seus canais de suporte do Azure normais.

## <a name="ddos-protection-reference-architectures"></a>Arquiteturas de referência de proteção contra DDoS

Proteção de DDoS Standard foi concebido [para os serviços que são implementados numa rede virtual](../virtual-network/virtual-network-for-azure-services.md). Para outros serviços, aplica-se o serviço de DDoS Protection básicas do padrão. As seguintes arquiteturas de referência estão organizadas por cenários, com os padrões de arquitetura agrupados em conjunto.

### <a name="virtual-machine-windowslinux-workloads"></a>Cargas de trabalho de máquina virtual (Windows/Linux)

#### <a name="application-running-on-load-balanced-vms"></a>Aplicação em execução em VMs com balanceamento de carga

Esta arquitetura de referência mostra um conjunto de práticas comprovadas para executar várias VMs do Windows num conjunto de dimensionamento atrás de um balanceador de carga, para melhorar a disponibilidade e escalabilidade. Esta arquitetura pode ser utilizada para qualquer carga de trabalho sem monitorização de estado, como um servidor web.

![Diagrama de arquitetura de referência para uma aplicação em execução em VMs com balanceamento de carga](media/azure-ddos-best-practices/image9.png)

Nesta arquitetura, uma carga de trabalho é distribuída por várias instâncias de VM. Há um único endereço IP público e o tráfego de internet é distribuído para as VMs através de um balanceador de carga. Padrão de proteção contra DDoS está ativada na rede virtual do Balanceador de carga do Azure (internet) que tem o IP público associado ao mesmo.

O Balanceador de carga distribui pedidos recebidos da internet para as instâncias de VM. Os conjuntos de dimensionamento de máquinas virtuais permitem que o número de VMs para ser aumentadas ou reduzidas horizontalmente manual ou automaticamente com base em regras predefinidas. Isso é importante se o recurso está sob ataque de DDoS. Para obter mais informações sobre esta arquitetura de referência, consulte [este artigo](https://docs.microsoft.com/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm).

#### <a name="application-running-on-windows-n-tier"></a>Aplicação em execução no Windows de N camadas

Existem várias formas de implementar uma arquitetura de N camadas. O diagrama seguinte mostra um aplicativo web de três camadas típica. Esta arquitetura baseia-se no artigo [executar VMs com balanceamento de carga para escalabilidade e disponibilidade](https://docs.microsoft.com/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm). As camadas Web e Business utilizam VMs com balanceamento de carga.

![Diagrama de arquitetura de referência para uma aplicação em execução no Windows de N camadas](media/azure-ddos-best-practices/image10.png)

Nesta arquitetura, padrão de proteção contra DDoS está ativada na rede virtual. Todos os IPs públicos na rede virtual obter proteção contra DDoS de camada 3 e 4. Para a proteção de camada 7, implemente o Gateway de aplicação no WAF SKU. Para obter mais informações sobre esta arquitetura de referência, consulte [este artigo](https://docs.microsoft.com/azure/architecture/reference-architectures/virtual-machines-windows/n-tier).

#### <a name="paas-web-application"></a>Aplicação web PaaS

Esta arquitetura de referência mostra a execução de uma aplicação de serviço de aplicações do Azure numa única região. Esta arquitetura mostra um conjunto de práticas comprovadas para uma aplicação web que utiliza [App Service do Azure](https://azure.microsoft.com/documentation/services/app-service/) e [base de dados do Azure SQL](https://azure.microsoft.com/documentation/services/sql-database/).
Uma região em modo de espera é configurada para cenários de ativação pós-falha.

![Diagrama de arquitetura de referência para um aplicativo da web de PaaS](media/azure-ddos-best-practices/image11.png)

O Gestor de tráfego do Azure encaminha os pedidos recebidos para o Gateway de aplicação de uma das regiões. Durante as operações normais, este encaminha os pedidos para o Gateway de aplicação na região de Active Directory. Se esta região ficar indisponível, o Gestor de tráfego efetuará ao longo do gateway de aplicação na região em espera.

Todo o tráfego da internet destinado ao aplicativo web é encaminhado para o [endereço IP público do Gateway de aplicação](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview) através do Gestor de tráfego. Neste cenário, o serviço de aplicações (aplicação web) em si não é diretamente externamente com acesso à e está protegida pelo Gateway de aplicação. 

Recomendamos que configure o SKU de WAF do Gateway de aplicação (modo de impedir) para ajudar a proteger contra ataques de camada 7 (HTTP/HTTPS/WebSocket). Além disso, as aplicações web estão configuradas para [aceite apenas o tráfego de Gateway de aplicação](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/) endereço IP.

Para obter mais informações sobre esta arquitetura de referência, consulte [este artigo](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region).

### <a name="mitigation-for-non-web-paas-services"></a>Atenuação para serviços de PaaS não web

#### <a name="hdinsight-on-azure"></a>HDInsight no Azure

Esta arquitetura de referência mostra a configuração de DDoS Protection Standard para uma [cluster Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/). Certifique-se de que o cluster do HDInsight está ligado a uma rede virtual e que a proteção contra DDoS está ativada na rede virtual.

!["HDInsight" e "Definições avançadas" painéis, com definições de rede virtual](media/azure-ddos-best-practices/image12.png)

![Seleção para ativar a proteção contra DDoS](media/azure-ddos-best-practices/image13.png)

Nesta arquitetura, o tráfego destinado ao cluster do HDInsight a partir da internet é encaminhado para o IP público associado ao balanceador de carga de gateway do HDInsight. O Balanceador de carga de gateway, em seguida, envia o tráfego para os nós principais ou nós de trabalho diretamente. Porque o padrão de proteção contra DDoS está ativada na rede virtual do HDInsight, todos os IPs públicos na rede virtual obter proteção contra DDoS de camada 3 e 4. Esta arquitetura de referência pode ser combinada com as camadas e arquiteturas de referência de várias regiões.

Para obter mais informações sobre esta arquitetura de referência, consulte a [expandir o Azure HDInsight com uma rede Virtual do Azure](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network?toc=%2fazure%2fvirtual-network%2ftoc.json) documentação.


> [!NOTE]
> Azure ambiente de serviço de aplicações do PowerApps ou a API management numa rede virtual com um IP público são ambos não suportados nativamente.

## <a name="next-steps"></a>Passos Seguintes

* [Página de produto de proteção contra DDoS do Azure](https://azure.microsoft.com/services/ddos-protection/)

* [Blogue de proteção contra DDoS do Azure](https://aka.ms/ddosblog)

* [Documentação de proteção contra DDoS do Azure](../virtual-network/ddos-protection-overview.md)
