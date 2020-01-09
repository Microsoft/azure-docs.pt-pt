---
title: Explorar Azure Stream Analytics trabalhos no Visual Studio Code
description: Este artigo mostra como exportar um trabalho de Azure Stream Analytics para um projeto local, listar trabalhos e exibir entidades de trabalho.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 05/15/2019
ms.topic: conceptual
ms.openlocfilehash: 1d3a02d3778f9b4113767c5f755d675aeadd901b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75479244"
---
# <a name="explore-azure-stream-analytics-with-visual-studio-code-preview"></a>Explorar Azure Stream Analytics com Visual Studio Code (versão prévia)

O Azure Stream Analytics para extensão de Visual Studio Code oferece aos desenvolvedores uma experiência leve para gerenciar seus trabalhos de Stream Analytics. Ele pode ser usado no Windows, Mac e Linux. Com a extensão Azure Stream Analytics, você pode:

- [Criar](quick-create-vs-code.md), iniciar e parar trabalhos
- Exportar trabalhos existentes para um projeto local
- Listar trabalhos e exibir entidades de trabalho

## <a name="export-a-job-to-a-local-project"></a>Exportar um trabalho para um projeto local

Para exportar um trabalho para um projeto local, localize o trabalho que você deseja exportar no **Stream Analytics Explorer** em Visual Studio Code. Em seguida, selecione uma pasta para seu projeto. O projeto é exportado para a pasta selecionada e você pode continuar a gerenciar o trabalho de Visual Studio Code. Para obter mais informações sobre como usar Visual Studio Code para gerenciar trabalhos de Stream Analytics, consulte o guia de [início rápido](quick-create-vs-code.md)do Visual Studio Code.

![Exportar trabalho ASA no Visual Studio Code](./media/vscode-explore-jobs/export-job.png)

## <a name="list-job-and-view-job-entities"></a>Listar trabalho e exibir entidades de trabalho

Você pode usar a exibição de trabalho para interagir com Azure Stream Analytics trabalhos do Visual Studio.


1. Clique no ícone **do Azure** na barra de atividade Visual Studio Code e, em seguida, expanda **Stream Analytics nó**. Seus trabalhos devem aparecer sob suas assinaturas.

   ![Abrir Stream Analytics Explorer](./media/vscode-explore-jobs/open-explorer.png)

2. Expanda o nó do trabalho, você pode abrir e exibir a consulta, a configuração, as entradas, as saídas e as funções do trabalho. 

3. Clique com o botão direito do mouse no nó do trabalho e escolha o **modo de exibição de trabalho aberto no** nó do portal para abrir a exibição de trabalho no portal do Azure.

   ![Abrir o modo de exibição de trabalho no portal](./media/vscode-explore-jobs/open-job-view.png)

## <a name="next-steps"></a>Passos seguintes

* [Criar um trabalho de nuvem Azure Stream Analytics no Visual Studio Code (visualização)](quick-create-vs-code.md)
