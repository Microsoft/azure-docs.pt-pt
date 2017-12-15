---
title: "BD do Azure do Cosmos como um arquivo de valor de chave – descrição geral do custo | Microsoft Docs"
description: "Saiba mais sobre a baixo custo de utilização da base de dados do Azure Cosmos como um arquivo de valor de chave."
keywords: arquivo de valor de chave
services: cosmos-db
author: mimig1
manager: jhubbard
editor: 
tags: 
documentationcenter: 
ms.assetid: 7f765c17-8549-4509-9475-46394fc3a218
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: mimig
ms.openlocfilehash: e6f87cd82ebe31965fcaac1f66d2df03fd58294f
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/14/2017
---
# <a name="azure-cosmos-db-as-a-key-value-store--cost-overview"></a>BD do Azure do Cosmos como um arquivo de valor de chave – descrição geral do custo

BD do Cosmos do Azure é um serviço de base de dados globalmente distribuída e múltiplos modelo para criar facilmente a aplicações em grande escala elevadas. Por predefinição, o Azure Cosmos DB indexa automaticamente todos os dados ingere forma eficiente. Isto permite a rápida e consistente [SQL](sql-api-sql-query.md) (e [JavaScript](programming.md)) consultas em qualquer tipo de dados. 

Este artigo descreve o custo de base de dados do Azure Cosmos para escrita simple e operações de leitura quando é utilizado como um arquivo de chave/valor. Escreva operações incluem inserções, substitui, eliminações e upserts de documentos. Para além de guaranteeing um 99,99% SLA de disponibilidade para todas as contas de única região e todas as contas de multirregião com consistência simples e 99.999% ler disponibilidade em todas as contas de base de dados de multirregião, ofertas de base de dados do Azure Cosmos garantida < latência de 10 ms para lê e < 15 ms latência para (indexada), respetivamente, escreve no percentil 99th. 

## <a name="why-we-use-request-units-rus"></a>Por que motivo podemos utilizar unidades de pedido (RUs)

Desempenho de base de dados do Cosmos Azure baseia-se na quantidade de aprovisionamento [unidades de pedido](request-units.md) (RU) para a partição. O aprovisionamento é granularidade segundo e é adquirido no RUs/seg ([não deve ser confundido com a hora a hora de faturação](https://azure.microsoft.com/pricing/details/cosmos-db/)). RUs devem ser consideradas como uma moeda que simplifica o aprovisionamento de débito necessário para a aplicação. Os nossos clientes não é necessário considerar differentiating entre leitura e escrita unidades de capacidade. O modelo de moeda única de RUs cria resulta numa eficiência para partilhar a capacidade aprovisionada entre leituras e escritas. Este modelo de capacidade aprovisionada permite que o serviço de fornecer um débito consistente e previsível, garantido baixa latência e elevada disponibilidade. Por fim, utilizamos RU para débito de modelo, mas cada RU aprovisionado tem também uma definidos quantidade de recursos (memória, Core). RU/seg não é apenas de IOPS.

Como um sistema de base de dados globalmente distribuída, a BD do Cosmos é o serviço apenas do Azure que fornece um SLA de consistência para além de elevada disponibilidade, o débito e latência. O débito que Aprovisiona é aplicado a cada uma das regiões associadas com a sua conta de base de dados de base de dados do Cosmos. Para leituras, base de dados do Cosmos oferece várias bem definidas [níveis de consistência](consistency-levels.md) para escolher. 

A tabela seguinte mostra que o número de RUs necessários para efetuar a leitura e escrita com base no tamanho do documento de 1 KB e 100 KBs de transações.

|Tamanho do item|1 leitura|1 escrita|
|-------------|------|-------|
|1 KB|1 RU|5 RUs|
|100 KB|10 RUs|50 RUs|

## <a name="cost-of-reads-and-writes"></a>Custo de leituras e escritas

Se aprovisionar 1.000 RU/seg, este quantidades m 3,6 RU/hora e irá custos $0.08 para a hora (nos E.U.A. e Europa). Para um documento de 1 KB de tamanho, isto significa que pode consumir 3.6-m leituras ou escritas 0.72-m (m 3,6 RU / 5) utilizando o débito aprovisionado. Normalizados para milhões de leituras e escritas, o custo seria $0.022 /m leituras ($0.08 / 3,6) e escreve $0.111/ m ($0.08 / 0,72). O custo por million fica mínima conforme mostrado na tabela abaixo.

|Tamanho do item|Leitura de 1-m|Escrita de 1-m|
|-------------|-------|--------|
|1 KB|$0.022|$0.111|
|100 KB|$0.222|$1.111|


Maioria dos BLOBs básico ou objeto arquivos de encargos de serviços $0.40 por milhões de transações de leitura e $5 por transação milhões de escrita. Se utilizados de forma ideal, base de dados do Cosmos podem ser até 98% mais barata que estas outras soluções (para transações de 1 KB).

## <a name="next-steps"></a>Passos seguintes

Esteja atento e novos artigos sobre a otimização de aprovisionamento de recursos do Azure Cosmos DB. Entretanto, não hesite em utilizar o nosso [Calculadora RU](https://www.documentdb.com/capacityplanner).

