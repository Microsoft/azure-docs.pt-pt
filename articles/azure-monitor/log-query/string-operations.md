---
title: Trabalhe com cordas em consultas de registo do Monitor Azure [ Microsoft Docs
description: Descreve como editar, comparar, pesquisar e realizar uma variedade de outras operações em cordas em consultas de registo do Monitor Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: a394fee7178b2e3e167c8bd905ab175b25d1d813
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75397473"
---
# <a name="work-with-strings-in-azure-monitor-log-queries"></a>Trabalhe com cordas em consultas de registo do Monitor Azure


> [!NOTE]
> Deve completar [O Get started com o Azure Monitor Log Analytics](get-started-portal.md) e começar com consultas de registo do [Azure Monitor](get-started-queries.md) antes de concluir este tutorial.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Este artigo descreve como editar, comparar, pesquisar e realizar uma variedade de outras operações em cordas.

Cada personagem numa corda tem um número de índice, de acordo com a sua localização. O primeiro personagem está no índice 0, o próximo personagem é 1, e assim por diante. Funções de cadeia diferentes utilizam números de índice como mostrado nas seguintes secções. Muitos dos seguintes exemplos usam o comando **de impressão** para demonstrar manipulação de cordas sem usar uma fonte de dados específica.


## <a name="strings-and-escaping-them"></a>Cordas e escapando-as
Os valores das cordas são embrulhados com caracteres de cotação simples ou dupla. Backslash\\é usado para escapar de personagens para o personagem que o segue, \" como \t para tab, \n para newline, e o próprio personagem de citação.

```Kusto
print "this is a 'string' literal in double \" quotes"
```

```Kusto
print 'this is a "string" literal in single \' quotes'
```

Para evitar\\que " " de agir\@como um personagem de fuga, adicione " como um prefixo para a corda:

```Kusto
print @"C:\backslash\not\escaped\with @ prefix"
```


## <a name="string-comparisons"></a>Comparações de cordas

Operador       |Descrição                         |Sensível a casos|Exemplo (rendimentos) `true`
---------------|------------------------------------|--------------|-----------------------
`==`           |É igual a                              |Sim           |`"aBc" == "aBc"`
`!=`           |Não é igual                          |Sim           |`"abc" != "ABC"`
`=~`           |É igual a                              |Não            |`"abc" =~ "ABC"`
`!~`           |Não é igual                          |Não            |`"aBc" !~ "xyz"`
`has`          |Lado direito é um termo inteiro no lado esquerdo |Não|`"North America" has "america"`
`!has`         |Lado direito não é um termo completo no lado esquerdo       |Não            |`"North America" !has "amer"` 
`has_cs`       |Lado direito é um termo inteiro no lado esquerdo |Sim|`"North America" has_cs "America"`
`!has_cs`      |Lado direito não é um termo completo no lado esquerdo       |Sim            |`"North America" !has_cs "amer"` 
`hasprefix`    |Lado direito é um termo prefixo no lado esquerdo         |Não            |`"North America" hasprefix "ame"`
`!hasprefix`   |Lado direito não é um termo prefixo no lado esquerdo     |Não            |`"North America" !hasprefix "mer"` 
`hasprefix_cs`    |Lado direito é um termo prefixo no lado esquerdo         |Sim            |`"North America" hasprefix_cs "Ame"`
`!hasprefix_cs`   |Lado direito não é um termo prefixo no lado esquerdo     |Sim            |`"North America" !hasprefix_cs "CA"` 
`hassuffix`    |Lado direito é um termo sufixo no lado esquerdo         |Não            |`"North America" hassuffix "ica"`
`!hassuffix`   |Lado direito não é um termo sufixo no lado esquerdo     |Não            |`"North America" !hassuffix "americ"`
`hassuffix_cs`    |Lado direito é um termo sufixo no lado esquerdo         |Sim            |`"North America" hassuffix_cs "ica"`
`!hassuffix_cs`   |Lado direito não é um termo sufixo no lado esquerdo     |Sim            |`"North America" !hassuffix_cs "icA"`
`contains`     |O lado direito ocorre como uma subsequência do lado esquerdo  |Não            |`"FabriKam" contains "BRik"`
`!contains`    |O lado direito não ocorre no lado esquerdo           |Não            |`"Fabrikam" !contains "xyz"`
`contains_cs`   |O lado direito ocorre como uma subsequência do lado esquerdo  |Sim           |`"FabriKam" contains_cs "Kam"`
`!contains_cs`  |O lado direito não ocorre no lado esquerdo           |Sim           |`"Fabrikam" !contains_cs "Kam"`
`startswith`   |Lado direito é uma subsequência inicial do lado esquerdo|Não            |`"Fabrikam" startswith "fab"`
`!startswith`  |O lado direito não é uma subsequência inicial do lado esquerdo|Não        |`"Fabrikam" !startswith "kam"`
`startswith_cs`   |Lado direito é uma subsequência inicial do lado esquerdo|Sim            |`"Fabrikam" startswith_cs "Fab"`
`!startswith_cs`  |O lado direito não é uma subsequência inicial do lado esquerdo|Sim        |`"Fabrikam" !startswith_cs "fab"`
`endswith`     |Lado direito é uma subsequência de fecho do lado esquerdo|Não             |`"Fabrikam" endswith "Kam"`
`!endswith`    |O lado direito não é uma subsequência de fecho do lado esquerdo|Não         |`"Fabrikam" !endswith "brik"`
`endswith_cs`     |Lado direito é uma subsequência de fecho do lado esquerdo|Sim             |`"Fabrikam" endswith "Kam"`
`!endswith_cs`    |O lado direito não é uma subsequência de fecho do lado esquerdo|Sim         |`"Fabrikam" !endswith "brik"`
`matches regex`|O lado esquerdo contém uma partida para o lado direito        |Sim           |`"Fabrikam" matches regex "b.*k"`
`in`           |Igual a um dos elementos       |Sim           |`"abc" in ("123", "345", "abc")`
`!in`          |Não é igual a nenhum dos elementos   |Sim           |`"bca" !in ("123", "345", "abc")`


## <a name="countof"></a>condede

Conta as ocorrências de um substring numa corda. Pode combinar cordas simples ou usar regex. Os fósforos de corda simples podem sobrepor-se enquanto os fósforos de regex não.

### <a name="syntax"></a>Sintaxe
```
countof(text, search [, kind])
```

### <a name="arguments"></a>Argumentos:
- `text`- A corda de entrada 
- `search`- Corda simples ou expressão regular para combinar texto interno.
- `kind` - _normal_ | _regex_ normal (padrão: normal).

### <a name="returns"></a>Devolve

O número de vezes que a cadeia de pesquisa pode ser igualada no recipiente. Os fósforos de corda simples podem sobrepor-se enquanto os fósforos de regex não.

### <a name="examples"></a>Exemplos

#### <a name="plain-string-matches"></a>Fósforos de corda simples

```Kusto
print countof("The cat sat on the mat", "at");  //result: 3
print countof("aaa", "a");  //result: 3
print countof("aaaa", "aa");  //result: 3 (not 2!)
print countof("ababa", "ab", "normal");  //result: 2
print countof("ababa", "aba");  //result: 2
```

#### <a name="regex-matches"></a>Jogos regex

```Kusto
print countof("The cat sat on the mat", @"\b.at\b", "regex");  //result: 3
print countof("ababa", "aba", "regex");  //result: 1
print countof("abcabc", "a.c", "regex");  // result: 2
```


## <a name="extract"></a>extrair

Obtém uma correspondência para uma expressão regular de uma determinada corda. Opcionalmente também converte a subcadeia extraída para o tipo especificado.

### <a name="syntax"></a>Sintaxe

```Kusto
extract(regex, captureGroup, text [, typeLiteral])
```

### <a name="arguments"></a>Argumentos

- `regex`- Uma expressão regular.
- `captureGroup`- Uma constante de inteiro positivo indicando o grupo de captura para extrair. 0 para todo o jogo, 1 pelo valor igualado pelo primeiro 'parênteses')' na expressão regular, 2 ou mais para os parênteses subsequentes.
- `text`- Uma corda para procurar.
- `typeLiteral`- Um tipo literal de tipo opcional (por exemplo, tipo (longo)). Se fornecido, o substring extraído é convertido para este tipo.

