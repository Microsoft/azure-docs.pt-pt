---
title: Explore os trabalhos do Azure Stream Analytics no Código do Estúdio Visual
description: Este artigo mostra-lhe como exportar um trabalho da Azure Stream Analytics para um projeto local, enumerar empregos e ver entidades de emprego.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 05/15/2019
ms.topic: how-to
ms.openlocfilehash: 4f1c0650820a419275ade6095344033b6d81a568
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98013877"
---
# <a name="explore-azure-stream-analytics-with-visual-studio-code-preview"></a>Explore a Azure Stream Analytics com Código de Estúdio Visual (Pré-visualização)

A extensão Azure Stream Analytics para Visual Studio Code proporciona aos desenvolvedores uma experiência leve para gerir os seus trabalhos stream Analytics. Pode ser usado em Windows, Mac e Linux. Com a extensão Azure Stream Analytics, pode:

- [Criar,](quick-create-visual-studio-code.md)iniciar e parar de trabalhar
- Exportar postos de trabalho existentes para um projeto local
- Listar empregos e ver entidades de emprego

## <a name="export-a-job-to-a-local-project"></a>Exportar um emprego para um projeto local

Para exportar um emprego para um projeto local, localize o trabalho que deseja exportar no **Stream Analytics Explorer** em Visual Studio Code. Em seguida, selecione uma pasta para o seu projeto. O projeto é exportado para a pasta que seleciona, e pode continuar a gerir o trabalho a partir do Código do Estúdio Visual. Para obter mais informações sobre a utilização do Código do Estúdio Visual para gerir os trabalhos do Stream Analytics, consulte o [quickstart](quick-create-visual-studio-code.md)do Código do Estúdio Visual .

![Exportar trabalho asa em Código de Estúdio Visual](./media/vscode-explore-jobs/export-job.png)

## <a name="list-job-and-view-job-entities"></a>Listar emprego e ver entidades de emprego

Você pode usar a vista de trabalho para interagir com os trabalhos da Azure Stream Analytics do Visual Studio.


1. Clique no ícone **Azure** na Barra de Atividade do Código do Estúdio Visual e, em seguida, expanda **o nó stream Analytics**. Os seus trabalhos devem aparecer sob as suas assinaturas.

   ![Explorador de analítica de fluxo aberto](./media/vscode-explore-jobs/open-explorer.png)

2. Expanda o nó de trabalho, pode abrir e ver a consulta de trabalho, configuração, entradas, saídas e funções. 

3. Clique no nó de trabalho e escolha a **Vista de Emprego Aberta no** nó Portal para abrir a vista de trabalho no portal Azure.

   ![Vista de emprego aberta no portal](./media/vscode-explore-jobs/open-job-view.png)

## <a name="next-steps"></a>Passos seguintes

* [Crie um trabalho em nuvem Azure Stream Analytics em Código de Estúdio Visual (Pré-visualização)](quick-create-visual-studio-code.md)
