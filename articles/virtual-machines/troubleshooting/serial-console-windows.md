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
ms.openlocfilehash: 87ccb1c4995337b385f685797980a9fc3962bc6f
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78381687"
---
# <a name="azure-serial-console-for-windows"></a>Consola em série Azure para Windows

A Consola Em Série no portal Azure fornece acesso a uma consola baseada em texto para máquinas virtuais Windows (VMs) e instâncias de conjunto de máquinas virtuais. Esta ligação em série liga-se à porta de série COM1 da instância de conjunto de banda vm ou virtual, proporcionando-lhe acesso independentemente da rede ou do estado do sistema operativo. A consola em série só pode ser acedida através do portal Azure e só é permitida para os utilizadores que tenham uma função de acesso de Colaborador ou superior ao conjunto de escala de máquinas VM ou virtual.

A Consola Série funciona da mesma forma para VMs e instâncias de conjunto de métricas de máquinas virtuais. Neste doc, todas as menções aos VMs incluirão implicitamente instâncias de conjunto de máquinas virtuais, salvo indicação em contrário.

Para obter documentação em série para consolas para Linux, consulte [A Consola Em Série Azure para Linux](serial-console-linux.md).

> [!NOTE]
> A Consola Em Série está geralmente disponível nas regiões globais do Azure e em antevisão pública no Governo azure. Ainda não está disponível na nuvem azure china.


## <a name="prerequisites"></a>Pré-requisitos

* A sua configuração de conjunto de métricas vm ou virtual deve utilizar o modelo de implementação da gestão de recursos. Implementações clássicas não são suportadas.

- A sua conta que utiliza a consola em série deve ter a [função de Colaborador de Máquina Virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) para o VM e a conta de armazenamento de diagnóstico de [arranque](boot-diagnostics.md)

- A sua configuração de conjunto de métricas vM ou virtual deve ter um utilizador baseado em palavra-passe. Pode criar um com a função de [palavra-passe redefinida](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) da extensão de acesso VM. Selecione **Redefinir a palavra-passe** da secção Suporte + resolução de **problemas.**

