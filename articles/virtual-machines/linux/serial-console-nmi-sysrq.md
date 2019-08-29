---
title: Console serial do Azure para chamadas SysRq e NMI | Microsoft Docs
description: Usando o console serial para chamadas SysRq e NMI em máquinas virtuais do Azure.
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/14/2018
ms.author: alsin
ms.openlocfilehash: 81fb9f99f4f7e4f77b39855445639369f65f0966
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70091320"
---
# <a name="use-serial-console-for-sysrq-and-nmi-calls"></a>Usar o console serial para chamadas SysRq e NMI

## <a name="system-request-sysrq"></a>Solicitação do sistema (SysRq)
Um SysRq é uma sequência de chaves compreendidas pelo kernel do sistema operacional Linux, que pode disparar um conjunto de ações predefinidas. Esses comandos são frequentemente usados quando a solução de problemas ou recuperação de máquina virtual não puder ser executada por meio da administração tradicional (por exemplo, se a VM não estiver respondendo). Usar o recurso SysRq do console serial do Azure imitará o pressionamento da tecla SysRq e dos caracteres inseridos em um teclado físico.

Depois que a sequência SysRq for entregue, a configuração do kernel controlará como o sistema responde. Para obter informações sobre como habilitar e desabilitar o SysRq, consulte o *Guia de administração do SysRq* [redução](https://aka.ms/linuxsysrq)de [texto](https://aka.ms/kernelorgsysreqdoc) | .  

O console serial do Azure pode ser usado para enviar um SysRq para uma máquina virtual do Azure usando o ícone de teclado na barra de comandos mostrada abaixo.

![](../media/virtual-machines-serial-console/virtual-machine-serial-console-command-menu.jpg)

Escolher "Enviar comando SysRq" abrirá uma caixa de diálogo, que fornecerá opções de SysRq comuns ou aceitará uma sequência de comandos SysRq inseridos na caixa de diálogo.  Isso permite que a série de SysRq execute uma operação de alto nível, como uma reinicialização segura usando `REISUB`:.

![](../media/virtual-machines-serial-console/virtual-machine-serial-console-sysreq_UI.png)

O comando SysRq não pode ser usado em máquinas virtuais que foram interrompidas ou cujo kernel está em um estado que não responde. (por exemplo, um pane no kernel).

### <a name="enable-sysrq"></a>Habilitar SysRq 
Conforme descrito no *Guia do administrador do SysRq* acima, o SysRq pode ser configurado de modo que todos, nenhum ou apenas alguns comandos estejam disponíveis. Você pode habilitar todos os comandos SysRq usando a etapa abaixo, mas ele não sobreviver a uma reinicialização:
```
echo "1" >/proc/sys/kernel/sysrq
```
Para tornar a configuração SysReq persistente, você pode fazer o seguinte para habilitar todos os comandos SysRq
1. Adicionando esta linha a */etc/sysctl.conf* <br>
    `kernel.sysrq = 1`
1. Reinicializando ou atualizando sysctl executando <br>
    `sysctl -p`

### <a name="command-keys"></a>Chaves de comando 
No guia do administrador do SysRq acima:

|Comando| Função
| ------| ----------- |
|``b``  |   O reinicializará imediatamente o sistema sem sincronizar ou desmontar os discos.
|``c``  |   Executará uma falha do sistema por uma desreferência de ponteiro nulo. Se configurado, um despejo de falha será feito.
|``d``  |   Mostra todos os bloqueios que são mantidos.
|``e``  |   Envie um SIGTERM para todos os processos, exceto para init.
|``f``  |   Chamará a Killer de OOM para eliminar um processo de exagero de memória, mas não entrará em pânico se nada puder ser eliminado.
|``g``  |   Usado por KGDB (depurador de kernel)
|``h``  |   Exibirá a ajuda (qualquer outra chave do que aquelas listadas aqui também exibirá ``h`` a ajuda, mas é fácil de lembrar:-)
|``i``  |    Envie um SIGKILL para todos os processos, exceto para init.
|``j``  |    Forçosamente "apenas descongelar" – sistemas de filecongelado pelo FIFREEZE IOCTL.
|``k``  |    A SAK (chave de acesso seguro) elimina todos os programas no console virtual atual. NOTA: Consulte os comentários importantes abaixo na seção SAK.
|``l``  |    Mostra um rastreamento inativo de pilha para todas as CPUs ativas.
|``m``  |    O irá despejar informações de memória atuais no seu console.
|``n``  |    Usado para tornar as tarefas RT mais interessantes
|``o``  |    O desligará o sistema (se configurado e tiver suporte).
|``p``  |    O despejará os registros e os sinalizadores atuais em seu console.
|``q``  |    Despejará por listas de CPU de todos os hrtimers armado (mas não de temporizadores de timer_list regulares) e informações detalhadas sobre todos os dispositivos clockevent.
|``r``  |    Desativa o modo RAW do teclado e o define como XLATE.
|``s``  |    Tentará sincronizar todos os sistemas de sistema montados.
|``t``  |    Despejará uma lista de tarefas atuais e suas informações em seu console.
|``u``  |    O tentará remontar todos os sistemas de filesistemas montados como somente leitura.
|``v``  |    Restaura de modo forçado o console do framebuffer
|``v``  |    Causa despejo de buffer ETM [específico do ARM]
|``w``  |    Despeja tarefas que estão no estado ininterrupto (bloqueado).
|``x``  |    Usado pela interface xmon em plataformas PPC/PowerPC. Mostrar registros de PMU globais em sparc64. Despeja todas as entradas de TLB em MIPS.
|``y``  |    Mostrar registros de CPU globais [SPARC-64 específico]
|``z``  |    Despejar o buffer ftrace
|``0``-``9`` | Define o nível de log do console, controlando quais mensagens de kernel serão impressas no console. (``0``por exemplo, o faria para que apenas mensagens de emergência, como panes ou OPAS, o tornem para o console.)

### <a name="distribution-specific-documentation"></a>Documentação específica da distribuição ###
Para obter a documentação específica de distribuição em SysRq e as etapas para configurar o Linux para criar um despejo de memória ao receber um comando de "falha" de SysRq, consulte os links abaixo:

#### <a name="ubuntu"></a>Ubuntu ####
 - [Despejo de memória do kernel](https://help.ubuntu.com/lts/serverguide/kernel-crash-dump.html)

#### <a name="red-hat"></a>Red Hat ####
- [O que é o recurso SysRq e como usá-lo?](https://access.redhat.com/articles/231663)
- [Como usar o recurso SysRq para coletar informações de um servidor RHEL](https://access.redhat.com/solutions/2023)

#### <a name="suse"></a>SUSE ####
- [Configurar a captura de despejo núcleo do kernel](https://www.suse.com/support/kb/doc/?id=3374462)

#### <a name="coreos"></a>CoreOS ####
- [Coletando logs de falhas](https://coreos.com/os/docs/latest/collecting-crash-logs.html)

## <a name="non-maskable-interrupt-nmi"></a>Interrupção não mascarável (NMI) 
Uma NMI (interrupção não mascarável) foi projetada para criar um sinal de que o software em uma máquina virtual não será ignorado. Historicamente, NMIs foram utilizadas para monitorizar a existência de problemas de hardware em sistemas que exigem tempos de resposta específica.  Atualmente, os programadores e administradores de sistema geralmente usam NMI como um mecanismo para depurar ou solucionar problemas de sistemas que não estão respondendo.

O console serial pode ser usado para enviar uma NMI para uma máquina virtual do Azure usando o ícone de teclado na barra de comandos mostrada abaixo. Depois que a NMI for entregue, a configuração da máquina virtual controlará como o sistema responde.  Os sistemas operacionais Linux podem ser configurados para falhar e criar um despejo de memória. o sistema operacional recebe um NMI.

![](../media/virtual-machines-serial-console/virtual-machine-serial-console-command-menu.jpg) <br>

Para sistemas Linux que dão suporte a sysctl para configurar parâmetros de kernel, você pode habilitar uma pane ao receber esse NMI usando o seguinte:
1. Adicionando esta linha a */etc/sysctl.conf* <br>
    `kernel.panic_on_unrecovered_nmi=1`
1. Reinicializando ou atualizando sysctl executando <br>
    `sysctl -p`

Para obter mais informações sobre as configurações de kernel `unknown_nmi_panic`do `panic_on_io_nmi`Linux, `panic_on_unrecovered_nmi`incluindo, e, consulte: [Documentação para/proc/sys/kernel/*](https://www.kernel.org/doc/Documentation/sysctl/kernel.txt). Para obter a documentação específica de distribuição sobre NMI e as etapas para configurar o Linux para criar um despejo de memória ao receber um NMI, consulte os links abaixo:
 
### <a name="ubuntu"></a>Ubuntu 
 - [Despejo de memória do kernel](https://help.ubuntu.com/lts/serverguide/kernel-crash-dump.html)

### <a name="red-hat"></a>Red Hat 
 - [O que é um NMI e para que eu posso usá-lo?](https://access.redhat.com/solutions/4127)
 - [Como posso configurar meu sistema para falhar quando a opção NMI for enviada por push?](https://access.redhat.com/solutions/125103)
 - [Guia de administração de despejo de memória](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/pdf/kernel_crash_dump_guide/kernel-crash-dump-guide.pdf)

### <a name="suse"></a>SUSE 
- [Configurar a captura de despejo núcleo do kernel](https://www.suse.com/support/kb/doc/?id=3374462)

### <a name="coreos"></a>CoreOS 
- [Coletando logs de falhas](https://coreos.com/os/docs/latest/collecting-crash-logs.html)

## <a name="next-steps"></a>Passos Seguintes
* A página principal de documentação do Linux do console serial está localizada [aqui](serial-console.md).
* Usar o console serial para inicializar no [grub e entrar no modo de usuário único](serial-console-grub-single-user-mode.md)
* O console serial também está disponível para VMs do [Windows](../windows/serial-console.md)
* Saiba mais sobre o [diagnóstico de inicialização](boot-diagnostics.md)