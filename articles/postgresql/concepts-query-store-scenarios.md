---
title: Cenários de utilização de Store de consulta na base de dados do Azure para PostgreSQL - servidor único
description: Este artigo descreve alguns cenários para o Store de consulta na base de dados do Azure para PostgreSQL - único servidor.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 029c595ba983d3b758568fbacaf6577014d893db
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "65067286"
---
# <a name="usage-scenarios-for-query-store"></a>Cenários de utilização de Store de consulta

**Aplica-se a:** Base de dados do Azure para PostgreSQL - único servidor 9.6 e 10

Pode utilizar o Query Store numa ampla variedade de cenários nos quais um desempenho previsível de carga de trabalho está acompanhando e manutenção é crítico. Considere os seguintes exemplos: 
- Identificar e ajustar as consultas dispendiosas superior 
- A testes a / B 
- Manter o desempenho estável durante as atualizações 
- Identificar e melhorando a cargas de trabalho ad hoc 

## <a name="identify-and-tune-expensive-queries"></a>Identificar e ajustar consultas dispendiosas 

### <a name="identify-longest-running-queries"></a>Identificar as consultas de execução mais longa 
Utilize o [Query Performance Insight](concepts-query-performance-insight.md) vista no portal do Azure para identificar rapidamente as consultas em execução mais longa. Estas consultas normalmente tendem a consumir uma quantidade significativa de recursos. Otimizar as suas perguntas de maior duração pode melhorar o desempenho, liberando recursos para utilização por outras consultas em execução no seu sistema. 

### <a name="target-queries-with-performance-deltas"></a>Consultas de destino com deltas de desempenho 
Consulta Store reparte os dados de desempenho em intervalos de tempo, para que possa acompanhar o desempenho de uma consulta ao longo do tempo. Isto ajuda a identificar exatamente que consultas estão a contribuir para um aumento no tempo total gasto. Assim pode fazer o destino de resolução de problemas da sua carga de trabalho.

### <a name="tuning-expensive-queries"></a>Ajuste de consultas dispendiosas 
Ao identificar uma consulta com um desempenho inferior ao ideal, a ação que tomar depende da natureza do problema: 
- Uso [recomendações de desempenho](concepts-performance-recommendations.md) determinar se existem algumas sugestões de índices. Se Sim, crie o índice e, em seguida, utilizar Store de consulta para avaliar o desempenho de consulta depois de criar o índice. 
- Certifique-se de que as estatísticas são atualizadas para as tabelas subjacentes usadas pela consulta.
- Pense em reescrever as consultas dispendiosas. Por exemplo, tire partido de parametrização de consulta e reduzir a utilização do SQL dinâmico. Implementar a lógica ideal durante a leitura de dados, como a aplicar dados filtragem no lado da base de dados, não no lado do aplicativo. 


## <a name="ab-testing"></a>A testes a / B 
Store de consulta de utilização para comparar o desempenho da carga de trabalho antes e após uma alteração de aplicação que pretende apresentar. Exemplos de cenários de utilização do Store de consulta para avaliar o impacto da aplicação de ambiente ou alteram para desempenho da carga de trabalho: 
- Implementar uma nova versão de um aplicativo. 
- Adicionar recursos adicionais para o servidor. 
- Criar índices em falta em tabelas referenciadas por consultas dispendiosas. 
 
Em qualquer um destes cenários, aplicam-se o fluxo de trabalho seguinte: 
1. Execute sua carga de trabalho com consultas Store antes da alteração planeada para gerar uma linha de base de desempenho. 
2. Aplicam-se n alteração ou do aplicativo alterações neste momento controlado no tempo. 
3. Continue a executar a carga de trabalho tempo suficiente para gerar a imagem de desempenho do sistema após a alteração. 
4. Compare os resultados de antes e após a alteração. 
5. Decida se pretende manter a alteração ou reversão. 


## <a name="identify-and-improve-ad-hoc-workloads"></a>Identificar e melhorar as cargas de trabalho ad hoc 
Algumas cargas de trabalho não tem dominantes consultas que pode otimizar para melhorar o desempenho geral do aplicativo. Normalmente, essas cargas de trabalho são caracterizadas com um número relativamente grande de consultas exclusivos, cada um deles consumir uma parte dos recursos do sistema. Cada consulta exclusiva é executada com pouca frequência, individualmente que seu consumo de tempo de execução não é crítico. Por outro lado, uma vez que a aplicação está a gerar novas consultas o tempo todo, uma parte significativa de recursos do sistema é gasto na compilação da consulta, que não é ideal. Normalmente, esta situação acontece se a sua aplicação gerar consultas (em vez de usar procedimentos armazenados ou consultas parametrizadas) ou se baseia-se em arquiteturas de mapeamento relacional de objetos que geram consultas por predefinição. 
 
Se estiver no controle de código da aplicação, poderá considerar reescrever a camada de acesso de dados para utilizar os procedimentos armazenados ou consultas parametrizadas. No entanto, esta situação pode ser também melhorada sem alterações à aplicação forçando a parametrização de consulta para a base de dados (todas as consultas) ou para os modelos de consulta individual com o mesmo hash de consulta. 

## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre o [melhores práticas para utilizar o Query Store](concepts-query-store-best-practices.md)