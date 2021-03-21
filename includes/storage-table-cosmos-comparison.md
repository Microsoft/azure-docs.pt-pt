---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: include
ms.date: 01/08/2021
ms.author: mimig
ms.custom: include file
ms.openlocfilehash: a7e34f077ce1b2541168df40f2806fdb24a63a79
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98050764"
---
Se utilizar atualmente o armazenamento de Tabelas do Azure, beneficia das vantagens seguintes se mudar para a API de Tabelas do Azure Cosmos DB:

|Funcionalidade | Armazenamento de Tabelas do Azure | API de Tabelas do Azure Cosmos DB |
| --- | --- | --- |
| Latência | Rápida, mas sem limites superiores. | Latência de milissegundos de um só dígito para leituras e escritas, suportada por leituras de latência inferiores a 10 ms e a escritas de latência inferiores a 15 ms no percentil 99, em qualquer escala e em qualquer parte do mundo. |
| Débito | Modelo de débito variável. As tabelas têm um limite de escalabilidade de 20 000 operações/s. | Altamente dimensionável, com [débito reservado dedicado por tabela](../articles/cosmos-db/request-units.md), com suporte dos SLAs. As contas não têm limite máximo de produção e apoio >10 milhões de operações/s por tabela (no modo de produção previsto). |
| Distribuição global | Região única com uma região de leitura secundária opcional para elevada disponibilidade. Não pode iniciar ativações pós-falha. | [Distribuição global chave na mão](../articles/cosmos-db/distribute-data-globally.md) de uma região para mais de 30. Suporte para [ativações pós-falha automáticas e manuais](../articles/cosmos-db/high-availability.md) em qualquer altura e em qualquer parte do mundo. |
| Indexação | Apenas índice primário em PartitionKey e RowKey. Sem índices secundários. | Indexação automática e completa em todas as propriedades, sem gestão de índices. |
| Consulta | A execução de consultas utiliza o índice para a chave primária e analisa, se for caso disso. | As consultas podem tirar partido da indexação automática nas propriedades para tempos de consulta rápidos. |
| Consistência | Forte na região primária. Eventual na região secundária. | [Cinco níveis de consistência bem definidos](../articles/cosmos-db/consistency-levels.md) para trocar disponibilidade, latência, produção e consistência com base nas necessidades da sua aplicação. |
| Preços | Baseado em consumo. | Disponível tanto nos modos de capacidade [baseados no consumo](../articles/cosmos-db/serverless.md) como nos modos [de capacidade avisionado.](../articles/cosmos-db/set-throughput.md) |
| SLAs | 99,99% de disponibilidade. | SLA de 99,99% disponibilidade para todas as contas de região única e para todas as contas de várias regiões com consistência flexível e 99,999% de disponibilidade de leitura em todas as contas de bases de dados de várias regiões [SLAs abrangentes e líderes da indústria](https://azure.microsoft.com/support/legal/sla/cosmos-db/) em disponibilidade geral. |
