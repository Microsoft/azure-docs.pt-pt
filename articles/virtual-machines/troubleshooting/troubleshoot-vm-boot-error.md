---
title: Botas VM Linux para O Resgate Grub
description: Máquina virtual não arrancou porque a máquina virtual entrou numa consola de resgate
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 08/28/2019
ms.author: tiag
ms.openlocfilehash: c24a840716841d04537ac5b77bcaf26fca4b78cf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77561954"
---
# <a name="linux-vm-boots-to-grub-rescue"></a>Botas VM Linux para O Resgate Grub

Identificamos que a sua Máquina Virtual (VM) entrou numa consola de resgate. O problema ocorre quando o seu VM Linux teve alterações no kernel aplicadas recentemente, como uma atualização de kernel, e já não está a funcionar corretamente devido a erros de kernel durante o processo de arranque. Durante o processo de arranque, quando o carregador de botões tenta localizar o kernel Linux e entregar-lhe o controlo da bota, o VM entra numa consola de resgate quando a entrega falha.

Se descobrir que não pode ligar-se a um VM no futuro, poderá ver uma imagem do seu VM utilizando a lâmina de diagnóstico de arranque no portal Azure. Isto pode ajudá-lo a diagnosticar o problema e determinar se a causa reside num erro de arranque semelhante.

## <a name="recommended-steps"></a>Passos recomendados

Siga os passos de mitigação abaixo, dependendo do erro que recebe:

### <a name="error---unknown-filesystem"></a>Error - Sistema de ficheiros desconhecido

* Se estiver a obter o erro Sistema de **ficheiros Desconhecido,** este erro pode resultar de uma corrupção no sistema de ficheiros na partição do arranque ou de uma configuração incorreta do núcleo.

   * Para problemas no sistema de ficheiros, siga os passos do artigo [Linux Recovery: Can SSH to Linux VM devido a erros do sistema de ficheiros (fsck, inodes)](https://blogs.msdn.microsoft.com/linuxonazure/2016/09/13/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck-inodes/).
   * Para problemas de kernel, siga os passos do artigo [Recuperação linux: corrigir manualmente problemas de não-arranque relacionados com problemas kernel](https://blogs.msdn.microsoft.com/linuxonazure/2016/10/09/linux-recovery-manually-fixing-non-boot-issues-related-to-kernel-problems/), ou [Recuperação linux: corrigir problemas de não-arranque relacionados com problemas kernel usando chroot](https://blogs.msdn.microsoft.com/linuxonazure/2016/10/09/linux-recovery-fixing-non-boot-issues-related-to-kernel-problems-using-chroot/).
   
### <a name="error---file-not-found"></a>Erro - Ficheiro não encontrado

* Se estiver a obter o **erro Erro 15: Ficheiro não encontrado ou disco RAM inicial** ou ficheiro **initramfs/initramfs não encontrados,** siga os passos abaixo.

    * Para o `/boot/grub2/grub.cfg` ficheiro `initrd/initramfs` em falta ou prossiga com o seguinte processo:

    1. Certifique-se de `/etc/default/grub` existir e tem definições corretas/desejadas. Se não souber quais são as definições predefinidas, pode verificar com um VM de trabalho.

    2. Em seguida, executar o seguinte comando para regenerar a sua configuração:`grub2-mkconfig -o /boot/grub2/grub.cfg`

   * Se o ficheiro `/boot/grub/menu.lst`em falta for , este erro é para versões mais antigas de SO **(RHEL 6.x,** **Centos 6.x** e **Ubuntu 14.04**) para que os comandos possam diferir. Terá de girar um servidor antigo e testar para garantir que os comandos corretos são fornecidos.

### <a name="error---no-such-partition"></a>Erro - Não há tal partição

* Se estiver a obter o erro **Não há tal partição,** consulte o Cenário de [Caso : erro "não existe tal partição" enquanto tenta iniciar o VM depois](https://blogs.technet.microsoft.com/shwetanayak/2017/03/12/case-scenario-no-such-partition-error-while-trying-to-start-the-vm-after-attempting-to-extend-the-os-drive/)de tentar estender a unidade de SO .

### <a name="error---grubcfg-file-not-found"></a>Erro - ficheiro grub.cfg não encontrado

* Se estiver a obter o **erro /boot/grub2/grub.cfg ficheiro não encontrado,** siga os passos abaixo.

    * Para o `/boot/grub2/grub.cfg` ficheiro `initrd/initramfs` em falta ou prossiga com o seguinte processo:

    1. Certifique-se de `/etc/default/grub` existir e tem definições corretas/desejadas. Se não souber quais são as definições predefinidas, pode verificar com um VM de trabalho.

    2. Em seguida, execute o seguinte `grub2-mkconfig -o /boot/grub2/grub.cfg`comando para regenerar a sua configuração: .

   * Se o ficheiro `/boot/grub/menu.lst`em falta for , este erro é para versões mais antigas de SO **(RHEL 6.x,** **Centos 6.x** e **Ubuntu 14.04**) para que os comandos possam adiar. Rode um servidor antigo e teste-o para garantir que os comandos corretos são fornecidos.

## <a name="next-steps"></a>Passos seguintes

* [Descrição geral do Agente da Máquina Virtual do Azure](../extensions/agent-windows.md)
* [Extensões e funcionalidades de máquinavirtual para Windows](../extensions/features-windows.md)

