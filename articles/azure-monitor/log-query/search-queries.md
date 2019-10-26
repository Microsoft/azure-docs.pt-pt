---
title: Pesquisar consultas em logs de Azure Monitor | Microsoft Docs
description: Este artigo fornece um tutorial para começar a usar a pesquisa em Azure Monitor consultas de log.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/06/2018
ms.openlocfilehash: d92cd42f0fceadee16035b605e8d25c6bc23bc67
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72933007"
---
# <a name="search-queries-in-azure-monitor-logs"></a>Pesquisar consultas em logs de Azure Monitor
Azure Monitor consultas de log podem começar com um nome de tabela ou um comando de pesquisa. Este tutorial aborda as consultas baseadas em pesquisa. Há vantagens para cada método.

As consultas baseadas em tabela começam com o escopo da consulta e, portanto, tendem a ser mais eficientes do que as consultas de pesquisa. As consultas de pesquisa são menos estruturadas, o que as torna a melhor opção ao pesquisar um valor específico entre colunas ou tabelas. a **pesquisa** pode verificar todas as colunas em uma determinada tabela ou, em todas as tabelas, para o valor especificado. A quantidade de dados que estão sendo processados pode ser enorme, motivo pelo qual essas consultas podem levar mais tempo para serem concluídas e podem retornar conjuntos de resultados muito grandes.

## <a name="search-a-term"></a>Pesquisar um termo
O comando de **pesquisa** normalmente é usado para pesquisar um termo específico. No exemplo a seguir, todas as colunas em todas as tabelas são verificadas para o termo "erro":

```Kusto
search "error"
| take 100
```

Embora eles sejam fáceis de usar, as consultas sem escopo como a mostrada acima não são eficientes e provavelmente retornarão muitos resultados irrelevantes. Uma prática melhor seria Pesquisar na tabela relevante ou até mesmo em uma coluna específica.

### <a name="table-scoping"></a>Escopo da tabela
Para pesquisar um termo em uma tabela específica, adicione `in (table-name)` logo após o operador de **pesquisa** :

```Kusto
search in (Event) "error"
| take 100
```

ou em várias tabelas:
```Kusto
search in (Event, SecurityEvent) "error"
| take 100
```

### <a name="table-and-column-scoping"></a>Escopo de tabela e coluna
Por padrão, a **pesquisa** avaliará todas as colunas no conjunto de dados. Para pesquisar apenas uma coluna específica (denominada *origem* no exemplo abaixo), use esta sintaxe:

```Kusto
search in (Event) Source:"error"
| take 100
```

> [!TIP]
> Se você usar `==` em vez de `:`, os resultados incluirão registros em que a coluna de *origem* tem o valor exato de "erro" e, nesse caso exato. Usar ': ' incluirá registros em que a *origem* tem valores como "código de erro 404" ou "erro".

## <a name="case-sensitivity"></a>Diferenciação de maiúsculas e minúsculas
Por padrão, a pesquisa de termo não diferencia maiúsculas de minúsculas, então Pesquisar "DNS" pode produzir resultados como "DNS", "DNS" ou "DNS". Para fazer a pesquisa diferenciar maiúsculas de minúsculas, use a opção `kind`:

```Kusto
search kind=case_sensitive in (Event) "DNS"
| take 100
```

## <a name="use-wild-cards"></a>Usar curingas
O comando **Search** dá suporte a curingas, no início, no final ou no meio de um termo.

Para pesquisar os termos que começam com "win":
```Kusto
search in (Event) "win*"
| take 100
```

Para pesquisar termos que terminem com ". com":
```Kusto
search in (Event) "*.com"
| take 100
```

Para pesquisar termos que contenham "www":
```Kusto
search in (Event) "*www*"
| take 100
```

Para pesquisar termos que comecem com "Corp" e terminem em ". com", como "corp.mydomain.com" "

```Kusto
search in (Event) "corp*.com"
| take 100
```

Você também pode obter tudo em uma tabela usando apenas um curinga: `search in (Event) *`, mas isso seria o mesmo que escrever apenas `Event`.

> [!TIP]
> Embora você possa usar `search *` para obter todas as colunas de cada tabela, é recomendável que você sempre faça o escopo de suas consultas para tabelas específicas. As consultas sem escopo podem demorar um pouco para serem concluídas e podem retornar muitos resultados.

## <a name="add-and--or-to-search-queries"></a>Adicionar *e* / *ou* para pesquisar consultas
Use **e** para procurar registros que contenham vários termos:

```Kusto
search in (Event) "error" and "register"
| take 100
```

Use **ou** para obter registros que contenham pelo menos um dos termos:

```Kusto
search in (Event) "error" or "register"
| take 100
```

Se você tiver vários critérios de pesquisa, poderá combiná-los na mesma consulta usando parênteses:

```Kusto
search in (Event) "error" and ("register" or "marshal*")
| take 100
```

Os resultados desse exemplo seriam registros que contêm o termo "erro" e também contêm "registro" ou algo que começa com "Marshal".

## <a name="pipe-search-queries"></a>Consultas de pesquisa de pipe
Assim como qualquer outro comando, a **pesquisa** pode ser canalizada para que os resultados da pesquisa possam ser filtrados, classificados e agregados. Por exemplo, para obter o número de registros de *eventos* que contêm "win":

```Kusto
search in (Event) "win"
| count
```




## <a name="next-steps"></a>Passos seguintes

- Consulte mais tutoriais no [site de linguagem de consulta do Kusto](/azure/kusto/query/).
