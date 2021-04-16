---
title: Cenários da Loja de Consultas - Base de Dados Azure para PostgreSQL - Servidor Flex
description: Este artigo descreve alguns cenários para a Loja de Consultas na Base de Dados Azure para PostgreSQL - Flex Server.
author: ssen-msft
ms.author: ssen
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/01/2021
ms.openlocfilehash: ac0374cf91229e7f1b3f84d967d9825e2a3090f2
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559276"
---
# <a name="usage-scenarios-for-query-store---flexible-server"></a>Cenários de utilização para Loja de Consultas - Servidor Flexível

**Aplica-se a:** Base de Dados Azure para PostgreSQL - Versões single server 11, 12

Você pode usar a Loja de Consulta em uma grande variedade de cenários em que rastrear e manter o desempenho previsível da carga de trabalho é crítico. Considere os seguintes exemplos: 
- Identificar e afinar consultas caras de topo 
- Testes A/B 
- Manter o desempenho estável durante as atualizações 
- Identificar e melhorar as cargas de trabalho ad hoc 

## <a name="identify-and-tune-expensive-queries"></a>Identificar e afinar consultas caras 

### <a name="identify-longest-running-queries"></a>Identificar consultas de execução mais longas 
Utilize as vistas da Loja de Consultas na base de dados azure_sys do seu servidor para identificar rapidamente as consultas mais longas. Estas consultas tendem tipicamente a consumir mais recursos. Otimizar as suas consultas de execução mais longas pode melhorar o desempenho libertando os recursos utilizados por outras consultas em execução no seu sistema. 

### <a name="target-queries-with-performance-deltas"></a>Consultas de destino com deltas de desempenho 
A Consulta Store corta os dados de desempenho em janelas de tempo, para que possa acompanhar o desempenho de uma consulta ao longo do tempo. Isto ajuda-o a identificar exatamente quais as consultas que estão a contribuir para um aumento do tempo total gasto. Como resultado, pode fazer uma resolução de problemas direcionada da sua carga de trabalho.

### <a name="tuning-expensive-queries"></a>Consultas caras de afinação 
Quando identifica uma consulta com desempenho subóptima, a ação que toma depende da natureza do problema: 
- Certifique-se de que as estatísticas estão atualizadas para as tabelas subjacentes utilizadas pela consulta.
- Considere reescrever consultas caras. Por exemplo, aproveite a parâmetroização da consulta e reduza o uso de SQL dinâmico. Implemente a lógica ideal ao ler dados como aplicar a filtragem de dados no lado da base de dados, e não no lado da aplicação. 


## <a name="ab-testing"></a>Testes A/B 
Utilize a Loja de Consultas para comparar o desempenho da carga de trabalho antes e depois de uma alteração de aplicação que pretende introduzir ou antes e depois da migração. Exemplo de cenários para a utilização da Loja de Consultas para avaliar o impacto das alterações no desempenho da carga de trabalho: 
- Migração entre versões PostgreSQL. 
- Lançando uma nova versão de uma aplicação. 
- Adicionando recursos adicionais ao servidor. 
- Criando índices em falta em tabelas referenciadas por consultas caras. 
- Migração de Servidor Único para Servidor Flex. 
 
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
