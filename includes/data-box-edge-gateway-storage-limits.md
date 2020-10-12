---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 01/30/2019
ms.author: alkohli
ms.openlocfilehash: 51fde73565c9c4cdb21e998b8d43d303ceef062e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "82561379"
---
Esta secção descreve os limites para o serviço de armazenamento Azure, e as convenções de nomeação necessárias para ficheiros Azure, blobs de blocoS Azure e blobs de página Azure, conforme aplicável ao serviço Azure Stack Edge / Data Box Gateway. Reveja cuidadosamente os limites de armazenamento e siga todas as recomendações.

Para obter as informações mais recentes sobre os limites do serviço de armazenamento Azure e as melhores práticas para ações, contentores e ficheiros de nomeação, vá a:

- [Naming e referenciação de contentores](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [Naming and referencing shares](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata) (Nomenclatura e referência a partilhas)
- [Bloco blobs e convenções de blob de página](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> Se existirem ficheiros ou diretórios que excedam os limites do serviço de armazenamento Azure, ou não estejam em conformidade com as convenções de nomeação Azure Files/Blob, então estes ficheiros ou diretórios não são ingeridos no Azure Storage através do serviço Azure Stack Edge / Data Box Gateway.