---
title: Console serial do Azure para Linux | Microsoft Docs
description: Console serial bidirecional para máquinas virtuais do Azure e conjuntos de dimensionamento de máquinas virtuais.
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 5/1/2019
ms.author: alsin
ms.openlocfilehash: f6e08f113e29b44e4ec94d14624d62c1c3d48d45
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70124472"
---
# <a name="azure-serial-console-for-linux"></a>Console serial do Azure para Linux

O console serial no portal do Azure fornece acesso a um console baseado em texto para VMs (máquinas virtuais) do Linux e instâncias do conjunto de dimensionamento de máquinas virtuais. Essa conexão serial conecta-se à porta serial ttyS0 da instância da VM ou do conjunto de dimensionamento de máquinas virtuais, fornecendo acesso a ela independentemente da rede ou do estado do sistema operacional. O console serial só pode ser acessado usando o portal do Azure e é permitido somente para os usuários que têm uma função de acesso de colaborador ou superior ao conjunto de dimensionamento de máquinas virtuais ou VM.

O console serial funciona da mesma maneira para VMs e instâncias do conjunto de dimensionamento de máquinas virtuais. Neste documento, todas as menção a VMs irão incluir implicitamente as instâncias do conjunto de dimensionamento de máquinas virtuais, salvo indicação em contrário.

Para obter a documentação do console serial para Windows, consulte [console serial para Windows](../windows/serial-console.md).

> [!NOTE]
> O console serial está geralmente disponível em regiões globais do Azure. Ele ainda não está disponível no Azure government ou a clouds do Azure China.


## <a name="prerequisites"></a>Pré-requisitos

- A instância do conjunto de dimensionamento de máquinas virtuais ou VM deve usar o modelo de implantação do gerenciamento de recursos. Implementações clássicas não são suportadas.

- Sua conta que usa o console serial deve ter a [função de colaborador de máquina virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) para a VM e a conta de armazenamento de [diagnóstico de inicialização](boot-diagnostics.md)

- Sua VM ou instância do conjunto de dimensionamento de máquinas virtuais deve ter um usuário baseado em senha. Pode criar uma com o [Repor palavra-passe](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) função da extensão de acesso VM. Selecione **Repor palavra-passe** partir do **suporte + resolução de problemas** secção.

