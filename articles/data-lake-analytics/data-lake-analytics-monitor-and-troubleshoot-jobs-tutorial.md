---
title: Monitorar Azure Data Lake Analytics-portal do Azure
description: Este artigo descreve como usar o portal do Azure para solucionar problemas de Azure Data Lake Analytics trabalhos.
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: b7066d81-3142-474f-8a34-32b0b39656dc
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: 7fb85f179f1e1e1ac873fc7d6d937b34c1d48ada
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/26/2019
ms.locfileid: "71316591"
---
# <a name="monitor-jobs-in-azure-data-lake-analytics-using-the-azure-portal"></a>Monitorar trabalhos no Azure Data Lake Analytics usando o portal do Azure

**Para ver todos os trabalhos**

1. No portal do Azure, clique em **Microsoft Azure** no canto superior esquerdo.
2. Clique no mosaico com o nome da sua conta de Data Lake Analytics.  O resumo do trabalho é mostrado no bloco **Gerenciamento de trabalhos** .

    ![Azure Data Lake Analytics o gerenciamento de trabalhos](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-job-management.png)

    O gerenciamento de trabalhos fornece uma visão geral do status do trabalho. Observe que há um trabalho com falha.
3. Clique no bloco **Gerenciamento de trabalho** para ver os trabalhos. Os trabalhos são categorizados em **execução**, **na fila**e **finalizados**. Você deverá ver seu trabalho com falha na seção **encerrada** . Ela deve ser a primeira na lista. Quando você tem muitos trabalhos, você pode clicar em **Filtrar** para ajudá-lo a localizar trabalhos.

    ![Azure Data Lake Analytics filtrar trabalhos](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-filter-jobs.png)
4. Clique no trabalho com falha na lista para abrir os detalhes do trabalho:

    ![Azure Data Lake Analytics trabalho com falha](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job.png)

    Observe o botão **reenviar** . Depois de corrigir o problema, você pode reenviar o trabalho.
5. Clique em parte realçada da captura de tela anterior para abrir os detalhes do erro.  Você deverá ver algo como:

    ![Azure Data Lake Analytics detalhes do trabalho com falha](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job-details.png)

    Ele informa que a pasta de origem não foi encontrada.
6. Clique em **duplicar script**.
7. Atualize o caminho **from** para:

    /Samples/data/SearchLog.tsv
8. Clique em **Submeter Tarefa**.

## <a name="see-also"></a>Consulte também
* [Visão geral de Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Introdução ao Azure Data Lake Analytics com o Azure PowerShell](data-lake-analytics-get-started-powershell.md)
* [Gerir o Azure Data Lake Analytics com o portal do Azure](data-lake-analytics-manage-use-portal.md)
