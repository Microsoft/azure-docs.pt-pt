---
title: Resolução de problemas Linux VM problemas de início devido a erros no sistema de ficheiros Microsoft Docs
description: Saiba como resolver problemas de início do Linux VM devido a erros no sistema de ficheiros
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
ms.openlocfilehash: 8f12b88a0ddbc6ae31f40ab31b0126e4fd66b1a5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91325951"
---
# <a name="troubleshoot-linux-vm-starting-issues-due-to-file-system-errors"></a>Resolução de problemas Linux VM problemas iniciais devido a erros no sistema de ficheiros

Não é possível ligar-se a uma máquina virtual Azure Linux (VM) utilizando a Secure Shell (SSH). Quando executar a funcionalidade 'Boot Diagnostics' no [portal Azure,](https://portal.azure.com/)vê entradas de registo que se assemelham aos seguintes exemplos.

## <a name="examples"></a>Exemplos

Seguem-se exemplos de possíveis erros.

### <a name="example-1"></a>Exemplo 1 

```
Checking all file systems.
[/sbin/fsck.ext4 (1) — /] fsck.ext4 -a /dev/sda1
/dev/sda1 contains a file system with errors, check forced.
/dev/sda1: Inodes that were part of a corrupted orphan linked list found.
/dev/sda1: UNEXPECTED INCONSISTENCY; RUN fsck MANUALLY
```

### <a name="example-2"></a>Exemplo 2

```
EXT4-fs (sda1): INFO: recovery required on readonly filesystem
EXT4-fs (sda1): write access will be enabled during recovery
EXT4-fs warning (device sda1): ext4_clear_journal_err:4531: Filesystem error recorded from previous mount: IO failure
EXT4-fs warning (device sda1): ext4_clear_journal_err:4532: Making fs in need of filesystem check.
```

### <a name="example-3"></a>Exemplo 3

```
[  14.252404] EXT4-fs (sda1): Couldn’t remount RDWR because of unprocessed orphan inode list.  Please unmount/remount instead
An error occurred while mounting /.
```

### <a name="example-4"></a>Exemplo 4 

Este exemplo é causado por um fsck limpo. Neste caso, existem também discos de dados adicionais ligados ao VM (/dev/sdc1 e /dev/sde1).

```
Checking all file systems. 
[/sbin/fsck.ext4 (1) — /] fsck.ext4 -a /dev/sda1
/dev/sda1: clean, 65405/1905008 files, 732749/7608064 blocks
[/sbin/fsck.ext4 (1) — /tmp] fsck.ext4 -a /dev/sdc1
[/sbin/fsck.ext4 (2) — /backup] fsck.ext4 -a /dev/sde1
/dev/sdc1: clean, 12/1048576 files, 109842/4192957 blocks
/dev/sde1 : clean, 51/67043328 files, 4259482/268173037 blocks
```

Este problema pode ocorrer se o sistema de ficheiros não tiver sido desligado de forma limpa ou problemas relacionados com o armazenamento. Os problemas incluem erros de hardware ou software, problemas com motoristas ou programas, erros de escrita, etc. É sempre importante ter uma cópia de segurança de dados críticos. As ferramentas que descrevem neste artigo podem ajudar a recuperar os sistemas de ficheiros, mas é a perda de dados ainda pode ocorrer.

Linux tem vários verificadores do sistema de ficheiros disponíveis. As mais comuns para as distribuições em Azure são: [FSCK,](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/storage_administration_guide/fsck-fs-specific) [E2FSCK,](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide/fsck-fs-specific)e [Xfs_repair](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide/xfsrepair).

## <a name="resolution"></a>Resolução

Para resolver este problema, insira o VM em modo de emergência utilizando a [consola em série](./serial-console-linux.md) e utilize essa ferramenta para reparar o sistema de ficheiros. Se a consola em série não estiver ativada no seu VM ou não funcionar, consulte a secção [offline VM](#repair-the-vm-offline) deste artigo.

## <a name="use-the-serial-console"></a>Utilizar a consola de série

1. Ligue-se à consola em série.

   > [!Note]
   > Para obter mais informações sobre a utilização da consola em série para Linux, consulte:
   > * [Utilize consola em série para aceder ao GRUB e ao modo de utilizador único](serial-console-grub-single-user-mode.md)
   > * [Utilize consola em série para chamadas SysRq e NMI](./serial-console-nmi-sysrq.md)

2. Selecione o botão de ícone de alimentação e, em seguida, selecione Restart VM. (Se a consola em série não estiver ativada ou não ligada com sucesso, não verá o botão.)

   ![IMAGEM](./media/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck/restart-vm.png)

3. Iniciar o VM no modo de emergência.

4. Introduza a palavra-passe da sua conta de raiz para iniciar scontabilidade no modo de emergência.

5. Utilize xfs_repair com a opção -n para detetar os erros no sistema de ficheiros. Na amostra seguinte, assumimos que a partição do sistema é /dev/sda1. Substitua-o pelo valor adequado para o seu VM:

   ```
   xfs_repair -n /dev/sda1
   ```

6. Executar o seguinte comando para reparar o sistema de ficheiros:

   ```
   xfs_repair /dev/sda1
   ```

7. Se receber a mensagem de erro "ERROR: The filesystem has valuable medata changes in a log which need to changeed", crie um diretório temporário e monte o sistema de ficheiros:

   ```
   mkdir /temp
   mount /dev/sda1 /temp
   ```

8. Se o disco não montar, executar o comando xfs_repair com a opção -L (fazer o zero do registo de força):

   ```
   xfs_repair /dev/sda1 -L
   ```

9. Em seguida, tente montar o sistema de ficheiros. Se o disco for montado com sucesso, receberá a seguinte saída:
 
   ```
   XFS (sda1): Mounting V1 Filesystem
   XFS (sda1): Ending clean mount
   ```

10. Reinicie o VM e verifique se o problema está resolvido.

    ```
    Reboot -f
    ```

## <a name="repair-the-vm-offline"></a>Reparar o VM offline

1. Fixe o disco de sistema do VM como um disco de dados a um VM de recuperação (qualquer LM Linux em funcionamento). Para isso, pode utilizar [comandos CLI](./troubleshoot-recovery-disks-linux.md) ou automatizar a configuração do VM de recuperação utilizando os [comandos de reparação VM](repair-linux-vm-using-azure-virtual-machine-repair-commands.md).

2. Localize a etiqueta de acionamento do disco de sistema que anexou. Neste caso, assumimos que a etiqueta do disco do sistema que anexou é /dev/sdc1. Substitua-o pelo valor adequado para o seu VM.

3. Utilize xfs_repair com a opção -n para detetar os erros no sistema de ficheiros.

   ```
   xfs_repair -n /dev/sdc1
   ```

4. Executar o seguinte comando para reparar o sistema de ficheiros:

   ```
   xfs_repair /dev/sdc1
   ```

5. Se receber a mensagem de erro "ERROR: The filesystem has valuable medata changes in a log which need to changeed", crie um diretório temporário e monte o sistema de ficheiros:

   ```
   mkdir /temp

   mount /dev/sdc1 /temp
   ```

   Se o disco não montar, executar o comando xfs_repair com a opção -L (fazer o zero do registo de força):

   ```
   xfs_repair /dev/sdc1 -L
   ```

6. Em seguida, tente montar o sistema de ficheiros. Se o disco for montado com sucesso, receberá a seguinte saída:

   ```
   XFS (sdc1): Mounting V1 Filesystem
   
   XFS (sdc1): Ending clean mount
   ```

7. Desmonte e desprete o disco rígido virtual original e, em seguida, crie um VM a partir do disco original do sistema. Para isso, pode utilizar [comandos CLI](troubleshoot-recovery-disks-linux.md) ou [comandos de reparação VM](repair-linux-vm-using-azure-virtual-machine-repair-commands.md) se os utilizar para criar o VM de recuperação.

8. Verifique se o problema está resolvido.

## <a name="next-steps"></a>Passos seguintes

* [Resolução de problemas de um Linux VM, fixando o disco DE A uma VM de recuperação com o Azure CLI 2.0](./troubleshoot-recovery-disks-linux.md)
* [Utilizar o portal para anexar um disco de dados a uma VM do Linux](../linux/attach-disk-portal.md)
