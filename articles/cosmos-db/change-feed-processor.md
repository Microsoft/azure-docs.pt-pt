---
title: Processador do feed de alterações no Azure Cosmos DB
description: Saiba como utilizar o processador de alimentação de alteração Azure Cosmos para ler o feed de alteração, os componentes do processador change feed
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 10/12/2020
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 409b51682700a8b13b2840f171642bdcbee6f6d2
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93340231"
---
# <a name="change-feed-processor-in-azure-cosmos-db"></a>Processador do feed de alterações no Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

O processador change feed faz parte do [Azure Cosmos DB SDK V3](https://github.com/Azure/azure-cosmos-dotnet-v3). Simplifica o processo de leitura do feed de alteração e distribui eficazmente o processamento do evento em vários consumidores.

O principal benefício da biblioteca de processadores de feed change é o seu comportamento tolerante a falhas que garante uma entrega "pelo menos uma vez" de todos os eventos no feed de mudança.

## <a name="components-of-the-change-feed-processor"></a>Componentes do processador de alimentação de alteração

Existem quatro componentes principais da implementação do processador do feed de alterações:

1. **Contentor monitorizado:** o contentor monitorizado possui os dados a partir dos quais o feed de alterações é gerado. Quaisquer inserções e atualizações ao contentor monitorizado serão refletidas no feed de alterações do contentor.

1. **Contentor de concessão:** o contentor de concessão funciona como um armazenamento de estados e coordena o processamento do feed de alterações entre várias funções de trabalho. Pode armazenar o contentor de concessão na mesma conta que o contentor monitorizado ou numa conta separada.

1. **Anfitrião:** um anfitrião é uma instância de aplicação que utiliza o processador do feed de alterações para escutar as alterações. Várias instâncias com a mesma configuração de concessão podem ser executadas em paralelo, mas cada instância deve ter um **nome de instância** diferente.

1. **Delegado:** o delegado é o código que define o que o programador quer fazer com cada lote de alterações que o processador do feed de alterações lê. 

Para entender melhor como estes quatro elementos do processador de feed de mudança funcionam em conjunto, vamos olhar para um exemplo no diagrama seguinte. O contentor monitorizado armazena documentos e utiliza a "Cidade" como chave de partição. Vemos que os valores-chave da partição são distribuídos em gamas que contêm itens. Existem duas instâncias hospedeiras e o processador de feed de alteração está a atribuir diferentes gamas de valores-chave de partição a cada instância para maximizar a distribuição de computação. Cada gama está a ser lida em paralelo e o seu progresso é mantido separadamente de outras gamas no contentor de arrendamento.

:::image type="content" source="./media/change-feed-processor/changefeedprocessor.png" alt-text="Alterar exemplo do processador de feed" border="false":::

## <a name="implementing-the-change-feed-processor"></a>Implementação do processador de feed de alteração

O ponto de entrada é sempre o recipiente monitorizado, a partir de um `Container` caso a que se `GetChangeFeedProcessorBuilder` chama:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-change-feed-processor/src/Program.cs?name=DefineProcessor)]

Onde o primeiro parâmetro é um nome distinto que descreve o objetivo deste processador e o segundo nome é a implementação do delegado que irá lidar com as mudanças. 

Um exemplo de um delegado seria:


[!code-csharp[Main](~/samples-cosmosdb-dotnet-change-feed-processor/src/Program.cs?name=Delegate)]

Por fim, define um nome para este caso de processador com `WithInstanceName` e qual é o recipiente para manter o estado de locação com `WithLeaseContainer` .

A chamada `Build` dá-lhe a instância do processador que pode começar por `StartAsync` ligar.

## <a name="processing-life-cycle"></a>Ciclo de vida de processamento

O ciclo de vida normal de uma instância do anfitrião é:

1. Leia o feed de mudança.
1. Se não houver alterações, durma por um período de tempo predefinido (personalizável com `WithPollInterval` o Construtor) e vá para #1.
1. Se houver alterações, envie-as ao **delegado.**
1. Quando o delegado terminar o processamento **com sucesso,** atualize a loja de arrendamento com o ponto processado mais recente no tempo e vá para #1.