### <a name="returns"></a>Devolve
O substring combinado com o grupo de captura indicado capturaGroup, opcionalmente convertido para tipoLiteral.
Se não houver correspondência, ou se a conversão do tipo falhar, volte a ser nula.

### <a name="examples"></a>Exemplos

O exemplo que se segue extrai o último octeto de *ComputerIP* de um registo de batimentos cardíacos:
```Kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| project ComputerIP, last_octet=extract("([0-9]*$)", 1, ComputerIP) 
```

O exemplo seguinte extrai o último octeto, lança-o para um tipo *real* (número) e calcula o próximo valor IP
```Kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| extend last_octet=extract("([0-9]*$)", 1, ComputerIP, typeof(real)) 
| extend next_ip=(last_octet+1)%255
| project ComputerIP, last_octet, next_ip
```

No exemplo abaixo, o *trace de* cadeia é procurado por uma definição de "Duração". A partida é lançada para *real* e multiplicada por uma constante de tempo (1 s) *que lança duração para escrever a hora de tempo*.
```Kusto
let Trace="A=12, B=34, Duration=567, ...";
print Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real));  //result: 567
print Duration_seconds =  extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s);  //result: 00:09:27
```


## <a name="isempty-isnotempty-notempty"></a>é vazio, não é vazio, não vazio

