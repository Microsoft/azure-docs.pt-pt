---
title: Erros de backup de resolução de problemas com VMs Azure
description: Neste artigo, aprenda a resolver problemas com os erros encontrados com cópia de segurança e restauro das máquinas virtuais Azure.
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 08/30/2019
ms.openlocfilehash: 6da91248c197eae12fbc59f2da8c5294d95117b6
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92173838"
---
# <a name="troubleshooting-backup-failures-on-azure-virtual-machines"></a>Falhas de backup de resolução de problemas em máquinas virtuais Azure

Pode resolver os erros encontrados durante a utilização do Azure Backup com as informações listadas abaixo:

## <a name="backup"></a>Cópia de segurança

Esta secção cobre a falha de funcionamento de backup da máquina Virtual Azure.

### <a name="basic-troubleshooting"></a>Resolução de problemas básicos

* Certifique-se de que o Agente VM (Agente WA) é a [versão mais recente.](./backup-azure-arm-vms-prepare.md#install-the-vm-agent)
* Certifique-se de que a versão Windows ou Linux VM OS está suportada, consulte a [Matriz de Suporte de Backup IaaS VM](./backup-support-matrix-iaas.md).
* Verifique se outro serviço de cópia de segurança não está a funcionar.
  * Para garantir que não existem problemas de extensão instantânea, [desinstale as extensões para forçar a recarga e, em seguida, re-tentar a cópia de segurança](./backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md).
* Verifique se o VM tem conectividade com a Internet.
  * Certifique-se de que outro serviço de reserva não esteja a funcionar.
* A partir de `Services.msc` , certifique-se de que o serviço **de Agente Convidado Do Windows Azure** está **a funcionar**. Se faltar o serviço **de Agente Convidado Windows Azure,** instale-o a partir de [VMs Azure de back up num cofre dos Serviços de Recuperação](./backup-azure-arm-vms-prepare.md#install-the-vm-agent).
* O **registo do Evento** pode apresentar falhas de backup que são de outros produtos de backup, por exemplo, cópia de segurança do Windows Server, e não são devidos a Azure Backup. Utilize os seguintes passos para determinar se o problema é com a Azure Backup:
  * Se houver um erro com a entrada **Cópia de Segurança** na fonte ou mensagem do evento, verifique se as cópias de segurança do Azure IaaS VM foram bem sucedidas e se foi criado um Ponto de Restauro com o tipo de instantâneo pretendido.
  * Se o Azure Backup estiver a funcionar, então o problema é provável que esteja com outra solução de backup.
  * Aqui está um exemplo de um erro do Observador de Eventos 517 onde o Azure Backup estava a funcionar bem, mas o "Windows Server Backup" estava a falhar: ![ Falha na backup do Windows Server](media/backup-azure-vms-troubleshoot/windows-server-backup-failing.png)
  * Se o Azure Backup estiver a falhar, procure o código de erro correspondente na secção Erros de backup VM comuns neste artigo.

## <a name="common-issues"></a>Problemas comuns

Seguem-se problemas comuns com falhas de backup em máquinas virtuais Azure.

### <a name="vmrestorepointinternalerror---antivirus-configured-in-the-vm-is-restricting-the-execution-of-backup-extension"></a>VMRestorePointInternalError - Antivírus configurado no VM está a restringir a execução da extensão de backup

Código de erro: VMRestorePointInternalError

Se no momento da cópia de segurança, os registos da **Aplicação do Visualizador** de Eventos apresentarem o nome da **aplicação falhando a mensagem: IaaSBcdrExtension.exe** então confirma-se que o antivírus configurado no VM está a restringir a execução da extensão de backup.
Para resolver este problema, exclua os diretórios abaixo na configuração antivírus e refemina a operação de backup.

* `C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot`
* `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot`

### <a name="copyingvhdsfrombackupvaulttakinglongtime---copying-backed-up-data-from-vault-timed-out"></a>CopyingVHDsFromBackUpVaultTakingLongTime - Cópia de dados com cópia suspensa do cofre cronometrado

Código de erro: CopyingVHDsFromBackUpVaultTakingLongTime <br/>
Mensagem de erro: Copiar dados com cópias de segurança do cofre cronometrados

Isto pode acontecer devido a erros de armazenamento transitórios ou a uma conta de armazenamento insuficiente IOPS para o serviço de cópia de segurança transferir dados para o cofre dentro do período de tempo. Configure a cópia de segurança VM utilizando estas [melhores práticas](backup-azure-vms-introduction.md#best-practices) e refaça a operação de backup.

### <a name="usererrorvmnotindesirablestate---vm-is-not-in-a-state-that-allows-backups"></a>UserErrorVmNotInDesirableState – a VM não está num estado que permita cópias de segurança

Código de erro: UserErrorVmNotInDesirableState <br/>
Error message: VM is not in a state that allows backups.<br/>

A operação de reserva falhou porque o VM está em estado de falha. Para obter uma cópia de segurança com êxito, o estado deve ser Em Execução, Parada ou Parada (desalocada).

* Se o VM estiver num estado transitório entre **correr** e **desligar,** aguarde que o Estado mude. Então desencadeie o trabalho de reserva.
* Se o VM for um Linux VM e utilizar o módulo de kernel linux Security-Enhanced, exclua o caminho do Agente Azure Linux **/var/lib/waagent** da política de segurança e certifique-se de que a extensão de Backup está instalada.

### <a name="usererrorfsfreezefailed---failed-to-freeze-one-or-more-mount-points-of-the-vm-to-take-a-file-system-consistent-snapshot"></a>UserErrorFsFreezeFailed - Não conseguiu congelar um ou mais pontos de montagem do VM para tirar uma imagem consistente do sistema de ficheiros

Código de erro: UserErrorFsFreezeFailed <br/>
Mensagem de erro: Não conseguiu congelar um ou mais pontos de montagem do VM para tirar uma imagem consistente do sistema de ficheiros.

* Desmonte os dispositivos para os quais o estado do sistema de ficheiros não foi limpo, utilizando o comando **umount.**
* Verifique a consistência do sistema de ficheiros nestes dispositivos utilizando o comando **FSCK.**
* Volte a montar os dispositivos e volte a tentar o funcionamento de reserva.</ol>

### <a name="extensionsnapshotfailedcom--extensioninstallationfailedcom--extensioninstallationfailedmdtc---extension-installationoperation-failed-due-to-a-com-error"></a>ExtensãoSSnapshotFailedCOM / ExtensãoInstalaçãoFailedCOM / ExtensãoInstalaçãoFailedMDTC - Instalação/operação de extensão falhou devido a um erro COM+

Código de erro: ExtensionSnapshotFailedCOM <br/>
Error message: A operação snapshot falhou devido a erro COM+

Código de erro: ExtensãoInstalaçãoFailedCOM  <br/>
Error message: A instalação/operação de extensão falhou devido a um erro COM+

Código de erro: ExtensãoInstalaçãoFailedMDTC <br/>
Error message: A instalação de extensão falhou com o erro "COM+ não foi capaz de falar com o Coordenador de Transações Distribuídas da Microsoft <br/>

A operação de backup falhou devido a um problema com a aplicação **do Sistema COM+ do** serviço Windows.  Para resolver este problema, siga estes passos:

* Tente iniciar/reiniciar o serviço Do Windows **com aplicação do sistema COM+** (a partir de um pedido de comando elevado **- net start COMSysApp**).
* Certifique-se de que o serviço **de Coordenador de Transações Distribuídas** está a funcionar como conta **de Serviço de Rede.** Caso contrário, altere-o para funcionar como conta **de Serviço de Rede** e reinicie a **Aplicação do Sistema COM+**.
* Se não conseguir reiniciar o serviço, reinstale o serviço **de Coordenador de Transações Distribuídas** seguindo os passos abaixo:
  * Pare o serviço MS DTC
  * Abra uma linha de comandos (cmd)
  * Execute o comando `msdtc -uninstall`
  * Execute o comando `msdtc -install`
  * Inicie o serviço MS DTC
* Inicie a **aplicação**do sistema com o serviço Windows COM+ . Após o início da **Aplicação do Sistema COM+,** desencadeie uma tarefa de backup a partir do portal Azure.</ol>

### <a name="extensionfailedvsswriterinbadstate---snapshot-operation-failed-because-vss-writers-were-in-a-bad-state"></a>ExtensionFailedVssWriterInBadState – a operação de instantâneo falhou porque os escritores VSS estavam num estado incorreto

Código de erro: ExtensãoFailedVssWriterInBadState <br/>
Error message: Snapshot operation failed because VSS writers were in a bad state.

Este erro ocorre porque os escritores do VSS estavam em mau estado. As extensões de Backup Azure interagem com os ESCRITORES VSS para tirar fotos dos discos. Para resolver este problema, siga estes passos:

Passo 1: Reiniciar os escritores vss que estão em mau estado.

* A partir de um pedido de comando elevado, corra ```vssadmin list writers``` .
* A produção contém todos os escritores vss e o seu estado. Para cada escritor vss com um estado que não é **[1] Estável,** reinicie o serviço do respetivo escritor VSS.
* Para reiniciar o serviço, executar os seguintes comandos a partir de uma solicitação de comando elevada:

 ```net stop serviceName``` <br>
 ```net start serviceName```

> [!NOTE]
> Reiniciar alguns serviços pode ter impacto no seu ambiente de produção. Certifique-se de que o processo de aprovação é seguido e que o serviço é reiniciado no tempo de inatividade programado.

Passo 2: Se reiniciar os escritores VSS não resolveu o problema, então executar o seguinte comando a partir de um pedido de comando elevado (como administrador) para evitar que os fios sejam criados para instantâneos blob.

```console
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v SnapshotWithoutThreads /t REG_SZ /d True /f
```

Passo 3: Se os passos 1 e 2 não resolverem o problema, então a falha pode ser devido ao timing dos escritores vss devido a IOPS limitado.<br>

Para verificar, navegue nos ***registos de aplicações do Visualizador de Sistema e evento*** e verifique a seguinte mensagem de erro:<br>
*O provedor de cópia-sombra cronometrou enquanto segurava as gravações para o volume que estava a ser copiado. Isto deve-se provavelmente a uma atividade excessiva no volume por uma aplicação ou um serviço de sistema. Tente novamente mais tarde quando a atividade no volume for reduzida.*<br>

Solução:

* Verifique se há possibilidades de distribuir a carga pelos discos VM. Isto reduzirá a carga em discos individuais. Pode [verificar o estrangulamento dos IOPs, permitindo métricas de diagnóstico ao nível de armazenamento](../virtual-machines/troubleshooting/performance-diagnostics.md#install-and-run-performance-diagnostics-on-your-vm).
* Mude a política de backup para efetuar backups durante as horas de ponta, quando a carga no VM estiver no seu nível mais baixo.
* Atualize os discos Azure para suportar IOPs mais elevados. [Saiba mais aqui](../virtual-machines/disks-types.md)

### <a name="extensionfailedvssserviceinbadstate---snapshot-operation-failed-due-to-vss-volume-shadow-copy-service-in-bad-state"></a>ExtensionFailedVssServiceInBadState – A operação de instantâneo falhou devido ao serviço VSS (Serviço de Cópia Sombra de Volumes) estar num estado incorreto

Código de erro: ExtensãoFailedVssServiceInBadState <br/>
Error message: A operação snapshot falhou devido ao serviço VSS (Volume Shadow Copy) em mau estado.

Este erro ocorre porque o serviço VSS estava em mau estado. As extensões de Backup Azure interagem com o serviço VSS para tirar fotos dos discos. Para resolver este problema, siga estes passos:

Reinicie o serviço VSS (Volume Shadow Copy).

* Navegue para Services.msc e reinicie o serviço 'Volume Shadow Copy'.<br>
(ou)<br>
* Executar os seguintes comandos a partir de uma solicitação de comando elevada:

 ```net stop VSS``` <br>
 ```net start VSS```

Se o problema persistir, reinicie o VM no tempo de paragem programado.

### <a name="usererrorskunotavailable---vm-creation-failed-as-vm-size-selected-is-not-available"></a>UserErrorSkuNotAvailable - Criação VM falhou uma vez que o tamanho VM selecionado não está disponível

Código de erro: Mensagem de erro Do UtilizadorErrorSkuNotAvailable Error: A criação de VM falhou, uma vez que o tamanho VM selecionado não está disponível.

Este erro ocorre porque o tamanho VM selecionado durante a operação de restauro é um tamanho não suportado. <br>

Para resolver este problema, utilize a opção [de restaurar os discos](./backup-azure-arm-restore-vms.md#restore-disks) durante a operação de restauro. Utilize estes discos para criar um VM a partir da lista de [tamanhos VM suportados disponíveis utilizando](./backup-support-matrix-iaas.md#vm-compute-support) [cmdlets Powershell](./backup-azure-vms-automation.md#create-a-vm-from-restored-disks).

### <a name="usererrormarketplacevmnotsupported---vm-creation-failed-due-to-market-place-purchase-request-being-not-present"></a>UserErrorMarketPlaceVMNotSupported - VM criação falhou devido ao pedido de compra do Market Place não estar presente

Código de erro: UtilizadorErrorMarketPlaceVMNotSupported Error message: A criação de VM falhou devido ao pedido de compra do Market Place não estar presente.

O Azure Backup suporta a cópia de segurança e a restauração de VMs que estão disponíveis no Azure Marketplace. Este erro ocorre quando está a tentar restaurar um VM (com uma definição específica de Plan/Publisher) que já não está disponível no Azure Marketplace, [Saiba mais aqui](/legal/marketplace/participation-policy#offering-suspension-and-removal).

* Para resolver este problema, utilize a opção [de restauro](./backup-azure-arm-restore-vms.md#restore-disks) dos discos durante a operação de restauro e, em seguida, utilize cmdlets [PowerShell](./backup-azure-vms-automation.md#create-a-vm-from-restored-disks) ou [Azure CLI](./tutorial-restore-disk.md) para criar o VM com as informações mais recentes do mercado correspondentes ao VM.
* Se o editor não tiver nenhuma informação do Marketplace, pode utilizar os discos de dados para recuperar os seus dados e pode anexá-los a um VM existente.

### <a name="extensionconfigparsingfailure--failure-in-parsing-the-config-for-the-backup-extension"></a>ExtensãoConfigParsingFailure - Falha na análise do config para a extensão de backup

Código de erro: Configuração de extensãoParparsingFailure<br/>
Error message: Falha na análise do config para a extensão de backup.

Este erro ocorre devido a permissões alteradas no diretório **machineKeys:** **%systemdrive%\programdata\microsoft\crypto\rsa\machinekeys**.
Executar o seguinte comando e verificar se as permissões no diretório **machineKeys** são padrão: `icacls %systemdrive%\programdata\microsoft\crypto\rsa\machinekeys` .

As permissões por defeito são as seguintes:

* Todos: (R,W)
* BUILTIN\Administradores: (F)

Se vir permissões no diretório **MachineKeys** que são diferentes das predefinições, siga estes passos para corrigir permissões, elimine o certificado e desencadeie a cópia de segurança:

1. Corrija permissões no diretório **do MachineKeys.** Utilizando propriedades de segurança do Explorer e definições de segurança avançadas no diretório, reinicie as permissões de volta aos valores predefinidos. Remova todos os objetos do utilizador, exceto os predefinidos do diretório e certifique-se de que a permissão **de Todos** tem acesso especial da seguinte forma:

   * Listar dados de pasta/leitura
   * Ler atributos
   * Ler atributos estendidos
   * Criar ficheiros/escrever dados
   * Criar dados de pastas/apêndice
   * Escrever atributos
   * Escreva atributos estendidos
   * Ler permissões
2. Eliminar todos os certificados em **que o Emitida é** o modelo clássico de implementação ou o Gerador de **Certificados CRP do Windows Azure:**

   * [Abra os certificados numa consola de computador local.](/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in)
   * Nos **Personal**  >  **Certificados Pessoais,** elimine todos os certificados em **que o Emitida seja** o modelo clássico de implementação ou o Gerador de **Certificados CRP do Windows Azure**.
3. Desencadeie um trabalho de reserva VM.

### <a name="extensionstuckindeletionstate---extension-state-is-not-supportive-to-backup-operation"></a>ExtensãoStuckInDeletionState - Estado de extensão não apoia a operação de backup

Código de erro: ExtensionStuckInDeletionState <br/>
Error message: Extension state is not supportive to backup operation

A operação de backup falhou devido ao estado inconsistente da extensão de reserva. Para resolver este problema, siga estes passos:

* Certifique-se de que o Agente Convidado está instalado e a responder
* A partir do portal Azure, vá para **a Máquina Virtual**Todas as  >  **Extensões de**  >  **Definições**
* Selecione a extensão de backup VmSnapshot ou VmSnapshotLinux e selecione **Desinstalar**.
* Depois de eliminar a extensão de backup, recandidutar a operação de backup
* A operação de cópia de segurança subsequente irá instalar a nova extensão no estado pretendido

### <a name="extensionfailedsnapshotlimitreachederror---snapshot-operation-failed-as-snapshot-limit-is-exceeded-for-some-of-the-disks-attached"></a>ExtensãoFailedSnapshotLimitReachedError - A operação Snapshot falhou, uma vez que o limite de instantâneo é ultrapassado para alguns dos discos ligados

Código de erro: ExtensãoFailedSnapshotLimitReachedError  <br/>
Error message: A operação snapshot falhou à medida que o limite de instantâneo é ultrapassado para alguns dos discos ligados

A operação de instantâneo falhou, uma vez que o limite de instantâneo excedeu para alguns dos discos ligados. Complete as seguintes etapas de resolução de problemas e, em seguida, relemisse a operação.

* Elimine as imagens de bolhas de disco que não são necessárias. Tenha cuidado para não apagar as bolhas de disco. Apenas as bolhas instantâneas devem ser eliminadas.
* Se a eliminação de soft-delete estiver ativada nas contas de armazenamento do disco VM, configure a retenção de eliminação suave para que as imagens existentes sejam inferiores ao máximo permitido em qualquer momento.
* Se a Recuperação do Sítio Azure estiver ativada no VM apoiado, então execute os passos abaixo:

  * Certifique-se de que o valor de **isanysnapshot falhado** é definido como falso em /etc/azure/vmbackup.conf
  * Agende a recuperação do local de Azure num momento diferente, para que não contraia a operação de backup.

### <a name="extensionfailedtimeoutvmnetworkunresponsive---snapshot-operation-failed-due-to-inadequate-vm-resources"></a>ExtensãoFailedTimeoutVMNetworkUnresponsive - Operação snapshot falhou devido a recursos VM inadequados

Código de erro: ExtensionFailedTimeoutVMNetworkUnresponsive<br/>
Error message: A operação snapshot falhou devido a recursos VM inadequados.

A operação de backup no VM falhou devido ao atraso nas chamadas de rede durante a operação de instantâneo. Para resolver este problema, siga o Passo 1. Se o problema persistir, experimente os passos 2 e 3.

**Passo 1**: Criar instantâneo através do anfitrião

A partir de um pedido de comando elevado (administrador), executar o seguinte comando:

```console
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v SnapshotMethod /t REG_SZ /d firstHostThenGuest /f
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v CalculateSnapshotTimeFromHost /t REG_SZ /d True /f
```

Desta forma, garante-es que os instantâneos são criados através do anfitrião e não do Convidado. Repita a operação de cópia de segurança.

**Passo 2**: Tente alterar o horário de backup para uma altura em que o VM esteja menos carregado (como menos CPU ou IOps)

**Passo 3**: Tente [aumentar o tamanho do VM](https://docs.microsoft.com/azure/virtual-machines/windows/resize-vm) e reforce a operação

### <a name="320001-resourcenotfound---could-not-perform-the-operation-as-vm-no-longer-exists--400094-bcmv2vmnotfound---the-virtual-machine-doesnt-exist--an-azure-virtual-machine-wasnt-found"></a>320001, ResourceNotFound - Não foi possível realizar a operação uma vez que a VM já não existe / 400094, BCMV2VMNotFound - A máquina virtual não existe / Não foi encontrada uma máquina virtual Azure

Código de erro: 320001, ResourceNotFound <br/> Error message: Não foi possível efetuar a operação uma vez que o VM já não existe. <br/> <br/> Código de erro: 400094, BCMV2VMNotFound <br/> Error message: A máquina virtual não existe <br/>
Uma máquina virtual Azure não foi encontrada.

Este erro ocorre quando o VM primário é eliminado, mas a política de backup ainda procura um VM para fazer backup. Para corrigir este erro, tome os seguintes passos:

* Recove a máquina virtual com o mesmo nome e mesmo nome de grupo de recursos, **nome de serviço em nuvem,**<br>ou
* Pare de proteger a máquina virtual com ou sem eliminar os dados de reserva. Para obter mais informações, consulte [Parar de proteger máquinas virtuais.](backup-azure-manage-vms.md#stop-protecting-a-vm)</li></ol>

### <a name="usererrorbcmpremiumstoragequotaerror---could-not-copy-the-snapshot-of-the-virtual-machine-due-to-insufficient-free-space-in-the-storage-account"></a>UserErrorBCMPremiumStorageQuotaError - Não foi possível copiar a imagem da máquina virtual, devido a um espaço livre insuficiente na conta de armazenamento

Código de erro: UserErrorBCMPremiumStorageQuotaError<br/> Error message: Não foi possível copiar o instantâneo da máquina virtual, devido a um espaço livre insuficiente na conta de armazenamento

 Para VMs premium na pilha de backup V1 V1, copiamos o instantâneo para a conta de armazenamento. Este passo garante que o tráfego de gestão de backup, que funciona no instantâneo, não limita o número de IOPS disponíveis para a aplicação usando discos premium. <br><br>Recomendamos que aloque apenas 50%, 17,5 TB, do espaço total da conta de armazenamento. Em seguida, o serviço de backup Azure pode copiar o instantâneo para a conta de armazenamento e transferir dados deste local copiado na conta de armazenamento para o cofre.

### <a name="380008-azurevmoffline---failed-to-install-microsoft-recovery-services-extension-as-virtual-machine--is-not-running"></a>380008, AzureVmOffline - Falhou na instalação da extensão dos Serviços de Recuperação da Microsoft, uma vez que a máquina virtual não está a funcionar

Código de erro: 380008, AzureVmOffline <br/> Error message: Failed to install Microsoft Recovery Services extension as virtual machine is not running

O Agente VM é um pré-requisito para a extensão dos Serviços de Recuperação do Azure. Instale o Agente de Máquinas Virtuais Azure e reinicie a operação de registo. <br> <ol> <li>Verifique se o Agente VM está instalado corretamente. <li>Certifique-se de que a bandeira da config VM está corretamente definida.</ol> Leia mais sobre a instalação do Agente VM e como validar a instalação do Agente VM.

### <a name="extensionsnapshotbitlockererror---the-snapshot-operation-failed-with-the-volume-shadow-copy-service-vss-operation-error"></a>ExtensõesSnapshotBitlockerError - A operação instantânea falhou com o erro de funcionamento do Serviço de Cópia Sombra de Volume (VSS)

Código de erro: ExtensionSnapshotBitlockerError <br/> Mensagem de erro: A operação instantânea falhou com o erro de funcionamento do Serviço de Cópia de Sombra de Volume (VSS) **Esta unidade está bloqueada pela Encriptação de Unidade BitLocker. Tem de desbloquear esta unidade do Painel de Controlo.**

Desligue o BitLocker para todas as unidades do VM e verifique se o problema vss está resolvido.

### <a name="vmnotindesirablestate---the-vm-isnt-in-a-state-that-allows-backups"></a>VmNotInDesirableState - O VM não está num estado que permite backups

Código de erro: Estado VmNotInDesirable <br/> Error message: The VM is in a state that allows backups.

* Se o VM estiver num estado transitório entre **correr** e **desligar,** aguarde que o Estado mude. Então desencadeie o trabalho de reserva.
* Se o VM for um Linux VM e utilizar o módulo de kernel linux Security-Enhanced, exclua o caminho do Agente Azure Linux **/var/lib/waagent** da política de segurança e certifique-se de que a extensão de Backup está instalada.

* O Agente VM não está presente na máquina virtual: <br>Instale qualquer pré-requisito e o agente VM. Em seguida, reinicie a operação. | Leia mais sobre [a instalação do Agente VM e como validar a instalação do Agente VM](#vm-agent).

### <a name="extensionsnapshotfailednosecurenetwork---the-snapshot-operation-failed-because-of-failure-to-create-a-secure-network-communication-channel"></a>ExtensõesSnapshotFailedNoSecureNet - A operação snapshot falhou devido à falha na criação de um canal de comunicação de rede seguro

Código de erro: ExtensionSnapshotFailedNoSecureNet <br/> Mensagem de erro: A operação instantânea falhou devido à falha na criação de um canal de comunicação de rede seguro.

* Abra o Editor de Registo executando **regedit.exe** em modo elevado.
* Identifique todas as versões do Quadro .NET presentes no seu sistema. Estão presentes sob a hierarquia da chave do registo **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft. **
* Para cada quadro .NET presente na chave de registo, adicione a seguinte chave: <br> **SchUseStrongCrypto"=dword:0000001**. </ol>

### <a name="extensionvcredistinstallationfailure---the-snapshot-operation-failed-because-of-failure-to-install-visual-c-redistributable-for-visual-studio-2012"></a>ExtensãoVCRedistInstallationFailure - A operação snapshot falhou devido à falha na instalação visual C++ Redistributable para Visual Studio 2012

Código de erro: ExtensãoVCRedistInstallationFailure <br/> Error message: A operação snapshot falhou devido à falha na instalação do Visual C++ Redistributable para Visual Studio 2012.

* Navegue `C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot\agentVersion` e instale vcredist2013_x64.<br/>Certifique-se de que o valor da chave de registo que permite a instalação do serviço está definido para o valor correto. Ou seja, desa um valor **de Início** em **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Msiserver** para **3** e não **4**. <br><br>Se ainda tiver problemas com a instalação, reinicie o serviço de instalação executando **o MSIEXEC /UNREGISTER** seguido de **MSIEXEC /REGISTER** a partir de uma solicitação de comando elevada.
* Verifique o registo do evento para verificar se está a notar problemas relacionados com o acesso. Por exemplo: *Produto: Microsoft Visual C++ 2013 x64 Tempo mínimo de funcionação - 12.0.21005 -- Erro 1401.Não foi possível criar a chave: Software\Classes.  Erro do sistema 5.  Verifique se tem acesso suficiente a essa chave ou contacte o seu pessoal de apoio.* <br><br> Certifique-se de que o administrador ou a conta do utilizador têm permissões suficientes para atualizar a chave de registo **HKEY_LOCAL_MACHINE\SOFTWARE\Classes**. Forneça permissões suficientes e reinicie o Windows Azure Guest Agent.<br><br> <li> Se tiver produtos antivírus no lugar, certifique-se de que têm as regras de exclusão adequadas para permitir a instalação.

### <a name="usererrorrequestdisallowedbypolicy---an-invalid-policy-is-configured-on-the-vm-which-is-preventing-snapshot-operation"></a>UserErrorRequestDisallowedByPolicy – foi configurada uma política inválida na VM e está a impedir a Operação de instantâneo

Código de erro: UserErrorRequestDisallowedByPolicy <BR> Error message: Uma política inválida está configurada no VM que está a impedir o funcionamento do Snapshot.

Se tiver uma Política Azure que [regule as etiquetas dentro](../governance/policy/tutorials/govern-tags.md)do seu ambiente, considere alterar a política de um [efeito Deny](../governance/policy/concepts/effects.md#deny) para um [efeito Modificar,](../governance/policy/concepts/effects.md#modify)ou criar o grupo de recursos manualmente de acordo com o [esquema de nomeação exigido pela Azure Backup](./backup-during-vm-creation.md#azure-backup-resource-group-for-virtual-machines).

## <a name="jobs"></a>Tarefas

| Detalhes do erro | Solução |
| --- | --- |
| O cancelamento não é suportado para este tipo de trabalho: <br>Espere até o trabalho terminar. |Nenhum |
| O trabalho não está num estado de anulação: <br>Espere até o trabalho terminar. <br>**ou**<br> O trabalho selecionado não está num estado de cancelamento: <br>Espere que o trabalho termine. |É provável que o trabalho esteja quase terminado. Espere até que o trabalho termine.|
| O backup não pode cancelar o trabalho porque não está em andamento. <br>O cancelamento é suportado apenas para empregos em curso. Tente cancelar um trabalho em curso. |Este erro acontece por causa de um estado transitório. Aguarde um minuto e re-tentar a operação de cancelamento. |
| A cópia de segurança não cancelou o trabalho: <br>Espere até o trabalho terminar. |Nenhum |

## <a name="restore"></a>Restauro

### <a name="disks-appear-offline-after-file-restore"></a>Os discos aparecem offline após a Restauração do Ficheiro

Se depois de restaurado, nota que os discos estão offline então:

* Verifique se a máquina onde o script é executado satisfaz os requisitos de SO. [Saiba mais](./backup-azure-restore-files-from-vm.md#system-requirements).  
* Certifique-se de que não está a restaurar a mesma fonte, [Saiba mais](./backup-azure-restore-files-from-vm.md#original-backed-up-machine-versus-another-machine).

### <a name="usererrorinstantrpnotfound---restore-failed-because-the-snapshot-of-the-vm-was-not-found"></a>UserErrorInstantRpNotFound - Restauro falhou porque a imagem do VM não foi encontrada

Código de erro: UserErrorInstantRpNotFound <br>
Error message: Restore failed because the snapshot of the VM was not found. A foto pode ter sido apagada, por favor verifique.<br>

Este erro ocorre quando se está a tentar restaurar a partir de um ponto de recuperação que não foi transferido para o cofre e foi eliminado na fase de instantâneo. 
<br>
Para resolver este problema, tente restaurar o VM de um ponto de restauro diferente.<br>

#### <a name="common-errors"></a>Erros comuns 
| Detalhes do erro | Solução |
| --- | --- |
| Restaurar falhou com um erro interno na nuvem. |<ol><li>O serviço de nuvem a que está a tentar restaurar está configurado com as definições de DNS. Pode verificar: <br>**$deployment = Get-AzureDeployment -ServiceName "ServiceName" - Slot "Production" Get-AzureDns -DnsSettings $deployment. DnsSettings**.<br>Se **o Endereço** estiver configurado, as definições de DNS são configuradas.<br> <li>O serviço de nuvem ao qual está a tentar restaurar está configurado com **o ReservedIP**, e os VM existentes no serviço de nuvem estão no estado parado. Pode verificar se um serviço de nuvem reservou um IP utilizando os seguintes cmdlets PowerShell: **$deployment = Get-AzureDeployment -ServiceName "servicename" -Slot "Production" $dep. Nome Reservado.** <br><li>Está a tentar restaurar uma máquina virtual com as seguintes configurações especiais de rede no mesmo serviço de nuvem: <ul><li>Máquinas virtuais em configuração do balanceador de carga, internas e externas.<li>Máquinas virtuais com múltiplos IPs reservados. <li>Máquinas virtuais com vários NICs. </ul><li>Selecione um novo serviço de nuvem na UI ou consulte [considerações de restauro](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) para VMs com configurações especiais de rede.</ol> |
| O nome DNS selecionado já está tomado: <br>Especifique um nome DNS diferente e tente novamente. |Este nome DNS refere-se ao nome de serviço na nuvem, normalmente terminando com **.cloudapp.net**. Este nome tem de ser único. Se cometer este erro, tem de escolher um nome VM diferente durante a restauração. <br><br> Este erro é mostrado apenas aos utilizadores do portal Azure. A operação de restauro através da PowerShell tem sucesso porque restaura apenas os discos e não cria o VM. O erro será encarado quando o VM for explicitamente criado por si após a operação de restauro do disco. |
| A configuração de rede virtual especificada não está correta: <br>Especifique uma configuração de rede virtual diferente e tente novamente. |Nenhum |
| O serviço de nuvem especificado está a utilizar um IP reservado que não corresponde à configuração da máquina virtual que está a ser restaurada: <br>Especifique um serviço de nuvem diferente que não esteja usando um IP reservado. Ou escolha outro ponto de recuperação para restaurar. |Nenhum |
| O serviço em nuvem atingiu o seu limite no número de pontos finais de entrada: <br>Redavei o funcionamento especificando um serviço de nuvem diferente ou utilizando um ponto final existente. |Nenhum |
| O cofre dos Serviços de Recuperação e a conta de armazenamento de alvos encontram-se em duas regiões diferentes: <br>Certifique-se de que a conta de armazenamento especificada na operação de restauro se encontra na mesma região Azure que o cofre dos Serviços de Recuperação. |Nenhum |
| A conta de armazenamento especificada para a operação de restauro não é suportada: <br>Apenas são suportadas contas de armazenamento básicas ou padrão com definições de replicação localmente redundantes ou geo-redundantes. Selecione uma conta de armazenamento suportada. |Nenhum |
| O tipo de conta de armazenamento especificada para a operação de restauro não está online: <br>Certifique-se de que a conta de armazenamento especificada na operação de restauro está online. |Este erro pode ocorrer devido a um erro transitório no Azure Storage ou devido a uma paragem. Escolha outra conta de armazenamento. |
| A quota do grupo de recursos foi atingida: <br>Elimine alguns grupos de recursos do portal Azure ou contacte o Suporte Azure para aumentar os limites. |Nenhum |
| A sub-rede selecionada não existe: <br>Selecione uma sub-rede que existe. |Nenhum |
| O serviço de cópia de segurança não tem autorização para aceder a recursos na sua subscrição. |Para resolver este erro, restabelece primeiro os discos utilizando os passos em [Restaurar os discos retrossaquentes](backup-azure-arm-restore-vms.md#restore-disks). Em seguida, utilize os passos PowerShell na [Criação de um VM a partir de discos restaurados](backup-azure-vms-automation.md#restore-an-azure-vm). |

## <a name="backup-or-restore-takes-time"></a>Backup ou restauro leva tempo

Se o seu backup demorar mais de 12 horas, ou restaurar leva mais de 6 horas, reveja as [melhores práticas](backup-azure-vms-introduction.md#best-practices)e [considerações de desempenho](backup-azure-vms-introduction.md#backup-performance)

## <a name="vm-agent"></a>Agente VM

### <a name="set-up-the-vm-agent"></a>Configurar o Agente VM

Tipicamente, o Agente VM já está presente em VMs que são criados a partir da galeria Azure. Mas as máquinas virtuais que são migradas de datacenters no local não terão o Agente VM instalado. Para esses VMs, o Agente VM precisa de ser instalado explicitamente.

#### <a name="windows-vms---set-up-the-agent"></a>Windows VMs - Configurar o agente

* Transfira e instale o [MSI do agente](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Precisa de privilégios de administrador para terminar a instalação.
* Para máquinas virtuais criadas utilizando o modelo de implementação clássico, [atualize a propriedade VM](../virtual-machines/troubleshooting/install-vm-agent-offline.md#use-the-provisionguestagent-property-for-classic-vms) para indicar que o agente está instalado. Este passo não é necessário para máquinas virtuais Azure Resource Manager.

#### <a name="linux-vms---set-up-the-agent"></a>Linux VMs - Configurar o agente

* Instale a versão mais recente do agente a partir do repositório de distribuição. Para mais informações sobre o nome do pacote, consulte o [repositório do Agente Linux.](https://github.com/Azure/WALinuxAgent)
* Para VMs criados utilizando o modelo de implementação clássico, [atualize a propriedade VM](../virtual-machines/troubleshooting/install-vm-agent-offline.md#use-the-provisionguestagent-property-for-classic-vms) e verifique se o agente está instalado. Este passo não é necessário para máquinas virtuais do Gestor de Recursos.

### <a name="update-the-vm-agent"></a>Atualizar o Agente VM

#### <a name="windows-vms---update-the-agent"></a>VMs do Windows - Atualizar o agente

* Para atualizar o Agente VM, reinstale os [binários do Agente VM](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Antes de atualizar o agente, certifique-se de que não ocorrem operações de backup durante a atualização do Agente VM.

#### <a name="linux-vms---update-the-agent"></a>Linux VMs - Atualizar o agente

* Para atualizar o Agente Linux VM, siga as instruções do artigo [Atualização do Agente Linux VM](../virtual-machines/extensions/update-linux-agent.md?toc=/azure/virtual-machines/linux/toc.json).

    > [!NOTE]
    > Utilize sempre o repositório de distribuição para atualizar o agente.

    Não descarregue o código de agente do GitHub. Se o último agente não estiver disponível para a sua distribuição, contacte o suporte de distribuição para obter instruções para adquirir o mais recente agente. Também pode verificar as informações mais recentes do [agente Do Windows Azure Linux](https://github.com/Azure/WALinuxAgent/releases) no repositório GitHub.

### <a name="validate-vm-agent-installation"></a>Validar a instalação do Agente VM

Verifique a versão VM Agent nos VMs do Windows:

1. Inscreva-se na máquina virtual Azure e navegue para a pasta **C:\WindowsAzure\Packages**. Devia encontrar o ficheiro **WaAppAgent.exe.**
2. Clique com o direito no ficheiro e vá ao **Properties.** Em seguida, selecione o separador **Detalhes.** O campo **versão do produto** deve ser 2.6.1198.718 ou superior.

## <a name="troubleshoot-vm-snapshot-issues"></a>Resolver problemas de instantâneos de VM

A cópia de segurança VM baseia-se na emissão de comandos instantâneos para o armazenamento subjacente. Não ter acesso ao armazenamento ou atrasos numa execução de tarefa instantânea pode fazer com que o trabalho de backup falhe. As seguintes condições podem causar falhas de tarefa instantâneas:

* **VMs com backup sql server configurado pode causar atraso de tarefa instantânea**. Por predefinição, a cópia de segurança VM cria uma cópia de segurança completa vsS em VMs do Windows. Os VMs que executam o SQL Server, com a cópia de segurança do SQL Server configurada, podem sofrer atrasos de instantâneo. Se os atrasos de instantâneo causarem falhas de backup, desa um ponto de registo seguinte:

   ```console
   [HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
   "USEVSSCOPYBACKUP"="TRUE"
   ```

* **O estado de VM é relatado incorretamente porque o VM é desligado em PDR**. Se utilizar o ambiente de trabalho remoto para desligar a máquina virtual, verifique se o estado de VM no portal está correto. Se o estado não estiver correto, utilize a opção **de Encerramento** no painel de instrumentos VM do portal para desligar o VM.
* **Se mais de quatro VMs partilharem o mesmo serviço de nuvem, espalhe os VMs através de várias políticas de backup**. Escalonar os tempos de reserva, por isso não mais do que quatro backups VM começam ao mesmo tempo. Tente separar os tempos ini por uma hora nas apólices.
* **O VM funciona com cpu alto ou memória**. Se a máquina virtual funciona com alta memória ou utilização de CPU, mais de 90%, a sua tarefa instantânea é a sua tarefa de instantâneo estão na fila e atrasadas. Eventualmente, acaba por ser assim. Se este problema acontecer, tente um backup a pedido.

## <a name="networking"></a>Rede

O DHCP deve ser ativado dentro do hóspede para que o backup IaaS VM funcione. Se precisar de um IP estático privado, configurá-lo através do portal Azure ou PowerShell. Certifique-se de que a opção DHCP dentro do VM está ativada.
Obtenha mais informações sobre como configurar um IP estático através do PowerShell:

* [Como adicionar um IP interno estático a um VM existente](/powershell/module/az.network/set-aznetworkinterfaceipconfig#description)
* [Alterar o método de atribuição de um endereço IP privado atribuído a uma interface de rede](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface)