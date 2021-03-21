---
title: Monitor Azure Data Lake Analytics - Portal Azure
description: Este artigo descreve como usar o portal Azure para resolver problemas de trabalhos do Azure Data Lake Analytics.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: troubleshooting
ms.date: 12/05/2016
ms.openlocfilehash: ddcf41a333d261bd9c5f669fde724a25eeba670e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92220283"
---
# <a name="monitor-jobs-in-azure-data-lake-analytics-using-the-azure-portal"></a>Monitorizar tarefas no Azure Data Lake Analytics com o Portal do Azure

## <a name="to-see-all-the-jobs"></a>Para ver todos os trabalhos

1. A partir do portal Azure, clique no **Microsoft Azure** no canto superior esquerdo.

2. Clique no mosaico com o nome da sua conta de Data Lake Analytics.  O resumo do trabalho é mostrado no azulejo de Gestão de **Emprego.**

   ![Gestão de empregos Azure Data Lake Analytics](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-job-management.png)

    A gestão do trabalho dá-lhe um olhar sobre o estado de trabalho. Reparem que há um trabalho falhado.
3. Clique no azulejo de **Gestão** de Emprego para ver os trabalhos. Os trabalhos são categorizados em **Running,** **Queued** e **Ended**. Verá o seu trabalho fracassado na secção **"Fim".** Será o primeiro da lista. Quando tem muitos empregos, pode clicar em **Filter** para ajudá-lo a localizar empregos.

   ![Azure Data Lake Analytics filtram trabalhos](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-filter-jobs.png)

4. Clique no trabalho falhado da lista para abrir os detalhes do trabalho:

   ![Azure Data Lake Analytics falhou no trabalho](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job.png)

    Reparem no botão **Resubmit.** Depois de resolver o problema, pode voltar a apresentar o trabalho.

5. Clique na parte realçada da imagem anterior para abrir os detalhes de erro.  Verá algo como:

   ![Azure Data Lake Analytics falhou detalhes do trabalho](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job-details.png)

   Diz-lhe que a pasta de origem não foi encontrada.

6. Clique **em Script Duplicado**.

7. Atualize o caminho **FROM** para: `/Samples/Data/SearchLog.tsv`

8. Clique em **Submeter Tarefa**.

## <a name="next-steps"></a>Passos seguintes

* [Visão geral do Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Introdução ao Azure Data Lake Analytics com o Azure PowerShell](data-lake-analytics-get-started-powershell.md)
* [Gerir a Azure Data Lake Analytics usando o portal Azure](data-lake-analytics-manage-use-portal.md)
