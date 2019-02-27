---
title: Resolver erros de cópia de segurança com máquinas virtuais do Azure
description: Resolver problemas de cópia de segurança e restauro de máquinas virtuais do Azure
services: backup
author: srinathv
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 8/7/2018
ms.author: srinathv
ms.openlocfilehash: 057057b155c6c48dabd0b97b3f6a57f3ee4c7cc1
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56888955"
---
# <a name="troubleshoot-azure-virtual-machine-backup"></a>Resolver problemas das cópias de segurança de máquina virtuais do Azure
Pode resolver erros encontrados ao utilizando o Azure Backup com as informações listadas na tabela a seguir:

| Detalhes do erro | Solução |
| ------ | --- |
| Cópia de segurança não foi possível efetuar a operação porque a máquina virtual (VM) já não existe: <br>Pare a proteção da máquina virtual sem eliminar dados de cópia de segurança. Para obter mais informações, consulte [parar a proteção de máquinas virtuais](http://go.microsoft.com/fwlink/?LinkId=808124). |Este erro ocorre quando a VM principal é eliminada, mas a política de cópia de segurança ainda procura de uma VM para criar cópias de segurança. Para corrigir este erro, siga os passos seguintes: <ol><li> Voltar a criar a máquina virtual com o mesmo nome e o mesmo nome do grupo de recursos, **nome do serviço cloud**,<br>**ou**</li><li> Pare a proteção da máquina virtual com ou sem eliminar os dados de cópia de segurança. Para obter mais informações, consulte [parar a proteção de máquinas virtuais](https://go.microsoft.com/fwlink/?LinkId=808124).</li></ol> |
| O agente da Máquina Virtual do Azure (VM Agent) não é possível comunicar com o serviço de cópia de segurança do Azure: <br>Certifique-se a VM tem conectividade de rede e o agente da VM é a versão mais recente e em execução. Para obter mais informações, consulte [falha de resolução de problemas de cópia de segurança do Azure: Problemas com o agente ou a extensão](http://go.microsoft.com/fwlink/?LinkId=800034). |Este erro acontece se ocorrer um problema com o agente da VM, ou acesso de rede para a infraestrutura do Azure é bloqueado de alguma forma. Saiba mais sobre [problemas de instantâneos de depuração VM](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#UserErrorGuestAgentStatusUnavailable-vm-agent-unable-to-communicate-with-azure-backup). <br><br>Se o agente da VM não está a causar problemas, reinicie a VM. Um Estado incorreto da VM pode causar problemas e reiniciar a VM repõe o estado. |
| A VM está no Estado com falhas de aprovisionamento: <br>Reinicie a VM e certifique-se de que a VM está em execução ou encerrada. | Este erro ocorre quando uma das falhas de extensões de coloca a VM no Estado com falhas de aprovisionamento. Vá para a lista de extensões, verifique se existe uma extensão com falha, remova-a e tente reiniciar a máquina virtual. Se todas as extensões estão no estado de execução, verifique se o serviço de agente da VM está em execução. Caso contrário, reinicie o serviço de agente da VM. |
| Cópia de segurança não foi possível copiar o instantâneo da máquina virtual devido a espaço livre suficiente na conta de armazenamento: <br>Certifique-se de que a conta de armazenamento tem espaço livre igual aos dados presentes nos discos de armazenamento premium ligados à máquina virtual. | Para VMs do premium na pilha de cópia de segurança de VM V1, podemos copiar o instantâneo para a conta de armazenamento. Este passo certifica-se de que o tráfego de gestão de cópia de segurança, que funciona no instantâneo, não limita o número de IOPS disponíveis para a aplicação utilizando os discos premium. <br><br>Recomendamos que atribua apenas 50 por cento, 17,5 TB, de espaço de conta de armazenamento total. Em seguida, o serviço de cópia de segurança do Azure pode copiar o instantâneo para os dados de conta e a transferência de armazenamento a partir desta localização copiada na conta de armazenamento para o cofre. |
| Cópia de segurança não é possível efetuar a operação, como o agente da VM não é reativo. |Este erro acontece se ocorrer um problema com o agente da VM ou o acesso de rede para a infraestrutura do Azure é bloqueado de alguma forma. Para VMs do Windows, verifique o estado do serviço de agente da VM nos serviços e se o agente é apresentado em programas no painel de controlo. <br><br>Tente remover o programa de painel de controle e reinstalar o agente, conforme descrito em [agente da VM](#vm-agent). Depois de reinstalar o agente, acione uma cópia de segurança ad hoc para o confirmar. |
| A operação de extensão de serviços de recuperação falhou: <br>Certifique-se de que o agente de VM mais recente está presente na máquina virtual e o serviço de agente da VM está em execução. Repita a operação de cópia de segurança. Se a operação de cópia de segurança falhar, contacte o Support da Microsoft. |Este erro ocorre quando o agente da VM está desatualizado. Consulte a cópia de segurança de máquina virtual de resolução de problemas do Azure para atualizar o agente da VM. |
| A máquina virtual não existe: <br>Certifique-se que a máquina virtual existe ou selecione uma máquina virtual diferente. |Este erro ocorre quando a VM principal é eliminada, mas a política de cópia de segurança ainda procura de uma VM para criar cópias de segurança. Para corrigir este erro, siga os passos seguintes: <ol><li> Voltar a criar a máquina virtual com o mesmo nome e o mesmo nome do grupo de recursos, **nome do serviço cloud**,<br>**ou**<br></li><li>Pare a proteção da máquina virtual sem eliminar os dados de cópia de segurança. Para obter mais informações, consulte [parar a proteção de máquinas virtuais](https://go.microsoft.com/fwlink/?LinkId=808124).</li></ol> |
| O comando de execução falhou: <br>Outra operação está atualmente em curso neste item. Aguarde até que seja concluída a operação anterior. Em seguida, repita a operação. |Está a executar uma tarefa de cópia de segurança existente e uma nova tarefa não é possível iniciar até que seja concluída a tarefa atual. |
| Copiar VHDs a partir do Cofre de serviços de recuperação excedido o limite: <br>Repita a operação dentro de alguns minutos. Se o problema persistir, contacte o Suporte da Microsoft. | Este erro ocorre se existir um erro transitório no lado do armazenamento, ou se o serviço de cópia de segurança não receber a conta de armazenamento suficiente IOPS para transferir dados para o cofre, dentro do período de tempo limite. Certifique-se seguir a [melhores práticas quando configurar as suas VMs](backup-azure-vms-introduction.md#best-practices). Mover a sua VM para outra conta de armazenamento que não é carregada e repita a tarefa de cópia de segurança.|
| Cópia de segurança falhou com um erro interno: <br>Repita a operação dentro de alguns minutos. Se o problema persistir, contacte o Suporte da Microsoft. |Obter este erro por dois motivos: <ul><li> Existe um problema transitório ao aceder ao armazenamento de VM. Verifique os [site do Estado do Azure](https://azure.microsoft.com/status/) para ver se existem computação, armazenamento ou problemas de rede na região. Depois de resolvido o problema, repita a tarefa de cópia de segurança. <li> A VM original foi eliminada e não é possível efetuar o ponto de recuperação. Para manter os dados de cópia de segurança para uma VM eliminada, mas remova os erros de cópia de segurança, desproteger a VM e escolha a opção para reter os dados. Esta ação para a tarefa de cópia de segurança agendada e as mensagens de erro recorrentes. |
| Cópia de segurança falha ao instalar a extensão de serviços de recuperação do Azure no item selecionado: <br>O agente da VM é um pré-requisito para a extensão de serviços de recuperação do Azure. Instale o agente da Máquina Virtual do Azure e reinicie a operação de registo. |<ol> <li>Verifique se o agente da VM está instalado corretamente. <li>Certifique-se de que o sinalizador sobre a configuração VM está definido corretamente.</ol> Leia mais sobre como instalar o agente da VM e como validar a instalação do agente de VM. |
| A instalação da extensão falhou com o erro **COM+ não conseguiu comunicar com o coordenador de transações distribuídas da Microsoft**. |Este erro normalmente, isto significa que o serviço COM+ não está em execução. Entre em contato com Support da Microsoft para obter ajuda com a corrigir este problema. |
| A operação de instantâneo falhou com o erro de operação do serviço de cópia de sombra de volumes (VSS) **esta unidade está bloqueada pela criptografia de unidade de disco BitLocker. Tem de desbloquear esta unidade do painel de controlo.** |Desativa o BitLocker para todas as unidades na VM e verifique se o problema VSS foi resolvido. |
| A VM não estiver num Estado que permita cópias de segurança. |<ul><li>Se a VM está num estado transitório entre **em execução** e **Encerrar**, aguarde que a alteração do Estado. Em seguida, acione a tarefa de cópia de segurança. <li> Se a VM é uma VM do Linux e utiliza o módulo de kernel de Security-Enhanced Linux, excluir o caminho do agente Linux do Azure **/var/lib/waagent** da política de segurança e certifique-se a extensão de cópia de segurança é instalada.  |
| Não foi encontrada uma máquina virtual do Azure. |Este erro ocorre quando a VM principal é eliminada, mas a política de cópia de segurança ainda procura-se a VM eliminada. Corrigi este erro da seguinte forma: <ol><li>Voltar a criar a máquina virtual com o mesmo nome e o mesmo nome do grupo de recursos, **nome do serviço cloud**, <br>**ou** <li> Desative a proteção para esta VM, para que as tarefas de cópia de segurança não serão criadas. </ol> |
| O agente da VM não estiver presente na máquina virtual: <br>Instale quaisquer pré-requisitos e o agente da VM. Em seguida, reinicie a operação. |Leia mais sobre [instalação do agente de VM e como validar a instalação do agente de VM](#vm-agent). |
| A operação de instantâneo falhou porque foram de escritores VSS num Estado incorreto. |Reinicie os escritores VSS que estão num Estado incorreto. A partir da linha de comandos elevada, execute ```vssadmin list writers```. A saída contém todos os escritores VSS e o respetivo estado. Para cada escritor VSS com um Estado que não seja **[1] estável**para reiniciar o escritor VSS, execute os seguintes comandos numa linha de comandos elevada: <ol><li>```net stop serviceName``` <li> ```net start serviceName```</ol>|
| A operação de instantâneo falhou devido a uma falha de análise da configuração. |Este erro ocorre devido a permissões alteradas no **MachineKeys** directory: **%systemdrive%\programdata\microsoft\crypto\rsa\machinekeys**. <br> Execute o seguinte comando e certifique-se de que as permissões a **MachineKeys** diretório são valores padrão:<br>**ICACLS %systemdrive%\programdata\microsoft\crypto\rsa\machinekeys**. <br><br>Permissões predefinidas são os seguintes: <ul><li>Todos os utilizadores: (R, W) <li>BUILTIN\Administrators: (F)</ul> Se vir as permissões no **MachineKeys** directory que são diferentes dos padrões, siga estes passos para as permissões corretas, eliminar o certificado e acionar a cópia de segurança: <ol><li>Corrigir permissões o **MachineKeys** diretório. Ao utilizar as propriedades de segurança do Explorer e definições de segurança avançadas no diretório, repor permissões os valores predefinidos. Remover todos os objetos de utilizador, exceto as predefinições do diretório e certifique-se a **todas as pessoas** permissão tem acesso especial da seguinte forma: <ul><li>Pasta/Ler dados da lista <li>Atributos de leitura <li>Atributos de leitura expandidos <li>Criar ficheiros/escrever dados <li>Criar pastas/Acrescentar dados<li>Atributos de escrita<li>Escrever atributos estendidos<li>Permissões de leitura </ul><li>Eliminar todos os certificados em que **emitido para** é o modelo de implementação clássica ou **gerador de certificados do Windows Azure CRP**:<ol><li>[Abra os certificados num console do computador local](https://msdn.microsoft.com/library/ms788967(v=vs.110).aspx).<li>Sob **pessoais** > **certificados**, elimine todos os certificados em que **emitido para** é o modelo de implementação clássica ou **CRP do Windows Azure Gerador de certificados**.</ol> <li>Acione uma tarefa de cópia de segurança de VM. </ol>|
| O serviço de cópia de segurança do Azure não tem permissões suficientes para o Azure Key Vault para cópia de segurança de máquinas virtuais encriptadas. |Fornecer o serviço de cópia de segurança estas permissões no PowerShell, utilizando os passos em [criar uma VM a partir de discos restaurados](backup-azure-vms-automation.md). |
|Instalação da extensão de instantâneo falhou com o erro **COM+ não conseguiu comunicar com o coordenador de transações distribuídas da Microsoft**. | A partir da linha de comandos elevada, inicie o serviço do Windows **aplicação de sistema COM+**. Um exemplo é **net iniciar COMSysApp**. Se o serviço não iniciar, em seguida, siga os passos seguintes:<ol><li> Certifique-se a conta de início de sessão do serviço **coordenador de transações distribuídas** é **serviço de rede**. Se não for, altere a conta de início de sessão para **serviço de rede** e reinicie o serviço. Em seguida, tente iniciar **aplicação de sistema COM+**.<li>Se **aplicação de sistema COM+** não iniciar, siga os passos seguintes para desinstalar e instalar o serviço **coordenador de transações distribuídas**: <ol><li>Pare o serviço MSDTC. <li>Abra uma linha de comandos **cmd**. <li>Execute o comando ```msdtc -uninstall```. <li>Execute o comando ```msdtc -install```. <li>Inicie o serviço MSDTC. </ol> <li>Iniciar o serviço do Windows **aplicação de sistema COM+**. Depois do **aplicação de sistema COM+** é iniciado, acionar uma tarefa de cópia de segurança do portal do Azure.</ol> |
|  A operação de instantâneo falhou devido a um erro COM+. | Recomendamos que reiniciar o serviço do Windows **aplicação de sistema COM+** da linha de comandos elevada, **net iniciar COMSysApp**. Se o problema persistir, reinicie a VM. Se reiniciar a VM não ajudar, tente [removendo a extensão de VMSnapshot](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout) e acionar a cópia de segurança manualmente. |
| Cópia de segurança falhou ao fixar um ou mais pontos de montagem da VM para tirar um instantâneo consistente do sistema de ficheiros. | Execute os seguintes passos: <ul><li>Verificar o estado do sistema de ficheiros de todos os dispositivos montados com o **'tune2fs'** comando. Um exemplo é **tune2fs -l/desenvolvimento/sdb1 \** .| GREP **estado do sistema de ficheiros**. <li>Desmonte os dispositivos para o qual o estado do sistema de ficheiros não é limpo, utilizando o **'umount'** comando. <li> Execute uma verificação de consistência do sistema de ficheiros nestes dispositivos com o **'fsck'** comando. <li> Os dispositivos de montagem novamente e repita a cópia de segurança.</ol> |
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
| Falha no restauro com um erro interno de cloud. |<ol><li>O serviço em nuvem para o qual está a tentar restaurar está configurado com as definições de DNS. Pode verificar: <br>**$deployment = Get-AzureDeployment -ServiceName "ServiceName" -Slot "Production"     Get-AzureDns -DnsSettings $deployment.DnsSettings**.<br>Se **endereço** estiver configurado, as definições de DNS estão configuradas.<br> <li>O serviço de nuvem para o qual está a tentar restaurar está configurado com **ReservedIP**, e as VMs existentes no serviço cloud estão no estado de paragem. Pode verificar que um serviço em nuvem reservou a um IP utilizando os seguintes cmdlets do PowerShell: **$deployment = Get-AzureDeployment - ServiceName "servicename"-DEP de ranhura "Produção" $. ReservedIPName**. <br><li>Está a tentar restaurar uma máquina virtual com as seguintes configurações de rede especiais para o mesmo serviço cloud: <ul><li>Máquinas de virtuais em configuração de Balanceador de carga, interna e externa.<li>Máquinas virtuais com vários IPs reservados. <li>Máquinas virtuais com vários NICs. </ul><li>Selecione um novo serviço cloud na interface do Usuário ou consulte [restaurar considerações](backup-azure-arm-restore-vms.md#restore-vms-with-special-network-configurations) para VMs com configurações de rede especiais.</ol> |
| O nome DNS selecionado já está a ser utilizado: <br>Especifique um nome DNS diferente e tente novamente. |Este nome DNS refere-se para o nome do serviço cloud, normalmente, terminando **. cloudapp.net**. Este nome tem de ser exclusivo. Se obtiver este erro, tem de escolher um nome VM diferente durante o restauro. <br><br> Este erro é mostrado apenas aos utilizadores do portal do Azure. A operação de restauro através do PowerShell é bem-sucedida, porque ele restaura apenas os discos e não cria a VM. O erro irá ser enfrentado quando a VM explicitamente é criada por si, depois da operação de restauro de disco. |
| A configuração de rede virtual especificada não está correta: <br>Especifique uma configuração de rede virtual diferente e tente novamente. |Nenhuma |
| O serviço cloud especificado está a utilizar um IP reservado que não corresponde à configuração da máquina virtual a ser restaurada: <br>Especifique um serviço cloud diferente que não está a utilizar um IP reservado. Ou escolha outro ponto de recuperação para restaurar a partir de. |Nenhuma |
| O serviço cloud atingiu o limite no número de pontos finais de entrada: <br>Repita a operação especificando um serviço cloud diferente ou através de um ponto final existente. |Nenhuma |
| A conta de armazenamento dos serviços de recuperação cofre e o destino estão em duas regiões diferentes: <br>Certifique-se de que a conta de armazenamento especificada na operação de restauro está na mesma região do Azure como o seu Cofre de serviços de recuperação. |Nenhuma |
| Não é suportada a conta de armazenamento especificada para a operação de restauro: <br>São suportadas apenas básico ou Standard contas de armazenamento com as definições de replicação localmente redundantes ou georredundantes. Selecione uma conta de armazenamento suportadas. |Nenhuma |
| O tipo de conta de armazenamento especificada para a operação de restauro não está online: <br>Certifique-se de que a conta de armazenamento especificada na operação de restauro está online. |Este erro pode acontecer devido a um erro transitório no armazenamento do Azure ou devido a uma falha. Escolha outra conta de armazenamento. |
| Atingiu a quota de grupo de recursos: <br>Elimine alguns grupos de recursos do portal do Azure ou contacte o suporte do Azure para aumentar os limites. |Nenhuma |
| A sub-rede selecionada não existe: <br>Selecione uma sub-rede existente. |Nenhuma |
| O serviço de cópia de segurança não tem autorização para aceder aos recursos na sua subscrição. |Para resolver este erro, restaurar discos utilizando os passos em [restaurar discos de cópia de segurança](backup-azure-arm-restore-vms.md#create-new-restore-disks). Em seguida, use o PowerShell os passos em [criar uma VM a partir de discos restaurados](backup-azure-vms-automation.md#restore-an-azure-vm). |

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
