---
title: Conjuntos de back-ends e back-end no serviço de porta de entrada do Azure | Documentos da Microsoft
description: Este artigo descreve os conjuntos de back-ends e back-end são à frente da configuração de porta de entrada.
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 2372f49c7280ee5c817f3d2f98cc80a196dae5f5
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58879204"
---
# <a name="backends-and-backend-pools-in-azure-front-door-service"></a>Conjuntos de back-ends e back-end no serviço de porta de entrada do Azure
Este artigo descreve conceitos sobre como mapear a sua implementação de aplicação com o serviço de porta de entrada do Azure. Explica também os termos diferentes na configuração de porta de entrada em torno de back-ends de aplicação.

## <a name="backends"></a>Back-ends
Um back-end é igual a instância de implementação de uma aplicação numa região. O serviço de porta de entrada suporta o Azure e o back-ends de não pertencente ao Azure, para que a região não é limitada apenas a regiões do Azure. Além disso, pode ser o seu datacenter no local ou uma instância de aplicação noutra cloud.

Consulte o back-ends de serviço de porta de entrada para o nome de anfitrião ou IP público da sua aplicação, que pode servir pedidos de cliente. Back-ends não devem ser confundida com a sua camada de base de dados, camada de armazenamento e assim por diante. Back-ends devem ser exibida como o ponto final público do seu back-end de aplicação. Ao adicionar um back-end num conjunto de back-end de porta de entrada, tem também de adicionar o seguinte:

- **Tipo de anfitrião de back-end**. O tipo de recurso que pretende adicionar. O serviço de porta de entrada suporta a descoberta automática do seu back-ends de aplicação se partir do serviço de aplicações, serviço cloud ou armazenamento. Se pretender que um recurso diferente no Azure ou até mesmo um backend não pertencente ao Azure, selecione **anfitrião personalizado**.

    >[!IMPORTANT]
    >Durante a configuração, as APIs não validam se o back-end não está acessível a partir de ambientes de porta de entrada. Certifique-se de que a porta da frente consegue contactar o back-end.

- **Nome de anfitrião de subscrição e o back-end**. Se não selecionou **anfitrião personalizado** para o tipo de anfitrião de back-end, selecione o back-end ao escolher a subscrição adequada e o nome de anfitrião correspondente do back-end na interface do Usuário.

