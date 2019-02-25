---
title: Trabalhar com cadeias de caracteres em consultas de registo do Azure Monitor | Documentos da Microsoft
description: Descreve como editar, comparar, procurar e realizar uma variedade de outras operações em cadeias de caracteres em consultas de registo do Azure Monitor.
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
ms.openlocfilehash: 4998084ed15e6ed455789689674e134c6cd9d0a8
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/24/2019
ms.locfileid: "56749771"
---
# <a name="work-with-strings-in-azure-monitor-log-queries"></a>Trabalhar com cadeias de caracteres em consultas de registo do Azure Monitor


> [!NOTE]
> Deve efetuar [introdução ao Log Analytics do Azure Monitor](get-started-portal.md) e [introdução às consultas de registo do Azure Monitor](get-started-queries.md) antes de concluir este tutorial.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Este artigo descreve como editar, comparar, procurar e realizar uma variedade de outras operações em cadeias de caracteres.

Cada caractere numa cadeia de caracteres tem um número de índice, de acordo com a localização. O primeiro caráter é no índice 0, o caráter seguinte é 1 e então, uma. Funções de cadeia de caracteres diferentes utilizam números de índice, conforme mostrado nas seções a seguir. Muitos dos exemplos seguintes utilizam o **imprimir** de comando para demonstrar a manipulação de cadeia de caracteres sem utilizar uma origem de dados específica.


## <a name="strings-and-escaping-them"></a>Cadeias de caracteres e carateres de escape-los
Valores de cadeia de caracteres são compactados com qualquer um deles com carateres de aspas simples ou duplas. Barra invertida (\) é utilizado para caracteres de escape para o caráter a seguir, como \t para o separador, \n para a nova linha, e \" o caráter de plica em si.

```Kusto
print "this is a 'string' literal in double \" quotes"
```

Para impedir a "\\"de atuar como um caráter de escape, adicionar"\@" como um prefixo para a cadeia de caracteres:

```Kusto
print @"C:\backslash\not\escaped\with @ prefix"
```


## <a name="string-comparisons"></a>Comparações de seqüência de caracteres

Operador       |Descrição                         |Diferencia maiúsculas de minúsculas|Exemplo (produz `true`)
---------------|------------------------------------|--------------|-----------------------
`==`           |Igual a                              |Sim           |`"aBc" == "aBc"`
`!=`           |Diferente de                          |Sim           |`"abc" != "ABC"`
`=~`           |Igual a                              |Não            |`"abc" =~ "ABC"`
`!~`           |Diferente de                          |Não            |`"aBc" !~ "xyz"`
`has`          |Direita lado é um termo todo no esquerdo lado |Não|`"North America" has "america"`
`!has`         |Direita lado não é um termo completo na esquerdo lado       |Não            |`"North America" !has "amer"` 
`has_cs`       |Direita lado é um termo todo no esquerdo lado |Sim|`"North America" has_cs "America"`
`!has_cs`      |Direita lado não é um termo completo na esquerdo lado       |Sim            |`"North America" !has_cs "amer"` 
`hasprefix`    |Direita lado é um prefixo de termo no esquerdo lado         |Não            |`"North America" hasprefix "ame"`
`!hasprefix`   |Direita lado não é um prefixo de termo no esquerdo lado     |Não            |`"North America" !hasprefix "mer"` 
`hasprefix_cs`    |Direita lado é um prefixo de termo no esquerdo lado         |Sim            |`"North America" hasprefix_cs "Ame"`
`!hasprefix_cs`   |Direita lado não é um prefixo de termo no esquerdo lado     |Sim            |`"North America" !hasprefix_cs "CA"` 
`hassuffix`    |Direita lado é um sufixo de termo em esquerdo lado         |Não            |`"North America" hassuffix "ica"`
`!hassuffix`   |Direita lado não é um sufixo de termo em esquerdo lado     |Não            |' "América do Norte"! hassuffix "americ"
`hassuffix_cs`    |Direita lado é um sufixo de termo em esquerdo lado         |Sim            |`"North America" hassuffix_cs "ica"`
`!hassuffix_cs`   |Direita lado não é um sufixo de termo em esquerdo lado     |Sim            |' "América do Norte"! hassuffix_cs "icA"
`contains`     |Direita lado ocorre como uma subsequente de esquerdo lado  |Não            |`"FabriKam" contains "BRik"`
`!contains`    |Direita lado não ocorre em esquerdo lado           |Não            |`"Fabrikam" !contains "xyz"`
`contains_cs`   |Direita lado ocorre como uma subsequente de esquerdo lado  |Sim           |`"FabriKam" contains_cs "Kam"`
`!contains_cs`  |Direita lado não ocorre em esquerdo lado           |Sim           |`"Fabrikam" !contains_cs "Kam"`
`startswith`   |Direita lado é uma subsequente inicial de esquerdo lado|Não            |`"Fabrikam" startswith "fab"`
`!startswith`  |Direita lado não é um subsequente inicial de esquerdo lado|Não        |`"Fabrikam" !startswith "kam"`
`startswith_cs`   |Direita lado é uma subsequente inicial de esquerdo lado|Sim            |`"Fabrikam" startswith_cs "Fab"`
`!startswith_cs`  |Direita lado não é um subsequente inicial de esquerdo lado|Sim        |`"Fabrikam" !startswith_cs "fab"`
`endswith`     |Direita lado é uma subsequente de fecho de esquerdo lado|Não             |`"Fabrikam" endswith "Kam"`
`!endswith`    |Direita lado não é um subsequente de fecho de esquerdo lado|Não         |`"Fabrikam" !endswith "brik"`
`endswith_cs`     |Direita lado é uma subsequente de fecho de esquerdo lado|Sim             |`"Fabrikam" endswith "Kam"`
`!endswith_cs`    |Direita lado não é um subsequente de fecho de esquerdo lado|Sim         |`"Fabrikam" !endswith "brik"`
`matches regex`|esquerdo lado contém uma correspondência para a direita lado        |Sim           |`"Fabrikam" matches regex "b.*k"`
`in`           |É igual a para um dos elementos       |Sim           |`"abc" in ("123", "345", "abc")`
`!in`          |Não é igual a qualquer um dos elementos   |Sim           |`"bca" !in ("123", "345", "abc")`


