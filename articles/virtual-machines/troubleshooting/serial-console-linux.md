---
title: Consola de série do Azure para Linux | Documentos da Microsoft
description: Consola de série de bidirecional para máquinas virtuais do Azure e conjuntos de dimensionamento de Máquina Virtual.
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 5/1/2019
ms.author: alsin
ms.openlocfilehash: a561d29f462d44eb6bc440bb6110430cc5c51688
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/06/2019
ms.locfileid: "66735245"
---
# <a name="azure-serial-console-for-linux"></a>Consola de série do Azure para Linux

A consola de série no portal do Azure fornece acesso a um console baseado em texto para máquinas virtuais do Linux (VMs) e instâncias do conjunto de dimensionamento de máquinas virtuais. Esta conexão serial liga-se para a porta serial de COM1 da VM ou instância de conjunto de dimensionamento de máquina virtual, fornecendo acesso ao mesmo independentemente do Estado de rede ou sistema operativo. A consola de série só pode ser acessada através do portal do Azure e é permitido apenas para os utilizadores que têm uma função de acesso de Contribuidor ou superior para o conjunto de dimensionamento VM ou numa máquina virtual.

Consola de série funciona da mesma forma para VMs e instâncias do conjunto de dimensionamento de máquinas virtuais. Neste documento, menções todas as VMS incluirá implicitamente instâncias do conjunto de dimensionamento de máquina virtual, a menos que indicado de outra forma.

Para obter documentação de consola de série para Windows, consulte [consola de série para Windows](../windows/serial-console.md).

> [!NOTE]
> A consola de série está disponível em geral em regiões globais do Azure. Ele ainda não está disponível no Azure government ou a clouds do Azure China.


## <a name="prerequisites"></a>Pré-requisitos

- A instância de conjunto de dimensionamento VM ou numa máquina virtual tem de utilizar o modelo de implementação de gestão de recursos. Implementações clássicas não são suportadas.

- A conta que utiliza a consola de série tem de ter o [função de contribuinte de Máquina Virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) para a VM e o [diagnósticos de arranque](boot-diagnostics.md) conta de armazenamento

- A instância de conjunto de dimensionamento VM ou numa máquina virtual tem de ter um utilizador com base em palavra-passe. Pode criar uma com o [Repor palavra-passe](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) função da extensão de acesso VM. Selecione **Repor palavra-passe** partir do **suporte + resolução de problemas** secção.

