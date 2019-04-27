---
title: Monitorizar tarefas no Azure Data Lake Analytics com o portal do Azure
description: Este artigo descreve como utilizar o portal do Azure para resolver problemas das tarefas de Azure Data Lake Analytics.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: b7066d81-3142-474f-8a34-32b0b39656dc
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: 40864bab068659be016161f7dc40243ebbd45174
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60812583"
---
# <a name="monitor-jobs-in-azure-data-lake-analytics-using-the-azure-portal"></a>Monitorizar tarefas no Azure Data Lake Analytics com o Portal do Azure

**Para ver todas as tarefas**

1. A partir do portal do Azure, clique em **Microsoft Azure** no canto superior esquerdo.
2. Clique no mosaico com o nome da sua conta de Data Lake Analytics.  O resumo da tarefa é apresentada no **Job Management** mosaico.

    ![Gestão de tarefas do Azure Data Lake Analytics](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-job-management.png)

    A tarefa de gestão proporciona-lhe rapidamente o estado da tarefa. Observe que há é uma tarefa falhada.
3. Clique nas **Job Management** mosaico para ver as tarefas. As tarefas são categorizadas em **em execução**, **em fila**, e **ter terminado**. Deverá ver a tarefa falhada na **ter terminado** secção. Ele deve ser primeiro na lista. Quando tiver muitas tarefas, pode clicar **filtro** para ajudá-lo a localizar tarefas.

    ![Tarefas de filtro do Azure Data Lake Analytics](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-filter-jobs.png)
4. Clique a tarefa falhada a partir da lista para abrir os detalhes da tarefa:

    ![O Azure Data Lake Analytics falha de tarefa](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job.png)

    Observe que o **volte a submeter** botão. Depois de corrigir o problema, pode submeter novamente a tarefa.
5. Clique em parte realçado na captura de tela anterior para abrir os detalhes do erro.  Deverá ver algo como:

    ![Detalhes da tarefa de falha do Azure Data Lake Analytics](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job-details.png)

    Indica que a pasta de origem não foi encontrada.
6. Clique em **duplicar Script**.
7. Atualização do **FROM** caminho para:

    "/Samples/Data/SearchLog.tsv"
8. Clique em **Submeter Tarefa**.

## <a name="see-also"></a>Consulte também
* [Descrição geral do Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Introdução ao Azure Data Lake Analytics com o Azure PowerShell](data-lake-analytics-get-started-powershell.md)
* [Gerir o Azure Data Lake Analytics com o portal do Azure](data-lake-analytics-manage-use-portal.md)
