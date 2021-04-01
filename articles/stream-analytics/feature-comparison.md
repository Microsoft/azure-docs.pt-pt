---
title: Comparação de recursos Azure Stream Analytics
description: Este artigo compara as funcionalidades suportadas para trabalhos em nuvem Azure Stream Analytics e IoT Edge no portal Azure, Visual Studio e Visual Studio Code.
author: an-emma
ms.author: raan
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 037bd8bc823cd8c77241d0ca25174e29d25149b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98020541"
---
# <a name="azure-stream-analytics-feature-comparison"></a>Comparação de recursos Azure Stream Analytics

Com o Azure Stream Analytics, pode criar soluções de streaming na nuvem e no IoT Edge utilizando o [portal Azure,](stream-analytics-quick-create-portal.md) [Visual Studio](stream-analytics-quick-create-vs.md)e Visual [Studio Code](quick-create-visual-studio-code.md). As tabelas deste artigo mostram quais as funcionalidades suportadas por cada plataforma para ambos os tipos de emprego.

> [!NOTE]
> As ferramentas Visual Studio e Visual Studio Code não suportam empregos nas regiões do Leste da China, China Norte, Alemanha Central e Alemanha NorthEast.

## <a name="cloud-job-features"></a>Recursos de trabalho em nuvem


|Funcionalidade  |Portal  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|Plataforma transversal     |Mac</br>Linux</br>Windows         |Windows        |Mac</br>Linux</br>Windows          |
|Autoria do guião     |Yes         |Yes         |Yes         |
|Script Intellisense     |Destaque de sintaxe         |Destaque de sintaxe</br>Conclusão do código</br>Marcador de erro         |Destaque de sintaxe</br>Conclusão do código</br>Marcador de erro         |
|Definir todos os tipos de entradas, saídas e configurações de emprego     |Yes         |Yes         |Yes         |
|Controlo de código fonte     |No         |Yes         |Yes         |
|Suporte ci/CD     |Parcial         |Yes         |Yes         |
|Partilhar entradas e saídas em várias consultas     |No         |Yes         |Yes         |
|Teste de consulta com um ficheiro de amostra     |Yes         |Yes        |Yes         |
|Testes locais de dados ao vivo     |No         |Yes       |Yes      |
|Listar empregos e ver entidades de emprego     |Yes         |Yes        |Yes         |
|Exportar um emprego para um projeto local     |No         |Yes         |Yes         |
|Submeter, iniciar e parar de trabalhar     |Yes         |Yes         |Yes         |
|Ver métricas de trabalho e diagrama     |Yes         |Yes         |Abrir no Portal         |
|Ver erros de tempo de execução de trabalho     |Yes         |Yes         |No         |
|Registos do recurso     |Yes         |No         |No         |
|Propriedades de mensagens personalizadas     |Yes         |Yes         |No       |
|Função de código personalizado C# e Deserializador|Modo apenas de leitura|Yes|No|
|JavaScript UDF e UDA     |Yes         |Yes         |Apenas no Windows         |
|Serviço Machine Learning     |Yes        |Yes         |No         |
|Azure Machine Learning Studio (clássico)|Sim, mas a consulta não pode ser testada.        |Yes |No         |
|Nível de compatibilidade     |1.0</br>1.1</br>1.2 (padrão)         |1.0</br>1.1</br>1.2 (padrão)           |1.0</br>1.1</br>1.2 (padrão)           |
|Funções de deteção de anomalias baseadas em ML incorporadas     |Yes         |Yes         |Yes         |
|Funções GeoSpaciais incorporadas     |Yes         |Yes         |Yes         |



## <a name="iot-edge-job-features"></a>Funcionalidades de trabalho IoT Edge

|Funcionalidade  |Portal  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|Autoria de emprego     |Yes         |Yes         |No         |
|Controlo de código fonte     |No         |Yes         |No         |
|Exportar um emprego para um projeto local     |No         |Yes         |No         |
|Teste de consulta com um ficheiro de amostra     |Yes         |Yes         |No         |
|Partilhar entradas e saídas em várias consultas     |No         |Yes         |No         |
|UDF em C#     |No         |Yes         |No         |
|Apresentar postos de trabalho     |Yes         |Yes         |No         |
|Listar empregos e ver entidades de emprego     |Yes         |Yes         |No         |
|Ver métricas de trabalho e diagrama     |Yes         |Parcial         |No         |
|Ver erros de tempo de execução de trabalho     |Yes         |Parcial         |No         |
|Suporte ci/CD     |No         |No         |Não         |


## <a name="next-steps"></a>Próximos passos

* [Azure Stream Analytics no IoT Edge](stream-analytics-edge.md)
* [Tutorial: Escreva uma função definida pelo utilizador C# para o trabalho Azure Stream Analytics IoT Edge (Pré-visualização)](stream-analytics-edge-csharp-udf.md)
* [Desenvolver trabalhos de Stream Analytics IoT Edge usando ferramentas de Estúdio Visual](stream-analytics-tools-for-visual-studio-edge-jobs.md)
* [Use o Estúdio Visual para ver os trabalhos do Azure Stream Analytics](stream-analytics-vs-tools.md)
* [Explore a Azure Stream Analytics com Código de Estúdio Visual (Pré-visualização)](visual-studio-code-explore-jobs.md)


