---
title: Boas práticas para SQL on-demand (pré-visualização) em Azure Synapse Analytics
description: Recomendações e boas práticas que deve conhecer enquanto trabalha com a SQL on-demand (pré-visualização).
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 1d4203141973c10fe7673f6ab9dedbc3bfdc8999
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429073"
---
# <a name="best-practices-for-sql-on-demand-preview-in-azure-synapse-analytics"></a>Boas práticas para SQL on-demand (pré-visualização) em Azure Synapse Analytics

Neste artigo, encontrará uma coleção de boas práticas para utilizar a SQL a pedido (pré-visualização). SQL on-demand é um recurso adicional dentro da Azure Synapse Analytics.

## <a name="general-considerations"></a>Considerações gerais

A SQL on-demand permite-lhe consultar ficheiros nas suas contas de armazenamento Azure. Não tem capacidades locais de armazenamento ou ingestão. Como tal, todos os ficheiros que os alvos de consulta são externos ao SQL a pedido. Tudo relacionado com a leitura de ficheiros do armazenamento pode ter impacto no desempenho da consulta.

## <a name="colocate-azure-storage-account-and-sql-on-demand"></a>Conta de Armazenamento Colocate Azure e SQL a pedido

Para minimizar a latência, coloque a sua conta de armazenamento Azure e o seu ponto final a pedido da SQL. As contas de armazenamento e os pontos finais aprovisionados durante a criação do espaço de trabalho situam-se na mesma região.

Para um desempenho ótimo, se aceder a outras contas de armazenamento com sql on-demand, certifique-se de que estão na mesma região. Se não estiverem na mesma região, haverá um aumento da latência para a transferência de rede dos dados entre as regiões remotas e finais.

## <a name="azure-storage-throttling"></a>Estrangulamento de armazenamento azure

Várias aplicações e serviços podem aceder à sua conta de armazenamento. O estrangulamento do armazenamento ocorre quando o IOPS combinado ou a entrada gerada por aplicações, serviços e carga de trabalho a pedido da SQL excedem os limites da conta de armazenamento. Como resultado, você vai experimentar um efeito negativo significativo no desempenho da consulta.

Uma vez detetada a aceleração, a SQL on-demand tem um manuseamento incorporado deste cenário. A SQL a pedido fará pedidos de armazenamento a um ritmo mais lento até que o estrangulamento seja resolvido.

> [!TIP]
> Para uma execução de consulta ideal, não deve sublinhar a conta de armazenamento com outras cargas de trabalho durante a execução da consulta.

## <a name="prepare-files-for-querying"></a>Prepare ficheiros para consulta

Se possível, pode preparar ficheiros para um melhor desempenho:

- Converter CSV para Parquet - Parquet é formato colunar. Uma vez que é comprimido, os seus tamanhos de ficheiros são menores do que os ficheiros CSV com os mesmos dados. A SQL a pedido necessitará de menos tempo e pedidos de armazenamento para lê-lo.
- Se uma consulta tiver como alvo um único ficheiro grande, beneficiar-se-á de dividi-lo em vários ficheiros menores.
- Tente manter o tamanho do ficheiro CSV abaixo de 10 GB.
- É melhor ter ficheiros de tamanho igual para um único caminho OPENROWSET ou uma localização de mesa externa.
- Partifique os seus dados armazenando divisórias em diferentes pastas ou nomes de ficheiros - verifique funções de nome de [ficheiro e de ficheiros para direcionar divisórias específicas](#use-fileinfo-and-filepath-functions-to-target-specific-partitions).

## <a name="use-fileinfo-and-filepath-functions-to-target-specific-partitions"></a>Utilize funções de fileinfo e filepath para direcionar divisórias específicas

Os dados são frequentemente organizados em divisórias. Pode instruir a SQL a pedido para consultar determinadas pastas e ficheiros. Esta função reduzirá o número de ficheiros e a quantidade de dados que a consulta precisa de ler e processar. Um bónus adicional é que conseguirá um melhor desempenho.

Para mais informações, verifique o nome de [ficheiros](develop-storage-files-overview.md#filename-function) e as funções [de path path](develop-storage-files-overview.md#filepath-function) e exemplos sobre como consultar [ficheiros específicos](query-specific-files.md).

Se os seus dados armazenados não forem divididos, considere dividi-los para que possa usar estas funções para otimizar consultas direcionadas a esses ficheiros. Ao [consultar tabelas de Faíscas divididas](develop-storage-files-spark-tables.md) a pedido da SQL, a consulta irá automaticamente direcionar apenas os ficheiros necessários.

## <a name="use-cetas-to-enhance-query-performance-and-joins"></a>Use o CETAS para melhorar o desempenho da consulta e junta-se

O [CETAS](develop-tables-cetas.md) é uma das funcionalidades mais importantes disponíveis na SQL a pedido. O CETAS é uma operação paralela que cria metadados de mesa externos e exporta os resultados da consulta SELECT para um conjunto de ficheiros na sua conta de armazenamento.

Pode utilizar o CETAS para armazenar partes frequentemente utilizadas de consultas, como tabelas de referência unidas, para um novo conjunto de ficheiros. Em seguida, você pode se juntar a esta única tabela externa em vez de repetir juntas comuns em múltiplas consultas.

À medida que o CETAS gera ficheiros Parquet, as estatísticas serão automaticamente criadas quando a primeira consulta visa esta tabela externa, resultando num melhor desempenho.

## <a name="next-steps"></a>Passos seguintes

Reveja o artigo de resolução de [problemas](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) para questões e soluções comuns. Se você está trabalhando com piscina SQL em vez de SQL on-demand, por favor veja as [melhores práticas para](best-practices-sql-pool.md) o artigo de piscina SQL para orientação específica.
