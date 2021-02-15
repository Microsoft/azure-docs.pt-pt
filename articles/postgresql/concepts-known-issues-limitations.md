---
title: Problemas e limitações conhecidos - Azure Database for PostgreSQL - Single Server and Flexible Server (Preview)
description: Lista os problemas conhecidos que os clientes devem estar atentos.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/05/2020
ms.openlocfilehash: 3c26368e6281ad74d617891ba15c980117b25a6e
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/10/2021
ms.locfileid: "100106428"
---
# <a name="azure-database-for-postgresql---known-issues-and-limitations"></a>Base de Dados Azure para PostgreSQL - Questões e limitações conhecidas

Esta página fornece uma lista de problemas conhecidos na Base de Dados Azure para PostgreSQL que podem afetar a sua aplicação. Ele também enumera qualquer mitigação e recomendações para resolver a questão.

## <a name="intelligent-performance---query-store"></a>Desempenho Inteligente - Loja de Consultas

Aplicável à Base de Dados Azure para PostgreSQL - Servidor Único.

| Aplicável | Causa | Remediação|
| ----- | ------ | ---- | 
| PostgresQL 9.6, 10, 11 | Ligar o parâmetro do servidor `pg_qs.replace_parameter_placeholders` pode levar a uma paragem do servidor em alguns cenários raros. | Através do Portal Azure, secção de Parâmetros do Servidor, rode o valor do parâmetro `pg_qs.replace_parameter_placeholders` `OFF` e guarde.   | 

## <a name="server-parameters"></a>Parâmetros do Servidor

Aplicável à Base de Dados de Azure para PostgreSQL - Servidor Único e Servidor Flexível

| Aplicável | Causa | Remediação| 
| ----- | ------ | ---- | 
| PostgresQL 9.6, 10, 11 | Alterar o parâmetro do servidor `max_locks_per_transaction` para um valor superior ao [recomendado](https://www.postgresql.org/docs/11/kernel-resources.html) pode levar a que o servidor não possa surgir após um reinício. | Deixe-o para o valor predefinido (32 ou 64) ou altere para um valor razoável por [documentação](https://www.postgresql.org/docs/11/kernel-resources.html)PostgreSQL . <br> <br> Do lado de serviço, isto está a ser trabalhado para limitar o valor elevado baseado no SKU.  | 

## <a name="next-steps"></a>Passos seguintes
- Ver As [melhores práticas](./concepts-query-store-best-practices.md) da Loja de Consultas
