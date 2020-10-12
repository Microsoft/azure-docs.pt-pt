---
title: Trabalhe com cordas em consultas de log do Azure Monitor Microsoft Docs
description: Descreve como editar, comparar, pesquisar e executar uma variedade de outras operações em cadeias em consultas de log do Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: a394fee7178b2e3e167c8bd905ab175b25d1d813
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "75397473"
---
# <a name="work-with-strings-in-azure-monitor-log-queries"></a>Trabalhe com cordas em consultas de log do Azure Monitor


> [!NOTE]
> Deve completar [Começar com a Azure Monitor Log Analytics](get-started-portal.md) e começar com as consultas de registo do [Azure Monitor](get-started-queries.md) antes de concluir este tutorial.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Este artigo descreve como editar, comparar, pesquisar e realizar uma variedade de outras operações em cordas.

Cada personagem numa cadeia tem um número de índice, de acordo com a sua localização. O primeiro caractere está no índice 0, o próximo caracter é 1, e assim por diante. Diferentes funções de cadeia utilizam números de índice como mostrado nas secções seguintes. Muitos dos exemplos seguintes usam o comando **de impressão** para demonstrar manipulação de cordas sem usar uma fonte de dados específica.


## <a name="strings-and-escaping-them"></a>Cordas e escapando-lhes
Os valores das cordas são embrulhados com caracteres de citação única ou dupla. Backslash \\ () é usado para escapar de caracteres para o personagem que o segue, como \t para aba, \n para newline, e \" o próprio personagem de citação.

```Kusto
print "this is a 'string' literal in double \" quotes"
```

```Kusto
print 'this is a "string" literal in single \' quotes'
```

Para evitar que \\ " " aja como um personagem de fuga, adicione \@ " como um prefixo à corda:

```Kusto
print @"C:\backslash\not\escaped\with @ prefix"
```


## <a name="string-comparisons"></a>Comparações de cordas

Operador       |Descrição                         |Case-Sensitive|Exemplo `true` (rendimentos)
---------------|------------------------------------|--------------|-----------------------
`==`           |Igual a                              |Sim           |`"aBc" == "aBc"`
`!=`           |Não é igual                          |Sim           |`"abc" != "ABC"`
`=~`           |Igual a                              |Não            |`"abc" =~ "ABC"`
`!~`           |Não é igual                          |Não            |`"aBc" !~ "xyz"`
`has`          |O lado direito é um termo inteiro no lado esquerdo |Não|`"North America" has "america"`
`!has`         |O lado direito não é um termo completo no lado esquerdo       |Não            |`"North America" !has "amer"` 
`has_cs`       |O lado direito é um termo inteiro no lado esquerdo |Sim|`"North America" has_cs "America"`
`!has_cs`      |O lado direito não é um termo completo no lado esquerdo       |Sim            |`"North America" !has_cs "amer"` 
`hasprefix`    |O lado direito é um prefixo de termo no lado esquerdo         |Não            |`"North America" hasprefix "ame"`
`!hasprefix`   |O lado direito não é um prefixo de termo no lado esquerdo     |Não            |`"North America" !hasprefix "mer"` 
`hasprefix_cs`    |O lado direito é um prefixo de termo no lado esquerdo         |Sim            |`"North America" hasprefix_cs "Ame"`
`!hasprefix_cs`   |O lado direito não é um prefixo de termo no lado esquerdo     |Sim            |`"North America" !hasprefix_cs "CA"` 
`hassuffix`    |O lado direito é um sufixo de termo no lado esquerdo         |Não            |`"North America" hassuffix "ica"`
`!hassuffix`   |O lado direito não é um sufixo de termo no lado esquerdo     |Não            |`"North America" !hassuffix "americ"`
`hassuffix_cs`    |O lado direito é um sufixo de termo no lado esquerdo         |Sim            |`"North America" hassuffix_cs "ica"`
`!hassuffix_cs`   |O lado direito não é um sufixo de termo no lado esquerdo     |Sim            |`"North America" !hassuffix_cs "icA"`
`contains`     |O lado direito ocorre como uma subsequância do lado esquerdo  |Não            |`"FabriKam" contains "BRik"`
`!contains`    |O lado direito não ocorre no lado esquerdo           |Não            |`"Fabrikam" !contains "xyz"`
`contains_cs`   |O lado direito ocorre como uma subsequância do lado esquerdo  |Sim           |`"FabriKam" contains_cs "Kam"`
`!contains_cs`  |O lado direito não ocorre no lado esquerdo           |Sim           |`"Fabrikam" !contains_cs "Kam"`
`startswith`   |O lado direito é uma subsequância inicial do lado esquerdo|Não            |`"Fabrikam" startswith "fab"`
`!startswith`  |O lado direito não é uma subsequância inicial do lado esquerdo|Não        |`"Fabrikam" !startswith "kam"`
`startswith_cs`   |O lado direito é uma subsequância inicial do lado esquerdo|Sim            |`"Fabrikam" startswith_cs "Fab"`
`!startswith_cs`  |O lado direito não é uma subsequância inicial do lado esquerdo|Sim        |`"Fabrikam" !startswith_cs "fab"`
`endswith`     |O lado direito é uma subsequância de fecho do lado esquerdo|Não             |`"Fabrikam" endswith "Kam"`
`!endswith`    |O lado direito não é uma subsequância de fecho do lado esquerdo|Não         |`"Fabrikam" !endswith "brik"`
`endswith_cs`     |O lado direito é uma subsequância de fecho do lado esquerdo|Sim             |`"Fabrikam" endswith "Kam"`
`!endswith_cs`    |O lado direito não é uma subsequância de fecho do lado esquerdo|Sim         |`"Fabrikam" !endswith "brik"`
`matches regex`|lado esquerdo contém uma correspondência para o lado direito        |Sim           |`"Fabrikam" matches regex "b.*k"`
`in`           |É igual a um dos elementos       |Sim           |`"abc" in ("123", "345", "abc")`
`!in`          |Não é igual a nenhum dos elementos   |Sim           |`"bca" !in ("123", "345", "abc")`


