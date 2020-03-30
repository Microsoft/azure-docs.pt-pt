---
title: Troubleshoot Mobility Service push instalação com recuperação do site Azure f
description: Erros de instalação dos Serviços de Mobilidade ao permitir a replicação para recuperação de desastres com a Recuperação do Site Azure.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.author: ramamill
ms.date: 09/11/2019
ms.openlocfilehash: 3646499ad2104566cb82f3f26c6b55d05f84dc7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73953783"
---
# <a name="troubleshoot-mobility-service-push-installation"></a>Instalação push do Serviço de Mobilidade de Troubleshoot 

A instalação do serviço de mobilidade é um passo fundamental durante a Replicação Ativa. O sucesso deste passo depende apenas do cumprimento dos pré-requisitos e do trabalho com configurações suportadas. As falhas mais comuns que enfrenta durante a instalação do serviço mobility devem-se a:

* [Erros credenciais/privilégios](#credentials-check-errorid-95107--95108)
* [Falhas de login](#login-failures-errorid-95519-95520-95521-95522)
* [Erros de conectividade](#connectivity-failure-errorid-95117--97118)
* [Erros de partilha de ficheiros e impressoras](#file-and-printer-sharing-services-check-errorid-95105--95106)
* [Falhas no OMI](#windows-management-instrumentation-wmi-configuration-check-error-code-95103)
* [Sistemas operativos não suportados](#unsupported-operating-systems)
* [Configurações de Arranque não suportadas](#unsupported-boot-disk-configurations-errorid-95309-95310-95311)
* [Falhas na instalação do VSS](#vss-installation-failures)
* [Nome do dispositivo na configuração GRUB em vez de uUID do dispositivo](#enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320)
* [Volume LVM](#lvm-support-from-920-version)
* [Avisos de reinicialização](#install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266)

Quando ativa a replicação, a Recuperação do Site Azure tenta pressionar a instalação de um agente de serviço de mobilidade na sua máquina virtual. Como parte disto, o servidor de configuração tenta ligar-se à máquina virtual e copiar o Agente. Para permitir uma instalação bem sucedida, siga a orientação passo a passo de resolução de problemas dada abaixo.

## <a name="credentials-check-errorid-95107--95108"></a>Verificação de credenciais (ErrorID: 95107 & 95108)

* Verifique se a conta de utilizador escolhida durante a replicação do ativação é **válida, precisa**.
* A Recuperação do Site Azure requer conta **ROOT** ou conta de utilizador com **privilégios de administrador** para realizar a instalação push. Caso contrário, a instalação de pressão será bloqueada na máquina de origem.
  * Para windows **(error 95107),** verifique se a conta de utilizador tem acesso administrativo, local ou de domínio, na máquina de origem.
  * Se não estiver a utilizar uma conta de domínio, tem de desativar o controlo de acesso remoto ao utilizador no computador local.
    * Para desativar o controlo de acesso remoto ao utilizador, em HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System', adicione um novo DWORD: LocalAccountTokenFilterPolicy. Defina o valor para 1. Para executar este passo, execute o seguinte comando a partir do pedido de comando:

         `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`
  * Para o Linux **(error 95108),** deve escolher a conta raiz para uma instalação bem sucedida de agente de mobilidade. Além disso, os serviços SFTP devem estar em funcionamento. Para ativar o subsistema SFTP e a autenticação de palavra-passe no ficheiro sshd_config:
    1. Inicie sessão como raiz.
    2. Vá ao ficheiro /etc/ssh/sshd_config, encontre a linha que começa com A Autenticação password.
    3. Descodere a linha, e mude o valor para sim.
    4. Encontre a linha que começa com o Subsistema e descomente a linha.
    5. Reinicie o serviço sshd.

Se pretender modificar as credenciais da conta de utilizador escolhida, siga as instruções [aqui](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)dadas .

## <a name="insufficient-privileges-failure-errorid-95517"></a>Falha de privilégios insuficientes (ErrorID: 95517)

Quando o utilizador escolhido para instalar o agente de mobilidade não tiver privilégios de administrador, o servidor de processo de configuração/scale-out não será autorizado a copiar o software do agente de mobilidade para a máquina de origem. Portanto, este erro é o resultado de uma falha negada no acesso. Certifique-se de que a conta de utilizador tem privilégios de administrador.

Se pretender modificar as credenciais da conta de utilizador escolhida, siga as instruções [aqui](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)dadas .

## <a name="insufficient-privileges-failure-errorid-95518"></a>Falha de privilégios insuficientes (ErrorID: 95518)

Quando o estabelecimento de relação de confiança de domínio entre o domínio primário e a estação de trabalho falha enquanto tenta iniciar sessão na máquina de origem, a instalação do agente de mobilidade falha com o erro ID 95518. Assim, certifique-se de que a conta de utilizador utilizada para instalar o agente de mobilidade tem privilégios administrativos para iniciar sessão através do domínio primário da máquina de origem.

Se pretender modificar as credenciais da conta de utilizador escolhida, siga as instruções [aqui](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)dadas .

## <a name="login-failures-errorid-95519-95520-95521-95522"></a>Falhas de login (ErrorID: 95519, 95520, 95521, 95522)

### <a name="credentials-of-the-user-account-have-been-disabled-errorid-95519"></a>As credenciais da conta de utilizador foram desativadas (ErrorID: 95519)

A conta de utilizador escolhida durante a Replicação Ativa foi desativada. Para ativar a conta de utilizador, consulte o artigo [aqui](https://aka.ms/enable_login_user) ou execute o seguinte comando substituindo o nome de *utilizador* de texto pelo nome de utilizador real.
`net user 'username' /active:yes`

### <a name="credentials-locked-out-due-to-multiple-failed-login-attempts-errorid-95520"></a>Credenciais bloqueadas devido a múltiplas tentativas de login falhadas (ErrorID: 95520)

Vários esforços de retry falhados para aceder a uma máquina bloquearão a conta do utilizador. A falha pode dever-se a:

* As credenciais fornecidas durante a configuração da configuração estão incorretas OU
* A conta de utilizador escolhida durante a Replicação Ativa está errada

Assim, modifique as credenciais escolhidas seguindo as instruções [aqui](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation) dadas e retente a operação após algum tempo.

### <a name="logon-servers-are-not-available-on-the-source-machine-errorid-95521"></a>Os servidores de logon não estão disponíveis na máquina de origem (ErrorID: 95521)

Este erro ocorre quando os servidores de início de sessão não estão disponíveis na máquina de origem. A indisponibilidade de servidores de logon conduzirá a uma falha no pedido de login e, portanto, o agente de mobilidade não pode ser instalado. Para obter um Login bem sucedido, certifique-se de que os servidores De Logon estão disponíveis na máquina de origem e ligue o serviço Delogon. Para obter instruções detalhadas, consulte o KB [139410](https://support.microsoft.com/en-in/help/139410/err-msg-there-are-currently-no-logon-servers-available) Err Msg: Atualmente não existem servidores de logon disponíveis.

### <a name="logon-service-isnt-running-on-the-source-machine-errorid-95522"></a>O serviço de logon não está a funcionar na máquina de origem (ErrorID: 95522)

O serviço de login não está a funcionar na sua máquina de origem e causou falha no pedido de login. Portanto, o agente de mobilidade não pode ser instalado. Para resolver, certifique-se de que o serviço Logon está a funcionar na máquina de origem para obter login com sucesso. Para iniciar o serviço de logon, execute o comando "logon de início líquido" a partir do pedido de comando ou inicie o serviço "NetLogon" do gestor de tarefas.

## <a name="connectivity-failure-errorid-95117--97118"></a>**Falha de conectividade (ErrorID: 95117 & 97118)**

O servidor de processo de configuração/scale-out tenta ligar-se ao VM de origem para instalar o agente mobility. Este erro ocorre quando a máquina de origem não é alcançável devido a problemas de conectividade da rede. Para resolver,

* Certifique-se de que é capaz de obter a sua máquina Fonte a partir do servidor de Configuração. Se escolheu o servidor de processo sinuoso durante a replicação ativa, certifique-se de que é capaz de obter o ping da sua máquina Source a partir do servidor de processos.
  * A partir da linha de comando da máquina Source Server, utilize a Telnet para pingar o servidor de processo de configuração/scale-out com a porta https (135), como mostrado abaixo para ver se existem problemas de conectividade de rede ou problemas de bloqueio de portas de firewall.

     `telnet <CS/ scale-out PS IP address> <135>`
* Adicionalmente, para **Linux VM,**
  * Verifique se estão instalados os mais recentes openssh, openssh-server e os pacotes de abertura.
  * Verifique e certifique-se de que a Secure Shell (SSH) está ativada e está a funcionar na porta 22.
  * Os serviços SFTP devem estar a funcionar. Para permitir a autenticação do subsistema SFTP e da palavra-passe no ficheiro sshd_config,
    * Inicie sessão como raiz.
    * Vá ao ficheiro /etc/ssh/sshd_config, encontre a linha que começa com A Autenticação password.
    * Descodere a linha, e mudar o valor para sim
    * Encontre a linha que começa com subsistema, e descomente a linha
    * Reinicie o serviço sshd.
* Uma tentativa de ligação poderia ter falhado se não houvesse uma resposta adequada após um período de tempo, ou a ligação estabelecida falhou porque o hospedeiro conectado não respondeu.
* Pode ser um problema relacionado conectividade/rede/domínio. Pode também dever-se à questão da resolução do nome DNS ou à questão da exaustão portuário tCP. Verifique se existem problemas tão conhecidos no seu domínio.

## <a name="connectivity-failure-errorid-95523"></a>Falha de conectividade (ErrorID: 95523)

Este erro ocorre quando a rede em que a máquina de origem reside não é encontrada ou pode ter sido eliminada ou já não está disponível. A única forma de resolver o erro é garantindo que a rede existe.

## <a name="file-and-printer-sharing-services-check-errorid-95105--95106"></a>Verificação de serviços de partilha de ficheiros e impressoras (ErrorID: 95105 & 95106)

Após verificação da conectividade, verifique se o serviço de partilha de ficheiros e impressoras está ativado na sua máquina virtual. Estas definições são necessárias para copiar o agente de mobilidade na máquina de origem.

Para **windows 2008 R2 e versões anteriores,**

* Para permitir a partilha de ficheiros e impressões através do Windows Firewall,
  * Open painel de controlo -> System and Security -> Windows Firewall -> no painel esquerdo, clique em definições avançadas - > clique em regras de entrada na árvore da consola.
  * Localizar regras De Partilha de Ficheiros e Impressoras (NB-Session-In) e Partilha de Ficheiros e Impressoras (SMB-In). Para cada regra, clique na regra e clique em **Regra ativação**.
* Para permitir a partilha de ficheiros com a Política de Grupos,
  * Vá para iniciar, escreva gpmc.msc e procure.
  * No painel de navegação, abra as seguintes pastas: Política local de computador, Configuração do Utilizador, Modelos Administrativos, Componentes do Windows e Partilha de Rede.
  * No painel de detalhes, clique em dois **cliques Evite que os utilizadores partilhem ficheiros dentro do seu perfil**. Para desativar a definição de Política de Grupo e ativar a capacidade do utilizador de partilhar ficheiros, clique em Desativar. Clique em OK para guardar as alterações. Para saber mais, consulte [Enable ou desative](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754359(v=ws.10))a partilha de ficheiros com a Política de Grupo .

Para **versões posteriores,** siga as instruções fornecidas no [Serviço de Mobilidade para recuperação de VMware vMs e servidores físicos](vmware-azure-install-mobility-service.md) para permitir a partilha de ficheiros e impressoras.

## <a name="windows-management-instrumentation-wmi-configuration-check-error-code-95103"></a>Verificação de configuração de configuração de instrumentação de gestão do Windows (WMI) (código de erro: 95103)

Depois de verificar os serviços de ficheiros e impressoras, ative o serviço WMI para perfis privados, públicos e de domínio através da firewall. Estas definições são necessárias para completar a execução remota na máquina de origem. Para permitir,

* Vá ao Painel de Controlo, clique em Segurança e, em seguida, clique no Windows Firewall.
* Clique em Alterar Definições e, em seguida, clique no separador Exceções.
* Na janela Exceções, selecione a caixa de verificação para instrumentação de gestão do Windows (WMI) para ativar o tráfego de WMI através da firewall. 

Também pode ativar o tráfego de WMI através da firewall no pedido de comando. Utilize o seguinte comando`netsh advfirewall firewall set rule group="windows management instrumentation (wmi)" new enable=yes`
Outros artigos de resolução de problemas da OMI podem ser encontrados nos seguintes artigos.

* [Testes básicos de OMI](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/)
* [Resolução de problemas da WMI](https://msdn.microsoft.com/library/aa394603(v=vs.85).aspx)
* [Problemas de resolução de problemas com scripts WMI e serviços WMI](https://technet.microsoft.com/library/ff406382.aspx#H22)

## <a name="unsupported-operating-systems"></a>Sistemas Operativos não suportados

Outra razão mais comum para a falha pode ser devido a um sistema operativo não suportado. Certifique-se de que está na versão suporte do Sistema Operativo/Kernel para uma instalação bem sucedida do serviço mobility. Evite o uso de patch privado.
Para ver a lista de sistemas operativos e versões kernel suportadas pela Recuperação do Site Azure, consulte o nosso [documento de matriz](vmware-physical-azure-support-matrix.md#replicated-machines)de suporte .

## <a name="unsupported-boot-disk-configurations-errorid-95309-95310-95311"></a>Configurações de disco de arranque não suportadas (ErrorID: 95309, 95310, 95311)

### <a name="boot-and-system-partitions--volumes-are-not-the-same-disk-errorid-95309"></a>Partições/volumes de arranque e sistema não são o mesmo disco (ErrorID: 95309)

Antes da versão 9.20, as divisórias/volumes de arranque e sistema em diferentes discos era uma configuração não suportada. A partir da [versão 9.20,](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery)esta configuração é suportada. Utilize a versão mais recente para este suporte.

### <a name="the-boot-disk-is-not-available-errorid-95310"></a>O disco de arranque não está disponível (ErrorID: 95310)

Uma máquina virtual sem disco de arranque não pode ser protegida. Isto é para garantir uma recuperação suave da máquina virtual durante o funcionamento da falha. A ausência do disco de arranque resulta em falha no arranque da máquina após a falha. Certifique-se de que a máquina virtual contém disco de arranque e tente novamente o funcionamento. Além disso, note que vários discos de boot na mesma máquina não são suportados.

### <a name="multiple-boot-disks-present-on-the-source-machine-errorid-95311"></a>Vários discos boot presentes na máquina de origem (ErrorID: 95311)

Uma máquina virtual com vários discos de arranque não é uma [configuração suportada.](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)

## <a name="system-partition-on-multiple-disks-errorid-95313"></a>Partição do sistema em vários discos (ErrorID: 95313)

Antes da versão 9.20, a partição de raízes ou o volume colocado em vários discos era uma configuração não suportada. A partir da [versão 9.20,](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery)esta configuração é suportada. Utilize a versão mais recente para este suporte.

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320"></a>Ativar a proteção falhou como nome do dispositivo mencionado na configuração GRUB em vez de UUID (ErrorID: 95320)

**Causa possível:** </br>
Os ficheiros de configuração GRUB ("/boot/grub/menu.lst", "/boot/grub/grub.cfg", "/boot/grub2/grub.cfg" ou "/etc/default/grub") podem conter o valor para a **raiz** dos parâmetros e **retomar** como os nomes reais do dispositivo em vez de UUID. A recuperação do site manda a abordagem UUID, uma vez que o nome dos dispositivos pode mudar através do reboot do VM, uma vez que o VM pode não ter o mesmo nome na failover, resultando em problemas. Por exemplo: </br>


- A linha seguinte é do ficheiro GRUB **/boot/grub2/grub.cfg**. <br>
  *linux /boot/vmlinuz-3.12.49-11-predefinição **raiz=/dev/sda2** ${extra_cmdline} **currículo=/dev/sda1** splash=showopts silenciosos silenciosos*


- A linha seguinte é do ficheiro GRUB **/boot/grub/menu.lst**
  *kernel /boot/vmlinuz-3.0.101-63-predefinição **root=/dev/sda2** **resume=//dev/sda1** splash=silent crashkernel=256M-:128M showopts vga=0x314*

Se observar a corda arrojada acima, a GRUB tem nomes reais de dispositivo para os parâmetros "raiz" e "currículo" em vez de UUID.
 
**Como Corrigir:**<br>
Os nomes dos dispositivos devem ser substituídos pelo UUID correspondente.<br>


1. Encontre o UUID do dispositivo executando \<o comando "blkid nome do dispositivo>". Por exemplo:<br>
   ```
   blkid /dev/sda1
   /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
   blkid /dev/sda2 
   /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3" 
   ```

2. Agora substitua o nome do dispositivo pelo seu UUID\<no formato "root=UUID= UUID>". Por exemplo, se substituirmos os nomes do dispositivo por UUID por um parâmetro de raiz e de retoma mencionado acima nos ficheiros "/boot/grub2/grub.cfg", "/boot/grub2/grub.cfg" ou "/etc/predefinido/grub: então as linhas nos ficheiros parecem. <br>
   *Kernel /boot/vmlinuz-3.0.101-63-default **root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4** **currículo=UUID=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b** splash=silent crashkernel=256M-:128M showopts vga=0x314*
3. Reinicie a proteção novamente

## <a name="install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266"></a>Instalar o Serviço de Mobilidade concluído com aviso para reiniciar (ErrorID: 95265 & 95266)

O serviço de mobilidade de recuperação do site tem muitos componentes, um dos quais é chamado de condutor de filtros. O condutor do filtro é carregado na memória do sistema apenas num momento de reinicialização do sistema. Significa que as correções do condutor do filtro só podem ser realizadas quando um novo condutor de filtro é carregado; o que só pode acontecer no momento do reboot do sistema.

**Por favor, note** que este é um aviso e a replicação existente funcionará mesmo após a atualização do novo agente. Pode optar por reiniciar sempre que quiser obter os benefícios do novo condutor de filtro, mas se não reiniciar o antigo condutor do filtro continua a trabalhar. Assim, após uma atualização sem reinicialização, para além do condutor do filtro, **os benefícios de outras melhorias e correções no serviço de mobilidade são realizados**. Assim, embora recomendado, não é obrigatório reiniciar após cada upgrade. Para obter informações sobre quando um reboot é obrigatório, detete o Reboot da máquina de origem após a secção [de atualização do agente de mobilidade](https://aka.ms/v2a_asr_reboot) nas atualizações do Serviço na Recuperação do Site Azure.

> [!TIP]
>Para obter as melhores práticas de agendamento de upgrades durante a sua janela de manutenção, consulte o [Suporte para versões OS/kernel mais recentes](https://aka.ms/v2a_asr_upgrade_practice) em atualizações de Serviço em Recuperação do Site Azure.

## <a name="lvm-support-from-920-version"></a>Suporte LVM a partir da versão 9.20

Antes da versão 9.20, o LVM era suportado apenas para discos de dados. /boot deve estar numa divisória de disco e não ser um volume DELvM.

A partir da [versão 9.20,](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery)o [disco OS no LVM](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) é suportado. Utilize a versão mais recente para este suporte.

## <a name="insufficient-space-errorid-95524"></a>Espaço insuficiente (ErrorID: 95524)

Quando o agente de mobilidade é copiado para a máquina de origem, é necessário pelo menos 100 MB de espaço livre. Por isso, certifique-se de que a sua máquina de origem necessita de espaço livre e de novo tente a operação.

## <a name="vss-installation-failures"></a>Falhas de instalação VSS

A instalação do VSS faz parte da instalação do Agente de mobilidade. Este serviço é utilizado no processo de geração de pontos de recuperação consistentes da aplicação. Falhas durante a instalação VSS podem ocorrer devido a múltiplas razões. Para identificar os erros exatos, consulte **c:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log**. Poucos erros comuns e os passos de resolução são destacados na secção seguinte.

### <a name="vss-error--2147023170-0x800706be---exit-code-511"></a>Erro VSS -2147023170 [0x800706BE] - código de saída 511

Este problema é visto principalmente quando o software antivírus está bloqueando as operações dos serviços de recuperação de sites do Azure. Para resolver este problema:

1. Excluir todas as pastas [aqui](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program)mencionadas .
2. Siga as diretrizes publicadas pelo seu fornecedor antivírus para desbloquear o registo de DLL no Windows.

### <a name="vss-error-7-0x7---exit-code-511"></a>Erro VSS 7 [0x7] - código de saída 511

Trata-se de um erro de tempo de execução e é causado por uma memória insuficiente para instalar vss. Certifique-se de aumentar o espaço do disco para a conclusão bem sucedida desta operação.

### <a name="vss-error--2147023824-0x80070430---exit-code-517"></a>Erro VSS -2147023824 [0x80070430] - código de saída 517

Este erro ocorre quando o serviço de fornecedor VSS de recuperação do site Azure está [marcado para eliminação](https://msdn.microsoft.com/library/ms838153.aspx). Tente instalar o VSS manualmente na máquina de origem, executando a seguinte linha de comando

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

### <a name="vss-error--2147023841-0x8007041f---exit-code-512"></a>Erro VSS -2147023841 [0x8007041F] - código de saída 512

Este erro ocorre quando a base de dados de serviço saciável do fornecedor de recuperação do site Azure VSS está [bloqueada](https://msdn.microsoft.com/library/ms833798.aspx). Tente instalar o VSS manualmente na máquina de origem, executando a seguinte linha de comando

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

Em caso de falha, verifique se algum programa antivírus ou outros serviços estão presos no estado de "Início". Isto poderia manter o bloqueio nos serviços de base de dados. Isto provocará falhas na instalação do fornecedor VSS. Certifique-se de que nenhum serviço está em estado de "arranque" e, em seguida, tente novamente a operação acima.

### <a name="vss-exit-code-806"></a>Código de saída VSS 806

Este erro ocorre quando a conta de utilizador utilizada para a instalação não tem permissões para executar o comando CSScript. Forneça as permissões necessárias à conta do utilizador para executar o script e voltar a tentar a operação.

### <a name="other-vss-errors"></a>Outros erros VSS

Tente instalar manualmente o serviço de fornecedor VSS na máquina de origem, executando a seguinte linha de comando

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`



## <a name="vss-error---0x8004e00f"></a>Erro VSS - 0x8004E00F

Este erro é normalmente encontrado durante a instalação do agente de mobilidade devido a problemas na DCOM e dCOM está em estado crítico.

Utilize o seguinte procedimento para determinar a causa do erro.

**Examinar os registos de instalação**

1. Abra o registo de instalação localizado em c:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log.
2. A presença do seguinte erro indica este problema:

    Desregistar a aplicação existente...  Criar o objeto de catálogo Obtenha a coleção de Aplicações 

    ERRO:

    - Código de erro: -2147164145 [0x8004E00F]
    - Código de saída: 802

Para resolver a questão:

Contacte a equipa da [plataforma Microsoft Windows](https://aka.ms/Windows_Support) para obter assistência na resolução do problema do DCOM.

Quando o problema da DCOM estiver resolvido, reinstale manualmente o Fornecedor VSS de Recuperação do Site Azure utilizando manualmente o seguinte comando:
 
**C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd**
  
Se a consistência da aplicação não for crítica para os seus requisitos de Recuperação de Desastres, pode contornar a instalação do Fornecedor VSS. 

Para contornar a instalação do Fornecedor VSS de Recuperação do Site Azure e instalar manualmente a instalação do post do Fornecedor VSS de Recuperação do Local Do Azure:

1. Instale o serviço de mobilidade. 
   > [!Note]
   > 
   > A instalação falhará na fase de 'Instalação de postes'. 
2. Para contornar a instalação VSS:
   1. Abra o diretório de instalação do Serviço de Recuperação de Mobilidade do Sítio Azure localizado em:
   
      C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
   2. Modifique as scripts de instalação vss de recuperação do site Azure **nMageVSSProvider_Install** e **InMageVSSProvider_Uninstall.cmd** para sempre ter sucesso adicionando as seguintes linhas:
    
      ```     
      rem @echo off
      setlocal
      exit /B 0
      ```

3. Reexecutar manualmente a instalação do Agente de Mobilidade. 
4. Quando a Instalação tiver sucesso e avançar para o próximo passo, **Configure,** retire as linhas que adicionou.
5. Para instalar o fornecedor VSS, abra um pedido de comando como Administrador e execute o seguinte comando:
   
    **C:\Program Files (x86)\Microsoft Azure Site Recovery\agent> .\InMageVSSProvider_Install.cmd**

9. Verifique se o Fornecedor ASR VSS está instalado como um serviço nos Serviços Windows e abra o MMC do Serviço de Componentes para verificar se o Fornecedor ASR VSS está listado.
10. Se a instalação do Fornecedor VSS continuar a falhar, trabalhe com a CX para resolver os erros de permissões no CAPI2.

## <a name="vss-provider-installation-fails-due-to-the-cluster-service-being-enabled-on-non-cluster-machine"></a>Falha a instalação do Fornecedor VSS devido ao serviço de cluster ativado na máquina não cluster

Este problema faz com que a instalação do Agente de Mobilidade de Recuperação do Sítio Azure falhe durante a etapa de instalação do Fornecedor VSS do site ASAzure devido a um problema com a COM+ que impede a instalação do fornecedor VSS.
 
### <a name="to-identify-the-issue"></a>Para identificar a questão

No registo localizado no servidor de configuração em C:\ProgramData\ASRSetupLogs\UploadedLogs\<data-time>UA_InstallLogFile.log, encontrará a seguinte exceção:

A COM+ não pôde falar com o Coordenador de Transações Distribuídas da Microsoft (Exceção da HRESULT: 0x8004E00F)

Para resolver a questão:

1.  Verifique se esta máquina é uma máquina não-cluster e se os componentes do cluster não estão a ser utilizados.
3.  Se os componentes não estiverem a ser utilizados, retire os componentes do cluster da máquina.

## <a name="drivers-are-missing-on-the-source-server"></a>Os condutores estão desaparecidos no Servidor fonte

Se a instalação do Agente de Mobilidade falhar, examine os registos em C:\ProgramData\ASRSetupLogs para determinar se alguns dos controladores necessários estão em falta em alguns conjuntos de controlo.
 
Para resolver a questão:
  
1. Usando um editor de registo como regedit.msc, abra o registo.
2. Abra o nó HKEY_LOCAL_MACHINE\SYSTEM.
3. No nó SYSTEM, localize os conjuntos de controlo.
4. Abra cada conjunto de controlo e verifique se os controladores Windows estão presentes:

   - Atapi
   - Vmbus
   - Storflt
   - Storvsc
   - intelide
 
Reinstale os controladores desaparecidos.

## <a name="next-steps"></a>Passos seguintes

[Saiba como](vmware-azure-tutorial.md) configurar a recuperação de desastres para VMware VMs.
