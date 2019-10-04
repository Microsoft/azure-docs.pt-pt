---
title: Solução de problemas-Azure Disk Encryption para VMs Linux
description: Este artigo fornece dicas de solução de problemas para Microsoft Azure a criptografia de disco para VMs do Linux.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 864c806f146d7da4e45cc2b58159e5cad23364f8
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828721"
---
# <a name="azure-disk-encryption-troubleshooting-guide"></a>Guia de resolução de problemas de encriptação de disco do Azure

Este guia destina-se a profissionais de TI, analistas de segurança de informações e os administradores de nuvem cujas organizações utilizam o Azure Disk Encryption. Este artigo é ajudar na resolução de problemas relacionados com encriptação de disco.

Antes de executar qualquer uma das etapas a seguir, primeiro verifique se as VMs que você está tentando criptografar estão entre os [tamanhos de VM e sistemas operacionais com suporte](disk-encryption-overview.md#supported-vms-and-operating-systems)e se você atendeu a todos os pré-requisitos:

- [Requisitos adicionais para VMs](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Requisitos de rede](disk-encryption-overview.md#networking-requirements)
- [Requisitos de armazenamento de chave de criptografia](disk-encryption-overview.md#encryption-key-storage-requirements)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="troubleshooting-linux-os-disk-encryption"></a>Resolução de problemas de encriptação de disco do SO Linux

Encriptação de disco de sistema operativo (SO) do Linux tem desmontar a unidade de sistema operacional antes de executá-lo no processo de encriptação de disco completa. Se ele não é possível desmontar a unidade, uma mensagem de erro de "Falha ao desmontar após..." é provável que ocorram.

Esse erro pode ocorrer quando a criptografia de disco do sistema operacional é tentada em uma VM com um ambiente que foi alterado da imagem da Galeria de estoque com suporte. Desvios em relação a imagem suportados podem interferir com a capacidade da extensão de desmontar a unidade do SO. Exemplos de desvios podem incluir os seguintes itens:
- Imagens personalizadas já não correspondem um sistema de ficheiros suportado ou o esquema de particionamento.
- Aplicativos grandes, como SAP, MongoDB, Apache Cassandra e a Docker não são suportados quando eles são instalados e em execução no sistema operacional antes de encriptação. O Azure Disk Encryption não consegue encerrar esses processos com segurança conforme necessário durante a preparação da unidade do SO para a encriptação de disco. Se existirem processos ainda ativos, que contém identificadores de abrir o ficheiro para a unidade de sistema operacional, a unidade do SO não pode estar desmontada, resultando numa falha ao encriptar a unidade do sistema operacional. 
- Custom scripts que são executados em proximidade fechar tempo para a encriptação ativada ou se existirem outras alterações estão a ser efetuadas na VM durante o processo de criptografia. Esse conflito pode acontecer quando um modelo Azure Resource Manager define várias extensões para executar em simultâneo, ou quando uma extensão de script personalizado ou outra ação é executada em simultâneo para encriptação de disco. Serializar e isolar esses passos poderão resolver o problema.
- Segurança avançada Linux (SELinux) não tiver sido desabilitada antes de ativar a encriptação, para que o passo de desmontar falhar. Pode ser reenabled SELinux após a conclusão da criptografia.
- O disco do SO utiliza um esquema de Gestor de volumes lógicos (LVM). Embora o suporte de disco de dados LVM limitado estiver disponível, não é um disco de SO de LVM.
- Requisitos mínimos de memória não forem cumpridos (7 GB é sugerida para a encriptação de disco do SO).
- Unidades de dados são recursivamente montada no diretório /mnt/ ou de outro (por exemplo, /mnt/data1, /mnt/data2, /data3 + /data3/data4).

## <a name="update-the-default-kernel-for-ubuntu-1404-lts"></a>Atualizar o kernel padrão para Ubuntu 14, 4 LTS

A imagem do Ubuntu 14, 4 LTS é fornecida com uma versão de kernel padrão de 4,4. Essa versão do kernel tem um problema conhecido em que a Killer da memória encerra incorretamente o comando dd durante o processo de criptografia do sistema operacional. Esse bug foi corrigido no kernel mais recente do Linux ajustado do Azure. Para evitar esse erro, antes de habilitar a criptografia na imagem, atualize para o [kernel do Azure ajustado 4,15](https://packages.ubuntu.com/trusty/linux-azure) ou posterior usando os seguintes comandos:

```
sudo apt-get update
sudo apt-get install linux-azure
sudo reboot
```

Depois que a VM for reiniciada no novo kernel, a nova versão do kernel poderá ser confirmada usando:

```
uname -a
```

## <a name="update-the-azure-virtual-machine-agent-and-extension-versions"></a>Atualizar o agente de máquina virtual do Azure e as versões de extensão

Azure Disk Encryption operações podem falhar em imagens de máquina virtual usando versões sem suporte do agente de máquina virtual do Azure. Imagens do Linux com versões de agente anteriores a 2.2.38 devem ser atualizadas antes de habilitar a criptografia. Para obter mais informações, consulte [como atualizar o agente Linux do Azure em uma VM e o](../extensions/update-linux-agent.md) [suporte de versão mínima para agentes de máquina virtual no Azure](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

A versão correta da extensão de agente convidado Microsoft. Azure. Security. AzureDiskEncryption ou Microsoft. Azure. Security. AzureDiskEncryptionForLinux também é necessária. As versões de extensão são mantidas e atualizadas automaticamente pela plataforma quando os pré-requisitos do agente de máquina virtual do Azure são satisfeitos e uma versão com suporte do agente de máquina virtual é usada.

A extensão Microsoft. OSTCExtensions. AzureDiskEncryptionForLinux foi preterida e não é mais suportada.  

## <a name="unable-to-encrypt-linux-disks"></a>Não é possível criptografar discos Linux

Em alguns casos, a encriptação de disco parece estar bloqueada em "Iniciada de encriptação de disco de SO" de Linux e SSH está desativada. O processo de encriptação pode demorar entre horas de 3-16 a terminar numa imagem da Galeria das ações. Se forem adicionados discos de dados de tamanho de terabytes de transmissões, o processo pode demorar dias.

A sequência de encriptação de disco do SO Linux desmonta temporariamente a unidade do SO. Em seguida, executa encriptação de bloco a bloco do disco do SO inteiro, antes de ele remounts-lo no seu estado encriptado. A criptografia de disco do Linux não permite o uso simultâneo da VM enquanto a criptografia está em andamento. As características de desempenho da VM podem fazer uma diferença significativa no tempo necessário para a encriptação completa. Essas características incluem o tamanho do disco e se a conta de armazenamento é standard ou o armazenamento premium (SSD).

Para verificar o status de criptografia, sondar o campo **ProgressMessage** retornado do comando [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) . Enquanto a unidade do SO está a ser encriptada, a VM entra num Estado de manutenção e desativa o SSH para evitar qualquer interrupção para o processo contínuo. O **EncryptionInProgress** relatórios para a maioria das vezes de mensagens, enquanto a encriptação está em curso. Várias horas depois, um **VMRestartPending** mensagem solicita que reinicie a VM. Por exemplo:


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

Quando a conectividade é restringida por uma firewall, o requisito de proxy ou definições de grupo (NSG) de segurança de rede, a capacidade da extensão para efetuar tarefas necessárias pode ser interrompida. Este interrupção pode resultar em mensagens de estado, tais como o "Estado da extensão não está disponível na VM." Em cenários esperados, a encriptação não for concluída. As secções que se seguem têm alguns problemas comuns de firewall que pode investigar.

### <a name="network-security-groups"></a>Grupos de segurança de rede
Quaisquer definições de grupo de segurança de rede que são aplicadas ainda permite que o ponto final de acordo com a configuração de rede documentado [pré-requisitos](disk-encryption-overview.md#networking-requirements) para a encriptação de disco.

### <a name="azure-key-vault-behind-a-firewall"></a>O Azure Key Vault protegido por uma firewall

Quando a criptografia está sendo habilitada com [as credenciais do Azure ad](disk-encryption-linux-aad.md#), a VM de destino deve permitir a conectividade com os pontos de extremidade Azure Active Directory e Key Vault pontos de extremidade. Os pontos de extremidade de autenticação Azure Active Directory atuais são mantidos nas seções 56 e 59 da documentação de [intervalos de endereços IP e URLs do Office 365](https://docs.microsoft.com/office365/enterprise/urls-and-ip-address-ranges) . Key Vault instruções são fornecidas na documentação sobre como [acessar Azure Key Vault atrás de um firewall](../../key-vault/key-vault-access-behind-firewall.md).

### <a name="azure-instance-metadata-service"></a>Serviço de metadados de instância do Azure 
A VM deve ser capaz de acessar o ponto de extremidade do [serviço de metadados de instância do Azure](../windows/instance-metadata-service.md) que usa um endereço IP não roteável conhecido (`169.254.169.254`) que pode ser acessado somente de dentro da VM.  Não há suporte para as configurações de proxy que alteram o tráfego HTTP local para esse endereço (por exemplo, a adição de um cabeçalho X-Forwardd-for).

### <a name="linux-package-management-behind-a-firewall"></a>Gestão de pacotes do Linux protegida por uma firewall

No tempo de execução, o Azure Disk Encryption para Linux baseia-se no sistema de gestão do pacote de distribuição de destino para instalar os componentes de pré-requisitos necessários antes de ativar a encriptação. Se as definições da firewall impedem que a VM ser capaz de transferir e instalar estes componentes, em seguida, falhas subsequentes esperadas. Os passos para configurar este sistema de gestão de pacotes podem variar de distribuição. Em Red Hat, quando um proxy for necessário, deve certificar-se de que o Gestor de subscrições e yum estão configuradas corretamente. Para obter mais informações, consulte [como solucionar problemas do Gestor de subscrições e yum](https://access.redhat.com/solutions/189533).  

## <a name="troubleshooting-encryption-status"></a>Solucionando problemas de status de criptografia 

O portal pode exibir um disco como criptografado mesmo após ele ter sido descriptografado na VM.  Isso pode ocorrer quando comandos de baixo nível são usados para descriptografar diretamente o disco de dentro da VM, em vez de usar os comandos de gerenciamento de Azure Disk Encryption de nível superior.  Os comandos de nível superior não apenas descriptografam o disco de dentro da VM, mas fora da VM eles também atualizam configurações importantes de criptografia de nível de plataforma e configurações de extensão associadas à VM.  Se eles não forem mantidos em alinhamento, a plataforma não poderá relatar o status de criptografia nem provisionar a VM corretamente.   

Para desabilitar Azure Disk Encryption com o PowerShell, use [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) seguido de [Remove-AzVMDiskEncryptionExtension](/powershell/module/az.compute/remove-azvmdiskencryptionextension). A execução de Remove-AzVMDiskEncryptionExtension antes da criptografia ser desabilitada falhará.

Para desabilitar Azure Disk Encryption com a CLI, use [AZ VM Encryption Disable](/cli/azure/vm/encryption). 

## <a name="next-steps"></a>Passos seguintes

Neste documento, ficou a saber mais sobre alguns problemas comuns no Azure Disk Encryption e como solucionar esses problemas. Para obter mais informações sobre este serviço e as respetivas capacidades, consulte os artigos seguintes:

- [Aplicar encriptação de disco no Centro de segurança do Azure](../../security-center/security-center-apply-disk-encryption.md)
- [Encriptação de dados do Azure em repouso](../../security/fundamentals/encryption-atrest.md)