## <a name="countof"></a>countof

Contagem de ocorrências de uma subcadeia numa cadeia de caracteres. Pode corresponder a cadeias de caracteres sem formatação ou utilizar a regex. Correspondências de seqüência de caracteres simples se possam sobrepor enquanto não correspondências de regex.

### <a name="syntax"></a>Sintaxe
```
countof(text, search [, kind])
```

### <a name="arguments"></a>Argumentos:
- `text` -A cadeia de entrada 
- `search` -Seqüência de caracteres simples ou uma expressão regular para corresponder ao dentro de texto.
- `kind` - _normal_ | _regex_ (predefinição: normal).

### <a name="returns"></a>Devolve

O número de vezes que a cadeia de pesquisa que possa ter correspondente no contentor. Correspondências de seqüência de caracteres simples podem sobrepor-se ao regex correspondências contrário.

### <a name="examples"></a>Exemplos

#### <a name="plain-string-matches"></a>Correspondências de cadeia de caracteres sem formatação

```Kusto
print countof("The cat sat on the mat", "at");  //result: 3
print countof("aaa", "a");  //result: 3
print countof("aaaa", "aa");  //result: 3 (not 2!)
print countof("ababa", "ab", "normal");  //result: 2
print countof("ababa", "aba");  //result: 2
```

#### <a name="regex-matches"></a>Correspondências de RegEx

```Kusto
print countof("The cat sat on the mat", @"\b.at\b", "regex");  //result: 3
print countof("ababa", "aba", "regex");  //result: 1
print countof("abcabc", "a.c", "regex");  // result: 2
```


## <a name="extract"></a>Extrair

Obtém uma correspondência para uma expressão regular de uma determinada seqüência de caracteres. Opcionalmente, também converte o extraídas de subcadeia o tipo especificado.

### <a name="syntax"></a>Sintaxe

```Kusto
extract(regex, captureGroup, text [, typeLiteral])
```

### <a name="arguments"></a>Argumentos

- `regex` -Uma expressão regular.
- `captureGroup` -Uma constante de número inteiro positivo que indica o grupo de captura para extrair. 0 para a correspondência de toda, 1 para o valor correspondido pela primeira '(' Parêntese')' na expressão regular, 2 ou mais para parênteses subsequentes.
- `text` -Uma cadeia de caracteres para pesquisar.
- `typeLiteral` -Um literal de tipo opcional (por exemplo, typeof(long)). Se for fornecido, a subcadeia extraída é convertida para este tipo.

### <a name="returns"></a>Devolve
A subcadeia comparada com captureGroup de grupo captura indicado, opcionalmente, convertido para typeLiteral.
Se não há nenhuma correspondência ou a conversão de tipo falhar, vrátit hodnotu null.

### <a name="examples"></a>Exemplos

O exemplo seguinte extrai o último octeto dos *ComputerIP* de um registo de heartbeat:
```Kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| project ComputerIP, last_octet=extract("([0-9]*$)", 1, ComputerIP) 
```

O exemplo seguinte extrai o último octeto, converte-o para um *real* escreva (número) e calcula o seguinte valor IP
```Kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| extend last_octet=extract("([0-9]*$)", 1, ComputerIP, typeof(real)) 
| extend next_ip=(last_octet+1)%255
| project ComputerIP, last_octet, next_ip
```

