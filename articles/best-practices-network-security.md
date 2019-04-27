---
title: Práticas recomendadas de segurança de rede do Azure | Documentos da Microsoft
description: Conheça alguns dos principais recursos disponíveis no Azure para ajudar a criar ambientes de rede seguros
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: ''
ms.assetid: d169387a-1243-4867-a602-01d6f2d8a2a1
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/03/2017
ms.author: jonor
ms.openlocfilehash: 74a46c7788b0a0dc729ab977489ed6d97a387a6e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60619530"
---
# <a name="microsoft-cloud-services-and-network-security"></a>Microsoft cloud services e segurança de rede
Serviços cloud da Microsoft oferecem serviços de hiper escala e infraestrutura, capacidades de nível empresarial e muitas opções de conectividade híbrida. Os clientes podem optar por aceder a estes serviços através da Internet ou com o Azure ExpressRoute, que fornece conectividade de rede privada. A plataforma Microsoft Azure permite aos clientes expandir sua infra-estrutura para a cloud e crie arquiteturas de várias camadas de forma totalmente integrada. Além disso, terceiros pode ativar recursos aprimorados, oferecendo serviços de segurança e de aplicações virtuais. Este white paper fornece uma descrição geral da segurança e problemas arquitetônicos que os clientes devem considerar ao utilizar serviços cloud da Microsoft acedidos através do ExpressRoute. Ele também inclui a criação de serviços mais seguros em redes virtuais do Azure.

## <a name="fast-start"></a>Início rápido
A tabela a seguir lógica pode direcioná-lo para um exemplo específico das muitas técnicas de segurança disponíveis com a plataforma do Azure. Para referência rápida, encontre o exemplo que melhor se adequa a seu caso. Para obter explicações expandidas, continue a ler o documento.
[![0]][0]

