---
title: Usando a API REST do serviço de importação/exportação do Azure | Microsoft Docs
description: Saiba onde encontrar recursos para usar a API REST do serviço de importação/exportação do Azure, incluindo instruções e material de referência.
author: twooley
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: 833b8c79fba57b7129092e084381c0671c396496
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978871"
---
# <a name="using-the-azure-importexport-service-rest-api"></a>Utilizar a API REST do serviço Importar/Exportar do Azure

O serviço de Importação/Exportação do Microsoft Azure expõe uma API REST para habilitar o controle programático de trabalhos de importação/exportação. Você pode usar a API REST para executar todas as operações de importação/exportação que podem ser executadas com o [portal do Azure](https://portal.azure.com/). Além disso, você pode usar a API REST para executar determinadas operações granulares, como consultar a porcentagem de conclusão de um trabalho, que não está disponível no momento no portal do Azure.

Consulte [usando o serviço de importação/exportação do Microsoft Azure para transferir dados para o armazenamento de BLOBs](../storage-import-export-service.md) para obter uma visão geral do serviço de importação/exportação e um tutorial que demonstra como usar o portal para criar e gerenciar trabalhos de importação e exportação.

## <a name="service-endpoints"></a>Pontos finais de serviço

O serviço de importação/exportação do Azure é um provedor de recursos para Azure Resource Manager e fornece um conjunto de APIs REST no seguinte ponto de extremidade HTTPS para gerenciar trabalhos de importação/exportação:

```
https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ImportExport/jobs/<job-name>
```

## <a name="versioning"></a>Controlo de versões

As solicitações para o serviço de importação/exportação devem especificar o parâmetro `api-version` e definir seu valor como `2016-11-01`.

## <a name="importexport-service-operations"></a>Operações do serviço de importação/exportação

[Criação de uma tarefa de importação](../storage-import-export-creating-an-import-job.md)

[Criação de uma tarefa de exportação](../storage-import-export-creating-an-export-job.md)

[Obter informações de estado para uma tarefa](storage-import-export-retrieving-state-info-for-a-job.md)

[Enumerar tarefas](../storage-import-export-enumerating-jobs.md)

[Cancelar e eliminar tarefas](storage-import-export-cancelling-and-deleting-jobs.md)

[Fazendo backup de manifestos de unidade](../storage-import-export-backing-up-drive-manifests.md)

[Recuperação de diagnósticos e erros para tarefas de Importação/Exportação](../storage-import-export-diagnostics-and-error-recovery.md)

## <a name="next-steps"></a>Passos seguintes

* [REST de importação/exportação de armazenamento](/rest/api/storageimportexport)