* O VM para a configuração de conjunto de máquinas virtuais deve ter [diagnósticos](boot-diagnostics.md) de arranque ativados.

    ![Definições de diagnóstico de arranque](../media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

## <a name="enable-serial-console-functionality-for-windows-server"></a>Ativar a funcionalidade da consola em série para o Windows Server

> [!NOTE]
> Se não estiver a ver nada na consola em série, certifique-se de que o diagnóstico de arranque está ativado no seu conjunto de escala de máquinas VM ou virtual.

### <a name="enable-the-serial-console-in-custom-or-older-images"></a>Ativar a consola de série em imagens personalizadas ou mais antigas
As imagens mais recentes do Windows Server no Azure têm consola de [administração especial](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) (SAC) ativada por padrão. SAC é suportada em versões de servidor do Windows, mas não está disponível em versões de cliente (por exemplo, Windows 10, Windows 8 ou Windows 7).

Para imagens do Windows Server mais antigas (criadas antes de Fevereiro de 2018), pode ativar automaticamente a consola de série por meio do recurso de comando de execução do portal do Azure. No portal Azure, selecione comando **executar**e, em seguida, selecione o comando denominado **EnableEMS** da lista.

![Executar a lista de comandos](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-runcommand.png)

Em alternativa, para ativar manualmente a consola em série para o conjunto de escala de máquinas VMs/máquinavirtual windows criado antes de fevereiro de 2018, siga estes passos:

1. Ligar a sua máquina virtual do Windows utilizando o ambiente de trabalho remoto
1. A partir de uma linha de comandos administrativa, execute os seguintes comandos:
    - `bcdedit /ems {current} on`
    - `bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200`
1. Reinicialize o sistema para a consola de SAC seja ativado.

    ![Consola de SAC](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

Se for necessário, o SAC pode ser ativadas offline também:

1. Anexe o disco do windows para o qual deseja SAC configurado como um disco de dados para a VM existente.

1. A partir de uma linha de comandos administrativa, execute os seguintes comandos:
   - `bcdedit /store <mountedvolume>\boot\bcd /ems {default} on`
   - `bcdedit /store <mountedvolume>\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

#### <a name="how-do-i-know-if-sac-is-enabled"></a>Como posso saber se SAC está ativado?

Se o [SAC](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) não estiver ativado, a consola em série não apresentará o pedido SAC. Em alguns casos, informações de estado de funcionamento da VM são apresentadas e, em outros casos está em branco. Se estiver a utilizar uma imagem do Windows Server criada antes de Fevereiro de 2018, SAC provavelmente não estar ativada.

### <a name="enable-the-windows-boot-menu-in-the-serial-console"></a>Habilitar o menu de arranque do Windows na consola de série

Se precisar de ativar Windows prompts de carregador de arranque apresentar na consola de série, pode adicionar as seguintes opções adicionais aos seus dados de configuração de arranque. Para mais informações, consulte [bcdedit.](https://docs.microsoft.com/windows-hardware/drivers/devtest/bcdedit--set)

1. Ligue-se à sua configuração de placa de máquina selelérvia VM ou virtual utilizando o Ambiente de Trabalho Remoto.

1. A partir de uma linha de comandos administrativa, execute os seguintes comandos:
   - `bcdedit /set {bootmgr} displaybootmenu yes`
   - `bcdedit /set {bootmgr} timeout 10`
   - `bcdedit /set {bootmgr} bootems yes`

1. Reinicie o sistema para o menu de arranque seja ativado

> [!NOTE]
> O tempo limite que definiu para o menu de Gestor de arranque apresentar irá afetar o tempo de arranque do sistema operacional. Se acha que o valor de tempo limite de 10 segundos é demasiado curto ou demasiado longo, defini-lo para um valor diferente.

## <a name="use-serial-console"></a>Utilizar consola em série

### <a name="use-cmd-or-powershell-in-serial-console"></a>Utilize CMD ou PowerShell na consola em série

1. Ligar à consola de série. Se conseguir ligar com sucesso, o aviso é **SAC>**

    ![Ligar ao SAC](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect-sac.png)

1.  Insira `cmd` para criar um canal que tenha uma instância CMD.

1.  Introduza `ch -si 1` ou pressione `<esc>+<tab>` teclas de atalho para mudar para o canal que está a executar a instância CMD.

1.  Pressione **Entrar**e, em seguida, insira credenciais de entrada com permissões administrativas.

1.  Após introduzir as credenciais válidas, abre-se a instância CMD.

1.  Para iniciar uma instância PowerShell, introduza `PowerShell` na instância CMD e, em seguida, prima **Enter**.

    ![Abra a instância do PowerShell](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-powershell.png)

### <a name="use-the-serial-console-for-nmi-calls"></a>Utilizar a consola de série para chamadas de NMI
Uma interrupção não maskable (NMI) foi concebida para criar um sinal de que o software numa máquina virtual não ignorar. Historicamente, NMIs foram utilizadas para monitorizar a existência de problemas de hardware em sistemas que exigem tempos de resposta específica. Hoje em dia, os programadores e os administradores de sistemas usam frequentemente o NMI como um mecanismo para depurar ou resolver sistemas que não estão a responder.

A consola de série pode ser utilizada para enviar um NMI para uma máquina virtual do Azure com o ícone de teclado na barra de comandos. Depois do NMI é enviado, a configuração de máquina virtual irá controlar como o sistema responde. Windows podem ser configurado para falhas e crie um arquivo de despejo de memória, ao receber um NMI.

![Enviar NMI](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-nmi.png) <br>

Para obter informações sobre a configuração do Windows para criar um ficheiro de despejo de falha quando receber um NMI, consulte como gerar um ficheiro de despejo de [falha utilizando um NMI](https://support.microsoft.com/help/927069/how-to-generate-a-complete-crash-dump-file-or-a-kernel-crash-dump-file).

### <a name="use-function-keys-in-serial-console"></a>Utilizar teclas de função na consola de série
Teclas de função estão ativadas para utilização para a consola de série em VMs do Windows. A tecla F8 na lista pendente de consola de série fornece a conveniência da introdução facilmente o menu de definições avançadas de inicialização, mas a consola de série é compatível com todas as outras teclas de função. Pode ser necessário premir **Fn** + **F1** (ou F2, F3, etc.) no teclado, dependendo do computador de onde está a utilizar a consola em série.

### <a name="use-wsl-in-serial-console"></a>Utilizar WSL na consola de série
O subsistema Windows para Linux (WSL) foi ativado para o Windows Server 2019 ou posterior, pelo que também é possível ativar WSL para uso dentro da consola de série, se estiver a executar o Windows Server 2019 ou posterior. Isso pode ser benéfico para os utilizadores que também tem uma familiaridade com os comandos de Linux. Para obter instruções para ativar o WSL para o Windows Server, consulte o guia de [instalação](https://docs.microsoft.com/windows/wsl/install-on-server).

### <a name="restart-your-windows-vmvirtual-machine-scale-set-instance-within-serial-console"></a>Reinicie a sua instância de conjunto de escala de máquina seleção VM/virtual no meio da consola em série
Pode iniciar um reinício dentro da consola em série navegando para o botão de alimentação e clicando em "Restart VM". Isto irá iniciar um reinício vm, e você verá uma notificação dentro do portal Azure sobre o reinício.

Isto é útil em situações em que poderá querer aceder ao menu de arranque sem deixar a experiência da consola em série.

![Reinício da consola de série do Windows](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-windows.gif)

## <a name="disable-the-serial-console"></a>Desativar a consola em série
Por padrão, todas as subscrições têm acesso à consola em série ativada. Pode desativar a consola em série ao nível de subscrição ou ao nível de conjunto de escala vm/máquina virtual. Para obter instruções detalhadas, visite [Enable e desative a consola em série Azure](./serial-console-enable-disable.md).

## <a name="serial-console-security"></a>Segurança da consola de série

### <a name="access-security"></a>Segurança de acesso
O acesso à consola em série está limitado aos utilizadores que tenham uma função de acesso ao [Virtual Machine Contributor](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) ou superior à máquina virtual. Se o seu inquilino do Diretório Ativo Azure necessitar de autenticação multi-factor (MFA), então o acesso à consola em série também necessitará de MFA porque o acesso da consola em série é através do [portal Azure.](https://portal.azure.com)

### <a name="channel-security"></a>Segurança de canal
Todos os dados que são enviados e volta são encriptados na conexão.

### <a name="audit-logs"></a>Registos de auditoria
Todo o acesso à consola em série está atualmente registado nos [registos](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) de diagnóstico de arranque da máquina virtual. Acesso a estes registos são propriedade e controlado pelo administrador de máquina virtual do Azure.

> [!CAUTION]
> Sem palavras-passe de acesso para a consola são registadas. No entanto, se os comandos são executados dentro da consola contém ou palavras-passe, segredos, os nomes de utilizador ou qualquer outra forma de informações de identificação pessoal (PII) de saída, aqueles serão escritos nos registos de diagnóstico de arranque VM. Eles serão escritos, juntamente com todos os outro texto visível, como parte da implementação de volta a rolagem da consola de série função. Estes registos são circulares e apenas indivíduos com permissões de leitura à conta de armazenamento de diagnóstico tem acesso aos mesmos. No entanto, recomendamos que siga a prática recomendada de usar o ambiente de trabalho remoto para tudo o que pode envolver segredos e/ou PII.

### <a name="concurrent-usage"></a>Utilização em simultâneo
Se um usuário estiver conectado à consola de série e outro utilizador com êxito pedidos de acesso a essa mesma máquina virtual, o primeiro utilizador será desligado e o segundo utilizador ligado à mesma sessão.

> [!CAUTION]
> Isto significa que um utilizador desligado não será desligado. A capacidade de impor um logout após a desconexão (utilizando sIGHUP ou mecanismo semelhante) ainda está no roteiro. Para Windows, há um tempo limite automático ativado no SAC; para o Linux, pode configurar a definição de tempo limite de terminal.

## <a name="accessibility"></a>Acessibilidade
A acessibilidade é um foco principal para a consola de série do Azure. Para esse fim, garantimos que a consola de série está acessível para o elemento visual e ouvir reduzida, bem como as pessoas que podem não ser capazes de usar um mouse.

### <a name="keyboard-navigation"></a>Navegação do teclado
Utilize a tecla **Tab** no seu teclado para navegar na interface de consola em série a partir do portal Azure. Sua localização será realçada na tela. Para deixar o foco da janela da consola em série, prima **CTRL**+**F6** no teclado.

### <a name="use-the-serial-console-with-a-screen-reader"></a>Utilizar a consola de série com um leitor de ecrã
A consola de série tem suporte de leitor de ecrã incorporado. Navegação com um leitor de ecrã ativado, permitirá que o texto alternativo para o botão selecionado atualmente a ser lido em voz alta pelo leitor de ecrã.

## <a name="common-scenarios-for-accessing-the-serial-console"></a>Cenários comuns para aceder à consola de série

Cenário          | Ações na consola de série
:------------------|:-----------------------------------------
Regras de firewall incorreta | Aceda a seriais regras de firewall de Windows de consola e correção.
Danos/verificação de sistema de ficheiros | Aceder à consola de série e recuperar o sistema de ficheiros.
Problemas de configuração de RDP | Aceder à consola de série e alterar as definições. Para mais informações, consulte a documentação do [PDR.](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-allow-access)
Bloqueio de rede para baixo do sistema | Aceda à consola de série do portal do Azure para gerir o sistema. Alguns comandos de rede estão listados em [comandos Windows: CMD e PowerShell](serial-console-cmd-ps-commands.md).
Interagir com o carregador de inicialização | Acesso BCD através da consola de série. Para obter informações, consulte [Ativar o menu](#enable-the-windows-boot-menu-in-the-serial-console)de arranque do Windows na consola em série .

## <a name="known-issues"></a>Problemas conhecidos
Estamos cientes de alguns problemas com a consola em série e o sistema operativo da VM. Aqui está uma lista destes problemas e passos para mitigação para VMs windows. Estas questões e atenuações aplicam-se tanto para os VMs como para as instâncias de conjunto de máquinas virtuais. Se estes não corresponderem ao erro que está a ver, veja os erros comuns de serviço da consola em série nos [erros da Consola Série Comum](./serial-console-errors.md).

Problema                             |   Mitigação
:---------------------------------|:--------------------------------------------|
Pressionar **Introduza** depois de o banner de ligação não causar a visualização de uma solicitação de entrada. | Para mais informações, consulte [hit enter não faz nada](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). Este erro pode ocorrer se estiver a executar uma VM personalizada, a aplicação protegida ou a configuração de arranque que faz com que o Windows não sejam corretamente ligar para a porta serial. Este erro também ocorrerá se estiver a executar um VM Windows 10, porque apenas os VMs do Windows Server estão configurados para ter EMS ativado.
Apenas as informações de estado de funcionamento são mostradas ao ligar a uma VM do Windows| Este erro ocorre se a Consola de Administração Especial não tiver sido ativada para a sua imagem do Windows. Consulte [a consola em série em imagens personalizadas ou mais antigas](#enable-the-serial-console-in-custom-or-older-images) para obter instruções sobre como ativar manualmente o SAC no seu VM do Windows. Para mais informações, consulte os sinais de [saúde do Windows.](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Windows_Health_Info.md)
SAC não assume toda a área de Consola em Série no navegador | Este é um problema conhecido com o Windows e o emulador terminal. Estamos a acompanhar esta questão com ambas as equipas, mas por enquanto não há mitigação.
Não é possível escreva na linha SAC perguntar se a depuração de kernel está ativada. | RDP para VM e executar `bcdedit /debug {current} off` a partir de um pedido de comando elevado. Se não conseguir RDP, pode, em vez disso, fixar o disco OS a outro VM Azure e modificá-lo enquanto está ligado como um disco de dados, executando `bcdedit /store <drive letter of data disk>:\boot\bcd /debug <identifier> off`, trocando o disco de volta.
Colar no PowerShell nos resultados de SAC num caractere de terceiro, se o conteúdo original tinha um caractere repetido. | Para uma suposição, corra `Remove-Module PSReadLine` para descarregar o módulo PSReadLine da sessão atual. Esta ação não irá eliminar ou desinstalar o módulo.
Algumas inputs de teclado produzem uma saída sac estranha (por exemplo, **[A,** **[3~** ). | As sequências de fuga [VT100](https://aka.ms/vtsequences) não são suportadas pelo pedido do SAC.
Colar longas seqüências de caracteres não funciona. | A consola de série limita o comprimento de cadeias de caracteres colado no terminal para 2048 carateres para evitar sobrecarregar a largura de banda da porta serial.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

**Q. Como posso enviar feedback?**

A. Fornecer feedback criando um problema GitHub em https://aka.ms/serialconsolefeedback. Alternativamente (menos preferencial), pode enviar feedback através de azserialhelp@microsoft.com ou na categoria de máquina virtual de https://feedback.azure.com.

**Q. A consola em série suporta cópia/pasta?**

A. Sim. Utilize **o Turno+CTRL**+**C** e **Ctrl**+**Shift**+**V** para copiar e colar no terminal.

**Q. Quem pode ativar ou desativar a consola em série para a minha subscrição?**

A. Para ativar ou desativar a consola de série a um nível de toda a subscrição, tem de ter permissões de escrita para a subscrição. As funções que tem permissão de escrita incluem funções de administrador ou proprietário. Funções personalizadas também podem ter permissões de escrita.

**Q. Quem pode aceder à consola em série para o meu VM?**

A. Tem de ter a função de contribuinte de Máquina Virtual ou superior para uma VM para aceder à consola de série da VM.

**Q. A minha consola em série não está a exibir nada, o que faço?**

A. A imagem é provavelmente mal configurada para acesso à consola de série. Para obter informações sobre a configuração da sua imagem para ativar a consola em série, consulte [Ativar a consola em série em imagens personalizadas ou mais antigas](#enable-the-serial-console-in-custom-or-older-images).

**Q. A consola em série está disponível para conjuntos de escala de máquinas virtuais?**

A. Sim, é! Ver [consola em série para conjuntos](./serial-console-overview.md#serial-console-for-virtual-machine-scale-sets) de escala de máquina virtual

## <a name="next-steps"></a>Passos seguintes
* Para obter um guia aprofundado dos comandos CMD e PowerShell que pode utilizar no Windows SAC, consulte [comandos Windows: CMD e PowerShell](serial-console-cmd-ps-commands.md).
* A consola em série também está disponível para [VMs Linux.](serial-console-linux.md)
* Saiba mais sobre diagnósticos de [arranque.](boot-diagnostics.md)
