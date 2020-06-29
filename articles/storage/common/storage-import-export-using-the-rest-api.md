---
title: Utilizando o serviço Azure Import/Export REST API ; Microsoft Docs
description: Saiba onde encontrar recursos para utilizar o serviço Azure Import/Export REST API, incluindo material de como fazer e referência.
author: twooley
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 01/15/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: ae80c00fd81d0966167d2adf00764a0b1089c163
ms.sourcegitcommit: 374e47efb65f0ae510ad6c24a82e8abb5b57029e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2020
ms.locfileid: "85514116"
---
# <a name="using-the-azure-importexport-service-rest-api"></a>Utilizar a API REST do serviço Importar/Exportar do Azure

O serviço Microsoft Azure Import/Export expõe uma API REST para permitir o controlo programático de postos de trabalho de importação/exportação. Pode utilizar a API REST para realizar todas as operações de importação/exportação que pode realizar com o [portal Azure.](https://portal.azure.com/) Além disso, pode utilizar a API REST para realizar determinadas operações granulares, como a consulta da conclusão percentual de um trabalho, que não está atualmente disponível no portal Azure.

Consulte [a Utilização do serviço de importação/exportação do Microsoft Azure para transferir dados para](../storage-import-export-service.md) o Blob Storage para uma visão geral do serviço de importação/exportação e um tutorial que demonstra como usar o portal para criar e gerir postos de trabalho de importação e exportação.

## <a name="service-endpoints"></a>Pontos finais de serviço

O serviço Azure Import/Export é um fornecedor de recursos para o Gestor de Recursos Azure e fornece um conjunto de APIs REST no seguinte ponto final HTTPS para a gestão de postos de trabalho de importação/exportação:

```
https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ImportExport/jobs/<job-name>
```

## <a name="versioning"></a>Controlo de versões

Os pedidos ao serviço de importação/exportação devem especificar o parâmetro e definir o `api-version` seu valor para `2016-11-01` .

## <a name="importexport-service-operations"></a>Operações de serviços de importação/exportação

[Criação de uma tarefa de importação](../storage-import-export-creating-an-import-job.md)

[Criação de uma tarefa de exportação](../storage-import-export-creating-an-export-job.md)

[Obter informações de estado para uma tarefa](storage-import-export-retrieving-state-info-for-a-job.md)

[Enumerar tarefas](../storage-import-export-enumerating-jobs.md)

[Cancelar e eliminar tarefas](storage-import-export-cancelling-and-deleting-jobs.md)

[Manifestos de unidade de backup Up](../storage-import-export-backing-up-drive-manifests.md)

[Recuperação de diagnósticos e erros para tarefas de Importação/Exportação](../storage-import-export-diagnostics-and-error-recovery.md)

## <a name="next-steps"></a>Passos seguintes

* [Importação de armazenamento/exportação DESE](/rest/api/storageimportexport)
