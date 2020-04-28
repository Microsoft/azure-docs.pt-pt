---
title: Como redefinir a senha local linux em VMs Azure [ Microsoft Docs
description: Introduza os passos para redefinir a senha linux local no Azure VM
services: virtual-machines-linux
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 08/20/2019
ms.author: delhan
ms.openlocfilehash: 83751538efe4f3d3af5928caa04b265b6c867442
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "71153577"
---
# <a name="how-to-reset-local-linux-password-on-azure-vms"></a>Como repor a palavra-passe local do Linux em VMs do Azure

Este artigo introduz vários métodos para redefinir senhas locais da Máquina Virtual linux (VM). Se a conta de utilizador estiver caducada ou pretender apenas criar uma nova conta, pode utilizar os seguintes métodos para criar uma nova conta de administração local e readquirir o acesso ao VM.

## <a name="symptoms"></a>Sintomas

Não pode iniciar sessão no VM e recebe uma mensagem que indica que a palavra-passe que usou está incorreta. Além disso, não é possível utilizar o VMAgent para redefinir a sua palavra-passe no portal Azure.

## <a name="manual-password-reset-procedure"></a>Procedimento manual de reset de palavra-passe

> [!NOTE]
> Os seguintes passos não se aplicam ao VM com disco não gerido.

1. Pegue uma foto para o disco OS do VM afetado, crie um disco a partir do instantâneo e, em seguida, prenda o disco a um VM de resolução de problemas. Para mais informações, consulte [Troubleshoot a Windows VM, ligando o disco OS a um VM de recuperação utilizando o portal Azure](troubleshoot-recovery-disks-portal-linux.md).

2. Ligue-se ao VM de resolução de problemas utilizando o Remote Desktop.

3.  Execute o seguinte comando SSH no VM de resolução de problemas para se tornar um super-utilizador.

    ```bash
    sudo su
    ```

4.  Executar **fdisk -l** ou olhar para os registos do sistema para encontrar o disco recém-ligado. Localize o nome da unidade para montar. Em seguida, no VM temporal, procure no ficheiro de registo relevante.

    ```bash
    grep SCSI /var/log/kern.log (ubuntu)
    grep SCSI /var/log/messages (centos, suse, oracle)
    ```

    Segue-se a saída do comando grep:

    ```bash
    kernel: [ 9707.100572] sd 3:0:0:0: [sdc] Attached SCSI disk
    ```

5.  Crie um ponto de montagem chamado **temporário.**

    ```bash
    mkdir /tempmount
    ```

6.  Monte o disco OS no ponto de montagem. Normalmente é necessário montar *sdc1* ou *sdc2*. Isto dependerá da partição de alojamento em */etc* diretório do disco da máquina partida.

    ```bash
    mount /dev/sdc1 /tempmount
    ```

7.  Crie cópias dos ficheiros credenciais fundamentais antes de efazer quaisquer alterações:

    ```bash
    cp /etc/passwd /etc/passwd_orig    
    cp /etc/shadow /etc/shadow_orig    
    cp /tempmount/etc/passwd /etc/passwd
    cp /tempmount/etc/shadow /etc/shadow 
    cp /tempmount/etc/passwd /tempmount/etc/passwd_orig
    cp /tempmount/etc/shadow /tempmount/etc/shadow_orig
    ```

8.  Redefinir a palavra-passe do utilizador de que necessita:

    ```bash
    passwd <<USER>> 
    ```

9.  Mova os ficheiros modificados para a localização correta no disco da máquina partida.

    ```bash
    cp /etc/passwd /tempmount/etc/passwd
    cp /etc/shadow /tempmount/etc/shadow
    cp /etc/passwd_orig /etc/passwd
    cp /etc/shadow_orig /etc/shadow
    ```

10. Volte para a raiz e desmonte o disco.

    ```bash
    cd /
    umount /tempmount
    ```

11. No portal Azure, retire o disco do VM de resolução de problemas.

12. [Altere o disco OS para o VM afetado](troubleshoot-recovery-disks-portal-linux.md#swap-the-os-disk-for-the-vm).

## <a name="next-steps"></a>Passos seguintes

* [Troubleshoot Azure VM ligando o disco OS a outro Azure VM](https://social.technet.microsoft.com/wiki/contents/articles/18710.troubleshoot-azure-vm-by-attaching-os-disk-to-another-azure-vm.aspx)

* [Azure CLI: Como eliminar e reutilizar um VM da VHD](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/azure-cli-how-to-delete-and-re-deploy-a-vm-from-vhd/)