- A instância de conjunto de dimensionamento VM ou numa máquina virtual tem de ter [diagnósticos de arranque](boot-diagnostics.md) ativada.

    ![Definições de diagnóstico de arranque](./media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

- Para configurações específicas de distribuições do Linux, consulte [consola de série disponibilidade de distribuição de Linux](#serial-console-linux-distribution-availability).

- A instância de conjunto de dimensionamento VM ou numa máquina virtual tem de ser configurada para saída serial em `ttys0`. Esta é a predefinição para imagens do Azure, mas convém verificar isso double em imagens personalizadas. Detalhes [abaixo](#custom-linux-images).


## <a name="get-started-with-the-serial-console"></a>Começar a utilizar a consola de série
A consola de série para VMs e o conjunto de dimensionamento de máquina virtual está acessível apenas através do portal do Azure:

### <a name="serial-console-for-virtual-machines"></a>Consola de série para máquinas virtuais
Consola de série para VMs é tão simples como clicar em **consola de série** dentro do **suporte + resolução de problemas** secção no portal do Azure.
  1. Abra o [Portal do Azure](https://portal.azure.com).

  1. Navegue para **todos os recursos** e selecione uma Máquina Virtual. É aberta a página de descrição geral para a VM.

  1. Desloque para baixo para o **suporte + resolução de problemas** secção e selecione **consola de série**. Um novo painel com a consola de série abre e começa a ligação.

     ![Janela de consola de série do Linux](./media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)

### <a name="serial-console-for-virtual-machine-scale-sets"></a>Consola de série para conjuntos de dimensionamento de máquinas virtuais
Consola de série está disponível numa base por instância para conjuntos de dimensionamento de máquina virtual. Terá de navegar para a instância individual de um conjunto de dimensionamento de máquina virtual antes de ver os **consola de série** botão. Se o conjunto de dimensionamento de máquina virtual não tiver ativado o diagnóstico de arranque, certifique-se de que atualizar o seu modelo de conjunto de dimensionamento de máquina virtual para ativar diagnósticos de arranque e, em seguida, atualizar todas as instâncias para o novo modelo para aceder à consola de série.
  1. Abra o [Portal do Azure](https://portal.azure.com).

  1. Navegue para **todos os recursos** e selecione um conjunto de dimensionamento de Máquina Virtual. A página de descrição geral para o dimensionamento de máquinas virtuais definida é aberta.

  1. Navegue para **instâncias**

  1. Selecione uma instância de conjunto de dimensionamento de máquina virtual

  1. Partir do **suporte + resolução de problemas** secção, selecione **consola de série**. Um novo painel com a consola de série abre e começa a ligação.

     ![Consola de série de conjunto de dimensionamento de máquina virtual do Linux](./media/virtual-machines-serial-console/vmss-start-console.gif)


> [!NOTE]
> A consola de série requer um utilizador local com uma palavra-passe configurado. VMs ou conjuntos de dimensionamento de máquina virtual configurados apenas com uma chave pública SSH não será possível iniciar sessão na consola de série. Para criar um utilizador local com uma palavra-passe, utilize o [extensão VMAccess](https://docs.microsoft.com/azure/virtual-machines/linux/using-vmaccess-extension), que está disponível no portal do selecionando **Repor palavra-passe** no portal do Azure e criar um utilizador local com uma palavra-passe.
> Também pode repor a palavra-passe de administrador na sua conta por [usando o GRUB para inicializar em modo de utilizador único](./serial-console-grub-single-user-mode.md).

## <a name="serial-console-linux-distribution-availability"></a>Disponibilidade de distribuição de Linux consola Serial
Para a consola de série funcionar corretamente, o sistema operativo convidado tem de ser configurado para ler e gravar mensagens de consola para a porta serial. A maioria dos [distribuições apoiadas pelo Azure Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) tenham a consola de série configurada por predefinição. Selecionando **consola de série** no **suporte + resolução de problemas** seção do portal do Azure fornece acesso à consola de série.

> [!NOTE]
> Se não vir nenhuma ação na consola de série, certifique-se de que o diagnóstico de arranque está ativado na sua VM. Acessando **Enter** será, muitas vezes, corrigir problemas onde nada está a aparecer na consola de série.

Distribuição      | Acesso à consola de série
:-----------|:---------------------
Red Hat Enterprise Linux    | Acesso de consola de série ativado por predefinição.
CentOS      | Acesso de consola de série ativado por predefinição.
Ubuntu      | Acesso de consola de série ativado por predefinição.
CoreOS      | Acesso de consola de série ativado por predefinição.
SUSE        | Mais recente SLES as imagens disponíveis no Azure têm acesso de consola de série ativado por predefinição. Se estiver a utilizar versões mais antigas (10 ou anteriores) do SLES no Azure, consulte a [artigo BDC](https://www.novell.com/support/kb/doc.php?id=3456486) para ativar a consola de série.
Oracle Linux        | Acesso de consola de série ativado por predefinição.

### <a name="custom-linux-images"></a>Imagens do Linux personalizadas
Para ativar a consola de série para a sua imagem de VM do Linux personalizada, ative o acesso à consola no ficheiro *nomedeanfitrião inittab* para executar um terminal em `ttyS0`. Por exemplo: `S0:12345:respawn:/sbin/agetty -L 115200 console vt102`.

Também vai querer adicionar ttys0 como o destino para saída serial. Para obter mais informações sobre como configurar uma imagem personalizada para trabalhar com a consola de série, consulte os requisitos de sistema geral no [criar e carregar um VHD do Linux no Azure](https://aka.ms/createuploadvhd#general-linux-system-requirements).

Se estiver criando um kernel personalizado, considere ativar estes sinalizadores de kernel: `CONFIG_SERIAL_8250=y` e `CONFIG_MAGIC_SYSRQ_SERIAL=y`. O ficheiro de configuração geralmente está localizado na */boot/* caminho. |

## <a name="common-scenarios-for-accessing-the-serial-console"></a>Cenários comuns para aceder à consola de série

Cenário          | Ações na consola de série
:------------------|:-----------------------------------------
Dividido *FSTAB* ficheiro | Prima a **Enter** tecla para continuar e utilize um editor de texto para corrigir o *FSTAB* ficheiro. Poderá ter de estar no modo de utilizador único para fazer isso. Para obter mais informações, consulte a secção consola de série [como corrigir problemas de fstab](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors) e [consola de série de utilização para aceder a GRUB e modo de utilizador único](serial-console-grub-single-user-mode.md).
Regras de firewall incorreta |  Se tiver configurado iptables para bloquear a conectividade SSH, pode utilizar a consola de série para interagir com a VM sem a necessidade de SSH. Obter mais detalhes podem ser encontrados no [iptables cara página](https://linux.die.net/man/8/iptables).<br>Da mesma forma, se seu firewalld está a bloquear o acesso SSH, pode aceder à VM através da consola de série e reconfigurar firewalld. Podem encontrar mais detalhes no [firewalld documentação](https://firewalld.org/documentation/).
Danos/verificação de sistema de ficheiros | Consulte a secção de consola de série do [VM Linux do Azure não consegue iniciar devido a erros de sistema de ficheiros](https://support.microsoft.com/en-us/help/3213321/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck) para obter mais detalhes sobre como resolver danificado sistemas de ficheiros utilizando a consola de série.
Problemas de configuração de SSH | Aceder à consola de série e alterar as definições. Consola de série pode ser utilizada independentemente da configuração de SSH de uma VM à medida que não é necessário que a VM tem conectividade de rede a funcionar. Um guia de resolução de problemas está disponível em [resolver problemas de SSH ligações a uma VM do Linux do Azure que falhar, erros de saída, ou for recusada](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-ssh-connection). Obter mais detalhes estão disponíveis em [detalhadas SSH resolução de problemas de passos para problemas de ligação a uma VM do Linux no Azure](./detailed-troubleshoot-ssh-connection.md)
Interagir com o carregador de inicialização | Reinicie a sua VM a partir do painel da consola de série para acessar o GRUB na sua VM do Linux. Para obter mais detalhes e informações específicas de distribuição, consulte [consola de série de utilização para aceder a GRUB e modo de utilizador único](serial-console-grub-single-user-mode.md).

## <a name="disable-the-serial-console"></a>Desativar a consola de série
Por predefinição, todas as subscrições têm acesso de consola de série ativado. Pode desativar a consola de série no nível de assinatura ou nível de conjunto de dimensionamento de máquina VM/virtual. Tenha em atenção que o diagnóstico de arranque tem de estar ativado numa VM por ordem para a consola de série para trabalhar.

### <a name="vmvirtual-machine-scale-set-level-disable"></a>Desativação de ao nível do conjunto de dimensionamento de máquina VM/virtual
A consola de série pode ser desativada para um dimensionamento VM ou numa máquina virtual específico definido, desativando a definição de diagnóstico de arranque. Desative o diagnóstico de arranque do portal do Azure para desativar a consola de série para a VM ou o conjunto de dimensionamento de máquina virtual. Se estiver a utilizar o consola de série num conjunto de dimensionamento de máquina virtual, certifique-se de que atualizar instâncias do conjunto de dimensionamento de máquina virtual para o modelo mais recente.

> [!NOTE]
> Para ativar ou desativar a consola de série para uma subscrição, tem de ter permissões de escrita para a subscrição. Estas permissões incluem funções de administrador ou proprietário. Funções personalizadas também podem ter permissões de escrita.

### <a name="subscription-level-disable"></a>Desativar o nível de assinatura
A consola de série pode ser desabilitada para uma subscrição completa através da [chamada à API de REST de consola desativar](/rest/api/serialconsole/console/disableconsole). Esta ação requer acesso de nível de Contribuidor ou superior para a subscrição. Pode utilizar o **experimentar** função disponível nesta página de documentação de API para desativar e ativar a consola de série para uma subscrição. Introduza o seu ID de subscrição **subscriptionId**, introduza **predefinido** para **predefinição**e, em seguida, selecione **executar**. Os comandos da CLI do Azure ainda não estão disponíveis.

Para reativar a consola de série para uma subscrição, utilize o [chamada à API de REST de consola ativar](/rest/api/serialconsole/console/enableconsole).

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

### <a name="use-serial-console-with-a-screen-reader"></a>Utilizar a consola de série com um leitor de ecrã
A consola de série tem suporte de leitor de ecrã incorporado. Navegação com um leitor de ecrã ativado, permitirá que o texto alternativo para o botão selecionado atualmente a ser lido em voz alta pelo leitor de ecrã.

## <a name="errors"></a>Erros
Como a maioria dos erros são transitórios, repetir a ligação pode, muitas vezes, corrigi-los. A tabela seguinte mostra uma lista de erros e mitigações. Estes erros e mitigações aplicam-se para ambas as VMs e instâncias do conjunto de dimensionamento de máquinas virtuais.

Erro                            |   Mitigação
:---------------------------------|:--------------------------------------------|
Não é possível obter as definições de diagnóstico de arranque de  *&lt;VMNAME&gt;* . Para utilizar a consola de série, certifique-se de que o diagnóstico de arranque está ativado para esta VM. | Certifique-se de que a VM tem [diagnósticos de arranque](boot-diagnostics.md) ativada.
A VM está num estado parado desalocado. Iniciar a VM e repita a ligação da consola de série. | A VM deve estar num estado iniciado para aceder à consola de série.
Não tem as permissões necessárias para usar essa VM com a consola de série. Certifique-se de que tem, pelo menos, permissões de função de contribuinte de Máquina Virtual.| O acesso de consola de série requer determinadas permissões. Para obter mais informações, consulte [pré-requisitos](#prerequisites).
Não é possível determinar o grupo de recursos para a conta de armazenamento do diagnóstico de arranque  *&lt;STORAGEACCOUNTNAME&gt;* . Certifique-se de que o diagnóstico de arranque está ativado para esta VM e que tem acesso a esta conta de armazenamento. | O acesso de consola de série requer determinadas permissões. Para obter mais informações, consulte [pré-requisitos](#prerequisites).
Web socket foi fechado ou não foi possível abrir. | Poderá ter de lista aprovada `*.console.azure.com`. Um mais detalhado for mas a abordagem mais é à lista de permissões a [intervalos de IP de Datacenter do Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653), que alterar bastante regularmente.
Uma resposta de "dizer proibido" foi encontrada ao aceder à conta de armazenamento do diagnóstico de arranque desta VM. | Certifique-se de que o diagnóstico de arranque não tem uma firewall de conta. Uma conta de armazenamento do diagnóstico de arranque acessível é necessária para a consola de série função.

## <a name="known-issues"></a>Problemas conhecidos
Estamos cientes de alguns problemas com a consola de série. Aqui está uma lista desses problemas e os passos para a mitigação. Estes problemas e atenuações aplicam-se para ambas as VMs e instâncias do conjunto de dimensionamento de máquinas virtuais.

Problema                           |   Mitigação
:---------------------------------|:--------------------------------------------|
Premir **Enter** depois da faixa de ligação não causa um prompt de início de sessão a apresentar. | Para obter mais informações, consulte [Hitting introduza não faz nada](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). Este problema pode ocorrer se estiver a executar uma VM personalizada, a aplicação protegida ou a configuração GRUB que faz com que o Linux para não conseguir estabelecer uma ligação para a porta serial.
Texto da consola de série ocupa apenas uma parte do tamanho da tela (muitas vezes, depois de utilizar um editor de texto). | Seriais consolas não suportam a negociação sobre o tamanho da janela ([RFC 1073](https://www.ietf.org/rfc/rfc1073.txt)), o que significa que não vai haver nenhum sinal SIGWINCH enviado para atualizar o tamanho da tela e a VM será não têm conhecimento de tamanho de seu terminal. Instalar xterm ou um utilitário semelhante para oferecer a com o `resize` comando e, em seguida, execute `resize`.
Colar longas seqüências de caracteres não funciona. | A consola de série limita o comprimento de cadeias de caracteres colado no terminal para 2048 carateres para evitar sobrecarregar a largura de banda da porta serial.
Consola de série não funciona com um firewall de conta de armazenamento. | Consola de série por design não consegue trabalhar com firewalls de conta de armazenamento ativadas nesta conta de armazenamento do diagnóstico de arranque.
Consola de série não funciona com uma conta de armazenamento com a geração 2 de armazenamento do Azure Data Lake com espaços de nomes hierárquicos. | Este é um problema conhecido com espaços de nomes hierárquicos. Para atenuar, certifique-se de que conta de armazenamento de diagnósticos de arranque da VM não é criada com a geração 2 de armazenamento do Azure Data Lake. Esta opção só pode ser definida durante a criação de conta de armazenamento. Poderá ter de criar um diagnóstico de arranque separado de conta de armazenamento sem o Azure Data Lake Storage Gen2 ativado para atenuar este problema.
Teclado irregular de entrada em imagens SLES BYOS. Entrada de teclado apenas esporadicamente é reconhecida. | Este é um problema com o pacote de Plymouth. Plymouth não deve ser executado no Azure, como não precisa de uma tela inicial e Plymouth interfere com a capacidade de plataforma de utilizar a consola de série. Remover Plymouth com `sudo zypper remove plymouth` e reinicie o computador. Em alternativa, modifique a linha de kernel de sua configuração GRUB acrescentando `plymouth.enable=0` ao final da linha. Pode fazê-lo [editar a entrada de inicialização no momento da inicialização](https://aka.ms/serialconsolegrub#single-user-mode-in-suse-sles), ou ao editar a linha GRUB_CMDLINE_LINUX `/etc/default/grub`, a reconstrução de GRUB com `grub2-mkconfig -o /boot/grub2/grub.cfg`e, em seguida, reiniciar.


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

**P. Quem pode aceder a consola de série para meu conjunto de dimensionamento de máquina VM/virtual?**

R. Tem de ter a função de contribuinte de Máquina Virtual ou superior para um conjunto para aceder à consola de série de dimensionamento de VM ou numa máquina virtual.

**P. Meu consola de série não é apresentada qualquer coisa, o que fazer?**

R. A imagem é provavelmente mal configurada para acesso à consola de série. Para obter informações sobre como configurar a sua imagem para ativar a consola de série, consulte [consola de série disponibilidade de distribuição de Linux](#serial-console-linux-distribution-availability).

**P. A consola de série está disponível para os conjuntos de dimensionamento de máquinas virtuais?**

R. Sim, é! Consulte [consola de série para conjuntos de dimensionamento de máquinas virtuais](#serial-console-for-virtual-machine-scale-sets)

**P. Se configurei minha VM ou o conjunto ao utilizar apenas autenticação por chave SSH de dimensionamento de máquina virtual, posso continuar a utilizar a consola de série para ligar a minha instância de conjunto de dimensionamento de máquina VM/virtual?**

R. Sim. Como a consola de série não requer chaves SSH, só precisa de configurar uma combinação de nome de utilizador/palavra-passe. Pode fazê-lo selecionando **Repor palavra-passe** no portal do Azure e utilizar essas credenciais para iniciar sessão na consola de série.

## <a name="next-steps"></a>Passos Seguintes
* Utilizar a consola de série para [acessar GRUB e modo de utilizador único](serial-console-grub-single-user-mode.md).
* Utilizar a consola de série para [chamadas NMI e SysRq](serial-console-nmi-sysrq.md).
* Saiba como utilizar a consola de série para [ativar o GRUB nas várias distribuições](https://blogs.msdn.microsoft.com/linuxonazure/2018/10/23/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/).
* Também está disponível para a consola de série [Windows VMs](../windows/serial-console.md).
* Saiba mais sobre [diagnósticos de arranque](boot-diagnostics.md).

