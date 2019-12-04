---
title: Cenários de Repositório de Consultas-banco de dados do Azure para PostgreSQL-servidor único
description: Este artigo descreve alguns cenários para o Repositório de Consultas no banco de dados do Azure para PostgreSQL-servidor único.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 31e3f82b6ea1b1fc15c0832dc03edce2a59f1e1b
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74768355"
---
# <a name="usage-scenarios-for-query-store"></a>Cenários de uso para Repositório de Consultas

**Aplica-se a:** Banco de dados do Azure para PostgreSQL-versões de servidor único 9,6, 10, 11

Você pode usar Repositório de Consultas em uma ampla variedade de cenários em que o rastreamento e a manutenção do desempenho previsível da carga de trabalho é essencial. Considere os seguintes exemplos: 
- Identificando e ajustando as principais consultas caras 
- Testes A/B 
- Mantendo o desempenho estável durante as atualizações 
- Identificando e melhorando cargas de trabalho ad hoc 

## <a name="identify-and-tune-expensive-queries"></a>Identificar e ajustar consultas caras 

### <a name="identify-longest-running-queries"></a>Identificar consultas de execução mais longa 
Use a exibição [análise de desempenho de consultas](concepts-query-performance-insight.md) no portal do Azure para identificar rapidamente as consultas em execução mais longas. Essas consultas geralmente tendem a consumir um valor significativo de recursos. Otimizar suas perguntas mais longas pode melhorar o desempenho liberando recursos para uso por outras consultas em execução no seu sistema. 

### <a name="target-queries-with-performance-deltas"></a>Consultas de destino com deltas de desempenho 
Repositório de Consultas fatia os dados de desempenho em janelas de tempo, para que você possa acompanhar o desempenho de uma consulta ao longo do tempo. Isso ajuda a identificar exatamente quais consultas estão contribuindo para um aumento no tempo total gasto. Como resultado, você pode fazer a solução de problemas direcionada de sua carga de trabalho.

### <a name="tuning-expensive-queries"></a>Ajustando consultas caras 
Quando você identifica uma consulta com desempenho inferior, a ação executada depende da natureza do problema: 
- Use as [recomendações de desempenho](concepts-performance-recommendations.md) para determinar se há quaisquer índices sugeridos. Se sim, crie o índice e, em seguida, use Repositório de Consultas para avaliar o desempenho da consulta depois de criar o índice. 
- Verifique se as estatísticas estão atualizadas para as tabelas subjacentes usadas pela consulta.
- Considere reescrever consultas caras. Por exemplo, aproveite a parametrização de consulta e reduza o uso do SQL dinâmico. Implemente uma lógica ideal ao ler dados, como aplicar filtragem de dados no lado do banco, não no lado do aplicativo. 


## <a name="ab-testing"></a>Testes A/B 
Use Repositório de Consultas para comparar o desempenho da carga de trabalho antes e depois de uma alteração de aplicativo que você planeja introduzir. Exemplos de cenários para usar o Repositório de Consultas para avaliar o impacto do ambiente ou da alteração do aplicativo para o desempenho da carga de trabalho: 
- Distribuir uma nova versão de um aplicativo. 
- Adicionando recursos adicionais ao servidor. 
- Criando índices ausentes em tabelas referenciadas por consultas caras. 
 
Em qualquer um desses cenários, aplique o seguinte fluxo de trabalho: 
1. Execute sua carga de trabalho com Repositório de Consultas antes da alteração planejada para gerar uma linha de base de desempenho. 
2. Aplicar alteração (ões) de aplicativo no momento controlado. 
3. Continue executando a carga de trabalho por tempo suficiente para gerar a imagem de desempenho do sistema após a alteração. 
4. Compare os resultados de antes e depois da alteração. 
5. Decida se deseja manter a alteração ou a reversão. 


## <a name="identify-and-improve-ad-hoc-workloads"></a>Identificar e melhorar cargas de trabalho ad hoc 
Algumas cargas de trabalho não têm consultas dominantes que você pode ajustar para melhorar o desempenho geral do aplicativo. Essas cargas de trabalho geralmente são caracterizadas com um número relativamente grande de consultas exclusivas, cada uma delas consumindo uma parte dos recursos do sistema. Cada consulta exclusiva é executada com pouca frequência, portanto, individualmente, o consumo de tempo de execução não é crítico. Por outro lado, Considerando que o aplicativo está gerando novas consultas o tempo todo, uma parte significativa dos recursos do sistema é gasta na compilação da consulta, o que não é o ideal. Normalmente, essa situação ocorre se seu aplicativo gerar consultas (em vez de usar procedimentos armazenados ou consultas parametrizadas) ou se depender de estruturas de mapeamento relacional de objeto que geram consultas por padrão. 
 
Se você estiver no controle do código do aplicativo, poderá considerar reescrever a camada de acesso a dados para usar procedimentos armazenados ou consultas parametrizadas. No entanto, essa situação também pode ser aprimorada sem alterações de aplicativo, forçando a parametrização de consulta para todo o banco de dados (todas as consultas) ou para os modelos de consulta individuais com o mesmo hash de consulta. 

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre as [práticas recomendadas para usar repositório de consultas](concepts-query-store-best-practices.md)