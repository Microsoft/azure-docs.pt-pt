---
title: Não é possível ligar remotamente às Máquinas Virtuais Azure porque o NIC está desativado Microsoft Docs
description: Saiba como resolver um problema em que o PDR falha porque o NIC está desativado em Azure VM. Microsoft Docs
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
ms.openlocfilehash: 0e79efc9de43fc0a3044e9ae1e3959f63bb6e69f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90090273"
---
#  <a name="cannot-remote-desktop-to-a-vm-because-the-network-interface-is-disabled"></a>Não é possível um ambiente de trabalho remoto para um VM porque a interface de rede está desativada

Este artigo explica como resolver um problema no qual não é possível fazer uma ligação de ambiente de trabalho remoto às Máquinas Virtuais do Azure Windows (VMs) se a interface de rede estiver desativada.


## <a name="symptoms"></a>Sintomas

Não é possível escoar uma ligação RDP ou qualquer outro tipo de ligação a quaisquer outras portas a um VM em Azure porque a interface de rede no VM está desativada.

![Screenshot que mostra um VM no qual a interface de rede está desligada.](./media/troubleshoot-rdp-nic-disabled/disconnected.png)

![Screenshot que mostra um VM no qual a interface de rede é desativada.](./media/troubleshoot-rdp-nic-disabled/disabled.png)


## <a name="solution"></a>Solução

Antes de seguir estes passos, tire uma foto do disco de SO do VM afetado como cópia de segurança. Para mais informações, consulte [Snapshot um disco](../windows/snapshot-copy-managed-disk.md).

Para ativar a interface para o VM, utilize o controlo de série ou [reinicie](#reset-network-interface) a interface de rede para o VM.

### <a name="use-serial-control"></a>Utilizar o controlo em série

1. Ligue-se à [Consola em Série e abra a instância CMD](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console). Se a Consola em Série não estiver ativada no seu VM, consulte [a interface de rede de reset](#reset-network-interface).
2. Verifique o estado da interface da rede:

    ```console
    netsh interface show interface
    ```

    Note o nome da interface de rede desativada.

3. Ativar a interface de rede:

    ```console
    netsh interface set interface name="interface Name" admin=enabled
    ```

    Por exemplo, se a interface de intertração for denominada "Ethernet 2", executar o seguinte comando:

    ```console
    netsh interface set interface name="Ethernet 2" admin=enabled
    ```

4.  Verifique novamente o estado da interface de rede para se certificar de que a interface de rede está ativada.

    ```console
    netsh interface show interface
    ```

    Não tens de reiniciar o VM neste momento. O VM voltará a ser alcançável.

5.  Ligue-se ao VM e veja se o problema está resolvido.

## <a name="reset-network-interface"></a>Redefinir interface de rede

Para redefinir a interface de rede, altere o endereço IP para outro endereço IP disponível na Sub-rede. Para isso, utilize o portal Azure ou o Azure PowerShell. Para obter mais informações, consulte [a interface de rede Reset](reset-network-interface.md).
