---
title: Não é possível o ambiente de trabalho remoto para máquinas de virtuais do Azure por causa de IP estático | Documentos da Microsoft
description: Aprenda a solucionar o problema RDP que é causado por um IP estático no Microsoft Azure. | Documentos da Microsoft
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
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77918194"
---
#  <a name="cannot-remote-desktop-to-azure-virtual-machines-because-of-static-ip"></a>Não é possível o ambiente de trabalho remoto para máquinas de virtuais do Azure por causa de IP estático

Este artigo descreve um problema em que não é possível o ambiente de trabalho remoto para máquinas de virtuais de Windows do Azure (VMs) depois de um IP estático está configurado na VM.


## <a name="symptoms"></a>Sintomas

Quando efetuar uma ligação RDP a uma VM no Azure, recebe a seguinte mensagem de erro:

**O Ambiente de Trabalho Remoto não pode ligar-se ao computador remoto por uma destas razões:**

1. **O acesso remoto ao servidor não está ativado**

2. **O computador remoto está desligado.**

3. **O computador remoto não está disponível na rede**

**Certifique-se de que o computador remoto está ligado e ligado à rede e que o acesso remoto está ativado.**

Quando verifica a imagem nos diagnósticos da [Bota](../troubleshooting/boot-diagnostics.md) no portal Azure, vê normalmente as botas VM e aguarda-se por credenciais no ecrã de login.

## <a name="cause"></a>Causa

A VM tem um endereço IP estático, que é definido na interface de rede no Windows. Este endereço IP é diferente do endereço que é definido no portal do Azure.

## <a name="solution"></a>Solução

Antes de seguir estes passos, tire um instantâneo do disco do SO da VM afetado como uma cópia de segurança. Para mais informações, consulte [snapshot um disco](../windows/snapshot-copy-managed-disk.md).

Para resolver este problema, utilize o controlo de série para ativar o DHCP ou [redefinir](reset-network-interface.md) a interface de rede para o VM.

### <a name="use-serial-control"></a>Utilizar o controlo de série

1. Ligue-se à consola em série e abra a [instância CMD](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
). Se a Consola série não estiver ativada no seu VM, consulte a [interface de rede Reset](reset-network-interface.md).
2. Verifique se o DHCP está desabilitado na interface de rede:

        netsh interface ip show config
3. Se o DHCP estiver desabilitado, reverta a configuração da sua interface de rede para utilizar DHCP:

        netsh interface ip set address name="<NIC Name>" source=dhc

    Por exemplo, se a interface interwork nomes "Ethernet 2", execute o seguinte comando:

        netsh interface ip set address name="Ethernet 2" source=dhc

4. Consulte a configuração de IP novamente para certificar-se de que a interface de rede está agora corretamente configurada. O novo endereço IP deve corresponder ao que é fornecido pelo Azure.

        netsh interface ip show config

    Não tem de reiniciar a VM neste momento. A VM será novamente acessível.

Depois disso, se pretender configurar o IP estático para o VM, consulte os [endereços IP estáticos configurados para um VM](../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md).