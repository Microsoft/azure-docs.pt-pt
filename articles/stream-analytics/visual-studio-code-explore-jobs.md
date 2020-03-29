---
title: Explore os trabalhos da Azure Stream Analytics no Código do Estúdio Visual
description: Este artigo mostra-lhe como exportar um trabalho de Azure Stream Analytics para um projeto local, listar empregos e ver entidades de emprego.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 05/15/2019
ms.topic: conceptual
ms.openlocfilehash: 1d3a02d3778f9b4113767c5f755d675aeadd901b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75479244"
---
# <a name="explore-azure-stream-analytics-with-visual-studio-code-preview"></a>Explore o Azure Stream Analytics com o Código do Estúdio Visual (Pré-visualização)

A extensão Azure Stream Analytics para Visual Studio Code proporciona aos desenvolvedores uma experiência leve para gerir os seus trabalhos de Stream Analytics. Pode ser usado em Windows, Mac e Linux. Com a extensão Azure Stream Analytics, pode:

- [Criar,](quick-create-vs-code.md)começar e parar empregos
- Exportar empregos existentes para um projeto local
- Listar empregos e visualizar entidades de emprego

## <a name="export-a-job-to-a-local-project"></a>Exportar um emprego para um projeto local

Para exportar um emprego para um projeto local, localize o trabalho que pretende exportar no **Stream Analytics Explorer** em Visual Studio Code. Em seguida, selecione uma pasta para o seu projeto. O projeto é exportado para a pasta que seleciona, e pode continuar a gerir o trabalho a partir do Visual Studio Code. Para obter mais informações sobre a utilização do Código de Estúdio Visual para gerir os trabalhos do Stream Analytics, consulte o Visual Studio Code [quickstart](quick-create-vs-code.md).

![Exportação de emprego asa em Código de Estúdio Visual](./media/vscode-explore-jobs/export-job.png)

## <a name="list-job-and-view-job-entities"></a>Listar entidades de trabalho e ver entidades de trabalho

Você pode usar a vista de trabalho para interagir com os trabalhos da Azure Stream Analytics do Visual Studio.


1. Clique no ícone **Azure** na Barra de Atividade do Código do Estúdio Visual e, em seguida, expanda o **nó Stream Analytics**. Os seus trabalhos devem aparecer sob as suas assinaturas.

   ![Explorador de análise de fluxo aberto](./media/vscode-explore-jobs/open-explorer.png)

2. Expanda o seu nó de trabalho, pode abrir e ver a consulta de trabalho, configuração, inputs, saídas e funções. 

3. Clique no seu nó de trabalho e escolha o **Open Job View no** portal para abrir a vista de trabalho no portal Azure.

   ![Vista aberta para o emprego no portal](./media/vscode-explore-jobs/open-job-view.png)

## <a name="next-steps"></a>Passos seguintes

* [Crie um trabalho em nuvem Azure Stream Analytics em Código de Estúdio Visual (Pré-visualização)](quick-create-vs-code.md)