- **Cabeçalho de anfitrião de back-end**. O valor de cabeçalho de anfitrião enviado para o back-end para cada solicitação. Para obter mais informações, consulte [cabeçalho de anfitrião de back-end](#hostheader).

- **Prioridade**. Atribua prioridades aos seus back-ends diferentes quando pretender utilizar um back-end de serviço principal para todo o tráfego. Além disso, fornece cópias de segurança se o principal ou os back-ends de cópia de segurança não estão disponíveis. Para obter mais informações, consulte [prioridade](front-door-routing-methods.md#priority).

- **Peso**. Atribua pesos ao seu back-ends diferentes para distribuir o tráfego entre um conjunto de back-ends, uniformemente ou, de acordo com os coeficientes de peso. Para obter mais informações, consulte [pesos](front-door-routing-methods.md#weighted).

### <a name = "hostheader"></a>Cabeçalho de anfitrião de back-end

Pedidos reencaminhados pela porta de entrada para um back-end incluem um campo de cabeçalho de anfitrião que o back-end utiliza para recuperar o recurso de destino. O valor para este campo normalmente provém o URI de back-end e tem o anfitrião e a porta.

Por exemplo, um pedido efetuado para www\.contoso.com terão o www de cabeçalho de anfitrião\.contoso.com. Se utilizar o portal do Azure para configurar o seu back-end, o valor predefinido para este campo é o nome de anfitrião do back-end. Se o seu back-end é contoso-westus.azurewebsites.net, no portal do Azure, o valor de preenchido automaticamente para o cabeçalho de anfitrião de back-end será contoso westus.azurewebsites.net. No entanto, se usar modelos Azure Resource Manager ou outro método sem definir explicitamente este campo, o serviço de porta da frente irá enviar o nome de anfitrião de entrada como o valor para o cabeçalho de anfitrião. Se o pedido foi feito para www\.contoso.com e o back-end é contoso-westus.azurewebsites.net que tenha um campo de cabeçalho vazio, o serviço de porta de entrada será definir o cabeçalho de anfitrião como www\.contoso.com.

A maioria das aplicações back-ends (aplicações Web do Azure, armazenamento de BLOBs e serviços Cloud) requerem o cabeçalho de anfitrião coincida com o domínio de back-end. No entanto, o anfitrião de front-end que encaminha para o back-end irá utilizar um nome de anfitrião diferente, por exemplo, www\.contoso.azurefd.net.

Se o seu back-end requer o cabeçalho de anfitrião de acordo com o nome de anfitrião de back-end, certifique-se de que o cabeçalho de anfitrião de back-end inclui o back-end de nome de anfitrião.

#### <a name="configuring-the-backend-host-header-for-the-backend"></a>Configurar o cabeçalho de anfitrião de back-end para o back-end

Para configurar o **cabeçalho de anfitrião de back-end** campo para um back-end na secção de conjunto de back-end:

1. Abra o recurso de porta de entrada e selecione o conjunto de back-end com o back-end para configurar.

2. Adicione um back-end, se ainda não o fez, ou editar um já existente.

3. Definir o anfitrião de back-end campo de cabeçalho para um valor personalizado ou deixe em branco. O nome de anfitrião para a solicitação de entrada será utilizado como o valor de cabeçalho de anfitrião.

## <a name="backend-pools"></a>Conjuntos de back-end
Um conjunto de back-end na frente porta serviço refere-se para o conjunto de back-ends que receberem o tráfego semelhante para a respetiva aplicação. Em outras palavras, é um agrupamento lógico das suas instâncias de aplicações em todo o mundo que recebem o mesmo tráfego e responder com o comportamento esperado. Estes back-ends são implementadas em diferentes regiões ou na mesma região. Todos os de back-ends podem estar em modo de implementação do ativo/ativo ou o que é definido como configuração de ativo/passivo.

Um conjunto de back-end define como os back-ends diferentes devem ser avaliados através de sondas de estado de funcionamento. Também define como balanceamento de carga ocorre entre eles.

### <a name="health-probes"></a>Sondas do estado de funcionamento
Serviço de porta de entrada envia pedidos periódicos de sonda HTTP/HTTPS para cada um dos seus back-ends configurado. Pedidos de sonda determinam o estado de funcionamento de cada back-end para carregar e proximidade equilibrar os pedidos de utilizador final. Definições de sonda de estado de funcionamento para um conjunto de back-end definem como podemos consultar o estado de funcionamento do back-ends de aplicação. As seguintes definições estão disponíveis para configuração de balanceamento de carga:

- **Caminho**. O URL utilizado para solicitações de sondagem para todos os back-ends no conjunto de back-end. Por exemplo, se é um dos seus back-ends contoso westus.azurewebsites.net e o caminho é definido como /probe/test.aspx, em seguida, ambientes de serviço de porta de entrada, partindo do princípio de que o protocolo está definido como HTTP, irão enviar solicitações de sondagem do Estado de funcionamento para http\:/ / contoso-westus.azurewebsites.net/probe/test.aspx.

- **Protocolo**. Define se pretende enviar os pedidos de sonda de estado de funcionamento do serviço de porta de entrada para o seu back-ends com o protocolo HTTP ou HTTPS.

- **Intervalo (segundos)**. Define a frequência de sondas de estado de funcionamento para o seu back-ends ou intervalos em que cada um dos ambientes de porta de entrada envia uma sonda.

    >[!NOTE]
    >Para as ativações pós-falha mais rápidas, defina o intervalo para um valor inferior. Quanto mais baixo for o valor, o maior volume de sonda de estado de funcionamento recebem os back-ends. Por exemplo, se o intervalo está definido para 30 segundos com 90 ambientes de porta de entrada ou POPs globalmente, cada back-end irá receber sobre pedidos de sonda de 3 a 5 por segundo.

Para obter mais informações, consulte [sondas de estado de funcionamento](front-door-health-probes.md).

### <a name="load-balancing-settings"></a>Definições de balanceamento de carga
Definições de balanceamento de carga para o conjunto de back-end definem como avaliamos sondas de estado de funcionamento. Estas definições determinam se o back-end é bom ou mau estado de funcionamento. Eles também verificam como tráfego de balanceamento de carga entre o back-ends diferentes no conjunto de back-end. As seguintes definições estão disponíveis para configuração de balanceamento de carga:

- **Tamanho de exemplo**. Identifica o número de amostras de sondas de estado de funcionamento que precisamos considerar para avaliação de estado de funcionamento do back-end.

- **Tamanho da amostra bem-sucedida**. Define o tamanho da amostra conforme mencionado anteriormente, o número de amostras com êxito necessários para chamar o back-end em bom estado de funcionamento. Por exemplo, suponha que um intervalo de sonda de estado de funcionamento de porta de entrada é de 30 segundos, tamanho da amostra é de 5 segundos e tamanho de exemplo com êxito é 3 segundos. Sondas de cada vez Avaliamos o estado de funcionamento para o back-end, vamos ver os últimos cinco exemplos mais de 150 segundos (5 x 30). Sondas com êxito, pelo menos, três são necessários para declarar o back-end como bom estado de funcionamento.

- **A sensibilidade de latência (latência adicional)**. Define se pretende que a porta de entrada para enviar o pedido para o back-ends dentro do intervalo de sensibilidade de medição de latência ou reencaminhar o pedido para o back-end mais próximo.

Para obter mais informações, consulte [latência, pelo menos, com base em método de encaminhamento](front-door-routing-methods.md#latency).

## <a name="next-steps"></a>Passos Seguintes

- [Criar um perfil de porta de entrada](quickstart-create-front-door.md)
- [Como funciona a porta de entrada](front-door-routing-architecture.md)