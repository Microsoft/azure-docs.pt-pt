---
title: Comparação de recursos do Azure Stream Analytics
description: Este artigo compara os recursos de suporte para a cloud do Azure Stream Analytics e as tarefas do IoT Edge no portal do Azure, Visual Studio e Visual Studio Code.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 22d7ef90ee0cf4d09467516b7bb0664327b7dabe
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509784"
---
# <a name="azure-stream-analytics-feature-comparison"></a>Comparação de recursos do Azure Stream Analytics

Com o Azure Stream Analytics, pode criar soluções de transmissão em fluxo em cloud e com o IoT Edge através de [portal do Azure](stream-analytics-quick-create-portal.md), [Visual Studio](stream-analytics-quick-create-vs.md), e [Visual Studio Code](quick-create-vs-code.md). As tabelas neste artigo mostram quais as funcionalidades são suportadas por cada plataforma para ambos os tipos de tarefa.

## <a name="cloud-job-features"></a>Recursos de trabalho de nuvem


|Funcionalidade  |Portal  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|Para várias plataformas     |Mac</br>Linux</br>Windows         |Windows        |Mac</br>Linux</br>Windows          |
|Script de criação     |Sim         |Sim         |Sim         |
|Intellisense do script     |Realce de sintaxe         |Realce de sintaxe</br>Conclusão de código</br>Marcador de erro         |Realce de sintaxe</br>Conclusão de código</br>Marcador de erro         |
|Definir entradas, saídas e as configurações da tarefa     |Sim         |Sim         |Sim         |
|Criação de partições de saída do blob     |Sim         |Sim         |Sim         |
|Power BI como saída     |Sim         |Sim         |Não         |
|Dados de referência de base de dados SQL     |Sim         |Sim         |Sim         |
|Propriedades da mensagem personalizada     |Sim         |Não         |Não         |
|Partilhar entradas e saídas em várias consultas     |Não         |Sim         |Sim         |
|UDF do JavaScript e UDA     |Sim         |Sim         |Apenas o Windows         |
|Textos explicativos do Machine Learning     |Sim, mas a consulta não é possível testar        |Sim, mas não é possível testar localmente         |Não         |
|Nível de compatibilidade     |1.0</br>1.1</br>1.2         |1.0</br>1.1</br>1.2          |1.0</br>1.1</br>1.2          |
|Funções internas de deteção de anomalias com base em ML     |Sim         |Sim         |Sim         |
|Funções de Geoespaciais incorporadas     |Sim         |Sim         |Sim         |
|Teste de consulta com um ficheiro de exemplo     |Sim         |Sim         |Sim         |
|Teste de dados local do Live     |Não         |Sim         |Não         |
|Listar tarefas e ver as entidades de tarefa     |Sim         |Sim         |Sim         |
|Exportar uma tarefa para um projeto de local     |Não         |Sim         |Sim         |
|Submeter, iniciar e parar tarefas     |Sim         |Sim         |Sim         |
|Controlo de origem     |Não         |Sim         |Sim         |
|Suporte de CI/CD     |Parcial         |Sim         |Sim         |
|Ver métricas de tarefa e de diagrama     |Sim         |Sim         |Abrir no Portal         |
|Ver erros de tempo de execução de tarefa     |Sim         |Sim         |Não         |
|Registos de diagnósticos     |Sim         |Não         |Não         |


## <a name="iot-edge-job-features"></a>Recursos de trabalho de IoT Edge

|Funcionalidade  |Portal  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|Tarefa de criação     |Sim         |Sim         |Não         |
|Controlo de origem     |Não         |Sim         |Não         |
|Exportar uma tarefa para um projeto de local     |Não         |Sim         |Não         |
|Teste de consulta com um ficheiro de exemplo     |Sim         |Sim         |Não         |
|Partilhar entradas e saídas em várias consultas     |Não         |Sim         |Não         |
|C#UDF     |Não         |Sim         |Não         |
|Submeter, iniciar e parar tarefas     |Sim         |Sim         |Não         |
|Listar tarefas e ver as entidades de tarefa     |Sim         |Sim         |Não         |
|Ver métricas de tarefa e de diagrama     |Sim         |Parcial         |Não         |
|Ver erros de tempo de execução de tarefa     |Sim         |Parcial         |Não         |
|Suporte de CI/CD     |Não         |Não         |Não         |


## <a name="next-steps"></a>Passos Seguintes

* [O Azure Stream Analytics do IoT Edge](stream-analytics-edge.md)
* [Tutorial: Escreva um C# função definida pelo utilizador para a tarefa do Azure Stream Analytics do IoT Edge (pré-visualização)](stream-analytics-edge-csharp-udf.md)
* [Desenvolver trabalhos do IoT Edge do Stream Analytics com ferramentas do Visual Studio](stream-analytics-tools-for-visual-studio-edge-jobs.md)
* [Use o Visual Studio para ver tarefas do Azure Stream Analytics](stream-analytics-vs-tools.md)
* [Explorar o Azure Stream Analytics com o Visual Studio Code (pré-visualização)](vscode-explore-jobs.md)


