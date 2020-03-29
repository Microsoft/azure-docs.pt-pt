---
title: Não é possível ligar remotamente às Máquinas Virtuais Azure porque o NIC está desativado / Microsoft Docs
description: Saiba como resolver um problema em que rdp falha porque o NIC está desativado em Azure VM. Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/12/2018
ms.author: genli
ms.openlocfilehash: 315974e4995630eb3af055ac0e1c44f7d8dd0737
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77918245"
---
#  <a name="cannot-remote-desktop-to-a-vm-because-the-network-interface-is-disabled"></a>Não é possível um ambiente de trabalho remoto para um VM porque a interface de rede está desativada

Este artigo explica como resolver um problema em que não é possível fazer uma ligação remota ao Ambiente de Trabalho às Máquinas Virtuais do Windows (VMs) se a interface de rede estiver desativada.


## <a name="symptoms"></a>Sintomas

Não é possível efetuar uma ligação RDP ou qualquer outro tipo de ligação a qualquer outra porta com um VM em Azure porque a interface de rede no VM está desativada.

## <a name="solution"></a>Solução

Antes de seguir estes passos, tire uma foto do disco osso do VM afetado como cópia de segurança. Para mais informações, consulte [snapshot um disco](../windows/snapshot-copy-managed-disk.md).

Para ativar a interface do VM, utilize o controlo de série ou [repor](#reset-network-interface) a interface de rede para o VM.

### <a name="use-serial-control"></a>Utilizar o controlo em série

1. Ligue-se à consola em série e abra a [instância CMD](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
). Se a Consola série não estiver ativada no seu VM, consulte [a interface de rede de reset](#reset-network-interface).
2. Verifique o estado da interface de rede:

        netsh interface show interface

    Tenha em anotao o nome da interface de rede desativada.

3. Ativar a interface de rede:

        netsh interface set interface name="interface Name" admin=enabled

    Por exemplo, se a interface interwork for denominada "Ethernet 2", executar o seguinte comando:

        netsh interface set interface name="Ethernet 2" admin=enabled

4.  Verifique novamente o estado da interface de rede para se certificar de que a interface de rede está ativada.

        netsh interface show interface

    Não precisa reiniciar o VM neste momento. O VM estará de volta acessível.

5.  Ligue-se ao VM e veja se o problema está resolvido.

## <a name="reset-network-interface"></a>Redefinir interface de rede

Para redefinir a interface da rede, altere o endereço IP para outro endereço IP que esteja disponível na Subnet. Para tal, utilize o portal Azure ou o Azure PowerShell. Para mais informações, consulte [redefinir a interface da rede](reset-network-interface.md).
