---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 01/30/2019
ms.author: alkohli
ms.openlocfilehash: 94fe099984fae77c65658d7085a8540ff4f2448b
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67184715"
---
Esta secção descreve os limites para o serviço de armazenamento do Azure e as convenções de nomenclatura necessárias para ficheiros do Azure, blobs de blocos do Azure e blobs de páginas do Azure, conforme aplicável para o serviço de borda de caixa de Gateway/dados de caixa de dados. Reveja os limites de armazenamento com atenção e siga as recomendações apresentadas.

Para obter as informações mais recentes sobre os limites de serviço de armazenamento do Azure e práticas recomendadas para a nomenclatura de partilhas, contentores e ficheiros de mensagens em fila, aceda a:

- [Nomenclatura e referência para contentores](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [Naming and referencing shares](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata) (Nomenclatura e referência a partilhas)
- [Blobs de blocos e as convenções de blob de página](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> Se existirem quaisquer ficheiros ou diretórios que excedem os limites de serviço de armazenamento do Azure ou não está em conformidade com as convenções de nomenclatura de ficheiros/BLOBs do Azure, em seguida, estes ficheiros ou diretórios não são ingeridos no armazenamento do Azure através do serviço de borda de caixa de Gateway/dados de caixa de dados.