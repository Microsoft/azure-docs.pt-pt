---
title: Como repor o Linux palavra-passe local em VMs do Azure | Documentos da Microsoft
description: Introduzir os passos para redefinir a senha de Linux local na VM do Azure
services: virtual-machines-linux
documentationcenter: ''
author: Deland-Han
manager: cshepard
editor: ''
tags: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: d96d75f4f2623476f7af4e6eea930c1f2c503e3a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60306956"
---
# <a name="how-to-reset-local-linux-password-on-azure-vms"></a>Como repor o Linux palavra-passe local em VMs do Azure

Este artigo apresenta vários métodos para redefinir senhas locais da Máquina Virtual (VM) do Linux. Se a conta de utilizador expirou ou apenas pretender criar uma nova conta, pode utilizar os seguintes métodos para criar uma nova conta de administrador local e obter novamente acesso à VM.

## <a name="symptoms"></a>Sintomas

Não é possível iniciar sessão VM e receberá uma mensagem que indica que a palavra-passe que utilizou está incorreta. Além disso, é possível utilizar VMAgent para repor a palavra-passe no portal do Azure.

## <a name="manual-password-reset-procedure"></a>Procedimento de reposição de palavra-passe manual

1.  Elimine a VM e manter os discos anexados.

2.  Anexe a unidade do SO como um disco de dados para outra VM temporal na mesma localização.

3.  Execute o seguinte comando SSH na VM temporal para se tornar um Superutilizador.

    ```bash
    sudo su
    ```

4.  Execute **fdisk -l** ou consulte os registos de sistema para localizar o disco anexado recentemente. Localize o nome de unidade para montar. Em seguida, na temporal VM, procure no ficheiro de registo relevantes.

    ```bash
    grep SCSI /var/log/kern.log (ubuntu)
    grep SCSI /var/log/messages (centos, suse, oracle)
    ```

    Exemplo de saída do comando grep é o seguinte:

    ```bash
    kernel: [ 9707.100572] sd 3:0:0:0: [sdc] Attached SCSI disk
    ```

5.  Criar um ponto de montagem chamado **tempmount**.

    ```bash
    mkdir /tempmount
    ```

6.  Monte o disco de SO no ponto de montagem. Normalmente, precisa montar *sdc1* ou *sdc2*. Isso dependerá a partição de alojamento na *etc* diretório a partir do disco de máquina quebrada.

    ```bash
    mount /dev/sdc1 /tempmount
    ```

7.  Crie cópias das principais arquivos de credenciais antes de fazer alterações:

    ```bash
    cp /etc/passwd /etc/passwd_orig    
    cp /etc/shadow /etc/shadow_orig    
    cp /tempmount/etc/passwd /etc/passwd
    cp /tempmount/etc/shadow /etc/shadow 
    cp /tempmount/etc/passwd /tempmount/etc/passwd_orig
    cp /tempmount/etc/shadow /tempmount/etc/shadow_orig
    ```

8.  Reposição de palavra-passe do utilizador que tem de:

    ```bash
    passwd <<USER>> 
    ```

9.  Mova os ficheiros modificados para a localização correta no disco da máquina quebrada.

    ```bash
    cp /etc/passwd /tempmount/etc/passwd
    cp /etc/shadow /tempmount/etc/shadow
    cp /etc/passwd_orig /etc/passwd
    cp /etc/shadow_orig /etc/shadow
    ```

10. Volte para a raiz e desmontar o disco.

    ```bash
    cd /
    umount /tempmount
    ```

11. Desanexe o disco a partir do portal de gestão.

12. Recrie a VM.

## <a name="next-steps"></a>Passos Seguintes

* [Resolver problemas de VM do Azure ao anexar o disco do SO a outra VM do Azure](https://social.technet.microsoft.com/wiki/contents/articles/18710.troubleshoot-azure-vm-by-attaching-os-disk-to-another-azure-vm.aspx)

* [CLI do Azure: Como eliminar e voltar a implementar uma VM a partir de VHD](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/azure-cli-how-to-delete-and-re-deploy-a-vm-from-vhd/)
