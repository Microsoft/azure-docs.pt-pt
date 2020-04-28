---
title: Utilização do serviço de importação/exportação Azure REST API [ Microsoft Docs
description: Saiba onde encontrar recursos para utilizar o serviço de importação/exportação Azure REST API, incluindo tanto o material de como e de referência.
author: twooley
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: 833b8c79fba57b7129092e084381c0671c396496
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "74978871"
---
# <a name="using-the-azure-importexport-service-rest-api"></a>Utilizar a API REST do serviço Importar/Exportar do Azure

O serviço de importação/exportação do Microsoft Azure expõe uma API REST para permitir o controlo programático de postos de trabalho de importação/exportação. Pode utilizar a API REST para realizar todas as operações de importação/exportação que pode realizar com o [portal Azure](https://portal.azure.com/). Além disso, pode utilizar a API REST para realizar determinadas operações granulares, tais como a consulta da percentagem de conclusão de um emprego, que não está atualmente disponível no portal Azure.

Consulte [a utilização do serviço de importação/exportação do Microsoft Azure para transferir dados para o Armazenamento Blob](../storage-import-export-service.md) para uma visão geral do serviço de importação/exportação e um tutorial que demonstre como usar o portal para criar e gerir postos de trabalho de importação e exportação.

## <a name="service-endpoints"></a>Pontos finais de serviço

O serviço azure import/exportação é um fornecedor de recursos para o Gestor de Recursos Azure e fornece um conjunto de APIs REST no seguinte ponto final HTTPS para a gestão de postos de trabalho de importação/exportação:

```
https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ImportExport/jobs/<job-name>
```

## <a name="versioning"></a>Controlo de versões

Os pedidos ao serviço de importação/exportação devem especificar o parâmetro e definir o `api-version` seu valor para `2016-11-01`.

## <a name="importexport-service-operations"></a>Operações de serviço de importação/exportação

[Criação de uma tarefa de importação](../storage-import-export-creating-an-import-job.md)

[Criação de uma tarefa de exportação](../storage-import-export-creating-an-export-job.md)

[Obter informações de estado para uma tarefa](storage-import-export-retrieving-state-info-for-a-job.md)

[Enumerar tarefas](../storage-import-export-enumerating-jobs.md)

[Cancelar e eliminar tarefas](storage-import-export-cancelling-and-deleting-jobs.md)

[Backing Up drive manifesta](../storage-import-export-backing-up-drive-manifests.md)

[Recuperação de diagnósticos e erros para tarefas de Importação/Exportação](../storage-import-export-diagnostics-and-error-recovery.md)

## <a name="next-steps"></a>Passos seguintes

* [Depósito importação/DESCANSO DE Exportação](/rest/api/storageimportexport)
