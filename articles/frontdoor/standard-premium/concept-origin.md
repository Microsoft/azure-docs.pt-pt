---
title: Grupo de origem e origem em Azure Front Door Standard/Premium
description: Este artigo descreve o grupo de origem e origem que estão numa configuração da porta frontal Azure.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: 42a9a0ea23faa481140a46ec52b2214431dd723e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101100082"
---
# <a name="origin-and-origin-group-in-azure-front-door-standardpremium-preview"></a>Grupo de origem e origem em Azure Front Door Standard/Premium (Preview)

> [!Note]
> Esta documentação destina-se ao Azure Front Door Standard/Premium (Preview). À procura de informações sobre a Porta da Frente Azure? Ver [aqui](../front-door-overview.md).

Este artigo abordará conceitos sobre como a sua implementação de aplicações web funciona com a Azure Front Door Standard/Premium. Você também vai aprender sobre o que é um grupo *de origem* e *origem* na configuração Azure Front Door Standard/Premium.

> [!IMPORTANT]
> Azure Front Door Standard/Premium (Preview) está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="origin"></a>Origem

A origem Azure Front Door Standard/Premium refere-se ao nome de anfitrião ou IP público da sua aplicação que serve os pedidos do seu cliente. A Azure Front Door Standard/Premium suporta recursos Azure e não-Azure num grupo de origem. A aplicação também pode ser hospedada no seu datacenter no local ou com outro fornecedor de nuvem. A origem não deve ser confundida com o nível da base de dados ou nível de armazenamento. A origem deve ser vista como o ponto final público para o backend da sua aplicação. Quando adicionar uma origem a um grupo de origem Azure Front Door Standard/Premium, deve também adicionar as seguintes informações:

* **Tipo de origem:** O tipo de recurso que pretende adicionar. A Porta Frontal suporta a autodiscover da sua aplicação backends do Serviço de Aplicações, Serviço de Nuvem ou Armazenamento. Se quiser um recurso diferente em Azure ou mesmo um backend não-Azure, selecione **Anfitrião Personalizado**.

    >[!IMPORTANT]
    >Durante a configuração, as APIs não validam se a origem não estiver acessível a partir do ambiente da porta frontal. Certifique-se de que a Porta da Frente pode chegar à sua origem.

* **Nome do anfitrião da subscrição e da origem:** Se não selecionou **o anfitrião personalizado** para o tipo de anfitrião backend, selecione o seu backend escolhendo a subscrição apropriada e o nome de anfitrião de backend correspondente.

