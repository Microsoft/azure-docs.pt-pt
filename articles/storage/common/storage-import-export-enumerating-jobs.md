---
title: Listar todos os trabalhos de importar/exportar do Azure | MicrosoftDocs
description: Saiba como listar todas as tarefas do serviço importar/exportar do Azure numa subscrição.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 0ae9e7fa76c8ecbb724cf0f494e648df989dff30
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61478696"
---
# <a name="enumerating-jobs-in-the-azure-importexport-service"></a>Enumerar tarefas no serviço importar/exportar do Azure
Para enumerar todas as tarefas numa subscrição, chamar o [lista de tarefas](/rest/api/storageimportexport/jobs) operação. `List Jobs` Devolve uma lista de tarefas, bem como os seguintes atributos:

-   O tipo de tarefa (importação ou exportação)

-   O estado atual da tarefa

-   A tarefa do associado à conta de armazenamento

## <a name="next-steps"></a>Passos Seguintes

* [Utilizar a API de REST do serviço de importação/exportação](storage-import-export-using-the-rest-api.md)
