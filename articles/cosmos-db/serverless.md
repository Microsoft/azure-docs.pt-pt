---
title: Oferta sem servidor baseada em consumo em Azure Cosmos DB
description: Saiba mais sobre a oferta sem servidor da Azure Cosmos DB baseada no consumo.
author: ThomasWeiss
ms.author: thweiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.openlocfilehash: 75d22a5021c7c8ae3a12f25644f2875e0ccf8cdd
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93098761"
---
# <a name="azure-cosmos-db-serverless-preview"></a>Azure Cosmos DB sem servidor (Pré-visualização)
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

> [!IMPORTANT]
> Azure Cosmos DB sem servidor está atualmente em pré-visualização. Esta versão de pré-visualização é fornecida sem um Acordo de Nível de Serviço e não é recomendada para cargas de trabalho de produção. Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O Azure Cosmos DB sem servidor permite-lhe utilizar a sua conta Azure Cosmos de forma baseada no consumo, onde só é cobrado pelas Unidades de Pedido consumidas pelas suas operações de base de dados e pelo armazenamento consumido pelos seus dados. Não existe uma carga mínima envolvida ao utilizar o Azure Cosmos DB no modo sem servidor.

> [!IMPORTANT] 
> Tem algum feedback sobre o servidor sem servidor? Queremos ouvi-la! Sinta-se livre para deixar uma mensagem para a equipa sem servidor da Azure Cosmos: [azurecosmosdbserverless@service.microsoft.com](mailto:azurecosmosdbserverless@service.microsoft.com) .

Ao utilizar o Azure Cosmos DB, todas as operações de base de dados têm um custo expresso nas [Unidades de Pedido](request-units.md). A forma como é cobrado por este custo depende do tipo de conta Azure Cosmos que está a utilizar:

- No modo [de produção previsto,](set-throughput.md) tem de se comprometer com uma certa quantidade de produção (expressa nas Unidades de Pedido por segundo) que está aprovisionada nas suas bases de dados e contentores. O custo das suas operações de base de dados é então deduzido do número de Unidades de Pedido disponíveis a cada segundo. No final do seu período de faturação, é cobrado pela quantidade de produção que anteste.
- No modo sem servidor, não é preciso prever qualquer produção na criação de contentores na sua conta Azure Cosmos. No final do seu período de faturação, é cobrado o número de Unidades de Pedido que foram consumidas pelas suas operações de base de dados.

## <a name="use-cases"></a>Casos de utilização

Azure Cosmos DB sem servidores melhor se encaixa em cenários onde você espera:

- **Tráfego de** luz : Porque a capacidade de provisionamento nestas situações não é necessária e pode ser proibitiva em termos de custos
- **Burstability moderada** : Porque os recipientes sem servidor podem entregar até 5.000 Unidades de Pedido por segundo
- **Desempenho moderado** : Porque os recipientes sem servidor têm [características de desempenho específicas](#performance)

Por estas razões, a Azure Cosmos DB sem servidor deve ser considerada para os seguintes tipos de carga de trabalho:

- Desenvolvimento
- Testar
- Prototipagem
- Prova de conceito
- Aplicação não crítica com tráfego de luz

Veja como [escolher entre o produto de produção e](throughput-serverless.md) o artigo sem servidor para obter mais orientações sobre como escolher a oferta que melhor se adequa ao seu caso de utilização.

## <a name="using-serverless-resources"></a>Usando recursos sem servidor

Serverless é um novo tipo de conta Azure Cosmos, o que significa que você tem que escolher entre **produção provisida** e **sem servidor** ao criar uma nova conta. Tem de criar uma nova conta sem servidor para começar sem servidor. Durante o lançamento da pré-visualização, a única forma suportada de criar uma nova conta sem servidor é [utilizando o portal Azure](create-cosmosdb-resources-portal.md). A migração de contas existentes para/a partir do modo sem servidor não é suportada atualmente.

> [!NOTE]
> Atualmente, o Serverless é suportado apenas na API do Azure Cosmos DB Core (SQL).

Qualquer recipiente criado numa conta sem servidor é um recipiente sem servidor. Os recipientes sem servidor expõem as mesmas capacidades que os contentores criados no modo de produção forerado, pelo que lê, escreve e consulta os seus dados da mesma forma. No entanto, contas e contentores sem servidor também têm características específicas:

> [!IMPORTANT]
> Algumas destas limitações podem ser facilitadas ou removidas quando o servidor fica geralmente disponível e **o seu feedback** vai ajudar-nos a decidir! Contacte-nos e conte-nos mais sobre a sua experiência sem servidor: [azurecosmosdbserverless@service.microsoft.com](mailto:azurecosmosdbserverless@service.microsoft.com) .

- Uma conta sem servidor só pode ser executada numa única região de Azure. Não é possível adicionar regiões Azure adicionais a uma conta sem servidor depois de a criar.
- Não é possível ativar a [funcionalidade de pré-visualização do Synapse Link](synapse-link.md) numa conta sem servidor.
- O fornecimento não é necessário em recipientes sem servidor, pelo que são aplicáveis as seguintes declarações:
    - Não é possível passar qualquer produção ao criar um recipiente sem servidor e, ao fazê-lo, retorna um erro.
    - Não é possível ler ou atualizar a produção num recipiente sem servidor e, ao fazê-lo, retorna um erro.
    - Não é possível criar uma base de dados de produção partilhada numa conta sem servidor e, ao fazê-lo, devolva um erro.
- Os contentores sem servidor podem fornecer uma capacidade máxima de produção de 5.000 Unidades de Pedido por segundo.
- Os recipientes sem servidor podem armazenar um máximo de 50 GB de dados e índices.

## <a name="monitoring-your-consumption"></a>Monitorização do seu consumo

Se já utilizou o Azure Cosmos DB no modo de produção a provisionado antes, verá que o servidor sem servidor é mais rentável quando o seu tráfego não justifica a capacidade a provisionada. A compensação é que os seus custos tornar-se-ão menos previsíveis porque você é cobrado com base no número de pedidos que a sua base de dados processou. Por causa disso, é importante estar atento ao seu consumo atual.

Ao navegar no painel **métrica** da sua conta, encontrará um gráfico denominado **Unidades de Pedido consumidas** no **separador Visão Geral.** Este gráfico mostra quantas Unidades de Pedido a sua conta consumiu:

:::image type="content" source="./media/serverless/request-units-consumed.png" alt-text="Gráfico mostrando as Unidades de Pedido consumidas" border="false":::

Pode encontrar o mesmo gráfico quando utilizar o Azure Monitor, como descrito [aqui.](monitor-request-unit-usage.md) Note que o Azure Monitor permite configurar [alertas,](../azure-monitor/platform/alerts-metric-overview.md)que podem ser utilizados para o notificar quando o consumo da Unidade de Pedido tiver ultrapassado um determinado limiar.

## <a name="performance"></a><a id="performance"></a>Desempenho

Os recursos sem servidor produzem características de desempenho específicas que são diferentes das que os recursos de produção a forrados fornecem:

- **Disponibilidade** : Após a oferta sem servidor ficar geralmente disponível, a disponibilidade de recipientes sem servidor será coberta por um Acordo de Nível de Serviço (SLA) de 99,9% quando as Zonas de Disponibilidade (redundância de zona) não forem utilizadas. O SLA é de 99,99% quando as Zonas de Disponibilidade são utilizadas.
- **Latência** : Após a oferta sem servidor tornar-se geralmente disponível, a latência de recipientes sem servidor será coberta por um Objetivo de Nível de Serviço (SLO) de 10 milissegundos ou menos para leituras pontuais e 30 milissegundos ou menos para escritas. Uma operação de leitura de ponto consiste em obter um único item pelo seu valor de ID e chave de partição.
- **Burstability** : Após a oferta sem servidor ficar geralmente disponível, a capacidade de explosão de contentores sem servidor será coberta por um Objetivo de Nível de Serviço (SLO) de 95%. Isto significa que a capacidade máxima de explosão pode ser alcançada pelo menos 95% das vezes.

> [!NOTE]
> Como qualquer pré-visualização do Azure Cosmos, o servidor DB do Azure Cosmos está excluído dos Acordos de Nível de Serviço (SLA). As características de desempenho acima mencionadas são fornecidas como uma pré-visualização do que esta oferta irá entregar quando geralmente disponível.

## <a name="next-steps"></a>Passos seguintes

Começar com servidores sem servidor com os seguintes artigos:

- [Unidades de Pedido no Azure Cosmos DB](request-units.md)
- [Escolha entre débito aprovisionado e um modelo sem servidor](throughput-serverless.md)
- [Modelo de preços no Azure Cosmos DB](how-pricing-works.md)