## <a name="error-handling"></a>Processamento de erros

O processador de feed de alteração é resistente a erros de código do utilizador. Isto significa que se a implementação do seu delegado tiver uma exceção não manipulada (passo #4), o processamento de fio que determinado lote de alterações será interrompido, e um novo fio será criado. O novo fio verificará qual foi o ponto mais recente no tempo que a loja de arrendamento tem para essa gama de valores-chave de partição, e reiniciará a partir daí, enviando efetivamente o mesmo lote de alterações ao delegado. Este comportamento continuará até que o seu delegado processe as alterações corretamente e é a razão pela qual o processador de feed de alteração tem uma garantia "pelo menos uma vez", porque se o código dedelegado lançar uma exceção, ele vai voltar a tentar esse lote.

Para evitar que o seu processador de feed de alteração fique "preso" continuamente a tentar o mesmo lote de alterações, deve adicionar lógica no seu código de delegado para escrever documentos, a exceção, a uma fila de letras mortas. Este design garante que pode acompanhar as alterações não processadas, enquanto ainda é capaz de continuar a processar futuras alterações. A fila das cartas mortas pode ser outro contentor do Cosmos. A loja exata de dados não importa, simplesmente que as alterações não processadas são persistidos.

Além disso, pode utilizar o [estimador de feed de alteração](how-to-use-change-feed-estimator.md) para monitorizar o progresso das instâncias do seu processador de alteração à medida que lêem o feed de alteração. Pode utilizar esta estimativa para entender se o seu processador de feed de mudança está "preso" ou atrasado devido a recursos disponíveis como CPU, memória e largura de banda de rede.

## <a name="deployment-unit"></a>Unidade de implantação

Uma única unidade de implantação do processador de alimentação de alteração consiste numa ou mais instâncias com a mesma `processorName` configuração do recipiente de aluguer e de aluguer. Pode ter muitas unidades de implantação onde cada uma tem um fluxo de negócio diferente para as alterações e cada unidade de implantação composta por um ou mais casos. 

Por exemplo, pode ter uma unidade de implantação que desencadeie uma API externa sempre que houver uma alteração no seu recipiente. Outra unidade de implantação pode mover dados, em tempo real, cada vez que há uma mudança. Quando uma mudança acontecer no seu recipiente monitorizado, todas as suas unidades de implantação serão notificadas.

## <a name="dynamic-scaling"></a>Dimensionamento dinâmico

Como mencionado anteriormente, dentro de uma unidade de implantação pode ter um ou mais casos. Para tirar partido da distribuição do cálculo dentro da unidade de implantação, os únicos requisitos-chave são:

1. Todas as instâncias devem ter a mesma configuração do recipiente de arrendamento.
1. Todas as instâncias devem ter o `processorName` mesmo.
1. Cada instância precisa de ter um nome de instância diferente (`WithInstanceName`).

Se estas três condições se aplicarem, então o processador de feed de alteração distribuirá, usando um algoritmo de distribuição igual, todos os locados do recipiente de locação em todas as instâncias de execução dessa unidade de implantação e paralizará o cálculo. Um contrato de arrendamento só pode ser propriedade de uma instância de cada vez, pelo que o número máximo de casos equivale ao número de contratos de arrendamento.

O número de casos pode crescer e diminuir, e o processador de alimentação de alteração ajustará dinamicamente a carga redistribuindo em conformidade.

Além disso, o processador de alimentação de alteração pode ajustar-se dinamicamente à escala de contentores devido a aumentos de produção ou armazenamento. Quando o seu recipiente cresce, o processador de feed de mudança lida de forma transparente com estes cenários, aumentando dinamicamente os locados e distribuindo os novos arrendamentos entre as instâncias existentes.

## <a name="change-feed-and-provisioned-throughput"></a>Alterar alimentação e produção a provisionada

Alterar as operações de leitura de alimentação no recipiente monitorizado consumirá RUs. 

As operações no contentor de arrendamento consomem RUs. Quanto maior for o número de casos que utilizam o mesmo recipiente de arrendamento, maior será o potencial de consumo de RU. Lembre-se de monitorizar o seu consumo de RU no recipiente de locação se decidir escalar e aumentar o número de ocorrências.

## <a name="starting-time"></a>Hora de início

Por predefinição, quando um processador de alimentação de alteração começa pela primeira vez, rubricará o recipiente de locação e iniciará o seu [ciclo de vida de processamento](#processing-life-cycle). Quaisquer alterações que ocorreram no recipiente monitorizado antes da inicializada do processador de alimentação de alteração pela primeira vez não serão detetadas.

### <a name="reading-from-a-previous-date-and-time"></a>Leitura a partir de uma data e hora anteriores

É possível inicializar o processador de feed de alteração para ler alterações a partir de uma **data e hora específicas,** passando uma instância de a para `DateTime` a extensão do `WithStartTime` construtor:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=TimeInitialization)]

