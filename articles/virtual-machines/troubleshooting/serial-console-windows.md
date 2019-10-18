---
title: Console serial do Azure para Windows | Microsoft Docs
description: Console serial bidirecional para máquinas virtuais do Azure e conjuntos de dimensionamento de máquinas virtuais.
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
ms.openlocfilehash: 0d48f3eacad86dac520d837b80605a75cce8cfd5
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72514498"
---
# <a name="azure-serial-console-for-windows"></a>Console serial do Azure para Windows

O console serial no portal do Azure fornece acesso a um console baseado em texto para VMs (máquinas virtuais) do Windows e instâncias do conjunto de dimensionamento de máquinas virtuais. Essa conexão serial conecta-se à porta serial COM1 da VM ou à instância do conjunto de dimensionamento de máquinas virtuais, fornecendo acesso a ela independentemente da rede ou do estado do sistema operacional. O console serial só pode ser acessado usando o portal do Azure e é permitido somente para os usuários que têm uma função de acesso de colaborador ou superior ao conjunto de dimensionamento de máquinas virtuais ou VM.

O console serial funciona da mesma maneira para VMs e instâncias do conjunto de dimensionamento de máquinas virtuais. Neste documento, todas as menção a VMs irão incluir implicitamente as instâncias do conjunto de dimensionamento de máquinas virtuais, salvo indicação em contrário.

Para obter a documentação do console serial para Linux, consulte [console serial do Azure para Linux](serial-console-linux.md).

> [!NOTE]
> O console serial está geralmente disponível em regiões globais do Azure. Ele ainda não está disponível nas nuvens do Azure governamental ou do Azure China.


## <a name="prerequisites"></a>Pré-requisitos

* A instância do conjunto de dimensionamento de máquinas virtuais ou VM deve usar o modelo de implantação do gerenciamento de recursos. Não há suporte para implantações clássicas.

- Sua conta que usa o console serial deve ter a [função de colaborador de máquina virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) para a VM e a conta de armazenamento de [diagnóstico de inicialização](boot-diagnostics.md)

- Sua VM ou instância do conjunto de dimensionamento de máquinas virtuais deve ter um usuário baseado em senha. Você pode criar um com a função [Redefinir senha](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) da extensão de acesso à VM. Selecione **Redefinir senha** na seção **suporte + solução de problemas** .

