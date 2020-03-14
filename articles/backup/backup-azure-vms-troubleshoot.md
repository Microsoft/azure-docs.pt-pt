---
title: Erros de backup de resolução de problemas com VMs Azure
description: Neste artigo, aprenda a resolver erros encontrados com backup e restauro de máquinas virtuais Azure.
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 08/30/2019
ms.openlocfilehash: 15e4b4c8850798fd2386cd2874b6ab58a18d5406
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79297395"
---
# <a name="troubleshooting-backup-failures-on-azure-virtual-machines"></a>Falhas de backup em máquinas virtuais Azure

Pode resolver erros encontrados ao utilizar o Azure Backup com as informações listadas abaixo:

## <a name="backup"></a>Cópia de segurança

Esta secção cobre a falha de funcionamento de backup da máquina Azure Virtual.

### <a name="basic-troubleshooting"></a>Resolução de problemas básicos

* Certifique-se de que o Agente VM (Agente WA) é a [versão mais recente](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms-prepare#install-the-vm-agent).
* Certifique-se de que a versão Do SI ou Do Sistema Operativo VM Do Windows ou Do Linux é suportada, consulte a Matriz de Suporte de [Backup IaaS VM](https://docs.microsoft.com/azure/backup/backup-support-matrix-iaas).
* Verifique se outro serviço de reserva não está a funcionar.
  * Para garantir que não existem problemas de extensão instantânea, [desinstale as extensões para forçar a recarga e, em seguida, tente novamente a cópia de segurança](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout).
* Verifique se o VM tem conectividade com a Internet.
  * Certifique-se de que outro serviço de reserva não está a funcionar.
* A partir de `Services.msc`, certifique-se de que o serviço **de agente convidado Windows Azure** está em **execução.** Se o serviço **de Agente Convidado Windows Azure** estiver em falta, instale-o a partir de [VMs Azure back up num cofre de Serviços de Recuperação](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms-prepare#install-the-vm-agent).
* O **registo do Evento** pode apresentar falhas de backup provenientes de outros produtos de backup, por exemplo, cópiade cópia de segurança do Windows Server, e não são devidos à cópia de segurança do Azure. Utilize os seguintes passos para determinar se o problema está com a Cópia de Segurança Azure:
  * Se houver algum erro com uma **cópia** de segurança de entrada na fonte ou mensagem do evento, verifique se as cópias de segurança VM Do Azure IaaS foram bem sucedidas e se foi criado um Ponto de Restauro com o tipo de instantâneo pretendido.
  * Se o Azure Backup está a funcionar, então o problema é provável que com outra solução de backup.
  * Aqui está um exemplo de um erro do espectador de evento517 onde a cópia de segurança do Azure estava a funcionar bem, mas o "Windows Server Backup" estava a falhar:<br>
    ![falha na reserva de backup do servidor do Windows](media/backup-azure-vms-troubleshoot/windows-server-backup-failing.png)
  * Se o Backup Azure estiver a falhar, procure o código de erro correspondente na secção Erros de backup VM comuns neste artigo.

## <a name="common-issues"></a>Problemas comuns

Seguem-se problemas comuns com falhas de backup em máquinas virtuais Azure.

## <a name="copyingvhdsfrombackupvaulttakinglongtime---copying-backed-up-data-from-vault-timed-out"></a>CopyingVHDsFromBackUpVaultTakingLongTime - Cópia de dados com cópias de dados cronometrados a partir do cofre cronometrado

Error code: CopyingVHDsFromBackUpVaultTakingLongTime <br/>
Error message: Copying backed dados from vault timeed out

Isto pode acontecer devido a erros de armazenamento transitórios ou à conta de armazenamento insuficiente IOPS para o serviço de backup transferir dados para o cofre dentro do período de tempo limite. Configure a cópia de segurança VM utilizando estas [boas práticas](backup-azure-vms-introduction.md#best-practices) e tente novamente a operação de backup.

## <a name="usererrorvmnotindesirablestate---vm-is-not-in-a-state-that-allows-backups"></a>UserErrorVmNotInDesirableState - VM não está num estado que permite backups

Código de erro: UserErrorVmNotInDesirableState <br/>
Mensagem de erro: VM não está num estado que permite backups.<br/>

A operação de reserva falhou porque o VM está em estado falhado. Para obter uma cópia de segurança com êxito, o estado deve ser Em Execução, Parada ou Parada (desalocada).

* Se o VM estiver num estado transitório entre **Correr** e **desligar,** espere que o Estado mude. Então desencadeie o trabalho de reserva.
* Se o VM for um VM Linux e utilizar o módulo kernel Linux reforçado pela segurança, exclua o caminho do Agente Azure Linux **/var/lib/waagent** da política de segurança e certifique-se de que a extensão de backup está instalada.

## <a name="usererrorfsfreezefailed---failed-to-freeze-one-or-more-mount-points-of-the-vm-to-take-a-file-system-consistent-snapshot"></a>UserErrorFsFreezeFailed - Falhou em congelar um ou mais pontos de montagem do VM para tirar um instantâneo consistente do sistema de ficheiros

Código de erro: UserErrorFsFreezeFailed <br/>
Mensagem de erro: Não congelou um ou mais pontos de montagem do VM para tirar uma imagem consistente do sistema de ficheiros.

* Desmontar os dispositivos para os quais o estado do sistema de ficheiros não foi limpo, utilizando o comando **umount.**
* Verifique a consistência do sistema de ficheiros nestes dispositivos utilizando o comando **fsck.**
* Monte os dispositivos novamente e volte a tentar o funcionamento de reserva.</ol>

## <a name="extensionsnapshotfailedcom--extensioninstallationfailedcom--extensioninstallationfailedmdtc---extension-installationoperation-failed-due-to-a-com-error"></a>ExtensionSnapshotFailedCOM / ExtensionInstallationFailedCOM / ExtensionInstallationFailedMDTC - Instalação/operação de extensão falhou devido a um erro COM+

Código de erro: ExtensionSnapshotFailedCOM <br/>
Error message: A operação instantânea falhou devido a erro com o COM+

Código de erro: Instalação de extensãoFailedCOM  <br/>
Error message: Instalação/operação de extensão falhou devido a um erro COM+

Código de erro: Instalação De extensãoFalhadoMDTC <br/>
Error message: Extension installation failed with the error "COM+ was t able to talk with the Microsoft Distributed Transaction Coordinator <br/>

A operação 'Backup' falhou devido a um problema com a aplicação do **sistema COM+** do serviço Windows.  Para resolver este problema, siga estes passos:

* Tente iniciar/reiniciar a **aplicação** do sistema COM+ do serviço Windows (a partir de um pedido de comando elevado **- comsysApp**de arranque líquido ).
* Certifique-se de que o serviço de Coordenador de **Transações Distribuídas** está a funcionar como conta de Serviço de **Rede.** Caso contrário, altere-o para funcionar como conta **de Serviço de Rede** e reiniciar a Aplicação do Sistema **COM+** .
* Se não conseguir reiniciar o serviço, reinstale o serviço de Coordenador de **Transações Distribuídas** seguindo as etapas a seguir:
  * Pare o serviço MS DTC
  * Abra uma linha de comandos (cmd)
  * Executar comando "msdtc -desinstalar"
  * Executar comando "msdtc-instalar"
  * Inicie o serviço MS DTC
* Inicie a **aplicação**do sistema COM+ do serviço Windows . Após o início da Aplicação do **Sistema COM+,** inicie uma cópia de segurança do portal Azure.</ol>

## <a name="extensionfailedvsswriterinbadstate---snapshot-operation-failed-because-vss-writers-were-in-a-bad-state"></a>ExtensionFailedVssWriterInBadState - A operação snapshot falhou porque os escritores vsS estavam em mau estado

Código de erro: ExtensionFailedVssWriterInBadState <br/>
Mensagem de erro: A operação instantânea falhou porque os escritores do VSS estavam em mau estado.

Reinicie os escritores da VSS que estão em mau estado. A partir de um pedido de comando elevado, corra ```vssadmin list writers```. A produção contém todos os escritores vss e o seu estado. Para cada escritor da VSS com um estado que não é **[1] Estável,** para reiniciar o escritor VSS, executar os seguintes comandos a partir de um pedido de comando elevado:

* ```net stop serviceName```
* ```net start serviceName```

## <a name="extensionconfigparsingfailure--failure-in-parsing-the-config-for-the-backup-extension"></a>ExtensionConfigParsingFailure - Falha na análise do config para a extensão de backup

Código de erro: Falha de extensãoConfigParsing<br/>
Mensagem de erro: Falha na análise do figo para a extensão de reserva.

Este erro ocorre devido a permissões alteradas no diretório **MachineKeys:** **%systemdrive%\programdata\microsoft\crypto\rsa\machinekeys**.
Executar o seguinte comando e verificar se as permissões no diretório **MachineKeys** são predefinidas:**icacls %systemdrive%\programdata\microsoft\crypto\rsa\machinekeys**.

As permissões predefinidas são as seguintes:

* Todos: (R,W)
* ADMINISTRADORES BUILTIN\: (F)

Se vir permissões no diretório **MachineKeys** diferentes das predefinições, siga estes passos para corrigir permissões, elimine o certificado e desencadeie a cópia de segurança:

1. Corrija permissões no diretório **MachineKeys.** Ao utilizar propriedades de segurança do Explorer e definições de segurança avançadas no diretório, redefinir as permissões de volta aos valores padrão. Remova todos os objetos do utilizador exceto as predefinições do diretório e certifique-se de que a permissão **de Todos** tem acesso especial da seguinte forma:

   * Lista de dados de pastas/leitura
   * Ler atributos
   * Ler atributos estendidos
   * Criar ficheiros/escrever dados
   * Criar dados de pastas/apêndices
   * Escrever atributos
   * Escrever atributos estendidos
   * Ler permissões
2. Elimine todos os certificados sempre que **emitido seja** o modelo de implantação clássico ou gerador de **certificados CRP Windows Azure:**

   * [Abram certificados numa consola de computador local.](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in)
   * Nos Certificados de > **Pessoais,** elimine todos os certificados sempre que **emitido** seja o modelo clássico de implementação ou gerador de **certificados CRP Windows Azure**.
3. Desencadeie um trabalho de apoio vm.

## <a name="extensionstuckindeletionstate---extension-state-is-not-supportive-to-backup-operation"></a>ExtensionStuckInDeletionState - Estado de extensão não apoia a operação de backup

Código de erro: ExtensionStuckInDeletionState <br/>
Mensagem de erro: Estado de extensão não apoia operação de backup

A operação de backup falhou devido ao estado inconsistente de extensão de backup. Para resolver este problema, siga estes passos:

* Certifique-se de que o Agente Convidado está instalado e a responder
* A partir do portal Azure, vá à **Máquina Virtual** > todas **as configurações** > **extensões**
* Selecione a extensão de cópia de segurança VmSnapshot ou VmSnapshotLinux e clique em **Desinstalar**
* Depois de apagar a extensão de reserva, tente novamente a operação de backup
* A operação de cópia de segurança subsequente irá instalar a nova extensão no estado pretendido

## <a name="extensionfailedsnapshotlimitreachederror---snapshot-operation-failed-as-snapshot-limit-is-exceeded-for-some-of-the-disks-attached"></a>ExtensãoSnapshotLimitReachedError - A operação instantânea falhou à medida que o limite de instantâneo é ultrapassado para alguns dos discos anexados

Código de erro: ExtensionFailedSnapshotLimitReachedError  <br/>
Mensagem de erro: A operação instantânea falhou à medida que o limite de instantâneo é ultrapassado para alguns dos discos ligados

A operação instantânea falhou, uma vez que o limite de instantâneo ultrapassou alguns dos discos ligados. Complete os passos abaixo de resolução de problemas e, em seguida, tente novamente a operação.

* Elimine as imagens blob do disco que não são necessárias. Tenha cuidado para não apagar a bolha do disco, apenas as bolhas de instantâneo devem ser eliminadas.
* Se o Soft-delete estiver ativado nas Contas de Armazenamento do disco VM, configure a retenção de eliminação suave de modo a que os instantâneos existentes sejam inferiores ao máximo permitido em qualquer momento.
* Se a Recuperação do Site Azure estiver ativada no VM apoiado, então execute os passos abaixo:

  * Certifique-se de que o valor do **isanysnapshotfailed** é definido como falso em /etc/azure/vmbackup.conf
  * Agendar a Recuperação do Site Azure num momento diferente, de modo a não entrar em conflito com a operação de backup.

## <a name="extensionfailedtimeoutvmnetworkunresponsive---snapshot-operation-failed-due-to-inadequate-vm-resources"></a>ExtensionFailedTimeoutVMNetworkUnresponsive - A operação snapshot falhou devido a recursos VM inadequados

Código de erro: ExtensionFailedTimeoutVMNetworkUnresponsive<br/>
Mensagem de erro: A operação instantânea falhou devido a recursos VM inadequados.

A operação de backup no VM falhou devido ao atraso nas chamadas de rede durante a execução da operação instantânea. Para resolver este problema, siga o Passo 1. Se o problema persistir, experimente os passos 2 e 3.

**Passo 1**: Criar instantâneo através do anfitrião

Numa linha de comandos elevada (administrador), execute o comando abaixo:

```text
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v SnapshotMethod /t REG_SZ /d firstHostThenGuest /f
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v CalculateSnapshotTimeFromHost /t REG_SZ /d True /f
```

Desta forma, garante-es que os instantâneos são criados através do anfitrião e não do Convidado. Repita a operação de cópia de segurança.

**Passo 2:** Tente alterar o horário de backup para um momento em que o VM esteja menos carregado (menos CPU/IOps, etc.)

**Passo 3:** Tente [aumentar o tamanho do VM](https://azure.microsoft.com/blog/resize-virtual-machines/) e tente novamente a operação

## <a name="common-vm-backup-errors"></a>Erros de backup VM comuns

| Detalhes do erro | Solução |
| ------ | --- |
| **Código de erro**: 320001, ResourceNotFound <br/> **Mensagem de erro**: Não podia executar a operação uma vez que a VM já não existe. <br/> <br/> **Código de erro**: 400094, BCMV2VMNotFound <br/> **Mensagem de erro**: A máquina virtual não existe <br/> <br/>  Não foi encontrada uma máquina virtual Azure.  |Este erro ocorre quando o VM primário é eliminado, mas a política de backup ainda procura um VM para apoiar. Para corrigir este erro, tome os seguintes passos: <ol><li> Recriar a máquina virtual com o mesmo nome e mesmo nome de grupo de recursos, nome de **serviço em nuvem,**<br>**ou**</li><li> Pare de proteger a máquina virtual com ou sem apagar os dados de cópia de segurança. Para mais informações, consulte [Pare de proteger as máquinas virtuais](backup-azure-manage-vms.md#stop-protecting-a-vm).</li></ol>|
|**Código de erro**: UserErrorBCMPremiumStorageQuotaError<br/> **Mensagem de erro**: Não conseguicopiar o instantâneo da máquina virtual, devido a falta de espaço livre na conta de armazenamento | Para VMs premium na pilha de backup VM V1, copiamos o instantâneo para a conta de armazenamento. Este passo garante que o tráfego de gestão de backup, que funciona no instantâneo, não limita o número de IOPS disponíveis para a aplicação utilizando discos premium. <br><br>Recomendamos que aloque apenas 50%, 17,5 TB, do espaço total da conta de armazenamento. Em seguida, o serviço de backup Azure pode copiar o instantâneo para a conta de armazenamento e transferir dados deste local copiado na conta de armazenamento para o cofre. |
| **Código de erro**: 380008, AzureVmOffline <br/> **Error message**: Falha na instalação da extensão dos Serviços de Recuperação da Microsoft, uma vez que a máquina virtual não está a funcionar | O Agente VM é um pré-requisito para a extensão dos Serviços de Recuperação do Azure. Instale o Agente De Máquina Virtual Azure e reinicie a operação de registo. <br> <ol> <li>Verifique se o Agente VM está instalado corretamente. <li>Certifique-se de que a bandeira do config VM está corretamente definida.</ol> Leia mais sobre a instalação do Agente VM e como validar a instalação do Agente VM. |
| **Código de erro**: ExtensionSnapshotBitlockerError <br/> **Mensagem de erro**: A operação instantânea falhou com o erro de funcionamento do Serviço de Cópia de Sombra de Volume (VSS) **Esta unidade está bloqueada pela Encriptação de Unidade BitLocker. Tem de desbloquear esta unidade do Painel de Controlo.** |Desligue o BitLocker para todas as unidades do VM e verifique se o problema vsS está resolvido. |
| **Código de erro**: VmNotInDesejávelState <br/> **Mensagem de erro**: O VM não está num estado que permite cópias de segurança. |<ul><li>Se o VM estiver num estado transitório entre **Correr** e **desligar,** espere que o Estado mude. Então desencadeie o trabalho de reserva. <li> Se o VM for um VM Linux e utilizar o módulo kernel Linux reforçado pela segurança, exclua o caminho do Agente Azure Linux **/var/lib/waagent** da política de segurança e certifique-se de que a extensão de backup está instalada.  |
| O Agente VM não está presente na máquina virtual: <br>Instale qualquer pré-requisito e o Agente VM. Em seguida, reiniciar a operação. |Leia mais sobre a instalação do [Agente VM e como validar a instalação](#vm-agent)do Agente VM . |
| **Código de erro**: ExtensionSnapshotFailedNoSecureNetwork <br/> **Mensagem de erro**: A operação instantânea falhou devido à falha na criação de um canal de comunicação de rede seguro. | <ol><li> Abra o Editor de Registo executando **regedit.exe** em modo elevado. <li> Identifique todas as versões do .NET Framework presentes no seu sistema. Estão presentes sob a hierarquia da chave de registo **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft**. <li> Para cada .NET Quadro presente na chave de registo, adicione a seguinte chave: <br> **SchUseStrongCrypto"=dword:0000000001**. </ol>|
| **Código de erro**: Falha de instalação do RevvcRedist <br/> **Mensagem de erro**: A operação instantânea C++ falhou devido à falha na instalação visual redistribuível para o Visual Studio 2012. | Navegue para C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot\agentVersion e instale vcredist2013_x64.<br/>Certifique-se de que o valor chave do registo que permite a instalação do serviço está definido para o valor correto. Ou seja, detete **o** valor inicial em **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Msiserver** para **3** e não **4**. <br><br>Se ainda tiver problemas com a instalação, reinicie o serviço de instalação executando **mSIEXEC/UNREGISTER** seguido de **MSIEXEC/REGISTER** a partir de um pedido de comando elevado.  |
| **Código de erro**: UserErrorRequestDisallowedByPolicy <BR> **Mensagem de erro**: Uma política inválida está configurada no VM que está a impedir o funcionamento do Snapshot. | Se tiver uma Política Azure que [regule as etiquetas no seu ambiente,](https://docs.microsoft.com/azure/governance/policy/tutorials/govern-tags)considere alterar a política de um [efeito Deny](https://docs.microsoft.com/azure/governance/policy/concepts/effects#deny) para um [efeito Modificar,](https://docs.microsoft.com/azure/governance/policy/concepts/effects#modify)ou crie o grupo de recursos manualmente de acordo com o esquema de [nomeação exigido pela Azure Backup](https://docs.microsoft.com/azure/backup/backup-during-vm-creation#azure-backup-resource-group-for-virtual-machines).
## <a name="jobs"></a>Tarefas

| Detalhes do erro | Solução |
| --- | --- |
| O cancelamento não é suportado para este tipo de trabalho: <br>Espere até o trabalho terminar. |Nenhum |
| O trabalho não está num estado cancelável: <br>Espere até o trabalho terminar. <br>**ou**<br> O trabalho selecionado não está num estado cancelável: <br>Espere que o trabalho termine. |É provável que o trabalho esteja quase terminado. Espere até o trabalho estar terminado.|
| O reforço não pode cancelar o trabalho porque não está em andamento. <br>O cancelamento é suportado apenas para postos de trabalho em curso. Tente cancelar um trabalho em andamento. |Este erro acontece por causa de um estado transitório. Espere um minuto e tente novamente a operação de cancelamento. |
| O reforço não cancelou o trabalho: <br>Espere até o trabalho terminar. |Nenhum |

## <a name="restore"></a>Restaurar

| Detalhes do erro | Solução |
| --- | --- |
| Restaurar falhou com um erro interno na nuvem. |<ol><li>O serviço de nuvem ao qual está a tentar restaurar está configurado com as definições de DNS. Pode verificar: <br>**$deployment = Get-AzureDeployment -ServiceName "ServiceName" -Slot "Production" Get-AzureDns -DnsSettings $deployment. DnsSettings**.<br>Se o **Endereço** estiver configurado, as definições de DNS são configuradas.<br> <li>O serviço de nuvem ao qual está a tentar restaurar está configurado com **O IP Reservado**, e os VMs existentes no serviço de nuvem estão em estado de paragem. Pode verificar se um serviço na nuvem reservou um IP utilizando os seguintes cmdlets PowerShell: **$deployment = Get-AzureDeployment -ServiceName "servicename" -Slot "Production" $dep. Nome IP Reservado**. <br><li>Está a tentar restaurar uma máquina virtual com as seguintes configurações especiais de rede no mesmo serviço de nuvem: <ul><li>Máquinas virtuais sob configuração do equilíbrio de carga, interna e externa.<li>Máquinas virtuais com vários IPs reservados. <li>Máquinas virtuais com vários NICs. </ul><li>Selecione um novo serviço de nuvem na UI ou consulte [considerações](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) de restauro para VMs com configurações especiais de rede.</ol> |
| O nome DNS selecionado já foi tomado: <br>Especifique um nome DNS diferente e tente novamente. |Este nome DNS refere-se ao nome do serviço na nuvem, normalmente terminando com **.cloudapp.net**. Este nome tem de ser único. Se tiver este erro, terá de escolher um nome VM diferente durante o restauro. <br><br> Este erro é mostrado apenas aos utilizadores do portal Azure. A operação de restauro através da PowerShell tem sucesso porque restaura apenas os discos e não cria o VM. O erro será enfrentado quando o VM for explicitamente criado por si após a operação de restauro do disco. |
| A configuração de rede virtual especificada não está correta: <br>Especifique uma configuração de rede virtual diferente e tente novamente. |Nenhum |
| O serviço de nuvem especificado está a utilizar um IP reservado que não corresponde à configuração da máquina virtual que está a ser restaurada: <br>Especifique um serviço de nuvem diferente que não esteja a usar um IP reservado. Ou escolha outro ponto de recuperação para restaurar. |Nenhum |
| O serviço de nuvem atingiu o seu limite no número de pontos finais de entrada: <br>Tente novamente a operação especificando um serviço de nuvem diferente ou utilizando um ponto final existente. |Nenhum |
| O cofre dos Serviços de Recuperação e a conta de armazenamento alvo estão em duas regiões diferentes: <br>Certifique-se de que a conta de armazenamento especificada na operação de restauro está na mesma região do Azure que o seu cofre dos Serviços de Recuperação. |Nenhum |
| A conta de armazenamento especificada para a operação de restauro não é suportada: <br>São suportadas apenas contas de armazenamento Básicas ou Standard com configurações de replicação localmente redundantes ou georedundantes. Selecione uma conta de armazenamento suportada. |Nenhum |
| O tipo de conta de armazenamento especificada para a operação de restauro não está online: <br>Certifique-se de que a conta de armazenamento especificada na operação de restauro está on-line. |Este erro pode acontecer devido a um erro transitório no Armazenamento Azure ou por causa de uma paragem. Escolha outra conta de armazenamento. |
| A quota do grupo de recursos foi atingida: <br>Elimine alguns grupos de recursos do portal Azure ou contacte o Suporte Azure para aumentar os limites. |Nenhum |
| A sub-rede selecionada não existe: <br>Selecione uma sub-rede que exista. |Nenhum |
| O serviço de backup não tem autorização para aceder a recursos na sua subscrição. |Para resolver este erro, restaure primeiro os discos utilizando os passos em [discos restabelecidos restaurados](backup-azure-arm-restore-vms.md#restore-disks). Em seguida, utilize os passos PowerShell em [Criar um VM a partir de discos restaurados](backup-azure-vms-automation.md#restore-an-azure-vm). |

## <a name="backup-or-restore-takes-time"></a>Backup ou restaurar leva tempo

Se o seu backup demorar mais de 12 horas, ou restaurar leva mais de 6 horas, reveja [as melhores práticas](backup-azure-vms-introduction.md#best-practices)e [considerações de desempenho](backup-azure-vms-introduction.md#backup-performance)

## <a name="vm-agent"></a>Agente VM

### <a name="set-up-the-vm-agent"></a>Configurar o Agente VM

Tipicamente, o VM Agent já está presente em VMs que são criados a partir da galeria Azure. Mas as máquinas virtuais que são migradas de centros de dados no local não terão o Agente VM instalado. Para esses VMs, o Agente VM precisa de ser instalado explicitamente.

#### <a name="windows-vms"></a>VMs do Windows

* Transfira e instale o [MSI do agente](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Precisa de privilégios do Administrador para terminar a instalação.
* Para máquinas virtuais criadas utilizando o modelo de implementação clássico, [atualize a propriedade VM](https://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) para indicar que o agente está instalado. Este passo não é necessário para máquinas virtuais do Gestor de Recursos Azure.

#### <a name="linux-vms"></a>VMs do Linux

* Instale a versão mais recente do agente a partir do repositório de distribuição. Para mais detalhes sobre o nome do pacote, consulte o [repositório](https://github.com/Azure/WALinuxAgent)do Agente Linux .
* Para VMs criados usando o modelo de implementação clássico, [use este blog](https://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) para atualizar a propriedade VM e verificar se o agente está instalado. Este passo não é necessário para máquinas virtuais do Gestor de Recursos.

### <a name="update-the-vm-agent"></a>Atualizar o Agente VM

#### <a name="windows-vms"></a>VMs do Windows

* Para atualizar o Agente VM, reinstale os binários do [Agente VM](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Antes de atualizar o agente, certifique-se de que não ocorrem operações de backup durante a atualização do Agente VM.

#### <a name="linux-vms"></a>VMs do Linux

* Para atualizar o Agente VM Linux, siga as instruções do artigo [Atualização do Agente VM Linux](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

    > [!NOTE]
    > Utilize sempre o repositório de distribuição para atualizar o agente.

    Não descarregue o código de agente do GitHub. Se o agente mais recente não estiver disponível para a sua distribuição, contacte o suporte de distribuição para obter instruções para adquirir o mais recente agente. Também pode verificar as mais recentes informações do [agente Windows Azure Linux](https://github.com/Azure/WALinuxAgent/releases) no repositório GitHub.

### <a name="validate-vm-agent-installation"></a>Validar a instalação do Agente VM

Verifique a versão do VM Agent nos VMs do Windows:

1. Inscreva-se na máquina virtual Azure e navegue para a pasta **C:\WindowsAzure\Packages**. Deve encontrar o ficheiro **WaAppAgent.exe.**
2. Clique no ficheiro e vá para **Propriedades**. Em seguida, selecione o separador **Detalhes.** O campo versão do **produto** deve ser 2.6.1198.718 ou superior.

## <a name="troubleshoot-vm-snapshot-issues"></a>Problemas de instantâneo vm

A cópia de segurança VM baseia-se na emissão de comandos instantâneos para armazenamento subjacente. Não ter acesso ao armazenamento ou atrasos numa execução de tarefa instantânea pode fazer com que o trabalho de reserva falhe. As seguintes condições podem causar falha de tarefa instantânea:

* **VMs com cópia de segurança do Servidor SQL configurada pode causar atraso de tarefa instantânea**. Por predefinição, a cópia de segurança VM cria uma cópia de segurança completa VSS nos VMs do Windows. Os VMs que executam o SQL Server, com a cópia de segurança do SQL Server configurada, podem experimentar atrasos instantâneos. Se os atrasos instantâneos causarem falhas de cópia de segurança, detete imeia a seguinte tecla de registo:

   ```text
   [HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
   "USEVSSCOPYBACKUP"="TRUE"
   ```

* **O estado vm é reportado incorretamente porque o VM é desligado em RDP**. Se utilizou o ambiente de trabalho remoto para desligar a máquina virtual, verifique se o estado VM no portal está correto. Se o estado não estiver correto, utilize a opção **Shutdown** no painel de instrumentos VM do portal para desligar o VM.
* **Se mais de quatro VMs partilharem o mesmo serviço na nuvem, espalhe os VMs através de múltiplas políticas**de backup . Escalonar os tempos de reserva, por isso, não mais do que quatro cópias de segurança VM começam ao mesmo tempo. Tente separar os horários de início das apólices em pelo menos uma hora.
* **O VM funciona em alta CPU ou memória.** Se a máquina virtual funciona com alta memória ou utilização de CPU, mais de 90%, a sua tarefa instantânea está na fila e atrasada. Eventualmente, vezes para fora. Se esta questão acontecer, tente um reforço a pedido.

## <a name="networking"></a>Redes

O DHCP deve ser ativado dentro do hóspede para que o backup IaaS VM funcione. Se precisar de um IP privado estático, configure-o através do portal Azure ou powerShell. Certifique-se de que a opção DHCP dentro do VM está ativada.
Obtenha mais informações sobre como configurar um IP estático através da PowerShell:

* [Como adicionar um IP interno estático a um VM existente](https://docs.microsoft.com/powershell/module/az.network/set-aznetworkinterfaceipconfig?view=azps-3.5.0#description)
* [Alterar o método de atribuição de um endereço IP privado atribuído a uma interface de rede](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface)

