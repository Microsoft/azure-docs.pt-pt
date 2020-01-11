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
ms.openlocfilehash: bc637976632551eb1afce1894714fc96baabfe03
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/11/2020
ms.locfileid: "75894221"
---
#  <a name="cannot-remote-desktop-to-a-vm-because-the-network-interface-is-disabled"></a>Não é possível do ambiente de trabalho remoto a uma VM porque a interface de rede está desativada

Este artigo explica como resolver um problema em que não pode fazer uma ligação de ambiente de trabalho remoto para máquinas de virtuais de Windows do Azure (VMs) se a interface de rede está desativada.

> [!NOTE]
> O Azure tem dois modelos de implementação para criar e trabalhar com recursos: [Resource Manager e Clássico](../../azure-resource-manager/resource-manager-deployment-model.md). Este artigo explica como utilizar o modelo de implementação do Resource Manager, que recomendamos que utilize para novas implementações em vez do modelo de implementação clássica.

## <a name="symptoms"></a>Sintomas

Não pode fazer uma ligação RDP ou qualquer outro tipo de ligação para quaisquer outras portas para uma VM no Azure, porque a interface de rede na VM está desativada.

## <a name="solution"></a>Solução

Antes de seguir estes passos, tire um instantâneo do disco do SO da VM afetado como uma cópia de segurança. Para obter mais informações, consulte [instantâneo de um disco](../windows/snapshot-copy-managed-disk.md).

Para ativar a interface para a VM, utilize o controlo Serial ou [interface de rede de reposição](#reset-network-interface) para a VM.

### <a name="use-serial-control"></a>Utilizar o controlo de série

1. Ligar à [consola de série e Abrir instância CMD](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
). Se a consola de série não estiver ativada na sua VM, consulte [interface de rede de reposição](#reset-network-interface).
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

Para repor a interface de rede, altere o endereço IP para outro endereço IP que está disponível na sub-rede. Para tal, utilize o portal do Azure ou do Azure PowerShell. Para obter mais informações, consulte [interface de rede de reposição](reset-network-interface.md).
