---
title: Firewall do SO de convidado de VM do Azure está configurado incorretamente | Documentos da Microsoft
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/22/2018
ms.author: delhan
ms.openlocfilehash: 8f04d943e1db49beed13c183fbd06e401546fc03
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71153902"
---
# <a name="azure-vm-guest-os-firewall-is-misconfigured"></a>Firewall do SO de convidado VM do Azure está configurado incorretamente

Este artigo apresenta como corrigir operativo firewall de sistema numa VM do Azure de convidado configurado incorretamente.

## <a name="symptoms"></a>Sintomas

1.  O ecrã de boas-vindas de máquina virtual (VM) mostra que a VM está totalmente carregada.

2.  Dependendo da configuração do sistema operativo convidado, pode haver alguns ou nenhum tráfego de rede atingir a VM.

## <a name="cause"></a>Causa

Uma configuração incorreta do firewall de sistema do convidado pode bloquear alguns ou todos os tipos de tráfego de rede para a VM.

## <a name="solution"></a>Solução

Antes de seguir estes passos, tire um instantâneo do disco de sistema da VM afetado como uma cópia de segurança. Para obter mais informações, consulte [instantâneo de um disco](../windows/snapshot-copy-managed-disk.md).

Para resolver este problema, utilize a consola de série ou [Repare a VM offline](troubleshoot-rdp-internal-error.md#repair-the-vm-offline) ao anexar o disco do sistema da VM para uma VM de recuperação.

## <a name="online-mitigations"></a>Atenuações online

Ligar para o [consola de série e, em seguida, abra uma instância do PowerShell](serial-console-windows.md#use-cmd-or-powershell-in-serial-console). Se a consola de série não estiver ativada na VM, aceda à secção "Reparar a VM Offline" do artigo do Azure seguinte:

 [Ocorreu um erro interno ao tentar ligar a uma VM do Azure através do ambiente de trabalho remoto](troubleshoot-rdp-internal-error.md#repair-the-vm-offline)

As seguintes regras podem ser editadas para optar por ativar o acesso à VM (através de RDP) ou para fornecer uma experiência de resolução de problemas mais fácil:

*   Área de Trabalho Remota (TCP-in): Essa é a regra padrão que fornece acesso primário à VM, permitindo o RDP no Azure.

*   Gerenciamento Remoto do Windows (HTTP-in): Essa regra permite que você se conecte à VM usando o PowerShell. no Azure, esse tipo de acesso permite que você use o aspecto de script de script remoto e solução de problemas.

*   Compartilhamento de arquivos e impressoras (SMB-in): Essa regra habilita o acesso de compartilhamento de rede como uma opção de solução de problemas.

*   Compartilhamento de arquivos e impressoras (solicitação de Eco-ICMPv4-in): Essa regra permite que você execute ping na VM.

Caso o acesso de consola de série, pode consultar o estado atual da regra de firewall.

*   Consultar com o nome a apresentar como um parâmetro:

    ```cmd
    netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(DisplayName.*<FIREWALL RULE NAME>)" -context 9,4 | more
    ```

*   Consultar com a porta Local que o aplicativo utiliza:

    ```cmd
    netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(LocalPort.*<APPLICATION PORT>)" -context 9,4 | more
    ```

*   Consultar com o endereço Local IP que o aplicativo usa:

    ```cmd
    netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(LocalIP.*<CUSTOM IP>)" -context 9,4 | more
    ```

*   Se vir que a regra está desativada, pode ativá-la ao executar o seguinte comando:

    ```cmd
    netsh advfirewall firewall set rule name="<RULE NAME>" new enable=yes
    ```

*   Para resolução de problemas, pode desativar os perfis de firewall:

    ```cmd
    netsh advfirewall set allprofiles state off
    ```

    Se fizer isso para definir o firewall corretamente, volte a ativar a firewall depois de concluir a sua solução de problemas.

    > [!Note]
    > Não tem de reiniciar a VM para aplicar esta alteração.

*   Para ligar à VM através de RDP, tente novamente.

### <a name="offline-mitigations"></a>Atenuações offline

1.  Para habilitar ou desabilitar regras de firewall, consulte [habilitar ou desabilitar uma regra de firewall em um SO convidado de VM do Azure](enable-disable-firewall-rule-guest-os.md).

2.  Verifique se está no [cenário de tráfego de entrada de bloqueio de firewall do SO convidado](guest-os-firewall-blocking-inbound-traffic.md).

3.  Se estiver na dúvida sobre se a firewall está a bloquear o acesso, consulte [desativar o Firewall do SO na VM do Azure de convidado](disable-guest-os-firewall-windows.md)e, em seguida, volte a ativar a firewall de sistema do convidado, utilizando as regras de corretas.