- *isempty* returns true if the argument is a empty string or null (ver também *é nulo).*
- *isnotempty* returns true if the argument not is a empty string or a null (ver também *é não nulo).* pseudónimo: *não vazio.*

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

Divide um URL nas suas partes (protocolo, anfitrião, porta, etc.), e devolve um objeto dicionário contendo as peças como cordas.

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

Substitui todos os fósforos regex por outra corda. 

### <a name="syntax"></a>Sintaxe

```
replace(regex, rewrite, input_text)
```

### <a name="arguments"></a>Argumentos

- `regex`- A expressão regular para combinar. Pode conter grupos de captura em 'parheses')».
- `rewrite`- O regex de substituição para qualquer partida feita por regex correspondente. Use \0 para se referir a toda a partida, \1 para o primeiro grupo de captura, \2, e assim por diante para grupos de captura subsequentes.
- `input_text`- A corda de entrada para procurar.

### <a name="returns"></a>Devolve
O texto após a substituição de todos os jogos de regex por avaliações de reescrita. Os fósforos não se sobrepõem.

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
4663 - Foi feita uma tentativa de acesso a um objeto  |ID de atividade 4663: Foi feita uma tentativa de acesso a um objeto.


## <a name="split"></a>dividir

Divide uma determinada corda de acordo com um delimitador especificado e devolve uma série das subcordas resultantes.

### <a name="syntax"></a>Sintaxe
```
split(source, delimiter [, requestedIndex])
```

### <a name="arguments"></a>Argumentos:

- `source`- A corda a dividir de acordo com o delimitador especificado.
- `delimiter`- O delimitador que será utilizado para dividir a cadeia de origem.
- `requestedIndex`- Um índice opcional de base zero. Se fornecido, a matriz de cordas devolvida suporá apenas esse item (se existir).


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

Concatenates argumentos de cordas (apoia 1-16 argumentos).

### <a name="syntax"></a>Sintaxe
```
strcat("string1", "string2", "string3")
```

### <a name="examples"></a>Exemplos
```Kusto
print strcat("hello", " ", "world") // result: "hello world"
```


## <a name="strlen"></a>strlen

Devolve o comprimento de uma corda.

### <a name="syntax"></a>Sintaxe
```
strlen("text_to_evaluate")
```

### <a name="examples"></a>Exemplos
```Kusto
print strlen("hello")   // result: 5
```


## <a name="substring"></a>substring

Extrai um substring a partir de uma determinada cadeia de origem, a partir do índice especificado. Opcionalmente, o comprimento do substring solicitado pode ser especificado.

### <a name="syntax"></a>Sintaxe
```
substring(source, startingIndex [, length])
```

### <a name="arguments"></a>Argumentos:

- `source`- A cadeia de origem da de onde o substring será retirado.
- `startingIndex`- A posição de carácter inicial de base zero do substring solicitado.
- `length`- Um parâmetro opcional que pode ser utilizado para especificar o comprimento solicitado do substring devolvido.

### <a name="examples"></a>Exemplos
```Kusto
print substring("abcdefg", 1, 2);   // result: "bc"
print substring("123456", 1);       // result: "23456"
print substring("123456", 2, 2);    // result: "34"
print substring("ABCD", 0, 2);  // result: "AB"
```


## <a name="tolower-toupper"></a>mais lento, toia

Converte uma determinada corda em todos os casos inferiores ou superiores.

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
* [Trabalhar com a JSON e estruturas de dados](json-data-structures.md)
* [Escrita de consulta avançada](advanced-query-writing.md)
* [Junta-se - análise cruzada](joins.md)