- Sua VM ou instância do conjunto de dimensionamento de máquinas virtuais deve ter o [diagnóstico de inicialização](boot-diagnostics.md) habilitado.

    ![Definições de diagnóstico de arranque](./media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

- Para configurações específicas para distribuições do Linux, consulte [console serial disponibilidade de distribuição do Linux](#serial-console-linux-distribution-availability).

- A instância do conjunto de dimensionamento de máquinas virtuais ou VM deve ser configurada para a saída serial em `ttys0`. Esse é o padrão para as imagens do Azure, mas você desejará fazer uma verificação dupla em imagens personalizadas. Detalhes [abaixo](#custom-linux-images).


> [!NOTE]
> O console serial requer um usuário local com uma senha configurada. VMs ou conjuntos de dimensionamento de máquinas virtuais configurados somente com uma chave pública SSH não poderão entrar no console serial. Para criar um usuário local com uma senha, use a [extensão VMAccess](https://docs.microsoft.com/azure/virtual-machines/linux/using-vmaccess-extension), que está disponível no portal selecionando **redefinir senha** no portal do Azure e criar um usuário local com uma senha.
> Você também pode redefinir a senha de administrador em sua conta [usando o grub para inicializar no modo de usuário único](./serial-console-grub-single-user-mode.md).

## <a name="serial-console-linux-distribution-availability"></a>Disponibilidade de distribuição do Linux do console serial
Para que o console serial funcione corretamente, o sistema operacional convidado deve ser configurado para ler e gravar mensagens do console na porta serial. As [distribuições mais endossadas do Linux do Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) têm o console serial configurado por padrão. A seleção de **console serial** na seção **suporte + solução de problemas** do portal do Azure fornece acesso ao console serial.

> [!NOTE]
> Se você não estiver vendo nada no console serial, verifique se o diagnóstico de inicialização está habilitado em sua VM. Pressionar **Enter** geralmente corrigirá problemas em que nada está aparecendo no console serial.

Distribuição      | Acesso à consola de série
:-----------|:---------------------
Red Hat Enterprise Linux    | O acesso ao Console serial habilitado por padrão.
CentOS      | O acesso ao Console serial habilitado por padrão.
Ubuntu      | O acesso ao Console serial habilitado por padrão.
CoreOS      | O acesso ao Console serial habilitado por padrão.
SUSE        | As imagens SLES mais recentes disponíveis no Azure têm o acesso ao console serial habilitado por padrão. Se você estiver usando versões mais antigas (10 ou anteriores) do SLES no Azure, consulte o [artigo da base de conhecimento](https://www.novell.com/support/kb/doc.php?id=3456486) para habilitar o console serial.
Oracle Linux        | O acesso ao Console serial habilitado por padrão.

### <a name="custom-linux-images"></a>Imagens personalizadas do Linux
Para habilitar o console serial para sua imagem de VM Linux personalizada, habilite o acesso ao console no arquivo */etc/inittab* para executar `ttyS0`um terminal. Por exemplo: `S0:12345:respawn:/sbin/agetty -L 115200 console vt102`. Talvez você também precise gerar um Getty em ttyS0. Isso pode ser feito com `systemctl start serial-getty@ttyS0.service`.

Você também vai querer adicionar ttyS0 como o destino para a saída serial. Para obter mais informações sobre como configurar uma imagem personalizada para trabalhar com o console serial, consulte os requisitos gerais do sistema em [criar e carregar um VHD do Linux no Azure](https://aka.ms/createuploadvhd#general-linux-system-requirements).

Se você estiver criando um kernel personalizado, considere habilitar estes sinalizadores de kernel `CONFIG_SERIAL_8250=y` : `CONFIG_MAGIC_SYSRQ_SERIAL=y`e. O arquivo de configuração normalmente está localizado no caminho */boot/* .

## <a name="common-scenarios-for-accessing-the-serial-console"></a>Cenários comuns para acessar o console serial

Cenário          | Ações no console serial
:------------------|:-----------------------------------------
Arquivo *fstab* danificado | Pressione a tecla **Enter** para continuar e use um editor de texto para corrigir o arquivo *fstab* . Talvez seja necessário estar no modo de usuário único para fazer isso. Para obter mais informações, consulte a seção do console serial de [como corrigir problemas do fstab](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors) e [usar o console serial para acessar o grub e o modo de usuário único](serial-console-grub-single-user-mode.md).
Regras de firewall incorreta |  Se você tiver configurado o iptables para bloquear a conectividade SSH, poderá usar o console serial para interagir com sua VM sem precisar de SSH. Mais detalhes podem ser encontrados na [página do iptables Man](https://linux.die.net/man/8/iptables).<br>Da mesma forma, se o firewall estiver bloqueando o acesso SSH, você poderá acessar a VM por meio do console serial e reconfigurar o firewall. Mais detalhes podem ser encontrados na [documentação do firewall](https://firewalld.org/documentation/).
Danos/verificação de sistema de ficheiros | Consulte a seção do console serial da [VM Linux do Azure não pode ser iniciada devido a erros do sistema de arquivos](https://support.microsoft.com/en-us/help/3213321/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck) para obter mais detalhes sobre como solucionar problemas em sistemas de arquivos corrompidos usando o console serial.
Problemas de configuração de SSH | Aceder à consola de série e alterar as definições. Console serial pode ser usada independentemente da configuração de SSH de uma VM, pois ela não requer que a VM tenha conectividade de rede para funcionar. Um guia de solução de problemas está disponível em [solucionar problemas de conexões SSH com uma VM Linux do Azure que falha, com erros ou é recusada](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-ssh-connection). Mais detalhes estão disponíveis em [etapas detalhadas de solução de problemas de SSH para problemas de conexão a uma VM do Linux no Azure](./detailed-troubleshoot-ssh-connection.md)
Interagir com o carregador de inicialização | Reinicie a VM de dentro da folha do console serial para acessar o GRUB em sua VM Linux. Para obter mais detalhes e informações específicas do distribuição, consulte [usar o console serial para acessar o grub e o modo de usuário único](serial-console-grub-single-user-mode.md).

## <a name="disable-the-serial-console"></a>Desabilitar o console serial

Por padrão, todas as assinaturas têm o acesso ao console serial habilitado. Você pode desabilitar o console serial no nível de assinatura ou de conjunto de dimensionamento de máquinas virtuais/VM. Para obter instruções detalhadas, visite [habilitar e desabilitar o console serial do Azure](./serial-console-enable-disable.md).

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
> Isso significa que um utilizador que está desligado não terminar a sessão. A capacidade de impor um logout após a desconexão (usando SIGHUP ou mecanismo semelhante) ainda está no roteiro. Para o Windows, há um tempo limite automático habilitado no console administrativo especial (SAC); no entanto, para Linux, você pode definir a configuração de tempo limite do terminal. Para fazer isso, adicione `export TMOUT=600` em seu arquivo *. bash_profile* ou *. Profile* para o usuário que você usa para entrar no console do. Essa configuração atingirá o tempo limite da sessão após 10 minutos.

## <a name="accessibility"></a>Acessibilidade
Acessibilidade é um foco importante para o console serial do Azure. Para esse fim, garantimos que o console serial esteja totalmente acessível.

### <a name="keyboard-navigation"></a>Navegação do teclado
Utilize o **separador** chave no teclado para navegar na interface de consola de série no portal do Azure. Sua localização será realçada na tela. Para deixar o foco da janela de consola de série, prima **Ctrl**+**F6** no teclado.

### <a name="use-serial-console-with-a-screen-reader"></a>Usar o console serial com um leitor de tela
A consola de série tem suporte de leitor de ecrã incorporado. Navegação com um leitor de ecrã ativado, permitirá que o texto alternativo para o botão selecionado atualmente a ser lido em voz alta pelo leitor de ecrã.

## <a name="known-issues"></a>Problemas conhecidos
Estamos cientes de alguns problemas com a consola de série. Aqui está uma lista desses problemas e os passos para a mitigação. Esses problemas e atenuações se aplicam tanto a VMs quanto a instâncias do conjunto de dimensionamento de máquinas virtuais.

Problema                           |   Mitigação
:---------------------------------|:--------------------------------------------|
Premir **Enter** depois da faixa de ligação não causa um prompt de início de sessão a apresentar. | Para obter mais informações, consulte [Hitting introduza não faz nada](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). Esse problema pode ocorrer se você estiver executando uma VM personalizada, um dispositivo protegido ou uma configuração do GRUB que faz com que o Linux falhe na conexão com a porta serial.
Console serial texto só ocupa uma parte do tamanho da tela (geralmente, depois de usar um editor de texto). | Os consoles seriais não dão suporte à negociação sobre o tamanho da janela ([RFC 1073](https://www.ietf.org/rfc/rfc1073.txt)), o que significa que não haverá nenhum sinal SIGWINCH enviado para atualizar o tamanho da tela e a VM não terá conhecimento do tamanho do seu terminal. Instale o xterm ou um utilitário semelhante para fornecer o `resize` comando e, em seguida, execute. `resize`
Colar longas seqüências de caracteres não funciona. | A consola de série limita o comprimento de cadeias de caracteres colado no terminal para 2048 carateres para evitar sobrecarregar a largura de banda da porta serial.
Console serial não funciona com um firewall de conta de armazenamento. | Console serial por design não pode funcionar com firewalls de conta de armazenamento habilitados na conta de armazenamento de diagnóstico de inicialização.
Console serial não funciona com uma conta de armazenamento usando Azure Data Lake Storage Gen2 com namespaces hierárquicos. | Esse é um problema conhecido com namespaces hierárquicos. Para atenuar, verifique se a conta de armazenamento do diagnóstico de inicialização da VM não foi criada usando Azure Data Lake Storage Gen2. Essa opção só pode ser definida na criação da conta de armazenamento. Talvez seja necessário criar uma conta de armazenamento de diagnóstico de inicialização separada sem Azure Data Lake Storage Gen2 habilitado para atenuar esse problema.
Entrada de teclado irregular em imagens SLES BYOS. A entrada do teclado é reconhecida apenas esporadicamente. | Isso é um problema com o pacote Plymouth. Plymouth não deve ser executado no Azure porque você não precisa de uma tela inicial e Plymouth interfere na capacidade da plataforma de usar o console serial. Remova Plymouth com `sudo zypper remove plymouth` e reinicialize. Como alternativa, modifique a linha de kernel da configuração do grub anexando `plymouth.enable=0` ao final da linha. Você pode fazer isso [editando a entrada de inicialização no momento da inicialização](https://aka.ms/serialconsolegrub#single-user-mode-in-suse-sles)ou editando a linha GRUB_CMDLINE_LINUX `/etc/default/grub`no, recriando `grub2-mkconfig -o /boot/grub2/grub.cfg`grub com e reinicializando.


## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

**P. Como posso enviar comentários?**

R. Forneça comentários criando um problema do GitHub em https://aka.ms/serialconsolefeedback. Como alternativa (menos preferencial), pode enviar comentários por meio azserialhelp@microsoft.com ou na categoria de máquina virtual de https://feedback.azure.com.

**P. A consola de série suporta copiar/colar?**

R. Sim. Uso **Ctrl**+**Shift**+**C** e **Ctrl**+**Shift** + **V** de copiar e colar no terminal.

**P. Posso usar o console serial em vez de uma conexão SSH?**

R. Embora esse uso possa parecer tecnicamente possível, o console serial destina-se a ser usado principalmente como uma ferramenta de solução de problemas em situações em que a conectividade via SSH não é possível. É recomendável usar o console serial como uma substituição de SSH pelos seguintes motivos:

- O console serial não tem tanta largura de banda quanto SSH. Como é uma conexão somente de texto, mais interações de GUI pesada são difíceis.
- O acesso Console serial é atualmente possível apenas usando um nome de usuário e senha. Como as chaves SSH são muito mais seguras do que as combinações de nome de usuário/senha, de uma perspectiva de segurança de entrada, recomendamos o SSH sobre o console serial.

**P. Quem pode habilitar ou desabilitar o console serial para minha assinatura?**

R. Para ativar ou desativar a consola de série a um nível de toda a subscrição, tem de ter permissões de escrita para a subscrição. As funções que tem permissão de escrita incluem funções de administrador ou proprietário. Funções personalizadas também podem ter permissões de escrita.

**P. Quem pode acessar o console serial do meu conjunto de dimensionamento de máquinas virtuais/VM?**

R. Você deve ter a função colaborador da máquina virtual ou superior para uma VM ou conjunto de dimensionamento de máquinas virtuais para acessar o console serial.

**P. Meu consola de série não é apresentada qualquer coisa, o que fazer?**

R. A imagem é provavelmente mal configurada para acesso à consola de série. Para obter informações sobre como configurar sua imagem para habilitar o console serial, consulte [console serial disponibilidade de distribuição do Linux](#serial-console-linux-distribution-availability).

**P. A consola de série está disponível para os conjuntos de dimensionamento de máquinas virtuais?**

R. Sim, é! Consulte o [console serial para conjuntos de dimensionamento de máquinas virtuais](serial-console-overview.md#serial-console-for-virtual-machine-scale-sets)

**P. Se eu configurar minha VM ou conjunto de dimensionamento de máquinas virtuais usando apenas a autenticação de chave SSH, ainda poderei usar o console serial para se conectar à minha instância de VM/conjunto de dimensionamento de máquinas virtuais?**

R. Sim. Como o console serial não requer chaves SSH, você só precisa configurar uma combinação de nome de usuário/senha. Você pode fazer isso selecionando **Redefinir senha** no portal do Azure e usando essas credenciais para entrar no console serial.

## <a name="next-steps"></a>Passos Seguintes
* Use o console serial para [acessar o grub e o modo de usuário único](serial-console-grub-single-user-mode.md).
* Use o console serial para [chamadas NMI e SysRq](serial-console-nmi-sysrq.md).
* Saiba como usar o console serial para [habilitar o grub em vários distribuições](https://blogs.msdn.microsoft.com/linuxonazure/2018/10/23/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/).
* O console serial também está disponível para [VMs do Windows](../windows/serial-console.md).
* Saiba mais sobre [diagnósticos de arranque](boot-diagnostics.md).

