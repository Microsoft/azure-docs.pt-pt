---
title: Método de interpretação – API de Conhecimento Acadêmico
titlesuffix: Azure Cognitive Services
description: Use o método interpret para retornar as interpretações formatadas de cadeias de caracteres de consulta do usuário com base em dados de grafo acadêmicos e na gramática acadêmica nos serviços cognitivas da Microsoft.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: d960aff109e0eca70cb87463770620093e563f63
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706680"
---
# <a name="interpret-method"></a>Método interpret

A API REST de **interpretação** usa uma cadeia de caracteres de consulta do usuário final (ou seja, uma consulta inserida por um usuário do seu aplicativo) e retorna as interpretações formatadas da intenção do usuário com base nos dados do grafo acadêmico e na gramática acadêmica.

Para fornecer uma experiência interativa, você pode chamar esse método repetidamente após cada caractere digitado pelo usuário. Nesse caso, você deve definir o parâmetro **Complete** como 1 para habilitar sugestões de preenchimento automático. Se seu aplicativo não precisar de preenchimento automático, você deverá definir o parâmetro **Complete** como 0.

**Ponto de extremidade REST:**

    https://westus.api.cognitive.microsoft.com/academic/v1.0/interpret?

## <a name="request-parameters"></a>Parâmetros do Pedido

Nome     | Value | Obrigatório?  | Descrição
---------|---------|---------|---------
**query**    | Cadeia de texto | Sim | Consulta inserida pelo usuário.  Se complete for definido como 1, a consulta será interpretada como um prefixo para gerar sugestões de preenchimento automático de consulta.        
**deprecia**    | Cadeia de texto | Não  | Nome do modelo que você deseja consultar.  Atualmente, o valor padrão é o *mais recente*.        
**complete** | 0 ou 1 | Não<br>padrão: 0  | 1 significa que as sugestões de preenchimento automático são geradas com base nos dados de gramática e de gráfico.         
**count**    | Número | Não<br>padrão: 10 | Número máximo de interpretações a serem retornadas.         
**offset**   | Número | Não<br>padrão: 0  | Índice da primeira interpretação a ser retornada. Por exemplo, *Count = 2 & deslocamento = 0* retorna as interpretações 0 e 1. *Count = 2 & deslocamento = 2* retorna as interpretações 2 e 3.       
**cedido**  | Número | Não<br>padrão: 1000 | Tempo limite em milissegundos. Somente as interpretações encontradas antes do tempo limite expirado são retornadas.

<br>
  
## <a name="response-json"></a>Resposta (JSON)

Nome     | Descrição
---------|---------
**query** |O parâmetro de *consulta* da solicitação.
**interpretações** |Uma matriz de 0 ou mais maneiras diferentes de fazer a correspondência entre a entrada do usuário e a gramática.
**interpretations[x].logprob**  |A probabilidade relativa do log natural da interpretação. Valores maiores são mais prováveis.
**interpretations[x].parse**  |Uma cadeia de caracteres XML que mostra como cada parte da consulta foi interpretada.
**interpretations[x].rules**  |Uma matriz de 1 ou mais regras definidas na gramática que foram invocadas durante a interpretação. Para a API de Conhecimento Acadêmico, sempre haverá 1 regra.
**interpretações [x]. regras [y]. nome**  |Nome da regra.
**interpretations[x].rules[y].output**  |Saída da regra.
**interpretations[x].rules[y].output.type** |O tipo de dados da saída da regra.  Para a API de Conhecimento Acadêmico, essa será sempre "consulta".
**interpretations[x].rules[y].output.value**  |A saída da regra. Para a API de Conhecimento Acadêmico, essa é uma cadeia de caracteres de expressão de consulta que pode ser passada para os métodos Evaluate e calchistogram.
**anulada** | True se a solicitação atingir o tempo limite.

<br>

#### <a name="example"></a>Exemplo:
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/interpret?query=papers by jaime&complete=1&count=2
 ```
<br>A resposta abaixo contém os dois principais (devido à contagem de parâmetros *= 2*) mais prováveis interpretações que completam os papéis de entrada do usuário parcial *por Jaime*: *papers por Jaime teevan* e *papers por Jaime Green*.  As conclusões de consulta geradas pelo serviço em vez de considerar apenas as correspondências exatas para o autor *Jaime* porque a solicitação especificou *Complete = 1*. Observe que o valor canônico *j l verde* foi correspondido por meio do sinônimo de *Jaime verde*, conforme indicado na análise.


```JSON
{
  "query": "papers by jaime",
  "interpretations": [
    {
      "logprob": -12.728,
      "parse": "<rule name=\"#GetPapers\">papers by <attr name=\"academic#AA.AuN\">jaime teevan</attr></rule>",
      "rules": [
        {
          "name": "#GetPapers",
          "output": {
            "type": "query",
            "value": "Composite(AA.AuN=='jaime teevan')"
          }
        }
      ]
    },
    {
      "logprob": -12.774,
      "parse": "<rule name=\"#GetPapers\">papers by <attr name=\"academic#AA.AuN\" canonical=\"j l green\">jaime green</attr></rule>",
      "rules": [
        {
          "name": "#GetPapers",
          "output": {
            "type": "query",
            "value": "Composite(AA.AuN=='j l green')"
          }
        }
      ]
    }
  ]
}
```  
<br>Para recuperar os resultados da entidade para uma interpretação, use *output. Value* da API de **interpretação** e passe-o para a API de **avaliação** por meio do parâmetro *expr* . Neste exemplo, a consulta para a primeira interpretação é: 
```
evaluate?expr=Composite(AA.AuN=='jaime teevan')
```
 
