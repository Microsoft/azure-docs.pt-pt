---
title: Como redefinir a senha local do Linux nos VMs do Azure Microsoft Docs
description: Introduza os passos para redefinir a senha local do Linux no Azure VM
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
ms.openlocfilehash: c6bfd5b9ff3626593916533f27c5c2755cebcb13
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87028486"
---
# <a name="how-to-reset-local-linux-password-on-azure-vms"></a>Como repor a palavra-passe local do Linux em VMs do Azure

Este artigo introduz vários métodos para redefinir as senhas locais da Máquina Virtual Linux (VM). Se a conta de utilizador tiver expirado ou apenas pretender criar uma nova conta, pode utilizar os seguintes métodos para criar uma nova conta de administração local e recuperar o acesso ao VM.

## <a name="symptoms"></a>Sintomas

Não pode iniciar sessão no VM e receber uma mensagem que indica que a palavra-passe que usou está incorreta. Além disso, não é possível utilizar o VMAgent para redefinir a sua palavra-passe no portal Azure.

## <a name="manual-password-reset-procedure"></a>Procedimento de reset de palavra-passe manual

> [!NOTE]
> Os seguintes passos não se aplicam ao VM com disco não gerido.

1. Tire uma fotografia para o disco de SO do VM afetado, crie um disco a partir do instantâneo e, em seguida, prenda o disco a um VM de resolução de problemas. Para obter mais informações, consulte [Troubleshoot a Windows VM, fixando o disco OS a um VM de recuperação utilizando o portal Azure](troubleshoot-recovery-disks-portal-linux.md).

2. Ligue-se ao VM de resolução de problemas utilizando o Ambiente de Trabalho Remoto.

3.  Executar o seguinte comando SSH no VM de resolução de problemas para se tornar um super utilizador.

    ```bash
    sudo su
    ```

4.  Executar **fdisk -l** ou ver registos do sistema para encontrar o disco recém-ligado. Localize o nome de unidade para montar. Em seguida, no VM temporal, procure no ficheiro de registo relevante.

    ```bash
    grep SCSI /var/log/kern.log (ubuntu)
    grep SCSI /var/log/messages (centos, suse, oracle)
    ```

    Segue-se a saída exemplo do comando grep:

    ```bash
    kernel: [ 9707.100572] sd 3:0:0:0: [sdc] Attached SCSI disk
    ```

5.  Criar um ponto de montagem chamado **temperatura.**

    ```bash
    mkdir /tempmount
    ```

6.  Monte o disco DE no ponto de montagem. Normalmente, é necessário montar *sdc1* ou *sdc2*. Isto dependerá da partição do hospedeiro em */etc* diretório do disco da máquina partida.

    ```bash
    mount /dev/sdc1 /tempmount
    ```

7.  Criar cópias dos ficheiros de credenciais principais antes de esgrime quaisquer alterações:

    ```bash
    cp /etc/passwd /etc/passwd_orig    
    cp /etc/shadow /etc/shadow_orig    
    cp /tempmount/etc/passwd /etc/passwd
    cp /tempmount/etc/shadow /etc/shadow 
    cp /tempmount/etc/passwd /tempmount/etc/passwd_orig
    cp /tempmount/etc/shadow /tempmount/etc/shadow_orig
    ```

8.  Repor a palavra-passe do utilizador de que necessita:

    ```bash
    passwd <<USER>> 
    ```

9.  Mova os ficheiros modificados para a localização correta no disco da máquina avariada.

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

12. [Altere o disco DE para o VM afetado](troubleshoot-recovery-disks-portal-linux.md#swap-the-os-disk-for-the-vm).

## <a name="next-steps"></a>Passos seguintes

* [Resolução de problemas Azure VM, ligando o disco OS a outro Azure VM](https://social.technet.microsoft.com/wiki/contents/articles/18710.troubleshoot-azure-vm-by-attaching-os-disk-to-another-azure-vm.aspx)

* [Azure CLI: Como eliminar e re-implantar um VM da VHD](/archive/blogs/linuxonazure/azure-cli-how-to-delete-and-re-deploy-a-vm-from-vhd)
