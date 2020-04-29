---
title: Azure Traffic Manager - métodos de encaminhamento de tráfego
description: Estes artigos ajudam-no a compreender os diferentes métodos de encaminhamento de tráfego usados pelo Traffic Manager
services: traffic-manager
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: rohink
ms.openlocfilehash: 4a035506943eeffa2c3fc4fec27c47da4136683b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79250910"
---
# <a name="traffic-manager-routing-methods"></a>Métodos de encaminhamento do Traffic Manager

O Azure Traffic Manager apoia seis métodos de encaminhamento de tráfego para determinar como encaminhar o tráfego da rede para os vários pontos finais do serviço. Para qualquer perfil, o Traffic Manager aplica o método de encaminhamento de tráfego associado a cada consulta de DNS que recebe. O método de encaminhamento de tráfego determina qual o ponto final devolvido na resposta do DNS.

Os seguintes métodos de encaminhamento de tráfego estão disponíveis no Traffic Manager:

* **[Prioridade](#priority-traffic-routing-method):** Selecione **Prioridade** quando pretender utilizar um ponto final de serviço primário para todo o tráfego e forneça cópias de segurança caso os pontos finais primários ou de backup não estejam disponíveis.
* **[Ponderado](#weighted):** Selecione **Ponderado** quando pretender distribuir o tráfego através de um conjunto de pontos finais, uniformemente ou de acordo com os pesos, que define.
* **[Desempenho](#performance):** Selecione **O desempenho** quando tiver pontos finais em diferentes localizações geográficas e pretende que os utilizadores finais utilizem o ponto final "mais próximo" em termos da menor latência da rede.
* **[Geographic](#geographic):** Select **Geographic** para que os utilizadores sejam direcionados para pontos finais específicos (Azure, External ou Nested) com base na localização geográfica de onde provém a sua consulta de DNS. Isto capacita os clientes do Traffic Manager a permitir cenários em que conhecer a região geográfica de um utilizador e encaminhar-se com base nisso é importante. Exemplos incluem o cumprimento de mandatos de soberania de dados, a localização de conteúdos & experiência do utilizador e a medição do tráfego de diferentes regiões.
* **[Multivalor](#multivalue):** Selecione **MultiValue** para perfis de Gestor de Tráfego que só podem ter endereços IPv4/IPv6 como pontos finais. Quando uma consulta é recebida para este perfil, todos os pontos finais saudáveis são devolvidos.
* **[Subnet](#subnet):** Selecione o método de encaminhamento de tráfego **subnet** para mapear conjuntos de endereços IP do utilizador final para um ponto final específico dentro de um perfil do Gestor de Tráfego. Quando um pedido é recebido, o ponto final devolvido será o traçado para o endereço IP de origem desse pedido. 


Todos os perfis do Traffic Manager incluem monitorização da saúde do ponto final e falha automática do ponto final. Para mais informações, consulte a [Monitorização do Ponto final do Gestor de Tráfego](traffic-manager-monitoring.md). Um único perfil do Gestor de Tráfego só pode utilizar um método de encaminhamento de tráfego. Pode selecionar um método de encaminhamento de tráfego diferente para o seu perfil a qualquer momento. As alterações são aplicadas dentro de um minuto e não é incorrido tempo de inatividade. Os métodos de encaminhamento de tráfego podem ser combinados utilizando perfis aninhados do Traffic Manager. O nidificação permite configurações sofisticadas e flexíveis de encaminhamento de tráfego que atendam às necessidades de aplicações maiores e complexas. Para mais informações, consulte [os perfis do Gestor de Tráfego.](traffic-manager-nested-profiles.md)

## <a name="priority-traffic-routing-method"></a>Método prioritário de encaminhamento de tráfego

Muitas vezes, uma organização quer fornecer fiabilidade aos seus serviços, implantando um ou mais serviços de backup no caso de o seu serviço primário diminuir. O método de encaminhamento de tráfego 'Priority' permite aos clientes da Azure implementar facilmente este padrão de failover.

![Método de encaminhamento de tráfego "prioritário" do Gestor de Tráfego do Azure](media/traffic-manager-routing-methods/priority.png)

O perfil do Gestor de Tráfego contém uma lista prioritária de pontos finais de serviço. Por predefinição, o Gestor de Tráfego envia todo o tráfego para o ponto final primário (com a prioridade mais alta). Se o ponto final primário não estiver disponível, o Gestor de Tráfego encaminha o tráfego para o segundo ponto final. Se os pontos finais primário e secundário não estiverem disponíveis, o tráfego passa para o terceiro e assim por diante. A disponibilidade do ponto final baseia-se no estado configurado (ativado ou desativado) e na monitorização contínua do ponto final.

### <a name="configuring-endpoints"></a>Configurar pontos finais

Com o Gestor de Recursos Azure, configura a prioridade do ponto final utilizando explicitamente a propriedade "prioritária" para cada ponto final. Esta propriedade tem um valor entre 1 e 1000. Valores mais baixos representam uma prioridade maior. Os pontos finais não podem partilhar valores prioritários. A definição da propriedade é opcional. Quando omitida, é utilizada uma prioridade predefinida com base na ordem final.

## <a name="weighted-traffic-routing-method"></a><a name = "weighted"></a>Método ponderado de encaminhamento de tráfego
O método de encaminhamento de tráfego 'ponderado' permite-lhe distribuir o tráfego uniformemente ou utilizar uma ponderação pré-definida.

![Método de encaminhamento de tráfego 'ponderado' do gestor de tráfego da Azure](media/traffic-manager-routing-methods/weighted.png)

No método de encaminhamento de tráfego ponderado, atribui um peso a cada ponto final na configuração do perfil do Gestor de Tráfego. A ponderação é um número inteiro entre 1 e 1000. Este parâmetro é opcional. Se omitir, os Gestores de Tráfego utilizam um peso padrão de '1'. Quanto maior o peso, maior a prioridade.

Para cada consulta DNS recebida, o Gestor de Tráfego seleciona aleatoriamente um ponto final disponível. A probabilidade de um ponto final ser selecionado baseia-se nos pesos atribuídos a todos os pontos finais disponíveis. Usar o mesmo peso em todos os pontos finais resulta numa distribuição uniforme do tráfego. A utilização de pesos mais altos ou mais baixos em pontos finais específicos faz com que esses pontos finais sejam devolvidos com mais ou menos frequência nas respostas do DNS.

O método ponderado permite alguns cenários úteis:

* Atualização gradual das aplicações: Alocar uma percentagem de tráfego para um novo ponto final e aumentar gradualmente o tráfego ao longo do tempo para 100%.
* Migração de candidaturas para Azure: Criar um perfil com pontos finais Azure e externos. Ajuste o peso dos pontos finais para preferir os novos pontos finais.
* Cloud-bursting para capacidade adicional: Expanda rapidamente uma implantação no local para a nuvem colocando-a atrás de um perfil do Gestor de Tráfego. Quando necessitar de capacidade extra na nuvem, pode adicionar ou ativar mais pontos finais e especificar que parte do tráfego vai para cada ponto final.

Além de utilizar o portal Azure, pode configurar pesos utilizando o Azure PowerShell, CLI e as APIs REST.

É importante entender que as respostas dNS são cached por clientes e pelos servidores DNS recursivos que os clientes usam para resolver nomes DNS. Este cache pode ter um impacto nas distribuições de tráfego ponderadas. Quando o número de clientes e servidores DNS recursivos é grande, a distribuição de tráfego funciona como esperado. No entanto, quando o número de clientes ou servidores DNS recursivos é pequeno, o cache pode alterar significativamente a distribuição de tráfego.

Os casos de utilização comum incluem:

* Ambientes de desenvolvimento e de testes
* Comunicações de aplicação para aplicação
* Aplicações destinadas a uma base de utilizadores estreita que partilham uma infraestrutura dNS recursiva comum (por exemplo, funcionários da empresa que ligam através de um proxy)

Estes efeitos de cache dNS são comuns a todos os sistemas de encaminhamento de tráfego baseados em DNS, e não apenas ao Azure Traffic Manager. Em alguns casos, limpar explicitamente a cache DNS pode fornecer uma supor. Noutros casos, um método alternativo de encaminhamento de tráfego pode ser mais adequado.

## <a name="performance-traffic-routing-method"></a><a name = "performance"></a>Método de encaminhamento de tráfego de desempenho

A implantação de pontos finais em dois ou mais locais em todo o mundo pode melhorar a capacidade de resposta de muitas aplicações, encaminhando o tráfego para o local que está "mais próximo" de si. O método de encaminhamento de tráfego 'Performance' fornece esta capacidade.

![Método de encaminhamento de tráfego 'Performance' do Gestor de Tráfego da Azure](media/traffic-manager-routing-methods/performance.png)

O ponto final "mais próximo" não é necessariamente o mais próximo medido pela distância geográfica. Em vez disso, o método de encaminhamento de tráfego "Desempenho" determina o ponto final mais próximo medindo a latência da rede. O Traffic Manager mantém uma Tabela de Latência da Internet para acompanhar o tempo de ida e volta entre as gamas de endereços IP e cada datacenter Azure.

O Gestor de Tráfego procura o endereço IP de origem do pedido de DNS na Tabela de Latência da Internet. O Traffic Manager escolhe então um ponto final disponível no centro de dados Azure que tem a menor latência para essa gama de endereços IP, e devolve esse ponto final na resposta do DNS.

Como explicado em Como funciona o Gestor de [Tráfego,](traffic-manager-how-it-works.md)o Gestor de Tráfego não recebe consultas de DNS diretamente dos clientes. Pelo contrário, as consultas dNS provêm do serviço DNS recursivo que os clientes estão configurados para usar. Por conseguinte, o endereço IP utilizado para determinar o ponto final 'mais próximo' não é o endereço IP do cliente, mas é o endereço IP do serviço DNS recursivo. Na prática, este endereço IP é um bom representante para o cliente.


O Traffic Manager atualiza regularmente a Tabela de Latência da Internet para ter em conta as mudanças na Internet global e nas novas regiões do Azure. No entanto, o desempenho da aplicação varia em função das variações em tempo real na carga através da Internet. O encaminhamento de tráfego de desempenho não monitoriza a carga num determinado ponto final de serviço. No entanto, se um ponto final ficar indisponível, o Gestor de Tráfego não o inclui nas respostas de consulta do DNS.


Pontos a notar:

* Se o seu perfil contiver vários pontos finais na mesma região de Azure, então o Traffic Manager distribui o tráfego uniformemente pelos pontos finais disponíveis naquela região. Se preferir uma distribuição de tráfego diferente dentro de uma região, pode utilizar [perfis aninhados do Traffic Manager](traffic-manager-nested-profiles.md).
* Se todos os pontos finais habilitados na região de Azure mais próxima estiverem degradados, o Gestor de Tráfego desloca o tráfego para os pontos finais da região de Azure mais próxima. Se quiser definir uma sequência de failover preferida, utilize [perfis aninhados do Traffic Manager](traffic-manager-nested-profiles.md).
* Ao utilizar o método de encaminhamento de tráfego de desempenho com pontos finais externos ou pontos finais aninhados, tem de especificar a localização desses pontos finais. Escolha a região de Azure mais próxima da sua implantação. Esses locais são os valores suportados pela Tabela de Latência da Internet.
* O algoritmo que escolhe o ponto final é determinista. As consultas repetidas de DNS do mesmo cliente são direcionadas para o mesmo ponto final. Normalmente, os clientes usam diferentes servidores DNS recursivos durante a viagem. O cliente pode ser encaminhado para outro ponto final. O encaminhamento também pode ser afetado por atualizações para a Tabela de Latência da Internet. Por isso, o método de encaminhamento de tráfego de desempenho não garante que um cliente seja sempre encaminhado para o mesmo ponto final.
* Quando a Tabela de Latência da Internet mudar, poderá notar que alguns clientes são direcionados para um ponto final diferente. Esta mudança de encaminhamento é mais precisa com base nos dados atuais da latência. Estas atualizações são essenciais para manter a precisão do tráfego de desempenho à medida que a Internet evolui continuamente.

## <a name="geographic-traffic-routing-method"></a><a name = "geographic"></a>Método geográfico de encaminhamento de tráfego

Os perfis do Traffic Manager podem ser configurados para utilizar o método de encaminhamento Geográfico para que os utilizadores sejam direcionados para pontos finais específicos (Azure, External ou Nested) com base na localização geográfica de onde provém a sua consulta de DNS. Isto capacita os clientes do Traffic Manager a permitir cenários em que conhecer a região geográfica de um utilizador e encaminhar-se com base nisso é importante. Exemplos incluem o cumprimento de mandatos de soberania de dados, a localização de conteúdos & experiência do utilizador e a medição do tráfego de diferentes regiões.
Quando um perfil é configurado para encaminhamento geográfico, cada ponto final associado a esse perfil precisa de ter um conjunto de regiões geográficas atribuídas a ele. Uma região geográfica pode estar a seguir níveis de granularidade 
- Mundo: qualquer região
- Agrupamento Regional – por exemplo, África, Médio Oriente, Austrália/Pacífico, etc. 
- País/Região – por exemplo, Irlanda, Peru, Hong Kong SAR etc. 
- Estado/Província – por exemplo, EUA-Califórnia, Austrália-Queensland, Canadá-Alberta etc. (nota: este nível de granularidade é apoiado apenas para estados/províncias na Austrália, Canadá e EUA).

Quando uma região ou um conjunto de regiões são atribuídos a um ponto final, quaisquer pedidos dessas regiões são encaminhados apenas para esse ponto final. O Traffic Manager utiliza o endereço IP de origem da consulta dNS para determinar a região de onde um utilizador está a consultar – normalmente este é o endereço IP do DNS local que faz a consulta em nome do utilizador.  

![Método de encaminhamento de tráfego 'Geographic' do Gestor de Tráfego da Azure](./media/traffic-manager-routing-methods/geographic.png)

O Gestor de Tráfego lê o endereço IP de origem da consulta do DNS e decide de que região geográfica é originária. Em seguida, procura ver se há um ponto final que tenha esta região geográfica mapeada para ela. Esta procura começa no nível mais baixo de granularidade (Estado/Província onde é apoiada, então ao nível país/região) e vai até ao nível mais alto, que é **o Mundo**. O primeiro jogo encontrado usando este traversal é designado como o ponto final para voltar na resposta de consulta. Ao combinar com um ponto final do tipo Nested, um ponto final dentro desse perfil da criança é devolvido, com base no seu método de encaminhamento. Aplicam-se a este comportamento os seguintes pontos:

- Uma região geográfica só pode ser mapeada para um ponto final num perfil do Traffic Manager quando o tipo de encaminhamento é O Encaminhamento Geográfico. Isto garante que o encaminhamento de utilizadores é determinista, e os clientes podem ativar cenários que requerem limites geográficos inequívocos.
- Se a região de um utilizador se submeter a dois pontos de referência diferentes, o Traffic Manager seleciona o ponto final com a menor granularidade e não considera os pedidos de encaminhamento dessa região para o outro ponto final. Por exemplo, considere um perfil do tipo Deenamento Geográfico com dois pontos finais - Endpoint1 e Endpoint2. Endpoint1 está configurado para receber tráfego da Irlanda e Endpoint2 está configurado para receber tráfego da Europa. Se um pedido for originário da Irlanda, é sempre encaminhado para Endpoint1.
- Uma vez que uma região pode ser mapeada apenas para um ponto final, o Traffic Manager devolve-o independentemente de o ponto final ser saudável ou não.

    >[!IMPORTANT]
    >Recomenda-se vivamente que os clientes que utilizam o método de encaminhamento geográfico o associem aos pontos finais do tipo Nested que têm perfis infantis que contenham pelo menos dois pontos finais dentro de cada um.
- Se for encontrada uma correspondência de ponto final e esse ponto final estiver no estado **de parado,** o Gestor de Tráfego devolve uma resposta NODATA. Neste caso, não se fazem mais investigações na hierarquia da região geográfica. Este comportamento também é aplicável para tipos de pontos finais aninhados quando o perfil da criança está no estado **de parada** ou **deficiente.**
- Se um ponto final apresentar um estado **de desativação,** não será incluído no processo de correspondência da região. Este comportamento também é aplicável para tipos de pontos finais aninhados quando o ponto final está no estado **de desativação.**
- Se uma consulta vem de uma região geográfica que não tem mapeamento nesse perfil, o Gestor de Tráfego devolve uma resposta NODATA. Por isso, recomenda-se vivamente que os clientes utilizem o encaminhamento geográfico com um ponto final, idealmente de tipo Nested com pelo menos dois pontos finais dentro do perfil da criança, com a região **Mundo** a atribuir-lhe. Isto também garante que quaisquer endereços IP que não mapeiem para uma região sejam tratados.

Como explicado em Como funciona o Gestor de [Tráfego,](traffic-manager-how-it-works.md)o Gestor de Tráfego não recebe consultas de DNS diretamente dos clientes. Pelo contrário, as consultas dNS provêm do serviço DNS recursivo que os clientes estão configurados para usar. Portanto, o endereço IP utilizado para determinar a região não é o endereço IP do cliente, mas é o endereço IP do serviço DNS recursivo. Na prática, este endereço IP é um bom representante para o cliente.

### <a name="faqs"></a>FAQs

* [Quais são os casos de uso em que o encaminhamento geográfico é útil?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-some-use-cases-where-geographic-routing-is-useful)

* [Como decido se devo usar o método de encaminhamento do desempenho ou o método de encaminhamento geográfico?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-do-i-decide-if-i-should-use-performance-routing-method-or-geographic-routing-method)

* [Quais são as regiões que são apoiadas pelo Gestor de Tráfego para o encaminhamento geográfico?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-the-regions-that-are-supported-by-traffic-manager-for-geographic-routing)

* [Como é que o gestor de tráfego determina de onde vem o utilizador?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-traffic-manager-determine-where-a-user-is-querying-from)

* [É garantido que o Gestor de Tráfego pode determinar corretamente a localização geográfica exata do utilizador em todos os casos?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#is-it-guaranteed-that-traffic-manager-can-correctly-determine-the-exact-geographic-location-of-the-user-in-every-case)

* [Será que um ponto final precisa de ser fisicamente localizado na mesma região que aquele com que está configurado para o encaminhamento geográfico?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#does-an-endpoint-need-to-be-physically-located-in-the-same-region-as-the-one-it-is-configured-with-for-geographic-routing)

* [Posso atribuir às regiões geográficas pontos finais num perfil que não está configurado para fazer encaminhamento geográfico?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-assign-geographic-regions-to-endpoints-in-a-profile-that-is-not-configured-to-do-geographic-routing)

* [Porque é que estou a ter um erro quando tento mudar o método de encaminhamento de um perfil existente para a Geographic?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#why-am-i-getting-an-error-when-i-try-to-change-the-routing-method-of-an-existing-profile-to-geographic)

* [Por que é fortemente recomendado que os clientes criem perfis aninhados em vez de pontos finais sob um perfil com encaminhamento geográfico habilitado?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#why-is-it-strongly-recommended-that-customers-create-nested-profiles-instead-of-endpoints-under-a-profile-with-geographic-routing-enabled)

* [Existem restrições na versão API que suporte este tipo de encaminhamento?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#are-there-any-restrictions-on-the-api-version-that-supports-this-routing-type)

## <a name="multivalue-traffic-routing-method"></a><a name = "multivalue"></a>Método de encaminhamento de tráfego multivalorizado
O método de encaminhamento de tráfego **Multivalue** permite-lhe obter múltiplos pontos finais saudáveis numa única resposta de consulta dNS. Isto permite ao ouvinte fazer repetições do lado do cliente com outros pontos finais em caso de um ponto final devolvido não ser respondido. Este padrão pode aumentar a disponibilidade de um serviço e reduzir a latência associada a uma nova consulta DNS para obter um ponto final em bom estado de funcionamento. O método de encaminhamento MultiValue só funciona se todos os pontos finais do tipo 'Externo' e forem especificados como endereços IPv4 ou IPv6. Quando uma consulta é recebida para este perfil, todos os pontos finais saudáveis são devolvidos e estão sujeitos a uma contagem máxima de retorno configurável.

### <a name="faqs"></a>FAQs

* [Quais são os casos de utilização em que o encaminhamento MultiValue é útil?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-some-use-cases-where-multivalue-routing-is-useful)

* [Quantos pontos finais são devolvidos quando o encaminhamento MultiValue é usado?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-many-endpoints-are-returned-when-multivalue-routing-is-used)

* [Vou ter o mesmo conjunto de pontos finais quando o encaminhamento MultiValue for usado?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#will-i-get-the-same-set-of-endpoints-when-multivalue-routing-is-used)

## <a name="subnet-traffic-routing-method"></a><a name = "subnet"></a>Método de encaminhamento de tráfego de sub-rede
O método de encaminhamento de tráfego **subnet** permite-lhe mapear um conjunto de endereços IP do utilizador final para pontos finais específicos num perfil. Depois disso, se o Gestor de Tráfego receber uma consulta de DNS para esse perfil, irá inspecionar o endereço IP de origem desse pedido (na maioria dos casos este será o endereço IP de saída do DNS utilizado pelo chamador), determinará a que ponto final está mapeado e devolverá esse ponto final na resposta à consulta. 

O endereço IP a ser mapeado para um ponto final pode ser especificado como gamas CIDR (por exemplo, 1.2.3.0/24) ou como uma gama de endereços (por exemplo, 1.2.3.4-5.6.7.8). As gamas IP associadas a um ponto final têm de ser únicas dentro desse perfil e não podem ter uma sobreposição com o conjunto de endereçoIP de um ponto final diferente no mesmo perfil.
Se definir um ponto final sem alcance de endereço, isso funciona como um recuo e retira o tráfego de quaisquer subredes restantes. Se não for incluído nenhum ponto final de recuo, o Traffic Manager envia uma resposta NODATA para quaisquer intervalos indefinidas. Por isso, é altamente recomendável que defina um ponto final de recuo, ou então certifique-se de que todas as gamas ip possíveis são especificadas em todos os seus pontos finais.

O encaminhamento de sub-redes pode ser usado para proporcionar uma experiência diferente para os utilizadores que se conectam a partir de um espaço IP específico. Por exemplo, ao utilizar o encaminhamento de sub-rede, um cliente pode fazer com que todos os pedidos do escritório empresarial sejam encaminhados para um ponto final diferente onde podem estar a testar uma versão apenas interna da sua aplicação. Outro cenário possível seria se quisesse fornecer uma experiência diferente aos utilizadores que estabelecem ligação a um ISP específico (por exemplo, bloquear utilizadores de um determinado ISP).

### <a name="faqs"></a>FAQs

* [Quais são os casos de utilização em que o encaminhamento de sub-rede é útil?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-some-use-cases-where-subnet-routing-is-useful)

* [Como é que o Gestor de Tráfego conhece o endereço IP do utilizador final?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-traffic-manager-know-the-ip-address-of-the-end-user)

* [Como posso especificar endereços IP ao utilizar o encaminhamento subnet?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-specify-ip-addresses-when-using-subnet-routing)

* [Como posso especificar um ponto final de recuo quando se usa o encaminhamento subnet?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-specify-a-fallback-endpoint-when-using-subnet-routing)

* [O que acontece se um ponto final for desativado num perfil do tipo de encaminhamento subnet?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-happens-if-an-endpoint-is-disabled-in-a-subnet-routing-type-profile)


## <a name="next-steps"></a>Passos seguintes

Saiba como desenvolver aplicações de alta disponibilidade utilizando monitorização de [pontofinal do Gestor](traffic-manager-monitoring.md) de Tráfego




