---
title: Funções definidas pelo utilizador no Azure Stream Analytics
description: Este artigo é uma visão geral das funções definidas pelo utilizador no Azure Stream Analytics.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/07/2020
ms.openlocfilehash: c671d3989fa46fa7546ba042b9132e19d80265a1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98020507"
---
# <a name="user-defined-functions-in-azure-stream-analytics"></a>Funções definidas pelo utilizador no Azure Stream Analytics

A linguagem de consulta semelhante ao SQL no Azure Stream Analytics facilita a implementação da lógica de análise em tempo real nos dados de streaming. O Stream Analytics proporciona flexibilidade adicional através de funções personalizadas que são invocadas na sua consulta. O exemplo de código a seguir é um UDF chamado `sampleFunction` que aceita um parâmetro, cada registo de entrada que o trabalho recebe, e o resultado é escrito para a saída como `sampleResult` .

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
* Funções definidas pelo utilizador (utilizando o Visual Studio) 
* Azure Machine Learning 

Pode utilizar estas funções para cenários como pontuação em tempo real utilizando modelos de aprendizagem automática, manipulações de cordas, cálculos matemáticos complexos, codificação e descodição de dados. 

## <a name="limitations"></a>Limitações

As funções definidas pelo utilizador são apátridas e o valor de retorno só pode ser um valor escalar. Não é possível chamar para os pontos finais externos rest a partir destas funções definidas pelo utilizador, uma vez que provavelmente terá impacto no desempenho do seu trabalho. 

O Azure Stream Analytics não mantém um registo de todas as invocações de funções e resultados devolvidos. Para garantir a repetibilidade - por exemplo, a reencam das suas funções a partir de um tempo mais antigo produz novamente os mesmos resultados - não utilize funções como `Date.GetData()` ou , uma vez que estas `Math.random()` funções não devolvem o mesmo resultado para cada invocação.  

## <a name="resource-logs"></a>Registos do recurso

Quaisquer erros de tempo de execução são considerados fatais e são surgidos através de registos de atividade e recursos. Recomenda-se que a sua função lide com todas as exceções e erros e devolva um resultado válido à sua consulta. Isto evitará que o seu trabalho vá para um [estado falhado.](job-states.md)  

## <a name="exception-handling"></a>Processamento de exceções

Qualquer exceção durante o processamento de dados é considerada uma falha catastrófica ao consumir dados no Azure Stream Analytics. As funções definidas pelo utilizador têm um potencial maior para lançar exceções e fazer com que o processamento pare. Para evitar este problema, utilize um bloco *de capturas* em JavaScript ou C# para apanhar exceções durante a execução do código. As exceções que são capturadas podem ser registadas e tratadas sem causar uma falha no sistema. É encorajado a embrulhar sempre o seu código personalizado num bloco *de capturas* para evitar abrir exceções inesperadas ao motor de processamento.

## <a name="next-steps"></a>Passos seguintes

* [Funções definidas pelo utilizador JavaScript no Azure Stream Analytics](stream-analytics-javascript-user-defined-functions.md)
* [Azure Stream Analytics JavaScript agregados definidos pelo utilizador](stream-analytics-javascript-user-defined-aggregates.md)
* [Desenvolver funções padrão .NET para trabalhos Azure Stream Analytics](stream-analytics-edge-csharp-udf-methods.md)
* [Integre a Azure Stream Analytics com Azure Machine Learning](machine-learning-udf.md)
