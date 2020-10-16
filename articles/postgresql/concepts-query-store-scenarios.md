---
title: Cenários de Loja de Consultas - Base de Dados Azure para PostgreSQL - Servidor Único
description: Este artigo descreve alguns cenários para a Loja de Consultas na Base de Dados Azure para PostgreSQL - Servidor Único.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: ad646e39e9b6a87611dc2f40330a77fe70c92c27
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91710554"
---
# <a name="usage-scenarios-for-query-store"></a>Cenários de utilização para a Loja de Consultas

**Aplica-se a:** Azure Database para PostgreSQL - Versões single Server 9.6, 10, 11

Você pode usar a Loja de Consulta em uma grande variedade de cenários em que rastrear e manter o desempenho previsível da carga de trabalho é crítico. Considere os seguintes exemplos: 
- Identificar e afinar consultas caras de topo 
- Testes A/B 
- Manter o desempenho estável durante as atualizações 
- Identificar e melhorar as cargas de trabalho ad hoc 

## <a name="identify-and-tune-expensive-queries"></a>Identificar e afinar consultas caras 

### <a name="identify-longest-running-queries"></a>Identificar consultas de execução mais longas 
Utilize a vista ['Insight de Desempenho de Consulta'](concepts-query-performance-insight.md) no portal Azure para identificar rapidamente as consultas de execução mais longas. Estas consultas tendem tipicamente a consumir uma quantidade significativa de recursos. Otimizar as suas questões de execução mais longas pode melhorar o desempenho libertando recursos para uso por outras consultas em execução no seu sistema. 

### <a name="target-queries-with-performance-deltas"></a>Consultas de destino com deltas de desempenho 
A Consulta Store corta os dados de desempenho em janelas de tempo, para que possa acompanhar o desempenho de uma consulta ao longo do tempo. Isto ajuda-o a identificar exatamente quais as consultas que estão a contribuir para um aumento do tempo total gasto. Como resultado, pode fazer uma resolução de problemas direcionada da sua carga de trabalho.

### <a name="tuning-expensive-queries"></a>Consultas caras de afinação 
Quando identifica uma consulta com desempenho subóptima, a ação que toma depende da natureza do problema: 
- Utilize [recomendações de desempenho](concepts-performance-recommendations.md) para determinar se existem índices sugeridos. Se sim, crie o índice e, em seguida, use a Loja de Consultas para avaliar o desempenho da consulta após a criação do índice. 
- Certifique-se de que as estatísticas estão atualizadas para as tabelas subjacentes utilizadas pela consulta.
- Considere reescrever consultas caras. Por exemplo, aproveite a parâmetroização da consulta e reduza o uso de SQL dinâmico. Implemente a lógica ideal ao ler dados como aplicar a filtragem de dados no lado da base de dados, e não no lado da aplicação. 


## <a name="ab-testing"></a>Testes A/B 
Utilize a Loja de Consultas para comparar o desempenho da carga de trabalho antes e depois de uma alteração de aplicação que pretende introduzir. Exemplos de cenários para a utilização da Loja de Consultas para avaliar o impacto do ambiente ou alteração da aplicação no desempenho da carga de trabalho: 
- Lançando uma nova versão de uma aplicação. 
- Adicionando recursos adicionais ao servidor. 
- Criando índices em falta em tabelas referenciadas por consultas caras. 
 
Em qualquer um destes cenários, aplique o seguinte fluxo de trabalho: 
1. Execute a sua carga de trabalho com a Loja de Consultas antes da alteração planeada para gerar uma linha de base de desempenho. 
2. Aplicar alterações de aplicação no momento controlado no tempo. 
3. Continue a executar a carga de trabalho tempo suficiente para gerar imagem de desempenho do sistema após a alteração. 
4. Compare os resultados de antes e depois da mudança. 
5. Decida se mantém a mudança ou o revés. 


## <a name="identify-and-improve-ad-hoc-workloads"></a>Identificar e melhorar cargas de trabalho ad hoc 
Algumas cargas de trabalho não têm consultas dominantes que você pode sintonizar para melhorar o desempenho geral da aplicação. Essas cargas de trabalho são tipicamente caracterizadas por um número relativamente grande de consultas únicas, cada uma delas consumindo uma parte dos recursos do sistema. Cada consulta única é executada com pouca frequência, pelo que, individualmente, o seu consumo em tempo de execução não é crítico. Por outro lado, dado que a aplicação está sempre a gerar novas consultas, uma parte significativa dos recursos do sistema é gasta na compilação de consultas, o que não é o ideal. Normalmente, esta situação acontece se a sua aplicação gerar consultas (em vez de usar procedimentos armazenados ou consultas parametrizadas) ou se se basear em quadros de mapeamento relacional de objetos que geram consultas por padrão. 
 
Se estiver a controlar o código de aplicação, poderá considerar reescrever a camada de acesso aos dados para utilizar procedimentos armazenados ou consultas paramoporadas. No entanto, esta situação também pode ser melhorada sem alterações de aplicação, forçando a parametrização de consultas para toda a base de dados (todas as consultas) ou para os modelos de consulta individual com o mesmo haxixe de consulta. 

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre as [melhores práticas para usar a Loja de Consultas](concepts-query-store-best-practices.md)