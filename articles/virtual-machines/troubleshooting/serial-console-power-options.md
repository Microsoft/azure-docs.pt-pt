---
title: Opções de Potência da Consola Em Série Azure / Microsoft Docs
description: Opções de potência VM disponíveis na Consola Série Azure
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451207"
---
# <a name="power-options-available-from-the-azure-serial-console"></a>Opções de potência disponíveis na Consola Em Série Azure

A Consola Em Série Azure fornece várias ferramentas poderosas para a gestão de energia no seu conjunto de escala de máquinas VM ou virtual. Estas opções de gestão de energia podem ser confusas para alguns, pelo que esta é uma visão geral de cada ferramenta e do seu caso de utilização pretendido.

Recurso de consola em série | Descrição | Caso de utilização
:----------------------|:------------|:---------
Restart VM | Um reinício gracioso da sua vm ou da configuração de conjunto de máquinas virtuais. Esta operação é a mesma que ligar para a função de reinício disponível na página 'Visão Geral'. | Na maioria dos casos, esta opção deve ser a sua primeira ferramenta na tentativa de reiniciar o seu VM. A ligação da consola em série sofrerá uma breve interrupção e será retomada automaticamente assim que o VM tiver reiniciado.
Repor VM | Um ciclo de potência vigoroso do seu VM ou escala de máquina virtual definida pela plataforma Azure. | Esta opção é utilizada para reiniciar imediatamente o seu sistema operativo, independentemente do seu estado atual. Como esta operação não é graciosa, há o risco de perda de dados ou corrupção. Não existe nenhuma interrupção na ligação consola em série, o que pode ser útil para o envio de comandos no início do tempo de arranque (por exemplo, chegar a GRUB num VM Linux ou Modo De Segurança num VM do Windows).
SysRq - Reboot (b) | Um pedido de sistema para forçar um hóspede a reiniciar. | Esta funcionalidade é apenas aplicável aos sistemas operativos Linux e requer que o [SysRq seja ativado](./serial-console-nmi-sysrq.md#system-request-sysrq) no sistema operativo. Se o sistema operativo estiver corretamente configurado para o SysRq, este comando fará com que o SISTEMA reinicie.
NMI (Interrupção não-máscara) | Um comando de interrupção, que será entregue ao sistema operativo | Esta operação está disponível tanto para [VMs Windows](./serial-console-windows.md#use-the-serial-console-for-nmi-calls) como [Linux,](./serial-console-nmi-sysrq.md#non-maskable-interrupt-nmi) e requer que o NMI seja ativado. Enviar um NMI normalmente fará com que o seu sistema operativo se despenhe. Pode configurar o seu sistema operativo para criar um ficheiro de despejo e, em seguida, reiniciar ao receber o NMI, o que pode ser útil na depuração de baixo nível.

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre a [Consola Em Série Azure para VMs Linux](./serial-console-linux.md)
* Saiba mais sobre a [consola em série Azure para VMs do Windows](./serial-console-windows.md)