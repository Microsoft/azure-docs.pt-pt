---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/04/2019
ms.author: alkohli
ms.openlocfilehash: 563849d3875ed0156d81770f58340633d90d515b
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66161295"
---
Aqui estão os tamanhos dos objetos do Azure que podem ser gravados. Certifique-se de que todos os ficheiros que são carregados estão em conformidade com estes limites.

| Tipo de objeto do Azure | Limite de carregamento                                             |
|-------------------|-----------------------------------------------------------|
| Blob de Blocos        | ~ 4.75 TB                                                 |
| Blob de Página         | 1 TB <br> Todos os ficheiros carregados no formato de Blob de páginas têm de ter 512 bytes alinhados (um múltiplo de integral), caso contrário o carregamento falha. <br> O VHD e VHDX são alinhadas de 512 bytes. |
| Ficheiros do Azure         | 1 TB <br> Todos os ficheiros carregados no formato de Blob de páginas têm de ter 512 bytes alinhados (um múltiplo de integral), caso contrário o carregamento falha. <br> O VHD e VHDX são alinhadas de 512 bytes. |

> [!IMPORTANT]
> Criação de arquivos (independentemente do tipo de armazenamento) é permitida até 5 TB. No entanto, se criar um arquivo cujo tamanho é maior do que o limite de carregamento definido na tabela anterior, não carregar o ficheiro obter. Tem de eliminar manualmente o ficheiro para recuperar o espaço.