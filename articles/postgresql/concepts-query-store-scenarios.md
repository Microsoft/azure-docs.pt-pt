---
title: Cenários da Consulta Store - Base de Dados Azure para PostgreSQL - Servidor Único
description: Este artigo descreve alguns cenários para a Loja de Consultas em Base de Dados Azure para PostgreSQL - Servidor Único.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 31e3f82b6ea1b1fc15c0832dc03edce2a59f1e1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74768355"
---
# <a name="usage-scenarios-for-query-store"></a>Cenários de utilização para A Loja de Consulta

**Aplica-se a:** Base de Dados Azure para PostgreSQL - Versões Single Server 9.6, 10, 11

Você pode usar a Consulta Store em uma grande variedade de cenários em que rastrear e manter o desempenho previsível da carga de trabalho é crítico. Considere os seguintes exemplos: 
- Identificar e afinar consultas caras 
- Ensaio a/B 
- Manter o desempenho estável durante as atualizações 
- Identificar e melhorar as cargas de trabalho ad hoc 

## <a name="identify-and-tune-expensive-queries"></a>Identificar e afinar consultas caras 

### <a name="identify-longest-running-queries"></a>Identifique as consultas de execução mais longas 
Utilize a vista [Query Performance Insight](concepts-query-performance-insight.md) no portal Azure para identificar rapidamente as consultas de execução mais longas. Estas consultas tendem normalmente a consumir uma quantidade significativa de recursos. Otimizar as suas questões de execução mais longas pode melhorar o desempenho libertando recursos para uso por outras consultas em execução no seu sistema. 

### <a name="target-queries-with-performance-deltas"></a>Consultas de destino com deltas de desempenho 
A Consulta Store corta os dados de desempenho em janelas de tempo, para que possa acompanhar o desempenho de uma consulta ao longo do tempo. Isto ajuda-o a identificar exatamente quais as consultas que estão a contribuir para um aumento do tempo total gasto. Como resultado, pode fazer uma resolução de problemas direcionada para a sua carga de trabalho.

### <a name="tuning-expensive-queries"></a>Afinação de consultas caras 
Quando se identifica uma consulta com desempenho sub-ideal, a ação que se toma depende da natureza do problema: 
- Utilize [recomendações](concepts-performance-recommendations.md) de desempenho para determinar se existem índices sugeridos. Se sim, crie o índice e, em seguida, use a Consulta Store para avaliar o desempenho da consulta após a criação do índice. 
- Certifique-se de que as estatísticas estão atualizadas para as tabelas subjacentes utilizadas pela consulta.
- Considere reescrever perguntas caras. Por exemplo, aproveite a parametrização da consulta e reduza o uso de SQL dinâmico. Implementar a lógica ideal ao ler dados como a aplicação de filtragem de dados no lado da base de dados, e não no lado da aplicação. 


## <a name="ab-testing"></a>Ensaio a/B 
Utilize a Consulta Store para comparar o desempenho da carga de trabalho antes e depois de uma alteração de aplicação que planeia introduzir. Exemplos de cenários para a utilização da Consulta Store para avaliar o impacto do ambiente ou alteração da aplicação ao desempenho da carga de trabalho: 
- Lançando uma nova versão de uma aplicação. 
- Adicionando recursos adicionais ao servidor. 
- Criação de índices em falta em tabelas referenciadas por consultas caras. 
 
Em qualquer um destes cenários, aplique o seguinte fluxo de trabalho: 
1. Execute a sua carga de trabalho com a Consulta Store antes da alteração planeada para gerar uma linha de base de desempenho. 
2. Aplicar a alteração da aplicação no momento controlado no tempo. 
3. Continue a executar a carga de trabalho tempo suficiente para gerar a imagem de desempenho do sistema após a mudança. 
4. Compare os resultados de antes e depois da mudança. 
5. Decida se mantém a mudança ou a reversão. 


## <a name="identify-and-improve-ad-hoc-workloads"></a>Identificar e melhorar as cargas de trabalho ad hoc 
Algumas cargas de trabalho não têm consultas dominantes que você pode sintonizar para melhorar o desempenho geral da aplicação. Essas cargas de trabalho são tipicamente caracterizadas com um número relativamente grande de consultas únicas, cada uma delas consumindo uma porção de recursos do sistema. Cada consulta única é executada com pouca frequência, pelo que individualmente o seu consumo de tempo de corrido não é crítico. Por outro lado, dado que a aplicação está sempre a gerar novas consultas, uma parte significativa dos recursos do sistema é gasta na compilação de consultas, o que não é o ideal. Normalmente, esta situação acontece se a sua aplicação gerar consultas (em vez de usar procedimentos armazenados ou consultas parametrizadas) ou se se basear em quadros de mapeamento relacional de objetos que geram consultas por padrão. 
 
Se estiver a controlar o código de aplicação, poderá considerar reescrever a camada de acesso a dados para utilizar procedimentos armazenados ou consultas parametrizadas. No entanto, esta situação também pode ser melhorada sem alterações de aplicação, forçando a parametrização de consultas para toda a base de dados (todas as consultas) ou para os modelos de consulta individual com o mesmo hash de consulta. 

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre as [melhores práticas para usar](concepts-query-store-best-practices.md) a Query Store