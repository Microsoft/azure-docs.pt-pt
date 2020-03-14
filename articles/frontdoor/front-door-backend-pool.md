---
title: Backends e piscinas de backend no Serviço da Porta da Frente Azure  Microsoft Docs
description: Este artigo descreve o que os backends e as piscinas de backend estão na configuração da Porta da Frente.
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
ms.openlocfilehash: b764799d3f40cef24a0412ac950026af650d4ec7
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79272854"
---
# <a name="backends-and-backend-pools-in-azure-front-door-service"></a>Backends e piscinas de backend no Serviço de Porta da Frente Azure
Este artigo descreve conceitos sobre como mapear a sua implementação de aplicações com o Azure Front Door Service. Também explica os diferentes termos na configuração da Porta Frontal em torno dos backends das aplicações.

## <a name="backends"></a>Back-ends
Um backend é igual ao caso de implantação de uma aplicação numa região. O Front Door Service suporta os backends Azure e não-Azure, pelo que a região não se limita apenas às regiões de Azure. Além disso, pode ser o seu datacenter no local ou uma instância de aplicação em outra nuvem.

Os backends do Front Door Service referem-se ao nome do anfitrião ou IP público da sua app, que pode servir pedidos de clientes. As backends não devem ser confundidas com o seu nível de base de dados, o nível de armazenamento, e assim por diante. Os backends devem ser vistos como o ponto final público do seu backend da sua aplicação. Quando adicionar um backend numa piscina frontal, também deve adicionar o seguinte:

- **Tipo de hospedeiro de backend**. O tipo de recurso que pretende adicionar. O Front Door Service suporta a descoberta automática das suas despesas de aplicação do serviço de aplicações, serviço de nuvem ou armazenamento. Se quiser um recurso diferente em Azure ou mesmo um backend não-Azure, selecione **Custom host**.

    >[!IMPORTANT]
    >Durante a configuração, as APIs não validam se o backend é inacessível a partir de ambientes da Porta Da Frente. Certifique-se de que a porta da frente pode chegar ao seu backend.

- **Nome de anfitrião de subscrição e backend**. Se ainda não selecionou o **anfitrião Personalizado** para o tipo de anfitrião de backend, selecione o seu backend escolhendo a subscrição apropriada e o nome de anfitrião correspondente no UI.

