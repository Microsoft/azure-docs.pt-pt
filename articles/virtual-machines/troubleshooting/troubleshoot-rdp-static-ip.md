---
title: Não é possível remota mente de trabalho para máquinas virtuais azure por causa de IP estático Microsoft Docs
description: Saiba como resolver problemas de RDP que é causado por IP estático no Microsoft Azure.[ Microsoft Docs
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
ms.openlocfilehash: 92ad33fbc759605ae901c3bcf09283c8e0b1c4b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77918194"
---
#  <a name="cannot-remote-desktop-to-azure-virtual-machines-because-of-static-ip"></a>Não é possível remota mente de trabalho para máquinas virtuais azure por causa de IP estático

Este artigo descreve um problema no qual não é possível remotamente desktop para Máquinas Virtuais Do Windows (VMs) depois de um IP estático estar configurado no VM.


## <a name="symptoms"></a>Sintomas

Quando efauma ligação RDP a um VM em Azure, recebe a seguinte mensagem de erro:

**O Ambiente de Trabalho Remoto não pode ligar-se ao computador remoto por uma destas razões:**

1. **O acesso remoto ao servidor não está ativado**

2. **O computador remoto está desligado.**

3. **O computador remoto não está disponível na rede**

**Certifique-se de que o computador remoto está ligado e ligado à rede e que o acesso remoto está ativado.**

Quando verifica a imagem nos diagnósticos da [Bota](../troubleshooting/boot-diagnostics.md) no portal Azure, vê normalmente as botas VM e aguarda-se por credenciais no ecrã de login.

## <a name="cause"></a>Causa

O VM tem um endereço IP estático que está definido na interface de rede dentro do Windows. Este endereço IP difere do endereço definido no portal Azure.

## <a name="solution"></a>Solução

Antes de seguir estes passos, tire uma foto do disco osso do VM afetado como cópia de segurança. Para mais informações, consulte [snapshot um disco](../windows/snapshot-copy-managed-disk.md).

Para resolver este problema, utilize o controlo de série para ativar o DHCP ou [redefinir](reset-network-interface.md) a interface de rede para o VM.

### <a name="use-serial-control"></a>Utilizar o controlo em série

1. Ligue-se à consola em série e abra a [instância CMD](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
). Se a Consola série não estiver ativada no seu VM, consulte a [interface de rede Reset](reset-network-interface.md).
2. Verifique se o DHCP está desativado na interface da rede:

        netsh interface ip show config
3. Se o DHCP estiver desativado, reverta a configuração da sua interface de rede para utilizar o DHCP:

        netsh interface ip set address name="<NIC Name>" source=dhc

    Por exemplo, se a interface interwork nomear "Ethernet 2", executar o seguinte comando:

        netsh interface ip set address name="Ethernet 2" source=dhc

4. Consultar novamente a configuração IP para se certificar de que a interface de rede está agora corretamente configurada. O novo endereço IP deve corresponder ao fornecido pelo Azure.

        netsh interface ip show config

    Não precisa reiniciar o VM neste momento. O VM estará de volta acessível.

Depois disso, se pretender configurar o IP estático para o VM, consulte os [endereços IP estáticos configurados para um VM](../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md).