* A VM para a instância do conjunto de dimensionamento de máquinas virtuais deve ter o [diagnóstico de inicialização](boot-diagnostics.md) habilitado.

    ![Configurações de diagnóstico de inicialização](../media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

## <a name="enable-serial-console-functionality-for-windows-server"></a>Habilitar a funcionalidade do console serial para o Windows Server

> [!NOTE]
> Se você não estiver vendo nada no console serial, verifique se o diagnóstico de inicialização está habilitado em sua VM ou conjunto de dimensionamento de máquinas virtuais.

### <a name="enable-the-serial-console-in-custom-or-older-images"></a>Habilitar o console serial em imagens personalizadas ou mais antigas
As imagens mais recentes do Windows Server no Azure têm o [console de administração especial](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) (SAC) habilitado por padrão. O SAC tem suporte nas versões de servidor do Windows, mas não está disponível nas versões do cliente (por exemplo, Windows 10, Windows 8 ou Windows 7).

Para imagens mais antigas do Windows Server (criadas antes de fevereiro de 2018), você pode habilitar automaticamente o console serial por meio do recurso de executar comando de portal do Azure. No portal do Azure, selecione **executar comando**e, em seguida, selecione o comando chamado **EnableEMS** na lista.

![Executar lista de comandos](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-runcommand.png)

Como alternativa, para habilitar manualmente o console serial para VMs do Windows/conjunto de dimensionamento de máquinas virtuais criado antes de fevereiro de 2018, siga estas etapas:

1. Conectar-se à sua máquina virtual do Windows usando Área de Trabalho Remota
1. Em um prompt de comando administrativo, execute os seguintes comandos:
    - `bcdedit /ems {current} on`
    - `bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200`
1. Reinicialize o sistema para que o console do SAC seja habilitado.

    ![Console do SAC](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

Se necessário, o SAC pode ser habilitado offline também:

1. Anexe o disco do Windows para o qual você deseja que o SAC seja configurado como um disco de dados para a VM existente.

1. Em um prompt de comando administrativo, execute os seguintes comandos:
   - `bcdedit /store <mountedvolume>\boot\bcd /ems {default} on`
   - `bcdedit /store <mountedvolume>\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

#### <a name="how-do-i-know-if-sac-is-enabled"></a>Como fazer saber se o SAC está habilitado?

Se o [SAC](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) não estiver habilitado, o console serial não exibirá o prompt do SAC. Em alguns casos, as informações de integridade da VM são mostradas e, em outros casos, estão em branco. Se você estiver usando uma imagem do Windows Server criada antes de fevereiro de 2018, o SAC provavelmente não estará habilitado.

### <a name="enable-the-windows-boot-menu-in-the-serial-console"></a>Habilitar o menu de inicialização do Windows no console serial

Se você precisar habilitar os prompts do carregador de inicialização do Windows para exibir no console serial, poderá adicionar as seguintes opções adicionais aos dados de configuração de inicialização. Para obter mais informações, consulte [bcdedit](https://docs.microsoft.com/windows-hardware/drivers/devtest/bcdedit--set).

1. Conecte-se à sua VM do Windows ou à instância do conjunto de dimensionamento de máquinas virtuais usando Área de Trabalho Remota.

1. Em um prompt de comando administrativo, execute os seguintes comandos:
   - `bcdedit /set {bootmgr} displaybootmenu yes`
   - `bcdedit /set {bootmgr} timeout 10`
   - `bcdedit /set {bootmgr} bootems yes`

1. Reinicialize o sistema para que o menu de inicialização seja habilitado

> [!NOTE]
> O tempo limite definido para o menu do Gerenciador de inicialização para ser exibido afetará o tempo de inicialização do sistema operacional. Se você considerar que o valor de tempo limite de 10 segundos é muito curto ou longo demais, defina-o com um valor diferente.

## <a name="use-serial-console"></a>Usar o console serial

### <a name="use-cmd-or-powershell-in-serial-console"></a>Usar o CMD ou o PowerShell no console serial

1. Conecte-se ao console serial. Se você se conectar com êxito, o prompt é **SAC >** :

    ![Conectar-se ao SAC](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect-sac.png)

1.  Insira `cmd` para criar um canal que tenha uma instância de CMD.

1.  Insira `ch -si 1` ou pressione `<esc>+<tab>` teclas de atalho para alternar para o canal que está executando a instância CMD.

1.  Pressione **Enter**e, em seguida, insira as credenciais de entrada com permissões administrativas.

1.  Depois que você inserir credenciais válidas, a instância de CMD será aberta.

1.  Para iniciar uma instância do PowerShell, digite `PowerShell` na instância do CMD e pressione **Enter**.

    ![Abrir instância do PowerShell](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-powershell.png)

### <a name="use-the-serial-console-for-nmi-calls"></a>Usar o console serial para chamadas NMI
Uma NMI (interrupção não mascarável) foi projetada para criar um sinal de que o software em uma máquina virtual não será ignorado. Historicamente, o NMIs foi usado para monitorar problemas de hardware em sistemas que exigiam tempos de resposta específicos. Atualmente, os programadores e administradores de sistema geralmente usam NMI como um mecanismo para depurar ou solucionar problemas de sistemas que não estão respondendo.

O console serial pode ser usado para enviar uma NMI para uma máquina virtual do Azure usando o ícone de teclado na barra de comandos. Depois que a NMI for entregue, a configuração da máquina virtual controlará como o sistema responde. O Windows pode ser configurado para falhar e criar um arquivo de despejo de memória ao receber um NMI.

![Enviar NMI](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-nmi.png) <br>

Para obter informações sobre como configurar o Windows para criar um arquivo de despejo de memória quando ele recebe um NMI, consulte [como gerar um arquivo de despejo de memória usando um NMI](https://support.microsoft.com/help/927069/how-to-generate-a-complete-crash-dump-file-or-a-kernel-crash-dump-file).

### <a name="use-function-keys-in-serial-console"></a>Usar chaves de função no console serial
As teclas de função são habilitadas para uso para o console serial em VMs do Windows. O botão F8 no menu suspenso do console serial fornece a conveniência de inserir facilmente o menu Advanced boot Settings (configurações avançadas de inicialização), mas o console serial é compatível com todas as outras chaves de função. Talvez seja necessário pressionar **Fn**  + **F1** (ou F2, F3, etc.) no teclado, dependendo do computador do qual você está usando o console serial.

### <a name="use-wsl-in-serial-console"></a>Usar o WSL no console serial
O subsistema do Windows para Linux (WSL) foi habilitado para o Windows Server 2019 ou posterior, portanto, também é possível habilitar o WSL para uso no console serial se você estiver executando o Windows Server 2019 ou posterior. Isso pode ser benéfico para os usuários que também têm familiaridade com os comandos do Linux. Para obter instruções para habilitar o WSL para Windows Server, consulte o [Guia de instalação](https://docs.microsoft.com/windows/wsl/install-on-server).

### <a name="restart-your-windows-vmvirtual-machine-scale-set-instance-within-serial-console"></a>Reiniciar sua instância de conjunto de dimensionamento de máquinas virtuais/VM do Windows dentro do console serial
Você pode iniciar uma reinicialização dentro do console serial navegando até o botão de energia e clicando em "reiniciar VM". Isso iniciará uma reinicialização de VM e você verá uma notificação dentro do portal do Azure em relação à reinicialização.

Isso é útil em situações em que você talvez queira acessar o menu de inicialização sem sair da experiência do console serial.

![Reinicialização do console serial do Windows](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-windows.gif)

## <a name="disable-the-serial-console"></a>Desabilitar o console serial
Por padrão, todas as assinaturas têm o acesso ao console serial habilitado. Você pode desabilitar o console serial no nível de assinatura ou de conjunto de dimensionamento de máquinas virtuais/VM. Para obter instruções detalhadas, visite [habilitar e desabilitar o console serial do Azure](./serial-console-enable-disable.md).

## <a name="serial-console-security"></a>Segurança de Console serial

### <a name="access-security"></a>Segurança de acesso
O acesso ao console serial é limitado aos usuários que têm uma função de acesso de [colaborador de máquina virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) ou superior à máquina virtual. Se seu locatário de Azure Active Directory exigir a MFA (autenticação multifator), o acesso ao console serial também precisará de MFA porque o acesso do console serial é por meio do [portal do Azure](https://portal.azure.com).

### <a name="channel-security"></a>Segurança de canal
Todos os dados que são enviados para frente e para trás são criptografados na conexão.

### <a name="audit-logs"></a>Registos de auditoria
Todo o acesso ao console serial está conectado atualmente aos logs de [diagnóstico de inicialização](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) da máquina virtual. O acesso a esses logs é de propriedade e controlado pelo administrador da máquina virtual do Azure.

> [!CAUTION]
> Nenhuma senha de acesso para o console do é registrada. No entanto, se os comandos executados no console contiverem ou gerarem senhas de saída, segredos, nomes de usuário ou qualquer outra forma de PII (informações de identificação pessoal), eles serão gravados nos logs de diagnóstico de inicialização da VM. Eles serão escritos junto com todos os outros textos visíveis, como parte da implementação da função rolar para trás do console serial. Esses logs são circulares e somente indivíduos com permissões de leitura para a conta de armazenamento de diagnóstico têm acesso a eles. No entanto, é recomendável seguir a prática recomendada de usar o Área de Trabalho Remota para qualquer coisa que possa envolver segredos e/ou PII.

### <a name="concurrent-usage"></a>Uso simultâneo
Se um usuário estiver conectado ao console serial e outro usuário solicitar acesso com êxito à mesma máquina virtual, o primeiro usuário será desconectado e o segundo usuário se conectará à mesma sessão.

> [!CAUTION]
> Isso significa que um usuário que está desconectado não será desconectado. A capacidade de impor um logout após a desconexão (usando SIGHUP ou mecanismo semelhante) ainda está no roteiro. Para o Windows, há um tempo limite automático habilitado no SAC; para o Linux, você pode definir a configuração de tempo limite do terminal.

## <a name="accessibility"></a>Acessibilidade
Acessibilidade é um foco importante para o console serial do Azure. Para esse fim, garantimos que o console serial esteja acessível para o Visual e com deficiência auditiva, bem como para pessoas que talvez não possam usar um mouse.

### <a name="keyboard-navigation"></a>Navegação por teclado
Use a tecla **Tab** no teclado para navegar na interface do console serial do portal do Azure. Seu local será realçado na tela. Para deixar o foco da janela do console serial, pressione **Ctrl** +**F6** no teclado.

### <a name="use-the-serial-console-with-a-screen-reader"></a>Usar o console serial com um leitor de tela
O console serial tem suporte de leitor de tela interno. Navegar com um leitor de tela ativado permitirá que o texto alt do botão atualmente selecionado seja lido em voz alta pelo leitor de tela.

## <a name="common-scenarios-for-accessing-the-serial-console"></a>Cenários comuns para acessar o console serial

Cenário          | Ações no console serial
:------------------|:-----------------------------------------
Regras de firewall incorretas | Acesse o console serial e corrija as regras de firewall do Windows.
Corrupção/verificação do sistema de arquivos | Acesse o console serial e recupere o sistema de arquivos.
Problemas de configuração de RDP | Acesse o console serial e altere as configurações. Para obter mais informações, consulte a [documentação do RDP](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-allow-access).
Sistema de bloqueio de rede | Acesse o console serial do portal do Azure para gerenciar o sistema. Alguns comandos de rede são listados em [comandos do Windows: cmd e PowerShell](serial-console-cmd-ps-commands.md).
Interagindo com o carregador de carregado | Acesse o BCD por meio do console serial. Para obter informações, consulte [habilitar o menu de inicialização do Windows no console serial](#enable-the-windows-boot-menu-in-the-serial-console).

## <a name="known-issues"></a>Problemas conhecidos
Estamos cientes de alguns problemas com o console serial e o sistema operacional da VM. Aqui está uma lista desses problemas e etapas para mitigação de VMs do Windows. Esses problemas e atenuações se aplicam tanto a VMs quanto a instâncias do conjunto de dimensionamento de máquinas virtuais. Se eles não corresponderem ao erro que você está vendo, consulte os erros comuns do serviço de console serial em [erros comuns do console serial](./serial-console-errors.md).

Problema                             |   Mitigação
:---------------------------------|:--------------------------------------------|
Pressionar **Enter** após a faixa de conexão não faz com que uma solicitação de entrada seja exibida. | Para obter mais informações, consulte [pressionar Enter não faz nada](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). Esse erro pode ocorrer se você estiver executando uma VM personalizada, um dispositivo protegido ou uma configuração de inicialização que faz com que o Windows não se conecte corretamente à porta serial. Esse erro também ocorrerá se você estiver executando uma VM do Windows 10, porque somente as VMs do Windows Server estão configuradas para ter o EMS habilitado.
Somente as informações de integridade são mostradas ao se conectar a uma VM do Windows| Esse erro ocorrerá se o console de administração especial não tiver sido habilitado para sua imagem do Windows. Consulte [habilitar o console serial em imagens personalizadas ou mais antigas](#enable-the-serial-console-in-custom-or-older-images) para obter instruções sobre como habilitar o SAC manualmente em sua VM do Windows. Para obter mais informações, consulte [sinais de integridade do Windows](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Windows_Health_Info.md).
O SAC não ocupa toda a área do console serial no navegador | Esse é um problema conhecido com o Windows e o emulador de terminal. Estamos acompanhando esse problema com as duas equipes, mas, por enquanto, não há nenhuma mitigação.
Não é possível digitar no prompt do SAC se a depuração do kernel estiver habilitada. | RDP para VM e executar `bcdedit /debug {current} off` de um prompt de comando elevado. Se não for possível o RDP, você poderá anexar o disco do sistema operacional a outra VM do Azure e modificá-lo enquanto estiver anexado como um disco de dados executando `bcdedit /store <drive letter of data disk>:\boot\bcd /debug <identifier> off` e, em seguida, alternando o disco de volta.
Colar no PowerShell no SAC resultará em um terceiro caractere se o conteúdo original tiver um caractere de repetição. | Para uma solução alternativa, execute `Remove-Module PSReadLine` para descarregar o módulo PSReadLine da sessão atual. Essa ação não excluirá nem desinstalará o módulo.
Algumas entradas de teclado produzem uma saída estranha do SAC (por exemplo, **[A**, **[3 ~** ). | As sequências de escape [vt100](https://aka.ms/vtsequences) não têm suporte no prompt do SAC.
Colar cadeias de caracteres longas não funciona. | O console serial limita o comprimento de cadeias de caracteres colados no terminal a 2048 para evitar o sobrecarregamento da largura de banda da porta serial.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

**P. como posso enviar comentários?**

R. Forneça comentários criando um problema do GitHub em https://aka.ms/serialconsolefeedback. Como alternativa (menos preferencial), você pode enviar comentários por meio de azserialhelp@microsoft.com ou na categoria de máquina virtual de https://feedback.azure.com.

**P. o console serial dá suporte a copiar/colar?**

R. Sim. Use **ctrl** +**Shift** +**C** e **Ctrl** +**Shift** +**V** para copiar e colar no terminal.

**P. quem pode habilitar ou desabilitar o console serial para minha assinatura?**

R. Para habilitar ou desabilitar o console serial em um nível de assinatura, você deve ter permissões de gravação para a assinatura. Funções que têm permissão de gravação incluem funções de administrador ou proprietário. As funções personalizadas também podem ter permissões de gravação.

**P. quem pode acessar o console serial da minha VM?**

R. Você deve ter a função colaborador da máquina virtual ou superior para que uma VM acesse o console serial da VM.

**P. meu console serial não está exibindo nada, o que eu faço?**

R. A imagem provavelmente está configurada incorretamente para acesso ao console serial. Para obter informações sobre como configurar sua imagem para habilitar o console serial, consulte [habilitar o console serial em imagens personalizadas ou mais antigas](#enable-the-serial-console-in-custom-or-older-images).

**P. o console serial está disponível para conjuntos de dimensionamento de máquinas virtuais?**

R. Sim, é! Consulte o [console serial para conjuntos de dimensionamento de máquinas virtuais](./serial-console-overview.md#serial-console-for-virtual-machine-scale-sets)

## <a name="next-steps"></a>Passos seguintes
* Para obter um guia detalhado para comandos CMD e PowerShell que você pode usar no Windows SAC, consulte [comandos do Windows: cmd e PowerShell](serial-console-cmd-ps-commands.md).
* O console serial também está disponível para VMs [Linux](serial-console-linux.md) .
* Saiba mais sobre o [diagnóstico de inicialização](boot-diagnostics.md).
