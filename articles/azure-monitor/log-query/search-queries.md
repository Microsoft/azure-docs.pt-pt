---
title: Consultas de pesquisa nos registos do Monitor Azure / Microsoft Docs
description: Este artigo fornece um tutorial para começar a usar a pesquisa em consultas de registo do Monitor Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/06/2018
ms.openlocfilehash: e13f4abc37e348759e7d0b8a2f7d890c82fe0d15
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77660245"
---
# <a name="search-queries-in-azure-monitor-logs"></a>Consultas de pesquisa em registos do Monitor Azure
As consultas de registo do Azure Monitor podem começar com um nome de mesa ou um comando de pesquisa. Este tutorial cobre consultas baseadas em pesquisa. Há vantagens em cada método.

As consultas baseadas em mesas começam por pesquisar a consulta e, portanto, tendem a ser mais eficientes do que consultas de pesquisa. As consultas de pesquisa são menos estruturadas, o que as torna a melhor escolha quando procuram um valor específico entre colunas ou tabelas. **pesquisar** pode digitalizar todas as colunas numa determinada tabela, ou em todas as tabelas, pelo valor especificado. A quantidade de dados que estão a ser tratados pode ser enorme, razão pela qual estas consultas podem demorar mais tempo a ser concluídas e podem devolver conjuntos de resultados muito grandes.

## <a name="search-a-term"></a>Pesquisar um termo
O comando de **pesquisa** é normalmente usado para pesquisar um termo específico. No exemplo seguinte, todas as colunas em todas as tabelas são digitalizadas para o termo "erro":

```Kusto
search "error"
| take 100
```

Embora sejam fáceis de usar, consultas desmeparadas como a que se apresentam acima não são eficientes e são suscetíveis de devolver muitos resultados irrelevantes. Uma prática melhor seria procurar na tabela relevante, ou mesmo numa coluna específica.

### <a name="table-scoping"></a>Scoping de mesa
Para pesquisar um termo numa `in (table-name)` tabela específica, adicione logo após o operador de **pesquisa:**

```Kusto
search in (Event) "error"
| take 100
```

ou em várias tabelas:
```Kusto
search in (Event, SecurityEvent) "error"
| take 100
```

### <a name="table-and-column-scoping"></a>Deteção de tabelas e colunas
Por predefinição, **a pesquisa** avaliará todas as colunas do conjunto de dados. Para pesquisar apenas uma coluna específica (denominada *Fonte* no exemplo abaixo), utilize esta sintaxe:

```Kusto
search in (Event) Source:"error"
| take 100
```

> [!TIP]
> Se utilizar `==` em `:`vez de , os resultados incluirão registos em que a coluna *Fonte* tem o valor exato de "erro", e neste caso exato. A utilização de ':' incluirá registos em que a *Fonte* tem valores como "error code 404" ou "Error".

## <a name="case-sensitivity"></a>Sensibilidade ao caso
Por predefinição, a pesquisa a prazo é insensível a casos, pelo que pesquisar "DNS" pode produzir resultados como "DNS", "DNS" ou "Dns". Para tornar o caso de `kind` pesquisa sensível, utilize a opção:

```Kusto
search kind=case_sensitive in (Event) "DNS"
| take 100
```

## <a name="use-wild-cards"></a>Use cartões selvagens
O comando de **busca** suporta cartões selvagens, no início, fim ou meio de um termo.

Para pesquisar termos que começam com "win":
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

Você também pode colocar tudo em uma mesa `search in (Event) *`usando apenas um wild `Event`card: , mas isso seria o mesmo que escrever apenas .

> [!TIP]
> Embora possa `search *` usar para obter todas as colunas de cada tabela, recomenda-se que consulte sempre as suas consultas para tabelas específicas. Consultas desmeparadas podem demorar algum tempo a ser completadas e podem devolver demasiados resultados.

## <a name="add-and--or-to-search-queries"></a>Adicionar *e* / *ou* pesquisar consultas
Utilizar **e** procurar registos que contenham vários termos:

```Kusto
search in (Event) "error" and "register"
| take 100
```

Utilize **ou** para obter registos que contenham pelo menos um dos termos:

```Kusto
search in (Event) "error" or "register"
| take 100
```

Se tiver múltiplas condições de pesquisa, pode combiná-las na mesma consulta usando parênteses:

```Kusto
search in (Event) "error" and ("register" or "marshal*")
| take 100
```

Os resultados deste exemplo seriam registos que contêm o termo "erro" e também contêm "registo" ou algo que comece com "marechal".

## <a name="pipe-search-queries"></a>Consultas de pesquisa de tubos
Tal como qualquer outro comando, a **procura** pode ser canalizada para que os resultados da pesquisa possam ser filtrados, classificados e agregados. Por exemplo, para obter o número de registos do *Evento* que contêm "win":

```Kusto
search in (Event) "win"
| count
```




## <a name="next-steps"></a>Passos seguintes

- Consulte mais tutoriais no site de [linguagem de consulta kusto](/azure/kusto/query/).
