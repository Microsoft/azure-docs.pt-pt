---
title: Consultas de pesquisa em registos do Azure Monitor Microsoft Docs
description: Este artigo fornece um tutorial para começar a usar pesquisas em consultas de registo do Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/06/2018
ms.openlocfilehash: e13f4abc37e348759e7d0b8a2f7d890c82fe0d15
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "77660245"
---
# <a name="search-queries-in-azure-monitor-logs"></a>Consultas de pesquisa em registos do Azure Monitor
As consultas de registo do Azure Monitor podem começar com um nome de mesa ou um comando de pesquisa. Este tutorial abrange consultas baseadas em pesquisa. Há vantagens em cada método.

As consultas baseadas em tabelas começam por digitalizar a consulta e, portanto, tendem a ser mais eficientes do que consultas de pesquisa. As consultas de pesquisa são menos estruturadas, o que os torna a melhor escolha quando procuram um valor específico através de colunas ou tabelas. **a pesquisa** pode digitalizar todas as colunas numa dada tabela, ou em todas as tabelas, pelo valor especificado. A quantidade de dados que estão a ser tratados pode ser enorme, razão pela qual estas consultas podem demorar mais tempo a ser concluídas e podem devolver conjuntos de resultados muito grandes.

## <a name="search-a-term"></a>Pesquisar um termo
O comando **de pesquisa** é normalmente usado para pesquisar um termo específico. No exemplo seguinte, todas as colunas em todas as tabelas são digitalizadas para o termo "erro":

```Kusto
search "error"
| take 100
```

Embora sejam fáceis de usar, consultas nãocopiadas como a acima mostrada não são eficientes e são suscetíveis de devolver muitos resultados irrelevantes. Uma melhor prática seria pesquisar na tabela relevante, ou mesmo numa coluna específica.

### <a name="table-scoping"></a>Scoping de mesa
Para pesquisar um termo numa tabela específica, adicione `in (table-name)` logo após o operador de **pesquisa:**

```Kusto
search in (Event) "error"
| take 100
```

ou em várias tabelas:
```Kusto
search in (Event, SecurityEvent) "error"
| take 100
```

### <a name="table-and-column-scoping"></a>Escoagem de mesa e coluna
Por predefinição, **a pesquisa** avaliará todas as colunas do conjunto de dados. Para pesquisar apenas uma coluna específica (chamada *Fonte* no exemplo abaixo), utilize esta sintaxe:

```Kusto
search in (Event) Source:"error"
| take 100
```

> [!TIP]
> Se utilizar `==` em vez de , os `:` resultados incluirão registos em que a coluna *Fonte* tem o valor exato "erro", e neste caso exato. A utilização de ':' incluirá registos em que *a Fonte* tem valores como "código de erro 404" ou "Erro".

## <a name="case-sensitivity"></a>Sensibilidade a casos
Por padrão, a procura por termo é insensível a casos, pelo que a pesquisa de "dns" pode produzir resultados como "DNS", "dns" ou "Dns". Para tornar o caso de pesquisa sensível, utilize a `kind` opção:

```Kusto
search kind=case_sensitive in (Event) "DNS"
| take 100
```

## <a name="use-wild-cards"></a>Use cartões selvagens
O comando **de pesquisa** suporta cartões selvagens, no início, fim ou meio de um termo.

Para pesquisar termos que começam com "ganhar":
```Kusto
search in (Event) "win*"
| take 100
```

Para pesquisar termos que terminam com ".com":
```Kusto
search in (Event) "*.com"
| take 100
```

Para pesquisar termos que contenham "www":
```Kusto
search in (Event) "*www*"
| take 100
```

Para pesquisar termos que começam com "corp" e termina em ".com", como "corp.mydomain.com".

```Kusto
search in (Event) "corp*.com"
| take 100
```

Você também pode obter tudo em uma mesa usando apenas um wild card: `search in (Event) *` , mas isso seria o mesmo que escrever apenas `Event` .

> [!TIP]
> Embora possa usar `search *` para obter todas as colunas de cada mesa, recomenda-se que sempre coloque as suas consultas em tabelas específicas. Consultas nãocopiadas podem demorar algum tempo a concluir e podem devolver demasiados resultados.

## <a name="add-and--or-to-search-queries"></a>Adicionar *e* / *ou* pesquisar consultas
Utilizar **e** procurar registos que contenham vários termos:

```Kusto
search in (Event) "error" and "register"
| take 100
```

Utilize **ou** obtenha registos que contenham pelo menos um dos termos:

```Kusto
search in (Event) "error" or "register"
| take 100
```

Se tiver várias condições de pesquisa, pode combiná-las na mesma consulta usando parênteses:

```Kusto
search in (Event) "error" and ("register" or "marshal*")
| take 100
```

Os resultados deste exemplo seriam registos que contêm o termo "erro" e também contêm "registo" ou algo que começa com "marechal".

## <a name="pipe-search-queries"></a>Consultas de pesquisa de tubos
Tal como qualquer outro comando, **a pesquisa** pode ser canalizada para que os resultados da pesquisa possam ser filtrados, classificados e agregados. Por exemplo, para obter o número de registos de *eventos* que contêm "win":

```Kusto
search in (Event) "win"
| count
```




## <a name="next-steps"></a>Passos seguintes

- Consulte mais tutoriais no site de [idioma de consulta de Kusto.](/azure/kusto/query/)
