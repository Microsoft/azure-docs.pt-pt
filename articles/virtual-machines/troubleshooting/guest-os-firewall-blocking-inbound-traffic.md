---
title: Firewall Azure VM Guest OS está a bloquear o tráfego de entrada Microsoft Docs
description: Saiba como corrigir o problema de ligação do Portal do Ambiente de Trabalho Remoto (RDP) que a firewall do sistema operativo dos hóspedes está a bloquear o tráfego de entrada.
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
ms.openlocfilehash: 1b80fc997a4b3d2b472717b1ec2f379a4e958d8c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "80422548"
---
# <a name="azure-vm-guest-os-firewall-is-blocking-inbound-traffic"></a>Firewall Azure VM Guest OS está bloqueando o tráfego de entrada

Este artigo discute como corrigir o problema do Portal de Ambiente de Trabalho Remoto (RDP) que ocorre se o sistema operativo do hóspede bloquear o tráfego de entrada.

## <a name="symptoms"></a>Sintomas

Não é possível utilizar uma ligação RDP para ligar a uma máquina virtual Azure (VM). A partir de Boot diagnostics -> Screenshot, mostra que o sistema operativo está totalmente carregado no ecrã Welcome (Ctrl+Alt+Del).

## <a name="cause"></a>Causa

### <a name="cause-1"></a>Causa 1

A regra rdp não está definida para permitir o tráfego de PDR.

### <a name="cause-2"></a>Causa 2

Os perfis de firewall do sistema de hóspedes estão configurados para bloquear todas as ligações de entrada, incluindo o tráfego RDP.

![Definição de firewall](./media/guest-os-firewall-blocking-inbound-traffic/firewall-advanced-setting.png)

## <a name="solution"></a>Solução

Antes de seguir estes passos, tire uma foto do disco do sistema do VM afetado como cópia de segurança. Para mais informações, consulte [Snapshot um disco](../windows/snapshot-copy-managed-disk.md).

Para corrigir o problema, utilize um dos métodos de utilização de [ferramentas remotas para resolver problemas](remote-tools-troubleshoot-azure-vm-issues.md) de VM para ligar remotamente ao VM e, em seguida, editar as regras de firewall do sistema operativo para permitir o tráfego **rdp.**

### <a name="online-troubleshooting"></a>Resolução de problemas on-line

Ligue-se à [Consola em Série e, em seguida, abra uma instância PowerShell](serial-console-windows.md#use-cmd-or-powershell-in-serial-console). Se a Consola em Série não estiver ativada no VM, vá para "[Reparar o VM Offline](troubleshoot-rdp-internal-error.md#repair-the-vm-offline).

#### <a name="mitigation-1"></a>Mitigação 1

1.  Se o Agente Azure estiver instalado e a funcionar corretamente no VM, pode utilizar a opção "Reset configuration only" no **Suporte + resolução de problemas**Redefinir a  >  **palavra-passe** no menu VM.

2.  Executar esta opção de recuperação faz o seguinte:

    *   Ativa um componente RDP se estiver desativado.

    *   Permite todos os perfis de firewall do Windows.

    *   Certifique-se de que a regra RDP está ligada no Windows Firewall.

    *   Se os passos anteriores não funcionarem, repõe manualmente a regra da firewall. Para tal, consultar todas as regras que contêm o nome "Remote Desktop" executando o seguinte comando:

        ```cmd
        netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(Name.*Remote Desktop)" -context 9,4 | more
        ```

        Se a porta RDP foi definida para qualquer outra porta que não seja 3389, você tem que encontrar qualquer regra personalizada que possa ter sido criada e definida para este porto. Para consultar todas as regras de entrada que têm uma porta personalizada, executar o seguinte comando:

        ```cmd
        netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(LocalPort.*<CUSTOM PORT>)" -context 9,4 | more
        ```

3.  Se vir que a regra está desativada, ative-a. Para abrir um grupo inteiro, como o grupo de desktop remoto incorporado, executar o seguinte comando:

    ```cmd
    netsh advfirewall firewall set rule group="Remote Desktop" new enable=yes
    ```

    Caso contrário, para abrir a regra específica do Ambiente de Trabalho Remoto (TCP-In), executar o seguinte comando:

    ```cmd
    netsh advfirewall firewall set rule name="<CUSTOM RULE NAME>" new enable=yes
    ```

4.  Para a resolução de problemas, pode virar os perfis de firewall para OFF:

    ```cmd
    netsh advfirewall set allprofiles state off
    ```

    Depois de terminar a resolução de problemas e de colocar a firewall corretamente, reecam ative a firewall.

    > [!Note]
    > Não é preciso reiniciar o VM para aplicar estas alterações.

5.  Tente fazer uma ligação RDP para aceder ao VM.

#### <a name="mitigation-2"></a>Mitigação 2

1.  Consultar os perfis de firewall para determinar se a política de firewall de entrada está definida para *BlockInboundAlways*:

    ```cmd
    netsh advfirewall show allprofiles | more
    ```

    ![Todos os filtros](./media/guest-os-firewall-blocking-inbound-traffic/firewall-profiles.png)

    > [!Note]
    > As seguintes diretrizes aplicam-se à política de firewall, dependendo da forma como é configurada:
    >    * *Bloqueio:* Todo o tráfego de entrada será bloqueado a menos que tenha uma regra em vigor para permitir esse tráfego.
    >    * *BlockInboundAlways*: Todas as regras de firewall serão ignoradas e todo o tráfego será bloqueado.

2.  Edite a *DefaultInboundAction* para definir estes perfis para **permitir o** tráfego. Para tal, execute o seguinte comando:

    ```cmd
    netsh advfirewall set allprofiles firewallpolicy allowinbound,allowoutbound
    ```

3.  Questione novamente os perfis para se certificar de que a sua mudança foi feita com sucesso. Para tal, execute o seguinte comando:

    ```cmd
    netsh advfirewall show allprofiles | more
    ```

    > [!Note]
    > Não é preciso reiniciar o VM para aplicar as alterações.

4.  Tente novamente aceder ao seu VM através de RDP.

### <a name="offline-mitigations"></a>Mitigações offline

1.  [Ligue o disco do sistema a um VM de recuperação](troubleshoot-recovery-disks-portal-windows.md).

2.  Inicie uma ligação de ambiente de trabalho remoto ao VM de recuperação.

3.  Certifique-se de que o disco está sinalizado como **Online** na consola de Gestão de Discos. Note a letra de unidade que é atribuída ao disco do sistema anexo.

#### <a name="mitigation-1"></a>Mitigação 1

Ver [como Enable-Disable uma regra firewall num sistema operativo convidado](enable-disable-firewall-rule-guest-os.md).

#### <a name="mitigation-2"></a>Mitigação 2

1.  [Ligue o disco do sistema a um VM de recuperação](troubleshoot-recovery-disks-portal-windows.md).

2.  Inicie uma ligação de ambiente de trabalho remoto ao VM de recuperação.

3.  Depois de o disco do sistema ser ligado ao VM de recuperação, certifique-se de que o disco está sinalizado como **Online** na consola de Gestão de Discos. Note a letra de unidade que é atribuída ao disco de sos anexado.

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

5.  [Retire o disco do sistema e recobri o VM](troubleshoot-recovery-disks-portal-windows.md).

6.  Verifique se a questão está resolvida.
