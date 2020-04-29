---
title: Problemas de jogo Do Linux VM iniciam problemas devido a erros de fstab Microsoft Docs
description: Explica porque é que o Linux VM não pode começar e como resolver o problema.
services: virtual-machines-linux
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 10/09/2019
ms.author: v-six
ms.openlocfilehash: 7e16eabc4f9572591eabd37b93258fcd783cce7e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80351157"
---
# <a name="troubleshoot-linux-vm-starting-issues-due-to-fstab-errors"></a>Problemas de jogo do Linux VM iniciam problemas devido a erros de fstab

Não é possível ligar-se a uma Máquina Virtual Azure Linux (VM) utilizando uma ligação Secure Shell (SSH). Quando executa a funcionalidade [Boot Diagnostics](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) no [portal Azure,](https://portal.azure.com/)vê entradas de registo que se assemelham aos seguintes exemplos:

## <a name="examples"></a>Exemplos

Seguem-se exemplos de possíveis erros.

### <a name="example-1-a-disk-is-mounted-by-the-scsi-id-instead-of-the-universally-unique-identifier-uuid"></a>Exemplo 1: Um disco é montado pelo ID SCSI em vez do identificador universalmente único (UUID)

```
[K[[1;31m TIME [0m] Timed out waiting for device dev-incorrect.device.
[[1;33mDEPEND[0m] Dependency failed for /data.
[[1;33mDEPEND[0m] Dependency failed for Local File Systems.
…
Welcome to emergency mode! After logging in, type "journalctl -xb" to viewsystem logs, "systemctl reboot" to reboot, "systemctl default" to try again to boot into default mode.
Give root password for maintenance
(or type Control-D to continue)
```

### <a name="example-2-an-unattached-device-is-missing-on-centos"></a>Exemplo 2: Falta um dispositivo desvinculado no CentOS

```
Checking file systems…
fsck from util-linux 2.19.1
Checking all file systems.
/dev/sdc1: nonexistent device ("nofail" fstab option may be used to skip this device)
/dev/sdd1: nonexistent device ("nofail" fstab option may be used to skip this device)
/dev/sde1: nonexistent device ("nofail" fstab option may be used to skip this device)

[/sbin/fsck.ext3 (1) — /CODE] sck.ext3 -a /dev/sdc1
fsck.ext3: No such file or directory while trying to open /dev/sdc1

/dev/sdc1:
The superblock could not be read or does not describe a correct ext2
filesystem. If the device is valid and it really contains an ext2
filesystem (and not swap or ufs or something else), then the superblock
is corrupt, and you might try running e2fsck with an alternate superblock:

e2fsck -b 8193 <device>

[/sbin/fsck.xfs (1) — /GLUSTERDISK] fsck.xfs -a /dev/sdd1
/sbin/fsck.xfs: /dev/sdd1 does not exist
[/sbin/fsck.ext3 (1) — /DATATEMP] fsck.ext3 -a /dev/sde1 fsck.ext3: No such file or directory while trying to open /dev/sde1
```

### <a name="example-3-a-vm-cannot-start-because-of-an-fstab-misconfiguration-or-because-the-disk-is-no-longer-attached"></a>Exemplo 3: Um VM não pode começar por causa de uma configuração errada ou porque o disco já não está ligado

```
The disk drive for /var/lib/mysql is not ready yet or not present.
Continue to wait, or Press S to skip mounting or M for manual recovery
```

### <a name="example-4-a-serial-log-entry-shows-an-incorrect-uuid"></a>Exemplo 4: Uma entrada de registo em série mostra um UUID incorreto

```
Checking filesystems
Checking all file systems.
[/sbin/fsck.ext4 (1) — /] fsck.ext4 -a /dev/sda1
/dev/sda1: clean, 70442/1905008 files, 800094/7608064 blocks
[/sbin/fsck.ext4 (1) — /datadrive] fsck.ext4 -a UUID="<UUID>"
fsck.ext4: Unable to resolve UUID="<UUID>"
[FAILED

*** An error occurred during the file system check.
*** Dropping you to a shell; the system will reboot
*** when you leave the shell.
*** Warning — SELinux is active
*** Disabling security enforcement for system recovery.
*** Run 'setenforce 1' to reenable.
type=1404 audit(1428047455.949:4): enforcing=0 old_enforcing=1 auid=<AUID> ses=4294967295
Give root password for maintenance
(or type Control-D to continue)
```

Este problema pode ocorrer se a sintaxe da tabela de sistemas de ficheiros (fstab) estiver incorreta ou se um disco de dados necessário que esteja mapeado para uma entrada no ficheiro "/etc/fstab" não estiver ligado ao VM.

## <a name="resolution"></a>Resolução

Para resolver este problema, inicie o VM em modo de emergência utilizando a consola em série para máquinas virtuais Azure. Em seguida, utilize a ferramenta para reparar o sistema de ficheiros. Se a consola em série não estiver ativada no seu VM, vá à secção [de reparação offline VM.](#repair-the-vm-offline)

## <a name="use-the-serial-console"></a>Utilizar a consola de série

### <a name="using-single-user-mode"></a>Utilização do modo de utilizador único

1. Ligue-se [à consola em série.](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux)
2. Utilize a consola de série para tomar o [modo de utilizador único single](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console-grub-single-user-mode) modo
3. Uma vez iniciado o vm no modo de utilizador único. Use o seu editor de texto favorito para abrir o ficheiro fstab. 

   ```
   # nano /etc/fstab
   ```

4. Reveja os sistemas de ficheiros listados. Cada linha no ficheiro fstab indica um sistema de ficheiros montado quando o VM começa. Para mais informações sobre a sintaxe do ficheiro fstab, execute o comando do homem. Para resolver uma falha inicial, reveja cada linha para se certificar de que está correta tanto na estrutura como no conteúdo.

   > [!Note]
   > * Os campos em cada linha são separados por separadores ou espaços. As linhas em branco são ignoradas. Linhas que têm um sinal de número (#) como o primeiro personagem são comentários. As linhas comentadas podem permanecer no ficheiro fstab, mas não serão processadas. Recomendamos que comente linhas de fstab que não tem a certeza em vez de remover as linhas.
   > * Para que o VM recupere e inicie, as divisórias do sistema de ficheiros devem ser as únicas divisórias necessárias. O VM pode experimentar erros de aplicação sobre divisórias comcomentários adicionais. No entanto, o VM deve começar sem as divisórias adicionais. Mais tarde, pode descomentar quaisquer linhas comentadas.
   > * Recomendamos que monte discos de dados em VMs Azure utilizando o UUID da partição do sistema de ficheiros. Por exemplo, executar o seguinte comando:``/dev/sdc1: LABEL="cloudimg-rootfs" UUID="<UUID>" TYPE="ext4" PARTUUID="<PartUUID>"``
   > * Para determinar o UUID do sistema de ficheiros, execute o comando blkid. Para mais informações sobre a sintaxe, mande o comando do homem blkid.
   > * A opção nofail ajuda a certificar-se de que o VM começa mesmo que o sistema de ficheiros seja corrompido ou o sistema de ficheiros não exista no arranque. Recomendamos que utilize a opção nofail no ficheiro fstab para permitir que o arranque continue após ocorrem erros em divisórias que não são necessárias para o arranque do VM.

5. Altere ou comente quaisquer linhas incorretas ou desnecessárias no ficheiro fstab para permitir que o VM comece corretamente.

6. Guarde as alterações no ficheiro fstab.

7. Reinicie o vm utilizando o comando abaixo.
   
   ```
   # reboot -f
   ```
> [!Note]
   > Também pode utilizar o comando "CTRL+x", que também reiniciaria o vm.


8. Se o comentário ou correção das entradas tiver sido bem sucedido, o sistema deve chegar a um aviso de batida no portal. Verifique se pode ligar-se ao VM.

### <a name="using-root-password"></a>Usando palavra-passe raiz

1. Ligue-se [à consola em série.](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux)
2. Inscreva-se no sistema utilizando um utilizador local e uma senha.

   > [!Note]
   > Não é possível utilizar uma chave SSH para iniciar sessão no sistema na consola em série.

3. Procure o erro que indica que o disco não foi montado. No exemplo seguinte, o sistema estava a tentar anexar um disco que já não estava presente:

   ```
   [DEPEND] Dependency failed for /datadisk1.
   [DEPEND] Dependency failed for Local File Systems.
   [DEPEND] Dependency failed for Relabel all filesystems, if necessary.
   [DEPEND] Dependency failed for Migrate local... structure to the new structure.
   Welcome to emergency mode! After logging in, type "journalctl -xb" to view
   system logs, "systemctl reboot" to reboot, "systemctl default" or ^D to try again to boot into default mode.
   Give root password for maintenance
   (or type Control-D to continue):
   ```

4. Ligue-se ao VM utilizando a palavra-passe de raiz (VMs baseados em Chapéu Vermelho).

5. Use o seu editor de texto favorito para abrir o ficheiro fstab. Depois de montado o disco, execute o seguinte comando para Nano:

   ```
   $ nano /mnt/troubleshootingdisk/etc/fstab
   ```

6. Reveja os sistemas de ficheiros listados. Cada linha no ficheiro fstab indica um sistema de ficheiros montado quando o VM começa. Para mais informações sobre a sintaxe do ficheiro fstab, execute o comando do homem. Para resolver uma falha inicial, reveja cada linha para se certificar de que está correta tanto na estrutura como no conteúdo.

   > [!Note]
   > * Os campos em cada linha são separados por separadores ou espaços. As linhas em branco são ignoradas. Linhas que têm um sinal de número (#) como o primeiro personagem são comentários. As linhas comentadas podem permanecer no ficheiro fstab, mas não serão processadas. Recomendamos que comente linhas de fstab que não tem a certeza em vez de remover as linhas.
   > * Para que o VM recupere e inicie, as divisórias do sistema de ficheiros devem ser as únicas divisórias necessárias. O VM pode experimentar erros de aplicação sobre divisórias comcomentários adicionais. No entanto, o VM deve começar sem as divisórias adicionais. Mais tarde, pode descomentar quaisquer linhas comentadas.
   > * Recomendamos que monte discos de dados em VMs Azure utilizando o UUID da partição do sistema de ficheiros. Por exemplo, executar o seguinte comando:``/dev/sdc1: LABEL="cloudimg-rootfs" UUID="<UUID>" TYPE="ext4" PARTUUID="<PartUUID>"``
   > * Para determinar o UUID do sistema de ficheiros, execute o comando blkid. Para mais informações sobre a sintaxe, mande o comando do homem blkid.
   > * A opção nofail ajuda a certificar-se de que o VM começa mesmo que o sistema de ficheiros seja corrompido ou o sistema de ficheiros não exista no arranque. Recomendamos que utilize a opção nofail no ficheiro fstab para permitir que o arranque continue após ocorrem erros em divisórias que não são necessárias para o arranque do VM.

7. Altere ou comente quaisquer linhas incorretas ou desnecessárias no ficheiro fstab para permitir que o VM comece corretamente.

8. Guarde as alterações no ficheiro fstab.

9. Reiniciar a máquina virtual.

10. Se o comentário ou correção das entradas tiver sido bem sucedido, o sistema deve chegar a um aviso de batida no portal. Verifique se pode ligar-se ao VM.

11. Verifique os pontos de montagem quando testar qualquer alteração de fstab, executando a montagem – um comando. Se não houver erros, os seus pontos de montagem devem ser bons.

## <a name="repair-the-vm-offline"></a>Reparar o VM offline

1. Fixe o disco de sistema do VM como um disco de dados a um VM de recuperação (qualquer VM Linux em funcionamento). Para tal, pode utilizar [comandos CLI](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-recovery-disks-linux) ou automatizar a configuração do VM de recuperação utilizando os comandos de [reparação VM](repair-linux-vm-using-azure-virtual-machine-repair-commands.md).

2. Depois de montar o disco do sistema como um disco de dados no VM de recuperação, faça o back up do ficheiro fstab antes de fazer alterações e, em seguida, siga os próximos passos para corrigir o ficheiro fstab.

3.    Procure o erro que indica que o disco não foi montado. No exemplo seguinte, o sistema estava a tentar anexar um disco que já não estava presente:

    ```
    [DEPEND] Dependency failed for /datadisk1.
    [DEPEND] Dependency failed for Local File Systems.
    [DEPEND] Dependency failed for Relabel all filesystems, if necessary.
    [DEPEND] Dependency failed for Migrate local... structure to the new structure.
    Welcome to emergency mode! After logging in, type "journalctl -xb" to view system logs, "systemctl reboot" to reboot, "systemctl default" or ^D to try again to boot into default mode.
    Give root password for maintenance (or type Control-D to continue):
    ```

4. Ligue-se ao VM utilizando a palavra-passe de raiz (VMs baseados em Chapéu Vermelho).

5. Use o seu editor de texto favorito para abrir o ficheiro fstab. Depois de montado o disco, execute o seguinte comando para Nano. Certifique-se de que está a trabalhar no ficheiro fstab que está localizado no disco montado e não no ficheiro fstab que está no VM de resgate.

   ```
   $ nano /mnt/troubleshootingdisk/etc/fstab
   ```

6. Reveja os sistemas de ficheiros listados. Cada linha no ficheiro fstab indica um sistema de ficheiros montado quando o VM começa. Para mais informações sobre a sintaxe do ficheiro fstab, execute o comando do homem. Para resolver uma falha inicial, reveja cada linha para se certificar de que está correta tanto na estrutura como no conteúdo.

   > [!Note]
   > * Os campos em cada linha são separados por separadores ou espaços. As linhas em branco são ignoradas. Linhas que têm um sinal de número (#) como o primeiro personagem são comentários. As linhas comentadas podem permanecer no ficheiro fstab, mas não serão processadas. Recomendamos que comente linhas de fstab que não tem a certeza em vez de remover as linhas.
   > * Para que o VM recupere e inicie, as divisórias do sistema de ficheiros devem ser as únicas divisórias necessárias. O VM pode experimentar erros de aplicação sobre divisórias comcomentários adicionais. No entanto, o VM deve começar sem as divisórias adicionais. Mais tarde, pode descomentar quaisquer linhas comentadas.
   > * Recomendamos que monte discos de dados em VMs Azure utilizando o UUID da partição do sistema de ficheiros. Por exemplo, executar o seguinte comando:``/dev/sdc1: LABEL="cloudimg-rootfs" UUID="<UUID>" TYPE="ext4" PARTUUID="<PartUUID>"``
   > * Para determinar o UUID do sistema de ficheiros, execute o comando blkid. Para mais informações sobre a sintaxe, mande o comando do homem blkid. Note que o disco que pretende recuperar está agora montado num novo VM. Embora os UUIDs devam ser consistentes, os IDs de partição do dispositivo (por exemplo, "/dev/sda1") são diferentes neste VM. As divisórias do sistema de ficheiros do VM original que estão localizados num VHD não-sistema não estão disponíveis para o VM de recuperação [utilizando comandos CLI](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-recovery-disks-linux).
   > * A opção nofail ajuda a certificar-se de que o VM começa mesmo que o sistema de ficheiros seja corrompido ou o sistema de ficheiros não exista no arranque. Recomendamos que utilize a opção nofail no ficheiro fstab para permitir que o arranque continue após ocorrem erros em divisórias que não são necessárias para o arranque do VM.

7. Altere ou comente quaisquer linhas incorretas ou desnecessárias no ficheiro fstab para permitir que o VM comece corretamente.

8. Guarde as alterações no ficheiro fstab.

9. Reiniciar a máquina virtual ou reconstruir o VM original.

10. Se o comentário ou correção das entradas tiver sido bem sucedido, o sistema deve chegar a um aviso de batida no portal. Verifique se pode ligar-se ao VM.

11. Verifique os pontos de montagem quando testar qualquer alteração de fstab, executando a montagem – um comando. Se não houver erros, os seus pontos de montagem devem ser bons.

12. Desmonte e desaperte o disco rígido virtual original e, em seguida, crie um VM a partir do disco original do sistema. Para tal, pode utilizar [comandos CLI](troubleshoot-recovery-disks-linux.md) ou comandos de [reparação VM](repair-linux-vm-using-azure-virtual-machine-repair-commands.md) se os utilizar para criar o VM de recuperação.

13. Depois de criar o VM novamente e pode ligá-lo através do SSH, tome as seguintes ações:
    * Reveja qualquer uma das linhas de fstab que foram alteradas ou comentadas durante a recuperação.
    * Certifique-se de que está a utilizar o UUID e a opção nofail adequadamente.
    * Teste quaisquer alterações antes de reiniciar o VM. Para tal, utilize o seguinte comando:``$ sudo mount -a``
    * Crie uma cópia adicional do ficheiro de fstab corrigido para utilização em futuros cenários de recuperação.

## <a name="next-steps"></a>Passos seguintes

* [Resolução de problemas um VM Linux, ligando o disco OS a um VM de recuperação com o Azure CLI 2.0](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-troubleshoot-recovery-disks)
* [Troubleshoot a Linux VM ligando o disco OS a um VM de recuperação usando o portal Azure](https://docs.microsoft.com/azure/virtual-machines/linux/troubleshoot-recovery-disks-portal)

