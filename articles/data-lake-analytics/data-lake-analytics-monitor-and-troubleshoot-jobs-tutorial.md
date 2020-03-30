---
title: Monitor Azure Data Lake Analytics - Portal Azure
description: Este artigo descreve como usar o portal Azure para resolver os empregos do Azure Data Lake Analytics.
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: b7066d81-3142-474f-8a34-32b0b39656dc
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: 7fb85f179f1e1e1ac873fc7d6d937b34c1d48ada
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "71316591"
---
# <a name="monitor-jobs-in-azure-data-lake-analytics-using-the-azure-portal"></a>Monitorizar tarefas no Azure Data Lake Analytics com o Portal do Azure

**Para ver todos os trabalhos**

1. A partir do portal Azure, clique no **Microsoft Azure** no canto superior esquerdo.
2. Clique no mosaico com o nome da sua conta de Data Lake Analytics.  O resumo do trabalho é mostrado no azulejo da Gestão de **Emprego.**

    ![Gestão de trabalho do Azure Data Lake Analytics](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-job-management.png)

    A Gestão do Emprego dá-lhe uma olhada no estado do trabalho. Reparem que há um trabalho falhado.
3. Clique no azulejo de **Gestão** de Emprego para ver os trabalhos. Os trabalhos são categorizados em **Running,** **Fila,** e **Terminados.** Verá o seu trabalho falhado na secção **Final.** Será o primeiro da lista. Quando tiver muitos empregos, pode clicar em **Filtro** para ajudá-lo a localizar empregos.

    ![Trabalhos de filtro azure Data Lake Analytics](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-filter-jobs.png)
4. Clique no trabalho falhado da lista para abrir os detalhes do trabalho:

    ![Azure Data Lake Analytics falhou no emprego](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job.png)

    Repare no botão **Reenviar.** Depois de resolver o problema, pode reenviar o trabalho.
5. Clique na parte realçada da imagem anterior para abrir os detalhes do erro.  Verá algo como:

    ![Azure Data Lake Analytics falhou detalhes do trabalho](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job-details.png)

    Diz-lhe que a pasta de origem não foi encontrada.
6. Clique no **Script Duplicado**.
7. Atualize o caminho **FROM** para:

    "/Amostras/Dados/SearchLog.tsv"
8. Clique em **Submeter Tarefa**.

## <a name="see-also"></a>Consulte também
* [Visão geral do Lago de Dados Azure](data-lake-analytics-overview.md)
* [Introdução ao Azure Data Lake Analytics com o Azure PowerShell](data-lake-analytics-get-started-powershell.md)
* [Gerir o Azure Data Lake Analytics usando o portal Azure](data-lake-analytics-manage-use-portal.md)
