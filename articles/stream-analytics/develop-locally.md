---
title: Desenvolver e depurar empregos da Azure Stream Analytics localmente
description: Aprenda a desenvolver e testar trabalhos da Azure Stream Analytics no seu computador local antes de os executar no portal Azure.
ms.author: mamccrea
author: mamccrea
ms.topic: conceptual
ms.date: 03/31/2020
ms.service: stream-analytics
ms.openlocfilehash: 736fce1d4b347e36ad5c10ca89ad0627104a0232
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879846"
---
# <a name="develop-and-debug-azure-stream-analytics-jobs-locally"></a>Desenvolver e depurar empregos da Azure Stream Analytics localmente

Enquanto você é capaz de criar e testar trabalhos Azure Stream Analytics no portal Azure, muitos desenvolvedores preferem uma experiência de desenvolvimento local. O Stream Analytics facilita a utilização do seu editor de código favorito e ferramentas de desenvolvimento para criar e testar trabalhos com fluxos de eventos ao vivo do Azure Event Hub, IoT Hub e Blob Storage usando um tempo de funcionamento local do nó único totalmente funcional. Também pode submeter empregos ao Azure diretamente do seu ambiente de desenvolvimento local.

## <a name="local-development-environments"></a>Ambientes de desenvolvimento local

A forma como desenvolve os trabalhos do Stream Analytics no seu computador local depende das suas preferências de ferramentas e disponibilidade de funcionalidades. Ver [Azure Stream Analytics comparação](feature-comparison.md) de recursos para ver quais as funcionalidades suportadas para cada ambiente de desenvolvimento.

Os ambientes da tabela seguinte apoiam o desenvolvimento local:

|Ambiente                              |Descrição    |
|-----------------------------------------|------------|
|[Visual Studio Code](visual-studio-code-explore-jobs.md)| A [extensão azure Stream Analytics Tools](https://marketplace.visualstudio.com/items?itemName=ms-bigdatatools.vscode-asa) para Visual Studio Code permite-lhe ser autor, gerir e testar o seu trabalho de análise de Stream tanto localmente como na nuvem com o rico IntelliSense e o controlo de fontes nativas. Suporta o desenvolvimento em Linux, MacOS e Windows. Para saber mais, consulte [Create a Azure Stream Analytics no Visual Studio Code](quick-create-vs-code.md).|
|[Visual Studio 2019](stream-analytics-tools-for-visual-studio-install.md) |Stream Analytics Tools faz parte do desenvolvimento do Azure e armazenamento de dados e processamento de cargas de trabalho no Estúdio Visual. Pode utilizar o Visual Studio para escrever funções e desserializers personalizados c# definidos pelo utilizador. Para saber mais, consulte [Create a Azure Stream Analytics utilizando o Visual Studio](stream-analytics-quick-create-vs.md).|
|[Pedido de comando ou terminal](stream-analytics-tools-for-visual-studio-cicd.md)|O pacote Azure Stream Analytics CI/CD NuGet fornece ferramentas para a construção de projetos de estúdio visual, testes locais numa máquina arbitrária. O pacote Azure Stream Analytics CI/CD npm fornece ferramentas para construções de projeto visual studio code (que gera um modelo de Gestor de Recursos Azure) numa máquina arbitrária.|

## <a name="next-steps"></a>Passos seguintes

* [Teste Stream Analytics consulta localmente com dados de amostra usando código de estúdio visual](visual-studio-code-local-run.md)
* [Test Stream Analytics questiona localmente contra entrada de fluxo ao vivo usando código de estúdio visual](visual-studio-code-local-run-live-input.md)
* [Test Stream Analytics consultas localmente com Estúdio Visual](stream-analytics-vs-tools-local-run.md)
* [Teste dados ao vivo localmente usando ferramentas Azure Stream Analytics para Estúdio Visual](stream-analytics-live-data-local-testing.md)
