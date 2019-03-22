---
title: Consola de série de máquina virtual do Azure para Windows | Documentos da Microsoft
description: Consola de série de bidirecional para máquinas de virtuais do Windows Azure.
services: virtual-machines-windows
documentationcenter: ''
author: harijay
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: harijay
ms.openlocfilehash: 57b20f9d694ae0581988762735c35cb65012fd8e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57992363"
---
# <a name="virtual-machine-serial-console-for-windows"></a>Consola de série de máquina virtual para Windows

A consola de série de máquina virtual (VM) no portal do Azure fornece acesso a um console baseado em texto para as máquinas virtuais do Windows. Esta conexão serial liga-se para a porta serial de COM1 da máquina virtual, fornecendo acesso ao mesmo, independentemente do Estado de rede ou sistema operativo da máquina virtual. Acesso à consola de série para uma máquina virtual pode ser feito apenas através do portal do Azure. É permitido apenas para os utilizadores que têm uma função de acesso de contribuinte de Máquina Virtual ou superior para a máquina virtual.

Para obter documentação de consola de série para VMs do Linux, consulte [consola de série de Máquina Virtual para Linux](serial-console-linux.md).

> [!NOTE]
> A consola de série para máquinas virtuais está disponível em geral em regiões globais do Azure. Ele ainda não está disponível no Azure government ou a clouds do Azure China.


## <a name="prerequisites"></a>Pré-requisitos

* A VM em que está a aceder à consola de série tem de utilizar o modelo de implementação de gestão de recursos. Implementações clássicas não são suportadas.

