---
title: Comparação de recursos do Azure Stream Analytics
description: Este artigo compara os recursos com suporte para trabalhos Azure Stream Analytics nuvem e IoT Edge no portal do Azure, no Visual Studio e no Visual Studio Code.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 8cdba27ac949584e1fa96e3f7b0874f4fc0d4212
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75443663"
---
# <a name="azure-stream-analytics-feature-comparison"></a>Comparação de recursos do Azure Stream Analytics

Com o Azure Stream Analytics, você pode criar soluções de streaming na nuvem e no IoT Edge usando [portal do Azure](stream-analytics-quick-create-portal.md), [Visual Studio](stream-analytics-quick-create-vs.md)e [Visual Studio Code](quick-create-vs-code.md). As tabelas neste artigo mostram quais recursos têm suporte em cada plataforma para ambos os tipos de trabalho.

## <a name="cloud-job-features"></a>Recursos de trabalho de nuvem


|Funcionalidade  |Portal  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|Plataforma cruzada     |Mac</br>Linux</br>Windows         |Windows        |Mac</br>Linux</br>Windows          |
|Criação de script     |Sim         |Sim         |Sim         |
|Script IntelliSense     |Realce de sintaxe         |Realce de sintaxe</br>Conclusão de código</br>Marcador de erro         |Realce de sintaxe</br>Conclusão de código</br>Marcador de erro         |
|Definir todos os tipos de entradas, saídas e configurações de trabalho     |Sim         |Sim         |Sim         |
|Controlo de código fonte     |Não         |Sim         |Sim         |
|Suporte a CI/CD     |Parcial         |Sim         |Sim         |
|Compartilhar entradas e saídas em várias consultas     |Não         |Sim         |Sim         |
|Teste de consulta com um arquivo de exemplo     |Sim         |Sim        |Sim         |
|Testes locais de dados dinâmicos     |Não         |Sim       |Sim      |
|Listar trabalhos e exibir entidades de trabalho     |Sim         |Sim        |Sim         |
|Exportar um trabalho para um projeto local     |Não         |Sim         |Sim         |
|Enviar, iniciar e parar trabalhos     |Sim         |Sim         |Sim         |
|Exibir as métricas e o diagrama do trabalho     |Sim         |Sim         |Abrir no Portal         |
|Exibir erros de tempo de execução de trabalho     |Sim         |Sim         |Não         |
|Registos de diagnósticos     |Sim         |Não         |Não         |
|Propriedades da mensagem personalizada     |Sim         |Sim         |Não       |
|C#função de código personalizado e desserializador|Modo somente leitura|Sim|Não|
|UDF e UDA do JavaScript     |Sim         |Sim         |Apenas no Windows         |
|Serviço do Machine Learning     |Sim, mas a consulta não pode ser testada        |Sim         |Não         |
|Machine Learning Studio     |Sim, mas a consulta não pode ser testada        |Sim |Não         |
|Nível de compatibilidade     |1.0</br>1.1</br>1,2 (padrão)         |1.0</br>1.1</br>1,2 (padrão)           |1.0</br>1.1</br>1,2 (padrão)           |
|Funções internas de detecção de anomalias baseadas em ML     |Sim         |Sim         |Sim         |
|Funções geoespaciais internas     |Sim         |Sim         |Sim         |



## <a name="iot-edge-job-features"></a>IoT Edge recursos de trabalho

|Funcionalidade  |Portal  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|Criação de trabalho     |Sim         |Sim         |Não         |
|Controlo de código fonte     |Não         |Sim         |Não         |
|Exportar um trabalho para um projeto local     |Não         |Sim         |Não         |
|Teste de consulta com um arquivo de exemplo     |Sim         |Sim         |Não         |
|Compartilhar entradas e saídas em várias consultas     |Não         |Sim         |Não         |
|C#UDF     |Não         |Sim         |Não         |
|Enviar trabalhos     |Sim         |Sim         |Não         |
|Listar trabalhos e exibir entidades de trabalho     |Sim         |Sim         |Não         |
|Exibir as métricas e o diagrama do trabalho     |Sim         |Parcial         |Não         |
|Exibir erros de tempo de execução de trabalho     |Sim         |Parcial         |Não         |
|Suporte a CI/CD     |Não         |Não         |Não         |


## <a name="next-steps"></a>Passos seguintes

* [O Azure Stream Analytics do IoT Edge](stream-analytics-edge.md)
* [Tutorial: gravar uma C# função definida pelo usuário para Azure Stream Analytics trabalho de IOT Edge (versão prévia)](stream-analytics-edge-csharp-udf.md)
* [Desenvolver Stream Analytics trabalhos de IoT Edge usando as ferramentas do Visual Studio](stream-analytics-tools-for-visual-studio-edge-jobs.md)
* [Use o Visual Studio para ver tarefas do Azure Stream Analytics](stream-analytics-vs-tools.md)
* [Explorar Azure Stream Analytics com Visual Studio Code (versão prévia)](visual-studio-code-explore-jobs.md)


