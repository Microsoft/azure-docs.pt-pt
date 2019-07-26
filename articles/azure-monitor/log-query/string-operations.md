---
title: Trabalhar com cadeias de caracteres em consultas de log de Azure Monitor | Microsoft Docs
description: Descreve como editar, comparar, Pesquisar e executar uma variedade de outras operações em cadeias de caracteres em Azure Monitor consultas de log.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/16/2018
ms.author: bwren
ms.openlocfilehash: f53d3bd64b4f837fe29baa338cd338158d59d95d
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68466958"
---
# <a name="work-with-strings-in-azure-monitor-log-queries"></a>Trabalhar com cadeias de caracteres em consultas de log de Azure Monitor


> [!NOTE]
> Você deve concluir a introdução [ao Azure Monitor log Analytics](get-started-portal.md) e [a introdução às consultas de Azure monitor log](get-started-queries.md) antes de concluir este tutorial.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Este artigo descreve como editar, comparar, Pesquisar e executar uma variedade de outras operações em cadeias de caracteres.

Cada caractere em uma cadeia de caracteres tem um número de índice, de acordo com seu local. O primeiro caractere está no índice 0, o próximo caractere é 1 e, portanto, um. Funções de cadeia de caracteres diferentes usam números de índice, conforme mostrado nas seções a seguir. Muitos dos exemplos a seguir usam o comando **Print** para para demonstrar a manipulação de cadeia de caracteres sem usar uma fonte de dados específica.


