---
title: Firewall de SO de convidado de VM do Azure está a bloquear o tráfego de entrada | Documentos da Microsoft
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
ms.openlocfilehash: 0a0da446385c592bfeda2e01e209ef1fb75b7de3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60711576"
---
# <a name="azure-vm-guest-os-firewall-is-blocking-inbound-traffic"></a>Firewall de SO de convidado de VM do Azure está a bloquear o tráfego de entrada

Este artigo discute como corrigir o problema de Portal de área de trabalho remoto (RDP) que ocorrerá se o tráfego de entrada de blocos de firewall de sistema operativo convidado.

## <a name="symptoms"></a>Sintomas

Não é possível utilizar uma ligação RDP para ligar a uma máquina virtual do Azure (VM). De arranque captura de ecrã -> de diagnóstico, mostra que o sistema operacional está totalmente carregado no ecrã de boas-vindos (Ctrl + Alt + Del).

## <a name="cause"></a>Causa

### <a name="cause-1"></a>Causa 1

A regra RDP não está configurada para permitir o tráfego RDP.

### <a name="cause-2"></a>Causa 2

Os perfis de firewall de sistema de convidado são configurados para bloquear todas as ligações de entrada, incluindo o tráfego RDP.

![Definição de firewall](./media/guest-os-firewall-blocking-inbound-traffic/firewall-advanced-setting.png)

## <a name="solution"></a>Solução

Antes de seguir estes passos, tire um instantâneo do disco de sistema da VM afetado como uma cópia de segurança. Para obter mais informações, consulte [instantâneo de um disco](../windows/snapshot-copy-managed-disk.md).

Para corrigir o problema, utilize um dos métodos na [como utilizar ferramentas remotas para resolver problemas de VM do Azure](remote-tools-troubleshoot-azure-vm-issues.md) para ligar remotamente à VM e, em seguida, edite as regras de firewall de sistema operativo convidado para **permitir** tráfego RDP .

### <a name="online-troubleshooting"></a>Resolução de problemas online

Ligar para o [consola de série e, em seguida, abra uma instância do PowerShell](serial-console-windows.md#use-cmd-or-powershell-in-serial-console). Se a consola de série não estiver ativada na VM, aceda a "[reparar a Offline VM](troubleshoot-rdp-internal-error.md#repair-the-vm-offline).

#### <a name="mitigation-1"></a>Atenuação 1

1.  Se o agente do Azure está instalado e a funcionar corretamente na VM, pode utilizar a opção "Reposição de configuração apenas" **suporte + resolução de problemas** > **Repor palavra-passe** no menu da VM.

2.  Executar esta opção de recuperação faz o seguinte:

    *   Permite que um componente RDP se está desativada.

    *   Permite que todos os perfis de firewall do Windows.

    *   Certifique-se de que a regra RDP está ativada na Firewall do Windows.

    *   Se os passos anteriores não funcionarem, repor manualmente a regra de firewall. Para tal, consulte todas as regras que contêm o nome "Ambiente de trabalho remoto" ao executar o seguinte comando:

        ```cmd
        netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(Name.*Remote Desktop)" -context 9,4 | more
        ```

        Se a porta RDP foi definida como qualquer outra porta que não seja 3389, é preciso encontrar qualquer regra personalizada que poderá ter sido criada e definir a esta porta. Para consultar todas as regras de entrada que têm uma porta personalizada, execute o seguinte comando:

        ```cmd
        netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(LocalPort.*<CUSTOM PORT>)" -context 9,4 | more
        ```

3.  Se vir que a regra está desativada, ativá-la. Para abrir um grupo inteiro, como o grupo interno do ambiente de trabalho remoto, execute o seguinte comando:

    ```cmd
    netsh advfirewall firewall set rule group="Remote Desktop" new enable=yes
    ```

    Caso contrário, para abrir a ambiente de trabalho remoto (TCP-In) regra específica, execute o seguinte comando:

    ```cmd
    netsh advfirewall firewall set rule name="<CUSTOM RULE NAME>" new enable=yes
    ```

4.  Para resolução de problemas, pode desativar os perfis de firewall para OFF:

    ```cmd
    netsh advfirewall set allprofiles state off
    ```

    Depois de concluir a resolução de problemas e a definição da firewall corretamente, volte a ativar a firewall.

    > [!Note]
    > Não tem de reiniciar a VM para aplicar estas alterações.

5.  Tente fazer com uma ligação de RDP para aceder à VM.

#### <a name="mitigation-2"></a>Atenuação 2

1.  Consultar os perfis de firewall para determinar se a política de firewall de entrada é definida como *BlockInboundAlways*:

    ```cmd
    netsh advfirewall show allprofiles | more
    ```

    ![Allprofiles](./media/guest-os-firewall-blocking-inbound-traffic/firewall-profiles.png)

    > [!Note]
    > As seguintes diretrizes aplicam-se para a política de firewall, dependendo de como é configurado:
    >    * *BlockInbound*: Todo o tráfego de entrada será bloqueado, exceto se tiver uma regra em vigor para permitir que o tráfego.
    >    * *BlockInboundAlways*: Todas as regras de firewall serão ignoradas e todo o tráfego será bloqueado.

2.  Editar a *DefaultInboundAction* para definir estes perfis para **permitir** tráfego. Para tal, execute o seguinte comando:

    ```cmd
    netsh advfirewall set allprofiles firewallpolicy allowinbound,allowoutbound
    ```

3.  Consulte os perfis novamente para certificar-se de que a alteração foi efetuada com êxito. Para tal, execute o seguinte comando:

    ```cmd
    netsh advfirewall show allprofiles | more
    ```

    > [!Note]
    > Não tem de reiniciar a VM para aplicar as alterações.

4.  Tente aceder novamente à sua VM através de RDP.

### <a name="offline-mitigations"></a>Atenuações offline

1.  [Anexar o disco do sistema para uma VM de recuperação](troubleshoot-recovery-disks-portal-windows.md).

2.  Inicie uma ligação de ambiente de trabalho remoto para a VM de recuperação.

3.  Certifique-se de que o disco é sinalizado de forma **Online** no console de gerenciamento de disco. Tenha em atenção a letra de unidade que está atribuída para o disco do sistema anexado.

#### <a name="mitigation-1"></a>Atenuação 1

Ver [como para ativar / desativar uma Firewall regra num SO convidado](enable-disable-firewall-rule-guest-os.md).

#### <a name="mitigation-2"></a>Atenuação 2

1.  [Anexar o disco do sistema para uma VM de recuperação](troubleshoot-recovery-disks-portal-windows.md).

2.  Inicie uma ligação de ambiente de trabalho remoto para a VM de recuperação.

3.  Depois do disco do sistema é anexado à VM de recuperação, certifique-se de que o disco é sinalizado de forma **Online** no console de gerenciamento de disco. Tenha em atenção a letra de unidade que está atribuída ao disco do SO anexado.

4.  Abra uma instância CMD elevada e, em seguida, execute o seguinte script:

    ```cmd
    REM Backup the registry prior doing any change
    robocopy f:\windows\system32\config f:\windows\system32\config.BACK /MT

    REM Mount the hive
    reg load HKLM\BROKENSYSTEM f:\windows\system32\config\SYSTEM

    REM Delete the keys to block all inbound connection scenario
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet001\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet001\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet001\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet002\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet002\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet002\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile" /v DoNotAllowExceptions

    REM Unmount the hive
    reg unload HKLM\BROKENSYSTEM
    ```

5.  [Desanexar o disco do sistema e voltar a criar a VM](troubleshoot-recovery-disks-portal-windows.md).

6.  Verifique se o problema está resolvido.
