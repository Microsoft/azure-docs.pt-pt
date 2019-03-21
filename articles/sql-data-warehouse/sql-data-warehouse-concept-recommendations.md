---
title: Recomendações - conceitos do SQL Data Warehouse | Documentos da Microsoft
description: Saiba mais sobre as recomendações de SQL Data Warehouse e como eles são gerados
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 11/05/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: b275f23209979e1a8068ecd99465f7b52392bc6c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58082913"
---
# <a name="sql-data-warehouse-recommendations"></a>Recomendações de armazém de dados SQL

Este artigo descreve as recomendações fornecidas ao SQL Data Warehouse através do Assistente do Azure.  

O SQL Data Warehouse fornece recomendações para garantir que o seu armazém de dados consistentemente está otimizado para desempenho. Recomendações de armazém de dados são totalmente integradas com [do Assistente do Azure](https://docs.microsoft.com/azure/advisor/advisor-performance-recommendations) para lhe fornecer melhores práticas diretamente dentro do [portal do Azure](https://aka.ms/Azureadvisor). O SQL Data Warehouse analisa o estado atual do seu armazém de dados, recolhe telemetria e recomendações de superfícies sobre a sua carga de trabalho ativada diariamente. Os cenários de recomendação do armazém de dados suportados estão descritos a seguir, juntamente com a aplicar as ações recomendadas.

Se tiver comentários sobre o Assistente de armazém de dados SQL ou se depare com problemas, contacte [ sqldwadvisor@service.microsoft.com ](mailto:sqldwadvisor@service.microsoft.com).   

Clique em [aqui](https://aka.ms/Azureadvisor) para verificar as suas recomendações hoje mesmo! Atualmente esta funcionalidade é aplicável a armazéns de dados de geração 2 apenas. 

## <a name="data-skew"></a>Distorção de dados

Distorção de dados pode provocar afunilamentos de movimento ou recurso de dados adicionais ao executar a sua carga de trabalho. A seguinte documentação descreve show para identificar a distorção de dados e impedir que ele aconteça, selecionando uma chave de distribuição ideal.

- [Identificar e remover inclinação](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice) 

## <a name="no-or-outdated-statistics"></a>Não ou desatualizados estatísticas

Com estatísticas inferior ao ideal pode afetar gravemente o desempenho das consultas que pode fazer com que o otimizador de consultas do SQL Data Warehouse gerar planos de consulta inferior ao ideal. A seguinte documentação descreve as práticas recomendadas sobre a criação e atualização de estatísticas de:

- [Criar e atualizar estatísticas de tabela](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics)

Para ver a lista de tabelas afetadas por estas recomendações, execute o seguinte [script T-SQL](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/samples/sqlops/MonitoringScripts/ImpactedTables). O assistente executa continuamente o mesmo script T-SQL para gerar estas recomendações.

## <a name="replicate-tables"></a>Replicar de tabelas

Para obter recomendações de tabela replicada, o assistente Deteta candidatos de tabela com base nas características físicas seguintes:

- Tamanho da tabela de replicados
- Número de colunas
- Tipo de distribuição da tabela
- Número de partições

Advisor continuamente tira partido da heurística com base na carga de trabalho, tais como a frequência de acesso de tabela, linhas devolvidas em média, e limiares em todos os dados do armazém de tamanho e a atividade para garantir que são geradas recomendações de alta qualidade. 

A seguir descreve a heurística com base na carga de trabalho, que pode encontrar no portal do Azure para cada recomendação da tabela replicada:

- Analisar média-a percentagem média de linhas que foram devolvidas da tabela para cada acesso de tabela nos últimos sete dias
- Leitura frequente, nenhuma atualização - indica que a tabela não foi atualizada nos últimos sete dias, ao mostrar a atividade de acesso
- Rácio de leitura/atualizar - a proporção de como freqüente que a tabela foi acedida em relação ao quando ele é atualizado nos últimos sete dias
- Atividade - mede a utilização com base na atividade de acesso. Isso se compara a atividade de acesso de tabela em relação a atividade de acesso de média de tabela entre o armazém de dados nos últimos sete dias. 

Atualmente Advisor mostrará apenas no máximo candidatos de quatro tabelas replicadas em simultâneo com índices columnstore em cluster, atribuir prioridades a atividade de mais alta.

> [!IMPORTANT]
> A recomendação de tabela replicada não é uma prova completa e não leva em operações de movimento de dados de conta. Estamos a tentar adicionar isso como uma heurística mas enquanto isso, deve sempre validar sua carga de trabalho depois de aplicar a recomendação. Entre em contato com sqldwadvisor@service.microsoft.com se detetar recomendações de tabela replicada que faz com que sua carga de trabalho para diminua. Para saber mais sobre as tabelas replicadas, visite o seguinte procedimento [documentação](https://docs.microsoft.com/azure/sql-data-warehouse/design-guidance-for-replicated-tables#what-is-a-replicated-table).