## <a name="countof"></a>contagem de

Conta ocorrências de um sub-cordão numa corda. Pode combinar cordas simples ou usar regex. Os fósforos de cordas simples podem sobrepor-se enquanto os jogos da Regex não.

### <a name="syntax"></a>Syntax
```
countof(text, search [, kind])
```

### <a name="arguments"></a>Argumentos:
- `text` - A cadeia de entrada 
- `search` - Corda simples ou expressão regular para combinar com texto interno.
- `kind` - _normal_  |  _regex_ (predefinição: normal).

### <a name="returns"></a>Devoluções

O número de vezes que a cadeia de pesquisa pode ser igualada no recipiente. Os fósforos de cordas simples podem sobrepor-se enquanto os jogos da Regex não.

### <a name="examples"></a>Exemplos

#### <a name="plain-string-matches"></a>Combinam com cordas simples

```Kusto
print countof("The cat sat on the mat", "at");  //result: 3
print countof("aaa", "a");  //result: 3
print countof("aaaa", "aa");  //result: 3 (not 2!)
print countof("ababa", "ab", "normal");  //result: 2
print countof("ababa", "aba");  //result: 2
```

#### <a name="regex-matches"></a>Jogos do Regex

```Kusto
print countof("The cat sat on the mat", @"\b.at\b", "regex");  //result: 3
print countof("ababa", "aba", "regex");  //result: 1
print countof("abcabc", "a.c", "regex");  // result: 2
```


## <a name="extract"></a>extrato

Obtém uma correspondência para uma expressão regular de uma determinada corda. Opcionalmente também converte o sublmotor extraído para o tipo especificado.

### <a name="syntax"></a>Sintaxe

```Kusto
extract(regex, captureGroup, text [, typeLiteral])
```

### <a name="arguments"></a>Argumentos

- `regex` - Uma expressão regular.
- `captureGroup` - Uma constante de inteiros positivo que indica o grupo de captura a extrair. 0 para todo o jogo, 1 pelo valor combinado pelo primeiro 'parênteses')' na expressão regular, 2 ou mais para parênteses subsequentes.
- `text` - Uma corda para procurar.
- `typeLiteral` - Um tipo opcional literal (por exemplo, tipo de (longo)). Se fornecido, o sublpetor extraído é convertido para este tipo.

### <a name="returns"></a>Devoluções
O sub-cordão corresponde ao grupo de captura de captura indicado, opcionalmente convertido em typeLiteral.
Se não houver correspondência, ou o tipo de conversão falhar, devolva nulo.

### <a name="examples"></a>Exemplos

