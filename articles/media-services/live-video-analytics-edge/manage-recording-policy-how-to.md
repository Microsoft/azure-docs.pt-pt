---
title: Gerir a política de gravação - Azure
description: Este tópico explica como gerir a política de gravação.
ms.topic: how-to
ms.date: 04/27/2020
ms.openlocfilehash: ec72f28496c1392b9d95134c343e1892998a0c28
ms.sourcegitcommit: 2dd0932ba9925b6d8e3be34822cc389cade21b0d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/01/2021
ms.locfileid: "99224994"
---
# <a name="manage-recording-policy"></a>Gerir política de gravação

Pode utilizar o Live Video Analytics no IoT Edge para gravação contínua de [vídeo,](continuous-video-recording-concept.md)através do qual pode gravar vídeos na nuvem durante semanas ou meses. Pode gerir o comprimento (em dias) desse arquivo em nuvem utilizando as ferramentas de Gestão do [Ciclo de Vida incorporadas](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal) no armazenamento do Azure.  

A sua conta de Media Service está ligada a uma conta de Armazenamento Azure e, quando grava o vídeo na nuvem, o conteúdo é escrito para um [ativo](../latest/assets-concept.md)do Media Service . Cada ativo é mapeado para um recipiente na conta de armazenamento. A gestão do ciclo de vida permite-lhe definir uma [política](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal#policy) para uma conta de Armazenamento, na qual pode especificar uma [regra](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal#rules) como a seguinte.

```
{
  "rules": [
    {
      "name": "NinetyDayRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 30 },
            "delete": { "daysAfterModificationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

A regra acima:

* Aplica-se a todas as bolhas de bloco na conta de Armazenamento.
* Especifica que quando as bolhas envelhecem para além dos 30 dias, são transferidas do [nível](../../storage/blobs/storage-blob-storage-tiers.md?tabs=azure-portal)de acesso quente para arrefecer .
* E quando as bolhas envelhecem para além dos 90 dias, devem ser apagadas.

Quando utiliza o Live Video Analytics para gravar num ativo, especifica uma `segmentLength` propriedade que diz ao módulo para agregar uma duração mínima de vídeo (em segundos) antes de ser escrita para a nuvem. O seu ativo conterá uma série de segmentos, cada um com um tempo de criação que é mais recente do que `segmentLength` o anterior. Quando a política de gestão do ciclo de vida entra em vigor, elimina segmentos mais antigos do que o limiar especificado. No entanto, continuará a poder aceder e reproduzir os restantes segmentos através das APIs do Media Service. Para obter mais informações, consulte [as gravações de reprodução](playback-recordings-how-to.md). 

## <a name="limitations"></a>Limitações

Seguem-se algumas limitações conhecidas com a gestão do ciclo de vida:

* Pode ter no máximo 100 regras dentro da apólice, e cada regra pode especificar até 10 contentores. Assim, se precisar de ter diferentes políticas de gravação (por exemplo, um arquivo de 3 dias para a câmara virada para o parque de estacionamento, 30 dias para a câmara na doca de carregamento e 180 dias para a câmara atrás do balcão de checkout), então com uma conta de Media Service pode personalizar as regras para no máximo 1000 câmaras.
* As atualizações da política de gestão do ciclo de vida não são imediatas. Consulte [esta secção de PERGUNTAS EQ](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal#faq) para mais detalhes.
* Se optar por aplicar uma política em que as bolhas são transferidas para o nível fresco, então a reprodução dessa parte do arquivo pode ser afetada. Pode ver latências adicionais, ou erros esporádicos. Os Serviços de Comunicação Social não suportam a reprodução de conteúdos no nível de arquivo.

## <a name="next-steps"></a>Passos seguintes

[Reprodução de gravações](playback-recordings-how-to.md)
