---
title: Consola em série Azure para Windows Microsoft Docs
description: Bi-Directional consola em série para máquinas virtuais Azure e conjuntos de balança de máquinas virtuais usando um exemplo windows.
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
ms.openlocfilehash: c5c139cb94358d70d1f23b68f2a369adb953da08
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91325985"
---
# <a name="azure-serial-console-for-windows"></a>Consola em série Azure para Windows

A Consola em Série no portal Azure fornece acesso a uma consola baseada em texto para máquinas virtuais Windows (VMs) e instâncias de conjunto de escala de máquina virtual. Esta ligação em série liga-se à porta em série COM1 da placa de conjunto de escala vm ou de máquina virtual, proporcionando acesso ao mesmo independente da rede ou do estado do sistema operativo. A consola em série só pode ser acedida através do portal Azure e só é permitida para os utilizadores que tenham uma função de acesso de Colaborador ou superior ao conjunto de escala de VM ou de máquina virtual.

A Consola em Série funciona da mesma forma para VMs e instâncias de escala de máquinas virtuais. Neste doc, todas as menções aos VMs incluirão implicitamente instâncias de conjunto de escala de máquina virtual, salvo indicação em contrário.

A Consola Em Série está geralmente disponível nas regiões globais do Azure e em pré-visualização pública no Governo de Azure. Ainda não está disponível na nuvem Azure China.

Para documentação de consola em série para Linux, consulte [a Consola em Série Azure para Linux](serial-console-linux.md).

> [!NOTE]
> A Consola em Série é atualmente incompatível com uma conta de armazenamento de diagnóstico de arranque gerida. Para utilizar a Consola em Série, certifique-se de que está a utilizar uma conta de armazenamento personalizada.


## <a name="prerequisites"></a>Pré-requisitos

* A sua instância de definição de escala de VM ou de máquina virtual deve utilizar o modelo de implementação de gestão de recursos. As implantações clássicas não são suportadas.

- A sua conta que utiliza consola em série deve ter o [papel de Contribuinte de Máquina Virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) para o VM e a conta de armazenamento de diagnóstico de [arranque](boot-diagnostics.md)

