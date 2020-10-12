---
title: Consola em série Azure para chamadas SysRq e NMI Microsoft Docs
description: A utilização da Consola em Série para chamadas SysRq e NMI em máquinas virtuais Azure.
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
ms.openlocfilehash: 545399e1d7941351ce861ac98d995d5e57006ea1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87074373"
---
# <a name="use-the-azure-serial-console-for-sysrq-and-nmi-calls"></a>Utilize a consola em série Azure para chamadas SysRq e NMI

## <a name="system-request-sysrq"></a>Pedido do Sistema (SysRq)
Um SysRq é uma sequência de teclas entendida pelo núcleo do sistema de operação Linux, que pode desencadear um conjunto de ações pré-definidas. Estes comandos são frequentemente utilizados quando a resolução ou recuperação de problemas da máquina virtual não podem ser realizadas através da administração tradicional (por exemplo, se o VM não estiver a responder). A utilização da funcionalidade SysRq da Consola em Série Azure irá imitar a pressão da chave SysRq e os caracteres introduzidos num teclado físico.

Uma vez que a sequência SysRq é entregue, a configuração do núcleo controlará a forma como o sistema responde. Para obter informações sobre a ativação e desativação do SysRq, consulte o texto *do Guia de Administração SysRq* [text](https://aka.ms/kernelorgsysreqdoc)  |  [markdown](https://aka.ms/linuxsysrq).

A Consola em Série Azure pode ser utilizada para enviar um SysRq para uma máquina virtual Azure utilizando o ícone do teclado na barra de comando mostrada abaixo.

![Screenshot da Consola Em Série Azure. O ícone do teclado é realçado e o seu menu é visível. Este menu contém um item de Comando Enviar SysRq.](../media/virtual-machines-serial-console/virtual-machine-serial-console-command-menu.jpg)

A escolha de "Enviar o Comando SysRq" abrirá um diálogo, que fornecerá opções sysRq comuns ou aceitará uma sequência de comandos SysRq introduzidos no diálogo.  Isto permite que uma série de SysRq's execute uma operação de alto nível, como um reboot seguro utilizando: `REISUB` .

![Screenshot do Comando Enviar SysRq para a caixa de diálogo do convidado. A opção para introduzir comandos é selecionada e a caixa de comandos contém REISUB.](../media/virtual-machines-serial-console/virtual-machine-serial-console-sysreq_UI.png)

O comando SysRq não pode ser usado em máquinas virtuais que são paradas ou cujo núcleo está em estado de não resposta. (por exemplo, um pânico de núcleo).

### <a name="enable-sysrq"></a>Ativar o SysRq
Como descrito no *Guia de Administração SysRq* acima, o SysRq pode ser configurado de modo a que todos, nenhum ou apenas certos comandos estejam disponíveis. Pode ativar todos os comandos SysRq utilizando o passo abaixo, mas não sobreviverá a um reboot:
```
echo "1" >/proc/sys/kernel/sysrq
```
Para tornar a configuração SysReq persistente, pode fazer o seguinte para ativar todos os comandos SysRq
1. Adicionando esta linha a */etc/sysctl.conf* <br>
    `kernel.sysrq = 1`
1. Reiniciar ou atualizar o sysctl correndo <br>
    `sysctl -p`

### <a name="command-keys"></a>Chaves de comando
Do Guia de Administração SysRq acima:

|Comando| Função
| ------| ----------- |
|``b``  |   Reiniciará imediatamente o sistema sem sincronizar ou desmontar os discos.
|``c``  |   Irá executar uma falha no sistema por uma dereferência do ponteiro NULL. Uma falha será tomada se configurada.
|``d``  |   Mostra todas as fechaduras que estão presas.
|``e``  |   Envie um SIGTERM para todos os processos, exceto para o init.
|``f``  |   Vai chamar o assassino de oom para matar um processo de porco de memória, mas não entreem em pânico se nada puder ser morto.
|``g``  |   Utilizado por kgdb (depurado de núcleo)
|``h``  |   Irá mostrar ajuda (qualquer outra chave que não as listadas aqui também mostrará ajuda, mas ``h`` é fácil de lembrar :-)
|``i``  |    Envie um SIGKILL para todos os processos, exceto para o init.
|``j``  |    À força "Basta descongelá-lo" - sistemas de ficheiros congelados pelo ioctl FIFREEZE.
|``k``  |    A Chave de Acesso Segura (SAK) Mata todos os programas na consola virtual atual. NOTA: Consulte comentários importantes abaixo na secção SAK.
|``l``  |    Mostra uma contratracção de pilha para todos os CPUs ativos.
|``m``  |    Irá despejar informações de memória atuais na sua consola.
|``n``  |    Usado para tornar as tarefas RT agradáveis
|``o``  |    Desligará o seu sistema (se configurado e suportado).
|``p``  |    Irá despejar os registos e bandeiras atuais na sua consola.
|``q``  |    Irá despejar de acordo com as listas de TODOS os hrtimers armados (mas não regulares timer_list temporizadores) e informações detalhadas sobre todos os dispositivos do relógio.
|``r``  |    Desliga o modo cru do teclado e define-o para XLATE.
|``s``  |    Tentará sincronizar todos os sistemas de ficheiros montados.
|``t``  |    Irá despejar uma lista de tarefas atuais e as suas informações para a sua consola.
|``u``  |    Tentará remontar todos os sistemas de ficheiros montados apenas para leitura.
|``v``  |    Restaura vigorosamente a consola framebuffer
|``v``  |    Causas de despejo de tampão ETM [específico ARM]
|``w``  |    Despeja tarefas que estão em estado ininterrupto (bloqueado).
|``x``  |    Usado pela interface xmon nas plataformas ppc/powerpc. Mostre os registos globais da PMU em sparc64. Despeje todas as entradas de TLB em MIPS.
|``y``  |    Mostrar registos globais do CPU [ESPECIFICAÇÃO SPARC-64]
|``z``  |    Despeje o tampão ftrace
|``0``-``9`` | Define o nível de registo da consola, controlando quais as mensagens de kernel que serão impressas na sua consola. ( ``0`` , por exemplo, faria com que apenas mensagens de emergência como PANICs ou OOPSes pudessem chegar à sua consola.)

### <a name="distribution-specific-documentation"></a>Documentação específica da distribuição ###
Para documentação específica de distribuição no SysRq e passos para configurar o Linux para criar um depósito de colisão quando receber um comando SysRq "Crash", consulte os links abaixo:

#### <a name="ubuntu"></a>Ubuntu ####
 - [Despejo de acidente de Kernel](https://help.ubuntu.com/lts/serverguide/kernel-crash-dump.html)

#### <a name="red-hat"></a>Red Hat ####
- [O que é a Instalação SysRq e como a uso?](https://access.redhat.com/articles/231663)
- [Como usar a instalação SysRq para recolher informações de um servidor RHEL](https://access.redhat.com/solutions/2023)

#### <a name="suse"></a>SUSE ####
- [Captura de despejo do núcleo de núcleo de configure](https://www.suse.com/support/kb/doc/?id=3374462)

#### <a name="coreos"></a>CoreOS ####
- [Recolha de registos de acidentes](https://coreos.com/os/docs/latest/collecting-crash-logs.html)

## <a name="non-maskable-interrupt-nmi"></a>Interrupção não mascarada (NMI)
Uma interrupção não mascarada (NMI) foi concebida para criar um sinal de que o software numa máquina virtual não ignorará. Historicamente, as NMIs têm sido usadas para monitorizar problemas de hardware em sistemas que exigiam tempos de resposta específicos.  Hoje em dia, programadores e administradores de sistemas usam frequentemente o NMI como um mecanismo para depurar ou resolver sistemas que não respondem.

A Consola em Série pode ser utilizada para enviar um NMI para uma máquina virtual Azure utilizando o ícone do teclado na barra de comando mostrada abaixo. Uma vez que o NMI é entregue, a configuração da máquina virtual controlará a forma como o sistema responde.  Os sistemas operativos Linux podem ser configurados para colidir e criar um depósito de memória que o sistema operativo recebe um NMI.

![Screenshot da Consola Em Série. O ícone do teclado é realçado e o seu menu é visível. Este menu contém um item de interrupção não mascarado.](../media/virtual-machines-serial-console/virtual-machine-serial-console-command-menu.jpg) <br>

### <a name="enable-nmi"></a>Ativar O NMI
Para os sistemas Linux que suportam sisctl para configurar parâmetros de kernel, pode ativar o pânico ao receber este NMI utilizando o seguinte:
1. Adicionando esta linha a */etc/sysctl.conf* <br>
    `kernel.panic_on_unrecovered_nmi=1`
1. Reiniciar ou atualizar o sysctl correndo <br>
    `sysctl -p`

Para obter mais informações sobre as configurações do kernel do Linux, `unknown_nmi_panic` `panic_on_io_nmi` incluindo, `panic_on_unrecovered_nmi` e, ver: [Documentação para /proc/sys/kernel/*](https://www.kernel.org/doc/Documentation/sysctl/kernel.txt). Para documentação específica de distribuição no NMI e passos para configurar o Linux para criar um depósito de colisão quando receber um NMI, consulte os links abaixo:

### <a name="ubuntu"></a>Ubuntu
 - [Despejo de acidente de Kernel](https://help.ubuntu.com/lts/serverguide/kernel-crash-dump.html)

### <a name="red-hat"></a>Red Hat
 - [O que é um NMI e para que posso usá-lo?](https://access.redhat.com/solutions/4127)
 - [Como posso configurar o meu sistema para falhar quando o interruptor NMI é pressionado?](https://access.redhat.com/solutions/125103)
 - [Guia de administração de despejo de acidente](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/pdf/kernel_crash_dump_guide/kernel-crash-dump-guide.pdf)

### <a name="suse"></a>SUSE
- [Captura de despejo do núcleo de núcleo de configure](https://www.suse.com/support/kb/doc/?id=3374462)

### <a name="coreos"></a>CoreOS
- [Recolha de registos de acidentes](https://coreos.com/os/docs/latest/collecting-crash-logs.html)

## <a name="next-steps"></a>Passos seguintes
* A principal página de documentação da Consola em Série Linux está localizada [aqui.](serial-console-linux.md)
* Utilize a Consola Serial para iniciar o [GRUB e insira o modo de utilizador único](serial-console-grub-single-user-mode.md)
* A Consola em Série também está disponível para [VMs windows](serial-console-windows.md)
* Saiba mais sobre [diagnósticos de arranque](boot-diagnostics.md)