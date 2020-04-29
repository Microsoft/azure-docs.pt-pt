---
title: Consola em série Azure para Windows / Microsoft Docs
description: Consola em série bidirecional para máquinas virtuais azure e conjuntos de escala de máquina virtual.
services: virtual-machines-windows
documentationcenter: ''
author: asinn826
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 5/1/2019
ms.author: alsin
ms.openlocfilehash: 68089a86b8b832638abd30aa7c36aa1c5bd84225
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80410124"
---
# <a name="azure-serial-console-for-windows"></a>Consola em série Azure para Windows

A Consola Em Série no portal Azure fornece acesso a uma consola baseada em texto para máquinas virtuais Windows (VMs) e instâncias de conjunto de máquinas virtuais. Esta ligação em série liga-se à porta de série COM1 da instância de conjunto de banda vm ou virtual, proporcionando-lhe acesso independentemente da rede ou do estado do sistema operativo. A consola em série só pode ser acedida através do portal Azure e só é permitida para os utilizadores que tenham uma função de acesso de Colaborador ou superior ao conjunto de escala de máquinas VM ou virtual.

A Consola Série funciona da mesma forma para VMs e instâncias de conjunto de métricas de máquinas virtuais. Neste doc, todas as menções aos VMs incluirão implicitamente instâncias de conjunto de máquinas virtuais, salvo indicação em contrário.

Para obter documentação em série para consolas para Linux, consulte [A Consola Em Série Azure para Linux](serial-console-linux.md).

> [!NOTE]
> A Consola Em Série está geralmente disponível nas regiões globais do Azure e em antevisão pública no Governo azure. Ainda não está disponível na nuvem azure china.


## <a name="prerequisites"></a>Pré-requisitos

* A sua configuração de conjunto de métricas vm ou virtual deve utilizar o modelo de implementação da gestão de recursos. As implantações clássicas não são apoiadas.

- A sua conta que utiliza a consola em série deve ter a [função de Colaborador de Máquina Virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) para o VM e a conta de armazenamento de diagnóstico de [arranque](boot-diagnostics.md)

- A sua configuração de conjunto de métricas vM ou virtual deve ter um utilizador baseado em palavra-passe. Pode criar um com a função de [palavra-passe redefinida](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) da extensão de acesso VM. Selecione **Redefinir a palavra-passe** da secção Suporte + resolução de **problemas.**

