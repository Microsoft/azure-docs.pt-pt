---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/04/2019
ms.author: alkohli
ms.openlocfilehash: 563849d3875ed0156d81770f58340633d90d515b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "67184712"
---
Aqui estão os tamanhos dos objetos Azure que podem ser escritos. Certifique-se de que todos os ficheiros que são carregados estão em conformidade com estes limites.

| Tipo de objeto azul | Limite de upload                                             |
|-------------------|-----------------------------------------------------------|
| Bloco Blob        | ~ 4.75 TB                                                 |
| Página Blob         | 1 TB <br> Todos os ficheiros enviados no formato Page Blob devem estar alinhados 512 bytes (um múltiplo integral), caso contrário o upload falha. <br> Os VHD e VHDX são 512 bytes alinhados. |
| Ficheiros do Azure         | 1 TB <br> Todos os ficheiros enviados no formato Page Blob devem estar alinhados 512 bytes (um múltiplo integral), caso contrário o upload falha. <br> Os VHD e VHDX são 512 bytes alinhados. |

> [!IMPORTANT]
> A criação de ficheiros (independentemente do tipo de armazenamento) é permitida até 5 TB. No entanto, se criar um ficheiro cujo tamanho é maior do que o limite de upload definido na tabela anterior, o ficheiro não é carregado. Tem de apagar manualmente o ficheiro para recuperar o espaço.