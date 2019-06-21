---
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 87dd3680aae3e87f78ab2dbe70c44b2008706747
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67184280"
---
Ao adicionar discos de dados a uma VM do Linux, poderá encontrar erros, se não existir um disco no LUN 0. Se estiver a adicionar um disco manualmente com o `azure vm disk attach-new` comando e especifique um LUN (`--lun`) em vez de permitir que a plataforma do Azure determinar o LUN apropriado, tenha cuidado que um disco já existe de colunas / existirá no LUN 0. 

Considere o exemplo seguinte, que mostra um trecho de código da saída do `lsscsi`:

```bash
[5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc 
[5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd 
```

Os dois discos de dados existem no LUN 0 e 1 de LUN (na primeira coluna da `lsscsi` detalhes de saída `[host:channel:target:lun]`). Os dois discos devem ser acessíveis a partir da VM. Se tivesse especificado manualmente o primeiro disco adicionado em 1 de LUN e o segundo disco no LUN 2, poderá não ver os discos a partir de corretamente na sua VM.

> [!NOTE]
> O Azure `host` valor é 5 nestes exemplos, mas isso pode variar consoante o tipo de armazenamento que selecionar.
> 
> 

Esse comportamento de disco não é um problema do Azure, mas a forma em que o kernel do Linux segue as especificações de SCSI. Quando o kernel do Linux analisa o barramento SCSI para dispositivos ligados, um dispositivo deve estar presente no LUN 0 para que o sistema para continuar a análise de dispositivos adicionais. Como tal:

* Reveja o resultado de `lsscsi` depois de adicionar um disco de dados para verificar se tem um disco no LUN 0.
* Se o disco não aparecer corretamente na sua VM, certifique-se de que existe um disco no LUN 0.

