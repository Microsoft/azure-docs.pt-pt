---
title: Solucionar problemas de erros de backup com máquinas virtuais do Azure
description: Solucionar problemas de backup e restauração de máquinas virtuais do Azure
ms.reviewer: srinathv
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 08/30/2019
ms.author: dacurwin
ms.openlocfilehash: 1b3d02d5cfdae2f196f2f35f075dd8c250b5ece1
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70860337"
---
# <a name="troubleshooting-backup-failures-on-azure-virtual-machines"></a>Solucionando problemas de falhas de backup em máquinas virtuais do Azure

Você pode solucionar os erros encontrados ao usar o backup do Azure com as informações listadas abaixo:

## <a name="backup"></a>Criar cópia de segurança

Esta seção aborda a falha da operação de backup da máquina virtual do Azure.

### <a name="basic-troubleshooting"></a>Resolução de problemas básicos

* Verifique se o agente de VM (agente de WA) é a [versão mais recente](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms-prepare#install-the-vm-agent).
* Verifique se há suporte para a versão do so da VM do Windows ou Linux, consulte a [matriz de suporte de backup da VM IaaS](https://docs.microsoft.com/azure/backup/backup-support-matrix-iaas).
* Verifique se outro serviço de backup não está em execução.
   * Para garantir que não haja problemas de extensão de instantâneo, [desinstale as extensões para forçar a recarga e repita o backup](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout#the-backup-extension-fails-to-update-or-load).
* Verifique se a VM tem conectividade com a Internet.
   * Verifique se outro serviço de backup não está em execução.
* Em `Services.msc`, verifique se o serviço do **agente convidado do Windows Azure** está **em execução**. Se o serviço do **agente convidado do Windows Azure** estiver ausente, instale-o de [fazer backup de VMs do Azure em um cofre dos serviços de recuperação](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms-prepare#install-the-vm-agent).
* O **log de eventos** pode mostrar falhas de backup que são de outros produtos de backup, por exemplo, backup do Windows Server e não são devidos ao backup do Azure. Use as etapas a seguir para determinar se o problema é com o backup do Azure:
   * Se houver um erro com um **backup** de entrada na mensagem ou origem do evento, verifique se os backups de backup da VM IaaS do Azure foram bem-sucedidos e se um ponto de restauração foi criado com o tipo de instantâneo desejado.
    * Se o backup do Azure estiver funcionando, é provável que o problema tenha outra solução de backup.
    * Aqui está um exemplo de um erro do Visualizador de eventos em que o backup do Azure estava funcionando bem, mas "Backup do Windows Server" estava falhando:<br>
    ![Backup do Windows Server falhando](media/backup-azure-vms-troubleshoot/windows-server-backup-failing.png)
    * Se o backup do Azure estiver falhando, procure o código de erro correspondente na seção erros comuns de backup da VM neste artigo.

## <a name="common-issues"></a>Problemas comuns

A seguir estão problemas comuns com falhas de backup em máquinas virtuais do Azure.

## <a name="copyingvhdsfrombackupvaulttakinglongtime---copying-backed-up-data-from-vault-timed-out"></a>CopyingVHDsFromBackUpVaultTakingLongTime-a cópia dos dados de backup do cofre atingiu o tempo limite

Código de erro: CopyingVHDsFromBackUpVaultTakingLongTime <br/>
Mensagem de erro: A cópia dos dados de backup do cofre atingiu o tempo limite

Isso pode ocorrer devido a erros de armazenamento transitórios ou IOPS de conta de armazenamento insuficiente para o serviço de backup transferir dados para o cofre dentro do período de tempo limite. Configure o backup de VM usando essas [práticas recomendadas](backup-azure-vms-introduction.md#best-practices) e repita a operação de backup.

## <a name="usererrorvmnotindesirablestate---vm-is-not-in-a-state-that-allows-backups"></a>UserErrorVmNotInDesirableState-a VM não está em um estado que permita backups.

Código de erro: UserErrorVmNotInDesirableState <br/>
Mensagem de erro: A VM não está num estado que permita cópias de segurança.<br/>

A operação de backup falhou porque a VM está em estado de falha. Para backup bem-sucedido, o estado da VM deve estar em execução, parado ou parado (desalocado).

* Se a VM estiver em um estado transitório entre **executar** e **desligar**, aguarde o estado ser alterado. Em seguida, dispare o trabalho de backup.
* Se a VM for uma VM do Linux e usar o módulo kernel do Linux com segurança avançada, exclua o caminho do agente Linux do Azure **/var/lib/waagent** da política de segurança e verifique se a extensão de backup está instalada.

## <a name="usererrorfsfreezefailed---failed-to-freeze-one-or-more-mount-points-of-the-vm-to-take-a-file-system-consistent-snapshot"></a>UserErrorFsFreezeFailed-falha ao congelar um ou mais pontos de montagem da VM para obter um instantâneo consistente do sistema de arquivos

Código de erro: UserErrorFsFreezeFailed <br/>
Mensagem de erro: Falha ao fixar um ou mais pontos de montagem da VM para tirar um instantâneo consistente do sistema de ficheiros.

* Verifique o estado do sistema de arquivos de todos os dispositivos montados usando o comando **tune2fs** , por exemplo,\| **tune2fs-l \\/dev/sdb1** . grep **FileSystem State**.
* Desmonte os dispositivos para os quais o estado do sistema de arquivos não foi limpo, usando o comando **umount** .
* Execute uma verificação de consistência do sistema de arquivos nesses dispositivos usando o comando **fsck** .
* Monte os dispositivos novamente e repita a operação de backup.</ol>

## <a name="extensionsnapshotfailedcom--extensioninstallationfailedcom--extensioninstallationfailedmdtc---extension-installationoperation-failed-due-to-a-com-error"></a>Falha na instalação/operação de ExtensionSnapshotFailedCOM/ExtensionInstallationFailedCOM/ExtensionInstallationFailedMDTC-Extension devido a um erro COM+

Código de erro: ExtensionSnapshotFailedCOM <br/>
Mensagem de erro: Falha na operação de instantâneo devido a erro COM+

Código de erro: ExtensionInstallationFailedCOM  <br/>
Mensagem de erro: Falha na instalação/operação da extensão devido a um erro COM+

Código de erro: ExtensionInstallationFailedMDTC <br/>
Mensagem de erro: Falha na instalação da extensão com o erro "COM+ não pôde se comunicar com o Coordenador de Transações Distribuídas da Microsoft <br/>

A operação de backup falhou devido a um problema com o aplicativo de **sistema com+** do serviço do Windows.  Para resolver este problema, siga estes passos:

* Tente iniciar/reiniciar o **aplicativo do sistema com+** do serviço do Windows (em um prompt de comando com privilégios elevados **– net start COMSysApp**).
* Verifique se os serviços de **Coordenador de transações distribuídas** estão sendo executados como conta de **serviço de rede** . Caso contrário, altere-o para executar como conta de **serviço de rede** e reinicie o **aplicativo de sistema com+** .
* Se não for possível reiniciar o serviço, reinstale **Coordenador de transações distribuídas** serviço seguindo as etapas abaixo:
    * Pare o serviço MS DTC
    * Abra uma linha de comandos (cmd)
    * Execute o comando "MSDTC-Uninstall"
    * desfazer comando "MSDTC-install"
    * Inicie o serviço MS DTC
* Inicie o aplicativo de **sistema com+** do serviço do Windows. Depois que o **aplicativo de sistema do com+** for iniciado, dispare um trabalho de backup do portal do Azure.</ol>

## <a name="extensionfailedvsswriterinbadstate---snapshot-operation-failed-because-vss-writers-were-in-a-bad-state"></a>Falha na operação ExtensionFailedVssWriterInBadState-snapshot porque os gravadores VSS estavam em um estado inadequado

Código de erro: ExtensionFailedVssWriterInBadState <br/>
Mensagem de erro: Falha na operação de instantâneo porque os gravadores VSS estavam em um estado inadequado.

Reinicie os gravadores VSS que estão em um estado inadequado. Em um prompt de comandos com privilégios ```vssadmin list writers```elevados, execute. A saída contém todos os gravadores VSS e seu estado. Para cada gravador VSS com um estado que não é **[1] estável**, para reiniciar o gravador VSS, execute os seguintes comandos em um prompt de comando elevado:

  * ```net stop serviceName```
  * ```net start serviceName```

## <a name="extensionconfigparsingfailure--failure-in-parsing-the-config-for-the-backup-extension"></a>ExtensionConfigParsingFailure-falha ao analisar a configuração da extensão de backup

Código de erro: ExtensionConfigParsingFailure<br/>
Mensagem de erro: Falha ao analisar a configuração para a extensão da cópia de segurança.

Esse erro ocorre devido às permissões alteradas no diretório **MachineKeys** : **%systemdrive%\ProgramData\Microsoft\Crypto\RSA\MachineKeys**.
Execute o comando a seguir e verifique se as permissões no diretório **MachineKeys** são padrão:**icacls%systemdrive%\ProgramData\Microsoft\Crypto\RSA\MachineKeys**.

As permissões padrão são as seguintes:
* Mundo (R, W)
* Builtin\administradores (F)

Se você vir permissões no diretório **MachineKeys** que são diferentes dos padrões, siga estas etapas para corrigir as permissões, excluir o certificado e disparar o backup:

1. Corrija as permissões no diretório **MachineKeys** . Usando as propriedades de segurança do Explorer e as configurações de segurança avançadas no diretório, redefina as permissões de volta para os valores padrão. Remova todos os objetos de usuário, exceto os padrões do diretório, e certifique-se de que a permissão **Everyone** tenha acesso especial da seguinte maneira:

    * Listar pasta/ler dados
    * Atributos de leitura
    * Ler atributos estendidos
    * Criar arquivos/gravar dados
    * Criar pastas/acrescentar dados
    * Atributos de gravação
    * Gravar atributos estendidos
    * Permissões de leitura
2. Exclua todos os certificados em que o é **emitido para** é o modelo de implantação clássico ou o **gerador de certificado do Microsoft Azure CRP**:
    * [Abra certificados em um console do computador local](https://msdn.microsoft.com/library/ms788967(v=vs.110).aspx).
    * Em**certificados** **pessoais** > , exclua todos os certificados em que o **emitido para** é o modelo de implantação clássico ou o **gerador de certificado do Microsoft Azure CRP**.
3. Disparar um trabalho de backup de VM.

## <a name="extensionstuckindeletionstate---extension-state-is-not-supportive-to-backup-operation"></a>ExtensionStuckInDeletionState-o estado da extensão não é de apoio para a operação de backup

Código de erro: ExtensionStuckInDeletionState <br/>
Mensagem de erro: O estado da extensão não é de apoio à operação de backup

A operação de backup falhou devido ao estado inconsistente da extensão de backup. Para resolver este problema, siga estes passos:

* Certifique-se de que o Agente Convidado está instalado e a responder
* No portal do Azure, aceda a **Máquina Virtual** > **Todas as Definições** > **Extensões**
* Selecione a extensão de cópia de segurança VmSnapshot ou VmSnapshotLinux e clique em **Desinstalar**
* Depois de eliminar a extensão de cópia de segurança, repita a operação de cópia de segurança
* A operação de cópia de segurança subsequente irá instalar a nova extensão no estado pretendido

## <a name="extensionfailedsnapshotlimitreachederror---snapshot-operation-failed-as-snapshot-limit-is-exceeded-for-some-of-the-disks-attached"></a>Falha na operação ExtensionFailedSnapshotLimitReachedError-snapshot porque o limite de instantâneos foi excedido para alguns dos discos anexados

Código de erro: ExtensionFailedSnapshotLimitReachedError <br/>
Mensagem de erro: A operação de instantâneo falhou porque o limite de instantâneos foi excedido para alguns dos discos anexados

A operação de instantâneo falhou porque o limite de instantâneos foi excedido para alguns dos discos anexados. Conclua as etapas de solução de problemas abaixo e repita a operação.

* Exclua o blob de disco-instantâneos que não são necessários. Tenha cuidado para não excluir o blob de disco, somente os blobs de instantâneo devem ser excluídos.
* Se a exclusão reversível estiver habilitada no armazenamento em disco da VM, configure a retenção de exclusão reversível de modo que os instantâneos existentes sejam menores do que o máximo permitido a qualquer momento.
* Se Azure Site Recovery estiver habilitado na VM de backup, execute o seguinte:

    * Verifique se o valor de **isanysnapshotfailed** está definido como false em/etc/Azure/VMBackup.conf
    * Agendar Azure Site Recovery em um momento diferente, de modo que ele não entra em conflito com a operação de backup.

## <a name="extensionfailedtimeoutvmnetworkunresponsive---snapshot-operation-failed-due-to-inadequate-vm-resources"></a>Falha na operação ExtensionFailedTimeoutVMNetworkUnresponsive-snapshot devido a recursos inadequados da VM.

Código de erro: ExtensionFailedTimeoutVMNetworkUnresponsive<br/>
Mensagem de erro: Falha na operação de instantâneo devido a recursos inadequados da VM.

Falha na operação de backup na VM devido a atraso em chamadas de rede ao executar a operação de instantâneo. Para resolver este problema, siga o Passo 1. Se o problema persistir, experimente os passos 2 e 3.

**Etapa 1**: Criar o instantâneo através do Anfitrião

Numa linha de comandos elevada (administrador), execute o comando abaixo:

```text
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v SnapshotMethod /t REG_SZ /d firstHostThenGuest /f
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v CalculateSnapshotTimeFromHost /t REG_SZ /d True /f
```

Desta forma, garante-es que os instantâneos são criados através do anfitrião e não do Convidado. Repita a operação de cópia de segurança.

**Etapa 2**: Tente alterar o agendamento de backup para uma hora em que a VM esteja sob menos carga (menos CPU/IOps, etc.)

**Etapa 3**: Tente [aumentar o tamanho da VM](https://azure.microsoft.com/blog/resize-virtual-machines/) e repita a operação

## <a name="common-vm-backup-errors"></a>Erros comuns de backup da VM

| Detalhes do erro | Solução |
| ------ | --- |
| **Código de erro**: 320001<br/> **Mensagem de erro**: Não foi possível executar a operação visto que a VM já não existe. <br/> <br/> **Código de erro**: 400094 <br/> **Mensagem de erro**: A máquina virtual não existe <br/> <br/>  Uma máquina virtual do Azure não foi encontrada.  |Esse erro ocorre quando a VM primária é excluída, mas a política de backup ainda procura por uma VM para fazer backup. Para corrigir esse erro, execute as seguintes etapas: <ol><li> Recriar a máquina virtual com o mesmo nome e o mesmo nome do grupo de recursos, **nome do serviço de nuvem**,<br>**ou**</li><li> Interrompa a proteção da máquina virtual com ou sem excluir os dados de backup. Para obter mais informações, consulte [parar de proteger máquinas virtuais](backup-azure-manage-vms.md#stop-protecting-a-vm).</li></ol>|
| A VM está em estado de provisionamento com falha: <br>Reinicie a VM e verifique se a VM está em execução ou desligada. | Esse erro ocorre quando uma das falhas de extensão coloca a VM no estado de provisionamento com falha. Vá para a lista de extensões, verifique se há uma extensão com falha, remova-a e tente reiniciar a máquina virtual. Se todas as extensões estiverem em estado de execução, verifique se o serviço do agente de VM está em execução. Caso contrário, reinicie o serviço do agente de VM. |
|**Código de erro**: UserErrorBCMPremiumStorageQuotaError<br/> **Mensagem de erro**: Não foi possível copiar o instantâneo da máquina virtual devido a espaço livre insuficiente na conta de armazenamento | Para VMs Premium na pilha de backup da VM v1, copiamos o instantâneo para a conta de armazenamento. Essa etapa garante que o tráfego de gerenciamento de backup, que funciona no instantâneo, não limite o número de IOPS disponíveis para o aplicativo usando discos Premium. <br><br>É recomendável que você aloque apenas 50%, 17,5 TB, do espaço total da conta de armazenamento. Em seguida, o serviço de backup do Azure pode copiar o instantâneo para a conta de armazenamento e transferir dados desse local copiado na conta de armazenamento para o cofre. |
| **Código de erro: 380008** <br/> **Mensagem de erro**: Falha ao instalar a extensão dos serviços de recuperação da Microsoft porque a máquina virtual não está em execução | O agente de VM é um pré-requisito para a extensão dos serviços de recuperação do Azure. Instale o agente de máquina virtual do Azure e reinicie a operação de registro. <br> <ol> <li>Verifique se o agente de VM está instalado corretamente. <li>Verifique se o sinalizador na configuração da VM está definido corretamente.</ol> Leia mais sobre como instalar o agente de VM e como validar a instalação do agente de VM. |
| **Código de erro**: ExtensionSnapshotBitlockerError <br/> **Mensagem de erro**: A operação de instantâneo falhou com o erro **de operação de serviço de cópias de sombra de volume (VSS) esta unidade está bloqueada pelo criptografia de unidade de disco BitLocker. Você deve desbloquear esta unidade no painel de controle.** |Desative o BitLocker para todas as unidades na VM e verifique se o problema do VSS foi resolvido. |
| **Código de erro**: VmNotInDesirableState <br/> **Mensagem de erro**:  A VM não está em um estado que permita backups. |<ul><li>Se a VM estiver em um estado transitório entre **executar** e **desligar**, aguarde o estado ser alterado. Em seguida, dispare o trabalho de backup. <li> Se a VM for uma VM do Linux e usar o módulo kernel do Linux com segurança avançada, exclua o caminho do agente Linux do Azure **/var/lib/waagent** da política de segurança e verifique se a extensão de backup está instalada.  |
| O agente de VM não está presente na máquina virtual: <br>Instale qualquer pré-requisito e o agente de VM. Em seguida, reinicie a operação. |Leia mais sobre [a instalação do agente de VM e como validar a instalação do agente de VM](#vm-agent). |
| **Código de erro**: ExtensionSnapshotFailedNoSecureNetwork <br/> **Mensagem de erro**: A operação de instantâneo falhou devido à falha na criação de um canal de comunicação de rede seguro. | <ol><li> Abra o editor do Registro executando **regedit. exe** em um modo com privilégios elevados. <li> Identifique todas as versões do .NET Framework presentes no seu sistema. Eles estão presentes na hierarquia da chave do registro **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft**. <li> Para cada .NET Framework presente na chave do registro, adicione a seguinte chave: <br> **SchUseStrongCrypto "= DWORD: 00000001**. </ol>|
| **Código de erro**: ExtensionVCRedistInstallationFailure <br/> **Mensagem de erro**: A operação de instantâneo falhou devido à falha na instalação C++ de pacotes redistribuíveis do visual Studio 2012. | Navegue até C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot\agentVersion e instale o vcredist2013_x64.<br/>Verifique se o valor da chave do registro que permite a instalação do serviço está definido com o valor correto. Ou seja, defina o valor **inicial** em **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Msiserver** como **3** e não **4**. <br><br>Se você ainda tiver problemas com a instalação, reinicie o serviço de instalação executando **msiexec/Unregister** seguido por **msiexec/Register** em um prompt de comandos com privilégios elevados.  |


## <a name="jobs"></a>Tarefas

| Detalhes do erro | Solução |
| --- | --- |
| O cancelamento não tem suporte para este tipo de trabalho: <br>Aguarde até que o trabalho seja concluído. |Nenhum |
| O trabalho não está em um estado cancelável: <br>Aguarde até que o trabalho seja concluído. <br>**ou**<br> O trabalho selecionado não está em um estado cancelável: <br>Aguarde a conclusão do trabalho. |É provável que o trabalho esteja quase concluído. Aguarde até que o trabalho seja concluído.|
| O backup não pode cancelar o trabalho porque ele não está em andamento: <br>O cancelamento tem suporte apenas para trabalhos em andamento. Tente cancelar um trabalho em andamento. |Esse erro ocorre devido a um estado transitório. Aguarde um minuto e repita a operação de cancelamento. |
| O backup falhou ao cancelar o trabalho: <br>Aguarde até que o trabalho seja concluído. |Nenhum |

## <a name="restore"></a>Restaurar

| Detalhes do erro | Solução |
| --- | --- |
| Falha na restauração com um erro interno na nuvem. |<ol><li>O serviço de nuvem ao qual você está tentando restaurar está configurado com as configurações de DNS. Você pode verificar: <br>**$Deployment = Get-AzureDeployment-ServiceName "ServiceName"-slot "produção" Get-AzureDns-DnsSettings $Deployment. DnsSettings**.<br>Se o **endereço** estiver configurado, as configurações de DNS serão configuradas.<br> <li>O serviço de nuvem ao qual você está tentando restaurar está configurado com o **ReservedIP**, e as VMs existentes no serviço de nuvem estão no estado parado. Você pode verificar se um serviço de nuvem reservou um IP usando os seguintes cmdlets do PowerShell: **$Deployment = Get-AzureDeployment-ServiceName "ServiceName"-slot "Production" $Dep. ReservedIPName**. <br><li>Você está tentando restaurar uma máquina virtual com as seguintes configurações de rede especiais no mesmo serviço de nuvem: <ul><li>Máquinas virtuais sob configuração do balanceador de carga, interna e externa.<li>Máquinas virtuais com vários IPs reservados. <li>Máquinas virtuais com várias NICs. </ul><li>Selecione um novo serviço de nuvem na interface do usuário ou consulte as [considerações de restauração](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) para VMs com configurações de rede especiais.</ol> |
| O nome DNS selecionado já está em uso: <br>Especifique um nome DNS diferente e tente novamente. |Esse nome DNS se refere ao nome do serviço de nuvem, geralmente terminando com **. cloudapp.net**. Esse nome precisa ser exclusivo. Se você receber esse erro, precisará escolher um nome de VM diferente durante a restauração. <br><br> Esse erro é mostrado somente para os usuários do portal do Azure. A operação de restauração por meio do PowerShell é realizada com sucesso porque restaura apenas os discos e não cria a VM. O erro será enfrentado quando a VM for criada explicitamente por você após a operação de restauração de disco. |
| A configuração de rede virtual especificada não está correta: <br>Especifique uma configuração de rede virtual diferente e tente novamente. |Nenhum |
| O serviço de nuvem especificado está usando um IP reservado que não corresponde à configuração da máquina virtual que está sendo restaurada: <br>Especifique um serviço de nuvem diferente que não esteja usando um IP reservado. Ou escolha outro ponto de recuperação do qual restaurar. |Nenhum |
| O serviço de nuvem atingiu seu limite no número de pontos de extremidade de entrada: <br>Repita a operação especificando um serviço de nuvem diferente ou usando um ponto de extremidade existente. |Nenhum |
| O cofre dos serviços de recuperação e a conta de armazenamento de destino estão em duas regiões diferentes: <br>Verifique se a conta de armazenamento especificada na operação de restauração está na mesma região do Azure que o cofre dos serviços de recuperação. |Nenhum |
| Não há suporte para a conta de armazenamento especificada para a operação de restauração: <br>Há suporte apenas para contas de armazenamento básicas ou padrão com configurações de replicação com redundância local ou com redundância geográfica. Selecione uma conta de armazenamento com suporte. |Nenhum |
| O tipo de conta de armazenamento especificado para a operação de restauração não está online: <br>Verifique se a conta de armazenamento especificada na operação de restauração está online. |Esse erro pode ocorrer devido a um erro transitório no armazenamento do Azure ou devido a uma interrupção. Escolha outra conta de armazenamento. |
| A cota do grupo de recursos foi atingida: <br>Exclua alguns grupos de recursos do portal do Azure ou entre em contato com o suporte do Azure para aumentar os limites. |Nenhum |
| A sub-rede selecionada não existe: <br>Selecione uma sub-rede que exista. |Nenhum |
| O serviço de backup não tem autorização para acessar recursos em sua assinatura. |Para resolver esse erro, primeiro restaure os discos usando as etapas em [restaurar discos de backup](backup-azure-arm-restore-vms.md#restore-disks). Em seguida, use as etapas do PowerShell em [criar uma VM de discos restaurados](backup-azure-vms-automation.md#restore-an-azure-vm). |

## <a name="backup-or-restore-takes-time"></a>O backup ou a restauração leva tempo
Se o backup levar mais de 12 horas ou a restauração demorar mais de 6 horas, examine [as práticas recomendadas](backup-azure-vms-introduction.md#best-practices) e [considerações de desempenho](backup-azure-vms-introduction.md#backup-performance)

## <a name="vm-agent"></a>Agente de VM
### <a name="set-up-the-vm-agent"></a>Configurar o agente de VM
Normalmente, o agente de VM já está presente em VMs criadas na galeria do Azure. Mas as máquinas virtuais que são migradas de datacenters locais não terão o agente de VM instalado. Para essas VMs, o agente de VM precisa ser instalado explicitamente.

#### <a name="windows-vms"></a>VMs do Windows

* Transfira e instale o [MSI do agente](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Você precisa de privilégios de administrador para concluir a instalação.
* Para máquinas virtuais criadas usando o modelo de implantação clássico, [atualize a propriedade VM](https://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) para indicar que o agente está instalado. Esta etapa não é necessária para Azure Resource Manager máquinas virtuais.

#### <a name="linux-vms"></a>VMs do Linux

* Instale a versão mais recente do agente do repositório de distribuição. Para obter detalhes sobre o nome do pacote, consulte o [repositório do agente do Linux](https://github.com/Azure/WALinuxAgent).
* Para VMs criadas usando o modelo de implantação clássico, [Use este blog](https://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) para atualizar a propriedade VM e verificar se o agente está instalado. Esta etapa não é necessária para máquinas virtuais do Resource Manager.

### <a name="update-the-vm-agent"></a>Atualizar o agente de VM
#### <a name="windows-vms"></a>VMs do Windows

* Para atualizar o agente de VM, reinstale os [binários do agente de VM](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Antes de atualizar o agente, certifique-se de que nenhuma operação de backup ocorra durante a atualização do agente de VM.

#### <a name="linux-vms"></a>VMs do Linux

* Para atualizar o agente de VM do Linux, siga as instruções no artigo [atualizando o agente de VM do Linux](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

    > [!NOTE]
    > Sempre use o repositório de distribuição para atualizar o agente.

    Não baixe o código do agente do GitHub. Se o agente mais recente não estiver disponível para sua distribuição, entre em contato com o suporte de distribuição para obter instruções para adquirir o agente mais recente. Você também pode verificar as informações mais recentes do [agente Linux do Windows Azure](https://github.com/Azure/WALinuxAgent/releases) no repositório github.

### <a name="validate-vm-agent-installation"></a>Validar a instalação do agente de VM

Verifique a versão do agente de VM em VMs do Windows:

1. Entre na máquina virtual do Azure e navegue até a pasta **C:\WindowsAzure\Packages**. Você deve encontrar o arquivo **WaAppAgent. exe** .
2. Clique com o botão direito do mouse no arquivo e vá para **Propriedades**. Em seguida, selecione a guia **detalhes** . O campo **versão do produto** deve ser 2.6.1198.718 ou superior.

## <a name="troubleshoot-vm-snapshot-issues"></a>Solucionar problemas de instantâneo de VM
O backup da VM depende de emitir comandos de instantâneo para o armazenamento subjacente. Não ter acesso ao armazenamento ou atrasos em uma execução de tarefa de instantâneo pode fazer com que o trabalho de backup falhe. As condições a seguir podem causar falha na tarefa de instantâneo:

- O **acesso à rede para armazenamento é bloqueado usando NSG**. Saiba mais sobre como [estabelecer o acesso à rede](backup-azure-arm-vms-prepare.md#establish-network-connectivity) para armazenamento usando a lista de IPs permitidos ou por meio de um servidor proxy.
- **VMs com SQL Server Backup configurado podem causar atraso na tarefa de instantâneo**. Por padrão, o backup da VM cria um backup completo do VSS em VMs do Windows. As VMs que executam o SQL Server, com SQL Server Backup configurado, podem ter atrasos de instantâneos. Se os atrasos de instantâneo causarem falhas de backup, defina a seguinte chave do registro:

   ```text
   [HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
   "USEVSSCOPYBACKUP"="TRUE"
   ```

- **O status da VM é relatado incorretamente porque a VM está desligada no RDP**. Se você usou a área de trabalho remota para desligar a máquina virtual, verifique se o status da VM no portal está correto. Se o status não estiver correto, use a opção de **desligamento** no painel da VM do portal para desligar a VM.
- **Se mais de quatro VMs compartilharem o mesmo serviço de nuvem, distribua as VMs entre várias políticas de backup**. Escalonar os tempos de backup, portanto, no máximo quatro backups de VM são iniciados ao mesmo tempo. Tente separar as horas de início nas políticas por pelo menos uma hora.
- **A VM é executada em alta CPU ou memória**. Se a máquina virtual for executada com alta utilização de memória ou CPU, mais de 90%, sua tarefa de instantâneo será enfileirada e atrasada. Eventualmente, ele expira. Se esse problema ocorrer, tente um backup sob demanda.

## <a name="networking"></a>Redes
Assim como todas as extensões, as extensões de backup precisam acessar a Internet pública para funcionar. Não ter acesso à Internet pública pode se manifestar de várias maneiras:

* A instalação da extensão pode falhar.
* Operações de backup como instantâneo de disco podem falhar.
* A exibição do status da operação de backup pode falhar.

A necessidade de resolver endereços da Internet pública é discutida neste [blog de suporte do Azure](https://blogs.msdn.com/b/mast/archive/2014/06/18/azure-vm-provisioning-stuck-on-quot-installing-extensions-on-virtual-machine-quot.aspx). Verifique as configurações de DNS para a VNET e verifique se os URIs do Azure podem ser resolvidos.

Após a resolução de nomes ser feita corretamente, o acesso aos IPs do Azure também precisa ser fornecido. Para desbloquear o acesso à infraestrutura do Azure, siga uma destas etapas:

- Lista de permissões de intervalos de IP do datacenter do Azure:
   1. Obtenha a lista de [IPS do datacenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653) para estar na lista de permissões.
   1. Desbloqueie os IPs usando o cmdlet [New-netroute](https://docs.microsoft.com/powershell/module/nettcpip/new-netroute) . Execute este cmdlet dentro da VM do Azure, em uma janela do PowerShell com privilégios elevados. Execute como administrador.
   1. Adicione regras ao NSG, se você tiver um em vigor, para permitir o acesso aos IPs.
- Crie um caminho para o tráfego HTTP fluir:
   1. Se você tiver alguma restrição de rede em vigor, implante um servidor proxy HTTP para rotear o tráfego. Um exemplo é um grupo de segurança de rede. Consulte as etapas para implantar um servidor proxy HTTP em [estabelecer conectividade de rede](backup-azure-arm-vms-prepare.md#establish-network-connectivity).
   1. Adicione regras ao NSG, se você tiver um em vigor, para permitir o acesso à Internet por meio do proxy HTTP.

> [!NOTE]
> O DHCP deve ser habilitado dentro do convidado para que o backup da VM IaaS funcione. Se você precisar de um IP privado estático, configure-o por meio do portal do Azure ou do PowerShell. Verifique se a opção DHCP dentro da VM está habilitada.
> Obtenha mais informações sobre como configurar um IP estático por meio do PowerShell:
> - [Como adicionar um IP interno estático a uma VM existente](../virtual-network/virtual-networks-reserved-private-ip.md#how-to-add-a-static-internal-ip-to-an-existing-vm)
> - [Alterar o método de alocação para um endereço IP privado atribuído a uma interface de rede](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface)
>
>
