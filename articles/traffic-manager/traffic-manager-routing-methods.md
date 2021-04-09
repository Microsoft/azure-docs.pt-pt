---
title: Azure Traffic Manager - métodos de encaminhamento de tráfego
description: Este artigo ajuda-o a compreender os diferentes métodos de encaminhamento de tráfego utilizados pelo Traffic Manager
services: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/21/2021
ms.author: duau
ms.openlocfilehash: eeebded128f636ecba2e4e0dab1bc2f0632aaa6a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98730977"
---
# <a name="traffic-manager-routing-methods"></a>Métodos de encaminhamento do Gestor de Tráfego

O Azure Traffic Manager suporta seis métodos de encaminhamento de tráfego para determinar como encaminhar o tráfego da rede para os vários pontos finais de serviço. Para qualquer perfil, o Traffic Manager aplica o método de encaminhamento de tráfego que lhe está associado a cada consulta de DNS que recebe. O método de encaminhamento de tráfego determina qual o ponto final devolvido na resposta ao DNS.

Os seguintes métodos de encaminhamento de tráfego estão disponíveis no Gestor de Tráfego:

* **[Prioridade](#priority-traffic-routing-method):** Selecione o encaminhamento **prioritário** quando pretender ter um ponto final de serviço primário para todo o tráfego. Pode fornecer vários pontos finais de backup caso o principal ou um dos pontos finais de backup não esteja disponível.
* **[Ponderado](#weighted):** Selecione o encaminhamento **ponderado** quando pretender distribuir o tráfego por um conjunto de pontos finais com base no seu peso. Desagre o peso da mesma forma para distribuir uniformemente em todos os pontos finais.
* **[Desempenho](#performance):** Selecione o encaminhamento de **desempenho** quando tiver pontos finais em diferentes localizações geográficas e pretende que os utilizadores finais utilizem o ponto final "mais próximo" para a latência da rede mais baixa.
* **[Geográfico](#geographic):** Selecione o encaminhamento **geográfico** para utilizadores direcionados para pontos finais específicos (Azure, External ou Nested) com base no local de origem das suas consultas de DNS. Com este método de encaminhamento, permite-lhe estar em conformidade com cenários como mandatos de soberania de dados, localização de conteúdos & experiência do utilizador e medir o tráfego de diferentes regiões.
* **[Multivalue](#multivalue):** Selecione **MultiValue** para perfis de Gestor de Tráfego que só podem ter endereços IPv4/IPv6 como pontos finais. Quando uma consulta é recebida para este perfil, todos os pontos finais saudáveis são devolvidos.
* **[Sub-rede](#subnet):** Selecione o método de encaminhamento de tráfego **sub-rede** para mapear conjuntos de intervalos de endereços IP do utilizador final para um ponto final específico. Quando um pedido é recebido, o ponto final devolvido será o mapeado para o endereço IP de origem desse pedido. 


Todos os perfis do Traffic Manager têm monitorização de saúde e falha automática de pontos finais. Para obter mais informações, consulte [a Monitorização endpoint do Gestor de Tráfego.](traffic-manager-monitoring.md) Dentro de um perfil de Gestor de Tráfego, só é possível configurar um método de encaminhamento de tráfego de cada vez. Pode selecionar um método de encaminhamento de tráfego diferente para o seu perfil a qualquer momento. As suas alterações serão aplicadas dentro de um minuto sem qualquer tempo de inatividade. Pode combinar métodos de encaminhamento de tráfego utilizando perfis de Gestor de Tráfego aninhados. Os perfis de nidificação permitem configurações sofisticadas de encaminhamento de tráfego que atendam às necessidades de aplicações maiores e complexas. Para mais informações, consulte [os perfis do Gestor de Tráfego aninhado.](traffic-manager-nested-profiles.md)

## <a name="priority-traffic-routing-method"></a>Método de encaminhamento de tráfego prioritário

Muitas vezes, uma organização quer fornecer fiabilidade para os seus serviços. Para tal, eles implantam um ou mais serviços de backup no caso de as suas primárias caírem. O método de encaminhamento de tráfego "Prioritário" permite aos clientes da Azure implementar facilmente este padrão de failover.

![Método de encaminhamento de tráfego 'prioritário' do gestor de tráfego da Azure](media/traffic-manager-routing-methods/priority.png)

O perfil do Gestor de Tráfego contém uma lista prioritária de pontos finais de serviço. Por predefinição, o Gestor de Tráfego envia todo o tráfego para o ponto final primário (com a prioridade mais alta). Se o ponto final primário não estiver disponível, o Gestor de Tráfego encaminha o tráfego para o segundo ponto final. Numa situação em que os pontos finais primários e secundários não estão disponíveis, o tráfego vai para o terceiro, e assim por diante. A disponibilidade do ponto final baseia-se no estado configurado (ativado ou desativado) e na monitorização contínua do ponto final.

### <a name="configuring-endpoints"></a>Pontos finais configurados

Com o Azure Resource Manager, configura a prioridade do ponto final usando explicitamente a propriedade 'prioritária' para cada ponto final. Esta propriedade é um valor entre 1 e 1000. Um valor mais baixo representa uma prioridade maior. Os pontos finais não podem partilhar valores prioritários. Definir a propriedade é opcional. Quando omitida, é utilizada uma prioridade padrão com base na ordem de ponto final.

## <a name="weighted-traffic-routing-method"></a><a name = "weighted"></a>Método de encaminhamento de tráfego ponderado
O método de encaminhamento de tráfego 'ponderado' permite-lhe distribuir o tráfego uniformemente ou utilizar uma ponderação pré-definida.

![Método de encaminhamento de tráfego 'ponderado' do gestor de tráfego da Azure](media/traffic-manager-routing-methods/weighted.png)

No método de encaminhamento de tráfego ponderado, atribui-se um peso a cada ponto final na configuração do perfil do Gestor de Tráfego. A ponderação é um número inteiro entre 1 e 1000. Este parâmetro é opcional. Se omitidos, os Gestores de Tráfego utilizam um peso predefinido de '1'. Quanto maior o peso, maior a prioridade.

Para cada consulta DNS recebida, o Gestor de Tráfego seleciona aleatoriamente um ponto final disponível. A probabilidade de um ponto final ser selecionado baseia-se nos pesos atribuídos a todos os pontos finais disponíveis. A utilização do mesmo peso em todos os pontos finais resulta numa distribuição uniforme do tráfego. A utilização de pesos mais altos ou inferiores em pontos finais específicos faz com que esses pontos finais sejam devolvidos com mais ou menos frequência nas respostas do DNS.

O método ponderado permite alguns cenários úteis:

* Atualização gradual da aplicação: Dada a percentagem de tráfego para encaminhar para um novo ponto final, e aumentar gradualmente o tráfego ao longo do tempo para 100%.
* Migração de aplicações para Azure: Criar um perfil com pontos finais Azure e externos. Ajuste o peso dos pontos finais para preferir os novos pontos finais.
* Cloud-bursting for more capacity: Expande rapidamente uma implantação no local para a nuvem, colocando-a atrás de um perfil de Gestor de Tráfego. Quando precisar de uma capacidade extra na nuvem, pode adicionar ou ativar mais pontos finais e especificar qual a parte do tráfego que vai para cada ponto final.

Pode configurar pesos utilizando o portal Azure, Azure PowerShell, CLI ou as APIs REST.

Um ponto a lembrar é que as respostas do DNS são cached pelos clientes. Também estão em cache pelos servidores DNS recursivos que os clientes usam para resolver os nomes dns. Este caching pode ter um efeito sobre as distribuições ponderadas de tráfego. Quando o número de clientes e servidores DNS recursivos é grande, a distribuição de tráfego funciona como esperado. No entanto, quando o número de clientes ou servidores DNS recursivos é pequeno, o caching pode distorcer significativamente a distribuição de tráfego.

Os casos de uso comum incluem:

* Ambientes de desenvolvimento e teste
* Comunicações de aplicação à aplicação
* Aplicações destinadas a uma base de utilizador estreita que partilha uma infraestrutura comum de DNS recursiva (por exemplo, funcionários da empresa que se conectam através de um representante)

Estes efeitos de caching DNS são comuns a todos os sistemas de encaminhamento de tráfego baseados em DNS, e não apenas ao Azure Traffic Manager. Em alguns casos, limpar explicitamente a cache DNS pode fornecer uma solução alternativa. Se isso não funcionar, um método alternativo de encaminhamento de tráfego pode ser mais apropriado.

## <a name="performance-traffic-routing-method"></a><a name = "performance"></a>Método de encaminhamento de tráfego de desempenho

A implementação de pontos finais em dois ou mais locais em todo o mundo pode melhorar a capacidade de resposta das suas aplicações. Com o método de encaminhamento de tráfego 'Performance', pode encaminhar o tráfego para o local que está 'mais próximo' de si.

![Método de encaminhamento de tráfego 'Performance' do Gestor de Tráfego da Azure](media/traffic-manager-routing-methods/performance.png)

O ponto final mais próximo não é necessariamente o mais próximo medido pela distância geográfica. Em vez disso, o método de encaminhamento de tráfego 'Performance' determina o ponto final mais próximo medindo a latência da rede. O Gestor de Tráfego mantém uma Tabela de Latência na Internet para acompanhar o tempo de ida e volta entre as gamas de endereços IP e cada datacenter Azure.

O Gestor de Tráfego procura o endereço IP de origem do pedido de DNS que chega na Tabela de Latência da Internet. O Gestor de Tráfego escolhe então um ponto final disponível no datacenter Azure que tem a latência mais baixa para essa gama de endereços IP. Em seguida, o Gestor de Tráfego devolve esse ponto final na resposta do DNS.

Como explicado em [Como o Gestor de Tráfego funciona,](traffic-manager-how-it-works.md)o Gestor de Tráfego não recebe consultas de DNS diretamente dos clientes. Em vez disso, as consultas dns provêm do serviço DNS recursivo que os clientes estão configurados a utilizar. Como tal, o endereço IP utilizado para determinar o ponto final 'mais próximo' não é o endereço IP do cliente, mas é o endereço IP do serviço DNS recursivo. Este endereço IP é um bom representante para o cliente.


O Gestor de Tráfego atualiza regularmente a Tabela de Latência da Internet para responder às mudanças na Internet global e nas novas regiões do Azure. No entanto, o desempenho da aplicação varia em base em variações em tempo real na carga em toda a Internet. O encaminhamento de tráfego de desempenho não monitoriza a carga num determinado ponto de serviço. Se um ponto final ficar indisponível, o Gestor de Tráfego não o incluirá nas respostas de consulta de DNS.


Pontos a notar:

* Se o seu perfil contiver vários pontos finais na mesma região de Azure, então o Traffic Manager distribui o tráfego uniformemente pelos pontos finais disponíveis nessa região. Se preferir uma distribuição de tráfego diferente dentro de uma região, pode utilizar [perfis de Gestor de Tráfego aninhados.](traffic-manager-nested-profiles.md)
* Se todos os pontos finais ativados na região de Azure mais próxima forem degradados, o Gestor de Tráfego desloca o tráfego para os pontos finais na região de Azure mais próxima. Se quiser definir uma sequência de falha preferida, utilize [perfis aninhados do Gestor de Tráfego](traffic-manager-nested-profiles.md).
* Ao utilizar o método de encaminhamento de tráfego de desempenho com pontos finais externos ou pontos finais aninhados, tem de especificar a localização desses pontos finais. Escolha a região Azure mais próxima da sua implantação. Esses locais são os valores suportados pela Tabela de Latência da Internet.
* O algoritmo que escolhe o ponto final é determinístico. As consultas repetidas de DNS do mesmo cliente são direcionadas para o mesmo ponto final. Normalmente, os clientes usam diferentes servidores DNS recursivos durante a viagem. O cliente pode ser encaminhado para um ponto final diferente. O encaminhamento também pode ser afetado por atualizações para a Tabela de Latência da Internet. É por isso que o método de encaminhamento de tráfego desempenho não garante que um cliente é sempre encaminhado para o mesmo ponto final.
* Quando a Tabela de Latência da Internet mudar, poderá notar que alguns clientes são direcionados para um ponto final diferente. Esta alteração de encaminhamento é mais precisa com base nos dados atuais de latência. Estas atualizações são essenciais para manter a precisão do encaminhamento de tráfego de desempenho à medida que a Internet evolui continuamente.

## <a name="geographic-traffic-routing-method"></a><a name = "geographic"></a>Método de encaminhamento de tráfego geográfico

Os perfis do Gestor de Tráfego podem ser configurados para utilizar o método de encaminhamento Geográfico para que os utilizadores sejam direcionados para pontos finais específicos (Azure, External ou Nested) com base na localização geográfica da qual a sua consulta DNS é originária. Com este método de encaminhamento, permite-lhe estar em conformidade com os mandatos de soberania de dados, a localização de conteúdos & a experiência do utilizador e a medição do tráfego de diferentes regiões.
Quando um perfil é configurado para o encaminhamento geográfico, cada ponto final associado a esse perfil precisa de ter um conjunto de regiões geográficas atribuídas a ele. Uma região geográfica pode estar a seguir níveis de granularidade 
- Mundo: qualquer região
- Agrupamento Regional – por exemplo, África, Médio Oriente, Austrália/Pacífico, etc. 
- País/Região – por exemplo, Irlanda, Peru, HONG KONG SAR etc. 
- Estado/Província – por exemplo, EUA-Califórnia, Austrália-Queensland, Canada-Alberta etc. (nota: este nível de granularidade é suportado apenas para estados/províncias na Austrália, Canadá e EUA).

Quando uma região ou um conjunto de regiões é atribuída a um ponto final, quaisquer pedidos dessas regiões são encaminhados apenas para esse ponto final. O Gestor de Tráfego utiliza o endereço IP de origem da consulta DNS para determinar a região de onde um utilizador está a consultar. Geralmente encontrado como o endereço IP do DNS local resolver fazendo a consulta para o utilizador.  

![Método de encaminhamento de tráfego 'Geográfico' do Gestor de Tráfego Azure](./media/traffic-manager-routing-methods/geographic.png)

O Gestor de Tráfego lê o endereço IP de origem da consulta do DNS e decide de que região geográfica é originária. Em seguida, olha para ver se há um ponto final que tem esta região geográfica mapeada para ele. Este lookup começa no nível mais baixo de granularidade (Estado/Província onde é apoiado, senão ao nível país/região) e vai até ao nível mais alto, que é o **Mundo.** O primeiro jogo encontrado usando este traversal é escolhido como o ponto final para voltar na resposta de consulta. Ao combinar com um ponto final do tipo Aninhado, é devolvido um ponto final dentro desse perfil da criança, com base no seu método de encaminhamento. Os seguintes pontos são aplicáveis a este comportamento:

- Uma região geográfica só pode ser mapeada para um ponto final num perfil de Gestor de Tráfego quando o tipo de encaminhamento é O Encaminhamento Geográfico. Esta restrição garante que o encaminhamento dos utilizadores é determinístico, e os clientes podem permitir cenários que exijam fronteiras geográficas inequívocas.
- Se a região de um utilizador estiver listada em dois mapeamentos geográficos de dois pontos finais diferentes, o Gestor de Tráfego seleciona o ponto final com a granularidade mais baixa. O Diretor de Trânsito não vai considerar os pedidos de encaminhamento daquela região para o outro ponto final. Por exemplo, considere um perfil do tipo de encaminhamento geográfico com dois pontos finais - Endpoint1 e Endpoint2. O ponto final1 está configurado para receber tráfego da Irlanda e o Endpoint2 está configurado para receber tráfego da Europa. Se um pedido tem origem na Irlanda, é sempre encaminhado para Endpoint1.
- Uma vez que uma região só pode ser mapeada para um ponto final, o Gestor de Tráfego devolve uma resposta se o ponto final é saudável ou não.

    >[!IMPORTANT]
    >Recomenda-se vivamente que os clientes que utilizam o método de encaminhamento geográfico o associem aos pontos finais do tipo Aninhado que contêm perfis infantis que contenham pelo menos dois pontos finais dentro de cada um.
- Se for encontrada uma correspondência de ponto final e o ponto final estiver no estado **de Stop,** o Gestor de Tráfego devolve uma resposta NODATA. Neste caso, não são feitas mais investigações na hierarquia da região geográfica. Este comportamento também é aplicável para tipos de pontos finais aninhados quando o perfil da criança está no estado **de paragem** ou **desativação.**
- Se um ponto final apresentar um estado **de desativação,** não será incluído no processo de correspondência da região. Este comportamento também é aplicável para tipos de pontos finais aninhados quando o ponto final está no estado **de Desativado.**
- Se uma consulta vem de uma região geográfica que não tem mapeamento nesse perfil, o Gestor de Tráfego devolve uma resposta NODATA. É por isso que recomendamos vivamente que use o encaminhamento geográfico com um ponto final. Idealmente do tipo Aninhado com pelo menos dois pontos finais dentro do perfil da criança, com a região **World** atribuída a ele. Esta configuração também garante que quaisquer endereços IP que não sejam mapear para uma região são tratados.

Como explicado em [Como o Gestor de Tráfego funciona,](traffic-manager-how-it-works.md)o Gestor de Tráfego não recebe consultas de DNS diretamente dos clientes. As consultas de DNS provêm do serviço DE DNS recursivo que os clientes estão configurados a utilizar. É por isso que o endereço IP usado para determinar a região não é o endereço IP do cliente, mas sim o endereço IP do serviço DNS recursivo. Este endereço IP é um bom representante para o cliente.

### <a name="faqs"></a>FAQs

* [Quais são alguns casos de uso em que o encaminhamento geográfico é útil?](./traffic-manager-faqs.md#what-are-some-use-cases-where-geographic-routing-is-useful)

* [Como decido se devo utilizar o método de encaminhamento de desempenho ou o método de encaminhamento geográfico?](./traffic-manager-faqs.md#how-do-i-decide-if-i-should-use-performance-routing-method-or-geographic-routing-method)

* [Quais são as regiões que são apoiadas pelo Gestor de Tráfego para o encaminhamento geográfico?](./traffic-manager-faqs.md#what-are-the-regions-that-are-supported-by-traffic-manager-for-geographic-routing)

* [Como é que o gestor de tráfego determina de onde um utilizador está a consultar?](./traffic-manager-faqs.md#how-does-traffic-manager-determine-where-a-user-is-querying-from)

* [É garantido que o Traffic Manager pode determinar corretamente a localização geográfica exata do utilizador em todos os casos?](./traffic-manager-faqs.md#is-it-guaranteed-that-traffic-manager-can-correctly-determine-the-exact-geographic-location-of-the-user-in-every-case)

* [Um ponto final precisa de ser fisicamente localizado na mesma região com a qual está configurado para o encaminhamento geográfico?](./traffic-manager-faqs.md#does-an-endpoint-need-to-be-physically-located-in-the-same-region-as-the-one-it-is-configured-with-for-geographic-routing)

* [Posso atribuir regiões geográficas a pontos finais num perfil que não está configurado para fazer o encaminhamento geográfico?](./traffic-manager-faqs.md#can-i-assign-geographic-regions-to-endpoints-in-a-profile-that-is-not-configured-to-do-geographic-routing)

* [Porque estou a cometer um erro quando tento mudar o método de encaminhamento de um perfil existente para a Geographic?](./traffic-manager-faqs.md#why-am-i-getting-an-error-when-i-try-to-change-the-routing-method-of-an-existing-profile-to-geographic)

* [Por que razão é fortemente recomendado que os clientes criem perfis aninhados em vez de pontos finais sob um perfil com encaminhamento geográfico ativado?](./traffic-manager-faqs.md#why-is-it-strongly-recommended-that-customers-create-nested-profiles-instead-of-endpoints-under-a-profile-with-geographic-routing-enabled)

* [Existem restrições à versão API que suporte este tipo de encaminhamento?](./traffic-manager-faqs.md#are-there-any-restrictions-on-the-api-version-that-supports-this-routing-type)

## <a name="multivalue-traffic-routing-method"></a><a name = "multivalue"></a>Método de encaminhamento de tráfego multivalue
O método de encaminhamento de tráfego **Multivalue** permite-lhe obter vários pontos finais saudáveis numa única resposta de consulta dns. Esta configuração permite ao ouvinte fazer recauchutagens do lado do cliente com outros pontos finais no caso de um ponto final devolvido não responder. Este padrão pode aumentar a disponibilidade de um serviço e reduzir a latência associada a uma nova consulta DNS para obter um ponto final em bom estado de funcionamento. O método de encaminhamento MultiValue só funciona se todos os pontos finais do tipo 'Externo' e forem especificados como endereços IPv4 ou IPv6. Quando uma consulta é recebida para este perfil, todos os pontos finais saudáveis são devolvidos e estão sujeitos a uma contagem máxima de retorno configurável.

### <a name="faqs"></a>FAQs

* [Quais são alguns casos de uso em que o encaminhamento MultiValue é útil?](./traffic-manager-faqs.md#what-are-some-use-cases-where-multivalue-routing-is-useful)

* [Quantos pontos finais são devolvidos quando o roteamento MultiValue é usado?](./traffic-manager-faqs.md#how-many-endpoints-are-returned-when-multivalue-routing-is-used)

* [Terei o mesmo conjunto de pontos finais quando o roteamento MultiValue for usado?](./traffic-manager-faqs.md#will-i-get-the-same-set-of-endpoints-when-multivalue-routing-is-used)

## <a name="subnet-traffic-routing-method"></a><a name = "subnet"></a>Método de encaminhamento de tráfego de sub-rede
O método de encaminhamento de tráfego **sub-rede** permite-lhe mapear um conjunto de gamas de endereços IP do utilizador final para pontos finais específicos num perfil. Se o Gestor de Tráfego receber uma consulta de DNS para esse perfil, irá inspecionar o endereço IP de origem desse pedido. Em seguida, determinará para que ponto final está mapeado e devolverá esse ponto final na resposta de consulta. Na maioria dos casos, o endereço IP de origem é o dns resolver que é usado pelo chamador.

O endereço IP a ser mapeado para um ponto final pode ser especificado como gamas CIDR (por exemplo, 1.2.3.0/24) ou como uma gama de endereços (por exemplo, 1.2.3.4-5.6.7.8). As gamas IP associadas a um ponto final têm de ser únicas dentro desse perfil. O intervalo de endereços não pode ter uma sobreposição com o conjunto de endereços IP de um ponto final diferente no mesmo perfil.
Se definir um ponto final sem intervalo de endereço, isso funciona como um recuo e tirar o tráfego de quaisquer sub-redes restantes. Se não for incluído nenhum ponto final de retorno, o Gestor de Tráfego envia uma resposta NODATA para quaisquer gamas indefinidas. É altamente recomendado que defina um ponto final de retorno para garantir que todas as gamas IP possíveis são especificadas nos seus pontos finais.

O encaminhamento de sub-redes pode ser usado para oferecer uma experiência diferente para os utilizadores que se conectam a partir de um espaço IP específico. Por exemplo, pode fazer com que todos os pedidos do seu escritório corporativo sejam encaminhados para um ponto final diferente. Este método de encaminhamento é especialmente útil se estiver a tentar testar uma versão interna da sua aplicação. Outro cenário possível seria se quisesse fornecer uma experiência diferente aos utilizadores que estabelecem ligação a um ISP específico (por exemplo, bloquear utilizadores de um determinado ISP).

### <a name="faqs"></a>FAQs

* [Quais são alguns casos de utilização em que o encaminhamento de sub-redes é útil?](./traffic-manager-faqs.md#what-are-some-use-cases-where-subnet-routing-is-useful)

* [Como é que o Gestor de Tráfego conhece o endereço IP do utilizador final?](./traffic-manager-faqs.md#how-does-traffic-manager-know-the-ip-address-of-the-end-user)

* [Como posso especificar endereços IP ao utilizar o encaminhamento sub-rede?](./traffic-manager-faqs.md#how-can-i-specify-ip-addresses-when-using-subnet-routing)

* [Como posso especificar um ponto final de recuo ao utilizar o encaminhamento sub-rede?](./traffic-manager-faqs.md#how-can-i-specify-a-fallback-endpoint-when-using-subnet-routing)

* [O que acontece se um ponto final for desativado num perfil do tipo de encaminhamento sub-rede?](./traffic-manager-faqs.md#what-happens-if-an-endpoint-is-disabled-in-a-subnet-routing-type-profile)


## <a name="next-steps"></a>Passos seguintes

Saiba como desenvolver aplicações de alta disponibilidade utilizando [a monitorização do ponto final do Traffic Manager](traffic-manager-monitoring.md)