* O VM para a configuração de conjunto de máquinas virtuais deve ter [diagnósticos](boot-diagnostics.md) de arranque ativados.

    ![Definições de diagnóstico de arranque](../media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

## <a name="enable-serial-console-functionality-for-windows-server"></a>Ativar a funcionalidade da consola em série para o Windows Server

> [!NOTE]
> Se não estiver a ver nada na consola em série, certifique-se de que o diagnóstico de arranque está ativado no seu conjunto de escala de máquinas VM ou virtual.

### <a name="enable-the-serial-console-in-custom-or-older-images"></a>Ativar a consola em série em imagens personalizadas ou mais antigas
As imagens mais recentes do Windows Server no Azure têm consola de [administração especial](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) (SAC) ativada por padrão. O SAC é suportado nas versões do servidor do Windows, mas não está disponível nas versões dos clientes (por exemplo, Windows 10, Windows 8 ou Windows 7).

Para imagens mais antigas do Windows Server (criadas antes de fevereiro de 2018), pode ativar automaticamente a consola em série através da função de comando de execução do portal Azure. No portal Azure, selecione comando **executar**e, em seguida, selecione o comando denominado **EnableEMS** da lista.

![Executar lista de comando](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-runcommand.png)

Em alternativa, para ativar manualmente a consola em série para o conjunto de escala de máquinas VMs/máquinavirtual windows criado antes de fevereiro de 2018, siga estes passos:

1. Ligue-se à sua máquina virtual windows utilizando o Ambiente de Trabalho Remoto
1. A partir de um pedido de comando administrativo, executar os seguintes comandos:
    - `bcdedit /ems {current} on`, `bcdedit /ems '{current}' on` ou se estiver a usar powerShell
    - `bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200`
1. Reinicie o sistema para ativar a consola SAC.

    ![Consola SAC](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

Se necessário, o SAC também pode ser ativado offline:

1. Fixe o disco windows para o qual pretende configurar o SAC como um disco de dados ao VM existente.

1. A partir de um pedido de comando administrativo, executar os seguintes comandos:
   - `bcdedit /store <mountedvolume>\boot\bcd /ems {default} on`
   - `bcdedit /store <mountedvolume>\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

#### <a name="how-do-i-know-if-sac-is-enabled"></a>Como sei se o SAC está ativado?

Se o [SAC](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) não estiver ativado, a consola em série não apresentará o pedido SAC. Em alguns casos, a informação de saúde vm é mostrada, e em outros casos é em branco. Se estiver a utilizar uma imagem do Windows Server criada antes de fevereiro de 2018, o SAC provavelmente não estará ativado.

### <a name="enable-the-windows-boot-menu-in-the-serial-console"></a>Ativar o menu de boot do Windows na consola em série

Se necessitar de ativar as instruções do carregador de arranque do Windows para visualizar na consola em série, pode adicionar as seguintes opções adicionais aos dados de configuração do seu arranque. Para mais informações, consulte [bcdedit.](https://docs.microsoft.com/windows-hardware/drivers/devtest/bcdedit--set)

1. Ligue-se à sua configuração de placa de máquina selelérvia VM ou virtual utilizando o Ambiente de Trabalho Remoto.

1. A partir de um pedido de comando administrativo, executar os seguintes comandos:
   - `bcdedit /set {bootmgr} displaybootmenu yes`
   - `bcdedit /set {bootmgr} timeout 10`
   - `bcdedit /set {bootmgr} bootems yes`

1. Reiniciar o sistema para ativar o menu de arranque

> [!NOTE]
> O tempo de tempo definido para o menu do boot manager para exibir terá impacto no seu tempo de arranque do SO. Se acha que o valor do tempo de 10 segundos é demasiado curto ou demasiado longo, detetetete-o para um valor diferente.

## <a name="use-serial-console"></a>Utilizar consola em série

### <a name="use-cmd-or-powershell-in-serial-console"></a>Utilize CMD ou PowerShell na consola em série

1. Ligue-se à consola em série. Se conseguir ligar com sucesso, o aviso é **SAC>: **

    ![Ligar ao SAC](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect-sac.png)

1.    Introduza `cmd` para criar um canal que tenha uma instância CMD.

1.    Introduza `ch -si 1` `<esc>+<tab>` ou pressione teclas de atalho para mudar para o canal que está a executar a instância CMD.

1.    Pressione **Entrar**e, em seguida, insira credenciais de entrada com permissões administrativas.

1.    Depois de ter introduzido credenciais válidas, a instância CMD abre.

1.    Para iniciar uma instância `PowerShell` PowerShell, introduza na instância CMD e, em seguida, prima **Enter**.

    ![Caso Open PowerShell](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-powershell.png)

### <a name="use-the-serial-console-for-nmi-calls"></a>Utilize a consola em série para chamadas NMI
Uma interrupção não-máscara (NMI) foi concebida para criar um sinal de que o software numa máquina virtual não vai ignorar. Historicamente, as NMIs têm sido usadas para monitorizar problemas de hardware em sistemas que exigiam tempos de resposta específicos. Hoje em dia, os programadores e os administradores de sistemas usam frequentemente o NMI como um mecanismo para depurar ou resolver sistemas que não estão a responder.

A consola em série pode ser usada para enviar um NMI para uma máquina virtual Azure utilizando o ícone do teclado na barra de comando. Após a entrega do NMI, a configuração da máquina virtual controlará a forma como o sistema reage. O Windows pode ser configurado para colidir e criar um ficheiro de despejo de memória ao receber um NMI.

![Enviar NMI](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-nmi.png) <br>

Para obter informações sobre a configuração do Windows para criar um ficheiro de despejo de falha quando receber um NMI, consulte como gerar um ficheiro de despejo de [falha utilizando um NMI](https://support.microsoft.com/help/927069/how-to-generate-a-complete-crash-dump-file-or-a-kernel-crash-dump-file).

### <a name="use-function-keys-in-serial-console"></a>Utilize as teclas de função na consola em série
As teclas de função estão ativadas para utilização para consola seletiva em VMs do Windows. O F8 no dropdown da consola em série proporciona a conveniência de entrar facilmente no menu Dedefinições de Arranque Avançada, mas a consola em série é compatível com todas as outras teclas de função. Pode ser necessário premir **Fn** + **F1** (ou F2, F3, etc.) no teclado, dependendo do computador de onde está a utilizar a consola em série.

### <a name="use-wsl-in-serial-console"></a>Utilizar a WSL na consola em série
O Subsistema Windows para Linux (WSL) foi ativado para o Windows Server 2019 ou mais tarde, pelo que também é possível ativar a WSL para utilização dentro da consola em série se estiver a executar o Windows Server 2019 ou mais tarde. Isto pode ser benéfico para os utilizadores que também têm uma familiaridade com os comandos Linux. Para obter instruções para ativar o WSL para o Windows Server, consulte o guia de [instalação](https://docs.microsoft.com/windows/wsl/install-on-server).

### <a name="restart-your-windows-vmvirtual-machine-scale-set-instance-within-serial-console"></a>Reinicie a sua instância de conjunto de escala de máquina seleção VM/virtual no meio da consola em série
Pode iniciar um reinício dentro da consola em série navegando para o botão de alimentação e clicando em "Restart VM". Isto irá iniciar um reinício vm, e você verá uma notificação dentro do portal Azure sobre o reinício.

Isto é útil em situações em que poderá querer aceder ao menu de arranque sem deixar a experiência da consola em série.

![Reinício da consola de série do Windows](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-windows.gif)

## <a name="disable-the-serial-console"></a>Desativar a consola em série
Por padrão, todas as subscrições têm acesso à consola em série ativada. Pode desativar a consola em série ao nível de subscrição ou ao nível de conjunto de escala vm/máquina virtual. Para obter instruções detalhadas, visite [Enable e desative a consola em série Azure](./serial-console-enable-disable.md).

## <a name="serial-console-security"></a>Segurança da consola em série

### <a name="access-security"></a>Segurança de acesso
O acesso à consola em série está limitado aos utilizadores que tenham uma função de acesso ao [Virtual Machine Contributor](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) ou superior à máquina virtual. Se o seu inquilino do Diretório Ativo Azure necessitar de autenticação multi-factor (MFA), então o acesso à consola em série também necessitará de MFA porque o acesso da consola em série é através do [portal Azure.](https://portal.azure.com)

### <a name="channel-security"></a>Segurança do canal
Todos os dados enviados para trás e para a frente estão encriptados no fio.

### <a name="audit-logs"></a>Registos de auditoria
Todo o acesso à consola em série está atualmente registado nos [registos](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) de diagnóstico de arranque da máquina virtual. O acesso a estes registos é propriedade e controlado pelo administrador de máquinas virtuais Azure.

> [!CAUTION]
> Não são registadas senhas de acesso para a consola. No entanto, se os comandos forem executados dentro da consola, contenham ou produzam senhas, segredos, nomes de utilizadores ou qualquer outra forma de informação pessoalmente identificável (PII), esses serão escritos nos registos de diagnóstico de arranque da VM. Serão escritas juntamente com todos os outros textos visíveis, como parte da implementação da função de deslocamento da consola em série. Estes registos são circulares e apenas os indivíduos com permissões de leitura para a conta de armazenamento de diagnósticos têm acesso aos mesmos. No entanto, recomendamos seguir as melhores práticas de utilização do Ambiente de Trabalho Remoto para qualquer coisa que possa envolver segredos e/ou PII.

### <a name="concurrent-usage"></a>Utilização simultânea
Se um utilizador estiver ligado à consola em série e outro utilizador solicitar com sucesso o acesso à mesma máquina virtual, o primeiro utilizador será desligado e o segundo utilizador ligado à mesma sessão.

> [!CAUTION]
> Isto significa que um utilizador desligado não será desligado. A capacidade de impor um logout após a desconexão (utilizando sIGHUP ou mecanismo semelhante) ainda está no roteiro. Para windows, há um tempo de paragem automático ativado no SAC; para o Linux, pode configurar o intervalo de tempo terminal.

## <a name="accessibility"></a>Acessibilidade
A acessibilidade é um foco chave para a consola em série Azure. Para tal, garantimos que a consola em série é acessível para deficientes visuais e auditivos, bem como pessoas que podem não ser capazes de usar um rato.

### <a name="keyboard-navigation"></a>Navegação através do teclado
Utilize a tecla **Tab** no seu teclado para navegar na interface de consola em série a partir do portal Azure. A sua localização será destacada no ecrã. Para deixar o foco da janela da consola em série, prima **Ctrl**+**F6** no teclado.

### <a name="use-the-serial-console-with-a-screen-reader"></a>Utilize a consola em série com um leitor de ecrã
A consola em série tem suporte para leitor de ecrã incorporado. Navegar em torno de um leitor de ecrã ligado permitirá que o texto alt para o botão atualmente selecionado seja lido em voz alta pelo leitor de ecrã.

## <a name="common-scenarios-for-accessing-the-serial-console"></a>Cenários comuns para aceder à consola em série

Cenário          | Ações na consola em série
:------------------|:-----------------------------------------
Regras de firewall incorretas | Aceda à consola em série e fixe as regras de firewall do Windows.
Corrupção/verificação do sistema de ficheiros | Aceda à consola em série e recupere o sistema de ficheiros.
Problemas de configuração rdp | Aceda à consola em série e altere as definições. Para mais informações, consulte a documentação do [PDR.](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-allow-access)
Sistema de bloqueio de rede | Aceda à consola em série do portal Azure para gerir o sistema. Alguns comandos de rede estão listados em [comandos Windows: CMD e PowerShell](serial-console-cmd-ps-commands.md).
Interagindo com o bootloader | Aceda ao BCD através da consola em série. Para obter informações, consulte [Ativar o menu](#enable-the-windows-boot-menu-in-the-serial-console)de arranque do Windows na consola em série .

## <a name="known-issues"></a>Problemas conhecidos
Estamos cientes de alguns problemas com a consola em série e o sistema operativo da VM. Aqui está uma lista destes problemas e passos para mitigação para VMs windows. Estas questões e atenuações aplicam-se tanto para os VMs como para as instâncias de conjunto de máquinas virtuais. Se estes não corresponderem ao erro que está a ver, veja os erros comuns de serviço da consola em série nos [erros da Consola Série Comum](./serial-console-errors.md).

Problema                             |   Mitigação
:---------------------------------|:--------------------------------------------|
Pressionar **Introduza** depois de o banner de ligação não causar a visualização de uma solicitação de entrada. | Para mais informações, consulte [hit enter não faz nada](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). Este erro pode ocorrer se estiver a executar um VM personalizado, um aparelho endurecido ou um config de arranque que faz com que o Windows não se conectem corretamente à porta de série. Este erro também ocorrerá se estiver a executar um VM Windows 10, porque apenas os VMs do Windows Server estão configurados para ter EMS ativado.
Apenas são mostradas informações de saúde ao ligar-se a um VM do Windows| Este erro ocorre se a Consola de Administração Especial não tiver sido ativada para a sua imagem do Windows. Consulte [a consola em série em imagens personalizadas ou mais antigas](#enable-the-serial-console-in-custom-or-older-images) para obter instruções sobre como ativar manualmente o SAC no seu VM do Windows. Para mais informações, consulte os sinais de [saúde do Windows.](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Windows_Health_Info.md)
SAC não assume toda a área de Consola em Série no navegador | Este é um problema conhecido com o Windows e o emulador terminal. Estamos a acompanhar esta questão com ambas as equipas, mas por enquanto não há mitigação.
Não é possível escrever a sac prompt se a depuração do kernel estiver ativada. | RDP para VM `bcdedit /debug {current} off` e correr de um pedido de comando elevado. Se não conseguir RDP, pode, em vez disso, fixar o disco OS a outro `bcdedit /store <drive letter of data disk>:\boot\bcd /debug <identifier> off`VM Azure e modificá-lo enquanto está ligado como um disco de dados, executando, e, em seguida, trocando o disco de volta.
Colar no PowerShell em SAC resulta num terceiro personagem se o conteúdo original tivesse um carácter repetitivo. | Para uma suposição, corra `Remove-Module PSReadLine` para descarregar o módulo PSReadLine da sessão atual. Esta ação não eliminará nem desinstalará o módulo.
Algumas inputs de teclado produzem uma saída sac estranha (por exemplo, **[A,** **[3~**). | As sequências de fuga [VT100](https://aka.ms/vtsequences) não são suportadas pelo pedido do SAC.
Colar cordas compridas não funciona. | A consola em série limita o comprimento das cordas coladas no terminal a caracteres de 2048 para evitar sobrecarregar a largura de banda da porta de série.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

**Q. Como posso enviar feedback?**

R. Fornecer feedback criando um problema https://aka.ms/serialconsolefeedbackGitHub em . Alternativamente (menos preferencial), pode azserialhelp@microsoft.com enviar feedback através ou https://feedback.azure.comna categoria de máquina virtual de .

**Q. A consola em série suporta cópia/pasta?**

R. Sim. Utilize o**turno**+**C** e o**turno**+V da **CTRL**+ **Ctrl**+**para** copiar e colar no terminal.

**Q. Quem pode ativar ou desativar a consola em série para a minha subscrição?**

R. Para ativar ou desativar a consola em série a nível de subscrição, deve ter permissões de escrita para a subscrição. As funções que tenham permissão de escrita incluem funções de administrador ou proprietário. Os papéis personalizados também podem ter permissões de escrita.

**Q. Quem pode aceder à consola em série para o meu VM?**

R. Deve ter a função de Colaborador de Máquina Virtual ou superior para que um VM aceda à consola em série do VM.

**Q. A minha consola em série não está a exibir nada, o que faço?**

R. A sua imagem é provavelmente mal configurada para acesso à consola em série. Para obter informações sobre a configuração da sua imagem para ativar a consola em série, consulte [Ativar a consola em série em imagens personalizadas ou mais antigas](#enable-the-serial-console-in-custom-or-older-images).

**Q. A consola em série está disponível para conjuntos de escala de máquinas virtuais?**

R. Sim, é! Ver [consola em série para conjuntos](./serial-console-overview.md#serial-console-for-virtual-machine-scale-sets) de escala de máquina virtual

## <a name="next-steps"></a>Passos seguintes
* Para obter um guia aprofundado dos comandos CMD e PowerShell que pode utilizar no Windows SAC, consulte [comandos Windows: CMD e PowerShell](serial-console-cmd-ps-commands.md).
* A consola em série também está disponível para [VMs Linux.](serial-console-linux.md)
* Saiba mais sobre diagnósticos de [arranque.](boot-diagnostics.md)