O processador de feed de alteração será inicializado para essa data e hora específicas e começará a ler as alterações que ocorreram depois.

### <a name="reading-from-the-beginning"></a>Leitura desde o início

Noutros cenários, como a migração de dados ou a análise de toda a história de um contentor, precisamos de ler a alteração da alimentação a partir do **início da vida desse contentor.** Para isso, podemos usar `WithStartTime` na extensão do construtor, mas passando, `DateTime.MinValue.ToUniversalTime()` o que geraria a representação UTC do `DateTime` valor mínimo, assim:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartFromBeginningInitialization)]

O processador de alimentação de alteração será inicializado e começará a ler alterações a partir do início da vida útil do recipiente.

> [!NOTE]
> Estas opções de personalização apenas funcionam para configurar o ponto de partida no tempo do processador de feed de alteração. Uma vez que o recipiente de arrendamento é inicializado pela primeira vez, mudá-los não tem efeito.

## <a name="where-to-host-the-change-feed-processor"></a>Onde hospedar o processador change feed

O processador de feed de alteração pode ser hospedado em qualquer plataforma que suporte processos ou tarefas de longa duração:

* Um [Azure WebJob](/learn/modules/run-web-app-background-task-with-webjobs/)em execução contínua .
* Um processo numa [Máquina Virtual Azure.](/azure/architecture/best-practices/background-jobs#azure-virtual-machines)
* Um trabalho de fundo no [Serviço Azure Kubernetes.](/azure/architecture/best-practices/background-jobs#azure-kubernetes-service)
* Um [serviço ASP.NET hospedado.](/aspnet/core/fundamentals/host/hosted-services)

Embora o processador de feed de mudança possa funcionar em ambientes de curta duração, porque o recipiente de arrendamento mantém o estado, o ciclo de arranque destes ambientes irá adicionar atraso para receber as notificações (devido à sobrecarga de arranque do processador cada vez que o ambiente é iniciado).

## <a name="additional-resources"></a>Recursos adicionais

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [Aplicação completa da amostra no GitHub](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)
* [Amostras de utilização adicionais no GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [Cosmos DB workshop laboratórios para processador de feed de mudança](https://azurecosmosdb.github.io/labs/dotnet/labs/08-change_feed_with_azure_functions.html#consume-cosmos-db-change-feed-via-the-change-feed-processor)

## <a name="next-steps"></a>Passos seguintes

Pode agora proceder para saber mais sobre o processador de feed de mudança nos seguintes artigos:

* [Visão geral do feed de mudança](change-feed.md)
* [Modelo Pull do feed de alterações](change-feed-pull-model.md)
* [Como migrar da biblioteca do processador de mudanças](how-to-migrate-from-change-feed-library.md)
* [Utilizar o calculador do feed de alterações](how-to-use-change-feed-estimator.md)
* [Hora de início do processador do feed de alterações](#starting-time)