## <a name="strings-and-escaping-them"></a>Cadeias de caracteres e saída
Os valores de cadeia de caracteres são encapsulados com caracteres de aspas simples ou duplas. Barra invertida\) (é usada para escapar caracteres para o caractere após ele, como \t para Tab, \n para nova linha e \" o próprio caractere de aspas.

```Kusto
print "this is a 'string' literal in double \" quotes"
```

```Kusto
print 'this is a "string" literal in single \' quotes'
```

Para impedir que\\"" atue como um caractere de escape, adicione\@"" como um prefixo à cadeia de caracteres:

```Kusto
print @"C:\backslash\not\escaped\with @ prefix"
```


## <a name="string-comparisons"></a>Comparações de cadeia de caracteres

Operator       |Descrição                         |Diferenciar maiúsculas de minúsculas|Exemplo (rendimento `true`)
---------------|------------------------------------|--------------|-----------------------
`==`           |É igual a                              |Sim           |`"aBc" == "aBc"`
`!=`           |Diferente de                          |Sim           |`"abc" != "ABC"`
`=~`           |É igual a                              |Não            |`"abc" =~ "ABC"`
`!~`           |Diferente de                          |Não            |`"aBc" !~ "xyz"`
`has`          |O lado direito é um termo completo no lado esquerdo |Não|`"North America" has "america"`
`!has`         |O lado direito não é um termo completo no lado esquerdo       |Não            |`"North America" !has "amer"` 
`has_cs`       |O lado direito é um termo completo no lado esquerdo |Sim|`"North America" has_cs "America"`
`!has_cs`      |O lado direito não é um termo completo no lado esquerdo       |Sim            |`"North America" !has_cs "amer"` 
`hasprefix`    |O lado direito é um prefixo de termo no lado esquerdo         |Não            |`"North America" hasprefix "ame"`
`!hasprefix`   |O lado direito não é um prefixo de termo no lado esquerdo     |Não            |`"North America" !hasprefix "mer"` 
`hasprefix_cs`    |O lado direito é um prefixo de termo no lado esquerdo         |Sim            |`"North America" hasprefix_cs "Ame"`
`!hasprefix_cs`   |O lado direito não é um prefixo de termo no lado esquerdo     |Sim            |`"North America" !hasprefix_cs "CA"` 
`hassuffix`    |O lado direito é um sufixo de termo no lado esquerdo         |Não            |`"North America" hassuffix "ica"`
`!hassuffix`   |O lado direito não é um sufixo de termo no lado esquerdo     |Não            |`"North America" !hassuffix "americ"`
`hassuffix_cs`    |O lado direito é um sufixo de termo no lado esquerdo         |Sim            |`"North America" hassuffix_cs "ica"`
`!hassuffix_cs`   |O lado direito não é um sufixo de termo no lado esquerdo     |Sim            |`"North America" !hassuffix_cs "icA"`
`contains`     |O lado direito ocorre como uma subsequência do lado esquerdo  |Não            |`"FabriKam" contains "BRik"`
`!contains`    |O lado direito não ocorre no lado esquerdo           |Não            |`"Fabrikam" !contains "xyz"`
`contains_cs`   |O lado direito ocorre como uma subsequência do lado esquerdo  |Sim           |`"FabriKam" contains_cs "Kam"`
`!contains_cs`  |O lado direito não ocorre no lado esquerdo           |Sim           |`"Fabrikam" !contains_cs "Kam"`
`startswith`   |O lado direito é uma subsequência inicial do lado esquerdo|Não            |`"Fabrikam" startswith "fab"`
`!startswith`  |O lado direito não é uma subsequência inicial do lado esquerdo|Não        |`"Fabrikam" !startswith "kam"`
`startswith_cs`   |O lado direito é uma subsequência inicial do lado esquerdo|Sim            |`"Fabrikam" startswith_cs "Fab"`
`!startswith_cs`  |O lado direito não é uma subsequência inicial do lado esquerdo|Sim        |`"Fabrikam" !startswith_cs "fab"`
`endswith`     |O lado direito é uma subsequência de fechamento do lado esquerdo|Não             |`"Fabrikam" endswith "Kam"`
`!endswith`    |O lado direito não é uma subsequência de fechamento do lado esquerdo|Não         |`"Fabrikam" !endswith "brik"`
`endswith_cs`     |O lado direito é uma subsequência de fechamento do lado esquerdo|Sim             |`"Fabrikam" endswith "Kam"`
`!endswith_cs`    |O lado direito não é uma subsequência de fechamento do lado esquerdo|Sim         |`"Fabrikam" !endswith "brik"`
`matches regex`|o lado esquerdo contém uma correspondência para o lado direito        |Sim           |`"Fabrikam" matches regex "b.*k"`
`in`           |É igual a um dos elementos       |Sim           |`"abc" in ("123", "345", "abc")`
`!in`          |Não é igual a qualquer um dos elementos   |Sim           |`"bca" !in ("123", "345", "abc")`


## <a name="countof"></a>countof

Conta ocorrências de uma subcadeia de caracteres em uma cadeia de caracteres. Pode corresponder a cadeias de caracteres sem formatação ou usar Regex. Correspondências de cadeia de caracteres simples podem se sobrepor enquanto Regex não corresponde a.

### <a name="syntax"></a>Sintaxe
```
countof(text, search [, kind])
```

### <a name="arguments"></a>Argumentos:
- `text`-A cadeia de caracteres de entrada 
- `search`-Cadeia de caracteres simples ou expressão regular para correspondência dentro do texto.
- `kind` -  | _Regex_ normal (padrão: normal).

### <a name="returns"></a>Devolve

O número de vezes que a cadeia de caracteres de pesquisa pode ser correspondida no contêiner. Correspondências de cadeia de caracteres simples podem se sobrepor enquanto o Regex faz a correspondência.

### <a name="examples"></a>Exemplos

#### <a name="plain-string-matches"></a>Correspondências de cadeia de caracteres simples

```Kusto
print countof("The cat sat on the mat", "at");  //result: 3
print countof("aaa", "a");  //result: 3
print countof("aaaa", "aa");  //result: 3 (not 2!)
print countof("ababa", "ab", "normal");  //result: 2
print countof("ababa", "aba");  //result: 2
```

#### <a name="regex-matches"></a>Correspondências de Regex

```Kusto
print countof("The cat sat on the mat", @"\b.at\b", "regex");  //result: 3
print countof("ababa", "aba", "regex");  //result: 1
print countof("abcabc", "a.c", "regex");  // result: 2
```


## <a name="extract"></a>auto-extraível

Obtém uma correspondência para uma expressão regular de uma determinada cadeia de caracteres. Opcionalmente também converte a subcadeia de caracteres extraída do tipo especificado.

### <a name="syntax"></a>Sintaxe

```Kusto
extract(regex, captureGroup, text [, typeLiteral])
```

### <a name="arguments"></a>Argumentos

- `regex`-Uma expressão regular.
- `captureGroup`-Uma constante inteira positiva que indica o grupo de captura a ser extraído. 0 para a correspondência inteira, 1 para o valor correspondido pelo primeiro ' (' parêntese ') ' na expressão regular, 2 ou mais para parênteses subsequentes.
- `text`-Uma cadeia de caracteres a ser pesquisada.
- `typeLiteral`-Um literal de tipo opcional (por exemplo, typeof (Long)). Se fornecido, a subcadeia de caracteres extraída será convertida nesse tipo.

### <a name="returns"></a>Devolve
A subcadeia de caracteres correspondente ao grupo de captura de grupos de captura indicado, opcionalmente, convertida em typeLiteral.
Se não houver correspondência ou a conversão de tipo falhar, retornará NULL.

### <a name="examples"></a>Exemplos

O exemplo a seguir extrai o último octeto de *ComputerIP* de um registro de pulsação:
```Kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| project ComputerIP, last_octet=extract("([0-9]*$)", 1, ComputerIP) 
```

O exemplo a seguir extrai o último octeto, converte-o em um tipo *real* (Number) e calcula o próximo valor de IP
```Kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| extend last_octet=extract("([0-9]*$)", 1, ComputerIP, typeof(real)) 
| extend next_ip=(last_octet+1)%255
| project ComputerIP, last_octet, next_ip
```

No exemplo a seguir, o *rastreamento* de cadeia de caracteres é pesquisado em busca de uma definição de "duração". A correspondência é convertida em *real* e multiplicada por uma constante de tempo (1 s) *que converte a duração para o tipo TimeSpan*.
```Kusto
let Trace="A=12, B=34, Duration=567, ...";
print Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real));  //result: 567
print Duration_seconds =  extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s);  //result: 00:09:27
```


## <a name="isempty-isnotempty-notempty"></a>IsEmpty, IsNotEmpty, não vazio

- *IsEmpty* retornará true se o argumento for uma cadeia de caracteres vazia ou NULL (consulte também *IsNull*).
- *IsNotEmpty* retornará true se o argumento não for uma cadeia de caracteres vazia ou NULL (consulte também *IsNotNull*). alias: não *vazio*.

### <a name="syntax"></a>Sintaxe

```Kusto
isempty(value)
isnotempty(value)
```

### <a name="examples"></a>Exemplos

```Kusto
print isempty("");  // result: true

print isempty("0");  // result: false

print isempty(0);  // result: false

print isempty(5);  // result: false

Heartbeat | where isnotempty(ComputerIP) | take 1  // return 1 Heartbeat record in which ComputerIP isn't empty
```


## <a name="parseurl"></a>parseurl

Divide uma URL em suas partes (protocolo, host, porta, etc.) e retorna um objeto Dictionary contendo as partes como cadeias de caracteres.

### <a name="syntax"></a>Sintaxe

```
parseurl(urlstring)
```

### <a name="examples"></a>Exemplos

```Kusto
print parseurl("http://user:pass@contoso.com/icecream/buy.aspx?a=1&b=2#tag")
```

O resultado será:
```
{
    "Scheme" : "http",
    "Host" : "contoso.com",
    "Port" : "80",
    "Path" : "/icecream/buy.aspx",
    "Username" : "user",
    "Password" : "pass",
    "Query Parameters" : {"a":"1","b":"2"},
    "Fragment" : "tag"
}
```


## <a name="replace"></a>Substitua

Substitui todas as correspondências de Regex por outra cadeia de caracteres. 

### <a name="syntax"></a>Sintaxe

```
replace(regex, rewrite, input_text)
```

### <a name="arguments"></a>Argumentos

- `regex`-A expressão regular a ser correspondente. Ele pode conter grupos de captura em ' (' parênteses ') '.
- `rewrite`-O Regex de substituição para qualquer correspondência feita por Regex correspondente. Use \ 0 para se referir a toda a correspondência, \ 1 para o primeiro grupo de captura, \ 2, e assim por diante para grupos de captura subsequentes.
- `input_text`-A cadeia de caracteres de entrada para pesquisa.

### <a name="returns"></a>Devolve
O texto depois de substituir todas as correspondências de Regex por avaliações de reescrita. As correspondências não se sobrepõem.

### <a name="examples"></a>Exemplos

```Kusto
SecurityEvent
| take 1
| project Activity 
| extend replaced = replace(@"(\d+) -", @"Activity ID \1: ", Activity) 
```

Pode ter os seguintes resultados:

Atividade                                        |substituição
------------------------------------------------|----------------------------------------------------------
4663-foi feita uma tentativa de acessar um objeto  |ID da atividade 4663: Foi feita uma tentativa de acessar um objeto.


## <a name="split"></a>split

Divide uma determinada cadeia de caracteres de acordo com um delimitador especificado e retorna uma matriz das subcadeias de caracteres resultantes.

### <a name="syntax"></a>Sintaxe
```
split(source, delimiter [, requestedIndex])
```

### <a name="arguments"></a>Argumentos:

- `source`-A cadeia de caracteres a ser dividida de acordo com o delimitador especificado.
- `delimiter`-O delimitador que será usado para dividir a cadeia de caracteres de origem.
- `requestedIndex`-Um índice opcional baseado em zero. Se fornecido, a matriz de cadeia de caracteres retornada manterá somente esse item (se existir).


### <a name="examples"></a>Exemplos

```Kusto
print split("aaa_bbb_ccc", "_");    // result: ["aaa","bbb","ccc"]
print split("aa_bb", "_");          // result: ["aa","bb"]
print split("aaa_bbb_ccc", "_", 1); // result: ["bbb"]
print split("", "_");               // result: [""]
print split("a__b", "_");           // result: ["a","","b"]
print split("aabbcc", "bb");        // result: ["aa","cc"]
```

## <a name="strcat"></a>strcat

Concatena argumentos de cadeia de caracteres (dá suporte a argumentos 1-16).

### <a name="syntax"></a>Sintaxe
```
strcat("string1", "string2", "string3")
```

### <a name="examples"></a>Exemplos
```Kusto
print strcat("hello", " ", "world") // result: "hello world"
```


## <a name="strlen"></a>strlen

Retorna o comprimento de uma cadeia de caracteres.

### <a name="syntax"></a>Sintaxe
```
strlen("text_to_evaluate")
```

### <a name="examples"></a>Exemplos
```Kusto
print strlen("hello")   // result: 5
```


## <a name="substring"></a>Subcadeia

Extrai uma substring de uma determinada cadeia de caracteres de origem, iniciando no índice especificado. Opcionalmente, o comprimento da subcadeia de caracteres solicitada pode ser especificado.

### <a name="syntax"></a>Sintaxe
```
substring(source, startingIndex [, length])
```

### <a name="arguments"></a>Argumentos:

- `source`-A cadeia de caracteres de origem da qual a subcadeia de caracteres será retirada.
- `startingIndex`-A posição de caractere inicial com base em zero da subcadeia de caracteres solicitada.
- `length`-Um parâmetro opcional que pode ser usado para especificar o comprimento solicitado da subcadeia de caracteres retornada.

### <a name="examples"></a>Exemplos
```Kusto
print substring("abcdefg", 1, 2);   // result: "bc"
print substring("123456", 1);       // result: "23456"
print substring("123456", 2, 2);    // result: "34"
print substring("ABCD", 0, 2);  // result: "AB"
```


## <a name="tolower-toupper"></a>ToLower, ToUpper

Converte uma determinada cadeia de caracteres em todas as letras minúsculas ou maiúsculas.

### <a name="syntax"></a>Sintaxe
```
tolower("value")
toupper("value")
```

### <a name="examples"></a>Exemplos
```Kusto
print tolower("HELLO"); // result: "hello"
print toupper("hello"); // result: "HELLO"
```



## <a name="next-steps"></a>Passos seguintes
Continue com os tutoriais avançados:
* [Funções de agregação](aggregations.md)
* [Agregações avançadas](advanced-aggregations.md)
* [Gráficos e diagramas](charts.md)
* [Trabalhando com estruturas de dados e JSON](json-data-structures.md)
* [Gravação de consulta avançada](advanced-query-writing.md)
* [Junções – análise cruzada](joins.md)
