---
title: Interpretar o método - API de conhecimento académico
titlesuffix: Azure Cognitive Services
description: Utilize o método de interpretação para devolver interpretações formatadas de cadeias de consulta com base em dados Academic Graph e a gramática para instituições académicas nos serviços cognitivos da Microsoft.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: b679f1da0ada3e61fca79cdb985a43dc445877ce
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57975496"
---
# <a name="interpret-method"></a>interpretar o método

O **interpretar** REST API de demora de um utilizador final a cadeia de consulta (ou seja, uma consulta introduzida por um utilizador da sua aplicação) e devolve interpretações da intenção do utilizador com base nos dados Academic Graph e a gramática académico no formato.

Para fornecer uma experiência interativa, é possível chamar este método repetidamente após cada caráter introduzido pelo utilizador. Nesse caso, deve definir os **concluída** parâmetro como 1 para ativar as sugestões de conclusão automática. Se seu aplicativo não precisar de conclusão automática, deve definir os **concluída** parâmetro como 0.

**Ponto final REST:**

    https://westus.api.cognitive.microsoft.com/academic/v1.0/interpret?

## <a name="request-parameters"></a>Parâmetros do Pedido

Name     | Value | Necessário?  | Descrição
---------|---------|---------|---------
**query**    | Cadeia de texto | Sim | Consulta introduzida pelo utilizador.  Se completa é definida como 1, consulta será interpretada como um prefixo para gerar sugestões de conclusão automática de consulta.        
**Modelo**    | Cadeia de texto | Não  | Nome do modelo que pretende consultar.  Atualmente, o valor é predefinido para *mais recente*.        
**complete** | 0 ou 1 | Não<br>predefinição: 0  | 1 significa que as sugestões de conclusão automática são geradas com base nos dados de gramática e de gráfico.         
**count**    | Number | Não<br>predefinição: 10 | Número máximo de interpretações a devolver.         
**offset**   | Number | Não<br>predefinição: 0  | Índice da interpretação de primeira para retornar. Por exemplo, *contagem = 2 Desl & ocamento = 0* devolve interpretações 0 e 1. *contagem = 2 Desl & ocamento = 2* devolve interpretações, 2 e 3.       
**timeout**  | Number | Não<br>predefinição: 1000 | Tempo limite em milissegundos. Só foram encontradas quaisquer interpretações antes do tempo limite decorreu são devolvidas.

<br>
  
## <a name="response-json"></a>Resposta (JSON)

Name     | Descrição
---------|---------
**query** |O *consulta* parâmetro no pedido.
**interpretations** |Uma matriz de 0 ou mais diferentes formas de entrada do usuário em relação a gramática de correspondentes.
**interpretations[x].logprob**  |A probabilidade de registo natural relativa da interpretação. Valores maiores têm maior probabilidade.
**interpretations[x].parse**  |Uma cadeia de caracteres XML que mostra como cada parte da consulta era interpretada.
**interpretations[x].rules**  |Uma matriz de 1 ou mais regras definidas na gramática que foram chamados durante a interpretação. Para a API de conhecimento académico, sempre haverá 1 regra.
**interpretations[x].rules[y].name**  |nome da regra.
**interpretations[x].rules[y].output**  |Saída da regra.
**interpretations[x].rules[y].output.type** |O tipo de dados de saída da regra.  A API de conhecimento académico, sempre será "consulta".
**interpretations[x].rules[y].output.value**  |A saída da regra. Para a API de conhecimento académico, esta é uma cadeia de expressão de consulta que pode ser passada para o método evaluate e calchistogram.
**aborted** | VERDADEIRO se o pedido excedeu o limite de tempo.

<br>

#### <a name="example"></a>Exemplo:
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/interpret?query=papers by jaime&complete=1&count=2
 ```
<br>A resposta abaixo contém as duas primeiras (devido ao parâmetro *contagem = 2*) interpretações mais prováveis que concluir a entrada do usuário parcial *papers por jaime*: *papers por jaime teevan*  e *papers por verde de jaime*.  As conclusões de consulta de serviço gerado em vez de considerar apenas exact correspondências para o autor *jaime* porque o pedido especificado *completa = 1*. Tenha em atenção que o valor canônico *l j verde* correspondido via o sinónimo *verde de jamie*, conforme indicado no parse.


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
<br>Para obter resultados de entidade para uma interpretação, utilize *output.value* da **interpretar** API e transmiti-lo para o **avaliar** API através do *expr*  parâmetro. Neste exemplo, a consulta para a interpretação primeiro é: 
```
evaluate?expr=Composite(AA.AuN=='jaime teevan')
```
 
