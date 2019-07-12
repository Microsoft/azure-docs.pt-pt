---
title: Consola de série do Azure para chamadas SysRq e NMI | Documentos da Microsoft
description: Utilizar a consola de série para SysRq e NMI chama em máquinas virtuais do Azure.
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/14/2018
ms.author: alsin
ms.openlocfilehash: 79729cf222c208a78a2eac430e51b996cddb4e78
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67710545"
---
# <a name="use-serial-console-for-sysrq-and-nmi-calls"></a>Utilizar a consola de série para chamadas SysRq e NMI

## <a name="system-request-sysrq"></a>Pedido de sistema (SysRq)
Um SysRq é uma sequência de chaves abrangidos pelo kernel de sistema de operação do Linux, que pode acionar um conjunto de ações predefinidas. Estes comandos, muitas vezes, são utilizados quando a resolução de problemas de máquina virtual ou de recuperação não pode ser efetuada através de administração tradicional (por exemplo, se a VM não está a responder). Utilizar a funcionalidade de SysRq da consola de série do Azure irão imitar prima da chave de SysRq e os carateres introduzidos num teclado físico.

Assim que a sequência de SysRq é enviada, a configuração de kernel irá controlar como o sistema responde. Para informações sobre como ativar e desativar SysRq, consulte a *Guia do administrador do SysRq* [texto](https://aka.ms/kernelorgsysreqdoc) | [markdown](https://aka.ms/linuxsysrq).  

Consola de série do Azure pode ser utilizada para enviar um SysRq para uma máquina virtual do Azure com o ícone de teclado na barra de comando mostrada abaixo.

![](../media/virtual-machines-serial-console/virtual-machine-serial-console-command-menu.jpg)

Escolher "Enviar o comando SysRq" abrirá uma caixa de diálogo, que irá fornecer opções de SysRq comuns ou aceitar uma seqüência de comandos de SysRq introduzido na caixa de diálogo.  Desta forma, para efetuar uma operação de alto nível, como um reinício seguro usando série de SysRq: `REISUB`.

![](../media/virtual-machines-serial-console/virtual-machine-serial-console-sysreq_UI.png)

Não é possível utilizar o comando SysRq em máquinas virtuais que estão parados ou cujo kernel está num Estado não responsivo. (por exemplo um entre em pânico do kernel).

### <a name="enable-sysrq"></a>Enable SysRq 
Conforme descrito no *Guia do administrador do SysRq* acima, pode ser configurado SysRq, de modo que todos os, none ou apenas determinados comandos estão disponíveis. Pode ativar todos os comandos de SysRq utilizando o passo abaixo, mas ele será sobrevivem a uma inicialização:
```
echo "1" >/proc/sys/kernel/sysrq
```
Para tornar a configuração de SysReq persistente, pode fazer o seguinte procedimento para ativar todos os comandos de SysRq
1. Adicionar esta linha para */etc/sysctl.conf* <br>
    `kernel.sysrq = 1`
1. Reiniciar ou atualizar sysctl através da execução <br>
    `sysctl -p`

### <a name="command-keys"></a>Chaves de comando 
No Guia do administrador SysRq acima:

|Comando| Função
| ------| ----------- |
|``b``  |   Imediatamente irá reiniciar o sistema sem sincronização ou desmontar os discos.
|``c``  |   Irá efetuar uma falha de sistema por um ponteiro NULL tirarmos a referência. Um crashdump será executada se configurado.
|``d``  |   Mostra todos os bloqueios que são mantidos.
|``e``  |   Envie um SIGTERM para todos os processos, exceto para init.
|``f``  |   Chamará o inibidor de memória insuficiente para finalizar um processo de exagero de memória, mas não entre em pânico se é possível eliminar nada.
|``g``  |   Utilizado por kgdb (depurador de kernel)
|``h``  |   Irá apresentar a ajuda (qualquer outra chave que os listados aqui também irá apresentar a ajuda, mas ``h`` é fácil de memorizar :-)
|``i``  |    Envie um SIGKILL para todos os processos, exceto para init.
|``j``  |    A forçar "Apenas descongelá-lo" - congelados por FIFREEZE ioctl de sistemas de ficheiros.
|``k``  |    Chave de acesso seguro (SAK) interrompe todos os programas na consola do virtual atual. NOTA: Veja importantes comentários abaixo na secção SAK.
|``l``  |    Mostra um stack backtrace para todas as CPUs de Active Directory.
|``m``  |    Despejará informações de memória atual para a consola.
|``n``  |    Utilizada para efetuar tarefas de RT bom ajustável
|``o``  |    Irá desativar seu sistema (se configurada e suportado).
|``p``  |    Despejará o registradores atuais e os sinalizadores para a consola.
|``q``  |    Despejará por listas de CPU de todos os hrtimers armed (mas não os temporizadores de regular timer_list) e obter informações detalhadas sobre todos os dispositivos de clockevent.
|``r``  |    Desativa o modo de não processados do teclado e a define como XLATE.
|``s``  |    Irá tentar sincronizar todos os sistemas de ficheiros montados.
|``t``  |    Será despeja uma lista de tarefas atuais e suas informações para a consola.
|``u``  |    Irá tentar voltar a montar montados todos os sistemas de ficheiros só de leitura.
|``v``  |    Restaura forçadamente framebuffer consola
|``v``  |    Faz com que o despejo de memória intermédia ETM [ARM específicos]
|``w``  |    Informações do Estado tarefas que estão num Estado (bloqueado) ininterrupta.
|``x``  |    Utilizado pela interface xmon em plataformas de ppc/powerpc. Mostre a opção global registra de PMU no sparc64. Despeja todas as entradas TLB em MIPS.
|``y``  |    Mostrar global da CPU registra [SPARC-64 específicas]
|``z``  |    Despejar o buffer de ftrace
|``0``-``9`` | Define o nível de registo da consola, controlar quais mensagens de kernel serão impressos para a consola. (``0``, para o exemplo seria fazer com que mensagens apenas de emergência, como Pânicos ou OOPSes tornaria ao console.)

### <a name="distribution-specific-documentation"></a>Documentação de distribuição específico ###
Para obter documentação de distribuição específicos sobre SysRq e os passos para configurar o Linux para criar um despejo de falha quando recebe um comando de "Crash" SysRq, veja as ligações abaixo:

#### <a name="ubuntu"></a>Ubuntu ####
 - [Informação de falha de kernel](https://help.ubuntu.com/lts/serverguide/kernel-crash-dump.html)

#### <a name="red-hat"></a>Red Hat ####
- [O que é o recurso de SysRq e como usá-lo?](https://access.redhat.com/articles/231663)
- [Como utilizar o recurso de SysRq para recolher informações de um servidor do RHEL](https://access.redhat.com/solutions/2023)

#### <a name="suse"></a>SUSE ####
- [Configurar a captura de despejo de núcleo de kernel](https://www.suse.com/support/kb/doc/?id=3374462)

#### <a name="coreos"></a>CoreOS ####
- [Recolher registos de falha](https://coreos.com/os/docs/latest/collecting-crash-logs.html)

## <a name="non-maskable-interrupt-nmi"></a>Interrupção não Maskable (NMI) 
Uma interrupção não maskable (NMI) foi concebida para criar um sinal de que o software numa máquina virtual não será ignorada. Historicamente, NMIs foram utilizadas para monitorizar a existência de problemas de hardware em sistemas que exigem tempos de resposta específica.  Hoje, os programadores e administradores de sistema utilizam frequentemente NMI como um mecanismo para depurar ou resolver problemas de sistemas que não estão a responder.

A consola de série pode ser utilizada para enviar um NMI para uma máquina virtual do Azure com o ícone de teclado na barra de comando mostrada abaixo. Depois do NMI é enviado, a configuração de máquina virtual irá controlar como o sistema responde.  Linux podem ser configurados para falhas de sistemas operacionais e criar um despejo de memória do sistema operativo recebe um NMI.

![](../media/virtual-machines-serial-console/virtual-machine-serial-console-command-menu.jpg) <br>

### <a name="enable-nmi"></a>Ativar NMI
Para sistemas Linux que suportam sysctl para configurar parâmetros de kernel, pode ativar um entre em pânico quando receber este NMI usando o seguinte:
1. Adicionar esta linha para */etc/sysctl.conf* <br>
    `kernel.panic_on_unrecovered_nmi=1`
1. Reiniciar ou atualizar sysctl através da execução <br>
    `sysctl -p`

Para obter mais informações sobre configurações de kernel do Linux, incluindo `unknown_nmi_panic`, `panic_on_io_nmi`, e `panic_on_unrecovered_nmi`, consulte: [Documentação para/proc/sys/kernel / *](https://www.kernel.org/doc/Documentation/sysctl/kernel.txt). Para obter documentação de distribuição específicos sobre NMI e os passos para configurar o Linux para criar um despejo de falha quando recebe um NMI, veja as ligações abaixo:
 
### <a name="ubuntu"></a>Ubuntu 
 - [Informação de falha de kernel](https://help.ubuntu.com/lts/serverguide/kernel-crash-dump.html)

### <a name="red-hat"></a>Red Hat 
 - [O que é um NMI e o que posso utilizá-lo?](https://access.redhat.com/solutions/4127)
 - [Como posso configurar meu sistema falhe ao comutador NMI é emitidos via Push?](https://access.redhat.com/solutions/125103)
 - [Falha despejar o Guia do administrador](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/pdf/kernel_crash_dump_guide/kernel-crash-dump-guide.pdf)

### <a name="suse"></a>SUSE 
- [Configurar a captura de despejo de núcleo de kernel](https://www.suse.com/support/kb/doc/?id=3374462)

### <a name="coreos"></a>CoreOS 
- [Recolher registos de falha](https://coreos.com/os/docs/latest/collecting-crash-logs.html)

## <a name="next-steps"></a>Passos Seguintes
* A principal página de documentação do Linux de consola Serial está localizada [aqui](serial-console-linux.md).
* Utilizar a consola de série para inicializar em [de GRUB e introduza o modo de utilizador único](serial-console-grub-single-user-mode.md)
* Também está disponível para a consola de série [Windows](serial-console-windows.md) VMs
* Saiba mais sobre [diagnósticos de arranque](boot-diagnostics.md)