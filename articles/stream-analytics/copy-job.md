---
title: Copiar ou apoiar trabalhos da Azure Stream Analytics
description: Este artigo descreve como copiar ou fazer cópias de um trabalho de Azure Stream Analytics.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 5c8f770855dd8d19a9d313f1b79f9bf8da4b2393
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75771500"
---
# <a name="copy-or-back-up-azure-stream-analytics-jobs"></a>Copiar ou apoiar trabalhos da Azure Stream Analytics

Pode copiar ou fazer cópias dos seus trabalhos de Azure Stream Analytics implantados utilizando o Visual Studio Code ou o Visual Studio. 

## <a name="before-you-begin"></a>Antes de começar
* Se não tiver uma subscrição Azure, crie uma [conta gratuita.](https://azure.microsoft.com/free/)

* Inicie sessão no [portal do Azure](https://portal.azure.com/).

* Instale [a extensão Azure Stream Analytics para](quick-create-vs-code.md#install-the-azure-stream-analytics-tools-extension) código de estúdio visual ou [ferramentas Azure Stream Analytics para Estúdio Visual](quick-create-vs-code.md#install-the-azure-stream-analytics-tools-extension).  

## <a name="visual-studio-code"></a>Visual Studio Code

1. Clique no ícone **Azure** na Barra de Atividade do Código do Estúdio Visual e, em seguida, expanda o nó **Stream Analytics.** Os seus trabalhos devem aparecer sob as suas assinaturas.

   ![Explorador de análise de fluxo aberto](./media/vscode-explore-jobs/open-explorer.png)

2. Para exportar um emprego para um projeto local, localize o trabalho que pretende exportar no **Stream Analytics Explorer** em Visual Studio Code. Em seguida, selecione uma pasta para o seu projeto.

    ![Exportação de emprego asa em Código de Estúdio Visual](./media/vscode-explore-jobs/export-job.png)

    O projeto é exportado para a pasta que seleciona e adicionado ao seu espaço de trabalho atual.

    ![Exportação de emprego asa em Código de Estúdio Visual](./media/stream-analytics-manage-job/copy-backup-stream-analytics-jobs.png)

3. Para publicar o trabalho para outra região ou cópia de segurança usando outro\*nome, selecione **Selecione a partir das suas subscrições para publicar** no editor de consulta (.asaql) e siga as instruções.

    ![Publicar para Azure em Código de Estúdio Visual](./media/quick-create-vs-code/submit-job.png)

## <a name="visual-studio"></a>Visual Studio

1. Siga a [exportação de um trabalho de Azure Stream Analytics implantado para um projeto instrutivo](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-vs-tools#export-jobs-to-a-project).

2. Abra \*o ficheiro .asaql no Editor de Consulta, selecione **Submeter a Azure** no editor de scripts e siga as instruções para publicar o trabalho para outra região ou cópia de segurança usando um novo nome.

## <a name="next-steps"></a>Passos seguintes

* [Quickstart: Criar um trabalho de Stream Analytics utilizando o Código do Estúdio Visual](quick-create-vs-code.md)
* [Quickstart: Criar um trabalho de Stream Analytics utilizando o Visual Studio](stream-analytics-quick-create-vs.md)
* [Implementar uma tarefa do Azure Stream Analytics com CI/CD através do Azure Pipelines](stream-analytics-tools-visual-studio-cicd-vsts.md)
