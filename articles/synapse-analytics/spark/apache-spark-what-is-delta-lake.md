---
title: O que é Delta Lake
description: Visão geral do Lago Delta e como funciona como parte da Azure Synapse Analytics
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 52758eab645fa0bb89cb499a5c617df62c21279e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429203"
---
# <a name="what-is-delta-lake"></a>O que é Delta Lake?

A Azure Synapse Analytics é compatível com o Lago Delta da Fundação Linux. Delta Lake é uma camada de armazenamento de código aberto que traz acid (atómico, consistência, isolamento e durabilidade) às transações de Apache Spark e grandes cargas de trabalho de dados.

## <a name="key-features"></a>Principais funcionalidades

| Funcionalidade | Descrição |
| --- | --- |
| **Transações ACID** | Os lagos de dados são tipicamente povoados através de múltiplos processos e oleodutos, alguns dos quais estão a escrever dados simultaneamente com leituras. Antes do Delta Lake e da adição de transações, os engenheiros de dados tiveram de passar por um processo de erro manual para garantir a integridade dos dados. Delta Lake traz transações familiares de ÁCIDO para os lagos de dados. Proporciona a serializabilidade, o nível mais forte de isolamento. Saiba mais em [Diving into Delta Lake: Desempacotar o Registo](https://databricks.com/blog/2019/08/21/diving-into-delta-lake-unpacking-the-transaction-log.html)de Transações .|
| **Manuseamento de metadados escaláveis** | Em big data, até os metadados em si podem ser "big data". Delta Lake trata metadados tal como dados, aproveitando o poder de processamento distribuído da Spark para lidar com todos os seus metadados. Como resultado, o Delta Lake pode lidar com mesas à escala de petabyte com milhares de milhões de divisórias e ficheiros à vontade. |
| **Viagem no Tempo (versão de dados)** | A capacidade de "desfazer" uma alteração ou voltar a uma versão anterior é uma das principais funcionalidades das transações. A Delta Lake fornece instantâneos de dados que permitem reverter para versões anteriores de dados para auditorias, reversão ou para reproduzir experiências. Saiba mais sobre [a introdução da Viagem no Tempo do Lago Delta para grandes lagos](https://databricks.com/blog/2019/02/04/introducing-delta-time-travel-for-large-scale-data-lakes.html)de dados. |
| **Formato aberto** | Apache Parquet é o formato de base para o Delta Lake, permitindo-lhe alavancar os eficientes esquemas de compressão e codificação que são nativos do formato. |
| **Lote unificado e fonte de streaming e pia** | Uma mesa no Lago Delta é simultaneamente uma mesa de lote, bem como uma fonte de streaming e pia. O streaming de dados ingerir, encher histórico de lote e consultas interativas funcionam apenas fora da caixa. |
| **Aplicação da Schema** | A aplicação da schema ajuda a garantir que os tipos de dados estão corretos e as colunas necessárias estão presentes, evitando que os dados maus causem inconsistência de dados. Para mais informações, consulte [Diving Into Delta Lake: Schema Enforcement & Evolution](https://databricks.com/blog/2019/09/24/diving-into-delta-lake-schema-enforcement-evolution.html) |
| **Evolução de Schema** | O Delta Lake permite-lhe fazer alterações num esquema de mesa que pode ser aplicado automaticamente, sem ter de escrever dDL migração. Para mais informações, consulte [Diving Into Delta Lake: Schema Enforcement & Evolution](https://databricks.com/blog/2019/09/24/diving-into-delta-lake-schema-enforcement-evolution.html) |
| **Histórico de Auditoria** | O registo de registos de transações da Delta Lake regista detalhes sobre cada alteração feita aos dados que fornecem um rasto de auditoria completo das alterações. |
| **Atualizações e Eliminações** | Delta Lake suporta Scala / Java / Python e APIs SQL para uma variedade de funcionalidades. O suporte para operações de fusão, atualização e eliminação ajuda-o a cumprir os requisitos de conformidade. Para mais informações, consulte [o Anúncio do Delta Lake 0.4.0 Lançamento](https://delta.io/news/delta-lake-0-4-0-released/) e [Upserts simples e fiáveis e elimina nas tabelas do Lago Delta utilizando APIs Python](https://databricks.com/blog/2019/10/03/simple-reliable-upserts-and-deletes-on-delta-lake-tables-using-python-apis.html), que inclui códigos para fusão, atualização e exclusão de comandos DML. |
| **100% compatível com API Apache Spark** | Os desenvolvedores podem usar o Delta Lake com os seus oleodutos de dados existentes com alterações mínimas, uma vez que é totalmente compatível com as implementações existentes da Spark. |

Para documentação completa, consulte a Página de [Documentação](https://docs.delta.io/latest/delta-intro.html) do Lago Delta

Para mais informações, consulte [o Projeto Delta Lake.](https://lfprojects.org)

## <a name="next-steps"></a>Passos seguintes

- [.NET para documentação Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
