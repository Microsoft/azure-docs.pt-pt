---
title: Não é possível o ambiente de trabalho remoto para máquinas de virtuais do Azure por causa de IP estático | Documentos da Microsoft
description: Aprenda a solucionar o problema RDP que é causado por um IP estático no Microsoft Azure. | Documentos da Microsoft
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/08/2018
ms.author: genli
ms.openlocfilehash: 81a3064290e0aa720a4fe6b0fa0d8eb13cfe6903
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60318923"
---
#  <a name="cannot-remote-desktop-to-azure-virtual-machines-because-of-static-ip"></a>Não é possível o ambiente de trabalho remoto para máquinas de virtuais do Azure por causa de IP estático

Este artigo descreve um problema em que não é possível o ambiente de trabalho remoto para máquinas de virtuais de Windows do Azure (VMs) depois de um IP estático está configurado na VM.

> [!NOTE]
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Resource Manager e clássica](../../azure-resource-manager/resource-manager-deployment-model.md). Este artigo explica como utilizar o modelo de implementação do Resource Manager, que recomendamos que utilize para novas implementações em vez do modelo de implementação clássica.

## <a name="symptoms"></a>Sintomas

Quando efetuar uma ligação RDP a uma VM no Azure, recebe a seguinte mensagem de erro:

**Ambiente de trabalho remoto não é possível ligar ao computador remoto para um dos seguintes motivos:**

1. **Acesso remoto para o servidor não está ativado**

2. **O computador remoto está desativado**

3. **O computador remoto não está disponível na rede**

**Certificar-se de que o computador remoto está ativado e ligado à rede e que o acesso remoto está ativado.**

Quando dá entrada a captura de ecrã do [diagnósticos de arranque](../troubleshooting/boot-diagnostics.md) no portal do Azure, verá a VM arranca normalmente e aguarda que as credenciais no ecrã de início de sessão.

## <a name="cause"></a>Causa

A VM tem um endereço IP estático, que é definido na interface de rede no Windows. Este endereço IP é diferente do endereço que é definido no portal do Azure.

## <a name="solution"></a>Solução

Antes de seguir estes passos, tire um instantâneo do disco do SO da VM afetado como uma cópia de segurança. Para obter mais informações, consulte [instantâneo de um disco](../windows/snapshot-copy-managed-disk.md).

Para resolver este problema, utilize o controlo Serial para ativar o DHCP ou [interface de rede de reposição](reset-network-interface.md) para a VM.

### <a name="use-serial-control"></a>Utilizar o controlo de série

1. Ligar à [consola de série e Abrir instância CMD](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
). Se a consola de série não estiver ativada na sua VM, consulte [interface de rede de reposição](reset-network-interface.md).
2. Verifique se o DHCP está desabilitado na interface de rede:

        netsh interface ip show config
3. Se o DHCP estiver desabilitado, reverta a configuração da sua interface de rede para utilizar DHCP:

        netsh interface ip set address name="<NIC Name>" source=dhc

    Por exemplo, se a interface interwork nomes "Ethernet 2", execute o seguinte comando:

        netsh interface ip set address name="Ethernet 2" source=dhc

4. Consulte a configuração de IP novamente para certificar-se de que a interface de rede está agora corretamente configurada. O novo endereço IP deve corresponder ao que é fornecido pelo Azure.

        netsh interface ip show config

    Não tem de reiniciar a VM neste momento. A VM será novamente acessível.

Depois disso, se quiser configurar o IP estático para a VM, veja [configurar endereços IP estáticos para uma VM](../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md).