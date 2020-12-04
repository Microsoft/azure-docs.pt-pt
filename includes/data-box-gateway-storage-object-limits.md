---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: 20d30935fe03bc002ab63f2f8ca1ce890ef9e3b4
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96582641"
---
Aqui estão os tamanhos dos objetos Azure que podem ser escritos. Certifique-se de que todos os ficheiros que são carregados estão em conformidade com estes limites.

| Tipo de objeto azul | Limite de upload                                             |
|-------------------|-----------------------------------------------------------|
| Blob de Bloco        | ~ 4.75 TB                                                 |
| Blob de página         | 1 TB <br> Todos os ficheiros carregados no formato Page Blob devem estar alinhados com 512 bytes (um múltiplo integral), caso contrário o upload falha. <br> Os VHD e VHDX estão alinhados com 512 bytes. |
| Ficheiros do Azure         | 1 TB <br> Todos os ficheiros carregados no formato Page Blob devem estar alinhados com 512 bytes (um múltiplo integral), caso contrário o upload falha. <br> Os VHD e VHDX estão alinhados com 512 bytes. |

> [!IMPORTANT]
> A criação de ficheiros (independentemente do tipo de armazenamento) é permitida até 5 TB. No entanto, se criar um ficheiro cujo tamanho seja superior ao limite de upload definido na tabela anterior, o ficheiro não é carregado. Tem de apagar manualmente o ficheiro para recuperar o espaço.