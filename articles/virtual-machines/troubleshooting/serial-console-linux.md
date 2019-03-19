---
title: Consola de série de máquina virtual do Azure para Linux | Documentos da Microsoft
description: Consola de série de bidirecional para máquinas virtuais do Azure.
services: virtual-machines-linux
documentationcenter: ''
author: harijay
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: harijay
ms.openlocfilehash: 4fd96aedc658833493d6fddb704104a70c01df44
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58010987"
---
# <a name="virtual-machine-serial-console-for-linux"></a>Consola de série de máquina virtual para Linux

A consola de série de máquina virtual (VM) no portal do Azure fornece acesso a um console baseado em texto para máquinas virtuais do Linux. Esta conexão serial liga-se para a porta serial de COM1 da máquina virtual, fornecendo acesso ao mesmo, independentemente do Estado de rede ou sistema operativo da máquina virtual. Acesso à consola de série para uma máquina virtual pode ser feito apenas através do portal do Azure. É permitido apenas para os utilizadores que têm uma função de acesso de contribuinte de Máquina Virtual ou superior para a máquina virtual.

Para obter documentação de consola de série para VMs do Windows, consulte [consola de série de Máquina Virtual para o Windows](../windows/serial-console.md).

> [!NOTE]
> A consola de série para máquinas virtuais está disponível em geral em regiões globais do Azure. Ele ainda não está disponível no Azure government ou a clouds do Azure China.


## <a name="prerequisites"></a>Pré-requisitos

- A VM em que está a aceder à consola de série tem de utilizar o modelo de implementação de gestão de recursos. Implementações clássicas não são suportadas.

