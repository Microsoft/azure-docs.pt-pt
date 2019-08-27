---
title: Gerenciador de tráfego do Azure-métodos de roteamento de tráfego
description: Este artigo ajuda você a entender os diferentes métodos de roteamento de tráfego usados pelo Gerenciador de tráfego
services: traffic-manager
author: asudbring
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: allensu
ms.openlocfilehash: 06ce7fb5d18920be6f71821b034dc13061c60032
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051454"
---
# <a name="traffic-manager-routing-methods"></a>Métodos de encaminhamento do Traffic Manager

O Gerenciador de tráfego do Azure dá suporte a seis métodos de roteamento de tráfego para determinar como rotear o tráfego de rede para os vários pontos de extremidade de serviço. Para qualquer perfil, o Gerenciador de tráfego aplica o método de roteamento de tráfego associado a ele a cada consulta DNS recebida. O método de roteamento de tráfego determina qual ponto de extremidade é retornado na resposta DNS.

Os seguintes métodos de roteamento de tráfego estão disponíveis no Gerenciador de tráfego:

* **[Prioridade](#priority):** Selecione **prioridade** quando desejar usar um ponto de extremidade de serviço primário para todo o tráfego e forneça backups caso os pontos de extremidades primário ou de backup não estejam disponíveis.
* **[Weighted](#weighted):** Selecione **ponderado** quando desejar distribuir o tráfego entre um conjunto de pontos de extremidade, seja uniformemente ou de acordo com os pesos que você definir.
* **[Desempenho](#performance):** Selecione **desempenho** quando você tiver pontos de extremidade em diferentes localizações geográficas e desejar que os usuários finais usem o ponto "mais próximo" em termos da menor latência de rede.
* **[Geográfico](#geographic):** Selecione **geográfico** para que os usuários sejam direcionados para pontos de extremidade específicos (Azure, externo ou aninhado) com base no local geográfico do qual sua consulta DNS provém. Isso permite que os clientes do Traffic Manager habilitem cenários em que conhecer a região geográfica de um usuário e roteá-los com base no que é importante. Os exemplos incluem conformidade com as normas da soberania de dados, localização de conteúdo & experiência do usuário e medição do tráfego de diferentes regiões.
* **Vários [valores](#multivalue):** Selecione vários **valores** para perfis do Gerenciador de tráfego que só podem ter endereços IPv4/IPv6 como pontos de extremidade. Quando uma consulta é recebida para esse perfil, todos os pontos de extremidade íntegros são retornados.
* **[Sub-rede](#subnet):** Selecione o método de roteamento de tráfego de **sub-rede** para mapear conjuntos de intervalos de endereços IP de usuário final para um ponto de extremidade específico dentro de um perfil do Gerenciador de tráfego. Quando uma solicitação é recebida, o ponto de extremidade retornado será aquele mapeado para o endereço IP de origem da solicitação. 


Todos os perfis do Gerenciador de tráfego incluem monitoramento de integridade do ponto de extremidade e failover automático do ponto de extremidade. Para obter mais informações, consulte [monitoramento de ponto de extremidade do Traffic Manager](traffic-manager-monitoring.md). Um único perfil do Gerenciador de tráfego pode usar apenas um método de roteamento de tráfego. Você pode selecionar um método de roteamento de tráfego diferente para seu perfil a qualquer momento. As alterações são aplicadas dentro de um minuto e nenhum tempo de inatividade é incorrido. Os métodos de roteamento de tráfego podem ser combinados usando perfis aninhados do Gerenciador de tráfego. O aninhamento permite configurações de roteamento de tráfego sofisticadas e flexíveis que atendem às necessidades de aplicativos maiores e complexos. Para obter mais informações, consulte [perfis aninhados do Gerenciador de tráfego](traffic-manager-nested-profiles.md).

## <a name = "priority"></a>Método de roteamento de tráfego prioritário

Muitas vezes, uma organização deseja fornecer confiabilidade para seus serviços implantando um ou mais serviços de backup caso seu serviço primário fique inativo. O método de roteamento de tráfego de "prioridade" permite que os clientes do Azure implementem facilmente esse padrão de failover.

![Método de roteamento de tráfego de "prioridade" do Gerenciador de tráfego do Azure](media/traffic-manager-routing-methods/priority.png)

O perfil do Gerenciador de tráfego contém uma lista priorizada de pontos de extremidade de serviço. Por padrão, o Gerenciador de tráfego envia todo o tráfego para o ponto de extremidade primário (prioridade mais alta). Se o ponto de extremidade primário não estiver disponível, o Gerenciador de tráfego roteará o tráfego para o segundo ponto de extremidade. Se os pontos de extremidade primários e secundários não estiverem disponíveis, o tráfego passará para o terceiro e assim por diante. A disponibilidade do ponto de extremidade é baseada no status configurado (habilitado ou desabilitado) e no monitoramento contínuo do ponto de extremidade.

### <a name="configuring-endpoints"></a>Configurando pontos de extremidade

Com Azure Resource Manager, você configura a prioridade do ponto de extremidade explicitamente usando a propriedade ' priority ' para cada ponto de extremidade. Essa propriedade é um valor entre 1 e 1000. Os valores mais baixos representam uma prioridade mais alta. Os pontos de extremidade não podem compartilhar valores de prioridade. A definição da propriedade é opcional. Quando omitido, uma prioridade padrão baseada na ordem do ponto de extremidade é usada.

## <a name = "weighted"></a>Método de roteamento de tráfego ponderado
O método de roteamento de tráfego "ponderado" permite distribuir o tráfego uniformemente ou usar um peso predefinido.

![Método de roteamento de tráfego "ponderado" do Gerenciador de tráfego do Azure](media/traffic-manager-routing-methods/weighted.png)

No método de roteamento de tráfego ponderado, você atribui um peso a cada ponto de extremidade na configuração do perfil do Gerenciador de tráfego. A ponderação é um número inteiro entre 1 e 1000. Esse parâmetro é opcional. Se omitido, os gerenciadores de tráfego usarão um peso padrão de ' 1 '. Quanto maior o peso, maior a prioridade.

Para cada consulta DNS recebida, o Gerenciador de tráfego escolhe aleatoriamente um ponto de extremidade disponível. A probabilidade de escolher um ponto de extremidade é baseada nos pesos atribuídos a todos os pontos de extremidade disponíveis. Usar o mesmo peso em todos os pontos de extremidade resulta em uma distribuição de tráfego par. Usar pesos maiores ou menores em pontos de extremidade específicos faz com que esses pontos de extremidade sejam retornados com mais ou menos frequência nas respostas de DNS.

O método ponderado habilita alguns cenários úteis:

* Atualização gradual do aplicativo: Aloque um percentual de tráfego para rotear para um novo ponto de extremidade e aumente gradualmente o tráfego ao longo do tempo para 100%.
* Migração de aplicativos para o Azure: Crie um perfil com os pontos de extremidade do Azure e externos. Ajuste o peso dos pontos de extremidade para preferir os novos pontos de extremidade.
* Disparo de nuvem para capacidade adicional: Expanda rapidamente uma implantação local na nuvem colocando-a atrás de um perfil do Gerenciador de tráfego. Quando você precisar de capacidade extra na nuvem, você pode adicionar ou habilitar mais pontos de extremidade e especificar qual parte do tráfego vai para cada ponto.

Além de usar o portal do Azure, você pode configurar pesos usando Azure PowerShell, CLI e as APIs REST.

É importante entender que as respostas DNS são armazenadas em cache por clientes e pelos servidores DNS recursivos que os clientes usam para resolver nomes DNS. Esse cache pode afetar as distribuições de tráfego ponderado. Quando o número de clientes e servidores DNS recursivos for grande, a distribuição de tráfego funcionará conforme o esperado. No entanto, quando o número de clientes ou servidores DNS recursivos é pequeno, o cache pode distorcer significativamente a distribuição do tráfego.

Os casos de uso comuns incluem:

* Ambientes de desenvolvimento e teste
* Comunicação entre aplicativos
* Aplicativos destinados a uma base de usuários restrita que compartilham uma infraestrutura de DNS recursivo comum (por exemplo, funcionários da empresa se conectando por meio de um proxy)

Esses efeitos de cache de DNS são comuns a todos os sistemas de roteamento de tráfego baseados em DNS, não apenas ao Gerenciador de tráfego do Azure. Em alguns casos, limpar explicitamente o cache DNS pode fornecer uma solução alternativa. Em outros casos, um método alternativo de roteamento de tráfego pode ser mais apropriado.

## <a name = "performance"></a>Método de roteamento de tráfego de desempenho

A implantação de pontos de extremidade em dois ou mais locais em todo o mundo pode melhorar a capacidade de resposta de muitos aplicativos roteando o tráfego para o local mais próximo de você. O método de roteamento de tráfego de "desempenho" fornece esse recurso.

![Método de roteamento de tráfego de "desempenho" do Gerenciador de tráfego do Azure](media/traffic-manager-routing-methods/performance.png)

O ponto de extremidade "mais próximo" não é necessariamente mais próximo que medido por distância geográfica. Em vez disso, o método de roteamento de tráfego de "desempenho" determina o ponto de extremidade mais próximo medindo a latência de rede. O Gerenciador de tráfego mantém uma tabela de latência da Internet para acompanhar o tempo de ida e volta entre os intervalos de endereços IP e cada Datacenter do Azure.

O Gerenciador de tráfego pesquisa o endereço IP de origem da solicitação DNS de entrada na tabela de latência da Internet. O Gerenciador de tráfego escolhe um ponto de extremidade disponível no datacenter do Azure que tem a menor latência para esse intervalo de endereços IP e retorna esse ponto de extremidade na resposta DNS.

Conforme explicado em [como funciona o Gerenciador de tráfego](traffic-manager-how-it-works.md), o Gerenciador de tráfego não recebe consultas DNS diretamente dos clientes. Em vez disso, as consultas DNS são provenientes do serviço DNS recursivo que os clientes estão configurados para usar. Portanto, o endereço IP usado para determinar o ponto de extremidade "mais próximo" não é o endereço IP do cliente, mas é o endereço IP do serviço DNS recursivo. Na prática, esse endereço IP é um bom proxy para o cliente.


O Gerenciador de tráfego atualiza regularmente a tabela de latência da Internet para considerar alterações na Internet global e em novas regiões do Azure. No entanto, o desempenho do aplicativo varia de acordo com as variações em tempo real na carga pela Internet. O roteamento de tráfego de desempenho não monitora a carga em um determinado ponto de extremidade de serviço. No entanto, se um ponto de extremidade ficar indisponível, o Gerenciador de tráfego não o incluirá em respostas de consulta DNS.


Pontos a serem observados:

* Se o seu perfil contiver vários pontos de extremidade na mesma região do Azure, o Gerenciador de tráfego distribuirá o tráfego uniformemente entre os pontos de extremidade disponíveis nessa região. Se preferir uma distribuição de tráfego diferente dentro de uma região, você poderá usar [perfis aninhados do Gerenciador de tráfego](traffic-manager-nested-profiles.md).
* Se todos os pontos de extremidade habilitados na região do Azure mais próxima estiverem degradados, o Gerenciador de tráfego moverá o tráfego para os pontos de extremidade na próxima região do Azure. Se você quiser definir uma sequência de failover preferencial, use [perfis aninhados do Gerenciador de tráfego](traffic-manager-nested-profiles.md).
* Ao usar o método de roteamento de tráfego de desempenho com pontos de extremidade externos ou pontos de extremidade aninhados, você precisa especificar o local desses pontos de extremidade. Escolha a região do Azure mais próxima de sua implantação. Esses locais são os valores suportados pela tabela de latência da Internet.
* O algoritmo que escolhe o ponto de extremidade é determinístico. Consultas DNS repetidas do mesmo cliente são direcionadas para o mesmo ponto de extremidade. Normalmente, os clientes usam diferentes servidores DNS recursivos ao viajar. O cliente pode ser roteado para um ponto de extremidade diferente. O roteamento também pode ser afetado pelas atualizações na tabela de latência da Internet. Portanto, o método de roteamento de tráfego de desempenho não garante que um cliente sempre seja roteado para o mesmo ponto de extremidade.
* Quando a tabela de latência da Internet é alterada, você pode observar que alguns clientes são direcionados para um ponto de extremidade diferente. Essa alteração de roteamento é mais precisa com base nos dados de latência atuais. Essas atualizações são essenciais para manter a precisão do roteamento de tráfego de desempenho à medida que a Internet evolui continuamente.

## <a name = "geographic"></a>Método de roteamento de tráfego geográfico

Os perfis do Gerenciador de tráfego podem ser configurados para usar o método de roteamento geográfico para que os usuários sejam direcionados para pontos de extremidade específicos (Azure, externo ou aninhado) com base no local geográfico do qual sua consulta DNS provém. Isso permite que os clientes do Traffic Manager habilitem cenários em que conhecer a região geográfica de um usuário e roteá-los com base no que é importante. Os exemplos incluem conformidade com as normas da soberania de dados, localização de conteúdo & experiência do usuário e medição do tráfego de diferentes regiões.
Quando um perfil é configurado para roteamento geográfico, cada ponto de extremidade associado a esse perfil precisa ter um conjunto de regiões geográficas atribuídas a ele. Uma região geográfica pode estar nos seguintes níveis de granularidade 
- Mundo – qualquer região
- Agrupamento regional – por exemplo, África, Oriente Médio, Austrália/Pacífico, etc. 
- País/região – por exemplo, Irlanda, Peru, RAE de Hong Kong, etc. 
- Estado/província – por exemplo, EUA-Califórnia, Austrália-Queensland, Canadá-Alberta, etc. (Observação: esse nível de granularidade tem suporte apenas para Estados/províncias na Austrália, no Canadá e nos EUA).

Quando uma região ou um conjunto de regiões é atribuído a um ponto de extremidade, todas as solicitações dessas regiões são roteadas somente para esse ponto de extremidade. O Gerenciador de tráfego usa o endereço IP de origem da consulta DNS para determinar a região da qual um usuário está consultando – geralmente esse é o endereço IP do resolvedor DNS local que faz a consulta em nome do usuário.  

![Método de roteamento de tráfego "geográfico" do Gerenciador de tráfego do Azure](./media/traffic-manager-routing-methods/geographic.png)

O Gerenciador de tráfego lê o endereço IP de origem da consulta DNS e decide em qual região geográfica ela se origina. Em seguida, ele procura ver se há um ponto de extremidade que tenha essa região geográfica mapeada para ele. Essa pesquisa começa no nível de granularidade mais baixo (estado/província onde há suporte, mais no nível de país/região) e passa até o nível mais alto, que é o **mundo**. A primeira correspondência encontrada usando essa passagem é designada como o ponto de extremidade para retornar na resposta da consulta. Ao fazer a correspondência com um ponto de extremidade de tipo aninhado, um ponto de extremidade dentro desse perfil filho é retornado, com base em seu método de roteamento. Os seguintes pontos são aplicáveis a esse comportamento:

- Uma região geográfica pode ser mapeada somente para um ponto de extremidade em um perfil do Gerenciador de tráfego quando o tipo de roteamento é roteamento geográfico. Isso garante que o roteamento de usuários seja determinístico e os clientes possam habilitar cenários que exigem limites geográficos não ambíguos.
- Se a região de um usuário estiver sob dois mapeamentos geográficos diferentes de pontos de extremidade, o Gerenciador de tráfego selecionará o ponto de extremidade com a menor granularidade e não considerará as solicitações de roteamento dessa região para o outro ponto de extremidade. Por exemplo, considere um perfil de tipo de roteamento geográfico com dois pontos de extremidade-Endpoint1 e endpoint2. O Endpoint1 está configurado para receber tráfego da Irlanda e o endpoint2 está configurado para receber tráfego da Europa. Se uma solicitação for originada na Irlanda, ela será sempre roteada para Endpoint1.
- Como uma região pode ser mapeada somente para um ponto de extremidade, o Gerenciador de tráfego a retorna, independentemente de o ponto de extremidade estar íntegro ou não.

    >[!IMPORTANT]
    >É altamente recomendável que os clientes que usam o método de roteamento geográfico o associem aos pontos de extremidade de tipo aninhado que têm perfis filho contendo pelo menos dois pontos de extremidade dentro de cada um.
- Se uma correspondência de ponto de extremidade for encontrada e esse ponto de extremidade estiver no estado **parado** , o Gerenciador de tráfego retornará uma resposta NODATA. Nesse caso, nenhuma pesquisa adicional é feita na hierarquia da região geográfica. Esse comportamento também se aplica a tipos de ponto de extremidade aninhados quando o perfil filho está no estado **parado** ou **desabilitado** .
- Se um ponto de extremidade exibir um status desabilitado, ele não será incluído no processo de correspondência de região. Esse comportamento também se aplica a tipos de ponto de extremidade aninhados quando o ponto de extremidade está no estado desabilitado.
- Se uma consulta for proveniente de uma região geográfica que não tem mapeamento nesse perfil, o Gerenciador de tráfego retornará uma resposta NODATA. Portanto, é altamente recomendável que os clientes usem o roteamento geográfico com um ponto de extremidade, o ideal é que o tipo seja aninhado com pelo menos dois pontos de extremidade no perfil filho, com a região **mundo** atribuída a ele. Isso também garante que todos os endereços IP que não são mapeados para uma região são manipulados.

Conforme explicado em [como funciona o Gerenciador de tráfego](traffic-manager-how-it-works.md), o Gerenciador de tráfego não recebe consultas DNS diretamente dos clientes. Em vez disso, as consultas DNS são provenientes do serviço DNS recursivo que os clientes estão configurados para usar. Portanto, o endereço IP usado para determinar a região não é o endereço IP do cliente, mas é o endereço IP do serviço DNS recursivo. Na prática, esse endereço IP é um bom proxy para o cliente.

### <a name="faqs"></a>FAQs

* [Quais são alguns casos de uso em que o roteamento geográfico é útil?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-some-use-cases-where-geographic-routing-is-useful)

* [Como fazer decidir se devo usar o método de roteamento de desempenho ou o método de roteamento geográfico?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-do-i-decide-if-i-should-use-performance-routing-method-or-geographic-routing-method)

* [Quais são as regiões com suporte do Gerenciador de tráfego para roteamento geográfico?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-the-regions-that-are-supported-by-traffic-manager-for-geographic-routing)

* [Como o Gerenciador de tráfego determina de onde um usuário está consultando?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-traffic-manager-determine-where-a-user-is-querying-from)

* [É garantido que o Gerenciador de tráfego possa determinar corretamente a localização geográfica exata do usuário em todos os casos?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#is-it-guaranteed-that-traffic-manager-can-correctly-determine-the-exact-geographic-location-of-the-user-in-every-case)

* [Um ponto de extremidade precisa estar fisicamente localizado na mesma região com a qual ele está configurado para roteamento geográfico?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#does-an-endpoint-need-to-be-physically-located-in-the-same-region-as-the-one-it-is-configured-with-for-geographic-routing)

* [Posso atribuir regiões geográficas a pontos de extremidade em um perfil que não está configurado para fazer o roteamento geográfico?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-assign-geographic-regions-to-endpoints-in-a-profile-that-is-not-configured-to-do-geographic-routing)

* [Por que estou recebendo um erro quando tento alterar o método de roteamento de um perfil existente para geográfico?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#why-am-i-getting-an-error-when-i-try-to-change-the-routing-method-of-an-existing-profile-to-geographic)

* [Por que é altamente recomendável que os clientes criem perfis aninhados em vez de pontos de extremidade em um perfil com roteamento geográfico habilitado?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#why-is-it-strongly-recommended-that-customers-create-nested-profiles-instead-of-endpoints-under-a-profile-with-geographic-routing-enabled)

* [Há alguma restrição na versão da API que dá suporte a esse tipo de roteamento?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#are-there-any-restrictions-on-the-api-version-that-supports-this-routing-type)

## <a name = "multivalue"></a>Método de roteamento de tráfego de vários valores
O método de roteamento de tráfego de **múltiplos valores** permite que você obtenha vários pontos de extremidade íntegros em uma única resposta de consulta DNS. Isso permite que o chamador faça repetições do lado do cliente com outros pontos de extremidade no caso de um ponto de extremidades retornado sem resposta. Esse padrão pode aumentar a disponibilidade de um serviço e reduzir a latência associada a uma nova consulta DNS para obter um ponto de extremidade íntegro. O método de roteamento de vários valores só funcionará se todos os pontos de extremidade do tipo ' external ' forem especificados como endereços IPv4 ou IPv6. Quando uma consulta é recebida para esse perfil, todos os pontos de extremidade íntegros são retornados e estão sujeitos a uma contagem de retorno máxima configurável.

### <a name="faqs"></a>FAQs

* [Quais são alguns casos de uso em que o roteamento de vários valores é útil?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-some-use-cases-where-multivalue-routing-is-useful)

* [Quantos pontos de extremidade são retornados quando o roteamento de vários valores é usado?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-many-endpoints-are-returned-when-multivalue-routing-is-used)

* [Eu receberei o mesmo conjunto de pontos de extremidade quando o roteamento de vários valores for usado?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#will-i-get-the-same-set-of-endpoints-when-multivalue-routing-is-used)

## <a name = "subnet"></a>Método de roteamento de tráfego de sub-rede
O método de roteamento de tráfego de **sub-rede** permite mapear um conjunto de intervalos de endereços IP de usuários finais para pontos de extremidade específicos em um perfil. Depois disso, se o Gerenciador de tráfego receber uma consulta DNS para esse perfil, ele inspecionará o endereço IP de origem dessa solicitação (na maioria dos casos, esse será o endereço IP de saída do resolvedor de DNS usado pelo chamador), determinar a qual ponto de extremidade ele está mapeado e retornará t ponto de extremidade do Hat na resposta da consulta. 

O endereço IP a ser mapeado para um ponto de extremidade pode ser especificado como intervalos CIDR (por exemplo, 1.2.3.0/24) ou como um intervalo de endereços (por exemplo, 1.2.3.4-5.6.7.8). Os intervalos de IP associados a um ponto de extremidade precisam ser exclusivos dentro desse perfil e não podem ter uma sobreposição com o conjunto de endereços IP de um ponto de extremidade diferente no mesmo perfil.
Se você definir um ponto de extremidade sem intervalo de endereços, isso funcionará como um fallback e assumirá o tráfego de quaisquer sub-redes restantes. Se nenhum ponto de extremidade de fallback for incluído, o Gerenciador de tráfego enviará uma resposta NODATA para quaisquer intervalos indefinidos. Portanto, é altamente recomendável que você defina um ponto de extremidade de fallback ou certifique-se de que todos os intervalos IP possíveis sejam especificados em seus pontos de extremidades.

O roteamento de sub-rede pode ser usado para fornecer uma experiência diferente para os usuários que se conectam de um espaço IP específico. Por exemplo, usando o roteamento de sub-rede, um cliente pode fazer com que todas as solicitações de seu escritório corporativo sejam roteadas para um ponto de extremidade diferente, onde podem estar testando uma versão somente interna de seu aplicativo. Outro cenário é se você quiser fornecer uma experiência diferente aos usuários que se conectam de um ISP específico (por exemplo, bloquear usuários de um determinado ISP).

### <a name="faqs"></a>FAQs

* [Quais são alguns casos de uso em que o roteamento de sub-rede é útil?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-some-use-cases-where-subnet-routing-is-useful)

* [Como o Gerenciador de tráfego sabe o endereço IP do usuário final?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-traffic-manager-know-the-ip-address-of-the-end-user)

* [Como especificar endereços IP ao usar o roteamento de sub-rede?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-specify-ip-addresses-when-using-subnet-routing)

* [Como posso especificar um ponto de extremidade de fallback ao usar o roteamento de sub-rede?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-specify-a-fallback-endpoint-when-using-subnet-routing)

* [O que acontece se um ponto de extremidade estiver desabilitado em um perfil de tipo de roteamento de sub-rede?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-happens-if-an-endpoint-is-disabled-in-a-subnet-routing-type-profile)


## <a name="next-steps"></a>Passos Seguintes

Saiba como desenvolver aplicativos de alta disponibilidade usando o [monitoramento de ponto de extremidade do Gerenciador de tráfego](traffic-manager-monitoring.md)




