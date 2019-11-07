---
title: A VM do Linux é inicializada para a resgate grub
description: Falha na inicialização da máquina virtual porque a máquina virtual entrou em um console de resgate
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 08/28/2019
ms.author: tiag
ms.openlocfilehash: 29242b802dbbff4218506422293082a495c4d21e
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685124"
---
# <a name="linux-vm-boots-to-grub-rescue"></a>A VM do Linux é inicializada para a resgate grub

Identificamos que sua VM (máquina virtual) entrou em um console de resgate. O problema ocorre quando sua VM Linux tem alterações de kernel aplicadas recentemente, como uma atualização de kernel, e não está mais iniciando corretamente devido a erros de kernel durante o processo de inicialização. Durante o processo de inicialização, quando o carregador de inicialização tenta localizar o kernel do Linux e entrega o controle de inicialização para ele, a VM entra em um console de resgate quando a entrega falha.

Se você achar que não pode se conectar a uma VM no futuro, poderá exibir uma captura de tela de sua VM usando a folha diagnóstico de inicialização na portal do Azure. Isto pode ajudá-lo a diagnosticar o problema e determinar se a causa reside num erro de arranque semelhante.

## <a name="recommended-steps"></a>Passos recomendados

Siga as etapas de mitigação abaixo, dependendo do erro que você receber:

### <a name="error---unknown-filesystem"></a>Erro-sistema de arquivos desconhecido

* Se você estiver recebendo o sistema de arquivos de erro **desconhecido**, esse erro poderá resultar de uma corrupção do sistema de arquivos na partição de inicialização ou de uma configuração de kernel incorreta.

   * Para problemas do sistema de arquivos, siga as etapas no artigo [recuperação do Linux: não é possível SSH para VM Linux devido a erros do sistema de arquivos (fsck, inodes)](https://blogs.msdn.microsoft.com/linuxonazure/2016/09/13/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck-inodes/).
   * Para problemas de kernel, siga as etapas no artigo [recuperação do Linux: corrigindo manualmente problemas de não inicialização relacionados a problemas de kernel](https://blogs.msdn.microsoft.com/linuxonazure/2016/10/09/linux-recovery-manually-fixing-non-boot-issues-related-to-kernel-problems/)ou [recuperação do Linux: corrigindo problemas de não inicialização relacionados a problemas de kernel usando o chroot](https://blogs.msdn.microsoft.com/linuxonazure/2016/10/09/linux-recovery-fixing-non-boot-issues-related-to-kernel-problems-using-chroot/).
   
### <a name="error---file-not-found"></a>Erro-arquivo não encontrado

* Se você estiver recebendo o erro de erro **15: arquivo não encontrado ou disco RAM inicial** ou **arquivo initrd/initramfs não encontrado**, siga as etapas abaixo.

    * Para o arquivo ausente `/boot/grub2/grub.cfg` ou `initrd/initramfs` prossiga com o seguinte processo:

    1. Verifique se `/etc/default/grub` existe e se tem as configurações corretas/desejadas. Se você não souber quais são as configurações padrão, poderá verificar com uma VM em funcionamento.

    2. Em seguida, execute o seguinte comando para regenerar sua configuração: `grub2-mkconfig -o /boot/grub2/grub.cfg`

   * Se o arquivo ausente for `/boot/grub/menu.lst`, esse erro será para versões mais antigas do sistema operacional (**RHEL 6. x**, **CentOS 6. x** e **Ubuntu 14, 4**) para que os comandos pudessem ser diferentes. Você precisará criar um servidor antigo e testar para garantir que os comandos corretos sejam fornecidos.

### <a name="error---no-such-partition"></a>Erro-nenhuma partição desse tipo

* Se você estiver recebendo o erro **sem tal partição**, consulte [o cenário de caso: erro "sem partição" ao tentar iniciar a VM depois de tentar estender a unidade do sistema operacional](https://blogs.technet.microsoft.com/shwetanayak/2017/03/12/case-scenario-no-such-partition-error-while-trying-to-start-the-vm-after-attempting-to-extend-the-os-drive/).

### <a name="error---grubcfg-file-not-found"></a>Erro-arquivo grub. cfg não encontrado

* Se você estiver recebendo o erro **/boot/Grub2/grub.cfg arquivo não encontrado**, siga as etapas abaixo.

    * Para o arquivo ausente `/boot/grub2/grub.cfg` ou `initrd/initramfs` prossiga com o seguinte processo:

    1. Verifique se `/etc/default/grub` existe e se tem as configurações corretas/desejadas. Se você não souber quais são as configurações padrão, poderá verificar com uma VM em funcionamento.

    2. Em seguida, execute o seguinte comando para regenerar sua configuração: `grub2-mkconfig -o /boot/grub2/grub.cfg`.

   * Se o arquivo ausente for `/boot/grub/menu.lst`, esse erro será para versões mais antigas do sistema operacional (**RHEL 6. x**, **CentOS 6. x** e **Ubuntu 14, 4**) para que os comandos possam ser adiados. Crie um servidor antigo e teste-o para garantir que os comandos corretos sejam fornecidos.

## <a name="next-steps"></a>Passos seguintes

* [Visão geral do agente de máquina virtual do Azure](../extensions/agent-windows.md)
* [Recursos e extensões de máquina virtual para Windows](../extensions/features-windows.md)

