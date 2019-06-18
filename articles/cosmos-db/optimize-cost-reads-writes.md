---
title: Otimizar o custo de leituras e escritas no Azure Cosmos DB
description: Este artigo explica explica como reduzir os custos do Azure Cosmos DB ao efetuar a leitura e operações nos dados de escrita.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: rimman
ms.openlocfilehash: 13ce5ee8b0e2a5d9cc84ea1a408ebba152b46050
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65967399"
---
# <a name="optimize-reads-and-writes-cost-in-azure-cosmos-db"></a>Otimizar as leituras e gravações de custos no Azure Cosmos DB

Este artigo descreve como é calculado o custo necessário para ler e escrever dados do Azure Cosmos DB. Operações de leitura incluem operações de get nos itens e as operações de escrita incluem Inserir, substituir, eliminar e upsert de itens.  

## <a name="cost-of-reads-and-writes"></a>Custo de leituras e gravações

O Azure Cosmos DB garante um desempenho previsível em termos de débito e latência, utilizando um modelo de débito aprovisionado. O débito aprovisionado é representado em termos de [unidades de pedido](request-units.md) por segundo ou RU/s. Uma unidade de pedido (RU) é uma abstração de lógica sobre os recursos de computação, tais como CPU, memória, e/s, etc., que são necessários para efetuar um pedido. O débito aprovisionado (RUs) é reservado e dedicado para o seu contentor ou a base de dados para fornecer o débito previsível e a latência. Débito aprovisionado permite que o Azure Cosmos DB fornecer um desempenho previsível e consistente, a garantia de baixa latência e elevada disponibilidade em qualquer escala. Unidades de pedido representam a moeda normalizada que simplifica o raciocínio sobre quantos recursos que precisa de um aplicativo. 

Não precisa se preocupar sobre unidades de pedido entre leituras e gravações de diferenciar. O modelo unificado de moeda de unidades de pedido cria eficiências de forma intercambiável utilizar a mesma capacidade de débito para leituras e gravações. A tabela seguinte mostra o custo de leituras e escritas em termos de RU/s para itens que são 1 KB e 100 KB de tamanho.

|**Tamanho do item**  |**Custo de uma leitura** |**Custo de uma gravação**|
|---------|---------|---------|
|1 KB |1 RU |5 RUs |
|100 KB |10 RUs |50 RUs |

A ler um item que é de 1 KB em tamanho custos uma UR. Escrever um item que é de 1 KB de custos de cinco RUs. Os custos de leitura e escrita são aplicáveis quando utiliza a sessão de predefinição [nível de consistência](consistency-levels.md).  As considerações em torno de RUs incluem: item tamanho, a contagem de propriedade, a consistência dos dados, propriedades indexadas, indexação e padrões de consulta.

## <a name="normalized-cost-for-1-million-reads-and-writes"></a>Leituras e escritas de custo normalizado para 1 milhão

Aprovisionamento 1.000 RU/s se traduz em 3.6 milhões de RU/hora e custará US $0,08 durante a hora (nos EUA e Europa). Para um item de 1 KB, pode realizar 3.6 milhões de leituras ou escritas de milhões de 0,72, (este valor é calculado como: `3.6 million RU / 5`) por hora, sendo este débito aprovisionado. Normalizados para um milhão de leituras e gravações, o custo seria US $0.022 para leituras de 1 milhão (este valor é calculado como: $0.08/3.6 milhões) e US $0.111 para gravações de 1 milhão (este valor é calculado como: $0.08/0.72 milhões).

## <a name="number-of-regions-and-the-request-units-cost"></a>Número de regiões e as unidades de pedido de custos

O custo das escritas é constante, independentemente do número de regiões associadas à conta do Azure Cosmos. Em outras palavras, uma gravação de 1 KB custará cinco RUs independentes do número de regiões que estão associados à conta. Há uma quantidade de recursos despendidos em replicar, aceitar e processar o tráfego de replicação em cada região. Para obter detalhes sobre a otimização de custos de várias regiões, consulte [otimizar o custo de contas do Cosmos de várias regiões](optimize-cost-regions.md) artigo.

## <a name="optimize-the-cost-of-writes-and-reads"></a>Otimizar o custo de gravações e leituras

Quando efetua operações de escrita, deve aprovisionar capacidade suficiente para suportar o número de gravações necessário por segundo. Pode aumentar o débito aprovisionado com o SDK, portal, da CLI antes de efetuar as operações de escrita e, em seguida, reduzir o débito depois das escritas são concluídas. O débito para o período de gravação é o débito mínimo necessários para os dados especificados, além do débito necessário para a carga de trabalho de inserção supondo que não existem outras cargas de trabalho em execução. 

Se estiver a executar em simultâneo outras cargas de trabalho, por exemplo, consulta/ler/atualizar/eliminar, deve adicionar as unidades de pedido adicionais necessárias para essas operações demasiado. Se as operações de escrita são taxa limitado, pode personalizar a política de repetição/término através de SDKs do Azure Cosmos DB. Por exemplo, pode aumentar a carga de até que uma pequena taxa de pedidos de obtém taxa limitado. Se ocorrer o limite de taxa, o aplicativo cliente deve voltar e na limitação de taxa de pedidos durante o intervalo de repetições especificada. Antes de repetir escritas, deve ter uma quantidade mínima de intervalo de tempo entre as repetições. Suporte de política de repetição está incluído no SQL .NET, Java, node. js e SDKs de Python e todas as versões suportadas dos SDKs do .NET Core. 

Também pode inserir dados em massa para o Azure Cosmos DB ou copiar os dados de qualquer arquivo de dados de origem suportada para o Azure Cosmos DB utilizando [do Azure Data Factory](../data-factory/connector-azure-cosmos-db.md). O Azure Data Factory integra-se nativamente com a API do Azure Cosmos DB em massa para fornecer o melhor desempenho, ao escrever dados.

## <a name="next-steps"></a>Passos Seguintes

Em seguida, pode avançar para obter mais informações sobre a otimização de custos no Azure Cosmos DB com os seguintes artigos:

* Saiba mais sobre [otimizar para desenvolvimento e teste](optimize-dev-test.md)
* Saiba mais sobre [entender a sua fatura do Azure Cosmos DB](understand-your-bill.md)
* Saiba mais sobre [otimizar o custo de débito](optimize-cost-throughput.md)
* Saiba mais sobre [otimizar o custo de armazenamento](optimize-cost-storage.md)
* Saiba mais sobre [otimizar o custo de consultas](optimize-cost-queries.md)
* Saiba mais sobre [otimizar o custo de contas do Cosmos do Azure de várias regiões](optimize-cost-regions.md)
