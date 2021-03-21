---
title: Backends e piscinas de backend na Porta frontal Azure| Microsoft Docs
description: Este artigo descreve o que os backends e as piscinas de backend estão na configuração da porta da frente.
services: front-door
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 4beba141fec7a819df52e4c3a669312a4ad76998
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91449293"
---
# <a name="backends-and-backend-pools-in-azure-front-door"></a>Backends e piscinas de backend na Porta frontal Azure
Este artigo descreve conceitos sobre como mapear a implementação da sua aplicação web com a Porta Frontal Azure. Também explica as diferentes terminologias utilizadas na configuração da porta frontal em torno dos backends da aplicação.

## <a name="backends"></a>Back-ends
Um backend refere-se a uma implementação de aplicações web numa região. A Porta frontal suporta recursos Azure e não-Azure na piscina de backend. A aplicação pode estar no datacenter no local ou localizada noutro fornecedor de nuvem.

Front Door backends refere-se ao nome de anfitrião ou IP público da sua aplicação que serve os pedidos do seu cliente. Os backends não devem ser confundidos com o nível da base de dados, o nível de armazenamento, e assim por diante. Os backends devem ser vistos como o ponto final público para o backend da sua aplicação. Quando adicionar um backend a uma piscina de backend front door, também deve adicionar o seguinte:

- **Tipo de anfitrião backend**. O tipo de recurso que pretende adicionar. A Porta Frontal suporta a autodiscover dos seus suportes de aplicações a partir do serviço de aplicações, serviço na nuvem ou armazenamento. Se quiser um recurso diferente em Azure ou mesmo um backend não-Azure, selecione **Anfitrião Personalizado**.

    >[!IMPORTANT]
    >Durante a configuração, as APIs não validam se o backend estiver inacessível dos ambientes da Porta Frontal. Certifique-se de que a Porta da Frente pode chegar ao seu backend.

- **Nome de anfitrião de subscrição e backend**. Se ainda não selecionou **o anfitrião personalizado** para o tipo de anfitrião backend, selecione o seu backend escolhendo a subscrição apropriada e o nome de anfitrião de backend correspondente na UI.

