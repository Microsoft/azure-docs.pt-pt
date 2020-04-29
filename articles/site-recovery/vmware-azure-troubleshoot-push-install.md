---
title: Problemas serviço de mobilidade push instalação com recuperação do site Azure
description: Erros de instalação dos Serviços de Mobilidade ao permitir a replicação para recuperação de desastres com a Recuperação do Site Azure.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.author: ramamill
ms.date: 04/03/2020
ms.openlocfilehash: 1afd931249d4dbeda2b4b25f822837e2a564f959
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80656311"
---
# <a name="troubleshoot-mobility-service-push-installation"></a>Instalação de push de serviço de mobilidade de troubleshoot

A instalação do serviço mobility é um passo fundamental para permitir a replicação. O sucesso deste passo depende do cumprimento dos pré-requisitos e do trabalho com configurações suportadas. As falhas mais comuns que poderá enfrentar durante a instalação do serviço mobility devem-se a:

* [Erros credenciais/privilégios](#credentials-check-errorid-95107--95108)
* [Falhas de login](#login-failures-errorid-95519-95520-95521-95522)
* [Erros de conectividade](#connectivity-failure-errorid-95117--97118)
* [Erros de partilha de ficheiros e impressoras](#file-and-printer-sharing-services-check-errorid-95105--95106)
* [Falhas na instrumentação de gestão do Windows (OMI)](#windows-management-instrumentation-wmi-configuration-check-error-code-95103)
* [Sistemas operativos não suportados](#unsupported-operating-systems)
* [Configurações de arranque não suportadas](#unsupported-boot-disk-configurations-errorid-95309-95310-95311)
* [Falhas de instalação do Serviço de Cópia sombra de volume (VSS)](#vss-installation-failures)
* [Nome do dispositivo na configuração GRUB em vez de uUID do dispositivo](#enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320)
* [Volume Lógico (LVM) volume](#lvm-support-from-920-version)
* [Avisos de reinicialização](#install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266)

Quando ativa a replicação, a Recuperação do Site Azure tenta instalar o agente de serviço mobility na sua máquina virtual (VM). Como parte deste processo, o servidor de configuração tenta ligar-se à máquina virtual e copiar o agente. Para permitir uma instalação bem sucedida, siga a orientação passo-a-passo para resolver problemas.

## <a name="credentials-check-errorid-95107--95108"></a>Verificação de credenciais (ErrorID: 95107 & 95108)

Verifique se a conta de utilizador escolhida durante a replicação do ativação é válida e precisa. A Recuperação do Site Azure requer a conta **raiz** ou a conta de utilizador com **privilégios de administrador** para realizar uma instalação push. Caso contrário, a instalação de pressão será bloqueada na máquina de origem.

Para windows **(erro 95107),** verifique se a conta de utilizador tem acesso administrativo no computador de origem, com uma conta local ou conta de domínio. Se não estiver a utilizar uma conta de domínio, tem de desativar o controlo de acesso remoto ao utilizador no computador local.

* Para adicionar manualmente uma chave de registo que desativa o controlo de acesso remoto ao utilizador:

  * `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System`
  * Adicione um `DWORD`novo :`LocalAccountTokenFilterPolicy`
  * Definir o valor para`1`

* Para adicionar a chave de registo, a partir de um pedido de comando, executar o seguinte comando:

  `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`

Para o Linux **(error 95108),** deve escolher a conta **raiz** para uma instalação bem sucedida do agente de serviço de Mobilidade. Adicionalmente, os serviços do Protocolo de Transferência de Ficheiros SSH (SFTP) devem estar em funcionamento. Para ativar o subsistema SFTP e a autenticação de palavra-passe no ficheiro _sshd_config:_

1. Inicie sessão como **raiz**.
1. Vá ao _ficheiro /etc/ssh/sshd_config,_ encontre `PasswordAuthentication`a linha que começa com .
1. Descodere a linha `yes`e altere o valor para .
1. Encontre a linha `Subsystem`que começa com , e descomente a linha.
1. Reinicie `sshd` o serviço.

Se pretender modificar as credenciais da conta de utilizador escolhida, siga [estas instruções](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="insufficient-privileges-failure-errorid-95517"></a>Falha de privilégios insuficientes (ErrorID: 95517)

Quando o utilizador escolhido para instalar o agente de Mobilidade não tem privilégios de administrador, o servidor de processo de configuração/scale-out não será autorizado a copiar o software do agente de Mobilidade para a máquina de origem. Este erro é o resultado de uma falha de acesso negada. Certifique-se de que a conta de utilizador tem privilégios de administrador.

Se pretender modificar as credenciais da conta de utilizador escolhida, siga [estas instruções](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="insufficient-privileges-failure-errorid-95518"></a>Falha de privilégios insuficientes (ErrorID: 95518)

Quando o estabelecimento de relação de confiança de domínio entre o domínio primário e a estação de trabalho falha enquanto tenta iniciar sessão na máquina de origem, a instalação do agente de mobilidade falha com o erro ID 95518. Certifique-se de que a conta de utilizador utilizada para instalar o agente de Mobilidade tem privilégios administrativos para iniciar sessão através do domínio primário da máquina de origem.

Se pretender modificar as credenciais da conta de utilizador escolhida, siga as [seguintes instruções](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="login-failures-errorid-95519-95520-95521-95522"></a>Falhas de login (ErrorID: 95519, 95520, 95521, 95522)

Esta secção descreve mensagens de erro de credencial e login.

### <a name="credentials-of-the-user-account-have-been-disabled-errorid-95519"></a>As credenciais da conta de utilizador foram desativadas (ErrorID: 95519)

A conta de utilizador escolhida durante a replicação do ativação foi desativada. Para ativar a conta de utilizador, consulte [este artigo](/powershell/module/Microsoft.PowerShell.LocalAccounts/Enable-LocalUser) ou execute o seguinte comando substituindo o nome de _utilizador_ de texto pelo nome de utilizador real.

`net user 'username' /active:yes`

### <a name="credentials-locked-out-because-of-multiple-failed-login-attempts-errorid-95520"></a>Credenciais bloqueadas por causa de múltiplas tentativas falhadas de login (ErrorID: 95520)

Vários esforços de retry falhados para aceder a uma máquina bloquearão a conta do utilizador. A falha pode dever-se a:

* As credenciais fornecidas durante a configuração da configuração estão incorretas.
* A conta de utilizador escolhida durante a replicação ativa está errada.

Modifique as credenciais escolhidas seguindo [estas instruções](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation) e tente novamente a operação.

### <a name="logon-servers-arent-available-on-the-source-machine-errorid-95521"></a>Os servidores de logon não estão disponíveis na máquina de origem (ErrorID: 95521)

Este erro ocorre quando os servidores de logon não estão disponíveis na máquina de origem. Se os servidores de logon não estiverem disponíveis, os pedidos de login falharão e o agente de Mobilidade não poderá ser instalado. Para um login bem sucedido, certifique-se de que os servidores de logon estão disponíveis na máquina de origem e inicie o serviço Netlogon. Para mais informações, consulte cenários de início de [sessão do Windows](/windows-server/security/windows-authentication/windows-logon-scenarios).

### <a name="logon-service-isnt-running-on-the-source-machine-errorid-95522"></a>O serviço de logon não está a funcionar na máquina de origem (ErrorID: 95522)

O serviço de login não está a funcionar na sua máquina de origem e causou falha no pedido de login. O agente de mobilidade não pode ser instalado. Para resolver o erro, utilize um dos `Netlogon` seguintes métodos para iniciar o serviço na máquina de origem:

* Para iniciar `Netlogon` o serviço a partir `net start Netlogon`de um pedido de comando, executar o comando .
* Do Gestor de `Netlogon` Tarefas, inicie o serviço.

## <a name="connectivity-failure-errorid-95117--97118"></a>Falha de conectividade (ErrorID: 95117 & 97118)

O servidor de processo de configuração/scale-out tenta ligar-se ao VM de origem para instalar o agente mobility. Este erro ocorre quando a máquina de origem não é alcançável porque existem problemas de conectividade da rede.

Para resolver o erro:

* Certifique-se de que pode ligar a sua máquina de origem a partir do servidor de configuração. Se escolheu o servidor de processo sem escala durante a replicação ativa, certifique-se de que pode ligar a sua máquina de origem a partir do servidor de processos.

* A partir da linha de `Telnet` comando da máquina do servidor de fonte, utilize para pingar o servidor de configuração ou o servidor de processo seleto na porta HTTPS 135, como mostrado no comando seguinte. Este comando verifica se existem problemas de conectividade de rede ou problemas de bloqueio de portas de firewall.

  `telnet <CS/ scale-out PS IP address> <135>`

* Adicionalmente, para um VM Linux:
  * Verifique se estão instalados os mais recentes pacotes OpenSSH, OpenSSH e OpenSSL.
  * Verifique e certifique-se de que a Secure Shell (SSH) está ativada e está a funcionar na porta 22.
  * Os serviços SFTP devem estar a funcionar. Para ativar o subsistema SFTP e a autenticação de palavra-passe no ficheiro _sshd_config:_

    1. Inicie sessão como **raiz**.
    1. Vá ao ficheiro _/etc/ssh/sshd_config,_ encontre `PasswordAuthentication`a linha que começa com .
    1. Descodere a linha `yes`e altere o valor para .
    1. Encontre a linha `Subsystem`que começa com , e descomente a linha
    1. Reinicie `sshd` o serviço.

* Uma tentativa de ligação poderia ter falhado se não existissem respostas adequadas após um período de tempo, ou uma ligação estabelecida falhou porque um hospedeiro conectado não respondeu.
* Pode ser um problema relacionado com conectividade/rede/domínio. Pode também ser devido ao problema da resolução do nome DNS ou da questão da exaustão portuário tCP. Verifique se existem problemas tão conhecidos no seu domínio.

## <a name="connectivity-failure-errorid-95523"></a>Falha de conectividade (ErrorID: 95523)

Este erro ocorre quando a rede que a máquina de origem reside não é encontrada, pode ter sido eliminada ou já não está disponível. A única forma de resolver o erro é garantir a existência da rede.

## <a name="file-and-printer-sharing-services-check-errorid-95105--95106"></a>Verificação de serviços de partilha de ficheiros e impressoras (ErrorID: 95105 & 95106)

Depois de uma verificação de conectividade, verifique se o serviço de partilha de ficheiros e impressoras está ativado na sua máquina virtual. Estas definições são necessárias para copiar o agente de Mobilidade para a máquina de origem.

Para **windows 2008 R2 e versões anteriores:**

* Para permitir a partilha de ficheiros e impressões através do Windows Firewall,
  1. Sistema de **painel** > de controlo aberto e**firewall do Windows**de**segurança** > . No painel esquerdo, selecione **Definições** > avançadas**Regras de entrada** na árvore da consola.
  1. Localizar regras De Partilha de Ficheiros e Impressoras (NB-Session-In) e Partilha de Ficheiros e Impressoras (SMB-In).
  1. Para cada regra, clique na regra e clique em **Regra ativação**.

* Para permitir a partilha de ficheiros com a Política de Grupo:
  1. Ir para **Iniciar,** escrever `gpmc.msc` e procurar.
  1. No painel de navegação, abra as seguintes pastas: **Configuração** > de configuração**de modelos administrativos** > de configuração do utilizador de**configuração** > do utilizador local Dos**modelos de imagem windows** > **Components**.
  1. No painel de detalhes, clique em dois **cliques Evite que os utilizadores partilhem ficheiros dentro do seu perfil**.

     Para desativar a definição de Política de Grupo e ativar a capacidade do utilizador de partilhar ficheiros, selecione **Disabled**.

  1. Selecione **OK** para guardar as alterações.

  Para saber mais, consulte [Enable ou desative](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754359(v=ws.10))a partilha de ficheiros com a Política de Grupo .

Para permitir a partilha de ficheiros e impressoras para versões posteriores do Windows ou do Linux, siga as instruções em [Instalar o serviço de Mobilidade para recuperação de desastres de VMware VMs e servidores físicos](vmware-azure-install-mobility-service.md) .

## <a name="windows-management-instrumentation-wmi-configuration-check-error-code-95103"></a>Verificação de configuração de configuração de instrumentação de gestão do Windows (WMI) (código de erro: 95103)

Depois de verificar os serviços de ficheiros e impressoras, ative o serviço WMI para perfis privados, públicos e de domínio através da firewall. Estas definições são necessárias para completar a execução remota na máquina de origem.

Para ativar o WMI:

1. Vá à**Segurança** **do Painel** > de Controlo e selecione Firewall do **Windows**.
1. Selecione **Definições de alteração** e, em seguida, selecione o separador **Exceções.**
1. Na janela **Exceções,** selecione a caixa de verificação para instrumentação de gestão do Windows (WMI) para ativar o tráfego de WMI através da firewall.

Também pode ativar o tráfego de WMI através da firewall a partir do pedido de comando com o seguinte comando:

`netsh advfirewall firewall set rule group="windows management instrumentation (wmi)" new enable=yes`

Outros artigos de resolução de problemas da OMI podem ser encontrados nos seguintes artigos.

* [Testes básicos de OMI](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/)
* [Resolução de problemas da WMI](/windows/win32/wmisdk/wmi-troubleshooting)
* [Problemas de resolução de problemas com scripts WMI e serviços WMI](/previous-versions/tn-archive/ff406382(v=msdn.10))

## <a name="unsupported-operating-systems"></a>Sistemas operativos não suportados

Outra razão comum para a falha pode ser devido a um sistema operativo não suportado. Utilize um sistema operativo suportado e uma versão kernel para uma instalação bem sucedida do serviço mobility. Evite o uso de patches privados.

Para visualizar a lista de sistemas operativos e versões kernel suportadas pela Recuperação do Site Azure, consulte o [documento da matriz](vmware-physical-azure-support-matrix.md#replicated-machines)de suporte .

## <a name="unsupported-boot-disk-configurations-errorid-95309-95310-95311"></a>Configurações de disco de arranque não suportadas (ErrorID: 95309, 95310, 95311)

### <a name="boot-and-system-partitions--volumes-arent-the-same-disk-errorid-95309"></a>Partições/volumes de arranque e sistema não são o mesmo disco (ErrorID: 95309)

Antes da versão 9.20, as divisórias/volumes do sistema em diferentes discos eram uma configuração não suportada. Começando com a [versão 9.20,](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)esta configuração é suportada.

### <a name="the-boot-disk-isnt-available-errorid-95310"></a>O disco de arranque não está disponível (ErrorID: 95310)

Uma máquina virtual sem um disco de arranque não pode ser protegida. Um disco de arranque garante uma recuperação suave de uma máquina virtual durante uma operação de failover. A ausência de um disco de arranque resulta numa falha no arranque da máquina após a falha. Certifique-se de que a máquina virtual contém um disco de arranque e tente novamente a operação. Além disso, vários discos de boot na mesma máquina não são suportados.

### <a name="multiple-boot-disks-present-on-the-source-machine-errorid-95311"></a>Vários discos boot presentes na máquina de origem (ErrorID: 95311)

Uma máquina virtual com vários discos de arranque não é uma [configuração suportada.](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)

## <a name="system-partition-on-multiple-disks-errorid-95313"></a>Partição do sistema em vários discos (ErrorID: 95313)

Antes da versão 9.20, uma divisão de raiz ou configuração de volume em vários discos era uma configuração não suportada. Começando com a [versão 9.20,](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)esta configuração é suportada.

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320"></a>Ativar a proteção falhou como nome do dispositivo mencionado na configuração GRUB em vez de UUID (ErrorID: 95320)

### <a name="possible-cause"></a>Causa Possível

Os ficheiros de configuração do Grande Bootloader Unificado (GRUB)_(/boot/grub/menu.lst,_ _/boot/grub/grub.cfg,_ _/boot/grub2/grub.cfg,_ ou _/etc/predefinido/grub)_ podem conter o valor para a **raiz** dos parâmetros e **retomar** como nomes reais do dispositivo em vez de um identificador universalmente único (UUID). A Recuperação do Site determina a abordagem UUID, uma vez que os nomes do dispositivo podem mudar através do reboot do VM. Por exemplo, o VM pode não estar online com o mesmo nome no failover e isso resulta em problemas.

Por exemplo:

- A seguinte linha é do ficheiro GRUB _/boot/grub2/grub.cfg:_

  `linux /boot/vmlinuz-3.12.49-11-default root=/dev/sda2  ${extra_cmdline} resume=/dev/sda1 splash=silent quiet showopts`

- A seguinte linha é do ficheiro GRUB _/boot/grub/menu.lst:_

  `kernel /boot/vmlinuz-3.0.101-63-default root=/dev/sda2 resume=/dev/sda1 splash=silent crashkernel=256M-:128M showopts vga=0x314`

> [!NOTE]
> As linhas GRUB contêm nomes reais do dispositivo para a **raiz** e **currículo** dos parâmetros em vez do UUID.

### <a name="how-to-fix"></a>Como Corrigir

Os nomes dos dispositivos devem ser substituídos pelo UUID correspondente.

1. Encontre o UUID do dispositivo executando o comando `blkid \<device name>`.

   Por exemplo:

   ```shell
   blkid /dev/sda1
   /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
   blkid /dev/sda2
   /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```

1. Agora substitua o nome do dispositivo pelo `root=UUID=\<UUID>`seu UUID no formato como . Por exemplo, se substituirmos os nomes do dispositivo por UUID para o parâmetro raiz e retoma mencionado nos ficheiros _/boot/grub2/grub.cfg,_ _/boot/grub2/grub.cfg,_ ou _/etc/predefinido/grub_ então as linhas nos ficheiros parecem a seguinte linha:

   `kernel /boot/vmlinuz-3.0.101-63-default root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4 resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b splash=silent crashkernel=256M-:128M showopts vga=0x314`

1. Reinicie a proteção.

## <a name="install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266"></a>Instalar serviço de mobilidade concluído com aviso para reiniciar (ErrorID: 95265 & 95266)

O serviço de Mobilidade de Recuperação do Site tem muitos componentes, um dos quais é chamado de condutor de filtros. O controlador de filtro é carregado na memória do sistema apenas durante uma reinicialização do sistema. As correções do controlador de filtro só podem ser realizadas quando um novo controlador de filtro é carregado no momento do reboot do sistema.

> [!IMPORTANT]
> Este é um aviso e a replicação existente funcionará mesmo após a atualização do novo agente. Pode optar por reiniciar sempre que quiser obter os benefícios do novo condutor de filtro, mas se não reiniciar, o antigo condutor do filtro continua a trabalhar. Assim, após uma atualização sem reinicialização, com exceção do condutor do filtro, **os benefícios de outras melhorias e correções no serviço de Mobilidade são realizados**. Embora recomendado, não é obrigatório reiniciar após cada upgrade. Para obter informações sobre quando um reboot é obrigatório, detete a secção de atualização do [serviço reboot após mobilidade](service-updates-how-to.md#reboot-after-mobility-service-upgrade) nas atualizações de Serviço em Recuperação do Site Azure.

> [!TIP]
>Para obter as melhores práticas de agendamento de upgrades durante a sua janela de manutenção, consulte o Suporte para as atualizações mais recentes do [sistema operativo/kernel](service-updates-how-to.md#support-for-latest-operating-systemskernels) em atualizações de Serviço em Recuperação do Site Azure.

## <a name="lvm-support-from-920-version"></a>Suporte LVM a partir da versão 9.20

Antes da versão 9.20, o Logical Volume Manager (LVM) foi suportado apenas para discos de dados. A `/boot` partição deve estar numa divisória de disco e não num volume DELvM.

Começando com a [versão 9.20,](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)o [disco OS em LVM](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) é suportado.

## <a name="insufficient-space-errorid-95524"></a>Espaço insuficiente (ErrorID: 95524)

Quando o agente de Mobilidade é copiado para a máquina de origem, é necessário pelo menos 100 MB de espaço livre. Certifique-se de que a sua máquina de origem tem a quantidade necessária de espaço livre e que tente novamente o funcionamento.

## <a name="vss-installation-failures"></a>Falhas de instalação VSS

A instalação do Serviço de Cópia seletiva De Volume Sombra (VSS) faz parte da instalação do agente de mobilidade. Este serviço é utilizado no processo para gerar pontos de recuperação consistentes da aplicação. Falhas durante a instalação VSS podem ocorrer devido a múltiplas razões. Para identificar os erros exatos, consulte _C:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log_. Alguns dos erros comuns e os passos de resolução são destacados na secção seguinte.

### <a name="vss-error--2147023170-0x800706be---exit-code-511"></a>Erro VSS -2147023170 [0x800706BE] - código de saída 511

Este problema é mais frequentemente visto quando o software antivírus está bloqueando as operações dos serviços de recuperação de sites do Azure.

Para resolver este problema:

1. Reveja a lista de exclusões de [pastas do programa Antivírus](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program).
1. Siga as diretrizes publicadas pelo seu fornecedor antivírus para desbloquear o registo de DLL no Windows.

### <a name="vss-error-7-0x7---exit-code-511"></a>Erro VSS 7 [0x7] - código de saída 511

Este erro é um erro de tempo de execução que é causado porque não há memória suficiente para instalar VSS. Aumente o espaço do disco para a conclusão bem sucedida desta operação.

### <a name="vss-error--2147023824-0x80070430---exit-code-517"></a>Erro VSS -2147023824 [0x80070430] - código de saída 517

Este erro ocorre quando o serviço de fornecedor VSS de recuperação do site Azure está [marcado para eliminação](/previous-versions/ms838153(v=msdn.10)). Tente instalar o VSS manualmente na máquina de origem, executando o seguinte comando:

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

### <a name="vss-error--2147023841-0x8007041f---exit-code-512"></a>Erro VSS -2147023841 [0x8007041F] - código de saída 512

Este erro ocorre quando a base de dados de serviço saciável do fornecedor de recuperação do site Azure VSS está [bloqueada](/previous-versions/ms833798(v=msdn.10)). Tente instalar o VSS manualmente na máquina de origem, executando o seguinte comando a partir de um pedido de comando:

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

Quando houver uma falha, verifique se algum programa antivírus ou outros serviços estão presos num estado **de partida.** Um processo em estado **de arranque** pode manter o bloqueio nos serviços de base de dados. Isto provocará falhas na instalação do fornecedor VSS. Certifique-se de que nenhum serviço está em estado **de partida** e, em seguida, tente novamente a operação acima referida.

### <a name="vss-exit-code-806"></a>Código de saída VSS 806

Este erro ocorre quando a conta de utilizador utilizada para `CSScript` a instalação não tem permissões para executar o comando. Forneça as permissões necessárias à conta do utilizador para executar o script e voltar a tentar a operação.

### <a name="other-vss-errors"></a>Outros erros VSS

Tente instalar manualmente o serviço de fornecedor VSS na máquina de origem, executando o seguinte comando a partir de um pedido de comando:

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

## <a name="vss-error---0x8004e00f"></a>Erro VSS - 0x8004E00F

Este erro ocorre tipicamente durante a instalação do `DCOM` agente `DCOM` de Mobilidade devido a problemas e encontra-se em estado crítico.

Utilize o seguinte procedimento para determinar a causa do erro.

### <a name="examine-the-installation-logs"></a>Examinar os registos de instalação

1. Abra o registo de instalação localizado em _C:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log_.
2. A presença do seguinte erro indica este problema:

    ```Output
    Unregistering the existing application...
    Create the catalogue object
    Get the collection of Applications

    ERROR:

    - Error code: -2147164145 [0x8004E00F]
    - Exit code: 802
    ```

Para resolver a questão:

Contacte a equipa da [plataforma Microsoft Windows](https://aka.ms/Windows_Support) para obter assistência na resolução do problema do DCOM.

Quando o problema da DCOM estiver resolvido, reinstale manualmente o Fornecedor VSS de Recuperação do Local Azure utilizando manualmente o seguinte comando a partir de um pedido de comando:

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

Se a consistência da aplicação não for crítica para os seus requisitos de recuperação de desastres, pode contornar a instalação do VsS Provider.

Para contornar a instalação do Fornecedor VSS de Recuperação do Site Azure e instalar manualmente a instalação do post do Fornecedor VSS de Recuperação do Local Do Azure:

1. Instale o serviço de Mobilidade. A instalação falhará no degrau: **Configuração de instalação de postes**.
1. Para contornar a instalação VSS:
   1. Abra o diretório de instalação do Serviço de Recuperação de Mobilidade do Sítio Azure localizado em:

      _C:\Program Files (x86)\Microsoft Azure Site Recovery\agent_

   1. Modifique as scripts de instalação vss de recuperação do site Azure _InMageVSSProvider_Install_ e _InMageVSSProvider_Uninstall.cmd_ para sempre ter sucesso adicionando as seguintes linhas:

      ```plaintext
      rem @echo off
      setlocal
      exit /B 0
      ```

1. Faça uma instalação manual do Agente de Mobilidade.
1. Quando a instalação tiver sucesso e avançar para o próximo passo, **Configure,** retire as linhas que adicionou.
1. Para instalar o fornecedor VSS, abra um pedido de comando como administrador e execute o seguinte comando:

   `"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

1. Verifique se o Fornecedor VSS de Recuperação do Site Azure está instalado como um serviço nos Serviços Windows. Abra o MMC do Serviço de Componentes para confirmar que o Fornecedor VSS está listado.
1. Se a instalação do Fornecedor VSS continuar a falhar, trabalhe com suporte técnico para resolver os erros de permissões na Interface de Programação de Aplicações Criptográficas (CAPI2).

## <a name="vss-provider-installation-fails-because-the-cluster-service-being-enabled-on-non-cluster-machine"></a>A instalação do Fornecedor VSS falha porque o serviço de cluster que está a ser ativado na máquina não cluster

Este problema faz com que a instalação do Agente de Mobilidade de Recuperação do Sítio Azure falhe durante a instalação do Fornecedor VSS de Recuperação do Local Azure. A falha deve-se ao `COM+` facto de existir um problema que impede a instalação do fornecedor VSS.

### <a name="to-identify-the-issue"></a>Para identificar a questão

No registo localizado no servidor de configuração em _C:\ProgramData\ASRSetupLogs\UploadedLogs\<data-data>UA_InstallLogFile.log_ encontrará a seguinte exceção:

```plaintext
COM+ was unable to talk to the Microsoft Distributed Transaction Coordinator (Exception from HRESULT: 0x8004E00F)
```

Para resolver a questão:

1. Verifique se esta máquina é uma máquina não-cluster e se os componentes do cluster não estão a ser utilizados.
1. Se os componentes não estiverem a ser utilizados, retire os componentes do cluster da máquina.

## <a name="drivers-are-missing-on-the-source-server"></a>Os condutores estão desaparecidos no servidor de origem.

Se a instalação do Agente de Mobilidade falhar, examine os registos em _C:\ProgramData\ASRSetupLogs_ para determinar se alguns dos controladores necessários estão em falta em alguns conjuntos de controlo.

Para resolver a questão:

1. Utilizando um editor de `regedit.msc`registo como, abrir o registo.
1. Abra `HKEY_LOCAL_MACHINE\SYSTEM` o nó.
1. No `SYSTEM` nó, localize os conjuntos de controlo.
1. Abra cada conjunto de controlo e verifique se os controladores Windows estão presentes:

   * Atapi
   * Vmbus
   * Storflt
   * Storvsc
   * Intelide

1. Reinstale os controladores desaparecidos.

## <a name="next-steps"></a>Passos seguintes

[Saiba mais](vmware-azure-tutorial.md) sobre como configurar a recuperação de desastres para VMware VMs.