No exemplo abaixo, a cadeia de caracteres *rastreio* é pesquisada para obter uma definição de "Duração". A correspondência é convertida em *reais* e multiplicado por uma constante de tempo (1 s) *que converte a duração para o tipo de período de tempo*.
```Kusto
let Trace="A=12, B=34, Duration=567, ...";
print Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real));  //result: 567
print Duration_seconds =  extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s);  //result: 00:09:27
```


## <a name="isempty-isnotempty-notempty"></a>IsEmpty isnotempty, notempty

- *IsEmpty* retorna true se o argumento é uma cadeia vazia ou null (Consulte também *isnull*).
- *isnotempty* retorna true se o argumento não é uma cadeia vazia ou um valor nulo (Consulte também *isnotnull*). alias: *notempty*.

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

Divide um URL em partes (protocolo, anfitrião, porta, etc.) e retorna um objeto de dicionário que contém as partes como cadeias de caracteres.

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


## <a name="replace"></a>substituir

Substitui todas as correspondências de regex por outra cadeia. 

### <a name="syntax"></a>Sintaxe

```
replace(regex, rewrite, input_text)
```

### <a name="arguments"></a>Argumentos

- `regex` -A expressão regular para corresponder ao. Pode conter grupos de captura parênteses' (' ')'.
- `rewrite` -O regex de substituição para qualquer correspondência feita pelo regex correspondente. Utilize \0 para fazer referência a correspondência de toda, \1 para o primeiro grupo de captura, \2, e assim por diante para grupos de captura subseqüente.
- `input_text` -A cadeia de entrada para procurar no.

### <a name="returns"></a>Devolve
O texto depois de substituir todas as correspondências de regex com avaliações de regravação. Correspondências não se sobrepõem.

### <a name="examples"></a>Exemplos

```Kusto
SecurityEvent
| take 1
| project Activity 
| extend replaced = replace(@"(\d+) -", @"Activity ID \1: ", Activity) 
```

Pode ter os seguintes resultados:
Atividade                                        |substituído
------------------------------------------------|----------------------------------------------------------
4663 - foi efetuada uma tentativa de acessar um objeto  |ID de atividade 4663: Foi efetuada uma tentativa de acessar um objeto.


## <a name="split"></a>dividir

Divide uma determinada seqüência de caracteres, de acordo com um delimitador especificado e retorna uma matriz de subcadeias de carateres resultantes.

### <a name="syntax"></a>Sintaxe
```
split(source, delimiter [, requestedIndex])
```

### <a name="arguments"></a>Argumentos:

- `source` -A cadeia a dividir, de acordo com o delimitador especificado.
- `delimiter` -O delimitador que será utilizado para dividir a cadeia de caracteres de origem.
- `requestedIndex` -Um índice baseado em zero opcional. Se for fornecido, a matriz de cadeia devolvida irá conter apenas esse item (se existir).


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

Concatena argumentos de cadeia de caracteres (argumentos de suporta 1-16).

### <a name="syntax"></a>Sintaxe
```
strcat("string1", "string2", "string3")
```

### <a name="examples"></a>Exemplos
```Kusto
print strcat("hello", " ", "world") // result: "hello world"
```


## <a name="strlen"></a>strlen

Devolve o comprimento de uma cadeia de caracteres.

### <a name="syntax"></a>Sintaxe
```
strlen("text_to_evaluate")
```

### <a name="examples"></a>Exemplos
```Kusto
print strlen("hello")   // result: 5
```


## <a name="substring"></a>subcadeia

Extrai uma subcadeia de uma cadeia de caracteres de origem específica, começando no índice especificado. Opcionalmente, pode ser especificado o comprimento da subcadeia pedida.

### <a name="syntax"></a>Sintaxe
```
substring(source, startingIndex [, length])
```

### <a name="arguments"></a>Argumentos:

- `source` -A cadeia de origem a subcadeia será retirada da.
- `startingIndex` -A com base em zero caractere posição inicial da subseqüência pedida.
- `length` -Um parâmetro opcional que pode ser utilizado para especificar o tamanho pedido da subseqüência retornado.

### <a name="examples"></a>Exemplos
```Kusto
print substring("abcdefg", 1, 2);   // result: "bc"
print substring("123456", 1);       // result: "23456"
print substring("123456", 2, 2);    // result: "34"
print substring("ABCD", 0, 2);  // result: "AB"
```


## <a name="tolower-toupper"></a>tolower, toupper

Converte uma determinada seqüência de caracteres para todos os inferior ou maiúsculas.

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



## <a name="next-steps"></a>Passos Seguintes
Continue com os Tutoriais avançados:
* [Funções de agregação](aggregations.md)
* [Agregações avançadas](advanced-aggregations.md)
* [Gráficos e diagramas](charts.md)
* [Trabalhar com estruturas de dados e JSON](json-data-structures.md)
* [Escrita de consultas avançado](advanced-query-writing.md)
* [Associações - cruzada da análise](joins.md)