* **Cabeçalho do anfitrião de origem:** O valor do cabeçalho do anfitrião enviado para o backend para cada pedido. Para obter mais informações, consulte [o cabeçalho do anfitrião Origin](#hostheader).

* **Prioridade:** Atribua prioridades à sua origem diferente quando pretender utilizar uma origem primária para todo o tráfego. Além disso, forneça cópias de segurança se as origens primárias ou de backup não estiverem disponíveis. Para mais informações, consulte [Prioridade.](#priority)

* **Peso:** Atribua pesos à sua origem diferente para distribuir o tráfego por um conjunto de origens, quer uniformemente, quer de acordo com coeficientes de peso. Para mais informações, consulte [Weights](#weighted).

### <a name="origin-host-header"></a><a name = "hostheader"></a>Cabeçalho de anfitrião de origem

Os pedidos que são reencaminhados pela Azure Front Door Standard/Premium para uma origem incluirão um campo de cabeçalho anfitrião que a origem utiliza para recuperar o recurso visado. O valor para este campo normalmente vem da origem URI que tem o cabeçalho anfitrião e a porta.

Por exemplo, um pedido feito `www.contoso.com` terá o cabeçalho do anfitrião `www.contoso.com` . Se utilizar o portal Azure para configurar a sua origem, o valor padrão para este campo é o nome de anfitrião do backend. Se a sua origem `contoso-westus.azurewebsites.net` for, no portal Azure, o valor autopovoado para o cabeçalho do hospedeiro de origem será `contoso-westus.azurewebsites.net` . No entanto, se utilizar modelos do Gestor de Recursos Azure ou outro método sem definir explicitamente este campo, a Porta Frontal enviará o nome de anfitrião de entrada como o valor para o cabeçalho do anfitrião. Se o pedido foi feito `www.contoso.com` para , e a sua origem é que tem um campo de `contoso-westus.azurewebsites.net` cabeçalho vazio, a Porta frontal definirá o cabeçalho do anfitrião como `www.contoso.com` .

A maioria dos backends de aplicações (Azure Web Apps, Blob storage e Cloud Services) requerem o cabeçalho do anfitrião para combinar com o domínio do backend. No entanto, o anfitrião frontal que vai para o seu backend usará um nome de anfitrião diferente, como `www.contoso.net` .

Se a sua origem exigir que o cabeçalho do anfitrião corresponda ao nome de anfitrião de backend, certifique-se de que o cabeçalho do anfitrião de backend inclui o nome de anfitrião do backend.

#### <a name="configuring-the-origin-host-header-for-the-origin"></a>Configurar o cabeçalho do hospedeiro de origem para a origem

Para configurar o campo **de cabeçalho do anfitrião de origem** para uma origem na secção do grupo de origem:

1. Abra o seu recurso porta frontal e selecione o grupo de origem com a origem para configurar.

2. Adicione uma origem se ainda não o fez, ou edite uma existente.

3. Desabrade o campo de cabeçalho do anfitrião de origem para um valor personalizado ou deixe-o em branco. O nome de anfitrião para o pedido de entrada será usado como valor do cabeçalho do anfitrião.

## <a name="origin-group"></a>Grupo de origem

Um grupo de origem em Azure Front Door Standard/Premium refere-se ao conjunto de origens que recebe tráfego semelhante para a sua aplicação. Por outras palavras, é um agrupamento lógico das suas aplicações em todo o mundo que recebem o mesmo tráfego e respondem com um comportamento esperado. Estas origens podem ser implantadas em diferentes regiões ou na mesma região. Todas as origens podem estar no modo de implementação Ative/Ative ou no que é definido como configuração Ative/Passive.

Um grupo de origem define como as origens devem ser avaliadas através de sondas de saúde. Também define como o equilíbrio de carga ocorre entre eles.

### <a name="health-probes"></a>Sondas do estado de funcionamento

Azure Front Door Standard/Premium envia pedidos periódicos de sonda HTTP/HTTPS a cada uma das suas origens configuradas. Os pedidos de sonda determinam a proximidade e a saúde de cada origem para carregar o equilíbrio dos seus pedidos de utilizador final. As configurações da sonda de saúde para um grupo de origem definem como sondamos o estado de saúde dos backends da aplicação. Estão disponíveis as seguintes definições para a configuração de equilíbrio de carga:

* **Caminho**: O URL utilizado para pedidos de sonda para todas as origens do grupo de origem. Por exemplo, se uma das suas origens for `contoso-westus.azurewebsites.net` e o caminho for definido para /sonda/teste.aspx, então os ambientes da Porta Frontal, assumindo que o protocolo é HTTP, enviarão pedidos de sonda de saúde para `http://contoso-westus.azurewebsites.net/probe/test.aspx` .

* **Protocolo**: Define se deve enviar os pedidos de sonda de saúde da Porta frontal para as suas origens com o protocolo HTTP ou HTTPS.

* **Método**: O método HTTP a utilizar para o envio de sondas sanitárias. As opções incluem GET ou HEAD (predefinição).
    > [!NOTE]
    > Para uma carga mais baixa e um custo nas suas costas, a Porta Frontal recomenda a utilização de pedidos DE CABEÇA para sondas de saúde.

* **Intervalo (segundos)**: Define a frequência das sondas sanitárias para as suas origens, ou os intervalos em que cada um dos ambientes da porta frontal envia uma sonda.

    >[!NOTE]
    >Para falhas mais rápidas, desa um intervalo para um valor mais baixo. Quanto menor for o valor, maior é o volume da sonda de saúde que os seus backends recebem. Por exemplo, se o intervalo for definido para 30 segundos com, digamos, 100 POPs front door globalmente, cada backend receberá cerca de 200 pedidos de sonda por minuto.

Para mais informações, consulte [as sondas Health.](concept-health-probes.md)

### <a name="load-balancing-settings"></a>Definições de equilíbrio de carga

As definições de equilíbrio de carga para o grupo de origem definem como avaliamos as sondas de saúde. Estas definições determinam se a origem é saudável ou pouco saudável. Também verificam como equilibrar o tráfego entre diferentes origens no grupo de origem. Estão disponíveis as seguintes definições para a configuração de equilíbrio de carga:

* **Tamanho da amostra:** Identifica quantas amostras de sondas de saúde precisamos considerar para avaliação de saúde de origem.

* **Tamanho da amostra bem-sucedido:** Define o tamanho da amostra como mencionado anteriormente, o número de amostras bem sucedidas necessárias para chamar a origem de saudável. Por exemplo, assuma que um intervalo de sonda de saúde da porta frontal é de 30 segundos, o tamanho da amostra é 5, e o tamanho da amostra bem-sucedido é 3. Cada vez que avaliamos as sondas de saúde para a sua origem, olhamos para as últimas cinco amostras em 150 segundos (5 x 30). Pelo menos três sondas bem sucedidas são necessárias para declarar a origem como saudável.

* **Sensibilidade à latência (latência extra):** Define se deseja que o Azure Front Door Standard/Premium envie o pedido para a origem dentro do intervalo de sensibilidade à medição de latência ou encaminhe o pedido para o backend mais próximo.

Para obter mais informações, consulte [o método de encaminhamento menos baseado em latência](#latency).

## <a name="routing-methods"></a>Métodos de encaminhamento

A Azure Front Door Standard/Premium suporta diferentes tipos de métodos de encaminhamento de tráfego para determinar como encaminhar o tráfego HTTP/HTTPS para diferentes pontos finais de serviço. Quando o seu cliente solicita chegar à Porta da Frente, o método de encaminhamento configurado é aplicado para garantir que os pedidos são encaminhados para a melhor instância de backend. 

Existem quatro métodos de encaminhamento de tráfego disponíveis no Azure Front Door Standard/Premium:

* **[Latência:](#latency)** O encaminhamento baseado em latência garante que os pedidos são enviados para os apoios de latência mais baixos aceitáveis dentro de um intervalo de sensibilidade. Basicamente, os seus pedidos de utilizador são enviados para o conjunto "mais próximo" de backends no que diz respeito à latência da rede.
* **[Prioridade:](#priority)** Pode atribuir prioridades aos seus backends quando quiser configurar um backend primário para servir todo o tráfego. O backend secundário pode ser uma cópia de segurança caso o backend primário fique indisponível.
* **[Ponderado:](#weighted)** Pode atribuir pesos aos seus backends quando quiser distribuir o tráfego por um conjunto de backends. Quer queira distribuir uniformemente ou de acordo com os coeficientes de peso.

Todas as configurações Azure Front Door Standard/Premium incluem monitorização da saúde backend e falha global instantânea automatizada. Para obter mais informações, consulte [o Backend Monitoring](concept-health-probes.md). A porta da frente pode funcionar com base num único método de encaminhamento. Mas, dependendo das necessidades da sua aplicação, também pode combinar vários métodos de encaminhamento para construir uma topologia de encaminhamento ideal.

### <a name="lowest-latencies-based-traffic-routing"></a><a name = "latency"></a>Encaminhamento de tráfego baseado em latências mais baixas

A implementação de backends em dois ou mais locais em todo o mundo pode melhorar a capacidade de resposta das suas aplicações, encaminhando o tráfego para o destino que está "mais próximo" dos seus utilizadores finais. O método de encaminhamento de tráfego predefinido para a configuração da porta frontal remete os pedidos dos seus utilizadores finais para o backend mais próximo do ambiente da Porta frontal que recebeu o pedido. Combinada com a arquitetura Anycast da Azure Front Door, esta abordagem garante que cada um dos seus utilizadores finais obtenha o máximo desempenho personalizado com base na sua localização.

O backend 'mais próximo' não é necessariamente o mais próximo medido pela distância geográfica. Em vez disso, a Porta Frontal determina os backends mais próximos medindo a latência da rede.

Segue-se o fluxo global de decisão:

| Backends disponíveis | Prioridade | Sinal de latência (baseado em sonda de saúde) | Pesos |
|-------------| ----------- | ----------- | ----------- |
| Em primeiro lugar, selecione todos os backends que estejam ativados e devolvidos saudáveis (200 OK) para a sonda de saúde. Se houver seis backends A, B, C, D, E e F, e entre eles C não é saudável e E é desativado. A lista de backends disponíveis é A, B, D e F.  | Em seguida, os backends prioritários entre os disponíveis são selecionados. Se o backend A, B e D têm prioridade 1 e o backend F tem uma prioridade de 2. Então, os backends selecionados serão A, B e D.| Selecione os backends com intervalo de latência (menos latência & sensibilidade à latência em ms especificado). Se o backend A é de 15 ms, B é 30 ms e D está a 60 ms do ambiente da Porta frontal onde o pedido aterrou, e a sensibilidade de latência é de 30 ms, então a piscina de latência mais baixa consiste em backend A e B, porque D está além de 30 ms de distância do backend mais próximo que é A. | Por último, a Porta Frontal rodeará o tráfego entre o conjunto final selecionado de backends na relação de pesos especificados. Digamos, se o backend A tem um peso de 5 e o backend B tem um peso de 8, então o tráfego será distribuído na proporção de 5:8 entre os backends A e B. |

>[!NOTE]
> Por padrão, a propriedade de sensibilidade à latência está definida para 0 ms, ou seja, sempre encaminhar o pedido para o backend mais rápido disponível.

### <a name="priority-based-traffic-routing"></a><a name = "priority"></a>Encaminhamento de tráfego baseado em prioridades

Muitas vezes, uma organização quer fornecer alta disponibilidade para os seus serviços, implantando mais de um serviço de backup no caso de o principal cair. Em toda a indústria, esta topologia também é referida como topologia de implantação ativa/standby ou ativa/passiva. O método de encaminhamento de tráfego "Prioritário" permite aos clientes da Azure implementar facilmente este padrão de failover.

A porta frontal padrão contém uma lista de prioridades iguais de backends. Por predefinição, a Porta Frontal envia o tráfego apenas para os backends prioritários (valor mais baixo para prioridade) ou seja, o conjunto primário de backends. Se os backends primários não estiverem disponíveis, a Porta Frontal encaminha o tráfego para o conjunto secundário de backends (segundo valor mais baixo para prioridade). Se os apoios primários e secundários não estiverem disponíveis, o tráfego vai para o terceiro, e assim por diante. A disponibilidade do backend baseia-se no estado configurado (ativado ou incapacitado) e no estado de saúde de backend em curso, conforme determinado pelas sondas de saúde.

#### <a name="configuring-priority-for-backends"></a>Configurar prioridade para backends

Cada backend na sua piscina de backend da configuração da Porta frontal tem uma propriedade chamada 'Prioridade', que pode ser um número entre 1 e 5. Com a Porta Frontal Azure, configura a prioridade de backend explicitamente usando esta propriedade para cada backend. Esta propriedade é um valor entre 1 e 5. Valores mais baixos representam uma prioridade maior. Os backends podem partilhar valores prioritários.

### <a name="weighted-traffic-routing-method"></a><a name = "weighted"></a>Método de encaminhamento de tráfego ponderado
O método de encaminhamento de tráfego 'ponderado' permite-lhe distribuir o tráfego uniformemente ou utilizar uma ponderação pré-definida.

No método de encaminhamento de tráfego ponderado, atribui-se um peso a cada backend na configuração da porta frontal da sua piscina de backend. A ponderação é um número inteiro entre 1 e 1000. Este parâmetro utiliza um peso predefinido de '50'.

Com a lista de backends disponíveis que têm uma sensibilidade de latência aceitável, o tráfego é distribuído com um mecanismo de robin redondo utilizando a relação de pesos especificados. Se a sensibilidade à latência for definida em 0 milissegundos, então esta propriedade não produz efeito a menos que haja dois backends com a mesma latência da rede. 

O método ponderado permite alguns cenários úteis:

* **Atualização gradual** da aplicação : Dá uma percentagem de tráfego para encaminhar para um novo backend, e gradualmente aumentar o tráfego ao longo do tempo para trazê-lo a par com outros backends.
* **Migração de aplicações para Azure**: Crie uma piscina de backend com backends Azure e externos. Ajuste o peso dos backends para preferir os novos backends. Você pode gradualmente configurar isto começando com ter os novos backends desativados, em seguida, atribuindo-lhes os pesos mais baixos, aumentando-o lentamente para níveis onde eles tomam a maioria do tráfego. Em seguida, finalmente desativando os backends menos preferidos e retirando-os da piscina.  
* **Rebentamento de nuvens para capacidade adicional**: Expanda rapidamente uma implantação no local para a nuvem colocando-a atrás da Porta da Frente. Quando precisar de uma capacidade extra na nuvem, pode adicionar ou ativar mais backends e especificar qual a parte do tráfego que vai para cada backend.

## <a name="next-steps"></a>Passos seguintes

Saiba como [criar um Padrão/Premium da Porta da Frente](create-front-door-portal.md)
