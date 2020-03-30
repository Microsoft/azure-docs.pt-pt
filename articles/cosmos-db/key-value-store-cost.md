---
title: Solicitar taxas unitárias para a Azure Cosmos DB como uma loja de valor-chave
description: Conheça as taxas da unidade de pedidos da Azure Cosmos DB para simples operações de escrita e leitura quando é usada como uma loja chave/valor.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/23/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 5b2ee8b5bf19f16d7f7f04e9515fe591db7132f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77647510"
---
# <a name="azure-cosmos-db-as-a-key-value-store--cost-overview"></a>Azure Cosmos DB como uma loja de valor chave - visão geral do custo

O Azure Cosmos DB é um serviço de base de dados multimodelo distribuído globalmente para a construção de aplicações altamente disponíveis e em larga escala facilmente. Por padrão, o Azure Cosmos DB indexa automaticamente e eficientemente todos os dados que ingeque. Isto permite consultas rápidas e consistentes [de SQL](how-to-sql-query.md) (e [JavaScript)](stored-procedures-triggers-udfs.md)nos dados. 

Este artigo descreve o custo do Azure Cosmos DB para simples operações de escrita e leitura quando é usado como uma loja chave/valor. As operações de escrita incluem inserções, substituições, exclusões e upserts de itens de dados. Além de garantir uma disponibilidade de 99,999% para todas as contas multi-regiões, a Azure Cosmos DB oferece <latência garantida de 10 ms para leituras e para os (indexados) escritos, no percentil 99. 

## <a name="why-we-use-request-units-rus"></a>Por que usamos Unidades de Pedido (RUs)

O desempenho da Azure Cosmos DB baseia-se na quantidade de entrada disponibilizada expressa nas [Unidades](request-units.md) de Pedido (RU/s). O fornecimento encontra-se numa segunda granularidade e é adquirido em RU/s[(não confundir com a faturação horária).](https://azure.microsoft.com/pricing/details/cosmos-db/) As RUs devem ser consideradas como uma abstração lógica (uma moeda) que simplifica o fornecimento de um produto obrigatório para a aplicação. Os utilizadores não têm de pensar em diferenciar entre ler e escrever a entrada. O modelo da moeda única das RUs cria eficiências para partilhar a capacidade provisionada entre leituras e escritos. Este modelo de capacidade provisionado permite ao serviço fornecer uma **entrada previsível e consistente, baixa latência garantida e elevada disponibilidade.** Finalmente, enquanto o modelo RU é usado para retratar a entrada, cada RU aprovisionado também tem uma quantidade definida de recursos (por exemplo, memória, núcleos/CPU e IOPS).

Como um sistema de base de dados distribuído globalmente, o Cosmos DB é o único serviço Azure que fornece SLAs abrangentes que cobrem latência, produção, consistência e alta disponibilidade. A provisão de entrada é aplicada a cada uma das regiões associadas à sua conta Cosmos. Para leituras, cosmos DB oferece múltiplos níveis de consistência bem [definidos](consistency-levels.md) para você escolher. 

O quadro seguinte mostra o número de RUs necessários para realizar operações de leitura e escrita com base num item de dados do tamanho 1 KB e 100 KBs com indexação automática padrão desligada. 

|Tamanho do artigo|1 Ler|1 Escrever|
|-------------|------|-------|
|1 KB|1 RU|5 RUs|
|100 KB|10 RUs|50 RUs|

## <a name="cost-of-reads-and-writes"></a>Custo das leituras e dos escritos

Se fornecer 1.000 RU/s, isto equivale a 3,6 milhões de RU/hora e custará $0,08 por hora (nos EUA e na Europa). Para um item de dados de tamanho 1 KB, isto significa que pode consumir 3,6 milhões de leituras ou 0,72 milhões de escritos (3,6 milhões de RU/ 5) utilizando a sua entrada provisionada. Normalizado para milhões de leituras e escritos, o custo seria $0.022 /milhão de leituras ($0,08 / 3,6) e $0.111/milhão de escritos ($0,08 / 0,72). O custo por milhão torna-se mínimo como indicado na tabela abaixo.

|Tamanho do artigo|Custo de 1 milhão de leituras|Custo de 1 milhão de escritos|
|-------------|-------|--------|
|1 KB|$0.022|$0.111|
|100 KB|$0.222|$1.111|


A maioria dos serviços básicos de blob ou lojas de objetos cobram $0,40 por milhão de transações de leitura e $5 por milhão de transações escritas. Se usado da melhor forma, cosmos DB pode ser até 98% mais barato do que estas outras soluções (para 1 transações KB).

## <a name="next-steps"></a>Passos seguintes

* Utilize [a calculadora RU](https://cosmos.azure.com/capacitycalculator/) para estimar a entrada para as suas cargas de trabalho.

