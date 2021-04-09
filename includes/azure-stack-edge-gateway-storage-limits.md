---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/13/2020
ms.author: alkohli
ms.openlocfilehash: d12a5042d0c2d79989d82e86e7265d030912f5ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98901130"
---
Esta secção descreve os limites para o serviço de armazenamento Azure, e as convenções de nomeação necessárias para ficheiros Azure, blobs de blocoS Azure e blobs de página Azure, conforme aplicável ao serviço Azure Stack Edge. Reveja cuidadosamente os limites de armazenamento e siga todas as recomendações.

Para obter as informações mais recentes sobre os limites do serviço de armazenamento Azure e as melhores práticas para ações, contentores e ficheiros de nomeação, vá a:

- [Naming e referenciação de contentores](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [Naming and referencing shares](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata) (Nomenclatura e referência a partilhas)
- [Bloco blobs e convenções de blob de página](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> Se existirem ficheiros ou diretórios que excedam os limites do serviço de armazenamento Azure, ou não estejam em conformidade com as convenções de nomeação Azure Files/Blob, então estes ficheiros ou diretórios não são ingeridos no Azure Storage através do serviço Azure Stack Edge.