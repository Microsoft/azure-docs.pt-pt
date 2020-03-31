---
title: Problemas de resolução de Problemas Linux VM iniciam problemas devido a erros no sistema de ficheiros Microsoft Docs
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
ms.openlocfilehash: 455cb1e0067217be6edcf665e8c07e8fcd684ab5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76842406"
---
# <a name="troubleshoot-linux-vm-starting-issues-due-to-file-system-errors"></a>Problemas de resolução de problemas De Linux VM problemas de partida devido a erros no sistema de ficheiros

Não é possível ligar-se a uma máquina virtual Azure Linux (VM) utilizando a Secure Shell (SSH). Quando executa a funcionalidade Boot Diagnostics no [portal Azure,](https://portal.azure.com/)vê entradas de registo que se assemelham aos seguintes exemplos.

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

Este problema pode ocorrer se o sistema de ficheiros não tiver sido desligado de forma limpa ou problemas relacionados com o armazenamento. Os problemas incluem erros de hardware ou software, problemas com controladores ou programas, erros de escrita, etc. É sempre importante ter uma cópia de segurança de dados críticos. As ferramentas que descrevem neste artigo podem ajudar a recuperar sistemas de ficheiros, mas é que a perda de dados ainda pode ocorrer.

O Linux tem vários verificadores de sistemade ficheiros disponíveis. As mais comuns para as distribuições em Azure são: [FSCK,](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/storage_administration_guide/fsck-fs-specific) [E2FSCK](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide/fsck-fs-specific)e [Xfs_repair](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide/xfsrepair).

## <a name="resolution"></a>Resolução

Para resolver este problema, insera o VM no modo de emergência utilizando a [consola em série](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux) e utilize essa ferramenta para reparar o sistema de ficheiros. Se a consola em série não estiver ativada no seu VM ou não funcionar, consulte a secção [de reparação da secção de sem-activação VM](#repair-the-vm-offline) deste artigo.

## <a name="use-the-serial-console"></a>Utilizar a consola de série

1. Ligue-se à consola em série.

   > [!Note]
   > Para obter mais informações sobre a utilização da consola em série para o Linux, consulte:
   > * [Utilize a consola em série para aceder ao MODO GRUB e ao modo de utilizador único](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console-grub-single-user-mode)
   > * [Utilize consola em série para chamadas SysRq e NMI](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-nmi-sysrq)

2. Selecione o botão de ícone Power e, em seguida, selecione Reiniciar VM. (Se a consola em série não estiver ativada ou não estiver ligada com sucesso, não verá o botão.)

   ![IMAGEM](./media/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck/restart-vm.png)

3. Insera o VM no modo de emergência.

4. Introduza a palavra-passe da sua conta raiz para iniciar sessão no modo de emergência.

5. Utilize xfs_repair com a opção -n para detetar os erros no sistema de ficheiros. Na amostra seguinte, assumimos que a partição do sistema é /dev/sda1. Substitua-o pelo valor adequado para o seu VM:

   ```
   xfs_repair -n /dev/sda1
   ```

6. Executar o seguinte comando para reparar o sistema de ficheiros:

   ```
   xfs_repair /dev/sda1
   ```

7. Se receber a mensagem de erro "ERROR: O sistema de ficheiros tem alterações valiosas de metadados num registo que precisa de ser reproduzido", criar um diretório temporário e montar o sistema de ficheiros:

   ```
   mkdir /temp
   mount /dev/sda1 /temp
   ```

8. Se o disco não conseguir montar, execute o comando xfs_repair com a opção -L (zeroing de tronco de força):

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

1. Fixe o disco de sistema do VM como um disco de dados a um VM de recuperação (qualquer VM Linux em funcionamento). Para tal, pode utilizar [comandos CLI](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-recovery-disks-linux) ou automatizar a configuração do VM de recuperação utilizando os comandos de [reparação VM](repair-linux-vm-using-azure-virtual-machine-repair-commands.md).

2. Localize a etiqueta de acionamento do disco do sistema que anexou. Neste caso, assumimos que a etiqueta do disco do sistema que anexou é /dev/sdc1. Substitua-o pelo valor adequado para o seu VM.

3. Utilize xfs_repair com a opção -n para detetar os erros no sistema de ficheiros.

   ```
   xfs_repair -n /dev/sdc1
   ```

4. Executar o seguinte comando para reparar o sistema de ficheiros:

   ```
   xfs_repair /dev/sdc1
   ```

5. Se receber a mensagem de erro "ERROR: O sistema de ficheiros tem alterações valiosas de metadados num registo que precisa de ser reproduzido", criar um diretório temporário e montar o sistema de ficheiros:

   ```
   mkdir /temp

   mount /dev/sdc1 /temp
   ```

   Se o disco não conseguir montar, execute o comando xfs_repair com a opção -L (zeroing de tronco de força):

   ```
   xfs_repair /dev/sdc1 -L
   ```

6. Em seguida, tente montar o sistema de ficheiros. Se o disco for montado com sucesso, receberá a seguinte saída:

   ```
   XFS (sdc1): Mounting V1 Filesystem
   
   XFS (sdc1): Ending clean mount
   ```

7. Desmonte e desaperte o disco rígido virtual original e, em seguida, crie um VM a partir do disco original do sistema. Para tal, pode utilizar [comandos CLI](troubleshoot-recovery-disks-linux.md) ou comandos de [reparação VM](repair-linux-vm-using-azure-virtual-machine-repair-commands.md) se os utilizar para criar o VM de recuperação.

8. Verifique se o problema está resolvido.

## <a name="next-steps"></a>Passos seguintes

* [Resolução de problemas um VM Linux, ligando o disco OS a um VM de recuperação com o Azure CLI 2.0](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-troubleshoot-recovery-disks)
* [Utilize o portal para anexar um disco de dados a um VM Linux](https://docs.microsoft.com/azure/virtual-machines/linux/attach-disk-portal)

