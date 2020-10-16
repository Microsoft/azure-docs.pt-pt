---
title: Botas Linux VM para Grub Rescue
description: Máquina virtual falhou no arranque porque a máquina virtual entrou numa consola de resgate
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
ms.openlocfilehash: 5a2fd7fcfdae8559bfb39bffff7c73c7082a86aa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87543287"
---
# <a name="linux-vm-boots-to-grub-rescue"></a>Botas Linux VM para Grub Rescue

Identificámos que a sua Máquina Virtual (VM) entrou numa consola de resgate. O problema ocorre quando o seu Linux VM teve alterações no kernel recentemente aplicadas, como uma atualização de kernel, e já não está a funcionar corretamente devido a erros no kernel durante o processo de arranque. Durante o processo de arranque, quando o carregador de arranque tenta localizar o kernel Linux e entregar-lhe o controlo da bota, o VM entra numa consola de resgate quando a entrega falha.

Se descobrir que não pode ligar-se a um VM no futuro, pode visualizar uma imagem do seu VM utilizando a lâmina de diagnóstico de arranque no portal Azure. Isto pode ajudá-lo a diagnosticar o problema e determinar se a causa reside num erro de arranque semelhante.

## <a name="recommended-steps"></a>Passos recomendados

Siga os passos de mitigação abaixo, dependendo do erro que recebe:

### <a name="error---unknown-filesystem"></a>Erro - Sistema de ficheiros desconhecido

* Se estiver a obter o erro **Do sistema de ficheiros Desconhecido,** este erro pode resultar de uma corrupção no sistema de ficheiros na partição do arranque ou de uma configuração incorreta do núcleo.

   * Para problemas no sistema de ficheiros, siga os passos do artigo [Recuperação Linux: Não pode SSH a Linux VM devido a erros do sistema de ficheiros (fsck, inodes)](/archive/blogs/linuxonazure/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck-inodes).
   * Para questões de kernel, siga os passos no artigo [Como recuperar uma máquina virtual Azure Linux de problemas de arranque relacionados com o kernel](https://support.microsoft.com/help/4091524/how-recover-azure-linux-vm-from-kernel-related-boot-related-issues), ou [recuperação linux: Corrigir problemas de não-arranque relacionados com problemas de Kernel usando cromota](http://linuxonazure.azurewebsites.net/linux-recovery-fixing-non-boot-issues-related-to-kernel-problems-using-chroot/).
   
### <a name="error---file-not-found"></a>Erro - Ficheiro não encontrado

* Se estiver a obter o **erro Erro 15: Ficheiro não encontrado ou ficheiro inicial de disco RAM** ou **initrd/initramfs não encontrados,** siga os passos abaixo.

    * Para o ficheiro em falta `/boot/grub2/grub.cfg` ou `initrd/initramfs` proceda ao seguinte processo:

    1. Certifique-se de `/etc/default/grub` que existe e tem configurações corretas/desejadas. Se não souber quais são as definições predefinições, pode verificar com um VM em funcionamento.

    2. Em seguida, executar o seguinte comando para regenerar a sua configuração: `grub2-mkconfig -o /boot/grub2/grub.cfg`

   * Se o ficheiro em falta for `/boot/grub/menu.lst` , este erro destina-se às versões de SISTEMA mais antigas **(RHEL 6.x**, **Centos 6.x** e **Ubuntu 14.04**) para que os comandos possam diferir. Terá de rodar um servidor antigo e testar para garantir que os comandos corretos são fornecidos.

### <a name="error---no-such-partition"></a>Erro - Não há tal partição

* Se tiver o erro **Não existe tal partição**, consulte o Cenário de Caso : ["não existe tal partição" enquanto tenta iniciar o VM depois de tentar estender a unidade de SO](/archive/blogs/shwetanayak/case-scenario-no-such-partition-error-while-trying-to-start-the-vm-after-attempting-to-extend-the-os-drive).

### <a name="error---grubcfg-file-not-found"></a>Error - grub.cfg ficheiro não encontrado

* Se tiver o **ficheiro error/boot/grub2/grub2/grub.cfg não encontrado,** siga os passos abaixo.

    * Para o ficheiro em falta `/boot/grub2/grub.cfg` ou `initrd/initramfs` proceda ao seguinte processo:

    1. Certifique-se de `/etc/default/grub` que existe e tem configurações corretas/desejadas. Se não souber quais são as definições predefinições, pode verificar com um VM em funcionamento.

    2. Em seguida, executar o seguinte comando para regenerar a sua configuração: `grub2-mkconfig -o /boot/grub2/grub.cfg` .

   * Se o ficheiro em falta for `/boot/grub/menu.lst` , este erro destina-se às versões de SISTEMA mais antigas **(RHEL 6.x**, **Centos 6.x** e **Ubuntu 14.04**) para que os comandos possam adiar. Rode um servidor antigo e teste-o para garantir que os comandos corretos são fornecidos.

## <a name="next-steps"></a>Passos seguintes

* [Descrição geral do Agente da Máquina Virtual do Azure](../extensions/agent-windows.md)
* [Extensões e funcionalidades de máquinas virtuais para Windows](../extensions/features-windows.md)
