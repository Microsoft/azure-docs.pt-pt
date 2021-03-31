---
title: Oferta sem servidor baseada em consumo em Azure Cosmos DB
description: Saiba mais sobre a oferta sem servidor da Azure Cosmos DB baseada no consumo.
author: ThomasWeiss
ms.author: thweiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/08/2021
ms.openlocfilehash: 3ee8d5f36977a5a9f20c7e636118ffa9f6ee0b6d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100570997"
---
# <a name="azure-cosmos-db-serverless-preview"></a>Azure Cosmos DB sem servidor (Pré-visualização)
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

O Azure Cosmos DB sem servidor permite-lhe utilizar a sua conta Azure Cosmos de forma baseada no consumo, onde só é cobrado pelas Unidades de Pedido consumidas pelas suas operações de base de dados e pelo armazenamento consumido pelos seus dados. Os contentores sem servidor podem servir milhares de pedidos por segundo sem carga mínima e sem necessidade de planeamento de capacidade.

> [!IMPORTANT] 
> Tem algum feedback sobre o servidor sem servidor? Queremos ouvi-la! Sinta-se livre para deixar uma mensagem para a equipa sem servidor da Azure Cosmos: [azurecosmosdbserverless@service.microsoft.com](mailto:azurecosmosdbserverless@service.microsoft.com) .

Ao utilizar o Azure Cosmos DB, todas as operações de base de dados têm um custo expresso nas [Unidades de Pedido](request-units.md). A forma como é cobrado por este custo depende do tipo de conta Azure Cosmos que está a utilizar:

- No modo [de produção previsto,](set-throughput.md) tem de se comprometer com uma certa quantidade de produção (expressa nas Unidades de Pedido por segundo) que está aprovisionada nas suas bases de dados e contentores. O custo das suas operações de base de dados é então deduzido do número de Unidades de Pedido disponíveis a cada segundo. No final do seu período de faturação, é cobrado pela quantidade de produção que anteste.
- No modo sem servidor, não é preciso prever qualquer produção na criação de contentores na sua conta Azure Cosmos. No final do seu período de faturação, é cobrado o número de Unidades de Pedido que foram consumidas pelas suas operações de base de dados.

## <a name="use-cases"></a>Casos de utilização

Azure Cosmos DB sem servidor melhor se encaixa em cenários onde se espera **tráfego intermitente e imprevisível** com longos tempos de marcha lenta. Uma vez que a capacidade de provisionamento nestas situações não é necessária e pode ser proibitiva em termos de custos, o servidor DB da Azure Cosmos deve ser considerado nos seguintes casos de utilização:

- Começando com Azure Cosmos DB
- Execução de aplicações com
    - tráfego rebentado, intermitente que é difícil de prever, ou
    - baixo (<10%) rácio de tráfego médio-máximo
- Desenvolvimento, teste, prototipagem e execução em produção de novas aplicações onde o padrão de tráfego é desconhecido
- Integração com serviços de computação sem servidor como [as Funções Azure](../azure-functions/functions-overview.md)

Veja como [escolher entre o produto de produção e](throughput-serverless.md) o artigo sem servidor para obter mais orientações sobre como escolher a oferta que melhor se adequa ao seu caso de utilização.

## <a name="using-serverless-resources"></a>Usando recursos sem servidor

Serverless é um novo tipo de conta Azure Cosmos, o que significa que você tem que escolher entre **produção provisida** e **sem servidor** ao criar uma nova conta. Tem de criar uma nova conta sem servidor para começar sem servidor. Durante o lançamento da pré-visualização, a única forma suportada de criar uma nova conta sem servidor é [utilizando o portal Azure](create-cosmosdb-resources-portal.md). A migração de contas existentes para/a partir do modo sem servidor não é suportada atualmente.

Qualquer recipiente criado numa conta sem servidor é um recipiente sem servidor. Os recipientes sem servidor expõem as mesmas capacidades que os contentores criados no modo de produção forerado, pelo que lê, escreve e consulta os seus dados da mesma forma. No entanto, contas e contentores sem servidor também têm características específicas:

> [!IMPORTANT]
> Algumas destas limitações podem ser facilitadas ou removidas quando o servidor fica geralmente disponível e **o seu feedback** vai ajudar-nos a decidir! Contacte-nos e conte-nos mais sobre a sua experiência sem servidor: [azurecosmosdbserverless@service.microsoft.com](mailto:azurecosmosdbserverless@service.microsoft.com) .

- Uma conta sem servidor só pode ser executada numa única região de Azure. Não é possível adicionar regiões Azure adicionais a uma conta sem servidor depois de a criar.
- Não é possível ativar a [funcionalidade de pré-visualização do Synapse Link](synapse-link.md) numa conta sem servidor.
- O fornecimento não é necessário em recipientes sem servidor, pelo que são aplicáveis as seguintes declarações:
    - Não é possível passar qualquer produção ao criar um recipiente sem servidor e, ao fazê-lo, retorna um erro.
    - Não é possível ler ou atualizar a produção num recipiente sem servidor e, ao fazê-lo, retorna um erro.
    - Não é possível criar uma base de dados de produção partilhada numa conta sem servidor e, ao fazê-lo, devolva um erro.
- Os recipientes sem servidor podem armazenar um máximo de 50 GB de dados e índices.

## <a name="monitoring-your-consumption"></a>Monitorização do seu consumo

Se já utilizou o Azure Cosmos DB no modo de produção a provisionado antes, verá que o servidor sem servidor é mais rentável quando o seu tráfego não justifica a capacidade a provisionada. A compensação é que os seus custos tornar-se-ão menos previsíveis porque você é cobrado com base no número de pedidos que a sua base de dados processou. Por causa disso, é importante estar atento ao seu consumo atual.

Ao navegar no painel **métrica** da sua conta, encontrará um gráfico denominado **Unidades de Pedido consumidas** no **separador Visão Geral.** Este gráfico mostra quantas Unidades de Pedido a sua conta consumiu:

:::image type="content" source="./media/serverless/request-units-consumed.png" alt-text="Gráfico mostrando as Unidades de Pedido consumidas" border="false":::

Pode encontrar o mesmo gráfico quando utilizar o Azure Monitor, como descrito [aqui.](monitor-request-unit-usage.md) Note que o Azure Monitor permite configurar [alertas,](../azure-monitor/alerts/alerts-metric-overview.md)que podem ser utilizados para o notificar quando o consumo da Unidade de Pedido tiver ultrapassado um determinado limiar.

## <a name="performance"></a><a id="performance"></a>Desempenho

Os recursos sem servidor produzem características de desempenho específicas que são diferentes das que os recursos de produção fornecem. Após a oferta sem servidor ficar geralmente disponível, a latência de contentores sem servidor será coberta por um Objetivo de Nível de Serviço (SLO) de 10 milissegundos ou menos para leituras pontuais e 30 milissegundos ou menos para escritas. Uma operação de leitura de ponto consiste em obter um único item pelo seu valor de ID e chave de partição.

## <a name="next-steps"></a>Passos seguintes

Começar com servidores sem servidor com os seguintes artigos:

- [Unidades de Pedido no Azure Cosmos DB](request-units.md)
- [Escolha entre débito aprovisionado e um modelo sem servidor](throughput-serverless.md)
- [Modelo de preços no Azure Cosmos DB](how-pricing-works.md)
