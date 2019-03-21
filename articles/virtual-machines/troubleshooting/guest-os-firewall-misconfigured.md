---
title: Firewall do SO de convidado de VM do Azure está configurado incorretamente | Documentos da Microsoft
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: willchen
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/22/2018
ms.author: delhan
ms.openlocfilehash: fcea5e4e6bb108f1a8d8036e51a5dae8a9e6431b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57839751"
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

*   Ambiente de trabalho remoto (TCP-In): Esta é a regra padrão que fornece acesso primária para a VM ao permitir que o RDP no Azure.

*   Gestão remota do Windows (HTTP-In): Esta permite que a regra-o a ligar à VM com o PowerShell., no Azure, este tipo de acesso permite-lhe utiliza o aspecto de script de criação de scripts e a resolução de problemas remota.

*   Partilha de ficheiros e impressoras (SMB-In): Esta regra permite o acesso à partilha de rede como uma opção de resolução de problemas.

*   Arquivo e impressora a partilharem (pedido de eco - ICMPv4-In): Esta regra permite-lhe enviar um ping a VM.

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

1.  Para ativar ou desativar as regras de firewall, consulte [ativar ou desativar uma regra de firewall num SO de convidado de VM do Azure](enable-disable-firewall-rule-guest-os.md).

2.  Verifique se está no [cenário de tráfego de entrada de bloqueio de firewall do SO convidado](guest-os-firewall-blocking-inbound-traffic.md).

3.  Se estiver na dúvida sobre se a firewall está a bloquear o acesso, consulte [desativar o Firewall do SO na VM do Azure de convidado](disable-guest-os-firewall-windows.md)e, em seguida, volte a ativar a firewall de sistema do convidado, utilizando as regras de corretas.

