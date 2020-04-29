---
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 09fa612e7e5c681da16bf19e94c626ae14a3b8a1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77590818"
---
Ao adicionar discos de dados a um VM Linux, poderá encontrar erros se um disco não existir no LUN 0. Se estiver a adicionar um `az vm disk attach -new` disco manualmente utilizando o`--lun`comando e especificar um LUN ( ) em vez de permitir que a plataforma Azure determine o LUN apropriado, tenha cuidado com o facto de já existir um disco/existir no LUN 0. 

Considere o seguinte exemplo que mostra um `lsscsi`corte da saída a partir de:

```bash
[5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc 
[5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd 
```

Os dois discos de dados existem em LUN 0 `lsscsi` e `[host:channel:target:lun]`LUN 1 (a primeira coluna nos detalhes de saída). Ambos os discos devem estar acessíveis a partir do VM. Se tiver especificado manualmente o primeiro disco a ser adicionado no LUN 1 e no segundo disco em LUN 2, pode não ver os discos corretamente dentro do seu VM.

> [!NOTE]
> O valor `host` Azure é de 5 nestes exemplos, mas este pode variar dependendo do tipo de armazenamento que selecionar.
> 
> 

Este comportamento do disco não é um problema Azure, mas a forma como o kernel Linux segue as especificações scsi. Quando o kernel Linux verificar o autocarro SCSI para dispositivos ligados, deve ser encontrado um dispositivo no LUN 0 para que o sistema continue a procurar dispositivos adicionais. Como tal:

* Reveja a `lsscsi` saída após adicionar um disco de dados para verificar se tem um disco no LUN 0.
* Se o seu disco não aparecer corretamente dentro do seu VM, verifique se existe um disco no LUN 0.

