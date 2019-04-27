---
title: Interpretar o método - API de serviço de exploração de conhecimento
titlesuffix: Azure Cognitive Services
description: Saiba como utilizar o método de interpretação no conhecimento exploração de serviço (KES) API.
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 88776e2f4167c950d60c0405dcf950b5173fb989
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60814144"
---
# <a name="interpret-method"></a>interpretar o método

O *interpretar* método assume uma cadeia de caracteres de consulta de linguagem natural e devolve interpretações da intenção do utilizador com base nos dados de gramática e índice de formatado.  Para proporcionar uma experiência de pesquisa interativas, este método pode ser chamado como cada caractere é introduzido pelo utilizador com o *concluída* parâmetro definido como 1 para ativar as sugestões de conclusão automática.

## <a name="request"></a>Pedir

`http://<host>/interpret?query=<query>[&<options>]`

Name|Value| Descrição
----|----|----
consulta    | Cadeia de texto | Consulta introduzida pelo utilizador.  Se completa é definida como 1, consulta será interpretada como um prefixo para gerar sugestões de conclusão automática de consulta.        
Concluir | 0 (predefinição) ou 1 | 1 significa que as sugestões de conclusão automática são gerados com base nos dados de gramática e índice.         
count    | Número (predefinição = 10) | Número máximo de interpretações a devolver.         
offset   | Número (predefinição = 0) | Índice da interpretação de primeira para retornar.  Por exemplo, *contagem = 2 Desl & ocamento = 0* devolve interpretações 0 e 1. *contagem = 2 Desl & ocamento = 2* devolve interpretações, 2 e 3.       
tempo limite  | Número (predefinição = 1000) | Tempo limite em milissegundos. Só foram encontradas quaisquer interpretações antes do tempo limite decorreu são devolvidas.

Utilizar o *contagem* e *deslocamento* parâmetros, um grande número de resultados pode ser obtido a forma incremental através de várias solicitações.

## <a name="response-json"></a>Resposta (JSON)

JSONPath     | Descrição
---------|---------
$.query |*consulta* parâmetro no pedido.
$.interpretations   |Matriz de 0 ou mais formas de acordo com a consulta de entrada em relação a gramática.
$.interpretations[\*].logprob   |Probabilidade de registo relativo da interpretação (< = 0).  Valores superiores têm maior probabilidade.
$.interpretations[\*].parse |Cadeia de caracteres XML que mostra como cada parte da consulta era interpretada.
$.interpretations[\*].rules |Matriz de 1 ou mais regras definidas na gramática invocada durante a interpretação.
$.interpretations[\*].rules[\*].name    |nome da regra.
$.interpretations[\*].rules[\*].output  |Saída semântica da regra.
$.interpretations[\*].rules[\*].output.type |Tipo de dados de saída semântico.
$.interpretations[\*].rules[\*].output.value|Valor de saída semântica.  
$.aborted | VERDADEIRO se o pedido excedeu o limite de tempo.

### <a name="parse-xml"></a>Analisar o XML

A analisar o XML annotates a consulta (concluída) com informações sobre como corresponde à contra as regras na gramática e atributos no índice.  Segue-se um exemplo do domínio publicações acadêmicas:

```xml
<rule name="#GetPapers">
  papers by 
  <attr name="academic#Author.Name" canonical="heungyeung shum">harry shum</attr>
  <rule name="#GetPaperYear">
    written in
    <attr name="academic#Year">2000</attr>
  </rule>
</rule>
```

O `<rule>` elemento delimits o intervalo de consulta que correspondem a regra especificada pelo respetivo `name` atributo.  Podem ser aninhado de quando a análise envolve referências de regra na gramática.

O `<attr>` elemento delimits o intervalo de consulta correspondente o atributo de índice especificado pelo respetivo `name` atributo.  Quando a correspondência envolve sinônimo da consulta de entrada, o `canonical` atributo irá conter o valor canônico que correspondem o sinónimos do índice.

## <a name="example"></a>Exemplo

O exemplo de publicações acadêmicas, o pedido seguinte devolve até 2 sugestões de conclusão automática para a consulta de prefixo "papers por jaime":

`http://<host>/interpret?query=papers by jaime&complete=1&count=2`

A resposta contém as duas primeiras ("contagem = 2") interpretações mais prováveis que concluir a consulta parcial "papers por jaime": "papers por jaime teevan" e "papers por verde de jaime".  As conclusões de consulta de serviço gerado em vez de considerar apenas exact correspondências para o autor do "jaime", porque o pedido especificado "completo = 1". Tenha em atenção que o valor canônico "l j verde" matched via o sinónimo "verde de jamie", conforme indicado no parse.


```json
{
  "query": "papers by jaime",
  "interpretations": [
    {
      "logprob": -5.615,
      "parse": "<rule name=\"#GetPapers\">papers by <attr name=\"academic#Author.Name\">jaime teevan</attr></rule>",
      "rules": [
        {
          "name": "#GetPapers",
          "output": {
            "type": "query",
            "value": "Composite(Author.Name=='jaime teevan')"
          }
        }
      ]
    },
    {
      "logprob": -5.849,
      "parse": "<rule name=\"#GetPapers\">papers by <attr name=\"academic#Author.Name\" canonical=\"j l green\">jaime green</attr></rule>",
      "rules": [
        {
          "name": "#GetPapers",
          "output": {
            "type": "query",
            "value": "Composite(Author.Name=='j l green')"
          }
        }
      ]
    }
  ]
}
```  

Quando o tipo de saída semântico é "consultar", tal como neste exemplo, os objetos correspondentes podem ser obtidos passando *output.value* para o [ *avaliar* ](evaluateMethod.md) API através do *expr* parâmetro.

`http://<host>/evaluate?expr=Composite(AA.AuN=='jaime teevan')`
  