- A VM em que está a aceder à consola de série tem de ter [diagnósticos de arranque](boot-diagnostics.md) ativada.

    ![Definições de diagnóstico de arranque](./media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

- Tem de ter uma conta que utiliza uma consola de série a [função de contribuinte de Máquina Virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) para a VM e o [diagnósticos de arranque](boot-diagnostics.md) conta de armazenamento:

    - A VM em que está a aceder à consola de série tem de ter uma conta baseada em palavra-passe. Pode criar uma com o [Repor palavra-passe](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) função da extensão de acesso VM. Selecione **Repor palavra-passe** partir do **suporte + resolução de problemas** secção.

    - Para configurações específicas de distribuições do Linux, consulte [consola de série disponibilidade de distribuição de Linux](#serial-console-linux-distribution-availability).



## <a name="get-started-with-the-serial-console"></a>Começar a utilizar a consola de série
A consola de série para máquinas virtuais é acessível apenas através do portal do Azure:

  1. Abra o [Portal do Azure](https://portal.azure.com).

  1. No menu da esquerda, selecione **máquinas virtuais**.

  1. Selecione uma VM na lista. É aberta a página de descrição geral para a VM.

  1. Desloque para baixo para o **suporte + resolução de problemas** secção e selecione **consola de série**. Um novo painel com a consola de série abre e começa a ligação.

     ![Janela de consola de série do Linux](./media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)


> [!NOTE]
> A consola de série requer um utilizador local com uma palavra-passe configurado. VMs configuradas apenas com uma chave pública SSH não será possível iniciar sessão na consola de série. Para criar um utilizador local com uma palavra-passe, utilize o [extensão VMAccess](https://docs.microsoft.com/azure/virtual-machines/linux/using-vmaccess-extension), que está disponível no portal do selecionando **Repor palavra-passe** no portal do Azure e criar um utilizador local com uma palavra-passe.
> Também pode repor a palavra-passe de administrador na sua conta por [usando o GRUB para inicializar em modo de utilizador único](./serial-console-grub-single-user-mode.md).

## <a name="serial-console-linux-distribution-availability"></a>Consola de série disponibilidade de distribuição do Linux
Para a consola de série funcionar corretamente, o sistema operativo convidado tem de ser configurado para ler e gravar mensagens de consola para a porta serial. A maioria dos [distribuições apoiadas pelo Azure Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) tenham a consola de série configurada por predefinição. Selecionando **consola de série** no **suporte + resolução de problemas** seção do portal do Azure fornece acesso à consola de série.

Distribuição      | Acesso à consola de série
:-----------|:---------------------
Red Hat Enterprise Linux    | Acesso de consola de série ativado por predefinição.
CentOS      | Acesso de consola de série ativado por predefinição.
Ubuntu      | Acesso de consola de série ativado por predefinição.
CoreOS      | Acesso de consola de série ativado por predefinição.
SUSE        | Mais recente SLES as imagens disponíveis no Azure têm acesso de consola de série ativado por predefinição. Se estiver a utilizar versões mais antigas (10 ou anteriores) do SLES no Azure, consulte a [artigo BDC](https://www.novell.com/support/kb/doc.php?id=3456486) para ativar a consola de série.
Oracle Linux        | Acesso de consola de série ativado por predefinição.
Imagens do Linux personalizadas     | Para ativar a consola de série para a sua imagem de VM do Linux personalizada, ative o acesso à consola no ficheiro *nomedeanfitrião inittab* para executar um terminal em `ttyS0`. Por exemplo: `S0:12345:respawn:/sbin/agetty -L 115200 console vt102`. Para obter mais informações sobre como criar adequadamente imagens personalizadas, consulte [criar e carregar um VHD do Linux no Azure](https://aka.ms/createuploadvhd). Se estiver criando um kernel personalizado, considere ativar estes sinalizadores de kernel: `CONFIG_SERIAL_8250=y` e `CONFIG_MAGIC_SYSRQ_SERIAL=y`. O ficheiro de configuração geralmente está localizado na */boot/* caminho.

> [!NOTE]
> Se não vir nenhuma ação na consola de série, certifique-se de que o diagnóstico de arranque está ativado na sua VM.

## <a name="common-scenarios-for-accessing-the-serial-console"></a>Cenários comuns para aceder à consola de série

Cenário          | Ações na consola de série
:------------------|:-----------------------------------------
Dividido *FSTAB* ficheiro | Prima a **Enter** tecla para continuar e utilize um editor de texto para corrigir o *FSTAB* ficheiro. Poderá ter de estar no modo de utilizador único para fazer isso. Para obter mais informações, consulte [como corrigir problemas de fstab](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors) e [consola de série de utilização para aceder a GRUB e modo de utilizador único](serial-console-grub-single-user-mode.md).
Regras de firewall incorreta | Aceder à consola de série e corrigir iptables.
Danos/verificação de sistema de ficheiros | Aceder à consola de série e recuperar o sistema de ficheiros.
Problemas de configuração de SSH/RDP | Aceder à consola de série e alterar as definições.
Bloqueio de rede para baixo do sistema| Aceda à consola de série do portal do Azure para gerir o sistema.
Interagir com o carregador de inicialização | Reinicie a sua VM a partir do painel da consola de série para acessar o GRUB na sua VM do Linux. Para obter mais informações, consulte [consola de série de utilização para aceder a GRUB e modo de utilizador único](serial-console-grub-single-user-mode.md).

## <a name="disable-the-serial-console"></a>Desativar a consola de série
Por predefinição, todas as subscrições têm acesso de consola de série ativado para todas as VMs. Pode desativar a consola de série no nível de assinatura ou o nível VM.

> [!NOTE]
> Para ativar ou desativar a consola de série para uma subscrição, tem de ter permissões de escrita para a subscrição. Estas permissões incluem funções de administrador ou proprietário. Funções personalizadas também podem ter permissões de escrita.

### <a name="subscription-level-disable"></a>Desativar o nível de assinatura
A consola de série pode ser desabilitada para uma subscrição completa através da [chamada à API de REST de consola desativar](/rest/api/serialconsole/console/disableconsole). Pode utilizar o **experimentar** função disponível nesta página de documentação de API para desativar e ativar a consola de série para uma subscrição. Introduza o seu ID de subscrição **subscriptionId**, introduza **predefinido** para **predefinição**e, em seguida, selecione **executar**. Os comandos da CLI do Azure ainda não estão disponíveis.

![Experimente-o API de REST](./media/virtual-machines-serial-console/virtual-machine-serial-console-rest-api-try-it.png)

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
> Isso significa que um utilizador que está desligado não terminar a sessão. A capacidade de impor um fim de sessão após a desconexão (ao utilizar SIGHUP ou mecanismo similar) ainda está no plano. Para Windows é um tempo limite automático ativado em especial administrativas consola (SAC); No entanto, para Linux pode configurar a definição de tempo limite de terminal. Para tal, adicione `export TMOUT=600` no seu *. bash_profile* ou *.profile* ficheiro para o utilizador que utiliza para iniciar sessão na consola. Esta definição será o tempo limite da sessão após 10 minutos.

## <a name="accessibility"></a>Acessibilidade
A acessibilidade é um foco principal para a consola de série do Azure. Para esse fim, garantimos que a consola de série é totalmente acessível.

### <a name="keyboard-navigation"></a>Navegação do teclado
Utilize o **separador** chave no teclado para navegar na interface de consola de série no portal do Azure. Sua localização será realçada na tela. Para deixar o foco da janela de consola de série, prima **Ctrl**+**F6** no teclado.

### <a name="use-the-serial-console-with-a-screen-reader"></a>Utilizar a consola de série com um leitor de ecrã
A consola de série tem suporte de leitor de ecrã incorporado. Navegação com um leitor de ecrã ativado, permitirá que o texto alternativo para o botão selecionado atualmente a ser lido em voz alta pelo leitor de ecrã.

## <a name="errors"></a>Erros
Como a maioria dos erros são transitórios, repetir a ligação pode, muitas vezes, corrigi-los. A tabela seguinte mostra uma lista de erros e mitigações.

Erro                            |   Mitigação
:---------------------------------|:--------------------------------------------|
Não é possível obter as definições de diagnóstico de arranque de  *&lt;VMNAME&gt;*. Para utilizar a consola de série, certifique-se de que o diagnóstico de arranque está ativado para esta VM. | Certifique-se de que a VM tem [diagnósticos de arranque](boot-diagnostics.md) ativada.
A VM está num estado parado desalocado. Iniciar a VM e repita a ligação da consola de série. | A VM deve estar num estado iniciado para aceder à consola de série.
Não tem as permissões necessárias para usar essa VM com a consola de série. Certifique-se de que tem, pelo menos, permissões de função de contribuinte de Máquina Virtual.| O acesso de consola de série requer determinadas permissões. Para obter mais informações, consulte [pré-requisitos](#prerequisites).
Não é possível determinar o grupo de recursos para a conta de armazenamento do diagnóstico de arranque  *&lt;STORAGEACCOUNTNAME&gt;*. Certifique-se de que o diagnóstico de arranque está ativado para esta VM e que tem acesso a esta conta de armazenamento. | O acesso de consola de série requer determinadas permissões. Para obter mais informações, consulte [pré-requisitos](#prerequisites).
Web socket foi fechado ou não foi possível abrir. | Poderá ter de lista aprovada `*.console.azure.com`. Um mais detalhado for mas a abordagem mais é à lista de permissões a [intervalos de IP de Datacenter do Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653), que alterar bastante regularmente.
Uma resposta de "dizer proibido" foi encontrada ao aceder à conta de armazenamento do diagnóstico de arranque desta VM. | Certifique-se de que o diagnóstico de arranque não tem uma firewall de conta. Uma conta de armazenamento do diagnóstico de arranque acessível é necessária para a consola de série função.

## <a name="known-issues"></a>Problemas conhecidos
Estamos cientes de alguns problemas com a consola de série. Aqui está uma lista desses problemas e os passos para a mitigação.

Problema                           |   Mitigação
:---------------------------------|:--------------------------------------------|
Premir **Enter** depois da faixa de ligação não causa um prompt de início de sessão a apresentar. | Para obter mais informações, consulte [Hitting introduza não faz nada](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). Este problema pode ocorrer se estiver a executar uma VM personalizada, a aplicação protegida ou a configuração GRUB que faz com que o Linux não sejam corretamente ligar para a porta serial.
Texto da consola de série ocupa apenas uma parte do tamanho da tela (muitas vezes, depois de utilizar um editor de texto). | Seriais consolas não suportam a negociação sobre o tamanho da janela ([RFC 1073](https://www.ietf.org/rfc/rfc1073.txt)), o que significa que não vai haver nenhum sinal SIGWINCH enviado para atualizar o tamanho da tela e a VM será não têm conhecimento de tamanho de seu terminal. Instalar xterm ou um utilitário semelhante para oferecer a com o `resize` comando e, em seguida, execute `resize`.
Colar longas seqüências de caracteres não funciona. | A consola de série limita o comprimento de cadeias de caracteres colado no terminal para 2048 carateres para evitar sobrecarregar a largura de banda da porta serial.
Consola de série não funciona com um firewall de conta de armazenamento. | Consola de série por design não consegue trabalhar com firewalls de conta de armazenamento ativadas nesta conta de armazenamento do diagnóstico de arranque.


## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

**P. Como posso enviar comentários?**

R. Fornecer comentários através da criação de um problema do GitHub https://aka.ms/serialconsolefeedback. Como alternativa (menos preferencial), pode enviar comentários por meio azserialhelp@microsoft.com ou na categoria de máquina virtual de https://feedback.azure.com.

**P. A consola de série suporta copiar/colar?**

R. Sim. Uso **Ctrl**+**Shift**+**C** e **Ctrl**+**Shift** + **V** de copiar e colar no terminal.

**P. Posso utilizar a consola de série, em vez de uma ligação SSH?**

R. Embora esta utilização possa parecer tecnicamente possível, a consola de série destina-se para ser usado principalmente como uma ferramenta de resolução de problemas em situações em que a conectividade através de SSH não é possível. Recomendamos contra a utilização da consola de série como um substituto de SSH pelos seguintes motivos:

- A consola de série não tem muito mais largura de banda como SSH. Como é uma ligação só de texto, interações de GUI intensiva mais são difíceis.
- Acesso à consola de série é atualmente possível usando apenas um nome de utilizador e palavra-passe. Uma vez que as chaves SSH são muito mais seguras do que as combinações de nome de utilizador/palavra-passe, de uma perspectiva de segurança de início de sessão, recomendamos SSH através da consola de série.

**P. Quem pode ativar ou desativar a consola de série para a minha subscrição?**

R. Para ativar ou desativar a consola de série a um nível de toda a subscrição, tem de ter permissões de escrita para a subscrição. As funções que tem permissão de escrita incluem funções de administrador ou proprietário. Funções personalizadas também podem ter permissões de escrita.

**P. Quem pode aceder a consola de série para a minha VM?**

R. Tem de ter a função de contribuinte de Máquina Virtual ou superior para uma VM para aceder à consola de série da VM.

**P. Meu consola de série não é apresentada qualquer coisa, o que fazer?**

R. A imagem é provavelmente mal configurada para acesso à consola de série. Para obter informações sobre como configurar a sua imagem para ativar a consola de série, consulte [consola de série disponibilidade de distribuição de Linux](#serial-console-linux-distribution-availability).

**P. A consola de série está disponível para os conjuntos de dimensionamento de máquinas virtuais?**

R. Neste momento, o acesso à consola de série para instâncias do conjunto de dimensionamento de máquina virtual não é suportado.

**P. Se configurei minha VM ao utilizar apenas autenticação por chave SSH, posso continuar a utilizar a consola de série para ligar à minha VM?**

R. Sim. Como a consola de série não requer chaves SSH, só precisa de configurar uma combinação de nome de utilizador/palavra-passe. Pode fazê-lo selecionando **Repor palavra-passe** no portal do Azure e utilizar essas credenciais para iniciar sessão na consola de série.

## <a name="next-steps"></a>Passos Seguintes
* Utilizar a consola de série para [acessar GRUB e modo de utilizador único](serial-console-grub-single-user-mode.md).
* Utilizar a consola de série para [chamadas NMI e SysRq](serial-console-nmi-sysrq.md).
* Saiba como utilizar a consola de série para [ativar o GRUB nas várias distribuições](https://blogs.msdn.microsoft.com/linuxonazure/2018/10/23/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/).
* Também está disponível para a consola de série [Windows VMs](../windows/serial-console.md).
* Saiba mais sobre [diagnósticos de arranque](boot-diagnostics.md).

