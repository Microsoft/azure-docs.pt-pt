---
title: Não é possível um ambiente de trabalho remoto para máquinas virtuais Azure por causa de IP estático Microsoft Docs
description: Saiba como resolver o problema do PDR que é causado por IP estático no Microsoft Azure.! Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/08/2018
ms.author: genli
ms.openlocfilehash: 49f3f44c7de8c700d0093c5eb6f166a1dffb34a4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86087253"
---
#  <a name="cannot-remote-desktop-to-azure-virtual-machines-because-of-static-ip"></a>Não é possível desktop remoto para Azure Virtual Machines por causa de IP estático

Este artigo descreve um problema no qual não é possível evitar o ambiente de trabalho para Azure Windows Virtual Machines (VMs) depois de um IP estático estar configurado no VM.


## <a name="symptoms"></a>Sintomas

Quando errar uma ligação RDP a um VM em Azure, recebe a seguinte mensagem de erro:

**O Ambiente de Trabalho Remoto não pode ligar-se ao computador remoto por uma destas razões:**

1. **O acesso remoto ao servidor não está ativado**

2. **O computador remoto está desligado**

3. **O computador remoto não está disponível na rede**

**Certifique-se de que o computador remoto está ligado e ligado à rede e que o acesso remoto está ativado.**

Quando verifica a imagem no [diagnóstico Boot](../troubleshooting/boot-diagnostics.md) no portal Azure, vê normalmente as botas VM e aguarda credenciais no ecrã de login.

## <a name="cause"></a>Causa

O VM tem um endereço IP estático definido na interface de rede dentro do Windows. Este endereço IP difere do endereço definido no portal Azure.

## <a name="solution"></a>Solução

Antes de seguir estes passos, tire uma foto do disco de SO do VM afetado como cópia de segurança. Para mais informações, consulte [Snapshot um disco](../windows/snapshot-copy-managed-disk.md).

Para resolver este problema, utilize o controlo de série para ativar a interface de rede DHCP ou [reinicializar](reset-network-interface.md) o VM.

### <a name="use-serial-control"></a>Utilizar o controlo em série

1. Ligue-se à [Consola em Série e abra a instância CMD](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
). Se a Consola em Série não estiver ativada no seu VM, consulte [a interface de rede Reset](reset-network-interface.md).
2. Verifique se o DHCP está desativado na interface de rede:

    ```console
    netsh interface ip show config
    ```

3. Se o DHCP estiver desativado, reverta a configuração da sua interface de rede para utilizar o DHCP:

    ```console
    netsh interface ip set address name="<NIC Name>" source=dhc
    ```

    Por exemplo, se a interface inter-trabalhar nomear "Ethernet 2", executar o seguinte comando:

    ```console
    netsh interface ip set address name="Ethernet 2" source=dhc
    ```

4. Consultar novamente a configuração IP para se certificar de que a interface de rede está agora corretamente configurada. O novo endereço IP deve corresponder ao fornecido pelo Azure.

    ```console
    netsh interface ip show config
    ```

    Não tens de reiniciar o VM neste momento. O VM voltará a ser alcançável.

Depois disso, se pretender configurar o IP estático para o VM, consulte [endereços IP estáticos de configuração para um VM](../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md).