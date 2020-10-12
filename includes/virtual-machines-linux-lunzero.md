---
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 09fa612e7e5c681da16bf19e94c626ae14a3b8a1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "77590818"
---
Ao adicionar discos de dados a um Linux VM, poderá encontrar erros se um disco não existir no LUN 0. Se estiver a adicionar um disco manualmente utilizando o `az vm disk attach -new` comando e especificar um LUN ( `--lun` ) em vez de permitir que a plataforma Azure determine o LUN apropriado, tenha cuidado para que um disco já exista / existirá na LUN 0. 

Considere o seguinte exemplo mostrando um corte da saída a partir `lsscsi` de:

```bash
[5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc 
[5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd 
```

Os dois discos de dados existem na LUN 0 e LUN 1 (a primeira coluna nos detalhes de `lsscsi` `[host:channel:target:lun]` saída). Ambos os discos devem estar acessíveis a partir do VM. Se tiver especificado manualmente o primeiro disco a ser adicionado no LUN 1 e o segundo disco no LUN 2, poderá não ver os discos corretamente a partir do seu VM.

> [!NOTE]
> O valor Azure `host` é de 5 nestes exemplos, mas este pode variar dependendo do tipo de armazenamento que seleciona.
> 
> 

Este comportamento do disco não é um problema de Azure, mas a forma como o kernel Linux segue as especificações do SCSI. Quando o kernel Linux digitalizar o autocarro SCSI para dispositivos ligados, um dispositivo deve ser encontrado no LUN 0 para que o sistema continue a procurar dispositivos adicionais. Como tal:

* Reveja a saída de depois de adicionar um disco de `lsscsi` dados para verificar se tem um disco no LUN 0.
* Se o seu disco não aparecer corretamente dentro do seu VM, verifique se existe um disco no LUN 0.

