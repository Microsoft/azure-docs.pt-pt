---
title: Desenvolver e depurar Azure Stream Analytics empregos localmente
description: Aprenda a desenvolver e testar os trabalhos do Azure Stream Analytics no seu computador local antes de os executar no portal Azure.
ms.author: sujie
author: su-jie
ms.topic: conceptual
ms.date: 03/31/2020
ms.service: stream-analytics
ms.openlocfilehash: 18df480dab90d9ab127bb96971fc19cdc5a361ce
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98016478"
---
# <a name="develop-and-debug-azure-stream-analytics-jobs-locally"></a>Desenvolver e depurar Azure Stream Analytics empregos localmente

Enquanto você é capaz de criar e testar empregos Azure Stream Analytics no portal Azure, muitos desenvolvedores preferem uma experiência de desenvolvimento local. O Stream Analytics facilita a utilização do seu editor de código favorito e ferramentas de desenvolvimento para criar e testar trabalhos com streams de eventos ao vivo do Azure Event Hub, IoT Hub e Blob Storage utilizando um único nó de desempenho local totalmente funcional. Você também pode submeter empregos ao Azure diretamente do seu ambiente de desenvolvimento local.

## <a name="local-development-environments"></a>Ambientes de desenvolvimento local

A forma como desenvolves trabalhos stream Analytics no computador local depende das suas preferências de ferramentas e disponibilidade de recursos. Consulte [a comparação de recursos do Azure Stream Analytics](feature-comparison.md) para ver quais as funcionalidades suportadas para cada ambiente de desenvolvimento.

Os ambientes da tabela seguinte apoiam o desenvolvimento local:

|Ambiente                              |Descrição    |
|-----------------------------------------|------------|
|[Visual Studio Code](visual-studio-code-explore-jobs.md)| A [extensão Azure Stream Analytics Tools](https://marketplace.visualstudio.com/items?itemName=ms-bigdatatools.vscode-asa) para Visual Studio Code permite-lhe ser autor, gerir, testar o seu trabalho de análise stream tanto localmente como na nuvem com rico IntelliSense e controlo de fontes nativo. Suporta o desenvolvimento em Linux, MacOS e Windows. Para saber mais, consulte [Criar um trabalho Azure Stream Analytics em Código de Estúdio Visual.](quick-create-visual-studio-code.md) A extensão também suporta [visual Studio Codespaces](https://visualstudio.microsoft.com/services/visual-studio-codespaces/) que é um ambiente dev hospedado na nuvem.|
|[Visual Studio 2019](stream-analytics-tools-for-visual-studio-install.md) |Stream Analytics Tools faz parte do desenvolvimento do Azure e do armazenamento de dados e processamento de cargas de trabalho no Estúdio Visual. Pode utilizar o Visual Studio para escrever funções e desserializantes definidos pelo utilizador. Para saber mais, consulte [Criar um trabalho Azure Stream Analytics utilizando o Visual Studio](stream-analytics-quick-create-vs.md).|
|[Pedido de comando ou terminal](stream-analytics-tools-for-visual-studio-cicd.md)|O pacote Azure Stream Analytics CI/CD NuGet fornece ferramentas para a construção de projetos de estúdio visual, testes locais numa máquina arbitrária. O pacote Azure Stream Analytics CI/CD npm fornece ferramentas para construções de projetos de Código de Estúdio Visual (que gera um modelo de Gestor de Recursos Azure) numa máquina arbitrária.|

## <a name="next-steps"></a>Próximos passos

* [Teste Stream Analytics consultas localmente com dados de amostra usando Código de Estúdio Visual](visual-studio-code-local-run.md)
* [Teste Stream Analytics consulta localmente contra entrada de transmissão ao vivo usando Código de Estúdio Visual](visual-studio-code-local-run-live-input.md)
* [Teste Stream Analytics consultas localmente com Visual Studio](stream-analytics-vs-tools-local-run.md)
* [Teste dados ao vivo localmente usando ferramentas Azure Stream Analytics para Estúdio Visual](stream-analytics-live-data-local-testing.md)
