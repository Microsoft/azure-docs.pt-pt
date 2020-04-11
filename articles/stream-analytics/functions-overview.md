---
title: Funções definidas pelo utilizador no Azure Stream Analytics
description: Este artigo é uma visão geral das funções definidas pelo utilizador no Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/07/2020
ms.openlocfilehash: b29d66e8bb213fbbb162c3249f022e0783f9f62f
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115589"
---
# <a name="user-defined-functions-in-azure-stream-analytics"></a>Funções definidas pelo utilizador no Azure Stream Analytics

A linguagem de consulta semelhante a SQL no Azure Stream Analytics facilita a implementação da lógica de análise em tempo real em dados de streaming. O Stream Analytics proporciona flexibilidade adicional através de funções personalizadas que são invocadas na sua consulta. O seguinte exemplo de código `sampleFunction` é uma UDF chamada que aceita um parâmetro, cada registo de entrada `sampleResult`que o trabalho recebe, e o resultado é escrito para a saída como .

```sql
SELECT 
    UDF.sampleFunction(InputStream) AS sampleResult 
INTO 
    output 
FROM 
    InputStream 
```

## <a name="types-of-functions"></a>Tipos de funções

O Azure Stream Analytics suporta os seguintes quatro tipos de funções: 

* Funções definidas pelo utilizador do JavaScript 
* Agregados definidos pelo utilizador JavaScript 
* C# funções definidas pelo utilizador (utilizando o Estúdio Visual) 
* Azure Machine Learning 

Pode utilizar estas funções para cenários como pontuação em tempo real usando modelos de aprendizagem automática, manipulações de cordas, cálculos matemáticos complexos, codificação e descodificação de dados. 

## <a name="limitations"></a>Limitações

As funções definidas pelo utilizador são apátridas, e o valor de retorno só pode ser um valor escalar. Não é possível chamar para pontos finais DE REST externos a partir destas funções definidas pelo utilizador, uma vez que provavelmente terá impacto no desempenho do seu trabalho. 

O Azure Stream Analytics não mantém um registo de todas as funções invocações e resultados devolvidos. Para garantir a repetição - por exemplo, reexecutar o seu trabalho a partir de carimbos de tempo mais antigos produz novamente os mesmos resultados - não utilizar funções como `Date.GetData()` ou `Math.random()`, uma vez que estas funções não devolvem o mesmo resultado para cada invocação.  

## <a name="diagnostic-logs"></a>Registos de diagnósticos

Quaisquer erros de tempo de execução são considerados fatais e são emergidos através de registos de atividade e diagnóstico. Recomenda-se que a sua função lide com todas as exceções e erros e devolva um resultado válido à sua consulta. Isto impedirá que o seu trabalho vá para um [estado falhado.](job-states.md)  


## <a name="next-steps"></a>Passos seguintes

* [Funções definidas pelo utilizador JavaScript no Azure Stream Analytics](stream-analytics-javascript-user-defined-functions.md)
* [Agregados definidos pelo utilizador do Azure Stream Analytics JavaScript](stream-analytics-javascript-user-defined-aggregates.md)
* [Desenvolver funções definidas pelo utilizador .NET Standard para trabalhos de Análise de Fluxo de Azure](stream-analytics-edge-csharp-udf-methods.md)
* [Integrar o Azure Stream Analytics com o Azure Machine Learning](machine-learning-udf.md)

