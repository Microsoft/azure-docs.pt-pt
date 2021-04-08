---
title: Instalação de push do Serviço de Mobilidade de Resolução de Problemas com Recuperação do Local de Azure
description: Resolução de problemas Serviços de Mobilidade Erros de instalação ao permitir a replicação para recuperação de desastres com a Recuperação do Local Azure.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.author: ramamill
ms.date: 04/03/2020
ms.openlocfilehash: 62c8240a4d2e50aa3b584f322baf7d2ee217c6d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98127877"
---
# <a name="troubleshoot-mobility-service-push-installation"></a>Instalação push do serviço de mobilidade de resolução de problemas

A instalação do serviço mobility é um passo fundamental para permitir a replicação. O sucesso deste passo depende do cumprimento dos pré-requisitos e do trabalho com configurações apoiadas. As falhas mais comuns que poderá enfrentar durante a instalação do serviço mobility devem-se a:

* [Erros de credencial/privilégio](#credentials-check-errorid-95107--95108)
* [Falhas de login](#login-failures-errorid-95519-95520-95521-95522)
* [Erros de conectividade](#connectivity-failure-errorid-95117--97118)
* [Erros de partilha de ficheiros e impressoras](#file-and-printer-sharing-services-check-errorid-95105--95106)
* [Falhas na Instrumentação de Gestão do Windows (IMI)](#windows-management-instrumentation-wmi-configuration-check-error-code-95103)
* [Sistemas operativos não suportados](#unsupported-operating-systems)
* [Configurações de arranque não suportadas](#unsupported-boot-disk-configurations-errorid-95309-95310-95311)
* [Falhas de instalação do Serviço de Cópias de Volume Shadow (VSS)](#vss-installation-failures)
* [Nome do dispositivo na configuração GRUB em vez do dispositivo UUID](#enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320)
* [Volume lógico do Volume (LVM)](#lvm-support-from-920-version)
* [Avisos de reinicialização](#install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266)

Quando ativa a replicação, a Azure Site Recovery tenta instalar o agente de serviço de mobilidade na sua máquina virtual (VM). Como parte deste processo, o servidor de configuração tenta ligar-se à máquina virtual e copiar o agente. Para ativar uma instalação bem sucedida, siga a orientação passo-a-passo de resolução de problemas.

## <a name="credentials-check-errorid-95107--95108"></a>Verificação de credenciais (ErrorID: 95107 & 95108)

Verifique se a conta de utilizador escolhida durante a replicação ativa é válida e precisa. A Azure Site Recovery requer a conta **raiz** ou conta de utilizador com **privilégios de administrador** para realizar uma instalação push. Caso contrário, a instalação de impulsos será bloqueada na máquina de origem.

Para o Windows **(erro 95107),** verifique se a conta de utilizador tem acesso administrativo no computador de origem, quer com uma conta local quer com uma conta de domínio. Se não estiver a utilizar uma conta de domínio, tem de desativar o controlo de Acesso ao Utilizador Remoto no computador local.

* Para adicionar manualmente uma chave de registo que desative o controlo de acesso ao utilizador remoto:

  * `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System`
  * Adicione um `DWORD` novo: `LocalAccountTokenFilterPolicy`
  * Definir o valor para `1`

* Para adicionar a chave de registo, a partir de um pedido de comando, executar o seguinte comando:

  `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`

Para o Linux **(erro 95108),** deve escolher a conta **raiz** para uma instalação bem sucedida do agente de serviço de mobilidade. Além disso, os serviços do SSH File Transfer Protocol (SFTP) devem estar a ser geridos. Para ativar o subsistema SFTP e a autenticação de senha no ficheiro _sshd_config:_

1. Inicie sessão como **raiz**.
1. Vá para _/etc/ssh/sshd_config ficheiro,_ encontre a linha que começa com `PasswordAuthentication` .
1. Descomprimir a linha, e alterar o valor para `yes` .
1. Encontre a linha que começa `Subsystem` com, e descompromeça a linha.
1. Reinicie o `sshd` serviço.

Se desejar modificar as credenciais da conta de utilizador escolhida, siga [estas instruções](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="insufficient-privileges-failure-errorid-95517"></a>Falha de privilégios insuficiente (ErrorID: 95517)

Quando o utilizador escolhido para instalar o agente Mobility não tiver privilégios de administrador, o servidor de processo de configuração/escala não será autorizado a copiar o software do agente mobility para obter a máquina. Este erro é o resultado de uma falha de acesso negada. Certifique-se de que a conta de utilizador tem privilégios de administrador.

Se desejar modificar as credenciais da conta de utilizador escolhida, siga [estas instruções](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="insufficient-privileges-failure-errorid-95518"></a>Falha de privilégios insuficiente (ErrorID: 95518)

Quando o estabelecimento de relação de confiança de domínio entre o domínio primário e a estação de trabalho falha enquanto tenta entrar na máquina de origem, a instalação do agente de mobilidade falha com o erro ID 95518. Certifique-se de que a conta de utilizador utilizada para instalar o agente mobility tem privilégios administrativos para iniciar scontabilidade através do domínio primário da máquina de origem.

Se desejar modificar as credenciais da conta de utilizador escolhida, siga [estas instruções](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="login-failures-errorid-95519-95520-95521-95522"></a>Falhas de login (ErrorID: 95519, 95520, 95521, 95522)

Esta secção descreve mensagens de erro credenciais e de login.

### <a name="credentials-of-the-user-account-have-been-disabled-errorid-95519"></a>As credenciais da conta de utilizador foram desativadas (ErrorID: 95519)

A conta de utilizador escolhida durante a replicação foi desativada. Para ativar a conta de utilizador, consulte [este artigo](/powershell/module/Microsoft.PowerShell.LocalAccounts/Enable-LocalUser) ou execute o seguinte comando substituindo o nome de _utilizador de_ texto pelo nome de utilizador real.

`net user 'username' /active:yes`

### <a name="credentials-locked-out-because-of-multiple-failed-login-attempts-errorid-95520"></a>Credenciais bloqueadas devido a múltiplas tentativas falhadas de login (ErrorID: 95520)

Vários esforços falhados para aceder a uma máquina bloqueiam a conta do utilizador. A falha pode dever-se a:

* As credenciais fornecidas durante a configuração da configuração estão incorretas.
* A conta de utilizador escolhida durante a replicação ativa está errada.

Modifique as credenciais escolhidas seguindo [estas instruções](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation) e relemque a operação.

### <a name="logon-servers-arent-available-on-the-source-machine-errorid-95521"></a>Os servidores logon não estão disponíveis na máquina de origem (ErrorID: 95521)

Este erro ocorre quando os servidores de início de são não estão disponíveis na máquina de origem. Se os servidores de início de sessão não estiverem disponíveis, os pedidos de login falharão e o agente de Mobilidade não poderá ser instalado. Para um login bem sucedido, certifique-se de que os servidores de início de sessão estão disponíveis na máquina de origem e inicie o serviço Netlogon. Para mais informações, consulte [os Cenários de Início de Sé.](/windows-server/security/windows-authentication/windows-logon-scenarios)

### <a name="logon-service-isnt-running-on-the-source-machine-errorid-95522"></a>O serviço de início de síl em funcionamento não está a funcionar na máquina de origem (ErrorID: 95522)

O serviço de login não está a funcionar na sua máquina de origem e causou falha no pedido de login. O agente da Mobilidade não pode ser instalado. Para resolver o erro, utilize um dos seguintes métodos para iniciar o `Netlogon` serviço na máquina de origem:

* Para iniciar o serviço a `Netlogon` partir de um pedido de comando, executar o comando `net start Netlogon` .
* A partir do Gestor de Tarefas, inicie o `Netlogon` serviço.

## <a name="connectivity-failure-errorid-95117--97118"></a>Falha de conectividade (ErrorID: 95117 & 97118)

O servidor de configuração/servidor de processo de escala tenta ligar-se à fonte VM para instalar o agente de Mobilidade. Este erro ocorre quando a máquina de origem não é alcançável porque existem problemas de conectividade de rede.

Para resolver o erro:

* Verifique se a conta de utilizador tem acesso administrativo no computador de origem, quer com uma conta local, quer com uma conta de domínio. Se não estiver a utilizar uma conta de domínio, tem de desativar o controlo de Acesso ao Utilizador Remoto no computador local.
  * Para adicionar manualmente uma chave de registo que desative o controlo de acesso ao utilizador remoto:
    * `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System`
    * Adicione um `DWORD` novo: `LocalAccountTokenFilterPolicy`
    * Definir o valor para `1`
  * Para adicionar a chave de registo, a partir de um pedido de comando, executar o seguinte comando:

    `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`

* Certifique-se de que consegue extrair a sua máquina de origem do servidor de configuração. Se tiver escolhido o servidor de processo de escala durante a replicação ativa, certifique-se de que pode tirar a sua máquina de origem do servidor de processos.

* Certifique-se de que o serviço de partilha de ficheiros e impressoras está ativado na sua máquina virtual. Confira os passos [aqui.](vmware-azure-troubleshoot-push-install.md#file-and-printer-sharing-services-check-errorid-95105--95106)

* Certifique-se de que o serviço WMI está ativado na sua máquina virtual. Confira os passos [aqui.](vmware-azure-troubleshoot-push-install.md#windows-management-instrumentation-wmi-configuration-check-error-code-95103)

* Certifique-se de que as pastas partilhadas na rede na sua máquina virtual estão acessíveis a partir do servidor de processos. Confira os passos [aqui.](vmware-azure-troubleshoot-push-install.md#check-access-for-network-shared-folders-on-source-machine-errorid-9510595523)

* A partir da linha de comando da máquina do servidor de origem, utilize `Telnet` para pingar o servidor de configuração ou o servidor de processo de escala na porta HTTPS 135, como mostrado no comando seguinte. Este comando verifica se existem problemas de conectividade de rede ou problemas de bloqueio de portas de firewall.

  `telnet <CS/ scale-out PS IP address> <135>`

* Adicionalmente, para um Linux VM:
  * Verifique se estão instalados os pacotes OpenSSH, OpenSSH e OpenSSL mais recentes.
  * Verifique e certifique-se de que a Secure Shell (SSH) está ativada e está a funcionar na porta 22.
  * Os serviços da SFTP devem estar a funcionar. Para ativar o subsistema SFTP e a autenticação de senha no ficheiro _sshd_config:_

    1. Inicie sessão como **raiz**.
    1. Vá para _/etc/ssh/sshd_config_ ficheiro, encontre a linha que começa com `PasswordAuthentication` .
    1. Descomprimir a linha, e alterar o valor para `yes` .
    1. Encontre a linha que começa `Subsystem` com, e descompromes a linha
    1. Reinicie o `sshd` serviço.

* Uma tentativa de ligação poderia ter falhado se não houver respostas adequadas após um período de tempo, ou uma ligação estabelecida falhou porque um hospedeiro ligado não respondeu.
* Pode ser uma questão de conectividade/rede/domínio. Pode também ser porque o nome DNS resolve a questão ou a questão da exaustão da porta TCP. Verifique se existem problemas tão conhecidos no seu domínio.

## <a name="connectivity-failure-errorid-95523"></a>Falha de conectividade (ErrorID: 95523)

Este erro ocorre quando a rede que a máquina de origem reside não é encontrada, pode ter sido eliminada ou já não está disponível. A única forma de resolver o erro é garantir a existência da rede.

## <a name="check-access-for-network-shared-folders-on-source-machine-errorid-9510595523"></a>Verifique o acesso a pastas partilhadas na rede na máquina de origem (ErrorID: 95105.95523)

Verifique se as pastas partilhadas na rede na sua máquina virtual estão acessíveis a partir do Process Server (PS) remotamente utilizando credenciais especificadas. Para confirmar o acesso: 

1. Inicie sessão na sua máquina processua Server.
2. Abra o Explorador de Ficheiros. Na barra de endereços, `\\<SOURCE-MACHINE-IP>\C$` escreva e clique em Entrar.

    ![Abrir pasta em PS](./media/vmware-azure-troubleshoot-push-install/open-folder-process-server.PNG)

3. O explorador de ficheiros pedirá credenciais. Introduza o nome de utilizador e a palavra-passe e clique em OK. <br><br/>

    ![Fornecer Credenciais](./media/vmware-azure-troubleshoot-push-install/provide-credentials.PNG)

    >[!NOTE]
    > Se a máquina de origem estiver unida ao domínio, forneça o nome de domínio juntamente com o nome de utilizador como `<domainName>\<username>` . Se a máquina de origem estiver no grupo de trabalho, forneça apenas o nome de utilizador.

4. Se a ligação for bem sucedida, as pastas da máquina de origem serão visíveis remotamente a partir do Process Server.

    ![Pastas visíveis da Máquina de Origem](./media/vmware-azure-troubleshoot-push-install/visible-folders-from-source.png)

Se a ligação não for bem sucedida, verifique se todos os requisitos prévios são cumpridos.

## <a name="file-and-printer-sharing-services-check-errorid-95105--95106"></a>Verificação dos serviços de partilha de ficheiros e impressoras (ErrorID: 95105 & 95106)

Após uma verificação de conectividade, verifique se o serviço de partilha de ficheiros e impressoras está ativado na sua máquina virtual. Estas definições são necessárias para copiar o agente mobility para a máquina de origem.

Para as **versões R2 e anteriores do Windows 2008:**

* Para ativar a partilha de ficheiros e impressões através do Windows Firewall,
  1. Sistema **de painel de controlo** aberto e firewall do Windows  >    >  **de** segurança . No painel esquerdo, selecione Regras de entrada **avançadas**  >   na árvore da consola.
  1. Localizar regras Partilha de Ficheiros e Impressoras (NB-Session-In) e Partilha de Ficheiros e Impressoras (SMB-In).
  1. Para cada regra, clique à direita na regra e, em seguida, clique em **Enable Rule**.

* Para ativar a partilha de ficheiros com a Política de Grupo:
  1. Vá para **iniciar,** escreva `gpmc.msc` e procure.
  1. No painel de navegação, abra as seguintes pastas: Configuração de modelos de configuração de modelos de configuração **do**  >    >  **utilizador**  >  **local, partilha**  >  **de componentes** do Windows .
  1. No painel de detalhes, clique duas **vezes Para evitar que os utilizadores partilhem ficheiros dentro do seu perfil**.

     Para desativar a definição de Política de Grupo e ativar a capacidade do utilizador de partilhar ficheiros, selecione **Disabled**.

  1. Selecione **OK** para guardar as alterações.

  Para saber mais, consulte [Ativar ou desativar a partilha de ficheiros com a Política de Grupo](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754359(v=ws.10)).

Para ativar a partilha de ficheiros e impressoras para versões posteriores do Windows ou Linux, siga as instruções na [Instalação do serviço de Mobilidade para a recuperação de VMware vMware e servidores físicos](vmware-azure-install-mobility-service.md) .

## <a name="windows-management-instrumentation-wmi-configuration-check-error-code-95103"></a>Verificação de configuração da Instrumentação de Gestão do Windows (WMI) (Código de erro: 95103)

Após a verificação dos serviços de ficheiros e impressoras, ative o serviço WMI para perfis privados, públicos e de domínio através da firewall. Estas definições são necessárias para completar a execução remota na máquina de origem.

Para ativar o WMI:

1. Vá à **Segurança do Painel de**  >  **Controlo** e selecione **Firewall do Windows**.
1. Selecione **'Alterar Definições'** e, em seguida, selecione o separador **Exceções.**
1. Na janela **Exceções,** selecione a caixa de verificação para instrumentação de gestão do Windows (WMI) para permitir o tráfego de WMI através da firewall.

Também pode ativar o tráfego de WMI através da firewall a partir da pronta de comando com o seguinte comando:

`netsh advfirewall firewall set rule group="windows management instrumentation (wmi)" new enable=yes`

Outros artigos de resolução de problemas do WMI podem ser encontrados nos seguintes artigos.

* [Teste básico de WMI](https://techcommunity.microsoft.com/t5/ask-the-performance-team/bg-p/AskPerf)
* [Resolução de problemas do WMI](/windows/win32/wmisdk/wmi-troubleshooting)
* [Problemas de resolução de problemas com scripts WMI e serviços de WMI](/previous-versions/tn-archive/ff406382(v=msdn.10))

## <a name="unsupported-operating-systems"></a>Sistemas operativos não suportados

Outra razão comum para a falha pode ser devido a um sistema operativo não suportado. Utilize um sistema operativo suportado e uma versão kernel para uma instalação bem sucedida do serviço Mobility. Evite a utilização de patches privados.

Para visualizar a lista de sistemas operativos e versões kernel suportadas pela Azure Site Recovery, consulte o [documento matriz](vmware-physical-azure-support-matrix.md#replicated-machines)de suporte .

## <a name="unsupported-boot-disk-configurations-errorid-95309-95310-95311"></a>Configurações de disco de arranque não suportadas (ErrorID: 95309, 95310, 95311)

### <a name="boot-and-system-partitions--volumes-arent-the-same-disk-errorid-95309"></a>As divisórias/volumes de arranque e sistema não são o mesmo disco (ErrorID: 95309)

Antes da versão 9.20, as divisórias/volumes de arranque e sistema em diferentes discos eram uma configuração não suportada. Começando pela [versão 9.20,](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)esta configuração é suportada.

### <a name="the-boot-disk-isnt-available-errorid-95310"></a>O disco de arranque não está disponível (ErrorID: 95310)

Uma máquina virtual sem um disco de arranque não pode ser protegida. Um disco de arranque garante uma recuperação suave de uma máquina virtual durante uma operação de falha. A ausência de um disco de arranque resulta numa falha no arranque da máquina após a falha. Certifique-se de que a máquina virtual contém um disco de arranque e relemque o funcionamento. Além disso, vários discos de arranque na mesma máquina não são suportados.

### <a name="multiple-boot-disks-present-on-the-source-machine-errorid-95311"></a>Vários discos de arranque presentes na máquina de origem (ErrorID: 95311)

Uma máquina virtual com vários discos de arranque não é uma [configuração suportada](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage).

## <a name="system-partition-on-multiple-disks-errorid-95313"></a>Partição do sistema em vários discos (ErrorID: 95313)

Antes da versão 9.20, uma divisão raiz ou configuração de volume em vários discos era uma configuração não suportada. Começando pela [versão 9.20,](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)esta configuração é suportada.

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320"></a>Ativar a proteção falhada como nome do dispositivo mencionado na configuração GRUB em vez de UUID (ErrorID: 95320)

### <a name="possible-cause"></a>Causa Possível

Os ficheiros de configuração do Grande Bootloader Unificado (GRUB)_(/boot/grub/menu.lst_, _/boot/grub/grub.cfg_, _/boot/grub2/grub.cfg_, ou _/etc/default/grub_) podem conter o valor para a **raiz** dos parâmetros e **retomar** como o nome do dispositivo real em vez de um identificador universalmente único (UUID). A Recuperação do Site determina a abordagem UUID, uma vez que os nomes do dispositivo podem mudar através do reboot do VM. Por exemplo, o VM pode não estar online com o mesmo nome no failover e isso resulta em problemas.

Por exemplo:

- A seguinte linha é do ficheiro GRUB _/boot/grub2/grub.cfg_:

  `linux /boot/vmlinuz-3.12.49-11-default root=/dev/sda2  ${extra_cmdline} resume=/dev/sda1 splash=silent quiet showopts`

- A seguinte linha é do ficheiro GRUB _/boot/grub/menu.lst_:

  `kernel /boot/vmlinuz-3.0.101-63-default root=/dev/sda2 resume=/dev/sda1 splash=silent crashkernel=256M-:128M showopts vga=0x314`

> [!NOTE]
> As linhas GRUB contêm nomes reais do dispositivo para a **raiz** e **o currículo** dos parâmetros em vez do UUID.

### <a name="how-to-fix"></a>Como Corrigir

Os nomes dos dispositivos devem ser substituídos pelo UUID correspondente.

1. Encontre o UUID do dispositivo executando o comando `blkid \<device name>` .

   Por exemplo:

   ```shell
   blkid /dev/sda1
   /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
   blkid /dev/sda2
   /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```

1. Agora substitua o nome do dispositivo pelo seu UUID no formato como `root=UUID=\<UUID>` . Por exemplo, se substituirmos os nomes do dispositivo por UUID por raiz e retomar o parâmetro mencionado nos ficheiros _/boot/grub2/grub.cfg_, _/boot/grub2/grub.cfg_, ou _/etc/default/grub,_ então as linhas nos ficheiros parecem a seguinte linha:

   `kernel /boot/vmlinuz-3.0.101-63-default root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4 resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b splash=silent crashkernel=256M-:128M showopts vga=0x314`

1. Reinicie a proteção.

## <a name="install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266"></a>Serviço de Mobilidade instalada concluído com aviso para reiniciar (ErrorID: 95265 & 95266)

O serviço de Mobilidade de Recuperação do Local tem muitos componentes, um dos quais é chamado de controlador de filtros. O controlador do filtro é carregado na memória do sistema apenas durante um reboot do sistema. As correções do controlador do filtro só podem ser realizadas quando um novo controlador de filtro é carregado no momento de um reboot do sistema.

> [!IMPORTANT]
> Este é um aviso e a replicação existente funcionará mesmo após a nova atualização do agente. Pode optar por reiniciar sempre que quiser obter os benefícios do novo controlador de filtros, mas se não reiniciar, o antigo controlador de filtros continua a funcionar. Assim, depois de uma atualização sem reinicialização, com exceção do controlador de filtros, **os benefícios de outras melhorias e correções no serviço de Mobilidade são realizados**. Apesar de recomendado, não é obrigatório reiniciar após cada upgrade. Para obter informações sobre quando um reboot é obrigatório, desenverda a secção de atualização do [serviço Desemesto após Mobilidade](service-updates-how-to.md#reboot-after-mobility-service-upgrade) em atualizações de Serviço na Recuperação do Site Azure.

> [!TIP]
>Para obter as melhores práticas sobre o agendamento de atualizações durante a sua janela de manutenção, consulte o [Suporte para as atualizações mais recentes do sistema operativo/kernel](service-updates-how-to.md#support-for-latest-operating-systemskernels) em atualizações de Serviço na Recuperação do Local de Azure.

## <a name="lvm-support-from-920-version"></a>Suporte LVM a partir da versão 9.20

Antes da versão 9.20, o Logical Volume Manager (LVM) era suportado apenas para discos de dados. A `/boot` partição deve estar numa partição de disco e não num volume LVM.

A partir da [versão 9.20,](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)o [disco OS na LVM](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) é suportado.

## <a name="insufficient-space-errorid-95524"></a>Espaço insuficiente (ErrorID: 95524)

Quando o agente de mobilidade é copiado para a máquina de origem, é necessário pelo menos 100 MB de espaço livre. Certifique-se de que a sua máquina de origem tem a quantidade necessária de espaço livre e relemque o funcionamento.

## <a name="low-system-resources"></a>Baixos recursos do sistema

Os possíveis IDs de erro vistos para esta edição são 95572 e 95573. Este problema ocorre quando o sistema tem pouca memória disponível, e não é capaz de alocar memória para instalação de serviço de mobilidade. Certifique-se de que a memória foi libertada para que a instalação prossiga e complete com sucesso.

## <a name="vss-installation-failures"></a>Falhas de instalação vss

A instalação do Serviço de Cópias Volume Shadow (VSS) faz parte da instalação do agente mobility. Este serviço é utilizado no processo para gerar pontos de recuperação consistentes de aplicação. Falhas durante a instalação VSS podem ocorrer devido a múltiplas razões. Para identificar os erros exatos, consulte _c:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log_. Alguns dos erros comuns e as medidas de resolução são salientados na secção seguinte.

### <a name="vss-error--2147023170-0x800706be---exit-code-511"></a>Erro VSS -2147023170 [0x800706BE] - código de saída 511

Este problema é visto mais frequentemente quando o software antivírus está bloqueando as operações dos serviços de Recuperação do Site Azure.

Para resolver este problema:

1. Reveja a lista de [exclusões de pastas do programa Antivírus](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program).
1. Siga as diretrizes publicadas pelo seu fornecedor antivírus para desbloquear o registo de DLL no Windows.

### <a name="vss-error-7-0x7---exit-code-511"></a>VSS error 7 [0x7] - código de saída 511

Este erro é um erro de tempo de execução que é causado porque não há memória suficiente para instalar VSS. Aumente o espaço do disco para a conclusão com sucesso desta operação.

### <a name="vss-error--2147023824-0x80070430---exit-code-517"></a>Erro VSS -2147023824 [0x80070430] - código de saída 517

Este erro ocorre quando o serviço de recuperação do site Azure VSS Provider está [marcado para eliminação](/previous-versions/ms838153(v=msdn.10)). Tente instalar o VSS manualmente na máquina de origem executando o seguinte comando:

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

### <a name="vss-error--2147023841-0x8007041f---exit-code-512"></a>Erro VSS -2147023841 [0x8007041F] - código de saída 512

Este erro ocorre quando a base de dados de serviços vss provider de recuperação do site Azure está [bloqueada.](/previous-versions/ms833798(v=msdn.10)) Tente instalar o VSS manualmente na máquina de origem executando o seguinte comando a partir de um pedido de comando:

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

Quando houver uma falha, verifique se algum programa antivírus ou outros serviços estão presos num estado **inicial.** Um processo num estado **inicial** pode manter o bloqueio nos serviços de base de dados. Isto levará a falhas na instalação do fornecedor VSS. Certifique-se de que nenhum serviço está em estado **de partida** e, em seguida, relemisse a operação acima.

### <a name="vss-exit-code-806"></a>VSS código de saída 806

Este erro ocorre quando a conta de utilizador utilizada para a instalação não tem permissões para executar o `CSScript` comando. Forneça as permissões necessárias à conta de utilizador para executar o script e voltar a tentar a operação.

### <a name="other-vss-errors"></a>Outros erros do VSS

Tente instalar manualmente o serviço do fornecedor VSS na máquina de origem, executando o seguinte comando a partir de uma indicação de comando:

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

## <a name="vss-error---0x8004e00f"></a>Erro VSS - 0x8004E00F

Este erro ocorre normalmente durante a instalação do agente mobility devido a problemas `DCOM` e `DCOM` encontra-se em estado crítico.

Utilize o seguinte procedimento para determinar a causa do erro.

### <a name="examine-the-installation-logs"></a>Examinar os registos de instalação

1. Abra o registo de instalação localizado em _C:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log_.
2. A presença do seguinte erro indica esta questão:

    ```Output
    Unregistering the existing application...
    Create the catalogue object
    Get the collection of Applications

    ERROR:

    - Error code: -2147164145 [0x8004E00F]
    - Exit code: 802
    ```

Para resolver o problema:

Contacte a equipa da [plataforma microsoft Windows](https://aka.ms/Windows_Support) para obter assistência na resolução do problema do DCOM.

Quando o problema do DCOM for resolvido, reinstale manualmente o Fornecedor VSS de Recuperação do Sítio Azure utilizando manualmente o seguinte comando a partir de uma solicitação de comando:

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

Se a consistência da aplicação não for fundamental para os seus requisitos de recuperação de desastres, pode contornar a instalação do VSS Provider.

Para contornar a instalação do Fornecedor VSS de recuperação do local de Azure e instalar manualmente a instalação do fornecedor de registos Azure Site Recovery VSS Provider:

1. Instale o serviço de Mobilidade. A Instalação falhará no degrau: **Configuração de instalação do post**.
1. Para contornar a instalação VSS:
   1. Abra o diretório de instalação do Serviço de Mobilidade de Recuperação do Local Azure localizado em:

      _C:\Ficheiros de programas (x86)\Microsoft Azure Site Recovery\agent_

   1. Modifique os scripts de instalação _do_ Fornecedor VSS de recuperação do local de Azure InMageVSSProvider_Install e _InMageVSSProvider_Uninstall.cmd_ para sempre ter sucesso adicionando as seguintes linhas:

      ```plaintext
      rem @echo off
      setlocal
      exit /B 0
      ```

1. Faça uma instalação manual do Agente de Mobilidade.
1. Quando a instalação for bem sucedida e passar para o passo seguinte, **Configure,** remova as linhas adicionadas.
1. Para instalar o fornecedor VSS, abra um pedido de comando como administrador e execute o seguinte comando:

   `"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

1. Verifique se o Fornecedor VSS de Recuperação de Sítios Azure está instalado como um serviço nos Serviços windows. Abra o Serviço de Componentes MMC para confirmar que o Fornecedor VSS está listado.
1. Se a instalação do VsS Provider continuar a falhar, trabalhe com suporte técnico para resolver os erros de permissões na Interface de Programação de Aplicações Criptográficas (CAPI2).

## <a name="vss-provider-installation-fails-because-the-cluster-service-being-enabled-on-non-cluster-machine"></a>A instalação do VSS Provider falha porque o serviço de cluster está a ser ativado na máquina não-cluster

Este problema faz com que a instalação do Agente de Mobilidade de Recuperação do Local Azure falhe durante a instalação do Fornecedor VSS de Recuperação do Local de Azure. A falha é porque há um problema com `COM+` o que impede a instalação do fornecedor VSS.

### <a name="to-identify-the-issue"></a>Para identificar a questão

No registo localizado no servidor de configuração _em C:\ProgramData\ASRSetupLogs\UploadedLogs \<date-time> UA_InstallLogFile.log_ encontrará a seguinte exceção:

```plaintext
COM+ was unable to talk to the Microsoft Distributed Transaction Coordinator (Exception from HRESULT: 0x8004E00F)
```

Para resolver o problema:

1. Verifique se esta máquina é uma máquina não-cluster e que os componentes do cluster não estão a ser utilizados.
1. Se os componentes não estiverem a ser utilizados, retire os componentes do cluster da máquina.

## <a name="drivers-are-missing-on-the-source-server"></a>Os condutores estão desaparecidos no servidor de origem.

Se a instalação do Agente de Mobilidade falhar, examine os registos em _C:\ProgramData\ASRSetupLogs_ para determinar se alguns dos controladores necessários estão em falta em alguns conjuntos de controlo.

Para resolver o problema:

1. Utilizando um editor de registo, `regedit.msc` como, abrir o registo.
1. Abra o `HKEY_LOCAL_MACHINE\SYSTEM` nó.
1. No `SYSTEM` nó, localize os conjuntos de controlo.
1. Abra cada conjunto de controlo e verifique se estão presentes os seguintes controladores Windows:

   * Atapi
   * Rio Vmbus
   * Storflt
   * Estação Storvsc
   * Intelecto

1. Reinstalar os condutores desaparecidos.

## <a name="next-steps"></a>Passos seguintes

[Saiba mais](vmware-azure-tutorial.md) sobre como configurar a recuperação de desastres para VMware VMs.
