---
title: Resolução de problemas Linux VM problemas de início devido a erros do FSTAB Microsoft Docs
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
ms.openlocfilehash: fd49993e6825c47bbae8f034715c03191e06ab2d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89441668"
---
# <a name="troubleshoot-linux-vm-starting-issues-due-to-fstab-errors"></a>Resolução de problemas Linux VM problemas de início devido a erros de fstab

Não é possível ligar-se a uma Máquina Virtual Azure Linux (VM) utilizando uma ligação Secure Shell (SSH). Quando executar a função ['Diagnóstico de Arranque'](./boot-diagnostics.md) no [portal Azure,](https://portal.azure.com/)vê entradas de registo que se assemelham aos seguintes exemplos:

## <a name="examples"></a>Exemplos

Seguem-se exemplos de possíveis erros.

### <a name="example-1-a-disk-is-mounted-by-the-scsi-id-instead-of-the-universally-unique-identifier-uuid"></a>Exemplo 1: Um disco é montado pelo SCSI ID em vez do identificador universalmente único (UUID)

```
[K[[1;31m TIME [0m] Timed out waiting for device dev-incorrect.device.
[[1;33mDEPEND[0m] Dependency failed for /data.
[[1;33mDEPEND[0m] Dependency failed for Local File Systems.
…
Welcome to emergency mode! After logging in, type "journalctl -xb" to viewsystem logs, "systemctl reboot" to reboot, "systemctl default" to try again to boot into default mode.
Give root password for maintenance
(or type Control-D to continue)
```

### <a name="example-2-an-unattached-device-is-missing-on-centos"></a>Exemplo 2: Falta um dispositivo não ligado no CentOS

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

Este problema pode ocorrer se a sintaxe da tabela de sistemas de ficheiros (fstab) estiver incorreta ou se um disco de dados necessário que é mapeado para uma entrada no ficheiro "/etc/fstab" não estiver ligado ao VM.

## <a name="resolution"></a>Resolução

Para resolver este problema, inicie o VM em modo de emergência utilizando a consola em série para Máquinas Virtuais Azure. Em seguida, utilize a ferramenta para reparar o sistema de ficheiros. Se a consola em série não estiver ativada no seu VM, vá à secção De Reparação da secção [offline VM.](#repair-the-vm-offline)

## <a name="use-the-serial-console"></a>Utilizar a consola de série

### <a name="using-single-user-mode"></a>Usando o modo de utilizador único

1. Ligue-se [à consola em série.](./serial-console-linux.md)
2. Utilize a consola em série para tomar o [modo único de utilizador no modo único de utilizador](serial-console-grub-single-user-mode.md)
3. Uma vez iniciado o vm no modo de utilizador único. Use o seu editor de texto favorito para abrir o ficheiro fstab. 

   ```
   # nano /etc/fstab
   ```

4. Reveja os sistemas de ficheiros listados. Cada linha no ficheiro fstab indica um sistema de ficheiros montado quando o VM começa. Para mais informações sobre a sintaxe do ficheiro fstab, executar o comando fstab man. Para resolver uma falha inicial, reveja cada linha para se certificar de que está correta tanto na estrutura como no conteúdo.

   > [!Note]
   > * Os campos em cada linha são separados por separadores ou espaços. As linhas em branco são ignoradas. Linhas que têm um sinal numer (#) como o primeiro personagem são comentários. As linhas comentadas podem permanecer no ficheiro fstab, mas não serão processadas. Recomendamos que comente linhas fstab sobre as quais não tem a certeza em vez de remover as linhas.
   > * Para que o VM recupere e inicie, as divisórias do sistema de ficheiros devem ser as únicas divisórias necessárias. O VM pode experimentar erros de aplicação sobre divisórias comentadas adicionais. No entanto, o VM deve começar sem as divisórias adicionais. Mais tarde, pode descoduir quaisquer linhas comentadas.
   > * Recomendamos que monte discos de dados em VMs Azure utilizando o UUID da partição do sistema de ficheiros. Por exemplo, executar o seguinte comando: ``/dev/sdc1: LABEL="cloudimg-rootfs" UUID="<UUID>" TYPE="ext4" PARTUUID="<PartUUID>"``
   > * Para determinar o UUID do sistema de ficheiros, executar o comando blkid. Para mais informações sobre a sintaxe, mande o comando blkid.
   > * A opção nofalil ajuda a garantir que o VM começa mesmo que o sistema de ficheiros esteja corrompido ou o sistema de ficheiros não exista no arranque. Recomendamos que utilize a opção nofalil no ficheiro fstab para permitir que o arranque continue após erros ocorridos em divisórias que não são necessárias para o arranque do VM.

5. Altere ou comente quaisquer linhas incorretas ou desnecessárias no ficheiro fstab para permitir que o VM comece corretamente.

6. Guarde as alterações no ficheiro fstab.

7. Reinicie o vm usando o comando abaixo.
   
   ```
   # reboot -f
   ```
> [!Note]
   > Também pode utilizar o comando "ctrl+x" que também reiniciaria o vm.


8. Se as entradas comentarem ou corrigirem ter sido bem sucedidas, o sistema deverá chegar a uma pontuação no portal. Verifique se pode ligar-se ao VM.

### <a name="using-root-password"></a>Usando senha de raiz

1. Ligue-se [à consola em série.](./serial-console-linux.md)
2. Iniciar sôs-in no sistema utilizando um utilizador local e uma palavra-passe.

   > [!Note]
   > Não é possível utilizar uma chave SSH para iniciar sinstrução no sistema na consola em série.

3. Procure o erro que indica que o disco não foi montado. No exemplo seguinte, o sistema tentava anexar um disco que já não estava presente:

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

4. Ligue-se ao VM utilizando a palavra-passe raiz (VMs baseados em chapéu vermelho).

5. Use o seu editor de texto favorito para abrir o ficheiro fstab. Depois de montado o disco, erguia o seguinte comando para Nano:

   ```
   $ nano /mnt/troubleshootingdisk/etc/fstab
   ```

6. Reveja os sistemas de ficheiros listados. Cada linha no ficheiro fstab indica um sistema de ficheiros montado quando o VM começa. Para mais informações sobre a sintaxe do ficheiro fstab, executar o comando fstab man. Para resolver uma falha inicial, reveja cada linha para se certificar de que está correta tanto na estrutura como no conteúdo.

   > [!Note]
   > * Os campos em cada linha são separados por separadores ou espaços. As linhas em branco são ignoradas. Linhas que têm um sinal numer (#) como o primeiro personagem são comentários. As linhas comentadas podem permanecer no ficheiro fstab, mas não serão processadas. Recomendamos que comente linhas fstab sobre as quais não tem a certeza em vez de remover as linhas.
   > * Para que o VM recupere e inicie, as divisórias do sistema de ficheiros devem ser as únicas divisórias necessárias. O VM pode experimentar erros de aplicação sobre divisórias comentadas adicionais. No entanto, o VM deve começar sem as divisórias adicionais. Mais tarde, pode descoduir quaisquer linhas comentadas.
   > * Recomendamos que monte discos de dados em VMs Azure utilizando o UUID da partição do sistema de ficheiros. Por exemplo, executar o seguinte comando: ``/dev/sdc1: LABEL="cloudimg-rootfs" UUID="<UUID>" TYPE="ext4" PARTUUID="<PartUUID>"``
   > * Para determinar o UUID do sistema de ficheiros, executar o comando blkid. Para mais informações sobre a sintaxe, mande o comando blkid.
   > * A opção nofalil ajuda a garantir que o VM começa mesmo que o sistema de ficheiros esteja corrompido ou o sistema de ficheiros não exista no arranque. Recomendamos que utilize a opção nofalil no ficheiro fstab para permitir que o arranque continue após erros ocorridos em divisórias que não são necessárias para o arranque do VM.

7. Altere ou comente quaisquer linhas incorretas ou desnecessárias no ficheiro fstab para permitir que o VM comece corretamente.

8. Guarde as alterações no ficheiro fstab.

9. Reinicie a máquina virtual.

10. Se as entradas comentarem ou corrigirem ter sido bem sucedidas, o sistema deverá chegar a uma pontuação no portal. Verifique se pode ligar-se ao VM.

11. Verifique os pontos de montagem quando testar qualquer alteração fstab executando o suporte -um comando. Se não houver erros, os seus pontos de montagem devem ser bons.

## <a name="repair-the-vm-offline"></a>Reparar o VM offline

1. Fixe o disco de sistema do VM como um disco de dados a um VM de recuperação (qualquer LM Linux em funcionamento). Para isso, pode utilizar [comandos CLI](./troubleshoot-recovery-disks-linux.md) ou automatizar a configuração do VM de recuperação utilizando os [comandos de reparação VM](repair-linux-vm-using-azure-virtual-machine-repair-commands.md).

2. Depois de montar o disco do sistema como um disco de dados no VM de recuperação, faça o back up do ficheiro fstab antes de escoar as alterações e, em seguida, siga os próximos passos para corrigir o ficheiro fstab.

3. Procura o erro que indica que o disco não foi montado. No exemplo seguinte, o sistema tentava anexar um disco que já não estava presente:

   ```output
   [DEPEND] Dependency failed for /datadisk1.
   [DEPEND] Dependency failed for Local File Systems.
   [DEPEND] Dependency failed for Relabel all filesystems, if necessary.
   [DEPEND] Dependency failed for Migrate local... structure to the new structure.
   Welcome to emergency mode! After logging in, type "journalctl -xb" to view system logs, "systemctl reboot" to reboot, "systemctl default" or ^D to try again to boot into default mode.
   Give root password for maintenance (or type Control-D to continue):
   ```

4. Ligue-se ao VM utilizando a palavra-passe raiz (VMs baseados em chapéu vermelho).

5. Use o seu editor de texto favorito para abrir o ficheiro fstab. Depois de montar o disco, erguia o seguinte comando para Nano. Certifique-se de que está a trabalhar no ficheiro fstab que está localizado no disco montado e não no ficheiro fstab que está no VM de resgate.

   ```
   $ nano /mnt/troubleshootingdisk/etc/fstab
   ```

6. Reveja os sistemas de ficheiros listados. Cada linha no ficheiro fstab indica um sistema de ficheiros montado quando o VM começa. Para mais informações sobre a sintaxe do ficheiro fstab, executar o comando fstab man. Para resolver uma falha inicial, reveja cada linha para se certificar de que está correta tanto na estrutura como no conteúdo.

   > [!Note]
   > * Os campos em cada linha são separados por separadores ou espaços. As linhas em branco são ignoradas. Linhas que têm um sinal numer (#) como o primeiro personagem são comentários. As linhas comentadas podem permanecer no ficheiro fstab, mas não serão processadas. Recomendamos que comente linhas fstab sobre as quais não tem a certeza em vez de remover as linhas.
   > * Para que o VM recupere e inicie, as divisórias do sistema de ficheiros devem ser as únicas divisórias necessárias. O VM pode experimentar erros de aplicação sobre divisórias comentadas adicionais. No entanto, o VM deve começar sem as divisórias adicionais. Mais tarde, pode descoduir quaisquer linhas comentadas.
   > * Recomendamos que monte discos de dados em VMs Azure utilizando o UUID da partição do sistema de ficheiros. Por exemplo, executar o seguinte comando: ``/dev/sdc1: LABEL="cloudimg-rootfs" UUID="<UUID>" TYPE="ext4" PARTUUID="<PartUUID>"``
   > * Para determinar o UUID do sistema de ficheiros, executar o comando blkid. Para mais informações sobre a sintaxe, mande o comando blkid. Note que o disco que pretende recuperar está agora montado num novo VM. Embora os UUIDs devam ser consistentes, os IDs de partição do dispositivo (por exemplo, "/dev/sda1") são diferentes neste VM. As divisórias do sistema de ficheiros do VM original que estão localizados num VHD não-sistema não estão disponíveis para a recuperação VM [utilizando comandos CLI](./troubleshoot-recovery-disks-linux.md).
   > * A opção nofalil ajuda a garantir que o VM começa mesmo que o sistema de ficheiros esteja corrompido ou o sistema de ficheiros não exista no arranque. Recomendamos que utilize a opção nofalil no ficheiro fstab para permitir que o arranque continue após erros ocorridos em divisórias que não são necessárias para o arranque do VM.

7. Altere ou comente quaisquer linhas incorretas ou desnecessárias no ficheiro fstab para permitir que o VM comece corretamente.

8. Guarde as alterações no ficheiro fstab.

9. Reinicie a máquina virtual ou reconstrua o VM original.

10. Se as entradas comentarem ou corrigirem ter sido bem sucedidas, o sistema deverá chegar a uma pontuação no portal. Verifique se pode ligar-se ao VM.

11. Verifique os pontos de montagem quando testar qualquer alteração fstab executando o suporte -um comando. Se não houver erros, os seus pontos de montagem devem ser bons.

12. Desmonte e desprete o disco rígido virtual original e, em seguida, crie um VM a partir do disco original do sistema. Para isso, pode utilizar [comandos CLI](troubleshoot-recovery-disks-linux.md) ou [comandos de reparação VM](repair-linux-vm-using-azure-virtual-machine-repair-commands.md) se os utilizar para criar o VM de recuperação.

13. Depois de voltar a criar o VM e pode ligá-lo através do SSH, tome as seguintes ações:
    * Reveja qualquer uma das linhas fstab que foram alteradas ou comentadas durante a recuperação.
    * Certifique-se de que está a utilizar o UUID e a opção nofale adequadamente.
    * Teste quaisquer alterações de fstab antes de reiniciar o VM. Para isso, utilize o seguinte comando: ``$ sudo mount -a``
    * Crie uma cópia adicional do ficheiro fstab corrigido para utilização em futuros cenários de recuperação.

## <a name="next-steps"></a>Passos seguintes

* [Resolução de problemas de um Linux VM, fixando o disco DE A uma VM de recuperação com o Azure CLI 2.0](./troubleshoot-recovery-disks-linux.md)
* [Resolução de problemas de um Linux VM, fixando o disco DE Aa a um VM de recuperação utilizando o portal Azure](./troubleshoot-recovery-disks-portal-linux.md)
