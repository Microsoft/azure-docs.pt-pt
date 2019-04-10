---
title: Resolver erros de cópia de segurança com máquinas virtuais do Azure
description: Resolver problemas de cópia de segurança e restauro de máquinas virtuais do Azure
services: backup
author: srinathv
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: srinathv
ms.openlocfilehash: e8b739c7b4dee67273e2f5c500c6d3b05190b3a5
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2019
ms.locfileid: "59361512"
---
# <a name="troubleshoot-azure-virtual-machine-backup"></a>Resolver problemas das cópias de segurança de máquina virtuais do Azure
Pode resolver erros encontrados ao utilizando o Azure Backup com as informações listadas na tabela a seguir:

## <a name="backup"></a>Cópia de segurança

### <a name="copyingvhdsfrombackupvaulttakinglongtime--copying-backed-up-data-from-vault-timed-out"></a>CopyingVHDsFromBackUpVaultTakingLongTime -Copying backed up data from vault timed out

Código do erro: CopyingVHDsFromBackUpVaultTakingLongTime <br/>
Mensagem de erro: Cópia de segurança dos dados do cofre foi excedido

Isto pode acontecer devido a erros de armazenamento transitório ou a conta de armazenamento insuficiente IOPS para o serviço de cópia de segurança para transferir dados para o Cofre dentro do período de tempo limite. Configurar cópia de segurança VM utilizar estes [melhores práticas](backup-azure-vms-introduction.md#best-practices) e repita a operação de cópia de segurança.

### <a name="usererrorvmnotindesirablestate---vm-is-not-in-a-state-that-allows-backups"></a>UserErrorVmNotInDesirableState - a VM não está num Estado que permita cópias de segurança.

Código do erro: UserErrorVmNotInDesirableState <br/>
Mensagem de erro: VM não está num Estado que permita cópias de segurança.<br/>

A operação de cópia de segurança falhou porque a VM está num estado falhado. Para a VM de cópia de segurança bem-sucedida Estado deve estar em execução, parado ou parada (desalocada).

* Se a VM está num estado transitório entre **em execução** e **Encerrar**, aguarde que a alteração do Estado. Em seguida, acione a tarefa de cópia de segurança.
*  Se a VM é uma VM do Linux e utiliza o módulo de kernel de Security-Enhanced Linux, excluir o caminho do agente Linux do Azure **/var/lib/waagent** da política de segurança e certifique-se a extensão de cópia de segurança é instalada.

### <a name="usererrorfsfreezefailed---failed-to-freeze-one-or-more-mount-points-of-the-vm-to-take-a-file-system-consistent-snapshot"></a>UserErrorFsFreezeFailed - falhou ao fixar um ou mais pontos de montagem da VM para tirar um instantâneo consistente do sistema de ficheiros

Código do erro: UserErrorFsFreezeFailed <br/>
Mensagem de erro: Falha ao fixar um ou mais pontos de montagem da VM para tirar um instantâneo consistente do sistema de ficheiros.

* Verifique o estado do sistema de ficheiros de todos os dispositivos montados usando o **tune2fs** por exemplo, o comando **tune2fs -l/desenvolvimento/sdb1 \\** .\| grep **deestadodosistemadeficheiros**.
* Desmonte os dispositivos para os quais não foi limpa o estado do sistema de ficheiros, com o **umount** comando.
* Execute uma verificação de consistência do sistema de ficheiros nestes dispositivos com o **fsck** comando.
* Os dispositivos de montagem novamente e repita a operação de cópia de segurança.</ol>

### <a name="extensionsnapshotfailedcom--extensioninstallationfailedcom--extensioninstallationfailedmdtc---extension-installationoperation-failed-due-to-a-com-error"></a>ExtensionSnapshotFailedCOM / ExtensionInstallationFailedCOM / ExtensionInstallationFailedMDTC - extensão instalação/operação falhou devido a um erro COM+

Código do erro: ExtensionSnapshotFailedCOM <br/>
Mensagem de erro: Operação de instantâneo falhou devido ao erro COM+

Código do erro: ExtensionInstallationFailedCOM  <br/>
Mensagem de erro: Instalação/operação de extensão falhou devido a um erro COM+

Código do erro: Mensagem de erro ExtensionInstallationFailedMDTC: A instalação da extensão falhou com o erro "COM+ não conseguiu comunicar com o coordenador de transações do Microsoft Distributed

A operação de cópia de segurança falhou devido a um problema com o serviço do Windows **COM+ sistema** aplicação.  Para resolver este problema, siga estes passos:

* Tente iniciar/reiniciar o serviço do Windows **aplicação de sistema COM+** (num prompt de comando elevado **-net iniciar COMSysApp**).
* Certifique-se **coordenador de transações distribuídas** serviços está a ser executado **serviço de rede** conta. Se não, alterá-la para ser executado como **serviço de rede** da conta e reinicie **aplicação de sistema COM+**.
* Se não for possível reiniciar o serviço, em seguida, reinstale **coordenador de transações distribuídas** service ao seguir os passos abaixo:
    * Pare o serviço MS DTC
    * Abra uma linha de comandos (cmd)
    * Execute o comando "msdtc-desinstalar"
    * comando de anulação "msdtc-instalar"
    * Inicie o serviço MS DTC
* Iniciar o serviço do Windows **aplicação de sistema COM+**. Depois do **aplicação de sistema COM+** é iniciado, acionar uma tarefa de cópia de segurança do portal do Azure.</ol>

### <a name="extensionfailedvsswriterinbadstate---snapshot-operation-failed-because-vss-writers-were-in-a-bad-state"></a>ExtensionFailedVssWriterInBadState - operação de instantâneo falhou porque eram de escritores VSS num Estado incorreto

Código do erro: ExtensionFailedVssWriterInBadState <br/>
Mensagem de erro: A operação de instantâneo falhou porque eram de escritores VSS num Estado incorreto.

Reinicie os escritores VSS que estão num Estado incorreto. A partir da linha de comandos elevada, execute ```vssadmin list writers```. A saída contém todos os escritores VSS e o respetivo estado. Para cada escritor VSS com um Estado que não seja **[1] estável**para reiniciar o escritor VSS, execute os seguintes comandos numa linha de comandos elevada:

  * ```net stop serviceName```
  * ```net start serviceName```

### <a name="extensionconfigparsingfailure--failure-in-parsing-the-config-for-the-backup-extension"></a>ExtensionConfigParsingFailure - Falha ao analisar a configuração para a extensão de cópia de segurança

Código do erro: ExtensionConfigParsingFailure<br/>
Mensagem de erro: Falha ao analisar a configuração para a extensão da cópia de segurança.

Este erro ocorre devido a permissões alteradas no **MachineKeys** directory: **%systemdrive%\programdata\microsoft\crypto\rsa\machinekeys**.
Execute o seguinte comando e certifique-se de que as permissões a **MachineKeys** diretório são valores padrão:**icacls %systemdrive%\programdata\microsoft\crypto\rsa\machinekeys**.

Permissões predefinidas são os seguintes:
* Todos os utilizadores: (R, W)
* BUILTIN\Administrators: (F)

Se vir as permissões no **MachineKeys** directory que são diferentes dos padrões, siga estes passos para as permissões corretas, eliminar o certificado e acionar a cópia de segurança:

1. Corrigir permissões o **MachineKeys** diretório. Ao utilizar as propriedades de segurança do Explorer e definições de segurança avançadas no diretório, repor permissões os valores predefinidos. Remover todos os objetos de utilizador, exceto as predefinições do diretório e certifique-se a **todas as pessoas** permissão tem acesso especial da seguinte forma:

    * Pasta/Ler dados da lista
    * Atributos de leitura
    * Atributos de leitura expandidos
    * Criar ficheiros/escrever dados
    * Criar pastas/Acrescentar dados
    * Atributos de escrita
    * Escrever atributos estendidos
    * Permissões de leitura
2. Eliminar todos os certificados em que **emitido para** é o modelo de implementação clássica ou **gerador de certificados do Windows Azure CRP**:
    * [Abra os certificados num console do computador local](https://msdn.microsoft.com/library/ms788967(v=vs.110).aspx).
    * Sob **pessoais** > **certificados**, elimine todos os certificados em que **emitido para** é o modelo de implementação clássica ou **CRP do Windows Azure Gerador de certificados**.
3. Acione uma tarefa de cópia de segurança de VM.

### <a name="extensionstuckindeletionstate---extension-state-is-not-supportive-to-backup-operation"></a>ExtensionStuckInDeletionState - o estado de extensão não é suporta a operação de cópia de segurança

Código do erro: ExtensionStuckInDeletionState <br/>
Mensagem de erro: Estado da extensão não é suporta a operação de cópia de segurança

A operação de cópia de segurança falhou devido a um estado inconsistente da extensão de cópia de segurança. Para resolver este problema, siga estes passos:

* Certifique-se de que o Agente Convidado está instalado e a responder
* No portal do Azure, aceda a **Máquina Virtual** > **Todas as Definições** > **Extensões**
* Selecione a extensão de cópia de segurança VmSnapshot ou VmSnapshotLinux e clique em **Desinstalar**
* Depois de eliminar a extensão de cópia de segurança, repita a operação de cópia de segurança
* A operação de cópia de segurança subsequente irá instalar a nova extensão no estado pretendido

### <a name="extensionfailedsnapshotlimitreachederror---snapshot-operation-failed-as-snapshot-limit-is-exceeded-for-some-of-the-disks-attached"></a>ExtensionFailedSnapshotLimitReachedError - operação de instantâneo falhou porque o limite de instantâneos foi excedido para alguns dos discos ligados

Código do erro: ExtensionFailedSnapshotLimitReachedError  <br/>
Mensagem de erro: A operação de instantâneo falhou porque o limite de instantâneos foi excedida para alguns dos discos ligados

A operação de instantâneo falhou porque o limite de instantâneos foi excedido para alguns dos discos ligados. Concluir a seguir passos e, em seguida, repita a operação de resolução de problemas.

* Elimine os BLOBs-instantâneos de disco que não são necessários. Tenha cuidado para não eliminar blob de disco, devem ser eliminados apenas blobs de instantâneo.
* Se a eliminação de forma recuperável estiver ativada no disco VM contas de armazenamento, configure a retenção de eliminação de forma recuperável de forma a que os instantâneos existentes são menos do que o máximo permitido em qualquer ponto no tempo.
* Se o Azure Site Recovery está ativado na VM com cópia de segurança, em seguida, execute o abaixo:

    * Certifique-se o valor de **isanysnapshotfailed** está definido como falso no /etc/azure/vmbackup.conf
    * Agende o Azure Site Recovery noutra altura, que ele não entram em conflito a operação de cópia de segurança.

### <a name="extensionfailedtimeoutvmnetworkunresponsive---snapshot-operation-failed-due-to-inadequate-vm-resources"></a>ExtensionFailedTimeoutVMNetworkUnresponsive - operação de instantâneo falhou devido a recursos da VM inadequados.

Código do erro: ExtensionFailedTimeoutVMNetworkUnresponsive<br/>
Mensagem de erro: Operação de instantâneo falhou devido a recursos da VM inadequados.

Operação de cópia de segurança na VM falhou devido ao atraso nas chamadas de rede ao executar a operação de instantâneo. Para resolver este problema, siga o Passo 1. Se o problema persistir, experimente os passos 2 e 3.

**Passo 1**: Criar o instantâneo através do Anfitrião

Numa linha de comandos elevada (administrador), execute o comando abaixo:

```
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v SnapshotMethod /t REG_SZ /d firstHostThenGuest /f
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v CalculateSnapshotTimeFromHost /t REG_SZ /d True /f
```

Desta forma, garante-es que os instantâneos são criados através do anfitrião e não do Convidado. Repita a operação de cópia de segurança.

**Passo 2**: Tente alterar a agenda de cópia de segurança para um tempo quando a VM está sob uma carga menor (menos CPU/IOps, etc.)

**Passo 3**: Tente [aumentar o tamanho da VM](https://azure.microsoft.com/blog/resize-virtual-machines/) e repita a operação

### <a name="common-vm-backup-errors"></a>Erros de cópia de segurança de VM comuns

| Detalhes do erro | Solução |
| ------ | --- |
| Código do erro: 320001<br/> Mensagem de erro: Não foi possível executar a operação visto que a VM já não existe. <br/> <br/> Código do erro: 400094 <br/> Mensagem de erro: A máquina virtual não existe <br/> <br/>  Não foi encontrada uma máquina virtual do Azure.  |Este erro ocorre quando a VM principal é eliminada, mas a política de cópia de segurança ainda procura de uma VM para criar cópias de segurança. Para corrigir este erro, siga os passos seguintes: <ol><li> Voltar a criar a máquina virtual com o mesmo nome e o mesmo nome do grupo de recursos, **nome do serviço cloud**,<br>**ou**</li><li> Pare a proteção da máquina virtual com ou sem eliminar os dados de cópia de segurança. Para obter mais informações, consulte [parar a proteção de máquinas virtuais](backup-azure-manage-vms.md#stop-protecting-a-vm).</li></ol>|
| A VM está no Estado com falhas de aprovisionamento: <br>Reinicie a VM e certifique-se de que a VM está em execução ou encerrada. | Este erro ocorre quando uma das falhas de extensões de coloca a VM no Estado com falhas de aprovisionamento. Vá para a lista de extensões, verifique se existe uma extensão com falha, remova-a e tente reiniciar a máquina virtual. Se todas as extensões estão no estado de execução, verifique se o serviço de agente da VM está em execução. Caso contrário, reinicie o serviço de agente da VM. |
|Código do erro: UserErrorBCMPremiumStorageQuotaError<br/> Mensagem de erro: Não foi possível copiar o instantâneo da máquina virtual, devido a insuficiente espaço livre na conta de armazenamento | Para VMs do premium na pilha de cópia de segurança de VM V1, podemos copiar o instantâneo para a conta de armazenamento. Este passo certifica-se de que o tráfego de gestão de cópia de segurança, que funciona no instantâneo, não limita o número de IOPS disponíveis para a aplicação utilizando os discos premium. <br><br>Recomendamos que atribua apenas 50 por cento, 17,5 TB, de espaço de conta de armazenamento total. Em seguida, o serviço de cópia de segurança do Azure pode copiar o instantâneo para os dados de conta e a transferência de armazenamento a partir desta localização copiada na conta de armazenamento para o cofre. |
| Falha ao instalar a extensão de serviços de recuperação da Microsoft, como a máquina virtual não está em execução <br>O agente da VM é um pré-requisito para a extensão de serviços de recuperação do Azure. Instale o agente da Máquina Virtual do Azure e reinicie a operação de registo. |<ol> <li>Verifique se o agente da VM está instalado corretamente. <li>Certifique-se de que o sinalizador sobre a configuração VM está definido corretamente.</ol> Leia mais sobre como instalar o agente da VM e como validar a instalação do agente de VM. |
| A operação de instantâneo falhou com o erro de operação do serviço de cópia de sombra de volumes (VSS) **esta unidade está bloqueada pela criptografia de unidade de disco BitLocker. Tem de desbloquear esta unidade do painel de controlo.** |Desativa o BitLocker para todas as unidades na VM e verifique se o problema VSS foi resolvido. |
| A VM não estiver num Estado que permita cópias de segurança. |<ul><li>Se a VM está num estado transitório entre **em execução** e **Encerrar**, aguarde que a alteração do Estado. Em seguida, acione a tarefa de cópia de segurança. <li> Se a VM é uma VM do Linux e utiliza o módulo de kernel de Security-Enhanced Linux, excluir o caminho do agente Linux do Azure **/var/lib/waagent** da política de segurança e certifique-se a extensão de cópia de segurança é instalada.  |
| O agente da VM não estiver presente na máquina virtual: <br>Instale quaisquer pré-requisitos e o agente da VM. Em seguida, reinicie a operação. |Leia mais sobre [instalação do agente de VM e como validar a instalação do agente de VM](#vm-agent). |
| Cópia de segurança falhou ao fixar um ou mais pontos de montagem da VM para tirar um instantâneo consistente do sistema de ficheiros. | Execute os seguintes passos: <ul><li>Verificar o estado do sistema de ficheiros de todos os dispositivos montados com o **'tune2fs'** comando. Um exemplo é **tune2fs -l/desenvolvimento/sdb1 \\** .\| grep **estado do sistema de ficheiros**. <li>Desmonte os dispositivos para o qual o estado do sistema de ficheiros não é limpo, utilizando o **'umount'** comando. <li> Execute uma verificação de consistência do sistema de ficheiros nestes dispositivos com o **'fsck'** comando. <li> Os dispositivos de montagem novamente e repita a cópia de segurança.</ol> |
| A operação de instantâneo falhou devido a falha para criar um canal de comunicação de rede segura. | <ol><li> Abra o Editor de registo, executando **regedit.exe** num modo elevado. <li> Identifique todas as versões do .NET Framework presentes no seu sistema. Eles estão presentes na hierarquia de chave de registo **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft**. <li> Para cada .NET Framework presentes na chave do registo, adicione a seguinte chave: <br> **SchUseStrongCrypto"=dword:00000001**. </ol>|
| A operação de instantâneo falhou devido a falha para instalar o Visual C++ Redistributable para Visual Studio 2012. | Navegue para C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot\agentVersion e instalar vcredist2012_x64. Certifique-se de que o valor de chave de registo que permite que esta instalação do serviço está definido para o valor correto. Ou seja, o valor da chave do registo **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Msiserver** está definida como **3** e não **4**. <br><br>Se ainda tiver problemas com a instalação, reinicie o serviço de instalação, executando **MSIEXEC /UNREGISTER** seguido **MSIEXEC /REGISTER** num prompt de comando elevado.  |


## <a name="jobs"></a>Tarefas

| Detalhes do erro | Solução |
| --- | --- |
| O cancelamento não é suportado para este tipo de tarefa: <br>Aguarde até que a tarefa é concluída. |Nenhuma |
| A tarefa não está num Estado pode ser cancelado: <br>Aguarde até que a tarefa é concluída. <br>**ou**<br> A tarefa selecionada não está num Estado pode ser cancelado: <br>Aguarde o conclusão do trabalho. |É provável que a tarefa está quase terminando. Aguarde até que a tarefa estiver concluída.|
| Cópia de segurança não é possível cancelar a tarefa porque não está em curso: <br>O cancelamento só é suportado para tarefas em curso. Tente cancelar uma tarefa em curso. |Este erro ocorre devido a um estado transitório. Aguarde um minuto e repita a operação de cancelamento. |
| Cópia de segurança falha ao cancelar a tarefa: <br>Aguarde até que a tarefa é concluída. |Nenhuma |

## <a name="restore"></a>Restauro

| Detalhes do erro | Solução |
| --- | --- |
| Falha no restauro com um erro interno de cloud. |<ol><li>O serviço em nuvem para o qual está a tentar restaurar está configurado com as definições de DNS. Pode verificar: <br>**$deployment = Get-AzureDeployment -ServiceName "ServiceName" -Slot "Production"     Get-AzureDns -DnsSettings $deployment.DnsSettings**.<br>Se **endereço** estiver configurado, as definições de DNS estão configuradas.<br> <li>O serviço de nuvem para o qual está a tentar restaurar está configurado com **ReservedIP**, e as VMs existentes no serviço cloud estão no estado de paragem. Pode verificar que um serviço em nuvem reservou a um IP utilizando os seguintes cmdlets do PowerShell: **$deployment = Get-AzureDeployment - ServiceName "servicename"-DEP de ranhura "Produção" $. ReservedIPName**. <br><li>Está a tentar restaurar uma máquina virtual com as seguintes configurações de rede especiais para o mesmo serviço cloud: <ul><li>Máquinas de virtuais em configuração de Balanceador de carga, interna e externa.<li>Máquinas virtuais com vários IPs reservados. <li>Máquinas virtuais com vários NICs. </ul><li>Selecione um novo serviço cloud na interface do Usuário ou consulte [restaurar considerações](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) para VMs com configurações de rede especiais.</ol> |
| O nome DNS selecionado já está a ser utilizado: <br>Especifique um nome DNS diferente e tente novamente. |Este nome DNS refere-se para o nome do serviço cloud, normalmente, terminando **. cloudapp.net**. Este nome tem de ser exclusivo. Se obtiver este erro, tem de escolher um nome VM diferente durante o restauro. <br><br> Este erro é mostrado apenas aos utilizadores do portal do Azure. A operação de restauro através do PowerShell é bem-sucedida, porque ele restaura apenas os discos e não cria a VM. O erro irá ser enfrentado quando a VM explicitamente é criada por si, depois da operação de restauro de disco. |
| A configuração de rede virtual especificada não está correta: <br>Especifique uma configuração de rede virtual diferente e tente novamente. |Nenhuma |
| O serviço cloud especificado está a utilizar um IP reservado que não corresponde à configuração da máquina virtual a ser restaurada: <br>Especifique um serviço cloud diferente que não está a utilizar um IP reservado. Ou escolha outro ponto de recuperação para restaurar a partir de. |Nenhuma |
| O serviço cloud atingiu o limite no número de pontos finais de entrada: <br>Repita a operação especificando um serviço cloud diferente ou através de um ponto final existente. |Nenhuma |
| A conta de armazenamento dos serviços de recuperação cofre e o destino estão em duas regiões diferentes: <br>Certifique-se de que a conta de armazenamento especificada na operação de restauro está na mesma região do Azure como o seu Cofre de serviços de recuperação. |Nenhuma |
| Não é suportada a conta de armazenamento especificada para a operação de restauro: <br>São suportadas apenas básico ou Standard contas de armazenamento com as definições de replicação localmente redundantes ou georredundantes. Selecione uma conta de armazenamento suportadas. |Nenhuma |
| O tipo de conta de armazenamento especificada para a operação de restauro não está online: <br>Certifique-se de que a conta de armazenamento especificada na operação de restauro está online. |Este erro pode acontecer devido a um erro transitório no armazenamento do Azure ou devido a uma falha. Escolha outra conta de armazenamento. |
| Atingiu a quota de grupo de recursos: <br>Elimine alguns grupos de recursos do portal do Azure ou contacte o suporte do Azure para aumentar os limites. |Nenhuma |
| A sub-rede selecionada não existe: <br>Selecione uma sub-rede existente. |Nenhuma |
| O serviço de cópia de segurança não tem autorização para aceder aos recursos na sua subscrição. |Para resolver este erro, restaurar discos utilizando os passos em [restaurar discos de cópia de segurança](backup-azure-arm-restore-vms.md#restore-disks). Em seguida, use o PowerShell os passos em [criar uma VM a partir de discos restaurados](backup-azure-vms-automation.md#restore-an-azure-vm). |

## <a name="backup-or-restore-takes-time"></a>Cópia de segurança ou restauro demora algum tempo
Se a cópia de segurança demora mais de 12 horas, ou o restauro demora mais de 6 horas, reveja [melhores práticas](backup-azure-vms-introduction.md#best-practices) e [considerações de desempenho](backup-azure-vms-introduction.md#backup-performance)

## <a name="vm-agent"></a>Agente da VM
### <a name="set-up-the-vm-agent"></a>Configurar o agente de VM
Normalmente, o agente da VM já está presente nas VMs que são criadas a partir da galeria do Azure. Mas as máquinas virtuais que são migradas dos datacenters no local não terá o agente da VM instalado. Para essas VMs, o agente de VM tem de ser instalada explicitamente.

#### <a name="windows-vms"></a>VMs do Windows

* Transfira e instale o [MSI do agente](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). São necessários privilégios de administrador para concluir a instalação.
* Para máquinas virtuais criadas com o modelo de implementação clássica [Atualize a propriedade VM](https://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) para indicar que o agente está instalado. Este passo não é necessário para as máquinas virtuais do Azure Resource Manager.

#### <a name="linux-vms"></a>VMs do Linux

* Instale a versão mais recente do agente a partir do repositório de distribuição. Para obter detalhes sobre o nome do pacote, consulte a [repositório do agente Linux](https://github.com/Azure/WALinuxAgent).
* Para as VMs criadas com o modelo de implementação clássica [Utilize este blogue](https://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) para atualizar a propriedade da VM e certifique-se de que o agente está instalado. Este passo não é necessário para as máquinas virtuais do Resource Manager.

### <a name="update-the-vm-agent"></a>Atualizar o agente da VM
#### <a name="windows-vms"></a>VMs do Windows

* Para atualizar o agente da VM, reinstalar o [binários do agente da VM](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Antes de atualizar o agente, certifique-se de que não existem operações de cópia de segurança ocorrem durante a atualização do agente de VM.

#### <a name="linux-vms"></a>VMs do Linux

* Para atualizar o agente de VM do Linux, siga as instruções no artigo [atualizar o agente de VM do Linux](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

    > [!NOTE]
    > Utilize sempre o repositório de distribuição para atualizar o agente.

    Não transferir o código do agente a partir do GitHub. Se o agente mais recente não está disponível para a sua distribuição, contacte o suporte de distribuição para obter instruções para adquirir o agente mais recente. Também pode verificar a versão mais recente [agente do Windows Azure Linux](https://github.com/Azure/WALinuxAgent/releases) informações no repositório do GitHub.

### <a name="validate-vm-agent-installation"></a>Validar a instalação do agente de VM

Certifique-se a versão do agente de VM em VMs do Windows:

1. Inicie sessão na máquina virtual do Azure e navegue para a pasta **C:\WindowsAzure\Packages**. Encontrará os **WaAppAgent.exe** ficheiro.
2. O ficheiro com o botão direito e aceda a **propriedades**. Em seguida, selecione o **detalhes** separador. O **versão do produto** campo deve ser 2.6.1198.718 ou superior.

## <a name="troubleshoot-vm-snapshot-issues"></a>Resolver problemas de instantâneos VM
Cópia de segurança VM depende de emissão de comandos de instantâneo para o armazenamento subjacente. Não ter acesso a armazenamento ou a atrasos numa tarefa de instantâneo executar pode fazer com que a tarefa de cópia de segurança efetuar a ativação. As seguintes condições podem causar a falha de tarefa de instantâneo:

- **Acesso à rede para o armazenamento é bloqueado ao utilizar o NSG**. Saiba mais sobre como [estabeleça o acesso de rede](backup-azure-arm-vms-prepare.md#establish-network-connectivity) para o armazenamento através da utilização de qualquer lista branca de IPs ou através de um servidor proxy.
- **As VMs com cópia de segurança do SQL Server configurada podem fazer com que o atraso na tarefa de instantâneo**. Por predefinição, cópia de segurança da VM cria uma VSS cópia de segurança completa em VMs do Windows. VMs que executam o SQL Server, com a cópia de segurança do SQL Server configurada, podem passar por atrasos de instantâneo. Se os atrasos de instantâneo provocar falhas de cópia de segurança, defina a seguinte chave de registo:

   ```
   [HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
   "USEVSSCOPYBACKUP"="TRUE"
   ```

- **Estado da VM é informado incorretamente porque a VM é encerrada no RDP**. Se utilizou o ambiente de trabalho remoto para encerrar a máquina virtual, certifique-se de que o estado da VM no portal do está correto. Se o estado não estiver correto, utilize o **encerramento** opção no dashboard VM do portal para encerrar a VM.
- **Se o compartilhamento de mais de quatro VMs o mesmo serviço cloud, distribuir as VMs por várias políticas de cópia de segurança**. Escalonar as horas cópia de segurança, por isso, não existem que cópias de segurança VM mais de quatro iniciar ao mesmo tempo. Tente separar as horas de início nas políticas por, pelo menos, uma hora.
- **A VM é executada às elevada da CPU ou memória**. Se a máquina virtual for executada na memória elevada ou a utilização da CPU, com mais de 90 por cento, a tarefa de instantâneo é colocado em fila e atrasada. Eventualmente, atingir o tempo limite. Se este problema acontecer, experimente uma cópia de segurança a pedido.

## <a name="networking"></a>Redes
Como todas as extensões, extensões de cópia de segurança precisam de acesso à internet pública para trabalhar. Sem acesso à Internet pública pode se manifestar de várias formas:

* A instalação da extensão pode falhar.
* Operações de cópia de segurança, como o instantâneo do disco podem falhar.
* Apresentar o estado da operação de cópia de segurança pode falhar.

A necessidade de resolver os endereços de internet pública é discutida [este blogue de suporte do Azure](https://blogs.msdn.com/b/mast/archive/2014/06/18/azure-vm-provisioning-stuck-on-quot-installing-extensions-on-virtual-machine-quot.aspx). Verifique as configurações de DNS para a VNET e certifique-se de que os URIs de Azure podem ser resolvidos.

Depois de resolução de nomes é feita corretamente, acesso a IPs do Azure também tem de ser fornecido. Para desbloquear o acesso para a infraestrutura do Azure, siga um destes passos:

- Intervalos de IP do datacenter do Azure da lista de permissões:
   1. Obter a lista de [IPs de datacenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653) de permissões.
   1. Os IPs de desbloqueio utilizando o [New-NetRoute](https://docs.microsoft.com/powershell/module/nettcpip/new-netroute) cmdlet. Execute este cmdlet dentro da VM do Azure, numa janela elevada do PowerShell. Execute como administrador.
   1. Adicione regras ao NSG, se tiver um no local, para permitir o acesso a IPs.
- Crie um caminho para o fluxo do tráfego HTTP:
   1. Se tiver algumas restrições de rede no local, implemente um servidor de proxy HTTP para encaminhar o tráfego. Um exemplo é um grupo de segurança de rede. Veja os passos para implementar um servidor de proxy HTTP na [estabelecer conectividade de rede](backup-azure-arm-vms-prepare.md#establish-network-connectivity).
   1. Adicione regras ao NSG, se tiver um no local, para permitir o acesso à internet através do proxy HTTP.

> [!NOTE]
> DHCP tem de estar ativado no computador convidado para cópia de segurança de VM de IaaS para trabalhar. Se precisar de um IP privado estático, configurá-lo através do portal do Azure ou o PowerShell. Certifique-se de que a opção de DHCP no interior da VM está ativada.
> Obter mais informações sobre como configurar um IP estático através do PowerShell:
> - [Como adicionar um IP interno estático a uma VM existente](../virtual-network/virtual-networks-reserved-private-ip.md#how-to-add-a-static-internal-ip-to-an-existing-vm)
> - [Alterar o método de alocação para um endereço IP privado atribuído a uma interface de rede](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface)
>
>
