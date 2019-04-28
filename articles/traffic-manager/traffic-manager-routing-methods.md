---
title: Gestor de tráfego do Azure - métodos de encaminhamento de tráfego
description: Artigos de ajuda a que compreender os métodos de encaminhamento de tráfego diferentes utilizados pelo Gestor de tráfego
services: traffic-manager
author: KumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: kumud
ms.openlocfilehash: 3cabfeda458011c5d3006642085f78dc74f3451e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60543449"
---
# <a name="traffic-manager-routing-methods"></a>Métodos de encaminhamento do Traffic Manager

O Gestor de tráfego do Azure suporta seis métodos de encaminhamento de tráfego de mensagens em fila para determinar como encaminhar o tráfego de rede para vários pontos de extremidade do serviço. Para qualquer perfil, o Gestor de tráfego aplica-se o método de encaminhamento de tráfego de mensagens em fila associado a ele para cada consulta DNS que recebe. O método de encaminhamento de tráfego determina qual ponto de extremidade é devolvido na resposta DNS.

Os métodos de encaminhamento de tráfego seguintes estão disponíveis no Gestor de tráfego:

* **[Prioridade](#priority):** Selecione **prioridade** quando pretender utilizar um ponto de extremidade de serviço principal para todo o tráfego e fornecer cópias de segurança no caso da primária ou os pontos finais de cópia de segurança não estão disponíveis.
* **[Weighted](#weighted):** Selecione **ponderado** quando deseja distribuir o tráfego por um conjunto de pontos de extremidade, uniformemente ou, de acordo com pesos, que defina.
* **[Desempenho](#performance):** Selecione **desempenho** quando têm pontos finais em localizações geográficas diferentes e pretender que os utilizadores finais utilizem o ponto final "mais próximo" em termos a menor latência de rede.
* **[Geográfica](#geographic):** Selecione **Geographic** para que os utilizadores são direcionados para os pontos finais (do Azure, externas ou aninhados) com base nas localizações geográficas em suas consultas DNS são originados. Isso capacita os clientes do Gestor de tráfego para ativar cenários em que conhecer a região geográfica de um utilizador e encaminhamento-los com base no que são importante. Os exemplos incluem o cumprimento mandatos de soberania de dados, a localização da experiência de utilizador e de conteúdo e medir o tráfego a partir de regiões diferentes.
* **[Multivalue](#multivalue):** Selecione **MultiValue** para perfis do Gestor de tráfego que podem ter apenas endereços IPv4/IPv6, como pontos finais. Quando uma consulta é recebida para este perfil, são devolvidos todos os pontos finais de bom estado de funcionamento.
* **[Subnet](#subnet):** Selecione **sub-rede** método de encaminhamento de tráfego de mensagens em fila para mapear os conjuntos de intervalos de endereços IP do utilizador final para um ponto final específico dentro de um perfil do Gestor de tráfego. Quando é recebido um pedido, o ponto final devolvido será a que é mapeada para o endereço IP de origem esse pedido. 


Todos os perfis do Gestor de tráfego incluem a monitorização de estado de funcionamento do ponto final e ativação pós-falha do ponto de extremidade automática. Para obter mais informações, consulte [monitorização de ponto final do Gestor de tráfego](traffic-manager-monitoring.md). Um único perfil do Gestor de tráfego pode utilizar apenas um método de encaminhamento de tráfego. Pode selecionar um método de encaminhamento de tráfego diferentes para o seu perfil em qualquer altura. As alterações são aplicadas num minuto e, sem períodos de indisponibilidade é incorrido. Métodos de encaminhamento de tráfego podem ser combinados utilizando perfis do Gestor de tráfego aninhados. Aninhamento permite sofisticadas e flexíveis configurações de encaminhamento de tráfego que satisfazem as necessidades das aplicações grandes e complexas. Para obter mais informações, consulte [aninhada de perfis do Gestor de tráfego](traffic-manager-nested-profiles.md).

## <a name = "priority"></a>Método de encaminhamento de tráfego de prioridade

Muitas vezes, uma organização deseja fornecer confiabilidade para seus serviços ao implementar um ou mais serviços de cópia de segurança no caso de seus principais do serviço ficar inativo. O método de encaminhamento de tráfego de 'Priority' permite que os clientes do Azure facilmente implementar este padrão de ativação pós-falha.

![O Gestor de tráfego do Azure 'Priority' método de encaminhamento de tráfego](media/traffic-manager-routing-methods/priority.png)

O perfil do Gestor de tráfego contém uma lista prioritária de pontos finais de serviço. Por predefinição, o Gestor de tráfego envia o tráfego para o ponto final (prioridade mais alta) primário. Se o ponto final primário não estiver disponível, o Gestor de tráfego encaminha o tráfego para o segundo ponto de extremidade. Se os pontos de extremidade primários e secundários não estiverem disponíveis, o tráfego passa para o terceiro e assim por diante. Disponibilidade do ponto de extremidade baseia-se sobre o estado configurado (ativada ou desativada) e a monitorização do ponto final em curso.

### <a name="configuring-endpoints"></a>Configurando pontos de extremidade

Com o Azure Resource Manager, pode configurar a prioridade de ponto final explicitamente usando a propriedade de 'priority' para cada ponto de extremidade. Esta propriedade é um valor entre 1 e 1000. Valores mais baixos representam uma prioridade mais alta. Pontos finais não podem partilhar os valores de prioridade. Definir a propriedade é opcional. Quando for omitido, é utilizada uma prioridade padrão com base na ordem de ponto final.

## <a name = "weighted"></a>Método de encaminhamento de tráfego ponderado
O método de encaminhamento de tráfego de mensagens em fila "Ponderado" permite-lhe para distribuir o tráfego de forma uniforme ou para utilizar um peso predefinido.

![O Azure Traffic Manager método da 'Ponderado' Encaminhamento de tráfego](media/traffic-manager-routing-methods/weighted.png)

O método de encaminhamento de tráfego ponderado, vai atribuir uma ponderação para cada ponto de extremidade na configuração de perfil do Gestor de tráfego. A ponderação é um número inteiro entre 1 e 1000. Este parâmetro é opcional. Se for omitido, os gestores de tráfego utiliza um peso padrão de '1'. O peso mais alto, maior será a prioridade.

Para cada consulta DNS recebida, o Gestor de tráfego escolhe aleatoriamente um ponto de extremidade disponível. A probabilidade de escolher um ponto de extremidade baseia-se os pesos atribuídos a todos os pontos de extremidade disponíveis. Em todos os pontos de extremidade resulta numa distribuição de tráfego até mesmo a utilizar a mesma importância. Usar os pesos superiores ou inferiores em pontos de extremidade específicos, faz com que esses pontos de extremidade seja retornado mais ou menos frequência nas respostas DNS.

O método ponderado permite alguns cenários úteis:

* Atualização gradual de aplicativos: Alocar uma percentagem de tráfego para encaminhar para um novo ponto final e aumentar gradualmente o tráfego ao longo do tempo para 100%.
* Migração de aplicativos para o Azure: Crie um perfil com pontos finais do Azure e externos. Ajuste o peso de pontos de extremidade para dar preferência os novos pontos de extremidade.
* Segurança na cloud para capacidade adicional: Expanda rapidamente uma implementação no local para a nuvem, colocando-o por trás de um perfil do Gestor de tráfego. Quando precisar de capacidade extra na cloud, pode adicionar ou ativar mais pontos finais e especifique a que parte do tráfego vai para cada ponto de extremidade.

Além de utilizar o portal do Azure, pode configurar os pesos usando o Azure PowerShell, CLI e as APIs REST.

É importante compreender que as respostas DNS são colocadas em cache por clientes e os servidores DNS recursivos que os clientes utilizam para resolver nomes DNS. Esta colocação em cache pode ter um impacto em distribuições de tráfego ponderada. Quando o número de clientes e servidores DNS recursivos é grande, a distribuição de tráfego funciona conforme esperado. No entanto, quando o número de clientes ou servidores DNS recursivos for pequeno, colocação em cache pode significativamente Inclinar a distribuição de tráfego.

Casos de utilização comuns incluem:

* Desenvolvimento e ambientes de teste
* Comunicações de aplicativo para aplicativo
* Aplicativos com o objetivo de uma pequena base de usuários que compartilham uma infra-estrutura DNS recursivo comum (por exemplo, os funcionários da empresa, ligar através de um proxy)

Esses efeitos de colocação em cache DNS são comuns a todos os tráfego baseado em DNS encaminhamento sistemas, não apenas o Gestor de tráfego do Azure. Em alguns casos, a explicitamente limpar a cache DNS poderá fornecer uma solução alternativa. Em outros casos, um método alternativo de encaminhamento de tráfego pode ser mais adequado.

## <a name = "performance"></a>Método de encaminhamento de tráfego de desempenho

Implementar pontos de extremidade em duas ou mais localizações em todo o mundo pode melhorar a capacidade de resposta de vários aplicativos ao encaminhar o tráfego para a localização que esteja "mais perto" de si. O método de encaminhamento de tráfego de "Performance" fornece esta capacidade.

![O Gestor de tráfego do Azure "Performance" método de encaminhamento de tráfego](media/traffic-manager-routing-methods/performance.png)

O ponto final "mais próximo" não é necessariamente mais próximo, medido pela distância geográfica. Em vez disso, o método de encaminhamento de tráfego de "Performance" determina o ponto final mais próximo ao medir a latência de rede. O Gestor de tráfego mantém uma tabela de latência de Internet para controlar o tempo de ida e volta entre intervalos de endereços IP e cada datacenter do Azure.

O Gestor de tráfego procura o endereço IP de origem do pedido a receber DNS na tabela de latência de Internet. O Gestor de tráfego, em seguida, escolhe um ponto de extremidade disponível no Centro de dados do Azure que tenha a latência mais baixa para esse intervalo de endereços IP e retorna o ponto de extremidade na resposta DNS.

Conforme explicado [como funciona o Gestor de tráfego](traffic-manager-how-it-works.md), Gestor de tráfego não recebe as consultas DNS diretamente a partir de clientes. Em vez disso, as consultas DNS são provenientes do serviço DNS recursivo que os clientes estão configurados para utilizar. Por conseguinte, o endereço IP utilizado para determinar o ponto final "mais próximo" não é o endereço IP do cliente, mas é o endereço IP do serviço DNS recursivo. Na prática, este endereço IP é um bom proxy para o cliente.


O Gestor de tráfego atualiza regularmente a tabela de latência de Internet para levar em conta as alterações na global Internet e novas regiões do Azure. No entanto, o desempenho de aplicativo varia consoante em tempo real variações na carga através da Internet. Encaminhamento de tráfego de desempenho não monitorizar a carga de um ponto de extremidade de determinado serviço. No entanto, se um ponto de extremidade ficar indisponível, o Gestor de tráfego não incluí-lo nas respostas de consulta DNS.


Pontos a serem observados:

* Se o seu perfil contém vários pontos de extremidade na mesma região do Azure, em seguida, o Gestor de tráfego distribui o tráfego uniformemente entre os pontos finais disponíveis nessa região. Se preferir uma distribuição de tráfego diferentes dentro de uma região, pode utilizar [aninhada de perfis do Gestor de tráfego](traffic-manager-nested-profiles.md).
* Se todos os pontos finais ativados na região do Azure mais próxima estão degradados, o Gestor de tráfego move tráfego para os pontos de extremidade na região do Azure mais próxima seguinte. Se pretender definir uma sequência de ativação pós-falha preferencial, utilize [aninhada de perfis do Gestor de tráfego](traffic-manager-nested-profiles.md).
* Ao utilizar o método de encaminhamento de tráfego de desempenho com pontos finais externos ou pontos de extremidade aninhados, terá de especificar a localização desses pontos de extremidade. Selecione a região do Azure mais próxima da sua implementação. Nesses locais são os valores suportados pela tabela de latência de Internet.
* O algoritmo que escolhe o ponto final é determinístico. Consultas DNS repetidas do mesmo cliente são direcionadas para o mesmo ponto final. Normalmente, os clientes utilizam servidores DNS recursivos diferentes em viagem. O cliente pode ser encaminhado para um ponto de extremidade diferente. Encaminhamento também pode ser afetado pelas atualizações para a tabela de latência de Internet. Por conseguinte, o método de encaminhamento de tráfego de desempenho não garante que um cliente sempre será roteado para o mesmo ponto final.
* Quando a tabela de latência de Internet é alterada, poderá reparar que alguns clientes são direcionados para um ponto de extremidade diferente. Esta alteração de encaminhamento é mais precisa com base nos dados de latência atual. Estas atualizações são essenciais para manter a precisão de encaminhamento de tráfego de desempenho, à medida que a Internet evolui continuamente.

## <a name = "geographic"></a>Método de encaminhamento de tráfego geográfico

Perfis do Gestor de tráfego podem ser configurados para utilizar o método de encaminhamento geográfico para que os utilizadores são direcionados para os pontos finais (do Azure, externas ou aninhados) com base nas localizações geográficas em suas consultas DNS são originados. Isso capacita os clientes do Gestor de tráfego para ativar cenários em que conhecer a região geográfica de um utilizador e encaminhamento-los com base no que são importante. Os exemplos incluem o cumprimento mandatos de soberania de dados, a localização da experiência de utilizador e de conteúdo e medir o tráfego a partir de regiões diferentes.
Quando um perfil está configurado para encaminhamento geográfico, cada ponto de extremidade associado com que o perfil tem de ter um conjunto de regiões geográficas atribuídos ao mesmo. Uma região geográfica que pode ser nos seguintes níveis de granularidade 
- Mundo – qualquer região
- Regional agrupamento – por exemplo, África, Médio Oriente, Austrália/Pacífico etc. 
- País/região – por exemplo, Irlanda, Peru, RAE de Hong Kong etc. 
- Estado/província – por exemplo, EUA Califórnia, Austrália-Queensland, etc. do Canadá-Alberta (Nota: este nível de granularidade é suportada apenas para Estados / províncias na Austrália, Canadá e EUA).

Quando uma região ou um conjunto de regiões é atribuído a um ponto de extremidade, quaisquer pedidos dessas regiões é encaminhado apenas para esse ponto final. O Gestor de tráfego utiliza o endereço IP de origem da consulta DNS para determinar a região a partir do qual é a consulta de um utilizador – normalmente, é o endereço IP do resolvedor DNS local, fazer a consulta em nome do utilizador.  

![O Azure Traffic Manager método da 'Geográfica' Encaminhamento de tráfego](./media/traffic-manager-routing-methods/geographic.png)

O Gestor de tráfego lê o endereço IP de origem da consulta DNS e decide o que é proveniente de região geográfica. Em seguida, ele procura para ver se existe um ponto de extremidade que tenha esta região geográfica mapeada para este. Esta pesquisa é iniciado no nível de granularidade mais baixo (estado/província onde é suportada, caso contrário ao nível do país/região) e vai até o nível mais elevado, o que é **mundo**. A primeira correspondência encontrada usar esta passagem é designado como o ponto final para devolver na resposta da consulta. Quando é devolvido a correspondência com um endpoint de tipo aninhados, um ponto final dentro desse perfil subordinado, com base no seu método de encaminhamento. Os pontos seguintes são aplicáveis a esse comportamento:

- Uma região geográfica pode ser mapeada apenas para um ponto final num perfil de Gestor de tráfego, quando o tipo de encaminhamento é encaminhamento geográfico. Isto garante que o encaminhamento de utilizadores é determinístico e os clientes podem ativar cenários que exigem inequívocos limites geográficos.
- Se a região de um utilizador mapeamento de geográfica em duas diferentes dos pontos finais, o Gestor de tráfego seleciona o ponto final com a granularidade mais baixo e não considera o encaminhamento de pedidos em que região para o ponto final. Por exemplo, considere um perfil de tipo encaminhamento geográfico com dois pontos de extremidade - Endpoint1 e Endpoint2. Endpoint1 está configurado para receber o tráfego da Irlanda e Endpoint2 está configurado para receber o tráfego da Europa. Se um pedido tem origem na Irlanda, ele sempre será roteado para Endpoint1.
- Uma vez que uma região pode ser mapeada apenas para um ponto final, o Gestor de tráfego devolve a mesma, independentemente do ponto final está ou não em bom estado.

    >[!IMPORTANT]
    >É altamente recomendável que os clientes que utilizam o método de encaminhamento geográfico associá-la com os pontos finais de tipo aninhados com perfis de subordinados que contém, pelo menos, dois pontos de extremidade dentro de cada um.
- Se for encontrada uma correspondência de ponto final e pertença a esse ponto final a **parado** de estado, o Gestor de tráfego devolve uma resposta NODATA. Neste caso, não existem pesquisas mais são feitas posição superiores na hierarquia de região geográfica. Esse comportamento também é aplicável para tipos de ponto final aninhado quando o perfil de subordinado está no **parado** ou **desativado** estado.
- Se um ponto final apresenta uma **desativado** Estado, não ser incluído na região de processo de correspondência. Esse comportamento também é aplicável para tipos de ponto final aninhado quando o ponto final está no **desativado** estado.
- Se uma consulta for proveniente de uma região geográfica na qual não tem nenhum mapeamento daquele perfil, o Gestor de tráfego devolve uma resposta NODATA. Por conseguinte, recomendamos vivamente que os clientes utilizem o encaminhamento geográfico com um ponto final, o ideal é que, de tipo aninhados com, pelo menos, dois pontos de extremidade no perfil subordinado, com a região **mundo** atribuídos ao mesmo. Isto também garante que quaisquer endereços IP que não são mapeadas para uma região são processados.

Conforme explicado [como funciona o Gestor de tráfego](traffic-manager-how-it-works.md), Gestor de tráfego não recebe as consultas DNS diretamente a partir de clientes. Em vez disso, as consultas DNS são provenientes do serviço DNS recursivo que os clientes estão configurados para utilizar. Por conseguinte, o endereço IP utilizado para determinar a região não é o endereço IP do cliente, mas é o endereço IP do serviço DNS recursivo. Na prática, este endereço IP é um bom proxy para o cliente.

## <a name = "multivalue"></a>Método de encaminhamento de tráfego de vários valores
O **Multivalue** o método de encaminhamento de tráfego permite-lhe obter vários pontos de extremidade de bom estado de funcionamento numa única resposta de consulta DNS. Isto permite ao chamador para fazer o lado do cliente é repetida com outros pontos finais em caso de um ponto de extremidade retornado a ser não responde. Este padrão pode aumentar a disponibilidade de um serviço e reduzir a latência associada uma nova consulta DNS para obter um bom ponto de extremidade. Método de encaminhamento de valores múltiplos só funciona se todos os pontos finais do tipo 'Externo' e são especificados como IPv4 ou IPv6 endereços. Quando uma consulta é recebida para este perfil, todos os pontos finais de bom estado de funcionamento são devolvidos e estão sujeitos a uma contagem de retorna máxima configurável.

## <a name = "subnet"></a>Método de encaminhamento de tráfego de sub-rede
O **sub-rede** o método de encaminhamento de tráfego permite-lhe mapear um conjunto de intervalos de endereços IP do utilizador final para os pontos finais num perfil. Depois disso, se o Gestor de tráfego recebe uma consulta DNS para o perfil, ele irá inspecionar a origem de endereço IP dessa solicitação (na maioria dos casos será o endereço IP de saída do resolvedor DNS utilizado pelo chamador), determinar qual ponto de extremidade, ele é mapeado para e retornará t ponto final de Hat na resposta da consulta. 

O endereço IP a ser mapeado para um ponto de extremidade pode ser especificado como intervalos CIDR (por exemplo, 1.2.3.0/24) ou como um intervalo de endereços (por exemplo, 1.2.3.4-5.6.7.8). Os intervalos IP associados a um ponto final tem de ser exclusivo dentro desse perfil e não podem ter uma sobreposição com o conjunto de endereços IP de um ponto de extremidade diferente no mesmo perfil.
Se definir um ponto de extremidade com nenhum intervalo de endereços, que funciona como um tráfego de contingência e take de quaisquer sub-redes restantes. Se nenhum ponto de final de contingência está incluído, o Gestor de tráfego envia uma resposta NODATA para quaisquer intervalos indefinidos. Portanto, recomendamos que o define um ponto de final de contingência, caso contrário, certifique-se de que todos os intervalos IP possíveis são especificados nos seus pontos finais.

Encaminhamento de sub-rede pode ser utilizado para proporcionar uma experiência diferente para os utilizadores a ligar a partir de um espaço IP específico. Por exemplo, usando o roteamento de sub-rede, um cliente pode tornar todos os pedidos de seu escritório Corporativo ser encaminhado para um ponto de extremidade diferente em que eles podem testar uma única versão interna da sua aplicação. Outro cenário é se pretende fornecer uma experiência diferente aos utilizadores ligar a partir de um ISP específico (por exemplo, impedir que os utilizadores de um determinado ISP).

## <a name="next-steps"></a>Passos Seguintes

Saiba como desenvolver aplicações de elevada disponibilidade utilizando [monitorização de pontos finais do Gestor de tráfego](traffic-manager-monitoring.md)




