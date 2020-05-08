---
title: Processador do feed de alterações no Azure Cosmos DB
description: Saiba como usar o processador de feed de mudança Azure Cosmos DB para ler o feed de mudança, os componentes do processador de feed de mudança
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 4/29/2020
ms.reviewer: sngun
ms.openlocfilehash: d069df0a095cc0356cd61155dde875a5d92ed18d
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82594156"
---
# <a name="change-feed-processor-in-azure-cosmos-db"></a>Processador do feed de alterações no Azure Cosmos DB

O processador de feed de mudança faz parte do [Azure Cosmos DB SDK V3](https://github.com/Azure/azure-cosmos-dotnet-v3). Simplifica o processo de leitura do feed de mudança e distribui eficazmente o processamento do evento por vários consumidores.

O principal benefício da biblioteca de processadores de feed de mudança é o seu comportamento tolerante a falhas que assegura uma entrega "pelo menos uma vez" de todos os eventos no feed de mudança.

## <a name="components-of-the-change-feed-processor"></a>Componentes do processador de alimentos para alterações

Existem quatro componentes principais da implementação do processador de alimentação de mudança:

1. **O recipiente monitorizado:** O recipiente monitorizado tem os dados a partir dos quais o feed de mudança é gerado. Quaisquer inserções e atualizações do recipiente monitorizado refletem-se na alimentação de alterações do recipiente.

1. **O contentor de aluguer:** O contentor de arrendamento funciona como um armazenamento estatal e coordena o processamento da mudança de alimentos em vários trabalhadores. O contentor de aluguer pode ser armazenado na mesma conta que o recipiente monitorizado ou numa conta separada.

1. **O hospedeiro:** Um hospedeiro é uma instância de aplicação que utiliza o processador de feed de mudança para ouvir alterações. Várias instâncias com a mesma configuração de locação podem ser executadas em paralelo, mas cada instância deve ter um nome de **instância**diferente .

1. **O delegado:** O delegado é o código que define o que você, o desenvolvedor, quer fazer com cada lote de alterações que o processador de feed de mudança lê. 

Para entender ainda como estes quatro elementos do processador de feed de mudança funcionam em conjunto, vamos olhar para um exemplo no diagrama seguinte. O contentor monitorizado armazena documentos e utiliza a 'Cidade' como chave de partição. Vemos que os valores-chave da partilha são distribuídos em gamas que contêm itens. Existem duas instâncias hospedeiras e o processador de feed de mudança está a atribuir diferentes gamas de valores-chave de divisórias a cada instância para maximizar a distribuição do cálculo. Cada gama está a ser lida paralelamente e o seu progresso é mantido separadamente de outras gamas no contentor de aluguer.

![Alterar exemplo de processador de feed](./media/change-feed-processor/changefeedprocessor.png)

## <a name="implementing-the-change-feed-processor"></a>Implementação do processador de feed de mudança

O ponto de entrada é sempre o `Container` recipiente `GetChangeFeedProcessorBuilder`monitorizado, a partir de um caso a que se chama:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-change-feed-processor/src/Program.cs?name=DefineProcessor)]

Onde o primeiro parâmetro é um nome distinto que descreve o objetivo deste processador e o segundo nome é a implementação do delegado que irá lidar com as alterações. 

Um exemplo de um delegado seria:


[!code-csharp[Main](~/samples-cosmosdb-dotnet-change-feed-processor/src/Program.cs?name=Delegate)]

Finalmente, define um nome para `WithInstanceName` esta instância do processador com `WithLeaseContainer`e qual é o recipiente para manter o estado de arrendamento com .

A `Build` chamada irá dar-lhe a instância `StartAsync`do processador que pode começar por ligar .

## <a name="processing-life-cycle"></a>Processamento do ciclo de vida

O ciclo de vida normal de um hospedeiro é:

1. Leia o feed de mudança.
1. Se não houver alterações, durma durante um período `WithPollInterval` de tempo predefinido (personalizável com o Construtor) e vá para #1.
1. Se houver alterações, envie-as ao **delegado.**
1. Quando o delegado terminar de processar as alterações **com sucesso,** atualize a loja de arrendamento com o mais recente ponto processado no tempo e vá para #1.

