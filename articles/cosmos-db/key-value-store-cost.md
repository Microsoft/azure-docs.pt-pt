---
title: Custos da unidade de pedido para o Azure Cosmos DB como um arquivo de chave-valor
description: Saiba mais sobre os custos da unidade de pedido do Azure Cosmos DB para escrita simple e operações de leitura, quando é utilizado como um arquivo de chave/valor.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/23/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 3a79db11ff05bcc9d18619c7f508a9864c17c3b8
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/23/2019
ms.locfileid: "70012803"
---
# <a name="azure-cosmos-db-as-a-key-value-store--cost-overview"></a>Azure Cosmos DB como um repositório de valor de chave – visão geral do custo

O Azure Cosmos DB é um serviço de base de dados com múltiplos modelos distribuída globalmente, para criar aplicativos altamente disponíveis, em grande escala facilmente. Por padrão, Azure Cosmos DB indexa de forma automática e eficiente todos os dados ingeridos. Isso permite consultas [SQL](how-to-sql-query.md) (e [JavaScript](stored-procedures-triggers-udfs.md)) rápidas e consistentes nos dados. 

Este artigo descreve o custo do Azure Cosmos DB para escrita simple e operações de leitura quando é utilizado como um arquivo de chave/valor. As operações de gravação incluem inserções, substituições, exclusões e upserts de itens de dados. Além de garantir um SLA de disponibilidade de 99,999% para todas as contas de várias regiões, Azure Cosmos DB oferece garantia de < latência de 10 ms para leituras e para as gravações (indexadas), no 99 º percentil. 

## <a name="why-we-use-request-units-rus"></a>Por que podemos usar unidades de pedido (RUs)

Azure Cosmos DB desempenho é baseado na quantidade de produtividade provisionada expressa em [unidades de solicitação](request-units.md) (ru/s). O provisionamento é de uma granularidade de segundo e é comprado em RU/s ([não deve ser confundido com a cobrança por hora](https://azure.microsoft.com/pricing/details/cosmos-db/)). RUs deve ser considerado uma abstração lógica (uma moeda) que simplifica o provisionamento da taxa de transferência necessária para o aplicativo. Os usuários não precisam considerar a diferenciação da taxa de transferência de leitura e gravação. O modelo de moeda único de RUs cria eficiências para partilhar a capacidade aprovisionada entre leituras e gravações. Esse modelo de capacidade provisionado permite que o serviço forneça uma **taxa de transferência previsível e consistente, baixa latência garantida e alta disponibilidade**. Finalmente, embora o modelo de RU seja usado para representar a taxa de transferência, cada RU provisionado também tem uma quantidade definida de recursos (por exemplo, memória, núcleos/CPU e IOPS).

Como um sistema de banco de dados distribuído globalmente, Cosmos DB é o único serviço do Azure que fornece SLAs abrangentes que abrangem latência, taxa de transferência, consistência e alta disponibilidade. A taxa de transferência que você provisiona é aplicada a cada uma das regiões associadas à sua conta do cosmos. Para leituras, o Cosmos DB oferece várias bem definidas [níveis de consistência](consistency-levels.md) para sua escolha. 

A tabela a seguir mostra o número de RUs necessário para executar operações de leitura e gravação com base em um item de dados de tamanho de 1 KB e 100 KBs.

|Tamanho do item|1 Read|1 write|
|-------------|------|-------|
|1 KB|1 RU|5 RUs|
|100 KB|10 RUs|50 RUs|

## <a name="cost-of-reads-and-writes"></a>Custo de leituras e gravações

Se você provisionar 1.000 RU/s, esse valor será de 3,6 milhões RU/hora e custará $0.08 pela hora (nos EUA e na Europa). Para um item de dados de tamanho de 1 KB, isso significa que você pode consumir 3,6 milhões leituras ou 720.000 gravações (3,6 milhões RU/5) usando sua taxa de transferência provisionada. Normalizado para milhões de leituras e gravações, o custo seria de $0.22/milhão de leituras ($0.08/3,6) e $0.111/milhão de gravações ($0.08/0,72). O custo por milhões se torna um mínimo de conforme mostrado na tabela abaixo.

|Tamanho do item|Custo de 1 milhão leituras|Custo de gravações de 1 milhão|
|-------------|-------|--------|
|1 KB|$0.022|$0.111|
|100 KB|$0.222|$1.111|


Maioria dos BLOBs básico ou objeto arquivos de serviços encargos 0,40 us $ por milhão de transações de leitura e US $5 por transações de escrita de milhões. Se usado de forma ideal, Cosmos DB pode ser de até 98% mais barato do que essas outras soluções (para transações de 1 KB).

## <a name="next-steps"></a>Passos Seguintes

* Use a [calculadora de ru](https://cosmos.azure.com/capacitycalculator/) para estimar a taxa de transferência para suas cargas de trabalho.