- **Cabeçalho de anfitrião de backend**. O valor do cabeçalho do anfitrião enviado para o backend para cada pedido. Para mais informações, consulte o [cabeçalho do anfitrião Backend](#hostheader).

- **Prioridade.** Atribua prioridades aos seus diferentes backends quando quiser utilizar um backend de serviço primário para todo o tráfego. Além disso, forneça cópias de segurança se as costas primárias ou as cópias de segurança não estiverem disponíveis. Para mais informações, consulte [Prioridade](front-door-routing-methods.md#priority).

- **Peso.** Atribuir pesos às suas diferentes costas para distribuir o tráfego através de um conjunto de backends, uniformemente ou de acordo com os coeficientes de peso. Para mais informações, consulte [Pesos](front-door-routing-methods.md#weighted).

### <a name = "hostheader"></a>Cabeçalho anfitrião backend

Os pedidos encaminhados pela Porta da Frente para um backend incluem um campo de cabeçalho hospedeiro que o backend usa para recuperar o recurso direcionado. O valor para este campo normalmente vem do uri backend e tem o hospedeiro e o porto.

Por exemplo, um pedido feito para www\.contoso.com terá o cabeçalho do anfitrião www\.contoso.com. Se utilizar o portal Azure para configurar o seu backend, o valor predefinido para este campo é o nome anfitrião do backend. Se o seu backend for contoso-westus.azurewebsites.net, no portal Azure, o valor autopovoado para o cabeçalho do hospedeiro será contoso-westus.azurewebsites.net. No entanto, se utilizar os modelos do Gestor de Recursos Azure ou outro método sem definir explicitamente este campo, o Front Door Service enviará o nome do anfitrião como valor para o cabeçalho do anfitrião. Se o pedido foi feito para www\.contoso.com, e o seu backend for contoso-westus.azurewebsites.net que tem um campo de cabeçalho vazio, o Front Door Service definirá o cabeçalho do anfitrião como www\.contoso.com.

A maioria dos backends de aplicações (Azure Web Apps, armazenamento blob e Serviços cloud) requerem que o cabeçalho do anfitrião corresponda ao domínio do backend. No entanto, o anfitrião frontal que as rotas para o seu backend usarão um nome de anfitrião diferente, como www\.contoso.azurefd.net.

Se o seu backend necessitar do cabeçalho do anfitrião para corresponder ao nome do anfitrião, certifique-se de que o cabeçalho do anfitrião inclui o backend do nome do anfitrião.

#### <a name="configuring-the-backend-host-header-for-the-backend"></a>Configurar o cabeçalho do anfitrião traseiro para o backend

Para configurar o campo de **cabeçalho** do hospedeiro traseiro para uma parte de trás na secção de piscina de backend:

1. Abra o recurso da Porta Da Frente e selecione a piscina de backend com o backend para configurar.

2. Adicione um backend se não o tiver feito, ou editar um existente.

3. Coloque o campo de cabeçalho do hospedeiro de backend a um valor personalizado ou deixe-o em branco. O nome de anfitrião para o pedido de entrada será usado como o valor do cabeçalho do anfitrião.

## <a name="backend-pools"></a>Piscinas de backend
Uma piscina de backend no Front Door Service refere-se ao conjunto de backends que recebem tráfego semelhante para a sua app. Por outras palavras, é um agrupamento lógico das instâncias da sua aplicação em todo o mundo que recebem o mesmo tráfego e respondem com o comportamento esperado. Estes backends são implantados em diferentes regiões ou na mesma região. Todos os backends podem estar no modo de implementação Ative/Ative ou o que é definido como configuração Ativa/Passiva.

Uma piscina de backend define como as diferentes backends devem ser avaliadas através de sondas de saúde. Também define como o equilíbrio de carga ocorre entre eles.

### <a name="health-probes"></a>Sondas do estado de funcionamento
O Serviço porta da frente envia pedidos periódicos de sonda HTTP/HTTPS para cada um dos seus backends configurados. Os pedidos de sonda determinam a proximidade e a saúde de cada backend para carregar o equilíbrio dos seus pedidos de utilizador final. As configurações da sonda de saúde para um pool de backend definem como sondamos o estado de saúde das backends da aplicação. Estão disponíveis as seguintes definições para a configuração de equilíbrio de carga:

- **Caminho.** O URL usado para pedidos de sonda para todas as costas na piscina de backend. Por exemplo, se um dos seus backends for contoso-westus.azurewebsites.net e o caminho for definido para /probe/test.aspx, então os ambientes do Serviço porta da frente, assumindo que o protocolo está definido para HTTP, enviarão pedidos de sonda de saúde para http\://contoso-westus.azurewebsites.net/probe/test.aspx.

- **Protocolo**. Define se envia os pedidos da sonda de saúde do Serviço porta da frente para os seus backends com protocolo HTTP ou HTTPS.

- **Intervalo (segundos)** . Define a frequência das sondas de saúde nas suas costas, ou os intervalos em que cada um dos ambientes da Porta da Frente envia uma sonda.

    >[!NOTE]
    >Para falhas mais rápidas, detete o intervalo para um valor mais baixo. Quanto menor for o valor, maior o volume da sonda de saúde que os seus backends recebem. Por exemplo, se o intervalo for definido para 30 segundos com 90 ambientes frontdoor ou POPs em todo o mundo, cada backend receberá cerca de 3-5 pedidos de sonda por segundo.

Para mais informações, consulte [sondas de saúde.](front-door-health-probes.md)

### <a name="load-balancing-settings"></a>Definições de equilíbrio de carga
As definições de equilíbrio de carga para o pool de backend definem como avaliamos as sondas de saúde. Estas definições determinam se o backend é saudável ou insalubre. Eles também verificam como carregar o tráfego entre diferentes backends na piscina de backend. Estão disponíveis as seguintes definições para a configuração de equilíbrio de carga:

- **Tamanho da amostra.** Identifica quantas amostras de sondas de saúde precisamos considerar para a avaliação de saúde.

- **Tamanho da amostra bem-sucedido.** Define o tamanho da amostra como mencionado anteriormente, o número de amostras bem sucedidas necessárias para chamar o backend saudável. Por exemplo, suponha que um intervalo de sonda de saúde front door é de 30 segundos, o tamanho da amostra é de 5, e o tamanho da amostra bem-sucedido é 3. Cada vez que avaliamos as sondas de saúde para o seu backend, olhamos para as últimas cinco amostras em 150 segundos (5 x 30). Pelo menos três sondas bem sucedidas são necessárias para declarar o backend como saudável.

- **Sensibilidade à latência (latência adicional)** . Define se pretende que a Porta Frontal envie o pedido para backends dentro da gama de sensibilidade à medição de latência ou reencaminha o pedido para o backend mais próximo.

Para mais informações, consulte o método de [encaminhamento baseado em menor latência](front-door-routing-methods.md#latency).

## <a name="next-steps"></a>Passos Seguintes

- [Criar um perfil porta da frente](quickstart-create-front-door.md)
- [Como funciona a Porta da Frente](front-door-routing-architecture.md)