[Exemplo 1: Crie uma rede de perímetro (também conhecida como DMZ, zona desmilitarizada ou sub-rede filtrada) para ajudar a proteger aplicações com grupos de segurança de rede (NSGs).](#example-1-build-a-perimeter-network-to-help-protect-applications-with-nsgs)</br>
[Exemplo 2: Crie uma rede de perímetro para ajudar a proteger aplicações com um firewall e NSGs.](#example-2-build-a-perimeter-network-to-help-protect-applications-with-a-firewall-and-nsgs)</br>
[Exemplo 3: Crie uma rede de perímetro para ajudar a proteger redes com uma firewall, a rota definida pelo utilizador (UDR) e o NSG.](#example-3-build-a-perimeter-network-to-help-protect-networks-with-a-firewall-and-udr-and-nsg)</br>
[Exemplo 4: Adicione uma ligação híbrida com uma rede privada virtual (VPN) de aplicação virtual de site a site.](#example-4-add-a-hybrid-connection-with-a-site-to-site-virtual-appliance-vpn)</br>
[Exemplo 5: Adicione uma ligação híbrida com um gateway de VPN do Azure de site a site.](#example-5-add-a-hybrid-connection-with-a-site-to-site-azure-vpn-gateway)</br>
[Exemplo 6: Adicione uma ligação híbrida com o ExpressRoute.](#example-6-add-a-hybrid-connection-with-expressroute)</br>
Exemplos para adicionar ligações entre redes virtuais, a elevada disponibilidade e o encadeamento de serviços serão adicionados a este documento sobre os próximos meses.

## <a name="microsoft-compliance-and-infrastructure-protection"></a>Proteção de conformidade e a infraestrutura da Microsoft
Para ajudar as organizações a cumprir os requisitos da nacionais, regionais e específicos da indústria que regem a recolha e utilização de dados dos indivíduos, a Microsoft oferece mais de 40 certificações e atestados. O conjunto mais abrangente de qualquer fornecedor de serviços cloud.

Para obter mais informações, consulte as informações de conformidade na [Microsoft Trust Center][TrustCenter].

A Microsoft tem uma abordagem abrangente para proteger a infraestrutura de nuvem necessária para executar serviços de hiper escala global. Infraestrutura de nuvem da Microsoft inclui hardware, software, redes e administrativas e equipe de operações, além de centros de dados físicos.

![2]

Esta abordagem fornece uma base mais segura para os clientes implementar os serviços na cloud da Microsoft. A próxima etapa é para os clientes projetar e criar uma arquitetura de segurança para proteger estes serviços.

## <a name="traditional-security-architectures-and-perimeter-networks"></a>Arquiteturas de segurança tradicionais e redes de perímetro
Embora a Microsoft investe intensamente a proteger a infraestrutura de nuvem, os clientes também terá de proteger seus serviços cloud e os grupos de recursos. Uma abordagem de várias camadas para segurança fornece o melhor defesa. Uma zona de segurança de rede de perímetro protege os recursos da rede interna de uma rede não fidedigna. Uma rede de perímetro refere-se para as margens ou partes da rede que ficam entre a Internet e a infraestrutura de TI de empresa protegida.

Em redes de empresa comum, a infraestrutura principal intensamente é fortified em perímetros, com várias camadas de dispositivos de segurança. O limite de cada camada consiste em dispositivos e os pontos de imposição de política. Cada camada pode incluir uma combinação dos seguintes dispositivos de segurança de rede: firewalls, Denial of Service (DoS) prevenção, detecção de intrusão ou sistemas de proteção (IDS/IPS) e dispositivos VPN. Imposição de política pode assumir a forma de políticas de firewall, listas de controlo de acesso (ACLs) ou de encaminhamento específico. A primeira linha de defesa da rede, diretamente a aceitar o tráfego de entrada da Internet, é uma combinação desses mecanismos para ataques de bloco e tráfego prejudicial enquanto permite que os pedidos legítimos ainda mais na rede. Este tráfego encaminha-se diretamente aos recursos na rede de perímetro. Esse recurso pode, em seguida, "conversar" com os recursos mais profundo na rede de trânsito do limite seguinte para validação do primeiro. A camada mais externa denomina-se a rede de perímetro porque esta parte da rede é exposto à Internet, geralmente com alguma forma de proteção em ambos os lados. A figura a seguir mostra um exemplo de uma rede de perímetro única sub-rede numa rede empresarial, com dois limites de segurança.

![3]

Existem muitas arquiteturas usadas para implementar uma rede de perímetro. Estas arquiteturas de podem variar de um balanceador de carga simples a uma rede de perímetro de múltiplas sub-redes com mecanismos de variadas em cada limite para bloquear o tráfego e proteger as camadas mais profundas da rede empresarial. Como é criada a rede de perímetro depende as necessidades específicas da organização e sua tolerância ao risco geral.

Como os clientes mover as cargas de trabalho para clouds públicas, é fundamental para suportar recursos semelhantes para arquitetura de rede de perímetro no Azure para cumprir os requisitos de segurança e de conformidade. Este documento fornece diretrizes sobre como os clientes podem criar um ambiente de rede segura no Azure. Ele se concentra na rede de perímetro, mas também inclui uma discussão abrangente sobre muitos aspectos de segurança de rede. As seguintes perguntas informam essa discussão:

* Como pode ser criada numa rede de perímetro no Azure?
* Quais são algumas das funcionalidades do Azure disponíveis para criar a rede de perímetro?
* Como as cargas de trabalho de back-end podem ser protegidas?
* Como são controladas comunicações de Internet para as cargas de trabalho no Azure?
* Como é possível proteger as redes no local de implementações no Azure?
* Quando devem ser utilizados os recursos de segurança do Azure nativo versus serviços ou aplicações de terceiros?

O diagrama seguinte mostra várias camadas de segurança que o Azure fornece aos clientes. Essas camadas são nativo na própria plataforma do Azure e os recursos definidos pelo cliente:

![4]

Entrada da Internet, DDoS do Azure ajuda a proteger contra ataques em grande escala no Azure. A próxima camada é definida pelo cliente endereços IP públicos (pontos de extremidade), que são utilizados para determinar qual o tráfego pode passar através do serviço de nuvem para a rede virtual. Nativo virtuais do Azure garante de isolamento de rede completo isolamento de todas as outras redes e que o tráfego apenas flui através de caminhos de utilizador configurado e métodos. Estes caminhos e os métodos são a próxima camada, onde os NSGs, UDR e aplicações virtuais de rede podem ser utilizadas para criar limites de segurança para proteger as implementações de aplicações na rede protegida.

A secção seguinte fornece uma descrição geral das redes virtuais do Azure. Nestas redes virtuais são criadas por clientes e são o que as cargas de trabalho implementadas estão ligadas a. Redes virtuais são a base de todas as funcionalidades de segurança de rede necessárias para estabelecer uma rede de perímetro para proteger as implementações do cliente no Azure.

## <a name="overview-of-azure-virtual-networks"></a>Descrição geral das redes virtuais do Azure
Antes do tráfego de Internet pode obter para as redes virtuais do Azure, existem duas camadas de segurança inerentes à plataforma Azure:

1.    **Proteção contra DDoS**: Proteção contra DDoS é uma camada de rede física do Azure que protege a própria plataforma do Azure de ataques baseados na Internet em grande escala. Esses ataques utilizam vários nós de "bot" numa tentativa para sobrecarregar um serviço de Internet. O Azure tem uma malha robusta para a proteção de DDoS em toda a conectividade de entrada, saída e entre o Azure região. Esta camada de proteção contra DDoS não tem nenhum atributo configuráveis do utilizador e não está acessível ao cliente. A camada de proteção contra DDoS protege o Azure como uma plataforma de ataques em grande escala, que também monitoriza o tráfego de out-vinculado e o tráfego do Azure entre regiões. Utilizar aplicações virtuais de rede na VNet, camadas adicionais de resiliência podem ser configuradas pelo cliente contra um ataque de escala mais pequeno que não perder a proteção ao nível da plataforma. Um exemplo de DDoS em ação; Se um endereço IP de acesso à internet foi atacado por um ataque de DDoS em grande escala, o Azure seria detetar as origens dos ataques e limpar o tráfego incorreto antes de que atingiu o seu destino pretendido. Em quase todos os casos, o ponto de extremidade atacado não é afetado pelo ataque. Nos casos raros que um ponto de extremidade é afetado, tráfego não é afetado para outros pontos de extremidade, apenas o ponto de extremidade atacado. Assim, outros clientes e serviços veria sem afetar a partir desse ataque. É fundamental ter em atenção que o DDoS do Azure só está à procura de ataques de grande escala. É possível que o seu serviço específico pode ficar sobrecarregado antes dos limiares de proteção ao nível da plataforma são atingidos. Por exemplo, um web site num único servidor de IIS de A0, poderia ser colocada offline por um ataque de DDoS antes do nível de plataforma do Azure DDoS protection registada uma ameaça.

2.  **Endereços IP públicos**: IP público de endereços (ativados por meio de pontos finais de serviço, endereços de IP público, o Gateway de aplicação e outras funcionalidades do Azure que apresentam um endereço IP público à internet encaminhado para o recurso) permitem que cloud os grupos de serviços ou recursos para que o IP de Internet público os endereços e portas expostas. O ponto final utiliza a tradução de endereços de rede (NAT) para encaminhar o tráfego para o endereço interno e a porta na rede virtual do Azure. Este caminho é o modo principal para tráfego externo para passar para a rede virtual. Os endereços IP públicos são configuráveis para determinar qual o tráfego é passado e como e onde é traduzido para a rede virtual.

Assim que o tráfego chega a rede virtual, existem muitos recursos que entram em cena. Redes virtuais do Azure são a base para os clientes anexar as cargas de trabalho e em que se aplique básicos de segurança ao nível da rede. É uma rede privada (uma sobreposição de rede virtual) no Azure para clientes com as seguintes funcionalidades e características:

* **Isolamento de tráfego**: Uma rede virtual é o limite de isolamento de tráfego na plataforma do Azure. Máquinas virtuais (VMs) numa rede virtual não consegue comunicar diretamente para as VMs numa rede virtual diferente, mesmo se ambas as redes virtuais são criadas ao mesmo cliente. Isolamento é uma propriedade crítica que garante a VMs de cliente e comunicação são mantidos privada numa rede virtual.

>[!NOTE]
>Isolamento de tráfego refere-se apenas ao tráfego *entrada* à rede virtual. Por predefinição o tráfego de saída da VNet à internet é permitido, mas pode ser impedido se desejado pelos NSGs.
>
>

* **Topologia multicamada**: Redes virtuais permitem aos clientes definir topologia multicamada através da alocação de sub-redes e designar espaços de endereço separados para diferentes elementos ou "escalões" de cargas de trabalho. Esses agrupamentos lógicos e topologias de permitem que os clientes definir a política de acesso diferentes com base nos tipos de carga de trabalho e também controlam fluxos de tráfego entre as camadas.
* **Conectividade em vários locais**: Os clientes podem estabelecer conectividade em vários locais entre uma rede virtual e a vários sites no local ou a outras redes virtuais no Azure. Para criar uma ligação, os clientes podem utilizar o VNet Peering, Gateways de VPN do Azure, aplicações virtuais de rede de terceiros ou ExpressRoute. O Azure suporta VPNs (S2S) de site a site usando protocolos padrão de IPsec/IKE e conectividade privada do ExpressRoute.
* **NSG** permite aos clientes criar regras (ACLs) no nível pretendido de granularidade: sub-redes virtuais, VMs individuais ou interfaces de rede. Os clientes podem controlar o acesso ao permitir ou negar a comunicação entre as cargas de trabalho dentro de uma rede virtual, de sistemas em redes do cliente por meio de conectividade em vários locais, ou direcionar a comunicação com a Internet.
* **UDR** e **reencaminhamento IP** permitem aos clientes definir os caminhos de comunicação entre camadas diferentes dentro de uma rede virtual. Os clientes podem implementar uma firewall, IDS/IPS e outras aplicações virtuais e encaminhar o tráfego de rede através destas aplicações de segurança para a imposição de políticas de limite de segurança, auditoria e inspeção.
* **Aplicações virtuais de rede** no Azure Marketplace: Aplicações de segurança, tais como firewalls, balanceadores de carga e IDS/IPS estão disponíveis no Azure Marketplace e a Galeria de imagens de VM. Os clientes podem implementar estas aplicações são nas suas redes virtuais e, especificamente, nos seus limites de segurança (incluindo as sub-redes de rede de perímetro) para concluir um ambiente de várias camadas de rede segura.

Com estas funcionalidades e capacidades, um exemplo de como é possível criar uma arquitetura de rede de perímetro no Azure é o diagrama seguinte:

![5]

## <a name="perimeter-network-characteristics-and-requirements"></a>Requisitos e características de rede de perímetro
A rede de perímetro é o front-end da rede, diretamente a interface comunicação da Internet. Os pacotes de entrada devem fluir através de aplicações de segurança, como o firewall, IDS e IPS, antes de chegar aos servidores back-end. Pacotes de vinculado à Internet das cargas de trabalho também podem fluir das aplicações de segurança na rede de perímetro para imposição de políticas, inspeção e fins de auditoria, antes de deixarem a rede. Além disso, a rede de perímetro pode alojar gateways de VPN em vários locais entre redes virtuais do cliente e redes no local.

### <a name="perimeter-network-characteristics"></a>Características de rede de perímetro
Referenciar a figura anterior, algumas das características de uma rede de perímetro boa são os seguintes:

* Acesso à Internet:
  * A sub-rede de rede de perímetro em si é o acesso à Internet, diretamente a comunicar com a Internet.
  * Endereços IP públicos, VIPs e/ou pontos finais de serviço passam o tráfego de Internet para os dispositivos e de rede front-end.
  * Tráfego de entrada da Internet passa através de dispositivos de segurança antes de outros recursos na rede front-end.
  * Se estiver ativada a segurança de saída, o tráfego passa através de dispositivos de segurança, como a etapa final, antes de passar para a Internet.
* Rede protegida:
  * Não existe nenhum caminho direto da Internet para a infraestrutura de núcleo.
  * Canais para a infraestrutura principal devem percorrer a dispositivos de segurança, tais como dispositivos VPN, os NSGs ou firewalls.
  * Outros dispositivos não devem preencher a infra-estrutura central ou de Internet.
  * Dispositivos de segurança sobre o acesso à Internet e a rede protegida com acesso dos limites da rede de perímetro (por exemplo, os dois firewall ícones mostrados na figura anterior), na verdade, podem ser uma única aplicação virtual com regras diferenciadas ou interfaces para cada limite. Por exemplo, um dispositivo físico, logicamente separado, processar a carga de ambos os limites de rede de perímetro.
* Outras práticas comuns e um restrições:
  * Cargas de trabalho não tem de armazenar informações críticas de negócios.
  * Acesso e atualizações para implementações e configurações de rede de perímetro estão limitadas apenas aos administradores autorizados.

### <a name="perimeter-network-requirements"></a>Requisitos de rede de perímetro
Para ativar estas características, siga estas diretrizes sobre os requisitos de rede virtual para implementar uma rede de perímetro com êxito:

* **Arquitetura de sub-rede:** Especifique a rede virtual, de modo a que uma sub-rede toda dedica-se que a rede de perímetro separada das outras sub-redes na mesma rede virtual. Essa separação garante que o tráfego entre a rede de perímetro e outros fluxos de camadas de sub-rede interno ou privado por meio de uma firewall ou IDS/IPS de aplicação virtual.  Rotas definidas pelo utilizador nas sub-redes limites são necessários para reencaminhar tráfego para a aplicação virtual.
* **NSG:** A sub-rede de rede de perímetro em si deve ser aberta para permitir a comunicação com a Internet, mas que não significa que os clientes devem ser ignorando NSGs. Siga práticas de segurança comuns para minimizar as superfícies de rede expostas à Internet. Bloquear os intervalos de endereços remotos permitidos para acessar as implementações ou os protocolos de aplicação específico e portas abertas. Pode haver circunstâncias, no entanto, no qual um bloqueado completo não é possível. Por exemplo, se os clientes têm um Web site externo no Azure, rede de perímetro deve permitir que os pedidos web recebidos a partir de qualquer endereços IP públicos, mas só deve abrir as portas de aplicação web: TCP na porta 80 e/ou TCP na porta 443.
* **Tabela de encaminhamento:** A sub-rede de rede de perímetro em si deve ser capaz de comunicar diretamente à Internet, mas não deve permitir a comunicação direta de e para os back-end ou no local redes sem passar através de uma aplicação de firewall ou de segurança.
* **Configuração da aplicação de segurança:** Para encaminhar e inspecionar os pacotes entre a rede de perímetro e o restante das redes protegidas, as aplicações de segurança, tais como firewall, IDS e IPS de dispositivos podem ser multihomed. Eles podem ter NICs separadas para a rede de perímetro e as sub-redes de back-end. Os NICs na rede de perímetro comunicarem diretamente para e partir da Internet, com os NSGs correspondentes e a tabela de encaminhamento de rede de perímetro. Os NICs ligar às sub-redes de back-end tenham mais restrito NSGs e tabelas de roteamento das sub-redes de back-end correspondentes.
* **Funcionalidade de segurança da aplicação:** As aplicações de segurança implementadas na rede de perímetro, normalmente, executam a seguinte funcionalidade:
  * Firewall: Impor as regras de firewall ou políticas de controlo de acesso para pedidos recebidos.
  * Deteção de ameaças e prevenção: Deteção e mitigação de ataques maliciosos da Internet.
  * Auditoria e Registro: Manutenção de registos detalhados para auditoria e análise.
  * Proxy inverso: Redirecionar os pedidos de entrada para os servidores de back-end correspondentes. Esse redirecionamento envolve o mapeamento e a tradução de endereços de destino nos dispositivos de front-end, normalmente, as firewalls, para os endereços de servidor back-end.
  * Proxy de encaminhamento: Fornecendo NAT e efetuar a auditoria para a comunicação iniciada a partir de dentro da rede virtual para a Internet.
  * Roteador: Encaminhamento de tráfego de entrada e entre sub-redes dentro da rede virtual.
  * Dispositivo VPN: Atuando como os gateways de VPN em vários locais para a conectividade VPN em vários locais entre redes no local do cliente e redes virtuais do Azure.
  * Servidor VPN: Aceitar os clientes VPN a ligar às redes virtuais do Azure.

> [!TIP]
> Manter os seguintes dois grupos separados: os indivíduos autorizados a aceder a engrenagem de segurança de rede de perímetro e as pessoas autorizadas como administradores de operações, desenvolvimento ou implementação de aplicação. Manter estes grupos separados permite que um segregação de funções e impede que uma única pessoa ignorar a segurança de aplicativos e controlos de segurança de rede.
>
>

### <a name="questions-to-be-asked-when-building-network-boundaries"></a>Perguntas para solicitado durante a criação de limites de rede
Nesta secção, a menos que especificamente mencionadas, o termo "redes" refere-se a redes virtuais privadas do Azure criados por um administrador de subscrição. O termo não referem-se para as redes físicas subjacentes no Azure.

Além disso, as redes virtuais do Azure geralmente são utilizadas para expandir redes tradicional no local. É possível incorporar o site a site ou soluções de rede híbrida do ExpressRoute com arquiteturas de rede de perímetro. Esta ligação híbrida é uma consideração importante na criação de limites de segurança de rede.

As seguintes perguntas de três são fundamentais para responder ao criar uma rede com uma rede de perímetro e vários limites de segurança.

#### <a name="1-how-many-boundaries-are-needed"></a>1) o número de limites são necessários?
O primeiro ponto de decisão será decidir quantos limites de segurança são necessários num determinado cenário:

* Um único limite: Uma na rede de perímetro de front-end, entre a rede virtual e a Internet.
* Dois limites: Um no lado de Internet de rede de perímetro e outro entre a sub-rede de rede de perímetro e as sub-redes de back-end nas redes virtuais do Azure.
* Limites de três: Um no lado de Internet de rede de perímetro, entre a rede de perímetro e as sub-redes de back-end e outra entre as sub-redes de back-end e a rede no local.
* Limites de N: Um número variável. Dependendo dos requisitos de segurança, não há limite para o número de limites de segurança que podem ser aplicados a uma determinada rede.

O número e tipo de limites necessários variam com base na tolerância de risco de uma empresa e do cenário específico que está a ser implementado. Essa decisão, muitas vezes, é feita em conjunto com vários grupos dentro de uma organização, muitas vezes, incluindo uma equipe de risco e conformidade, uma rede e equipe da plataforma e uma equipe de desenvolvimento de aplicativos. As pessoas com conhecimento de segurança, os dados envolvidos e as tecnologias a ser utilizadas devem ter um dizer essa decisão para garantir a postura de segurança adequados para cada implementação.

> [!TIP]
> Utilize o menor número de limites que satisfazem os requisitos de segurança para uma determinada situação. Com os limites de mais, operações e a resolução de problemas podem ser mais difícil, bem como os custos de gestão pertencentes à gestão de múltiplas políticas de limite ao longo do tempo. No entanto, os limites insuficientes aumentam o risco. Encontrar o equilíbrio é fundamental.
>
>

![6]

A figura anterior mostra uma visão geral de uma rede de limite de três segurança. São os limites entre a rede de perímetro e a Internet, os Azure front-end e back-end sub-redes privadas e a sub-rede de back-end do Azure e a rede empresarial no local.

#### <a name="2-where-are-the-boundaries-located"></a>2) onde estão localizados os limites?
Assim que o número de limites é decidiu, onde pretende implementá-los é o próximo ponto de decisão. Em geral, há três opções:

* Utilizar um serviço intermediário baseado na Internet (por exemplo, um com base na cloud firewall de aplicações Web, que não é abordada neste documento)
* Utilizar recursos nativos e/ou dispositivos de rede virtual no Azure
* Com dispositivos físicos na rede no local

Em redes puramente do Azure, as opções são as funcionalidades do Azure nativas (por exemplo, balanceadores de carga do Azure) ou dispositivos de rede virtual do ecossistema de parceiros rico do Azure (por exemplo, firewalls de ponto de verificação).

Se necessitar de um limite entre o Azure e uma rede no local, os dispositivos de segurança podem residir em ambos os lados da ligação (ou ambos os lados). Assim, uma decisão deve ser feita na localização de colocar engrenagem de segurança.

Na figura anterior, a rede de Internet para o perímetro e os limites de frente para back-end são inteiramente contidos dentro do Azure e tem de ser as funcionalidades do Azure nativas ou rede aplicações virtuais. Dispositivos de segurança no limite de entre o Azure (sub-rede de back-end) e rede da empresa podem ser no lado do Azure ou o lado no local ou até mesmo uma combinação de dispositivos em ambos os lados. Pode ser significativas vantagens e desvantagens para qualquer uma das opções que tem de ser seriamente considerado.

Por exemplo, com existentes de segurança física de engrenagem no lado de rede no local, tem a vantagem de que nenhuma nova engrenagem é necessária. Ele apenas precisa de reconfiguração. A desvantagem, no entanto, é que todo o tráfego deve voltar do Azure para a rede no local para ser vistas pelo ícone de engrenagem de segurança. Assim, o tráfego do Azure para o Azure poderia incorrer uma latência significativa e afetam o desempenho do aplicativo e experiência do usuário, se ele foi forçado a voltar à rede no local para a imposição de política de segurança.

#### <a name="3-how-are-the-boundaries-implemented"></a>3) como são implementados os limites?
Cada limite de segurança terão, provavelmente, os requisitos de capacidade diferente (por exemplo, IDS e regras de firewall no lado de Internet da rede de perímetro, mas apenas ACLs entre a rede de perímetro e a sub-rede de back-end). Decidir em que dispositivo (ou o número de dispositivos) a utilizar depende dos requisitos de cenário e a segurança. Na seção a seguir, exemplos, 1, 2 e 3 discutem algumas opções que podem ser utilizadas. Rever os recursos do Azure nativo de rede e os dispositivos disponíveis no Azure a partir do ecossistema do parceiro mostra as inúmeras opções disponíveis para resolver praticamente qualquer cenário.

Outro ponto de decisão de implementação de chaves é como se pode ligar a rede no local com o Azure. Deve usar o gateway virtual do Azure ou uma aplicação virtual de rede? Estas opções são abordadas mais detalhadamente na seção a seguir (exemplos 4, 5 e 6).

Além disso, o tráfego entre redes virtuais no Azure pode ser necessárias. Estes cenários serão adicionados no futuro.

Quando souber as respostas às questões anteriores, o [, início rápido](#fast-start) secção pode ajudar a identificar quais os exemplos que são mais adequados para um determinado cenário.

## <a name="examples-building-security-boundaries-with-azure-virtual-networks"></a>Exemplos: Criar limites de segurança com redes virtuais do Azure
### <a name="example-1-build-a-perimeter-network-to-help-protect-applications-with-nsgs"></a>Exemplo 1 criar uma rede de perímetro para ajudar a proteger aplicações com NSGs
[Voltar ao início rápido](#fast-start) | [Detailed criar instruções para este exemplo][Example1]

[![7]][7]

#### <a name="environment-description"></a>Descrição de ambiente
Neste exemplo, existe uma subscrição que contém os seguintes recursos:

- Um grupo de recursos
- Uma rede virtual com duas sub-redes: "Front-end" e "Back-end"
- Um grupo de segurança de rede que é aplicado a ambas as sub-redes
- Um servidor do Windows que representa um servidor de web de aplicações ("IIS01")
- Dois servidores do Windows que representam servidores de back-end de aplicação ("AppVM01", "AppVM02")
- Um servidor do Windows que representa um servidor DNS ("DNS01")
- Um IP público associado ao servidor de web de aplicação

Para scripts e um modelo Azure Resource Manager, consulte a [compilação instruções detalhadas][Example1].

#### <a name="nsg-description"></a>Descrição de NSG
Neste exemplo, um grupo NSG é criado e, em seguida, é carregado com regras de seis.

> [!TIP]
> Em termos gerais, deve criar as regras específicas de "Permitir" em primeiro lugar, seguido as regras mais genéricas de "Negar". A prioridade atribuída determina as regras são avaliadas primeiro. Assim que o tráfego for encontrado para aplicar a uma regra específica, não existem regras adicionais são avaliadas. As regras do NSG podem aplicar em qualquer direção de entrada ou saída (a partir do ponto de vista da sub-rede).
>
>

De forma declarativa, as seguintes regras estão sendo criadas para tráfego de entrada:

1. O tráfego DNS interno (porta 53) é permitido.
2. Tráfego RDP (porta 3389) da Internet para qualquer máquina Virtual é permitido.
3. Tráfego HTTP (porta 80) da Internet para o servidor web (IIS01) é permitido.
4. Qualquer tráfego (todas as portas) de IIS01 para AppVM1 é permitido.
5. Qualquer tráfego (todas as portas) da Internet para a rede virtual inteira (ambas as sub-redes) é negado.
6. Qualquer tráfego (todas as portas) da sub-rede do front-end para a sub-rede de back-end é negado.

Com estas regras vinculado a cada sub-rede, se um pedido de HTTP foi entrado da Internet para o servidor web, ambas as regras 3 (permitir) e 5 (negar) seria aplicada. Mas como a regra 3 tem uma prioridade mais alta, só seriam aplicáveis e, regra 5 não seria entram em cena. Assim, a solicitação HTTP teria permissão para o servidor web. Se esse mesmo tráfego estava a tentar aceder ao servidor DNS01, regra 5 (negar) seria o primeiro a aplicar e o tráfego que não permitia a passar para o servidor. Regra 6 (negar) bloqueia a sub-rede do front-end ao falar com a sub-rede de back-end (exceto para o tráfego permitido nas regras 1 e 4). Este conjunto de regras protege a rede de back-end, no caso de um invasor compromete a aplicação web no front-end. O invasor teria de limitaram o acesso à rede back-end "protegido" (apenas para recursos expostos no servidor AppVM01).

Existe uma regra de saída predefinida que permita o tráfego de saída à Internet. Neste exemplo, estamos a permitir o tráfego de saída e sem modificar quaisquer regras de saída. Para bloquear o tráfego em ambas as direções, encaminhamento definido pelo utilizador é necessária (veja o exemplo 3).

#### <a name="conclusion"></a>Conclusão
Neste exemplo é uma maneira relativamente simples e fácil de isolar a sub-rede de back-end do tráfego de entrada. Para obter mais informações, consulte a [compilação instruções detalhadas][Example1]. Estas instruções incluem:

* Como criar esta rede de perímetro com scripts do PowerShell clássicos.
* Como criar esta rede de perímetro com um modelo Azure Resource Manager.
* Descrições detalhadas de cada comando NSG.
* Cenários de fluxo do tráfego detalhada, que mostra como o tráfego é permitido ou negado em cada camada.


### <a name="example-2-build-a-perimeter-network-to-help-protect-applications-with-a-firewall-and-nsgs"></a>Exemplo 2 criar uma rede de perímetro para ajudar a proteger aplicações com um firewall e NSGs
[Voltar ao início rápido](#fast-start) | [Detailed criar instruções para este exemplo][Example2]

[![8]][8]

#### <a name="environment-description"></a>Descrição de ambiente
Neste exemplo, existe uma subscrição que contém os seguintes recursos:

* Um grupo de recursos
* Uma rede virtual com duas sub-redes: "Front-end" e "Back-end"
* Um grupo de segurança de rede que é aplicado a ambas as sub-redes
* Uma aplicação virtual de rede, neste caso, um firewall, ligados à sub-rede de front-end
* Um servidor do Windows que representa um servidor de web de aplicações ("IIS01")
* Dois servidores do Windows que representam servidores de back-end de aplicação ("AppVM01", "AppVM02")
* Um servidor do Windows que representa um servidor DNS ("DNS01")

Para scripts e um modelo Azure Resource Manager, consulte a [compilação instruções detalhadas][Example2].

#### <a name="nsg-description"></a>Descrição de NSG
Neste exemplo, um grupo NSG é criado e, em seguida, é carregado com regras de seis.

> [!TIP]
> Em termos gerais, deve criar as regras específicas de "Permitir" em primeiro lugar, seguido as regras mais genéricas de "Negar". A prioridade atribuída determina as regras são avaliadas primeiro. Assim que o tráfego for encontrado para aplicar a uma regra específica, não existem regras adicionais são avaliadas. As regras do NSG podem aplicar em qualquer direção de entrada ou saída (a partir do ponto de vista da sub-rede).
>
>

De forma declarativa, as seguintes regras estão sendo criadas para tráfego de entrada:

1. O tráfego DNS interno (porta 53) é permitido.
2. Tráfego RDP (porta 3389) da Internet para qualquer máquina Virtual é permitido.
3. Qualquer tráfego de Internet (todas as portas) para a aplicação virtual de rede (firewall) é permitido.
4. Qualquer tráfego (todas as portas) de IIS01 para AppVM1 é permitido.
5. Qualquer tráfego (todas as portas) da Internet para a rede virtual inteira (ambas as sub-redes) é negado.
6. Qualquer tráfego (todas as portas) da sub-rede do front-end para a sub-rede de back-end é negado.

Com estas regras vinculado a cada sub-rede, se um pedido de HTTP foi entrado da Internet para o firewall, regras de 3 (permitir) e 5 (negar) seria aplicada. Mas como a regra 3 tem uma prioridade mais alta, só seriam aplicáveis e, regra 5 não seria entram em cena. Assim, o pedido HTTP teria permissão para a firewall. Se esse mesmo tráfego estava a tentar aceder ao servidor IIS01, mesmo que ele está na sub-rede de front-end, da regra 5 (negar) aplicaria, e o tráfego que não permitia a passar para o servidor. Regra 6 (negar) bloqueia a sub-rede do front-end ao falar com a sub-rede de back-end (exceto para o tráfego permitido nas regras 1 e 4). Este conjunto de regras protege a rede de back-end, no caso de um invasor compromete a aplicação web no front-end. O invasor teria de limitaram o acesso à rede back-end "protegido" (apenas para recursos expostos no servidor AppVM01).

Existe uma regra de saída predefinida que permita o tráfego de saída à Internet. Neste exemplo, estamos a permitir o tráfego de saída e sem modificar quaisquer regras de saída. Para bloquear o tráfego em ambas as direções, encaminhamento definido pelo utilizador é necessária (veja o exemplo 3).

#### <a name="firewall-rule-description"></a>Descrição da regra de firewall
Na firewall, as regras de reencaminhamento devem ser criadas. Uma vez que este exemplo apenas encaminha o tráfego de Internet no vinculados à firewall e, em seguida, para o servidor web, de reencaminhamento de apenas uma rede (NAT) de tradução de endereços é necessária a regra.

A regra de encaminhamento aceita qualquer endereço de origem de entrada que chegue o firewall tentando alcançar HTTP (porta 80 ou 443 para HTTPS). É enviada para fora da interface de local do firewall e redirecionado para o servidor web com o endereço IP do 10.0.1.5N.

#### <a name="conclusion"></a>Conclusão
Neste exemplo é uma maneira relativamente simples de proteger a sua aplicação com um firewall e isolamento da sub-rede de back-end do tráfego de entrada. Para obter mais informações, consulte a [compilação instruções detalhadas][Example2]. Estas instruções incluem:

* Como criar esta rede de perímetro com scripts do PowerShell clássicos.
* Como criar este exemplo com um modelo Azure Resource Manager.
* Descrições detalhadas de cada regra de firewall e de comando do NSG.
* Cenários de fluxo do tráfego detalhada, que mostra como o tráfego é permitido ou negado em cada camada.

### <a name="example-3-build-a-perimeter-network-to-help-protect-networks-with-a-firewall-and-udr-and-nsg"></a>Exemplo 3 criar uma rede de perímetro para ajudar a proteger redes com uma firewall, UDR e NSG
[Voltar ao início rápido](#fast-start) | [Detailed criar instruções para este exemplo][Example3]

[![9]][9]

#### <a name="environment-description"></a>Descrição de ambiente
Neste exemplo, existe uma subscrição que contém os seguintes recursos:

* Um grupo de recursos
* Uma rede virtual com três sub-redes: "SecNet", "FrontEnd" e "Back-end"
* Uma aplicação virtual de rede, neste caso, um firewall, ligado à subrede SecNet
* Um servidor do Windows que representa um servidor de web de aplicações ("IIS01")
* Dois servidores do Windows que representam servidores de back-end de aplicação ("AppVM01", "AppVM02")
* Um servidor do Windows que representa um servidor DNS ("DNS01")

Para scripts e um modelo Azure Resource Manager, consulte a [compilação instruções detalhadas][Example3].

#### <a name="udr-description"></a>Descrição de UDR
Por predefinição, as seguintes rotas de sistema são definidas como:

        Effective routes :
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.0.0/16}     VNETLocal                            Active   Default    
         {0.0.0.0/0}       Internet                             Active   Default    
         {10.0.0.0/8}      Null                                 Active   Default    
         {100.64.0.0/10}   Null                                 Active   Default    
         {172.16.0.0/12}   Null                                 Active   Default    
         {192.168.0.0/16}  Null                                 Active   Default

O VNETLocal é sempre um ou mais prefixos de endereços definidos que compõem a rede virtual para essa rede específica (ou seja, ele é alterado de rede virtual para rede virtual, dependendo de como cada rede virtual específico é definido). As rotas de sistema restantes são estáticas e predefinido conforme indicado na tabela.

Neste exemplo, duas tabelas de roteamento são criados, um para as sub-redes de front-end e back-end. Cada tabela é carregada com rotas estáticas adequadas para a sub-rede especificada. Neste exemplo, cada tabela tem três rotas que direcionar todo o tráfego (0.0.0.0/0) através da firewall (do próximo salto = o endereço IP de aplicação Virtual):

1. Tráfego de sub-rede local com nenhum próximo salto definido para permitir o tráfego de sub-rede local ignorar a firewall.
2. Tráfego de rede virtual com um salto seguinte definido como o firewall. Esse próximo salto substitui a regra predefinida que permita o tráfego de rede virtual local encaminhar diretamente.
3. Todos os restante tráfego (0/0) com um salto seguinte definido como o firewall.

> [!TIP]
> Não ter a entrada da sub-rede local na sub-rede local UDR quebras de comunicações.
>
> * No nosso exemplo, é fundamental a 10.0.1.0/24 apontando para VNETLocal! Sem ele, pacotes, deixando o servidor Web (10.0.1.4), destinado a outro servidor local (por exemplo) 10.0.1.25 falhará como eles serão enviados para a NVA. A NVA enviará-lo para a sub-rede e a sub-rede será reenvie-o para a NVA num loop infinito.
> * As chances de um loop de roteamento são normalmente superiores em aplicações com vários NICs estão ligadas a sub-redes, separadas que é muitas vezes de aplicações do tradicional, no local.
>
>

Assim que as tabelas de encaminhamento são criadas, deve ser vinculados a suas sub-redes. A tabela de encaminhamento para a sub-rede de front-end como, uma vez criado e associado à sub-rede, ficaria esta saída:

        Effective routes :
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.1.0/24}     VNETLocal                            Active
         {10.0.0.0/16}     VirtualAppliance 10.0.0.4            Active    
         {0.0.0.0/0}       VirtualAppliance 10.0.0.4            Active

> [!NOTE]
> Agora pode ser aplicada a UDR para a sub-rede de gateway em que o circuito do ExpressRoute está ligado.
>
> Exemplos de como ativar a rede de perímetro com o ExpressRoute ou rede de site a site são mostrados nos exemplos 3 e 4.
>
>

#### <a name="ip-forwarding-description"></a>Descrição de reencaminhamento IP
O reencaminhamento IP é uma funcionalidade de complementar a UDR. O reencaminhamento IP é uma definição de uma aplicação virtual que permite que ele receba tráfego endereçado não especificamente para a aplicação e, em seguida, reencaminhe que o tráfego para um destino final.

Por exemplo, se AppVM01 faz uma solicitação para o servidor de DNS01, UDR seria encaminhar este tráfego para o firewall. Com o reencaminhamento IP ativado, o tráfego para o destino de DNS01 (10.0.2.4) é aceite pela aplicação (10.0.0.4) e, depois, reencaminhado para um destino final (10.0.2.4). Sem o reencaminhamento IP ativada na firewall, o tráfego não será Aceito ou não pela aplicação, apesar da tabela de rotas tem o firewall como salto seguinte. Para utilizar uma aplicação virtual, é essencial lembrar-se ativar o reencaminhamento de IP, juntamente com o UDR.

#### <a name="nsg-description"></a>Descrição de NSG
Neste exemplo, um grupo NSG é criado e, em seguida, é carregado com uma única regra. Este grupo, em seguida, está associado apenas para as sub-redes de front-end e back-end (não o SecNet). Declarativamente a seguinte regra está sendo criada:

* Qualquer tráfego (todas as portas) da Internet para a rede virtual inteira (todas as sub-redes) é negado.

Embora os NSGs são utilizados neste exemplo, o seu principal propósito é como uma camada secundária de defesa contra má configuração manual. O objetivo é bloquear todo o tráfego de entrada da Internet para as sub-redes front-end ou back-end. Tráfego apenas deve fluir através da sub-rede SecNet o firewall (e, em seguida, se apropriado, para as sub-redes de front-end ou back-end). Além disso, com as regras UDR em vigor, qualquer tráfego que passou a fazer para as sub-redes de front-end ou back-end seria direcionado horizontalmente para a firewall (graças à UDR). O firewall veria este tráfego como um fluxo assimétrico e seria remover o tráfego de saída. Assim, há três camadas de segurança protegendo as sub-redes:

* Não existem endereços IP públicos em qualquer front-end ou back-end NICs.
* NSGs negar o tráfego da Internet.
* O firewall a colocar assimétrica tráfego.

Um ponto interessante sobre o NSG neste exemplo é que ele contém apenas uma regra, o que é negar o tráfego de Internet para a rede virtual completo, incluindo a sub-rede de segurança. No entanto, como o NSG está ligado apenas para as sub-redes de front-end e back-end, a regra não é processada no tráfego de entrada para a sub-rede de segurança. Como resultado, o tráfego flui para a sub-rede de segurança.

#### <a name="firewall-rules"></a>Regras da firewall
Na firewall, as regras de reencaminhamento devem ser criadas. Uma vez que a firewall está a bloquear ou o encaminhamento de toda a entrada, saído e o tráfego de rede flui virtual, são necessárias várias regras de firewall. Além disso, todo o tráfego de entrada atinge o endereço IP público do serviço de segurança (em portas diferentes), para serem processados pelo firewall. Uma prática recomendada é para os fluxos de lógicos antes de configurar as sub-redes de diagrama e regras de firewall, para evitar retrabalho mais tarde. A figura a seguir é uma visão lógica das regras de firewall para este exemplo:

![10]

> [!NOTE]
> As portas de gestão com base na aplicação Virtual da rede utilizada, variam. Neste exemplo, um Barracuda NextGen Firewall é referenciado, o que utiliza as portas 22, 801 e 807. Consulte a documentação do fornecedor da aplicação para encontrar as portas exatas utilizadas para a gestão do dispositivo a ser utilizado.
>
>

#### <a name="firewall-rules-description"></a>Descrição de regras de firewall
A sub-rede de segurança não é apresentada no diagrama lógico anterior, porque o firewall é o único recurso dessa sub-rede. O diagrama mostra as regras de firewall e como eles logicamente permitem ou negar fluxos de tráfego, não o caminho real roteado. Além disso, as portas externas selecionadas para o tráfego RDP são superior ranged portas (8014 – 8026) e foram selecionadas para vagamente alinhado com os dois últimos octetos do endereço IP local para facilitar a leitura (por exemplo, o endereço de servidor local 10.0.1.4 está associado porta externa 8014). No entanto, podem ser utilizadas qualquer superior portas não conflitantes.

Neste exemplo, precisamos de sete tipos de regras:

* Regras de externas (para o tráfego de entrada):
  1. Regra de firewall de gestão: Esta regra de redirecionamento da aplicação permite que o tráfego passar para as portas de gestão da aplicação virtual de rede.
  2. Regras RDP (para cada servidor do Windows): Estas quatro regras (um para cada servidor) permitem a gestão dos servidores individuais através de RDP. As regras RDP quatro também poderiam ser recolhidas numa regra, consoante as capacidades do dispositivo virtual de rede a ser utilizado.
  3. Regras de tráfego de aplicações: Existem duas dessas regras, o primeiro para o tráfego de web de front-end e o segundo para o tráfego de back-end (por exemplo, servidor web para a camada de dados). A configuração dessas regras depende da arquitetura de rede (em que os servidores são colocados) e fluxos de tráfego (a direção são utilizados os fluxos de tráfego e que as portas).
     * A primeira regra permite o tráfego de aplicativo real alcançar o servidor de aplicação. Enquanto outras regras permitem a gestão e segurança, as regras de tráfego de aplicativo são o que permitem que os utilizadores externos ou serviços para aceder às aplicações. Neste exemplo, existe um servidor web único na porta 80. Assim, uma única aplicação regra de firewall redireciona o tráfego de entrada para o IP externo, para o endereço IP da interno de servidores web. A sessão de tráfego redirecionado poderia ser convertida através de NAT para o servidor interno.
     * A segunda regra é a regra de back-end para permitir que o servidor web comunicar com o servidor de AppVM01 (mas não AppVM02) por meio de qualquer porta.
* Regras internas (para o tráfego de rede flui virtual)
  1. Saída para a regra de Internet: Esta regra permite o tráfego de qualquer rede para passar para as redes selecionadas. Esta regra é, normalmente, uma regra predefinida já na firewall, mas num Estado desabilitado. Esta regra deve ser ativada para este exemplo.
  2. Regra DNS: Esta regra permite apenas tráfego de DNS (porta 53) passar para o servidor DNS. Para este ambiente, a maioria do tráfego de front-end para back-end está bloqueado. Esta regra permite especificamente DNS a partir de qualquer sub-rede local.
  3. Sub-rede a regra de sub-rede: Esta regra é permitir que qualquer servidor na sub-rede de back-end para ligar a qualquer servidor na sub-rede de front-end (mas não o inverso).
* Obter regra isento de falhas (para o tráfego que não cumpre nenhum dos anterior):
  1. Todos os de tráfego de regra de negação: Esta regra de negação deve ser sempre a regra final (em termos de prioridade) e, como tal, se um fluxo de tráfego não conseguir corresponder a qualquer uma das regras anteriores é removido por esta regra. Esta regra é uma regra predefinida e, normalmente, no local e Active Directory. Sem modificações, normalmente, são necessárias para esta regra.

> [!TIP]
> Na segunda regra de tráfego do aplicativo, a para simplificar este exemplo, é permitida qualquer porta. Num cenário real, a porta mais específica e intervalos de endereços devem ser utilizados para reduzir a superfície de ataque desta regra.
>
>

Assim que as regras anteriores são criadas, é importante revisar a prioridade de cada regra para garantir que o tráfego é permitido ou negado conforme pretendido. Neste exemplo, as regras são por ordem de prioridade.

#### <a name="conclusion"></a>Conclusão
Este exemplo é uma mais complexa mas concluir a forma de proteger e isolar a rede que os exemplos anteriores. (Exemplo 2 protege apenas o aplicativo e exemplo 1 apenas isola as sub-redes). Esse design permite a monitorização de tráfego em ambas as direções e protege não apenas o servidor de aplicações de entrada mas impõe a política de segurança de rede para todos os servidores nesta rede. Além disso, consoante a aplicação utilizada, a auditoria de tráfego total e reconhecimento podem ser obtidos. Para obter mais informações, consulte a [compilação instruções detalhadas][Example3]. Estas instruções incluem:

* Como criar esta rede de perímetro de exemplo com scripts do PowerShell clássicos.
* Como criar este exemplo com um modelo Azure Resource Manager.
* As descrições detalhadas de cada UDR, NSG comando e a regra de firewall.
* Cenários de fluxo do tráfego detalhada, que mostra como o tráfego é permitido ou negado em cada camada.

### <a name="example-4-add-a-hybrid-connection-with-a-site-to-site-virtual-appliance-vpn"></a>Exemplo 4 adicionar uma ligação híbrida com uma aplicação VPN site a site, virtual
[Voltar ao início rápido](#fast-start) | Instruções de compilação disponíveis de detalhadas em breve

[![11]][11]

#### <a name="environment-description"></a>Descrição de ambiente
Rede híbrida com uma aplicação virtual de rede (NVA) pode ser adicionado a qualquer um dos tipos de rede de perímetro, descritos nos exemplos 1, 2 ou 3.

Conforme mostrado na figura anterior, uma ligação VPN através da Internet (site a site) é utilizada para ligar uma rede no local a uma rede virtual do Azure através de uma NVA.

> [!NOTE]
> Se utilizar o ExpressRoute com a opção de Peering público do Azure ativada, uma rota estática deve ser criada. Esta rota estática deve encaminhar para o endereço de IP de VPN da NVA sua Internet empresarial e não através da ligação do ExpressRoute. O NAT necessário na opção de Peering público do ExpressRoute do Azure pode quebrar a sessão VPN.
>
>

Depois da VPN estiver em vigor, a NVA torna-se o hub central para todas as redes e sub-redes. As regras de reencaminhamento de firewall determinam quais fluxos de tráfego são permitidos, são traduzidas por meio de NAT, são redirecionadas ou são ignoradas (mesmo para fluxos de tráfego entre a rede no local e o Azure).

Fluxos de tráfego devem ser considerados com cuidado, à medida que eles podem ser otimizados ou degradado por este padrão de design, consoante a específicas do caso de utilização.

Usando o ambiente criado no exemplo 3 e, em seguida, adicionar uma ligação de rede do site-site VPN híbrida, produz a seguinte estrutura:

[![12]][12]

O router no local, ou qualquer outro dispositivo de rede que é compatível com a NVA para VPN, seria o cliente VPN. Este dispositivo físico seria responsável por iniciar e manter a ligação de VPN com a NVA.

Logicamente para a NVA, a rede é semelhante a quatro separado "zonas de segurança" com as regras na NVA a ser o primário diretor de tráfego entre essas zonas:

![13]

#### <a name="conclusion"></a>Conclusão
A adição de uma ligação de rede híbrida do site-site VPN a uma rede virtual do Azure pode expandir a rede no local para o Azure de forma segura. Usando uma conexão VPN, o tráfego é encriptado e encaminha através da Internet. A NVA neste exemplo fornece um local central para impor a e gerir a política de segurança. Para obter mais informações, consulte as instruções detalhadas de compilação (será lançado em breve). Estas instruções incluem:

* Como criar esta rede de perímetro de exemplo com scripts do PowerShell.
* Como criar este exemplo com um modelo Azure Resource Manager.
* Cenários de fluxo do tráfego detalhada, que mostra como o tráfego flui através deste design.

### <a name="example-5-add-a-hybrid-connection-with-a-site-to-site-azure-vpn-gateway"></a>Exemplo 5 adicionar uma ligação híbrida com um gateway de VPN do Azure de site a site
[Voltar ao início rápido](#fast-start) | Instruções de compilação disponíveis de detalhadas em breve

[![14]][14]

#### <a name="environment-description"></a>Descrição de ambiente
Rede híbrida com um gateway de VPN do Azure pode ser adicionado a qualquer tipo de rede de perímetro descrito nos exemplos 1 ou 2.

Conforme mostrado na figura anterior, uma ligação VPN através da Internet (site a site) é utilizada para ligar uma rede no local a uma rede virtual do Azure através de um gateway de VPN do Azure.

> [!NOTE]
> Se utilizar o ExpressRoute com a opção de Peering público do Azure ativada, uma rota estática deve ser criada. Esta rota estática deve encaminhar para o endereço de IP de VPN da NVA sua Internet empresarial e não através de WAN o ExpressRoute. O NAT necessário na opção de Peering público do ExpressRoute do Azure pode quebrar a sessão VPN.
>
>

A figura seguinte mostra os dois limites de rede, neste exemplo. No primeiro limite, a NVA e NSGs controlam fluxos de tráfego para redes flui do Azure e entre o Azure e a Internet. O segundo edge é o gateway de VPN do Azure, o que é uma borda de rede separada e isolados entre no local e o Azure.

Fluxos de tráfego devem ser considerados com cuidado, à medida que eles podem ser otimizados ou degradado por este padrão de design, consoante a específicas do caso de utilização.

Usando o ambiente criado no exemplo 1 e, em seguida, adicionar uma ligação de rede do site-site VPN híbrida, produz a seguinte estrutura:

[![15]][15]

#### <a name="conclusion"></a>Conclusão
A adição de uma ligação de rede híbrida do site-site VPN a uma rede virtual do Azure pode expandir a rede no local para o Azure de forma segura. Utilizar o gateway de VPN do Azure nativo, o tráfego é encriptado IPSec e encaminha através da Internet. Além disso, se utilizar o gateway de VPN do Azure, pode fornecer uma opção de custo mais baixo (não existem licenças adicionais de custos tal como acontece com NVAs de terceiros). Esta opção é mais económica no exemplo 1, em que não é utilizada nenhuma NVA. Para obter mais informações, consulte as instruções detalhadas de compilação (será lançado em breve). Estas instruções incluem:

* Como criar esta rede de perímetro de exemplo com scripts do PowerShell.
* Como criar este exemplo com um modelo Azure Resource Manager.
* Cenários de fluxo do tráfego detalhada, que mostra como o tráfego flui através deste design.

### <a name="example-6-add-a-hybrid-connection-with-expressroute"></a>Exemplo 6 adicionar uma ligação híbrida com o ExpressRoute
[Voltar ao início rápido](#fast-start) | Instruções de compilação disponíveis de detalhadas em breve

[![16]][16]

#### <a name="environment-description"></a>Descrição de ambiente
Rede híbrida com uma ligação de peering privada do ExpressRoute pode ser adicionado a cada tipo de rede de perímetro descritos nos exemplos 1 ou 2.

Conforme mostrado na figura anterior, o peering privado do ExpressRoute fornece uma ligação direta entre a sua rede no local e a rede virtual do Azure. Tráfego transits apenas a rede de fornecedor de serviço e de rede do Microsoft Azure, nunca tocar na Internet.

> [!TIP]
> Com o ExpressRoute mantém o tráfego de rede empresarial fora da Internet. Ele também permite que os contratos de nível de serviço através do seu fornecedor do ExpressRoute. O Gateway do Azure pode transmitir até 10 Gbps com o ExpressRoute, enquanto com VPNs de site a site, o débito máximo do Gateway do Azure é 200 Mbps.
>
>

Como mostrado no diagrama seguinte, com esta opção o ambiente tem agora duas extremidades da rede. A NVA e NSG controlam fluxos de tráfego para redes flui do Azure e entre o Azure e a Internet, enquanto o gateway é uma borda de rede separada e isolados entre no local e o Azure.

Fluxos de tráfego devem ser considerados com cuidado, à medida que eles podem ser otimizados ou degradado por este padrão de design, consoante a específicas do caso de utilização.

Usando o ambiente criado no exemplo 1 e, em seguida, adicionar uma ligação de rede do ExpressRoute híbrida, produz a seguinte estrutura:

[![17]][17]

#### <a name="conclusion"></a>Conclusão
A adição de uma ligação de rede do Peering privado do ExpressRoute pode expandir a rede no local para o Azure numa latência segura, mais baixa, melhor desempenho de forma. Além disso, a utilizar o Gateway do Azure nativo, tal como neste exemplo, fornece uma opção de custo mais baixo e (às licenciamento tal como acontece com NVAs de terceiros não adicionais). Para obter mais informações, consulte as instruções detalhadas de compilação (será lançado em breve). Estas instruções incluem:

* Como criar esta rede de perímetro de exemplo com scripts do PowerShell.
* Como criar este exemplo com um modelo Azure Resource Manager.
* Cenários de fluxo do tráfego detalhada, que mostra como o tráfego flui através deste design.

## <a name="references"></a>Referências
### <a name="helpful-websites-and-documentation"></a>Sites úteis e documentação
* Azure de acesso com o Azure Resource Manager:
* Aceder ao Azure com o PowerShell: [https://docs.microsoft.com/powershell/azureps-cmdlets-docs/](/powershell/azure/overview)
* Documentação do sistema de rede virtual: [https://docs.microsoft.com/azure/virtual-network/](https://docs.microsoft.com/azure/virtual-network/)
* Documentação do grupo de segurança de rede: [https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg](virtual-network/security-overview.md)
* Documentação de encaminhamento definido pelo utilizador: [https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview](virtual-network/virtual-networks-udr-overview.md)
* Gateways virtuais do Azure: [https://docs.microsoft.com/azure/vpn-gateway/](https://docs.microsoft.com/azure/vpn-gateway/)
* VPNs de site a Site: [https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell](vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* Documentação do ExpressRoute (não se esqueça de verificar as seções "Getting Started" e "Como a"): [https://docs.microsoft.com/azure/expressroute/](https://docs.microsoft.com/azure/expressroute/)

<!--Image References-->
[0]: ./media/best-practices-network-security/flowchart.png "fluxograma de opções de segurança"
[2]: ./media/best-practices-network-security/azuresecurityfeatures.png "funcionalidades de segurança do azure"
[3]: ./media/best-practices-network-security/dmzcorporate.png "A rede de Perímetro numa rede empresarial"
[4]: ./media/best-practices-network-security/azuresecurityarchitecture.png "arquitetura de segurança do azure"
[5]: ./media/best-practices-network-security/dmzazure.png "uma rede de Perímetro numa rede Virtual do Azure"
[6]: ./media/best-practices-network-security/dmzhybrid.png "rede híbrida com três limites de segurança"
[7]: ./media/best-practices-network-security/example1design.png "DMZ com NSG de entrada"
[8]: ./media/best-practices-network-security/example2design.png "DMZ com NVA e NSG de entrada"
[9]: ./media/best-practices-network-security/example3design.png "DMZ bidirecional com NVA, um NSG e UDR"
[10]: ./media/best-practices-network-security/example3firewalllogical.png "visão lógica das regras de Firewall"
[11]: ./media/best-practices-network-security/example3designoptions.png "DMZ com NVA ligado rede híbrida"
[12]: ./media/best-practices-network-security/example4designs2s.png "DMZ com NVA ligado através de uma VPN de Site a Site"
[13]: ./media/best-practices-network-security/example4networklogical.png "rede lógica do ponto de vista da NVA"
[14]: ./media/best-practices-network-security/example5designoptions.png "DMZ com o Gateway do Azure ligada rede híbrida de Site a Site"
[15]: ./media/best-practices-network-security/example5designs2s.png "DMZ com o Gateway do Azure através de VPN de Site a Site"
[16]: ./media/best-practices-network-security/example6designoptions.png "DMZ com o Gateway do Azure ligada rede híbrida de ExpressRoute"
[17]: ./media/best-practices-network-security/example6designexpressroute.png "DMZ com o Gateway do Azure através de uma ligação de ExpressRoute"

<!--Link References-->
[TrustCenter]: https://azure.microsoft.com/support/trust-center/compliance/
[Example1]: ./virtual-network/virtual-networks-dmz-nsg.md
[Example2]: ./virtual-network/virtual-networks-dmz-nsg-fw-asm.md
[Example3]: ./virtual-network/virtual-networks-dmz-nsg-fw-udr-asm.md
[Example4]: ./virtual-network/virtual-networks-hybrid-s2s-nva-asm.md
[Example5]: ./virtual-network/virtual-networks-hybrid-s2s-agw-asm.md
[Example6]: ./virtual-network/virtual-networks-hybrid-expressroute-asm.md
[Example7]: ./virtual-network/virtual-networks-vnet2vnet-direct-asm.md
[Example8]: ./virtual-network/virtual-networks-vnet2vnet-transit-asm.md
