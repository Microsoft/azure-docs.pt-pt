---
title: Consola em série Azure para chamadas SysRq e NMI Microsoft Docs
description: Utilização de consola sysRq e nMI chamadas em máquinas virtuais Azure.
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
ms.openlocfilehash: 3ad68438f5fc015b6a9150d67485b90a095f1a4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250091"
---
# <a name="use-serial-console-for-sysrq-and-nmi-calls"></a>Utilize consola em série para chamadas SysRq e NMI

## <a name="system-request-sysrq"></a>Pedido de Sistema (SysRq)
Um SysRq é uma sequência de teclas compreendidapelo núcleo do sistema de operação Linux, que pode desencadear um conjunto de ações pré-definidas. Estes comandos são frequentemente utilizados quando a resolução ou recuperação de problemas de máquinas virtuais não podem ser realizadas através da administração tradicional (por exemplo, se o VM não estiver a responder). A utilização da função SysRq da Consola Em Série Azure imitará a prensagem da tecla SysRq e os caracteres introduzidos num teclado físico.

Uma vez entregue a sequência SysRq, a configuração do núcleo controlará a forma como o sistema reage. Para obter informações sobre a ativação e desativação do SysRq, consulte a[marcação](https://aka.ms/linuxsysrq)de [texto](https://aka.ms/kernelorgsysreqdoc) | do Guia de *Administração SysRq* .

A Consola Em Série Azure pode ser usada para enviar um SysRq para uma máquina virtual Azure utilizando o ícone do teclado na barra de comando mostrada abaixo.

![](../media/virtual-machines-serial-console/virtual-machine-serial-console-command-menu.jpg)

A escolha do "Comando Enviar SysRq" abrirá um diálogo, que fornecerá opções comuns de SysRq ou aceitará uma sequência de comandos SysRq introduzidos no diálogo.  Isto permite que séries de SysRq realizem uma operação de `REISUB`alto nível, como um reboot seguro usando: .

![](../media/virtual-machines-serial-console/virtual-machine-serial-console-sysreq_UI.png)

O comando SysRq não pode ser usado em máquinas virtuais que são paradas ou cujo núcleo está em estado não responsivo. (por exemplo, um pânico de kernel).

### <a name="enable-sysrq"></a>Ativar o SysRq
Tal como descrito no *Guia de Administração SysRq* acima, o SysRq pode ser configurado de tal forma que todos, nenhum, ou apenas certos comandos estão disponíveis. Pode ativar todos os comandos SysRq utilizando o passo abaixo, mas não sobreviverá a um reboot:
```
echo "1" >/proc/sys/kernel/sysrq
```
Para tornar a configuração SysReq persistente, pode fazer o seguinte para ativar todos os comandos SysRq
1. Adicionar esta linha a */etc/sysctl.conf* <br>
    `kernel.sysrq = 1`
1. Reiniciar ou atualizar o sisctl correndo <br>
    `sysctl -p`

### <a name="command-keys"></a>Chaves de comando
Do Guia de Administração SysRq acima:

|Comando| Função
| ------| ----------- |
|``b``  |   Reiniciará imediatamente o sistema sem sincronizar ou desmontar os discos.
|``c``  |   Irá executar uma falha no sistema por uma referência de ponteiro NULO. Será tomada uma lixeira se estiver configurada.
|``d``  |   Mostra todas as fechaduras que estão presas.
|``e``  |   Envie um SIGTERM para todos os processos, exceto para o inite.
|``f``  |   Vai chamar o assassino de oom para matar um processo de porco de memória, mas não entre em pânico se nada puder ser morto.
|``g``  |   Usado por kgdb (debugger kernel)
|``h``  |   Mostrará ajuda (qualquer outra chave que não a ``h`` listada aqui também mostrará ajuda, mas é fácil de lembrar :-)
|``i``  |    Envie um SIGKILL para todos os processos, exceto para o inite.
|``j``  |    Forciblly "Just thaw it" - sistemas de ficheiros congelados pelo ioctl FIFREEZE.
|``k``  |    Chave de Acesso Segura (SAK) Mata todos os programas da consola virtual atual. NOTA: Consulte comentários importantes abaixo na secção SAK.
|``l``  |    Mostra um backtrace de pilha para todos os CPUs ativos.
|``m``  |    Despejará informações de memória atuais na sua consola.
|``n``  |    Usado para tornar as tarefas RT agradáveis
|``o``  |    Desligará o seu sistema (se configurado e suportado).
|``p``  |    Despejará os registos e bandeiras atuais na sua consola.
|``q``  |    Despejará por listas de CPU de todos os hrtimers armados (mas não regulares timer_list temporizadores) e informações detalhadas sobre todos os dispositivos de eventos de relógio.
|``r``  |    Desliga o modo bruto do teclado e define-o para XLATE.
|``s``  |    Tentará sincronizar todos os sistemas de ficheiros montados.
|``t``  |    Despejará uma lista das tarefas atuais e as suas informações para a sua consola.
|``u``  |    Tentará remontar todos os sistemas de ficheiros montados apenas.
|``v``  |    Restaura com força a consola do tampão de quadro
|``v``  |    Causa a lixeira tampão ETM [específica da ARM]
|``w``  |    Despejos tarefas que estão em estado ininterrupto (bloqueado).
|``x``  |    Usado pela interface xmon nas plataformas ppc/powerpc. Mostre os registos globais de PMU no sparc64. Despeje todas as entradas de TLB no MIPS.
|``y``  |    Mostrar registos globais de CPU [SPARC-64 específicos]
|``z``  |    Despeje o tampão de ftrace
|``0``-``9`` | Define o nível de registo da consola, controlando quais as mensagens de kernel que serão impressas na sua consola. (``0``( ( por exemplo, o faria para que apenas mensagens de emergência como PANICs ou OOPSes pudessem chegar à sua consola.)

### <a name="distribution-specific-documentation"></a>Documentação específica da distribuição ###
Para documentação específica de distribuição no SysRq e passos para configurar o Linux para criar um depósito de colisão quando receber um comando SysRq "Crash", consulte os links abaixo:

#### <a name="ubuntu"></a>Ubuntu ####
 - [Despejo de colisão de Kernel](https://help.ubuntu.com/lts/serverguide/kernel-crash-dump.html)

#### <a name="red-hat"></a>Red Hat ####
- [O que é a Instalação SysRq e como a uso?](https://access.redhat.com/articles/231663)
- [Como utilizar a instalação SysRq para recolher informações de um servidor RHEL](https://access.redhat.com/solutions/2023)

#### <a name="suse"></a>SUSE ####
- [Configure captura de despejo de núcleo de núcleo de kernel](https://www.suse.com/support/kb/doc/?id=3374462)

#### <a name="coreos"></a>CoreOS ####
- [Recolha de registos de colisão](https://coreos.com/os/docs/latest/collecting-crash-logs.html)

## <a name="non-maskable-interrupt-nmi"></a>Interrupção não-máscara (NMI)
Um interrupção não-máscara (NMI) foi concebido para criar um sinal de que o software numa máquina virtual não vai ignorar. Historicamente, as NMIs têm sido usadas para monitorizar problemas de hardware em sistemas que exigiam tempos de resposta específicos.  Hoje em dia, os programadores e os administradores de sistemas usam frequentemente o NMI como um mecanismo para depurar ou resolver sistemas que não estão a responder.

A Consola em Série pode ser usada para enviar um NMI para uma máquina virtual Azure utilizando o ícone do teclado na barra de comando sabotada abaixo. Uma vez entregue o NMI, a configuração da máquina virtual controlará a forma como o sistema reage.  Os sistemas operativos Linux podem ser configurados para colidir e criar um despejo de memória que o sistema operativo recebe um NMI.

![](../media/virtual-machines-serial-console/virtual-machine-serial-console-command-menu.jpg) <br>

### <a name="enable-nmi"></a>Ativar o NMI
Para sistemas Linux que suportam sisctl para configurar parâmetros de kernel, pode permitir o pânico ao receber este NMI utilizando o seguinte:
1. Adicionar esta linha a */etc/sysctl.conf* <br>
    `kernel.panic_on_unrecovered_nmi=1`
1. Reiniciar ou atualizar o sisctl correndo <br>
    `sysctl -p`

Para obter mais informações sobre as `unknown_nmi_panic` `panic_on_io_nmi`configurações `panic_on_unrecovered_nmi`do kernel linux, incluindo, e, ver: [Documentação para /proc/sys/kernel/*](https://www.kernel.org/doc/Documentation/sysctl/kernel.txt). Para documentação específica de distribuição sobre nMI e passos para configurar o Linux para criar um dump de colisão quando receber um NMI, consulte os links abaixo:

### <a name="ubuntu"></a>Ubuntu
 - [Despejo de colisão de Kernel](https://help.ubuntu.com/lts/serverguide/kernel-crash-dump.html)

### <a name="red-hat"></a>Red Hat
 - [Para que é um NMI e para que posso usá-lo?](https://access.redhat.com/solutions/4127)
 - [Como posso configurar o meu sistema para falhar quando o interruptor NMI é pressionado?](https://access.redhat.com/solutions/125103)
 - [Guia de administrador de despejo de acidente](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/pdf/kernel_crash_dump_guide/kernel-crash-dump-guide.pdf)

### <a name="suse"></a>SUSE
- [Configure captura de despejo de núcleo de núcleo de kernel](https://www.suse.com/support/kb/doc/?id=3374462)

### <a name="coreos"></a>CoreOS
- [Recolha de registos de colisão](https://coreos.com/os/docs/latest/collecting-crash-logs.html)

## <a name="next-steps"></a>Passos seguintes
* A página principal de documentação do Linux da Consola Em Série está localizada [aqui.](serial-console-linux.md)
* Utilize a Consola de Série para iniciar o [GRUB e introduzir o modo de utilizador único](serial-console-grub-single-user-mode.md)
* A Consola em Série também está disponível para [VMs do Windows](serial-console-windows.md)
* Saiba mais sobre [diagnósticos de botas](boot-diagnostics.md)