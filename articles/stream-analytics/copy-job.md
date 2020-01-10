---
title: Copiar ou fazer backup de trabalhos de Azure Stream Analytics
description: Este artigo descreve como copiar ou fazer backup de um trabalho de Azure Stream Analytics.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 5c8f770855dd8d19a9d313f1b79f9bf8da4b2393
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75771500"
---
# <a name="copy-or-back-up-azure-stream-analytics-jobs"></a>Copiar ou fazer backup de trabalhos de Azure Stream Analytics

Você pode copiar ou fazer backup de seus trabalhos de Azure Stream Analytics implantados usando o Visual Studio Code ou o Visual Studio. 

## <a name="before-you-begin"></a>Antes de começar
* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/).

* Inicie sessão no [Portal do Azure](https://portal.azure.com/).

* Instale [a extensão de Azure Stream Analytics para Visual Studio Code](quick-create-vs-code.md#install-the-azure-stream-analytics-tools-extension) ou [Azure Stream Analytics Tools para Visual Studio](quick-create-vs-code.md#install-the-azure-stream-analytics-tools-extension).  

## <a name="visual-studio-code"></a>Visual Studio Code

1. Clique no ícone **do Azure** na barra de atividade do Visual Studio Code e, em seguida, expanda **Stream Analytics** nó. Seus trabalhos devem aparecer sob suas assinaturas.

   ![Abrir Stream Analytics Explorer](./media/vscode-explore-jobs/open-explorer.png)

2. Para exportar um trabalho para um projeto local, localize o trabalho que você deseja exportar no **Stream Analytics Explorer** em Visual Studio Code. Em seguida, selecione uma pasta para seu projeto.

    ![Exportar trabalho ASA no Visual Studio Code](./media/vscode-explore-jobs/export-job.png)

    O projeto é exportado para a pasta que você selecionar e adicionou ao seu espaço de trabalho atual.

    ![Exportar trabalho ASA no Visual Studio Code](./media/stream-analytics-manage-job/copy-backup-stream-analytics-jobs.png)

3. Para publicar o trabalho em outra região ou backup usando outro nome, selecione **selecionar em suas assinaturas para publicar** no editor de consultas (\*. asaql) e siga as instruções.

    ![Publicar no Azure no Visual Studio Code](./media/quick-create-vs-code/submit-job.png)

## <a name="visual-studio"></a>Visual Studio

1. Siga o [trabalho exportar um Azure Stream Analytics implantado para uma instrução de projeto](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-vs-tools#export-jobs-to-a-project).

2. Abra o arquivo \*. asaql no editor de consultas, selecione **Enviar para o Azure** no editor de scripts e siga as instruções para publicar o trabalho em outra região ou backup usando um novo nome.

## <a name="next-steps"></a>Passos seguintes

* [Início rápido: criar um trabalho de Stream Analytics usando Visual Studio Code](quick-create-vs-code.md)
* [Início rápido: criar um trabalho de Stream Analytics usando o Visual Studio](stream-analytics-quick-create-vs.md)
* [Implementar uma tarefa do Azure Stream Analytics com CI/CD através do Azure Pipelines](stream-analytics-tools-visual-studio-cicd-vsts.md)
