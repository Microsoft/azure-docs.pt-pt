---
title: Não é possível ligar remotamente às máquinas de virtuais do Azure porque a NIC está desabilitada | Documentos da Microsoft
description: Saiba como resolver um problema em que o RDP falha porque a NIC está desabilitada na VM do Azure | Documentos da Microsoft
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
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77918245"
---
#  <a name="cannot-remote-desktop-to-a-vm-because-the-network-interface-is-disabled"></a>Não é possível do ambiente de trabalho remoto a uma VM porque a interface de rede está desativada

Este artigo explica como resolver um problema em que não pode fazer uma ligação de ambiente de trabalho remoto para máquinas de virtuais de Windows do Azure (VMs) se a interface de rede está desativada.


## <a name="symptoms"></a>Sintomas

Não pode fazer uma ligação RDP ou qualquer outro tipo de ligação para quaisquer outras portas para uma VM no Azure, porque a interface de rede na VM está desativada.

## <a name="solution"></a>Solução

Antes de seguir estes passos, tire um instantâneo do disco do SO da VM afetado como uma cópia de segurança. Para mais informações, consulte [snapshot um disco](../windows/snapshot-copy-managed-disk.md).

Para ativar a interface do VM, utilize o controlo de série ou [repor](#reset-network-interface) a interface de rede para o VM.

### <a name="use-serial-control"></a>Utilizar o controlo de série

1. Ligue-se à consola em série e abra a [instância CMD](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
). Se a Consola série não estiver ativada no seu VM, consulte [a interface de rede de reset](#reset-network-interface).
2. Verificar o estado da interface de rede:

        netsh interface show interface

    Tenha em atenção o nome da interface de rede desativado.

3. Ative a interface de rede:

        netsh interface set interface name="interface Name" admin=enabled

    Por exemplo, se a interface interwork é o nome "Ethernet 2", execute o seguinte comando:

        netsh interface set interface name="Ethernet 2" admin=enabled

4.  Verifique o estado da interface de rede novamente para certificar-se de que a interface de rede está ativada.

        netsh interface show interface

    Não tem de reiniciar a VM neste momento. A VM será novamente acessível.

5.  Ligar à VM e ver se o problema está resolvido.

## <a name="reset-network-interface"></a>Repor a interface de rede

Para repor a interface de rede, altere o endereço IP para outro endereço IP que está disponível na sub-rede. Para tal, utilize o portal do Azure ou do Azure PowerShell. Para mais informações, consulte [redefinir a interface da rede](reset-network-interface.md).
