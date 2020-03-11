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
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2020
ms.locfileid: "78970493"
---
# <a name="azure-disk-encryption-troubleshooting-guide"></a>Guia de resolução de problemas de encriptação de disco do Azure

Este guia destina-se a profissionais de TI, analistas de segurança de informações e os administradores de nuvem cujas organizações utilizam o Azure Disk Encryption. Este artigo é ajudar na resolução de problemas relacionados com encriptação de disco.

Antes de dar qualquer um dos passos abaixo, certifique-se primeiro de que os VMs que está a tentar encriptar estão entre os [tamanhos de VM suportados e sistemas operativos,](disk-encryption-overview.md#supported-vms-and-operating-systems)e que preencheu todos os pré-requisitos:

- [Requisitos adicionais para VMs](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Requisitos de networking](disk-encryption-overview.md#networking-requirements)
- [Requisitos de armazenamento de chaves de encriptação](disk-encryption-overview.md#encryption-key-storage-requirements)

 

## <a name="troubleshooting-linux-os-disk-encryption"></a>Resolução de problemas de encriptação de disco do SO Linux

Encriptação de disco de sistema operativo (SO) do Linux tem desmontar a unidade de sistema operacional antes de executá-lo no processo de encriptação de disco completa. Se ele não é possível desmontar a unidade, uma mensagem de erro de "Falha ao desmontar após..." é provável que ocorram.

Este erro pode ocorrer quando a encriptação do disco OS é tentada num VM com um ambiente que foi alterado a partir da imagem da galeria de stock suportada. Desvios em relação a imagem suportados podem interferir com a capacidade da extensão de desmontar a unidade do SO. Exemplos de desvios podem incluir os seguintes itens:
- Imagens personalizadas já não correspondem um sistema de ficheiros suportado ou o esquema de particionamento.
- Aplicativos grandes, como SAP, MongoDB, Apache Cassandra e a Docker não são suportados quando eles são instalados e em execução no sistema operacional antes de encriptação. O Azure Disk Encryption não consegue encerrar esses processos com segurança conforme necessário durante a preparação da unidade do SO para a encriptação de disco. Se existirem processos ainda ativos, que contém identificadores de abrir o ficheiro para a unidade de sistema operacional, a unidade do SO não pode estar desmontada, resultando numa falha ao encriptar a unidade do sistema operacional. 
- Custom scripts que são executados em proximidade fechar tempo para a encriptação ativada ou se existirem outras alterações estão a ser efetuadas na VM durante o processo de criptografia. Esse conflito pode acontecer quando um modelo Azure Resource Manager define várias extensões para executar em simultâneo, ou quando uma extensão de script personalizado ou outra ação é executada em simultâneo para encriptação de disco. Serializar e isolar esses passos poderão resolver o problema.
- Segurança avançada Linux (SELinux) não tiver sido desabilitada antes de ativar a encriptação, para que o passo de desmontar falhar. Pode ser reenabled SELinux após a conclusão da criptografia.
- O disco do SO utiliza um esquema de Gestor de volumes lógicos (LVM). Embora o suporte de disco de dados LVM limitado estiver disponível, não é um disco de SO de LVM.
- Requisitos mínimos de memória não forem cumpridos (7 GB é sugerida para a encriptação de disco do SO).
- Unidades de dados são recursivamente montada no diretório /mnt/ ou de outro (por exemplo, /mnt/data1, /mnt/data2, /data3 + /data3/data4).

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

Em alguns casos, a encriptação de disco parece estar bloqueada em "Iniciada de encriptação de disco de SO" de Linux e SSH está desativada. O processo de encriptação pode demorar entre horas de 3-16 a terminar numa imagem da Galeria das ações. Se forem adicionados discos de dados de tamanho de terabytes de transmissões, o processo pode demorar dias.

A sequência de encriptação de disco do SO Linux desmonta temporariamente a unidade do SO. Em seguida, executa encriptação de bloco a bloco do disco do SO inteiro, antes de ele remounts-lo no seu estado encriptado. A encriptação do disco Linux não permite o uso simultâneo do VM enquanto a encriptação está em andamento. As características de desempenho da VM podem fazer uma diferença significativa no tempo necessário para a encriptação completa. Essas características incluem o tamanho do disco e se a conta de armazenamento é standard ou o armazenamento premium (SSD).

Para verificar o estado de encriptação, sondar o campo **ProgressMessage** devolvido do comando [Get-AzVmDiskCryptonStatus.](/powershell/module/az.compute/get-azvmdiskencryptionstatus) Enquanto a unidade do SO está a ser encriptada, a VM entra num Estado de manutenção e desativa o SSH para evitar qualquer interrupção para o processo contínuo. A mensagem **EncryptionInProgress** reporta durante a maior parte do tempo enquanto a encriptação está em andamento. Algumas horas depois, uma mensagem **VMRestartPending** pede-lhe para reiniciar o VM. Por exemplo:


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

Depois de lhe for pedido para reiniciar a VM e, depois de reinicia a VM, tem de aguardar 2 a 3 minutos para o reinício e para os últimos passos a serem executadas no destino. As alterações de mensagem de estado quando a encriptação é finalmente concluída. Depois desta mensagem estiver disponível, a unidade do SO encriptada deve estar pronta a utilizar e a VM está pronta para ser utilizada novamente.

Nos seguintes casos, recomendamos que restaure a VM para o instantâneo ou a cópia de segurança criada imediatamente antes de encriptação:
   - Se a sequência de reinicialização, descrita anteriormente, não acontece.
   - Se as informações de arranque, mensagem de progresso ou outros indicadores de erro se a encriptação de SO de relatório não no meio esse processo. Um exemplo de uma mensagem é o erro "Falha ao desmontar" descrita neste guia.

Antes da próxima tentativa, reavaliar as características da VM e certifique-se de que todos os pré-requisitos são cumpridos.

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>Resolução de problemas do Azure Disk Encryption atrás de uma firewall

Ver [Encriptação de disco numa rede isolada](disk-encryption-isolated-network.md)

## <a name="troubleshooting-encryption-status"></a>Estado de encriptação de resolução de problemas 

O portal pode apresentar um disco como encriptado mesmo depois de não ter sido encriptado dentro do VM.  Isto pode ocorrer quando comandos de baixo nível são usados para desencriptar diretamente o disco de dentro do VM, em vez de usar os comandos de gestão de encriptação de disco de nível superior.  Os comandos de nível superior não só desencriptam o disco a partir do VM, mas também fora do VM, também atualizam definições importantes de encriptação de nível de plataforma e definições de extensão associadas ao VM.  Se estes não forem mantidos em alinhamento, a plataforma não será capaz de reportar o estado de encriptação ou fornecer o VM corretamente.   

Para desativar a encriptação do disco Azure com powerShell, utilize a [Encriptação Desactivada-AzVMDisk,](/powershell/module/az.compute/disable-azvmdiskencryption) seguida de [Remover-AzVMDiskEncryptionExtension](/powershell/module/az.compute/remove-azvmdiskencryptionextension). Executar Remover-AzVMDiskEncriptaçãoA Extensão antes da encriptação ser desativada falhará.

Para desativar a encriptação do disco Azure com CLI, utilize o [desativação de encriptação az vm](/cli/azure/vm/encryption). 

## <a name="next-steps"></a>Passos seguintes

Neste documento, ficou a saber mais sobre alguns problemas comuns no Azure Disk Encryption e como solucionar esses problemas. Para obter mais informações sobre este serviço e as respetivas capacidades, consulte os artigos seguintes:

- [Aplicar encriptação de disco no Centro de Segurança Azure](../../security-center/security-center-apply-disk-encryption.md)
- [Encriptação de dados azure em repouso](../../security/fundamentals/encryption-atrest.md)
