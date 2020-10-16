---
title: Comparação de recursos Azure Stream Analytics
description: Este artigo compara as funcionalidades suportadas para trabalhos em nuvem Azure Stream Analytics e IoT Edge no portal Azure, Visual Studio e Visual Studio Code.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 2fbaaac0095622f14d5bc0033b7787bec8ec2fa2
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92018292"
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
|Controlo de código fonte     |Não         |Sim         |Yes         |
|Suporte ci/CD     |Parcial         |Sim         |Yes         |
|Partilhar entradas e saídas em várias consultas     |Não         |Sim         |Yes         |
|Teste de consulta com um ficheiro de amostra     |Yes         |Sim        |Yes         |
|Testes locais de dados ao vivo     |Não         |Yes       |Sim      |
|Listar empregos e ver entidades de emprego     |Sim         |Yes        |Yes         |
|Exportar um emprego para um projeto local     |Não         |Yes         |Sim         |
|Submeter, iniciar e parar de trabalhar     |Yes         |Yes         |Yes         |
|Ver métricas de trabalho e diagrama     |Yes         |Yes         |Abrir no Portal         |
|Ver erros de tempo de execução de trabalho     |Sim         |Yes         |Não         |
|Registos do recurso     |Yes         |Não         |Não         |
|Propriedades de mensagens personalizadas     |Yes         |Yes         |Não       |
|Função de código personalizado C# e Deserializador|Modo apenas de leitura|Yes|Não|
|JavaScript UDF e UDA     |Yes         |Yes         |Apenas no Windows         |
|Serviço Machine Learning     |Yes        |Sim         |Não         |
|Azure Machine Learning Studio (clássico)|Sim, mas a consulta não pode ser testada.        |Sim |Não         |
|Nível de compatibilidade     |1.0</br>1.1</br>1.2 (padrão)         |1.0</br>1.1</br>1.2 (padrão)           |1.0</br>1.1</br>1.2 (padrão)           |
|Funções de deteção de anomalias baseadas em ML incorporadas     |Sim         |Yes         |Sim         |
|Funções GeoSpaciais incorporadas     |Yes         |Sim         |Sim         |



## <a name="iot-edge-job-features"></a>Funcionalidades de trabalho IoT Edge

|Funcionalidade  |Portal  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|Autoria de emprego     |Sim         |Yes         |Não         |
|Controlo de código fonte     |Não         |Yes         |Não         |
|Exportar um emprego para um projeto local     |Não         |Yes         |Não         |
|Teste de consulta com um ficheiro de amostra     |Yes         |Sim         |Não         |
|Partilhar entradas e saídas em várias consultas     |Não         |Yes         |Não         |
|UDF em C#     |Não         |Sim         |Não         |
|Apresentar postos de trabalho     |Yes         |Yes         |Não         |
|Listar empregos e ver entidades de emprego     |Sim         |Yes         |Não         |
|Ver métricas de trabalho e diagrama     |Sim         |Parcial         |Não         |
|Ver erros de tempo de execução de trabalho     |Yes         |Parcial         |Não         |
|Suporte ci/CD     |Não         |Não         |Não         |


## <a name="next-steps"></a>Próximos passos

* [Azure Stream Analytics no IoT Edge](stream-analytics-edge.md)
* [Tutorial: Escreva uma função definida pelo utilizador C# para o trabalho Azure Stream Analytics IoT Edge (Pré-visualização)](stream-analytics-edge-csharp-udf.md)
* [Desenvolver trabalhos de Stream Analytics IoT Edge usando ferramentas de Estúdio Visual](stream-analytics-tools-for-visual-studio-edge-jobs.md)
* [Use o Estúdio Visual para ver os trabalhos do Azure Stream Analytics](stream-analytics-vs-tools.md)
* [Explore a Azure Stream Analytics com Código de Estúdio Visual (Pré-visualização)](visual-studio-code-explore-jobs.md)