- A sua instância de definição de escala de VM ou de máquina virtual deve ter um utilizador baseado em palavra-passe. Pode criar uma com a função [de senha de reset](../extensions/vmaccess.md#reset-password) da extensão de acesso VM. Selecione Redefinir a **palavra-passe** a partir da secção **'Suporte + resolução de problemas'.**

* O VM para a definição de escala de máquina virtual deve ter [diagnósticos de arranque](boot-diagnostics.md) ativados.

    ![Definições de diagnóstico de arranque](../media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

## <a name="enable-serial-console-functionality-for-windows-server"></a>Ativar a funcionalidade do Consola em Série para o Servidor do Windows

> [!NOTE]
> Se não estiver a ver nada na consola em série, certifique-se de que os diagnósticos de arranque estão ativados no seu conjunto de escala de VM ou de máquina virtual.

### <a name="enable-the-serial-console-in-custom-or-older-images"></a>Ativar a consola em série em imagens personalizadas ou mais antigas
As imagens mais recentes do Servidor do Windows no Azure têm [consola de administração especial](/previous-versions/windows/it-pro/windows-server-2003/cc787940(v=ws.10)) (SAC) ativada por padrão. O SAC é suportado nas versões do servidor do Windows, mas não está disponível nas versões dos clientes (por exemplo, Windows 10, Windows 8 ou Windows 7).

Para imagens mais antigas do Windows Server (criadas antes de fevereiro de 2018), pode ativar automaticamente a consola em série através da funcionalidade de comando do portal Azure. No portal Azure, selecione **o comando executar**e, em seguida, selecione o comando chamado **EnableEMS** da lista.

![Executar lista de comandos](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-runcommand.png)

Em alternativa, para ativar manualmente a consola em série para o conjunto de escala de máquinas windows VMs/virtual criado antes de fevereiro de 2018, siga estes passos:

1. Ligue-se à sua máquina virtual Windows utilizando o Ambiente de Trabalho Remoto
1. A partir de um pedido de comando administrativo, executar os seguintes comandos:
    - `bcdedit /ems {current} on`, ou `bcdedit /ems '{current}' on` se estiver a usar o PowerShell
    - `bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200`
1. Reinicie o sistema para a consola SAC ser ativada.

    ![Consola SAC](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

Se necessário, o SAC também pode ser ativado offline:

1. Fixe o disco de janelas para o qual pretende que o SAC seja configurado como um disco de dados ao VM existente.

1. A partir de um pedido de comando administrativo, executar os seguintes comandos:
   - `bcdedit /store <mountedvolume>\boot\bcd /ems {default} on`
   - `bcdedit /store <mountedvolume>\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

#### <a name="how-do-i-know-if-sac-is-enabled"></a>Como sei se o SAC está habilitado?

Se [o SAC](/previous-versions/windows/it-pro/windows-server-2003/cc787940(v=ws.10)) não estiver ativado, a consola em série não apresentará a solicitação SAC. Em alguns casos, a informação de saúde em VM é mostrada, e em outros casos está em branco. Se estiver a utilizar uma imagem do Windows Server criada antes de fevereiro de 2018, a SAC provavelmente não estará ativada.

### <a name="enable-the-windows-boot-menu-in-the-serial-console"></a>Ativar o menu inicial do Windows na consola em série

Se precisar de ativar as solicitações de carregamento de arranque do Windows para exibir na consola em série, pode adicionar as seguintes opções adicionais aos dados de configuração do arranque. Para mais informações, consulte [bcdedit.](/windows-hardware/drivers/devtest/bcdedit--set)

1. Ligue-se ao seu Windows VM ou à definição de escala de máquina virtual utilizando o Ambiente de Trabalho Remoto.

1. A partir de um pedido de comando administrativo, executar os seguintes comandos:
   - `bcdedit /set {bootmgr} displaybootmenu yes`
   - `bcdedit /set {bootmgr} timeout 10`
   - `bcdedit /set {bootmgr} bootems yes`

1. Reinicie o sistema para o menu de arranque a ser ativado

> [!NOTE]
> O tempo limite que definiu para o menu do boot manager para exibir terá impacto no tempo de arranque do seu SISTEMA. Se acha que o valor de intervalo de 10 segundos é demasiado curto ou longo, desa um valor diferente.

## <a name="use-serial-console"></a>Use consola em série

### <a name="use-cmd-or-powershell-in-serial-console"></a>Utilize CMD ou PowerShell na consola em série

1. Ligue-se à consola em série. Se ligar com sucesso, o pedido é **SAC>: **

   ![Ligue-se ao SAC](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect-sac.png)
1. `cmd`Insira para criar um canal que tenha uma instância CMD.

1. Introduza `ch -si 1` ou pressione as `<esc>+<tab>` teclas de atalho para mudar para o canal que está a executar a instância CMD.

1. Pressione **Intromia**e, em seguida, introduza credenciais de inscrição com permissões administrativas.

1. Depois de ter introduzido credenciais válidas, abre-se a instância CMD.

1. Para iniciar uma instância PowerShell, insira `PowerShell` na instância CMD e, em seguida, prima **Enter**.

   ![Exemplo Open PowerShell](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-powershell.png)

### <a name="use-the-serial-console-for-nmi-calls"></a>Utilize a consola em série para chamadas NMI
Uma interrupção não mascarada (NMI) foi concebida para criar um sinal de que o software numa máquina virtual não ignorará. Historicamente, as NMIs têm sido usadas para monitorizar problemas de hardware em sistemas que exigiam tempos de resposta específicos. Hoje em dia, programadores e administradores de sistemas usam frequentemente o NMI como um mecanismo para depurar ou resolver sistemas que não respondem.

A consola em série pode ser utilizada para enviar um NMI para uma máquina virtual Azure utilizando o ícone do teclado na barra de comando. Após a entrega do NMI, a configuração da máquina virtual controlará a forma como o sistema responde. As janelas podem ser configuradas para se despenhar e criar um ficheiro de despejo de memória ao receber um NMI.

![Enviar NMI](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-nmi.png) <br>

Para obter informações sobre a configuração do Windows para criar um ficheiro de despejo de falhas quando receber um NMI, consulte [como gerar um ficheiro de despejo de falhas utilizando um NMI](https://support.microsoft.com/help/927069/how-to-generate-a-complete-crash-dump-file-or-a-kernel-crash-dump-file).

### <a name="use-function-keys-in-serial-console"></a>Utilize teclas de função na consola em série
As teclas de função estão ativadas para utilização para consola em série em VMs do Windows. O F8 no dropdown da consola em série proporciona a conveniência de entrar facilmente no menu Definições avançadas de arranque, mas a consola em série é compatível com todas as outras teclas de função. Poderá ter de pressionar **Fn**  +  **F1** (ou F2, F3, etc.) no seu teclado, dependendo do computador a partir do computador a partir do seguinte.

### <a name="use-wsl-in-serial-console"></a>Use wSL na consola em série
O Subsistema Windows para Linux (WSL) foi ativado para o Windows Server 2019 ou posteriormente, pelo que também é possível ativar o WSL para utilização dentro da consola em série se estiver a executar o Windows Server 2019 ou posteriormente. Isto pode ser benéfico para os utilizadores que também têm uma familiaridade com os comandos Linux. Para obter instruções para ativar o WSL para o Windows Server, consulte o [guia de instalação](/windows/wsl/install-on-server).

### <a name="restart-your-windows-vmvirtual-machine-scale-set-instance-within-serial-console"></a>Reinicie a sua instância definida em escala de máquina virtual/Windows VM/virtual dentro da Consola Em Série
Pode iniciar um reinício dentro da consola em série navegando para o botão de alimentação e clicando em "Reiniciar VM". Isto iniciará um reinício de VM, e verá uma notificação dentro do portal Azure sobre o reinício.

Isto é útil em situações em que poderá querer aceder ao menu de arranque sem deixar a experiência da consola em série.

![Reiniciar consolas em série do Windows](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-windows.gif)

## <a name="disable-the-serial-console"></a>Desativar a Consola em Série
Por padrão, todas as subscrições têm acesso à consola em série ativado. Pode desativar a consola em série ao nível de subscrição ou ao nível de escala de máquina vm/virtual. Para obter instruções detalhadas, visite [Ativar e desativar a Consola em Série Azure](./serial-console-enable-disable.md).

## <a name="serial-console-security"></a>Segurança da consola em série

### <a name="access-security"></a>Segurança de acesso
O acesso à consola em série está limitado a utilizadores que tenham uma função de acesso do [Virtual Machine Contributor](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) ou superior à máquina virtual. Se o seu inquilino do Azure Ative Directory necessitar de autenticação multi-factor (MFA), então o acesso à consola em série também necessitará de MFA porque o acesso da consola em série é através do [portal Azure.](https://portal.azure.com)

### <a name="channel-security"></a>Segurança do canal
Todos os dados enviados de um lado para o outro estão encriptados no fio.

### <a name="audit-logs"></a>Registos de auditoria
Todo o acesso à consola em série está atualmente registado nos [registos](./boot-diagnostics.md) de diagnóstico de arranque da máquina virtual. O acesso a estes registos é propriedade e controlado pelo administrador de máquina virtual Azure.

> [!CAUTION]
> Não são registadas senhas de acesso para a consola. No entanto, se os comandos executados dentro da consola contiverem ou derramar palavras-passe, segredos, nomes de utilizador ou qualquer outra forma de informação pessoalmente identificável (PII), esses serão escritos para os registos de diagnóstico de arranque VM. Serão escritas juntamente com todos os outros textos visíveis, como parte da implementação da função de retrocesso da consola em série. Estes registos são circulares e apenas os indivíduos com permissões de leitura para a conta de armazenamento de diagnósticos têm acesso aos mesmos. No entanto, recomendamos seguir a melhor prática de usar o Ambiente de Trabalho Remoto para qualquer coisa que possa envolver segredos e/ou PII.

### <a name="concurrent-usage"></a>Utilização simultânea
Se um utilizador estiver ligado à consola em série e outro utilizador solicitar com sucesso o acesso à mesma máquina virtual, o primeiro utilizador será desligado e o segundo utilizador ligado à mesma sessão.

> [!CAUTION]
> Isto significa que um utilizador desligado não será desligado. A capacidade de impor um logout após a desconexão (utilizando SIGHUP ou mecanismo semelhante) ainda está no roteiro. Para o Windows, existe um tempo limite automático ativado no SAC; para o Linux, pode configurar a definição de tempo limite de terminal.

## <a name="accessibility"></a>Acessibilidade
A acessibilidade é um foco chave para a consola em série Azure. Para tal, garantimos que a consola em série é acessível para pessoas com deficiência visual, ou que são difíceis de ouvir, bem como pessoas que podem não ser capazes de usar um rato.

### <a name="keyboard-navigation"></a>Navegação do teclado
Utilize a tecla **'Separador'** no teclado para navegar na interface da consola em série a partir do portal Azure. A sua localização será destacada no ecrã. Para deixar o foco da janela da consola em série, prima **Ctrl** + **F6** no seu teclado.

### <a name="use-the-serial-console-with-a-screen-reader"></a>Use a consola em série com um leitor de ecrã
A consola em série tem suporte para leitores de ecrã incorporado. Navegar por aí com um leitor de ecrã ligado permitirá que o texto alt para o botão atualmente selecionado seja lido em voz alta pelo leitor de ecrã.

## <a name="common-scenarios-for-accessing-the-serial-console"></a>Cenários comuns para aceder à consola em série

Cenário          | Ações na consola em série
:------------------|:-----------------------------------------
Regras de firewall incorretas | Aceda à consola em série e corrija as regras de firewall do Windows.
Corrupção/verificação do sistema de ficheiros | Aceda à consola em série e recupere o sistema de ficheiros.
Problemas de configuração RDP | Aceda à consola em série e altere as definições. Para mais informações, consulte a [documentação do PDR.](/windows-server/remote/remote-desktop-services/clients/remote-desktop-allow-access)
Sistema de bloqueio de rede | Aceda à consola em série a partir do portal Azure para gerir o sistema. Alguns comandos de rede estão listados nos [comandos Windows: CMD e PowerShell](serial-console-cmd-ps-commands.md).
Interagindo com o bootloader | Aceda ao BCD através da consola em série. Para obter informações, consulte [Ativar o menu de arranque do Windows na consola em série](#enable-the-windows-boot-menu-in-the-serial-console).

## <a name="known-issues"></a>Problemas conhecidos
Estamos cientes de alguns problemas com a consola em série e o sistema operativo do VM. Aqui está uma lista destes problemas e passos para mitigação para VMs windows. Estas questões e mitigações aplicam-se tanto aos VMs como aos casos de escala de máquinas virtuais. Se estes não corresponderem ao erro que está a ver, consulte os erros comuns de serviço de consola em série nos [erros da Consola De Série Comum](./serial-console-errors.md).

Problema                             |   Mitigação
:---------------------------------|:--------------------------------------------|
Pressionar **Insira** depois do banner de ligação não fazer com que seja visualizada uma indicação de entrada. | Para mais informações, ver [Hitting enter não faz nada.](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md) Este erro pode ocorrer se estiver a executar um VM personalizado, um aparelho endurecido ou uma config de arranque que faça com que o Windows não se conecte corretamente à porta de série. Este erro também ocorrerá se estiver a executar um VM Do Windows 10, porque apenas os VMs do Windows Server estão configurados para ter EMS ativado.
Apenas são mostradas informações de saúde ao ligar-se a um VM do Windows| Este erro ocorre se a Consola De Administração Especial não tiver sido ativada para a sua imagem do Windows. Consulte [Ativar a consola em série em imagens personalizadas ou mais antigas](#enable-the-serial-console-in-custom-or-older-images) para obter instruções sobre como ativar manualmente o SAC no seu VM do Windows. Para obter mais informações, consulte [os sinais de saúde do Windows.](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Windows_Health_Info.md)
SAC não assume toda a área de Consola em Série no navegador | Este é um problema conhecido com o Windows e o emulador terminal. Estamos a acompanhar esta questão com ambas as equipas, mas por enquanto não há mitigação.
Não é possível digitar na pontuação SAC se estiver ativada a depuragem do kernel. | RDP para VM e corre `bcdedit /debug {current} off` de uma ômissão de comando elevada. Se não conseguir pDR, pode, em vez disso, ligar o disco DE Azure a outro Azure VM e modificá-lo enquanto está ligado como um disco de dados em `bcdedit /store <drive letter of data disk>:\boot\bcd /debug <identifier> off` execução, trocando o disco de volta.
Colar em PowerShell em SAC resulta num terceiro personagem se o conteúdo original tiver um carácter repetitivo. | Para uma solução alternativa, corra `Remove-Module PSReadLine` para descarregar o módulo PSReadLine da sessão atual. Esta ação não eliminará nem desinstalará o módulo.
Algumas entradas de teclado produzem uma saída SAC estranha (por exemplo, **[A**, **[3~**). | As sequências de fuga [vT100](https://aka.ms/vtsequences) não são suportadas pelo pedido da SAC.
Colar cordas compridas não funciona. | A consola em série limita o comprimento das cordas coladas no terminal aos caracteres de 2048 para evitar sobrecargas na largura de banda da porta em série.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

**Q. Como posso enviar feedback?**

A. Fornecer feedback criando um problema gitHub em https://aka.ms/serialconsolefeedback . Alternativamente (menos preferencial), pode enviar feedback através azserialhelp@microsoft.com ou na categoria de máquina virtual de https://feedback.azure.com .

**Q. A consola em série suporta a cópia/pasta?**

A. Sim. Utilize **ctrl** + **Shift** + **C** e **Ctrl** + **Shift** + **V** para copiar e colar no terminal.

**Q. Quem pode ativar ou desativar a consola em série para a minha subscrição?**

A. Para ativar ou desativar a consola em série a um nível de subscrição, tem de escrever permissões para a subscrição. As funções que têm permissão de escrita incluem funções de administrador ou proprietário. As funções personalizadas também podem ter permissões de escrita.

**Q. Quem pode aceder à consola em série para o meu VM?**

A. Tem de ter o papel de Contribuinte de Máquina Virtual ou superior para que um VM aceda à consola em série do VM.

**Q. A minha consola em série não está a mostrar nada, o que faço?**

A. A sua imagem está provavelmente mal configurada para o acesso à consola em série. Para obter informações sobre a configuração da sua imagem para ativar a consola em série, consulte [Ativar a consola em série em imagens personalizadas ou mais antigas.](#enable-the-serial-console-in-custom-or-older-images)

**Q. A consola em série está disponível para conjuntos de escala de máquinas virtuais?**

A. Sim, é! Ver [consola em série para conjuntos de escala de máquinas virtuais](./serial-console-overview.md#serial-console-for-virtual-machine-scale-sets)

## <a name="next-steps"></a>Passos seguintes
* Para obter um guia aprofundado dos comandos CMD e PowerShell que pode utilizar no Windows SAC, consulte os [comandos windows: CMD e PowerShell](serial-console-cmd-ps-commands.md).
* A consola em série também está disponível para Os VMs [Linux.](serial-console-linux.md)
* Saiba mais sobre [diagnósticos de arranque.](boot-diagnostics.md)
