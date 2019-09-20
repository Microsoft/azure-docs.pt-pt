---
title: Como redefinir a senha local do Linux em VMs do Azure | Microsoft Docs
description: Apresente as etapas para redefinir a senha local do Linux na VM do Azure
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
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71153577"
---
# <a name="how-to-reset-local-linux-password-on-azure-vms"></a>Como redefinir a senha local do Linux em VMs do Azure

Este artigo apresenta vários métodos para redefinir senhas locais de VM (máquina virtual) do Linux. Se a conta de usuário tiver expirado ou se você quiser apenas criar uma nova conta, poderá usar os seguintes métodos para criar uma nova conta de administrador local e obter novamente o acesso à VM.

## <a name="symptoms"></a>Sintomas

Você não pode fazer logon na VM e recebe uma mensagem que indica que a senha usada está incorreta. Além disso, você não pode usar VMAgent para redefinir sua senha no portal do Azure.

## <a name="manual-password-reset-procedure"></a>Procedimento de redefinição de senha manual

> [!NOTE]
> As etapas a seguir não se aplicam à VM com disco não gerenciado.

1. Tire um instantâneo do disco do sistema operacional da VM afetada, crie um disco do instantâneo e, em seguida, anexe o disco a uma VM de solução de problemas. Para obter mais informações, consulte [solucionar problemas de uma VM do Windows anexando o disco do sistema operacional a uma VM de recuperação usando o portal do Azure](troubleshoot-recovery-disks-portal-linux.md).

2. Conecte-se à VM de solução de problemas usando Área de Trabalho Remota.

3.  Execute o seguinte comando SSH na VM de solução de problemas para se tornar um superusuário.

    ```bash
    sudo su
    ```

4.  Execute **fdisk-l** ou examine os logs do sistema para localizar o disco anexado recentemente. Localize o nome da unidade a ser montada. Em seguida, na VM temporal, examine o arquivo de log relevante.

    ```bash
    grep SCSI /var/log/kern.log (ubuntu)
    grep SCSI /var/log/messages (centos, suse, oracle)
    ```

    Veja a seguir um exemplo de saída do comando grep:

    ```bash
    kernel: [ 9707.100572] sd 3:0:0:0: [sdc] Attached SCSI disk
    ```

5.  Crie um ponto de montagem chamado **tempmount**.

    ```bash
    mkdir /tempmount
    ```

6.  Monte o disco do sistema operacional no ponto de montagem. Normalmente, você precisa montar *sdc1* ou *SDC2*. Isso dependerá da partição de hospedagem no diretório */etc* do disco de computador danificado.

    ```bash
    mount /dev/sdc1 /tempmount
    ```

7.  Crie cópias dos principais arquivos de credencial antes de fazer qualquer alteração:

    ```bash
    cp /etc/passwd /etc/passwd_orig    
    cp /etc/shadow /etc/shadow_orig    
    cp /tempmount/etc/passwd /etc/passwd
    cp /tempmount/etc/shadow /etc/shadow 
    cp /tempmount/etc/passwd /tempmount/etc/passwd_orig
    cp /tempmount/etc/shadow /tempmount/etc/shadow_orig
    ```

8.  Redefina a senha do usuário de que você precisa:

    ```bash
    passwd <<USER>> 
    ```

9.  Mova os arquivos modificados para o local correto no disco do computador danificado.

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

11. Em portal do Azure, desanexe o disco da VM de solução de problemas.

12. [Altere o disco do sistema operacional para a VM afetada](troubleshoot-recovery-disks-portal-linux.md#swap-the-os-disk-for-the-vm).

## <a name="next-steps"></a>Passos seguintes

* [Solucionar problemas de VM do Azure anexando o disco do sistema operacional a outra VM do Azure](https://social.technet.microsoft.com/wiki/contents/articles/18710.troubleshoot-azure-vm-by-attaching-os-disk-to-another-azure-vm.aspx)

* [CLI do Azure: Como excluir e reimplantar uma VM do VHD](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/azure-cli-how-to-delete-and-re-deploy-a-vm-from-vhd/)
