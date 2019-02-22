---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/21/2019
ms.author: alkohli
ms.openlocfilehash: 8c87e14071b3bb40421ab655c172df739570e295
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56660357"
---
As limitações seguintes aplicam-se aos dados quando são transmitidos para o Azure.

- Sugerimos que mais de um dispositivo não deve escrever o mesmo contentor.
- Se tiver um objeto do Azure existente (como um blob ou um arquivo) na cloud com o mesmo nome que o objeto que está a ser copiado, o dispositivo irá substituir o ficheiro na cloud.
- Uma hierarquia de diretório vazio (sem quaisquer ficheiros), criada em pastas de partilha não é carregada para os contentores de Blobs.
- Pode copiar os dados através de arrastar e soltar com o Explorador de ficheiros ou através da linha de comando. Se o tamanho do agregado de ficheiros que está sendo copiado for superior a 10 GB, recomendamos que usar um programa de cópia em massa, como o Robocopy ou rsync. As ferramentas de cópia em massa, repita a operação de cópia para erros freqüentes e fornecem uma resiliência adicional.
- Se a partilha associada ao contentor de armazenamento do Azure carrega blobs que não correspondem o tipo de blobs definidos para a partilha no momento da criação, tais blobs não são atualizadas. Por exemplo, criar uma partilha de blob de blocos no dispositivo. Associe a partilha com um contentor de cloud existente com blobs de páginas. Esse compartilhamento para transferir os ficheiros de atualização. Modificar alguns dos arquivos atualizados que já são armazenados como blobs de páginas na cloud. Irá ver falhas de carregamento.
