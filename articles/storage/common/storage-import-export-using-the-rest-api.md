---
title: Usando o REST API do serviço importar/exportar do Azure | Documentos da Microsoft
description: Saiba onde encontrar recursos para utilizar a API de REST, incluindo material de referência e procedimentos do serviço de importar/exportar do Azure.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.component: common
ms.openlocfilehash: 3775a77a6dfc590e79e785e1604226c1187952de
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2018
ms.locfileid: "39528413"
---
# <a name="using-the-azure-importexport-service-rest-api"></a>Utilizar a API REST do serviço Importar/Exportar do Azure

O serviço de importação/exportação do Microsoft Azure expõe uma API REST para permitir o controle programático de tarefas de importação/exportação. Pode utilizar a API REST para realizar todas as operações de importação/exportação que pode realizar com o [portal do Azure](https://portal.azure.com/). Além disso, pode utilizar a API REST para executar determinadas operações granulares, tais como consultar a porcentagem de conclusão de uma tarefa, que não está atualmente disponível no portal do Azure.

Ver [com o serviço de importação/exportação do Microsoft Azure para transferir dados para armazenamento de BLOBs](../storage-import-export-service.md) para uma descrição geral do serviço importar/exportar e um tutorial que demonstra como utilizar o portal para criar e gerir a importar e exportar tarefas.

## <a name="service-endpoints"></a>Pontos finais de serviço

O serviço importar/exportar do Azure é um fornecedor de recursos do Azure Resource Manager e fornece um conjunto de REST APIs no seguinte ponto final HTTPS para gestão de tarefas de importação/exportação:

```
https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ImportExport/jobs/<job-name>
```

## <a name="versioning"></a>Controlo de versões

Pedidos para o serviço de importação/exportação tem de especificar o `api-version` parâmetro e defina seu valor como `2016-11-01`.

## <a name="importexport-service-operations"></a>Operações de serviço de importação/exportação

[Criação de uma tarefa de importação](../storage-import-export-creating-an-import-job.md)

[Criação de uma tarefa de exportação](../storage-import-export-creating-an-export-job.md)

[Obter informações de estado para uma tarefa](storage-import-export-retrieving-state-info-for-a-job.md)

[Enumerar tarefas](../storage-import-export-enumerating-jobs.md)

[Cancelar e eliminar tarefas](storage-import-export-cancelling-and-deleting-jobs.md)

[Efetuar manifestos da unidade](../storage-import-export-backing-up-drive-manifests.md)

[Recuperação de diagnósticos e erros para tarefas de Importação/Exportação](../storage-import-export-diagnostics-and-error-recovery.md)

## <a name="next-steps"></a>Passos Seguintes

* [Importar/exportar do armazenamento REST](/rest/api/storageimportexport)
