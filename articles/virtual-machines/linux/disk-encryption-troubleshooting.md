---
title: Solução de problemas-Azure Disk Encryption para VMs Linux
description: Este artigo fornece dicas de solução de problemas para Microsoft Azure a criptografia de disco para VMs do Linux.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: c1e96a3acf2a576e0656afb3abea9dd787bf989a
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73750064"
---
# <a name="azure-disk-encryption-troubleshooting-guide"></a>Guia de solução de problemas Azure Disk Encryption

Este guia destina-se a profissionais de ti, analistas de segurança de informações e administradores de nuvem cujas organizações usam Azure Disk Encryption. Este artigo é para ajudar na solução de problemas relacionados à criptografia de disco.

Antes de executar qualquer uma das etapas a seguir, primeiro verifique se as VMs que você está tentando criptografar estão entre os [tamanhos de VM e sistemas operacionais com suporte](disk-encryption-overview.md#supported-vms-and-operating-systems)e se você atendeu a todos os pré-requisitos:

- [Requisitos adicionais para VMs](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Requisitos de rede](disk-encryption-overview.md#networking-requirements)
- [Requisitos de armazenamento de chave de criptografia](disk-encryption-overview.md#encryption-key-storage-requirements)

 

## <a name="troubleshooting-linux-os-disk-encryption"></a>Solucionando problemas de criptografia de disco do SO Linux

A criptografia de disco do so (sistema operacional Linux) deve desmontar a unidade do sistema operacional antes de executá-la por meio do processo de criptografia de disco completo. Se não for possível desmontar a unidade, uma mensagem de erro "falha ao desmontar após..." provavelmente ocorrerá.

Esse erro pode ocorrer quando a criptografia de disco do sistema operacional é tentada em uma VM com um ambiente que foi alterado da imagem da Galeria de estoque com suporte. Os desvios da imagem com suporte podem interferir na capacidade da extensão de desmontar a unidade do sistema operacional. Exemplos de desvios podem incluir os seguintes itens:
- Imagens personalizadas não correspondem mais a um sistema de arquivos ou esquema de particionamento com suporte.
- Aplicativos grandes, como SAP, MongoDB, Apache Cassandra e Docker, não têm suporte quando estão instalados e em execução no sistema operacional antes da criptografia. O Azure Disk Encryption não pode desligar esses processos com segurança, conforme necessário, na preparação da unidade do sistema operacional para a criptografia de disco. Se ainda houver processos ativos mantendo identificadores de arquivos abertos na unidade do sistema operacional, a unidade do sistema operacional não poderá ser desmontada, resultando em uma falha na criptografia da unidade do sistema operacional. 
- Scripts personalizados que são executados em tempo de fechamento em proximidade com a criptografia sendo habilitada ou se outras alterações estão sendo feitas na VM durante o processo de criptografia. Esse conflito pode ocorrer quando um modelo de Azure Resource Manager define várias extensões para ser executado simultaneamente ou quando uma extensão de script personalizado ou outra ação é executada simultaneamente na criptografia de disco. A serialização e o isolamento dessas etapas podem resolver o problema.
- A SELinux (segurança aprimorada do Linux) não foi desabilitada antes de habilitar a criptografia, portanto, a etapa de desmontagem falha. SELinux pode ser reabilitado após a conclusão da criptografia.
- O disco do sistema operacional usa um esquema LVM (Gerenciador de volumes lógicos). Embora o suporte a disco de dados do LVM limitado esteja disponível, um disco do sistema operacional do LVM não é.
- Os requisitos mínimos de memória não são atendidos (7 GB são sugeridos para criptografia de disco do SO).
- As unidades de dados são montadas recursivamente no diretório/mnt/ou uma na outra (por exemplo,/mnt/data1,/mnt/data2,/data3 +/data3/data4).

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

Em alguns casos, a criptografia de disco do Linux parece estar presa em "criptografia de disco do so iniciada" e o SSH está desabilitado. O processo de criptografia pode levar entre 3-16 horas para ser concluído em uma imagem da Galeria de estoque. Se discos de dados de vários terabytes forem adicionados, o processo poderá levar dias.

A sequência de criptografia de disco do SO Linux desmonta a unidade do sistema operacional temporariamente. Em seguida, ele executa a criptografia bloco a bloco de todo o disco do so, antes de remontá-lo em seu estado criptografado. A criptografia de disco do Linux não permite o uso simultâneo da VM enquanto a criptografia está em andamento. As características de desempenho da VM podem fazer uma diferença significativa no tempo necessário para concluir a criptografia. Essas características incluem o tamanho do disco e se a conta de armazenamento é o armazenamento padrão ou Premium (SSD).

Para verificar o status de criptografia, sondar o campo **ProgressMessage** retornado do comando [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) . Enquanto a unidade do sistema operacional está sendo criptografada, a VM entra em um estado de manutenção e desabilita o SSH para evitar qualquer interrupção no processo em andamento. A mensagem **EncryptionInProgress** relata a maior parte do tempo enquanto a criptografia está em andamento. Várias horas depois, uma mensagem **VMRestartPending** solicita que você reinicie a VM. Por exemplo:


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

Depois de ser solicitado a reinicializar a VM e, depois que a VM for reiniciada, você deverá aguardar 2-3 minutos para a reinicialização e para que as etapas finais sejam executadas no destino. A mensagem de status é alterada quando a criptografia é concluída por fim. Depois que essa mensagem estiver disponível, espera-se que a unidade do sistema operacional criptografada esteja pronta para uso e a VM esteja pronta para ser usada novamente.

Nos casos a seguir, recomendamos que você restaure a VM de volta para o instantâneo ou backup feito imediatamente antes da criptografia:
   - Se a sequência de reinicialização, descrita anteriormente, não acontecerá.
   - Se as informações de inicialização, mensagem de progresso ou outros indicadores de erro relatarem que a criptografia do sistema operacional falhou no meio desse processo. Um exemplo de uma mensagem é o erro "falha ao desmontar", que é descrito neste guia.

Antes da próxima tentativa, reavalie as características da VM e verifique se todos os pré-requisitos estão satisfeitos.

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>Solução de problemas Azure Disk Encryption por trás de um firewall

Quando a conectividade é restrita por um firewall, requisito de proxy ou configurações de NSG (grupo de segurança de rede), a capacidade da extensão de executar as tarefas necessárias pode ser interrompida. Essa interrupção pode resultar em mensagens de status como "status da extensão não disponível na VM". Em cenários esperados, a criptografia não é concluída. As seções a seguir têm alguns problemas de firewall comuns que você pode investigar.

### <a name="network-security-groups"></a>Grupos de segurança de rede
Qualquer configuração de grupo de segurança de rede aplicada ainda deve permitir que o ponto de extremidade atenda aos [pré-requisitos](disk-encryption-overview.md#networking-requirements) de configuração de rede documentados para criptografia de disco.

### <a name="azure-key-vault-behind-a-firewall"></a>Azure Key Vault atrás de um firewall

Quando a criptografia está sendo habilitada com [as credenciais do Azure ad](disk-encryption-linux-aad.md#), a VM de destino deve permitir a conectividade com os pontos de extremidade Azure Active Directory e Key Vault pontos de extremidade. Os pontos de extremidade de autenticação Azure Active Directory atuais são mantidos nas seções 56 e 59 da documentação de [intervalos de endereços IP e URLs do Office 365](https://docs.microsoft.com/office365/enterprise/urls-and-ip-address-ranges) . Key Vault instruções são fornecidas na documentação sobre como [acessar Azure Key Vault atrás de um firewall](../../key-vault/key-vault-access-behind-firewall.md).

### <a name="azure-instance-metadata-service"></a>Serviço de metadados de instância do Azure 
A VM deve ser capaz de acessar o ponto de extremidade do [serviço de metadados de instância do Azure](../windows/instance-metadata-service.md) , que usa um endereço IP não roteável conhecido (`169.254.169.254`) que pode ser acessado somente de dentro da VM.  Não há suporte para as configurações de proxy que alteram o tráfego HTTP local para esse endereço (por exemplo, a adição de um cabeçalho X-Forwardd-for).

### <a name="linux-package-management-behind-a-firewall"></a>Gerenciamento de pacotes do Linux por trás de um firewall

Em tempo de execução, Azure Disk Encryption para Linux depende do sistema de gerenciamento de pacotes da distribuição de destino para instalar os componentes de pré-requisito necessários antes de habilitar a criptografia. Se as configurações de firewall impedirem que a VM seja capaz de baixar e instalar esses componentes, as falhas subsequentes serão esperadas. As etapas para configurar esse sistema de gerenciamento de pacotes podem variar de acordo com a distribuição. No Red Hat, quando um proxy é necessário, você deve verificar se o Gerenciador de assinatura e yum estão configurados corretamente. Para obter mais informações, consulte [como solucionar problemas do Gerenciador de assinaturas e yum](https://access.redhat.com/solutions/189533).  

## <a name="troubleshooting-encryption-status"></a>Solucionando problemas de status de criptografia 

O portal pode exibir um disco como criptografado mesmo após ele ter sido descriptografado na VM.  Isso pode ocorrer quando comandos de baixo nível são usados para descriptografar diretamente o disco de dentro da VM, em vez de usar os comandos de gerenciamento de Azure Disk Encryption de nível superior.  Os comandos de nível superior não apenas descriptografam o disco de dentro da VM, mas fora da VM eles também atualizam configurações importantes de criptografia de nível de plataforma e configurações de extensão associadas à VM.  Se eles não forem mantidos em alinhamento, a plataforma não poderá relatar o status de criptografia nem provisionar a VM corretamente.   

Para desabilitar Azure Disk Encryption com o PowerShell, use [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) seguido de [Remove-AzVMDiskEncryptionExtension](/powershell/module/az.compute/remove-azvmdiskencryptionextension). A execução de Remove-AzVMDiskEncryptionExtension antes da criptografia ser desabilitada falhará.

Para desabilitar Azure Disk Encryption com a CLI, use [AZ VM Encryption Disable](/cli/azure/vm/encryption). 

## <a name="next-steps"></a>Passos seguintes

Neste documento, você aprendeu mais sobre alguns problemas comuns no Azure Disk Encryption e como solucionar esses problemas. Para obter mais informações sobre esse serviço e seus recursos, consulte os seguintes artigos:

- [Aplicar a criptografia de disco na central de segurança do Azure](../../security-center/security-center-apply-disk-encryption.md)
- [Criptografia de dados do Azure em repouso](../../security/fundamentals/encryption-atrest.md)