## <a name="error-handling"></a>Processamento de erros

O processador de alimentação de alteração é resiliente a erros de código do utilizador. Isto significa que se a implementação do seu delegado tiver uma exceção não tratada (passo #4), o processamento de fios que determinado lote de alterações será interrompido, e um novo fio será criado. A nova linha verificará qual foi o último ponto no tempo que a loja de arrendamento tem para essa gama de valores-chave de divisórias, e reiniciar a partir daí, efetivamente enviando o mesmo lote de alterações ao delegado. Este comportamento continuará até que o seu delegado processe as alterações corretamente e é por isso que o processador de feed de mudança tem uma garantia "pelo menos uma vez", porque se o código delegado lançar uma exceção, irá voltar a tentar esse lote.

Para evitar que o seu processador de feed de mudança fique "preso" continuamente a tentar o mesmo lote de alterações, deve adicionar lógica no seu código de delegado para escrever documentos, exceto, a uma fila de cartas mortas. Este design garante que pode acompanhar alterações não processadas enquanto ainda pode continuar a processar futuras alterações. A fila de cartas mortas pode ser apenas outro contentor cosmos. A loja de dados exata não importa, simplesmente que as alterações não processadas são perduradas.

Além disso, pode utilizar o estimador de alimentação de [alterações](how-to-use-change-feed-estimator.md) para monitorizar o progresso das instâncias do seu processador de feed de mudança à medida que lêem o feed de mudança. Além de monitorizar se o processador de feed de mudança ficar "preso" continuamente a tentar o mesmo lote de alterações, também pode entender se o seu processador de feed de mudança está atrasado devido a recursos disponíveis como CPU, memória e largura de banda da rede.

## <a name="dynamic-scaling"></a>Dimensionamento dinâmico

Como mencionado durante a introdução, o processador de feed de mudança pode distribuir a computação em várias instâncias automaticamente. Pode implementar várias instâncias da sua aplicação utilizando o processador de feed de mudança e tirar partido dela, os únicos requisitos-chave são:

1. Todas as instâncias devem ter a mesma configuração de contentor de aluguer.
1. Todos os casos devem ter o mesmo nome de fluxo de trabalho.
1. Cada instância precisa de ter`WithInstanceName`um nome de instância diferente ( ).

Se estas três condições se aplicarem, o processador de feed de mudança irá, usando um algoritmo de distribuição igual, distribuir todos os arrendamentos no contentor de aluguer em todas as instâncias de execução e paralelamente a computação. Um contrato de arrendamento só pode ser propriedade de um caso num dado momento, pelo que o número máximo de casos equivale ao número de locações.

O número de casos pode crescer e diminuir, e o processador de feed de mudança irá ajustar dinamicamente a carga redistribuindo em conformidade.

Além disso, o processador de alimentos para alterações pode ajustar-se dinamicamente à escala dos contentores devido ao aumento da entrada ou armazenamento. Quando o seu recipiente cresce, o processador de feed de mudança lida de forma transparente com estes cenários aumentando dinamicamente os contratos de arrendamento e distribuindo os novos arrendamentos entre os casos existentes.

## <a name="change-feed-and-provisioned-throughput"></a>Alterar alimentos e provisão provisionada

É cobrado pelas RUs consumidas, uma vez que o movimento de dados dentro e fora dos contentores da Cosmos consome sempre RUs. É cobrado pelas RUs consumidas pelo contentor de aluguer.

## <a name="additional-resources"></a>Recursos adicionais

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [Amostras de utilização no GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [Amostras adicionais no GitHub](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Passos seguintes

Pode agora proceder a mais informações sobre o processador de feed de mudança nos seguintes artigos:

* [Visão geral do feed de mudança](change-feed.md)
* [Como migrar da biblioteca de processadores de feed de mudança](how-to-migrate-from-change-feed-library.md)
* [Utilizar o calculador do feed de alterações](how-to-use-change-feed-estimator.md)
* [Hora de início do processador do feed de alterações](how-to-configure-change-feed-start-time.md)