* A VM em que está a aceder à consola de série tem de ter [diagnósticos de arranque](boot-diagnostics.md) ativada.

    ![Definições de diagnóstico de arranque](../media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

* Tem de ter uma conta com uma consola de série a [função de contribuinte de Máquina Virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) para a VM e o [diagnósticos de arranque](boot-diagnostics.md) conta de armazenamento.

* A VM em que está a aceder à consola de série tem de ter uma conta baseada em palavra-passe. Pode criar uma com o [Repor palavra-passe](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) função da extensão de acesso VM. Selecione **Repor palavra-passe** partir do **suporte + resolução de problemas** secção.


## <a name="get-started-with-the-serial-console"></a>Começar a utilizar a consola de série
A consola de série para máquinas virtuais é acessível apenas através do portal do Azure:

  1. Abra o [Portal do Azure](https://portal.azure.com).
  1. No menu da esquerda, selecione **máquinas virtuais**.
  1. Selecione uma VM na lista. Abre a página de descrição geral para a VM.
  1. Desloque para baixo para o **suporte + resolução de problemas** secção e selecione **consola de série**. Um novo painel com a consola de série abre e começa a ligação.

## <a name="enable-serial-console-functionality"></a>Ativar a funcionalidade de consola de série

> [!NOTE]
> Se não vir nenhuma ação na consola de série, certifique-se de que o diagnóstico de arranque está ativado na sua VM.

### <a name="enable-the-serial-console-in-custom-or-older-images"></a>Ativar a consola de série em imagens personalizadas ou mais antigas
As imagens mais recente do Windows Server no Azure têm [consola administrativa especial](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) (SAC) ativada por predefinição. SAC é suportada em versões de servidor do Windows, mas não está disponível em versões de cliente (por exemplo, Windows 10, Windows 8 ou Windows 7).

Para imagens do Windows Server mais antigas (criadas antes de Fevereiro de 2018), pode ativar automaticamente a consola de série por meio do recurso de comando de execução do portal do Azure. No portal do Azure, selecione **execute o comando**, em seguida, selecione o comando com o nome **EnableEM** da lista.

![Executar a lista de comandos](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-runcommand.png)

Em alternativa, para ativar manualmente a consola de série para máquinas de virtuais do Windows criada antes de Fevereiro de 2018, siga estes passos:

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

Se [SAC](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) não está ativada, a consola de série não será exibido na linha de comandos da SAC. Em alguns casos, informações de estado de funcionamento da VM são apresentadas e, em outros casos está em branco. Se estiver a utilizar uma imagem do Windows Server criada antes de Fevereiro de 2018, SAC provavelmente não estar ativada.

### <a name="enable-the-windows-boot-menu-in-the-serial-console"></a>Habilitar o menu de arranque do Windows na consola de série

Se precisar de ativar Windows prompts de carregador de arranque apresentar na consola de série, pode adicionar as seguintes opções adicionais aos seus dados de configuração de arranque. Para obter mais informações, consulte [bcdedit](https://docs.microsoft.com/windows-hardware/drivers/devtest/bcdedit--set).

1. Ligar à sua máquina virtual do Windows com o ambiente de trabalho remoto.

1. A partir de uma linha de comandos administrativa, execute os seguintes comandos:
   - `bcdedit /set {bootmgr} displaybootmenu yes`
   - `bcdedit /set {bootmgr} timeout 10`
   - `bcdedit /set {bootmgr} bootems yes`

1. Reinicie o sistema para o menu de arranque seja ativado

> [!NOTE]
> O tempo limite que definiu para o menu de Gestor de arranque apresentar irá afetar o tempo de arranque do sistema operacional. Se acha que o valor de tempo limite de 10 segundos é demasiado curto ou demasiado longo, defini-lo para um valor diferente.

## <a name="use-serial-console"></a>Utilizar a consola de série

### <a name="use-cmd-or-powershell-in-serial-console"></a>Utilize o CMD ou PowerShell na consola de série

1. Ligar à consola de série. Se se ligar com êxito, o pedido é **SAC >**:

    ![Ligar ao SAC](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect-sac.png)

1.  Introduza `cmd` para criar um canal que tem uma instância CMD.

1.  Introduza `ch -si 1` para mudar para o canal que está executando a instância CMD.

1.  Prima **Enter**e, em seguida, introduza credenciais de início de sessão com permissões administrativas.

1.  Após introduzir as credenciais válidas, abre-se a instância CMD.

1.  Para iniciar uma instância do PowerShell, introduza `PowerShell` na instância CMD e, em seguida, prima **Enter**.

    ![Abra a instância do PowerShell](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-powershell.png)

### <a name="use-the-serial-console-for-nmi-calls"></a>Utilizar a consola de série para chamadas de NMI
Uma interrupção não maskable (NMI) foi concebida para criar um sinal de que o software numa máquina virtual não ignorar. Historicamente, NMIs foram utilizadas para monitorizar a existência de problemas de hardware em sistemas que exigem tempos de resposta específica. Hoje, os programadores e administradores de sistema utilizam frequentemente NMI como um mecanismo para depurar ou resolver problemas de sistemas de suspenso.

A consola de série pode ser utilizada para enviar um NMI para uma máquina virtual do Azure com o ícone de teclado na barra de comandos. Depois do NMI é enviado, a configuração de máquina virtual irá controlar como o sistema responde. Windows podem ser configurado para falhas e crie um arquivo de despejo de memória, ao receber um NMI.

![Enviar NMI](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-nmi.png) <br>

Para obter informações sobre como configurar o Windows para criar um arquivo de despejo de pane ao receber um NMI, consulte [como gerar um arquivo de despejo de pane, utilizando um NMI](https://support.microsoft.com/help/927069/how-to-generate-a-complete-crash-dump-file-or-a-kernel-crash-dump-file).

### <a name="use-function-keys-in-serial-console"></a>Utilizar teclas de função na consola de série
Teclas de função estão ativadas para utilização para a consola de série em VMs do Windows. A tecla F8 na lista pendente de consola de série fornece a conveniência da introdução facilmente o menu de definições avançadas de inicialização, mas a consola de série é compatível com todas as outras teclas de função. Poderá ter de premir **Fn** + **F1** (ou F2, F3, etc) no seu teclado dependendo do computador estiver a utilizar da consola de série.

### <a name="use-wsl-in-serial-console"></a>Utilizar WSL na consola de série
O subsistema Windows para Linux (WSL) foi ativado para o Windows Server 2019 ou posterior, pelo que também é possível ativar WSL para uso dentro da consola de série, se estiver a executar o Windows Server 2019 ou posterior. Isso pode ser benéfico para os utilizadores que também tem uma familiaridade com os comandos de Linux. Para obter instruções ativar WSL para o Windows Server, consulte a [guia de instalação](https://docs.microsoft.com/windows/wsl/install-on-server).

### <a name="restart-your-windows-vm-within-serial-console"></a>Reinicie a VM do Windows na consola de série
Pode reiniciar a VM dentro da consola de série ao navegar para o botão de energia e clicar em "VM reiniciar". Isto irá iniciar um reinício VM, e verá uma notificação no portal do Azure sobre o reinício.

Isto é útil em situações nas quais poderá desejar para aceder ao menu de arranque da sua VM sem deixar a experiência de consola de série.

![Reinício de consola de série do Windows](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-windows.gif)

## <a name="disable-serial-console"></a>Desativar a consola de série
Por predefinição, todas as subscrições têm acesso de consola de série ativado para todas as VMs. Pode desativar a consola de série ao nível da subscrição ou o nível VM.

> [!NOTE]
> Para ativar ou desativar a consola de série para uma subscrição, tem de ter permissões de escrita para a subscrição. Estas permissões incluem, mas não sejam limitam às funções de administrador ou proprietário. Funções personalizadas também podem ter permissões de escrita.

### <a name="subscription-level-disable"></a>Desativar o nível de assinatura
A consola de série pode ser desabilitada para uma subscrição completa através da [chamada à API de REST de consola desativar](/rest/api/serialconsole/console/disableconsole). Pode utilizar o **experimentar** função disponível nesta página de documentação de API para desativar e ativar a consola de série para uma subscrição. Introduza o seu ID de subscrição para **subscriptionId**, introduza "predefinição" para **predefinição**e, em seguida, selecione **executar**. Os comandos da CLI do Azure ainda não estão disponíveis.

![Experimente-o API de REST](../media/virtual-machines-serial-console/virtual-machine-serial-console-rest-api-try-it.png)

Em alternativa, pode utilizar o seguinte conjunto de comandos de bash no Cloud Shell para desativar, ativar e ver o estado desativado da consola de série para uma subscrição:

* Para obter o estado desativado da consola de série para uma subscrição:
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"'))

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s | jq .properties
    ```
* Para desativar a consola de série para uma subscrição:
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"'))

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl -X POST "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default/disableConsole?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s -H "Content-Length: 0"
    ```
* Para ativar a consola de série para uma subscrição:
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"'))

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl -X POST "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default/enableConsole?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s -H "Content-Length: 0"
    ```

### <a name="vm-level-disable"></a>Desativar ao nível da VM
A consola de série pode ser desativada para uma VM específica, desativando a definição de diagnóstico de arranque essa VM. Desative o diagnóstico de arranque do portal do Azure para desativar a consola de série para a VM.

## <a name="serial-console-security"></a>Segurança da consola de série

### <a name="access-security"></a>Segurança de acesso
Acesso à consola de série está limitado a utilizadores que têm uma função de acesso de [contribuinte de Máquina Virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) ou superior para a máquina virtual. Se o seu inquilino do Azure Active Directory requer autenticação multifator (MFA), então acesso à consola de série também terá de MFA porque o acesso da consola de série é através da [portal do Azure](https://portal.azure.com).

### <a name="channel-security"></a>Segurança de canal
Todos os dados que são enviados e volta são encriptados na conexão.

### <a name="audit-logs"></a>Registos de auditoria
Todo o acesso à consola de série é iniciado a [diagnósticos de arranque](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) registos da máquina virtual. Acesso a estes registos são propriedade e controlado pelo administrador de máquina virtual do Azure.

> [!CAUTION]
> Sem palavras-passe de acesso para a consola são registadas. No entanto, se os comandos são executados dentro da consola contém ou palavras-passe, segredos, os nomes de utilizador ou qualquer outra forma de informações de identificação pessoal (PII) de saída, aqueles serão escritos nos registos de diagnóstico de arranque VM. Eles serão escritos, juntamente com todos os outro texto visível, como parte da implementação de volta a rolagem da consola de série função. Estes registos são circulares e apenas indivíduos com permissões de leitura à conta de armazenamento de diagnóstico tem acesso aos mesmos. No entanto, recomendamos que siga a prática recomendada de usar o ambiente de trabalho remoto para tudo o que pode envolver segredos e/ou PII.

### <a name="concurrent-usage"></a>Utilização em simultâneo
Se um usuário estiver conectado à consola de série e outro utilizador com êxito pedidos de acesso a essa mesma máquina virtual, o primeiro utilizador será desligado e o segundo utilizador ligado à mesma sessão.

> [!CAUTION]
> Isso significa que um utilizador que está desligado não terminar a sessão. A capacidade de impor um fim de sessão após a desconexão (ao utilizar SIGHUP ou mecanismo similar) ainda está no plano. Para Windows, há um tempo limite automático ativado no SAC; para o Linux, pode configurar a definição de tempo limite de terminal.

## <a name="accessibility"></a>Acessibilidade
A acessibilidade é um foco principal para a consola de série do Azure. Para esse fim, garantimos que a consola de série está acessível para o elemento visual e ouvir reduzida, bem como as pessoas que podem não ser capazes de usar um mouse.

### <a name="keyboard-navigation"></a>Navegação do teclado
Utilize o **separador** chave no teclado para navegar na interface de consola de série no portal do Azure. Sua localização será realçada na tela. Para deixar o foco da janela de consola de série, prima **Ctrl**+**F6** no teclado.

### <a name="use-the-serial-console-with-a-screen-reader"></a>Utilizar a consola de série com um leitor de ecrã
A consola de série tem suporte de leitor de ecrã incorporado. Navegação com um leitor de ecrã ativado, permitirá que o texto alternativo para o botão selecionado atualmente a ser lido em voz alta pelo leitor de ecrã.

## <a name="common-scenarios-for-accessing-the-serial-console"></a>Cenários comuns para aceder à consola de série

Cenário          | Ações na consola de série
:------------------|:-----------------------------------------
Regras de firewall incorreta | Aceda a seriais regras de firewall de Windows de consola e correção.
Danos/verificação de sistema de ficheiros | Aceder à consola de série e recuperar o sistema de ficheiros.
Problemas de configuração de RDP | Aceder à consola de série e alterar as definições. Para obter mais informações, consulte a [documentação de RDP](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-allow-access).
Bloqueio de rede para baixo do sistema | Aceda à consola de série do portal do Azure para gerir o sistema. Alguns comandos de rede estão listados na [comandos do Windows: CMD e PowerShell](serial-console-cmd-ps-commands.md).
Interagir com o carregador de inicialização | Acesso BCD através da consola de série. Para obter informações, consulte [habilitar o menu de arranque do Windows na consola de série do](#enable-the-windows-boot-menu-in-the-serial-console).


## <a name="errors"></a>Erros
Como a maioria dos erros são transitórios, repetir a ligação pode, muitas vezes, corrigi-los. A tabela seguinte mostra uma lista de erros e mitigações.

Erro                            |   Mitigação
:---------------------------------|:--------------------------------------------|
Não é possível obter as definições de diagnóstico de arranque de  *&lt;VMNAME&gt;*. Para utilizar a consola de série, certifique-se de que o diagnóstico de arranque está ativado para esta VM. | Certifique-se de que a VM tem [diagnósticos de arranque](boot-diagnostics.md) ativada.
A VM está num estado parado desalocado. Iniciar a VM e repita a ligação da consola de série. | Máquina virtual deve estar num estado iniciado para aceder à consola de série
Não tem as permissões necessárias para utilizar esta consola de série de VM. Certifique-se de que tem, pelo menos, permissões de função de contribuinte de Máquina Virtual.| O acesso de consola de série requer determinadas permissões. Para obter mais informações, consulte [pré-requisitos](#prerequisites).
Não é possível determinar o grupo de recursos para a conta de armazenamento do diagnóstico de arranque  *&lt;STORAGEACCOUNTNAME&gt;*. Certifique-se de que o diagnóstico de arranque está ativado para esta VM e que tem acesso a esta conta de armazenamento. | O acesso de consola de série requer determinadas permissões. Para obter mais informações, consulte [pré-requisitos](#prerequisites).
Uma resposta de "dizer proibido" foi encontrada ao aceder à conta de armazenamento do diagnóstico de arranque desta VM. | Certifique-se de que o diagnóstico de arranque não tem uma firewall de conta. Uma conta de armazenamento do diagnóstico de arranque acessível é necessária para a consola de série função.
Web socket foi fechado ou não foi possível abrir. | Poderá ter de lista aprovada `*.console.azure.com`. Um mais detalhado for mas a abordagem mais é à lista de permissões a [intervalos de IP de Datacenter do Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653), que alterar bastante regularmente.
Apenas as informações de estado de funcionamento são mostradas ao ligar a uma VM do Windows| Este erro ocorre se o Console de administração especial não tiver sido ativado para a sua imagem do Windows. Ver [ativar a consola de série em imagens personalizadas ou mais antigas](#enable-the-serial-console-in-custom-or-older-images) para obter instruções sobre como ativar manualmente SAC na sua VM do Windows. Para obter mais informações, consulte [sinais de estado de funcionamento do Windows](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Windows_Health_Info.md).

## <a name="known-issues"></a>Problemas conhecidos
Estamos cientes de alguns problemas com a consola de série. Aqui está uma lista desses problemas e os passos para a mitigação.

Problema                             |   Mitigação
:---------------------------------|:--------------------------------------------|
Premir **Enter** depois da faixa de ligação não causa um prompt de início de sessão a apresentar. | Para obter mais informações, consulte [Hitting introduza não faz nada](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). Este erro pode ocorrer se estiver a executar uma VM personalizada, a aplicação protegida ou a configuração de arranque que faz com que o Windows não sejam corretamente ligar para a porta serial. Este erro também irá ocorrer se estiver a executar um VM, de cliente do Windows 10 porque apenas VMs do Windows Server estão configuradas para terem EMS ativada.
Não é possível escreva na linha SAC perguntar se a depuração de kernel está ativada. | RDP para a VM e execute `bcdedit /debug {current} off` num prompt de comando elevado. Se não for possível RDP, em vez disso, pode anexar o disco do SO a outra VM do Azure e modificá-lo enquanto anexado como um disco de dados através da execução `bcdedit /store <drive letter of data disk>:\boot\bcd /debug <identifier> off`, em seguida, a troca de volta o disco.
Colar no PowerShell nos resultados de SAC num caractere de terceiro, se o conteúdo original tinha um caractere repetido. | Para obter uma solução, executar `Remove-Module PSReadLine` descarregar o módulo de PSReadLine da sessão atual. Esta ação não irá eliminar ou desinstalar o módulo.
Algumas entradas de teclado produzem estranha SAC saída (por exemplo, **[R**, **[3 ~**). | [VT100](https://aka.ms/vtsequences) seqüências de escape não são suportadas pela linha de comandos da SAC.
Colar longas seqüências de caracteres não funciona. | A consola de série limita o comprimento de cadeias de caracteres colado no terminal para 2048 carateres para evitar sobrecarregar a largura de banda da porta serial.
Consola de série não funciona com um firewall de conta de armazenamento. | Consola de série por design não consegue trabalhar com firewalls de conta de armazenamento ativadas nesta conta de armazenamento do diagnóstico de arranque.


## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

**P. Como posso enviar comentários?**

R. Fornecer comentários através da criação de um problema do GitHub https://aka.ms/serialconsolefeedback. Como alternativa (menos preferencial), pode enviar comentários por meio azserialhelp@microsoft.com ou na categoria de máquina virtual de https://feedback.azure.com.

**P. A consola de série suporta copiar/colar?**

R. Sim. Uso **Ctrl**+**Shift**+**C** e **Ctrl**+**Shift** + **V** de copiar e colar no terminal.

**P. Quem pode ativar ou desativar a consola de série para a minha subscrição?**

R. Para ativar ou desativar a consola de série a um nível de toda a subscrição, tem de ter permissões de escrita para a subscrição. As funções que tem permissão de escrita incluem funções de administrador ou proprietário. Funções personalizadas também podem ter permissões de escrita.

**P. Quem pode aceder a consola de série para a minha VM?**

R. Tem de ter a função de contribuinte de Máquina Virtual ou superior para uma VM para aceder à consola de série da VM.

**P. Meu consola de série não é apresentada qualquer coisa, o que fazer?**

R. A imagem é provavelmente mal configurada para acesso à consola de série. Para obter informações sobre como configurar a sua imagem para ativar a consola de série, consulte [ativar a consola de série em imagens personalizadas ou mais antigas](#enable-the-serial-console-in-custom-or-older-images).

**P. A consola de série está disponível para os conjuntos de dimensionamento de máquinas virtuais?**

R. Neste momento, o acesso à consola de série para instâncias do conjunto de dimensionamento de máquina virtual não é suportado.

## <a name="next-steps"></a>Passos Seguintes
* Para obter um guia detalhado sobre a comandos CMD e do PowerShell, pode utilizar o SAC do Windows, consulte [comandos do Windows: CMD e PowerShell](serial-console-cmd-ps-commands.md).
* Também está disponível para a consola de série [Linux](serial-console-linux.md) VMs.
* Saiba mais sobre [diagnósticos de arranque](boot-diagnostics.md).
