---
title: Opções de potência da consola em série Azure Microsoft Docs
description: Opções de potência VM disponíveis dentro da Consola em Série Azure
services: virtual-machines
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 8/6/2019
ms.author: alsin
ms.openlocfilehash: 11c2549d7282bae5654ede1ac34d63a43cd0f059
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "75451207"
---
# <a name="power-options-available-from-the-azure-serial-console"></a>Opções de potência disponíveis na Consola em Série Azure

O Azure Serial Console fornece várias ferramentas poderosas para a gestão de energia no seu conjunto de escala de VM ou máquina virtual. Estas opções de gestão de energia podem ser confusas para alguns, pelo que esta é uma visão geral de cada ferramenta e da sua caixa de utilização pretendida.

Recurso de consola em série | Descrição | Caso de Utilização
:----------------------|:------------|:---------
Reiniciar VM | Um recomeço gracioso do seu VM ou da caixa de escala de máquina virtual. Esta operação é o mesmo que chamar a função de reinício disponível na página 'Vista Geral'. | Na maioria dos casos, esta opção deve ser a sua primeira ferramenta na tentativa de reiniciar o seu VM. A ligação da consola em série sofrerá uma breve interrupção e será retomada automaticamente assim que o VM recomeçar.
Repor VM | Um ciclo de potência vigoroso da sua balança de máquinas VM ou virtual definida pela plataforma Azure. | Esta opção é utilizada para reiniciar imediatamente o seu sistema operativo, independentemente do estado atual. Como esta operação não é graciosa, há o risco de perda de dados ou corrupção. Não existe qualquer interrupção na ligação da Consola em Série, o que pode ser útil para o envio de comandos no início do tempo de arranque (por exemplo, chegar ao GRUB num Linux VM ou modo de segurança num VM do Windows).
SysRq - Reboot (b) | Um pedido de sistema para forçar um convidado a reiniciar. | Esta funcionalidade aplica-se apenas aos sistemas operativos Linux e requer que o [SysRq esteja ativado](./serial-console-nmi-sysrq.md#system-request-sysrq) no sistema operativo. Se o sistema operativo estiver corretamente configurado para o SysRq, este comando fará com que o SO reinicie.
NMI (Interrupção não mascarada) | Um comando de interrupção, que será entregue ao sistema operativo | Esta operação está disponível tanto para [Windows](./serial-console-windows.md#use-the-serial-console-for-nmi-calls) como [para Linux](./serial-console-nmi-sysrq.md#non-maskable-interrupt-nmi) VMs, e requer que o NMI seja ativado. O envio de um NMI normalmente fará com que o seu sistema operativo se despenhe. Pode configurar o seu sistema operativo para criar um ficheiro de despejo e, em seguida, reiniciar após receber o NMI, o que pode ser útil na depuração de baixo nível.

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre a [Consola Em Série Azure para Os VMs Linux](./serial-console-linux.md)
* Saiba mais sobre a [Consola Em Série Azure para VMs Windows](./serial-console-windows.md)