- **Cabeçalho do anfitrião backend**. O valor do cabeçalho do anfitrião enviado para o backend para cada pedido. Para mais informações, consulte [o cabeçalho do anfitrião Backend](#hostheader).

- **Prioridade**. Atribua prioridades aos seus diferentes backends quando pretender utilizar um backend de serviço primário para todo o tráfego. Além disso, forneça cópias de segurança se os backends primários ou os backups não estiverem disponíveis. Para mais informações, consulte [Prioridade.](front-door-routing-methods.md#priority)

- **Peso**. Atribua pesos aos seus diferentes backends para distribuir o tráfego por um conjunto de backends, quer uniformemente, quer de acordo com coeficientes de peso. Para mais informações, consulte [Weights](front-door-routing-methods.md#weighted).

### <a name="backend-host-header"></a><a name = "hostheader"></a>Cabeçalho de anfitrião de backend

Os pedidos reencaminhados pela Porta Frontal para um backend incluem um campo de cabeçalho anfitrião que o backend utiliza para recuperar o recurso visado. O valor para este campo normalmente vem do backend URI que tem o cabeçalho anfitrião e a porta.

Por exemplo, um pedido feito `www.contoso.com` terá o cabeçalho anfitrião www.contoso.com. Se utilizar o portal Azure para configurar o seu backend, o valor padrão para este campo é o nome de anfitrião do backend. Se o seu backend for contoso-westus.azurewebsites.net, no portal Azure, o valor autopovoado para o cabeçalho do anfitrião de backend será contoso-westus.azurewebsites.net. No entanto, se utilizar modelos do Gestor de Recursos Azure ou outro método sem definir explicitamente este campo, a Porta Frontal enviará o nome de anfitrião de entrada como o valor para o cabeçalho do anfitrião. Se o pedido foi feito para www \. contoso.com, e o seu backend é contoso-westus.azurewebsites.net que tem um campo de cabeçalho vazio, a Porta frontal definirá o cabeçalho do anfitrião como www \. contoso.com.

A maioria dos backends de aplicações (Azure Web Apps, Blob storage e Cloud Services) requerem o cabeçalho do anfitrião para combinar com o domínio do backend. No entanto, o anfitrião frontal que as rotas para o seu backend usarão um nome de anfitrião diferente, como www.contoso.net.

Se o seu backend exigir que o cabeçalho do anfitrião corresponda ao nome do anfitrião de backend, certifique-se de que o cabeçalho do anfitrião de backend inclui o nome de anfitrião do backend.

#### <a name="configuring-the-backend-host-header-for-the-backend"></a>Configurar o cabeceamento do anfitrião de backend para o backend

Para configurar o campo **de cabeçalho do anfitrião backend** para um backend na secção de piscina de backend:

1. Abra o recurso da porta da frente e selecione o pool de backend com o backend para configurar.

2. Adicione um backend se ainda não o fez, ou edite um existente.

3. Desabrade o campo de cabeçalho do anfitrião de backend para um valor personalizado ou deixe-o em branco. O nome de anfitrião para o pedido de entrada será usado como valor do cabeçalho do anfitrião.

## <a name="backend-pools"></a>Piscinas de backend
Uma piscina de backend em Front Door refere-se ao conjunto de backends que recebem tráfego semelhante para a sua aplicação. Por outras palavras, é um agrupamento lógico das suas aplicações em todo o mundo que recebem o mesmo tráfego e respondem com o comportamento esperado. Estes backends são implantados em diferentes regiões ou na mesma região. Todos os backends podem estar no modo de implementação Ative/Ative ou no que é definido como configuração Ative/Passive.

Um pool de backend define como os diferentes backends devem ser avaliados através de sondas de saúde. Também define como o equilíbrio de carga ocorre entre eles.

### <a name="health-probes"></a>Sondas do estado de funcionamento
A Porta Frontal envia pedidos periódicos de sonda HTTP/HTTPS a cada um dos seus backends configurados. Os pedidos de sonda determinam a proximidade e a saúde de cada backend para carregar o equilíbrio dos seus pedidos de utilizador final. As configurações da sonda de saúde para um pool de backend definem como sondamos o estado de saúde dos backends de aplicações. Estão disponíveis as seguintes definições para a configuração de equilíbrio de carga:

- **Caminho**: O URL utilizado para pedidos de sonda para todos os backends na piscina de backend. Por exemplo, se um dos seus backends for contoso-westus.azurewebsites.net e o caminho estiver definido para /sonda/teste.aspx, então os ambientes da Porta Frontal, assumindo que o protocolo está definido para HTTP, enviarão pedidos de sonda de saúde para \: http/contoso-westus.azurewebsites.net/probe/test.aspx.

- **Protocolo**: Define se envia os pedidos de sonda de saúde da Porta frontal para os seus backends com o protocolo HTTP ou HTTPS.

- **Método**: O método HTTP a utilizar para o envio de sondas sanitárias. As opções incluem GET ou HEAD (predefinição).
    > [!NOTE]
    > Para uma carga mais baixa e um custo nas suas costas, a Porta Frontal recomenda a utilização de pedidos DE CABEÇA para sondas de saúde.

- **Intervalo (segundos)**: Define a frequência das sondas de saúde para as suas costas ou os intervalos em que cada um dos ambientes da porta frontal envia uma sonda.

    >[!NOTE]
    >Para falhas mais rápidas, desa um intervalo para um valor mais baixo. Quanto menor for o valor, maior é o volume da sonda de saúde que os seus backends recebem. Por exemplo, se o intervalo for definido para 30 segundos com, digamos, 100 POPs front door globalmente, cada backend receberá cerca de 200 pedidos de sonda por minuto.

Para mais informações, consulte [as sondas Health.](front-door-health-probes.md)

### <a name="load-balancing-settings"></a>Definições de equilíbrio de carga
As definições de equilíbrio de carga para o pool de backend definem como avaliamos as sondas de saúde. Estas definições determinam se o backend é saudável ou pouco saudável. Eles também verificam como equilibrar o tráfego entre diferentes backends na piscina de backend. Estão disponíveis as seguintes definições para a configuração de equilíbrio de carga:

- **Tamanho da amostra.** Identifica quantas amostras de sondas de saúde temos de considerar para uma avaliação de saúde.

- **Tamanho da amostra bem-sucedido.** Define o tamanho da amostra como mencionado anteriormente, o número de amostras bem sucedidas necessárias para chamar o backend saudável. Por exemplo, assuma que um intervalo de sonda de saúde da porta frontal é de 30 segundos, o tamanho da amostra é 5, e o tamanho da amostra bem-sucedido é 3. Cada vez que avaliamos as sondas de saúde para o seu backend, olhamos para as últimas cinco amostras em 150 segundos (5 x 30). Pelo menos três sondas bem sucedidas são necessárias para declarar o backend como saudável.

- **Sensibilidade à latência (latência adicional)**. Define se deseja que a Porta Frontal envie o pedido para retrocede dentro do intervalo de sensibilidade à medição de latência ou encaminhe o pedido para o backend mais próximo.

Para obter mais informações, consulte [o método de encaminhamento menos baseado em latência](front-door-routing-methods.md#latency).

## <a name="next-steps"></a>Passos seguintes

- [Criar um perfil da porta da frente](quickstart-create-front-door.md)
- [Como funciona a Porta da Frente](front-door-routing-architecture.md)
