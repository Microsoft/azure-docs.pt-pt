---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/04/2019
ms.author: alkohli
ms.openlocfilehash: 10d6ef2c90390f08e38726363416493f937de8f2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96467602"
---
Aqui estão os tamanhos dos objetos Azure que podem ser escritos. Certifique-se de que todos os ficheiros que são carregados estão em conformidade com estes limites.

| Tipo de objeto azul | Limite de upload                                             |
|-------------------|-----------------------------------------------------------|
| Blob de Bloco        | 4.75 TB                                                 |
| Blob de página         | 1 TB <br> Todos os ficheiros carregados no formato Page Blob devem estar alinhados com 512 bytes (um múltiplo integral), caso contrário o upload falha. <br> Os VHD e VHDX estão alinhados com 512 bytes. |
| Ficheiros do Azure         | 1 TB <br> Todos os ficheiros carregados no formato Page Blob devem estar alinhados com 512 bytes (um múltiplo integral), caso contrário o upload falha. <br> Os VHD e VHDX estão alinhados com 512 bytes. |

> [!IMPORTANT]
> A criação de ficheiros (independentemente do tipo de armazenamento) é permitida até 5 TB. No entanto, se criar um ficheiro cujo tamanho seja superior ao limite de upload definido na tabela anterior, o ficheiro não é carregado. Tem de apagar manualmente o ficheiro para recuperar o espaço.
