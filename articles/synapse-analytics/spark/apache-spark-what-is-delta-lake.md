---
title: O que é Delta Lake
description: Visão geral do Lago Delta e como funciona como parte da Azure Synapse Analytics
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: cd582c1175d6bff0aa18ee77dde8b7734b6acf25
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98121027"
---
# <a name="what-is-delta-lake"></a>O que é Delta Lake

A Azure Synapse Analytics é compatível com o Lago Delta da Fundação Linux. Delta Lake é uma camada de armazenamento de código aberto que traz transações acid (atomicidade, consistência, isolamento e durabilidade) para Apache Spark e grandes cargas de trabalho de dados.

A versão atual do Delta Lake incluída com a Azure Synapse tem suporte linguístico para Scala, PySpark e .NET. Existem links na parte inferior da página para exemplos e documentação mais detalhados.

## <a name="key-features"></a>Principais funcionalidades

| Funcionalidade | Descrição |
| --- | --- |
| **Transações ACID** | Os lagos de dados são tipicamente povoados através de múltiplos processos e oleodutos, alguns dos quais estão escrevendo dados em simultâneo com leituras. Antes da Delta Lake e da adição de transações, os engenheiros de dados tiveram de passar por um processo de erro manual propenso a garantir a integridade dos dados. Delta Lake traz transações acid familiares para lagos de dados. Proporciona serializabilidade, o nível de isolamento mais forte. Saiba mais no [Diving into Delta Lake: Desembalar o Registo de Transações.](https://databricks.com/blog/2019/08/21/diving-into-delta-lake-unpacking-the-transaction-log.html)|
| **Tratamento de metadados escaláveis** | No big data, até os metadados em si podem ser "big data". A Delta Lake trata os metadados tal como os dados, aproveitando o poder de processamento distribuído da Spark para lidar com todos os seus metadados. Como resultado, o Delta Lake pode lidar com mesas à escala de petabyte com biliões de divisórias e ficheiros à vontade. |
| **Viagem no tempo (versão de dados)** | A capacidade de "desfazer" uma alteração ou voltar a uma versão anterior é uma das principais características das transações. A Delta Lake fornece instantâneos de dados que lhe permitem reverter para versões anteriores de dados para auditorias, retrocessos ou para reproduzir experiências. Saiba mais na [introdução da Viagem no Tempo do Delta Lake para lagos de dados em grande escala.](https://databricks.com/blog/2019/02/04/introducing-delta-time-travel-for-large-scale-data-lakes.html) |
| **Formato aberto** | Apache Parquet é o formato de base para o Delta Lake, permitindo-lhe alavancar os esquemas eficientes de compressão e codificação que são nativos do formato. |
| **Lote unificado e fonte de streaming e pia** | Uma mesa em Delta Lake é simultaneamente uma mesa de lote, bem como uma fonte de streaming e pia. Streaming de dados ingeridos, lote histórico de backfill, e consultas interativas tudo apenas funciona fora da caixa. |
| **Aplicação de Schema** | A aplicação do Schema ajuda a garantir que os tipos de dados estão corretos e as colunas necessárias estão presentes, evitando que os dados maus causem inconsistências de dados. Para mais informações, consulte [Mergulhar no Lago Delta: Schema Enforcement & Evolution](https://databricks.com/blog/2019/09/24/diving-into-delta-lake-schema-enforcement-evolution.html) |
| **Evolução do Schema** | O Delta Lake permite-lhe fazer alterações num esquema de mesa que pode ser aplicado automaticamente, sem ter de escrever DDL de migração. Para mais informações, consulte [Mergulhar no Lago Delta: Schema Enforcement & Evolution](https://databricks.com/blog/2019/09/24/diving-into-delta-lake-schema-enforcement-evolution.html) |
| **Histórico de Auditoria** | Registos de registos de transações da Delta Lake registam detalhes sobre todas as alterações feitas aos dados que fornecem um rasto completo de auditoria das alterações. |
| **Atualizações e Eliminações** | Delta Lake suporta ApIs Scala / Java / Python e SQL para uma variedade de funcionalidades. O suporte para a fusão, atualização e eliminação de operações ajuda-o a cumprir os requisitos de conformidade. Para mais informações, consulte [anunciando o Lançamento delta Lake 0.6.1](https://delta.io/news/delta-lake-0-6-1-released/),  [anunciando o Lançamento delta Lake 0.7](https://delta.io/news/delta-lake-0-7-0-released/) e [Simples, Upserts e Deletes em Delta Lake Tables usando APIs Python,](https://databricks.com/blog/2019/10/03/simple-reliable-upserts-and-deletes-on-delta-lake-tables-using-python-apis.html)que inclui snippets de código para fusão, atualização e eliminação de comandos DML. |
| **100% Compatível com Apache Spark API** | Os desenvolvedores podem usar o Delta Lake com os seus oleodutos de dados existentes com alterações mínimas, uma vez que é totalmente compatível com as implementações de Spark existentes. |

Para documentação completa, consulte a Página de [Documentação do Lago Delta](https://docs.delta.io/latest/delta-intro.html)

Para mais informações, consulte [o Projeto Delta Lake.](https://github.com/delta-io/delta)

## <a name="next-steps"></a>Passos seguintes

- [.NET para documentação Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](../index.yml)