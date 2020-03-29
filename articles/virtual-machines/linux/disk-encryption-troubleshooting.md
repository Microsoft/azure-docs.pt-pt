---
title: Resolução de problemas de encriptação do disco azure para VMs Linux
description: Este artigo fornece dicas de resolução de problemas para encriptação do disco Microsoft Azure para VMs Linux.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: eeacea9e3305865881747801100dc17770b7df63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970493"
---
# <a name="azure-disk-encryption-troubleshooting-guide"></a>Guia de resolução de problemas de encriptação de disco azure

Este guia é para profissionais de TI, analistas de segurança da informação e administradores de nuvem cujas organizações usam encriptação de disco azure. Este artigo é para ajudar na resolução de problemas relacionados com a encriptação do disco.

Antes de dar qualquer um dos passos abaixo, certifique-se primeiro de que os VMs que está a tentar encriptar estão entre os [tamanhos de VM suportados e sistemas operativos,](disk-encryption-overview.md#supported-vms-and-operating-systems)e que preencheu todos os pré-requisitos:

- [Requisitos adicionais para VMs](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Requisitos de networking](disk-encryption-overview.md#networking-requirements)
- [Requisitos de armazenamento de chaves de encriptação](disk-encryption-overview.md#encryption-key-storage-requirements)

 

## <a name="troubleshooting-linux-os-disk-encryption"></a>Resolução de problemas da encriptação do disco Linux OS

A encriptação do disco do sistema operativo Linux (OS) deve desmontar a unidade DE SO antes de a executar através do processo completo de encriptação do disco. Se não conseguir desmontar a unidade, uma mensagem de erro de "não conseguiu desmontar depois..." é provável que ocorra.

Este erro pode ocorrer quando a encriptação do disco OS é tentada num VM com um ambiente que foi alterado a partir da imagem da galeria de stock suportada. Desvios da imagem suportada podem interferir com a capacidade da extensão de desmontar a unidade de SO. Exemplos de desvios podem incluir os seguintes itens:
- As imagens personalizadas já não correspondem a um sistema de ficheiros suportado ou a um esquema de partilha.
- Grandes aplicações como SAP, MongoDB, Apache Cassandra e Docker não são suportadas quando são instaladas e em execução no Sistema operativo antes da encriptação. A encriptação do disco azure não é capaz de desligar estes processos com segurança, conforme necessário na preparação da unidade de S Para encriptação do disco. Se ainda existirem processos ativos que mantêm as pegas de ficheiro abertas na unidade de SISTEMA, a unidade de SO não pode ser desmontada, resultando numa falha na encriptação da unidade de SO. 
- Scripts personalizados que funcionam em pouco tempo perto da encriptação que está sendo ativada, ou se quaisquer outras alterações estão sendo feitas no VM durante o processo de encriptação. Este conflito pode acontecer quando um modelo do Gestor de Recursos Azure define múltiplas extensões para executar simultaneamente, ou quando uma extensão de script personalizada ou outra ação corre simultaneamente para encriptação de disco. Serializar e isolar tais passos pode resolver o problema.
- Security Enhanced Linux (SELinux) não foi desativado antes de permitir a encriptação, pelo que o passo desmontado falha. O SELinux pode ser reativado após a encriptação estar completa.
- O disco OS utiliza um esquema de Gestor de Volume Lógico (LVM). Embora esteja disponível um suporte limitado de disco de dados LVM, um disco DE VS OS não está.
- Os requisitos mínimos de memória não são cumpridos (7 GB é sugerido para encriptação de disco OS).
- As unidades de dados são montadas novamente sob o /mnt/diretório, ou uns dos outros (por exemplo, /mnt/data1, /mnt/data2, /data3 + /data3/data4).

## <a name="update-the-default-kernel-for-ubuntu-1404-lts"></a>Atualize o núcleo padrão para Ubuntu 14.04 LTS

Os navios de imagem Ubuntu 14.04 LTS com uma versão padrão de kernel de 4.4. Esta versão kernel tem um problema conhecido no qual out of Memory Killer termina indevidamente o comando dd durante o processo de encriptação do OS. Este bug foi corrigido no mais recente kernel de Linux afinado em Azure. Para evitar este erro, antes de permitir a encriptação da imagem, atualize para o [kernel 4.15 afinado](https://packages.ubuntu.com/trusty/linux-azure) do Azure ou mais tarde utilizando os seguintes comandos:

```
sudo apt-get update
sudo apt-get install linux-azure
sudo reboot
```

Depois de o VM ter reiniciado no novo núcleo, a nova versão kernel pode ser confirmada usando:

```
uname -a
```

## <a name="update-the-azure-virtual-machine-agent-and-extension-versions"></a>Atualize as versões de Agente Virtual de Máquinas Azure e extensão

As operações de encriptação do disco Azure podem falhar em imagens de máquinas virtuais utilizando versões não suportadas do Agente De Máquina Virtual Azure. As imagens linux com versões de agente superiores a 2.2.38 devem ser atualizadas antes de permitir a encriptação. Para mais informações, consulte [Como atualizar o Agente Azure Linux num](../extensions/update-linux-agent.md) suporte de versão VM e Minimum para agentes de [máquinas virtuais em Azure](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

É também necessária a versão correta da extensão do agente convidado Microsoft.Azure.Security.AzureDiskCrypton ou Microsoft.Azure.Security.Azure.Security.AzureDiskCryptonForLinux. As versões de extensão são mantidas e atualizadas automaticamente pela plataforma quando os pré-requisitos do agente Da Máquina Virtual Do Azure estão satisfeitos e é utilizada uma versão suportada do agente virtual da máquina.

A extensão Microsoft.OSTCExtensions.AzureDiskCryptonForLinux foi depreciada e já não é suportada.  

## <a name="unable-to-encrypt-linux-disks"></a>Incapaz de encriptar discos Linux

Em alguns casos, a encriptação do disco Linux parece estar presa na encriptação do disco OS iniciada" e o SSH está desativado. O processo de encriptação pode demorar entre 3 a 16 horas para terminar numa imagem de galeria de stock. Se forem adicionados discos de dados de vários tamanhos de terabytes, o processo pode demorar dias.

A sequência de encriptação do disco Linux OS desmonta temporariamente a unidade de SO. Em seguida, executa encriptação block-by-block de todo o disco de SO, antes de o remontar no seu estado encriptado. A encriptação do disco Linux não permite o uso simultâneo do VM enquanto a encriptação está em andamento. As características de desempenho do VM podem fazer uma diferença significativa no tempo necessário para completar a encriptação. Estas características incluem o tamanho do disco e se a conta de armazenamento é de armazenamento padrão ou premium (SSD).

Para verificar o estado de encriptação, sondar o campo **ProgressMessage** devolvido do comando [Get-AzVmDiskCryptonStatus.](/powershell/module/az.compute/get-azvmdiskencryptionstatus) Enquanto a unidade de SO está a ser encriptada, o VM entra num estado de manutenção e desativa o SSH para evitar qualquer perturbação no processo em curso. A mensagem **EncryptionInProgress** reporta durante a maior parte do tempo enquanto a encriptação está em andamento. Algumas horas depois, uma mensagem **VMRestartPending** pede-lhe para reiniciar o VM. Por exemplo:


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

Depois de ser solicitado para reiniciar o VM, e depois do VM reiniciar, deve esperar 2-3 minutos para o reboot e para que os passos finais sejam realizados no alvo. A mensagem de estado muda quando a encriptação está finalmente completa. Depois de esta mensagem estar disponível, espera-se que a unidade de OS encriptada esteja pronta para ser utilizada e o VM está pronto para ser novamente utilizado.

Nos seguintes casos, recomendamos que restaure o VM de volta ao instantâneo ou cópia de segurança tirado imediatamente antes da encriptação:
   - Se a sequência de reinicialização, descrita anteriormente, não acontecer.
   - Se as informações de arranque, a mensagem de progresso ou outros indicadores de erro reportarem que a encriptação do OS falhou no meio deste processo. Um exemplo de uma mensagem é o erro "não desmontado" que é descrito neste guia.

Antes da próxima tentativa, reavalie as características do VM e certifique-se de que todos os pré-requisitos estão satisfeitos.

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>Resolução de problemas da encriptação do disco azure atrás de uma firewall

Ver [Encriptação de disco numa rede isolada](disk-encryption-isolated-network.md)

## <a name="troubleshooting-encryption-status"></a>Estado de encriptação de resolução de problemas 

O portal pode apresentar um disco como encriptado mesmo depois de não ter sido encriptado dentro do VM.  Isto pode ocorrer quando comandos de baixo nível são usados para desencriptar diretamente o disco de dentro do VM, em vez de usar os comandos de gestão de encriptação de disco de nível superior.  Os comandos de nível superior não só desencriptam o disco a partir do VM, mas também fora do VM, também atualizam definições importantes de encriptação de nível de plataforma e definições de extensão associadas ao VM.  Se estes não forem mantidos em alinhamento, a plataforma não será capaz de reportar o estado de encriptação ou fornecer o VM corretamente.   

Para desativar a encriptação do disco Azure com powerShell, utilize a [Encriptação Desactivada-AzVMDisk,](/powershell/module/az.compute/disable-azvmdiskencryption) seguida de [Remover-AzVMDiskEncryptionExtension](/powershell/module/az.compute/remove-azvmdiskencryptionextension). Executar Remover-AzVMDiskEncriptaçãoA Extensão antes da encriptação ser desativada falhará.

Para desativar a encriptação do disco Azure com CLI, utilize o [desativação de encriptação az vm](/cli/azure/vm/encryption). 

## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu mais sobre alguns problemas comuns na Encriptação do Disco Azure e como resolver esses problemas. Para obter mais informações sobre este serviço e as suas capacidades, consulte os seguintes artigos:

- [Aplicar encriptação de disco no Centro de Segurança Azure](../../security-center/security-center-apply-disk-encryption.md)
- [Encriptação de dados azure em repouso](../../security/fundamentals/encryption-atrest.md)
