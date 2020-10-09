---
title: Solicite taxas unitárias para a Azure Cosmos DB como uma loja de valor chave
description: Saiba mais sobre as taxas da unidade de pedido da Azure Cosmos DB para operações simples de escrita e leitura quando é usada como uma loja chave/valor.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: how-to
ms.date: 08/23/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 1cd6b4b52db224db5febcec1eff79b01379a5956
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85262825"
---
# <a name="azure-cosmos-db-as-a-key-value-store--cost-overview"></a>Azure Cosmos DB como uma loja de valor chave - visão geral de custos

Azure Cosmos DB é um serviço de base de dados multi-modelo distribuído globalmente para a construção de aplicações altamente disponíveis e em larga escala facilmente. Por padrão, a Azure Cosmos DB indexa de forma automática e eficiente todos os dados que ingere. Isto permite consultas rápidas e consistentes [de SQL](how-to-sql-query.md) (e [JavaScript)](stored-procedures-triggers-udfs.md)sobre os dados. 

Este artigo descreve o custo da Azure Cosmos DB para operações simples de escrita e leitura quando é usado como uma loja chave/valor. As operações de escrita incluem inserções, substituições, eliminações e eserções de itens de dados. Além de garantir uma disponibilidade de 99,999% para todas as contas multi-regiões, a Azure Cosmos DB oferece <latência de 10 ms para leituras e para as (indexadas) escreve, no percentil 99. 

## <a name="why-we-use-request-units-rus"></a>Por que usamos Unidades de Pedido (RUs)

O desempenho do DB da Azure Cosmos baseia-se na quantidade de produção provisida expressa nas [Unidades de Pedido](request-units.md) (RU/s). O provisionamento encontra-se numa segunda granularidade e é adquirido em RU/s ([não deve ser confundido com a faturação horária).](https://azure.microsoft.com/pricing/details/cosmos-db/) As RUs devem ser consideradas como uma abstração lógica (uma moeda) que simplifica o fornecimento do rendimento exigido para a aplicação. Os utilizadores não têm de pensar em diferenciar entre ler e escrever. O modelo de moeda única das RUs cria eficiências para partilhar a capacidade prevista entre leituras e escritos. Este modelo de capacidade a provisionada permite que o serviço forneça uma **produção previsível e consistente, baixa latência garantida e elevada disponibilidade.** Finalmente, enquanto o modelo RU é usado para retratar a produção, cada RU a forcado também tem uma quantidade definida de recursos (por exemplo, memória, núcleos/CPU e IOPS).

Como um sistema de base de dados distribuído globalmente, o Cosmos DB é o único serviço Azure que fornece SLAs abrangentes cobrindo latência, produção, consistência e alta disponibilidade. A produção que fornece é aplicada a cada uma das regiões associadas à sua conta Cosmos. Para leituras, a Cosmos DB oferece múltiplos níveis de consistência bem [definidos](consistency-levels.md) para você escolher. 

A tabela seguinte mostra o número de RUs necessários para executar operações de leitura e escrita com base num item de dados do tamanho 1 KB e 100 KBs com indexação automática predefinida desligada. 

|Tamanho do item|1 Ler|1 Escrever|
|-------------|------|-------|
|1 KB|1 RU|5 RUs|
|100 KB|10 RUs|50 RUs|

## <a name="cost-of-reads-and-writes"></a>Custo das leituras e dos escritos

Se você provisionar 1.000 RU/s, isto equivale a 3,6 milhões de RU/hora e custará $0,08 por hora (nos EUA e na Europa). Para um item de dados de tamanho KB de 1 KB, isto significa que você pode consumir 3,6 milhões de leituras ou 0,72 milhões de escritos (3,6 milhões RU / 5) usando o seu rendimento provisitado. Normalizado para milhões de leituras e escritos, o custo seria $0.022 /milhão de leituras ($0,08 / 3,6) e $0.111/milhão de escritos ($0,08 / 0,72). O custo por milhão torna-se mínimo, como mostra a tabela abaixo.

|Tamanho do item|Custo de 1 milhão de leituras|Custo de 1 milhão de escritos|
|-------------|-------|--------|
|1 KB|$0,022|$0.111|
|100 KB|$0.222|$1.111|


A maioria dos serviços básicos de blob ou lojas de objetos cobram $0,40 por transação de leitura e $5 por milhão de transações de escrita. Se for utilizado da melhor forma, o Cosmos DB pode ser até 98% mais barato do que estas outras soluções (para 1 transações de KB).

## <a name="next-steps"></a>Passos seguintes

* Utilize [calculadora RU](https://cosmos.azure.com/capacitycalculator/) para estimar a produção das suas cargas de trabalho.

