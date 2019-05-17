---
title: Explorar as tarefas de Azure Stream Analytics com o Visual Studio Code (pré-visualização)
description: Este artigo mostra-lhe como exportar uma tarefa do Azure Stream Analytics para um projeto local, a lista de tarefas e entidades de tarefa de vista.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 05/15/2019
ms.topic: conceptual
ms.openlocfilehash: 8674d478646c8f9be6b32521c6624752ac6df052
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/17/2019
ms.locfileid: "65827804"
---
# <a name="explore-azure-stream-analytics-with-visual-studio-code-preview"></a>Explorar o Azure Stream Analytics com o Visual Studio Code (pré-visualização)

O Azure Stream Analytics para a extensão do Visual Studio Code fornece aos desenvolvedores uma experiência leve para gerenciar seus trabalhos do Stream Analytics. Ele pode ser usado no Windows, Mac e Linux. Com a extensão do Azure Stream Analytics, pode:

- [Criar](quick-create-vs-code.md), iniciar e parar tarefas
- Exportar tarefas existentes para um projeto de local
- Listar tarefas e ver as entidades de tarefa

## <a name="export-a-job-to-a-local-project"></a>Exportar uma tarefa para um projeto de local

Para exportar uma tarefa para um projeto local, localize a tarefa que pretende exportar na **Explorer do Stream Analytics** no Visual Studio Code. Em seguida, selecione uma pasta para o seu projeto. O projeto é exportado para a pasta selecionada e, pode continuar a gerir a tarefa a partir do Visual Studio Code. Para obter mais informações sobre como usar o Visual Studio Code para gerir as tarefas do Stream Analytics, consulte o Visual Studio Code [guia de introdução](quick-create-vs-code.md).

![Tarefa de exportação do ASA no Visual Studio Code](./media/vscode-explore-jobs/export-job.png)

## <a name="list-job-and-view-job-entities"></a>Tarefa de listar e ver as entidades de tarefa

Pode utilizar a vista de tarefas para interagir com tarefas do Azure Stream Analytics a partir do Visual Studio.


1. Clique nas **Azure** ícone na barra de atividade de código do Visual Studio e, em seguida, expanda **nó de Stream Analytics**. As tarefas devem aparecer nas suas subscrições.

   ![Abra Stream Analytics Explorer](./media/vscode-explore-jobs/open-explorer.png)

2. Expanda o nó de trabalho, pode abrir e ver a consulta da tarefa, configuração, entradas, saídas e funções. 

3. Clique com o botão direito do rato em seu nó de trabalho e escolha o **abrir a vista de tarefas no Portal** o nó para abrir a vista de tarefas no portal do Azure.

   ![Vista de tarefas aberto no portal](./media/vscode-explore-jobs/open-job-view.png)

## <a name="next-steps"></a>Passos Seguintes

* [Criar uma tarefa de cloud do Azure Stream Analytics no Visual Studio Code (pré-visualização)](quick-create-vs-code.md)
