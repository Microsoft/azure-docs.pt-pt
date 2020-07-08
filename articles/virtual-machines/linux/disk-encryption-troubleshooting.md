---
title: Encriptação do disco Azure para Os VMs de Resolução de Problemas
description: Este artigo fornece dicas de resolução de problemas para a Encriptação do Disco Azure da Microsoft para Os VMs Linux.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: eeacea9e3305865881747801100dc17770b7df63
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "78970493"
---
# <a name="azure-disk-encryption-troubleshooting-guide"></a>Guia de resolução de problemas de encriptação de disco Azure

Este guia destina-se a profissionais de TI, analistas de segurança de informação e administradores de nuvem cujas organizações utilizam encriptação de disco Azure. Este artigo é para ajudar na resolução de problemas relacionados com a encriptação do disco.

Antes de tomar qualquer um dos passos abaixo, certifique-se primeiro de que os VMs que está a tentar encriptar estão entre os [tamanhos e sistemas operativos VM suportados](disk-encryption-overview.md#supported-vms-and-operating-systems), e que cumpriu todos os pré-requisitos:

- [Requisitos adicionais para os VM](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Requisitos de rede](disk-encryption-overview.md#networking-requirements)
- [Requisitos de armazenamento de chaves de encriptação](disk-encryption-overview.md#encryption-key-storage-requirements)

 

## <a name="troubleshooting-linux-os-disk-encryption"></a>Encriptação do disco Linux OS de resolução de problemas

A encriptação do sistema operativo Linux (OS) deve desmontar a unidade de SO antes de a executar através do processo de encriptação do disco completo. Se não conseguir desmontar a unidade, uma mensagem de erro de "não foi desmontada depois..." é provável que ocorra.

Este erro pode ocorrer quando a encriptação do disco os é tentada num VM com um ambiente que foi alterado a partir da imagem de galeria de stock suportada. Os desvios da imagem suportada podem interferir com a capacidade da extensão de desmontar a unidade de SO. Exemplos de desvios podem incluir os seguintes itens:
- As imagens personalizadas já não correspondem a um sistema de ficheiros suportado ou a um esquema de partição.
- Grandes aplicações como SAP, MongoDB, Apache Cassandra e Docker não são suportadas quando são instaladas e em execução no SISTEMA antes da encriptação. A Encriptação do Disco Azure não é capaz de desligar estes processos com segurança, conforme necessário na preparação da unidade de SISTEMA para encriptação de disco. Se ainda existirem processos ativos que contenha alças de ficheiro abertas para a unidade de sistema operativo, a unidade de SO não pode ser desmontada, resultando numa falha na encriptação da unidade de sistema operativo. 
- Scripts personalizados que funcionam em tempo próximo perto da encriptação ativada, ou se quaisquer outras alterações estão sendo feitas no VM durante o processo de encriptação. Este conflito pode ocorrer quando um modelo do Gestor de Recursos Azure define múltiplas extensões para executar simultaneamente, ou quando uma extensão de script personalizada ou outra ação é executada simultaneamente para encriptação de disco. Serializar e isolar tais passos pode resolver a questão.
- O Linux Melhorado de Segurança (SELinux) não foi desativado antes de permitir a encriptação, pelo que o passo desmontagem falha. O SELinux pode ser reentransivelado após a encriptação estar completa.
- O disco SO utiliza um esquema de Gestor de Volume Lógico (LVM). Embora o suporte limitado do disco de dados LVM esteja disponível, um disco LVM OS não está.
- Os requisitos mínimos de memória não são cumpridos (7 GB é sugerido para encriptação do disco DE).
- As unidades de dados são montadas de forma recorrente sob o diretório /mnt/, ou entre si (por exemplo, /mnt/data1, /mnt/data2, /data3 + /data3/data4).

## <a name="update-the-default-kernel-for-ubuntu-1404-lts"></a>Atualize o núcleo padrão para Ubuntu 14.04 LTS

A imagem Ubuntu 14.04 LTS envia com uma versão padrão de kernel de 4.4. Esta versão kernel tem um problema conhecido no qual o Assassino de Memória termina indevidamente o comando dd durante o processo de encriptação do SO. Este bug foi corrigido no mais recente núcleo linux afinado do Azure. Para evitar este erro, antes de ativar a encriptação na imagem, atualize o [núcleo sintonizado Azure 4.15](https://packages.ubuntu.com/trusty/linux-azure) ou posteriormente utilizando os seguintes comandos:

```
sudo apt-get update
sudo apt-get install linux-azure
sudo reboot
```

Depois de o VM ter reiniciado no novo núcleo, a nova versão do kernel pode ser confirmada utilizando:

```
uname -a
```

## <a name="update-the-azure-virtual-machine-agent-and-extension-versions"></a>Atualizar o Agente de Máquina Virtual Azure e versões de extensão

As operações de encriptação do disco Azure podem falhar em imagens de máquinas virtuais utilizando versões não suportadas do Agente de Máquina Virtual Azure. As imagens Linux com versões de agente antes de 2.2.38 devem ser atualizadas antes de permitir a encriptação. Para obter mais informações, consulte [Como atualizar o Agente Azure Linux num](../extensions/update-linux-agent.md) suporte de versão VM e Mínimo para agentes de [máquinas virtuais em Azure](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

É também necessária a versão correta da extensão do agente de hóspedes Microsoft.Azure.Security.AzureDiskEncryption ou Microsoft.Azure.Security.AzureDiskEncryptionForLinux. As versões de extensão são mantidas e atualizadas automaticamente pela plataforma quando os pré-requisitos do agente Azure Virtual Machine estão satisfeitos e uma versão suportada do agente de máquina virtual é utilizada.

A extensão Microsoft.OSTCExtensions.AzureDiskEncryptionForLinux foi depreciada e já não está suportada.  

## <a name="unable-to-encrypt-linux-disks"></a>Incapaz de encriptar discos Linux

Em alguns casos, a encriptação do disco Linux parece estar presa na "encriptação do disco OS iniciada" e sSH é desativada. O processo de encriptação pode demorar entre 3 a 16 horas para terminar numa imagem de galeria de stock. Se forem adicionados discos de dados multi-terabytes, o processo pode demorar dias.

A sequência de encriptação do disco Linux OS desmonta temporariamente a unidade de SISTEMA. Em seguida, executa encriptação block-by-block de todo o disco DE, antes que ele o remonte no seu estado encriptado. A Encriptação do Disco Linux não permite o uso simultâneo do VM enquanto a encriptação estiver em andamento. As características de desempenho do VM podem fazer uma diferença significativa no tempo necessário para completar a encriptação. Estas características incluem o tamanho do disco e se a conta de armazenamento é de série ou de armazenamento premium (SSD).

Para verificar o estado de encriptação, poll o campo **ProgressMessage** devolvido do comando [Get-AzVmDiskEncryptionStatus.](/powershell/module/az.compute/get-azvmdiskencryptionstatus) Enquanto a unidade de SO está a ser encriptada, o VM entra num estado de manutenção e desativa o SSH para evitar qualquer perturbação no processo em curso. A mensagem **EncryptionInProgress** reporta a maior parte do tempo enquanto a encriptação está em andamento. Algumas horas depois, uma mensagem **VMRestartPending** solicita-lhe que reinicie o VM. Por exemplo:


```azurepowershell
PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyVirtualMachineResourceGroup" -VMName "VirtualMachineName"
OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk encryption started

PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyVirtualMachineResourceGroup" -VMName "VirtualMachineName"
OsVolumeEncrypted          : VMRestartPending
DataVolumesEncrypted       : Encrypted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk successfully encrypted, please reboot the VM
```

Depois de ser solicitado para reiniciar o VM, e depois do recomeço do VM, deve esperar 2-3 minutos para o reboot e para que os passos finais sejam executados no alvo. A mensagem de estado muda quando a encriptação está finalmente completa. Depois desta mensagem estar disponível, espera-se que a unidade de SISTEMA encriptada esteja pronta para ser utilizada e o VM está pronto para ser novamente utilizado.

Nos seguintes casos, recomendamos que volte a restaurar o VM na fotografia ou cópia de segurança tirada imediatamente antes da encriptação:
   - Se a sequência de reinicialização, descrita anteriormente, não acontecer.
   - Se as informações de arranque, mensagem de progresso ou outros indicadores de erro reportarem que a encriptação do SO falhou no meio deste processo. Um exemplo de uma mensagem é o erro "não desmontado" que é descrito neste guia.

Antes da próxima tentativa, reavalie as características do VM e certifique-se de que todos os pré-requisitos estão satisfeitos.

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>Resolução de problemas Azure Disk Encriptação atrás de uma firewall

Ver [Encriptação do Disco numa rede isolada](disk-encryption-isolated-network.md)

## <a name="troubleshooting-encryption-status"></a>Estado de encriptação de resolução de problemas 

O portal pode apresentar um disco encriptado mesmo depois de ter sido desencriptado dentro do VM.  Isto pode ocorrer quando comandos de baixo nível são usados para desencriptar diretamente o disco a partir do VM, em vez de usar os comandos de gestão de encriptação de disco Azure de nível superior.  Os comandos de nível superior não só desencriptam o disco a partir do VM, mas também fora do VM, também atualizam importantes definições de encriptação de nível da plataforma e definições de extensão associadas ao VM.  Se estes não forem mantidos em alinhamento, a plataforma não será capaz de reportar o estado de encriptação ou a provisionação adequada do VM.   

Para desativar a encriptação do disco Azure com PowerShell, utilize [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) seguido por [Remove-AzVMDiskEncryptionExtension](/powershell/module/az.compute/remove-azvmdiskencryptionextension). Executar Remove-AzVMDiskEncryptionExtension antes de a encriptação ser desativada falhará.

Para desativar a encriptação do disco Azure com O CLI, utilize [desativação de encriptação az vm](/cli/azure/vm/encryption). 

## <a name="next-steps"></a>Próximos passos

Neste documento, aprendeu mais sobre alguns problemas comuns na Encriptação do Disco Azure e como resolver esses problemas. Para obter mais informações sobre este serviço e as suas capacidades, consulte os seguintes artigos:

- [Aplicar encriptação de disco no Azure Security Center](../../security-center/security-center-apply-disk-encryption.md)
- [Encriptação de dados Azure em repouso](../../security/fundamentals/encryption-atrest.md)