O exemplo a seguir extrai o último octeto de *ComputerIP* de um registo de batimentos cardíacos:
```Kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| project ComputerIP, last_octet=extract("([0-9]*$)", 1, ComputerIP) 
```

O exemplo a seguir extrai o último octeto, lança-o para um tipo *real* (número) e calcula o próximo valor IP
```Kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| extend last_octet=extract("([0-9]*$)", 1, ComputerIP, typeof(real)) 
| extend next_ip=(last_octet+1)%255
| project ComputerIP, last_octet, next_ip
```

No exemplo abaixo, o *traço de* corda é procurado por uma definição de "Duração". A partida é lançada ao *real* e multiplicada por uma constante de tempo (1 s) *que lança a Duração para o intervalo de tempos do tipo*.
```Kusto
let Trace="A=12, B=34, Duration=567, ...";
print Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real));  //result: 567
print Duration_seconds =  extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s);  //result: 00:09:27
```


## <a name="isempty-isnotempty-notempty"></a>isempty, isnotempty, notampty

- *isempty* retorna verdadeiro se o argumento é uma corda vazia ou nula (ver também *isnull*).
- *isnotempty* retorna verdadeiro se o argumento não é uma corda vazia ou um nulo (ver também *énonull).* pseudónimo: *nota.*

### <a name="syntax"></a>Syntax

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

Divide um URL nas suas partes (protocolo, hospedeiro, porta, etc.) e devolve um objeto dicionário que contém as peças como cordas.

### <a name="syntax"></a>Syntax

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

Substitui todos os jogos regex por outra corda. 

### <a name="syntax"></a>Sintaxe

```
replace(regex, rewrite, input_text)
```

### <a name="arguments"></a>Argumentos

- `regex` - A expressão regular para combinar. Pode conter grupos de captura em '('parênteses')'.
- `rewrite` - O regex de substituição para qualquer partida feita por regex correspondente. Utilize \0 para se referir a toda a correspondência, \1 para o primeiro grupo de captura, \2, e assim por diante para grupos de captura subsequentes.
- `input_text` - A cadeia de entrada para procurar.

### <a name="returns"></a>Devoluções
O texto após a substituição de todos os jogos do regex por avaliações de reescrita. Os fósforos não se sobrepõem.

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

Divide uma determinada corda de acordo com um delimiter especificado, e devolve uma matriz das sublufações resultantes.

### <a name="syntax"></a>Syntax
```
split(source, delimiter [, requestedIndex])
```

### <a name="arguments"></a>Argumentos:

- `source` - A corda a ser dividida de acordo com o delimiter especificado.
- `delimiter` - O declimiter que será usado para dividir a cadeia de origem.
- `requestedIndex` - Um índice opcional baseado em zero. Se for fornecida, a matriz de corda devolvida apenas manterá esse item (se existir).


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

Concatenates argumentos de corda (suporta 1-16 argumentos).

### <a name="syntax"></a>Syntax
```
strcat("string1", "string2", "string3")
```

### <a name="examples"></a>Exemplos
```Kusto
print strcat("hello", " ", "world") // result: "hello world"
```


## <a name="strlen"></a>strlen

Devolve o comprimento de uma corda.

### <a name="syntax"></a>Syntax
```
strlen("text_to_evaluate")
```

### <a name="examples"></a>Exemplos
```Kusto
print strlen("hello")   // result: 5
```


## <a name="substring"></a>substring

Extrai um sublaminho de uma determinada cadeia de origem, a partir do índice especificado. Opcionalmente, o comprimento do sublpeso solicitado pode ser especificado.

### <a name="syntax"></a>Syntax
```
substring(source, startingIndex [, length])
```

### <a name="arguments"></a>Argumentos:

- `source` - A cadeia de origem de onde o sub-adcões será retirado.
- `startingIndex` - A posição de caracteres initenta da sub-formação solicitada.
- `length` - Um parâmetro opcional que pode ser utilizado para especificar o comprimento solicitado do sublmotor devolvido.

### <a name="examples"></a>Exemplos
```Kusto
print substring("abcdefg", 1, 2);   // result: "bc"
print substring("123456", 1);       // result: "23456"
print substring("123456", 2, 2);    // result: "34"
print substring("ABCD", 0, 2);  // result: "AB"
```


## <a name="tolower-toupper"></a>tolower, toupper

Converte uma corda dada a todos os casos inferiores ou superiores.

### <a name="syntax"></a>Syntax
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
* [Joins - análise cruzada](joins.md)
