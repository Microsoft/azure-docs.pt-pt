---
title: Ativar o Azure Disk Encryption para VM do Linux
description: Este artigo fornece instruções para permitir a encriptação do disco do Microsoft Azure para os VMs Linux.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 0f23e2c58ae25fefb8508474921e476163b33e79
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91978429"
---
# <a name="azure-disk-encryption-for-linux-vms"></a>Azure Disk Encryption para VMs do Linux 

A Azure Disk Encryption ajuda a proteger e a salvaguardar os seus dados para cumprir as obrigações de conformidade e segurança da sua organização. Utiliza a funcionalidade [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) do Linux para fornecer encriptação de volume para o SISTEMA e discos de dados de máquinas virtuais Azure (VMs), e está integrado com [o Azure Key Vault](../../key-vault/index.yml) para ajudá-lo a controlar e gerir as chaves e segredos de encriptação do disco. 

Se utilizar [o Centro de Segurança Azure,](../../security-center/index.yml)é alertado se tiver VMs que não estejam encriptados. Os alertas mostram como Alta Severidade e a recomendação é encriptar estes VMs.

![Alerta de encriptação do disco do Azure Security Center](media/disk-encryption/security-center-disk-encryption-fig1.png)

> [!WARNING]
> - Se já utilizou a Encriptação do Disco Azure com AZure AD para encriptar um VM, deve continuar a utilizar esta opção para encriptar o seu VM. Consulte [a encriptação do disco Azure com Azure AD (versão anterior)](disk-encryption-overview-aad.md) para obter mais detalhes. 
> - Certas recomendações podem aumentar o uso de dados, redes ou recursos computativos, resultando em custos adicionais de licença ou subscrição. Você deve ter uma subscrição ativa válida Azure para criar recursos em Azure nas regiões apoiadas.
> - Atualmente, os VMs da Geração 2 não suportam a Encriptação do Disco Azure. Consulte [o Suporte para Azure da Geração 2](../generation-2.md) para obter mais detalhes.

Pode aprender os fundamentos da Encriptação do Disco Azure para o Linux em poucos minutos com o [Create e encriptar um Linux VM com quickstart Azure CLI](disk-encryption-cli-quickstart.md) ou o [Create e encriptar um Linux VM com quickstart Azure PowerShell](disk-encryption-powershell-quickstart.md).

## <a name="supported-vms-and-operating-systems"></a>VMs e sistemas operativos suportados

### <a name="supported-vms"></a>VMs suportados

Os VMs Linux estão disponíveis numa [gama de tamanhos](../sizes.md). A Encriptação do Disco Azure não está disponível em [VMs básicos, série A,](https://azure.microsoft.com/pricing/details/virtual-machines/series/)ou em máquinas virtuais que não satisfaçam estes requisitos mínimos de memória:

| Máquina virtual | Requisito mínimo de memória |
|--|--|
| VMs Linux quando apenas encriptam volumes de dados| 2 GB |
| Os VMs Do Linux ao encriptar os volumes de dados e de SO, e onde o uso do sistema de ficheiros raiz (/) é de 4GB ou menos | 8 GB |
| Os VMs Do Linux ao encriptar os volumes de dados e de SO, e onde o uso do sistema de ficheiros raiz (/) é superior a 4GB | O sistema de ficheiros radi ao seu uso * 2. Por exemplo, um sistema de 16 GB de sistema de ficheiros radiculantes requer pelo menos 32GB de RAM |

Uma vez concluído o processo de encriptação do disco OS em máquinas virtuais Linux, o VM pode ser configurado para funcionar com menos memória. 

A Azure Disk Encryption também está disponível para VMs com armazenamento premium.

A encriptação do disco Azure não está disponível em [VMs da Geração 2](../generation-2.md#generation-1-vs-generation-2-capabilities) e [VMs da série Lsv2](../lsv2-series.md). Para mais exceções, consulte [encriptação do disco Azure: cenários não suportados](disk-encryption-linux.md#unsupported-scenarios).

### <a name="supported-operating-systems"></a>Sistemas operativos suportados

A Azure Disk Encryption é suportada num subconjunto das [distribuições Linux apoiadas pelo Azure](endorsed-distros.md), que é por si só um subconjunto de todas as possíveis distribuições do servidor Linux.

![Diagrama venn das distribuições de servidores Linux que suportam encriptação do disco Azure](./media/disk-encryption/ade-supported-distros.png)

As distribuições de servidores Linux que não são endossadas pelo Azure não suportam encriptação do disco Azure; daqueles que são endossados, apenas as seguintes distribuições e versões suportam encriptação do disco Azure:

| Publisher | Oferta | SKU | URN | Tipo de volume suportado para encriptação |
| --- | --- |--- | --- |
| Canónico | Ubuntu | 18.04-LTS | Canónico:UbuntuServer:18.04-LTS:mais recente | SO e disco de dados |
| Canónico | Ubuntu 18.04 | 18.04-DAILY-LTS | Canónico:UbuntuServer:18.04-DAILY-LTS:mais recente | SO e disco de dados |
| Canónico | Ubuntu 16.04 | 16.04-DAILY-LTS | Canónico:UbuntuServer:16.04-DAILY-LTS:mais recente | SO e disco de dados |
| Canónico | Ubuntu 14.04.5</br>[com o núcleo afinado Azure atualizado para 4.15 ou mais tarde](disk-encryption-troubleshooting.md) | 14.04.5-LTS | Canónico:UbuntuServer:14.04.5-LTS:mais recente | SO e disco de dados |
| Canónico | Ubuntu 14.04.5</br>[com o núcleo afinado Azure atualizado para 4.15 ou mais tarde](disk-encryption-troubleshooting.md) | 14.04.5-DAILY-LTS | Canónico:UbuntuServer:14.04.5-DAILY-LTS:mais recente | SO e disco de dados |
| RedHat | RHEL 7.8 | 7.8 | RedHat:RHEL:7.8:mais recente | SO e disco de dados (ver nota abaixo) |
| RedHat | RHEL 7.7 | 7.7 | RedHat:RHEL:7.7:mais recente | SO e disco de dados (ver nota abaixo) |
| RedHat | RHEL 7.7 | 7-LVM | RedHat:RHEL:7-LVM:mais recente | SO e disco de dados (ver nota abaixo) |
| RedHat | RHEL 7.6 | 7.6 | RedHat:RHEL:7.6:mais recente | SO e disco de dados (ver nota abaixo) |
| RedHat | RHEL 7.5 | 7,5 | RedHat:RHEL:7.5:mais recente | SO e disco de dados (ver nota abaixo) |
| RedHat | RHEL 7.4 | 7.4 | RedHat:RHEL:7.4:mais recente | SO e disco de dados (ver nota abaixo) |
| RedHat | RHEL 7.3 | 7.3 | RedHat:RHEL:7.3:mais recente | SO e disco de dados (ver nota abaixo) |
| RedHat | RHEL 7.2 | 7.2 | RedHat:RHEL:7.2:mais recente | SO e disco de dados (ver nota abaixo) |
| RedHat | RHEL 6.8 | 6.8 | RedHat:RHEL:6.8:mais recente | Disco de dados (ver nota abaixo) |
| RedHat | RHEL 6.7 | 6.7 | RedHat:RHEL:6.7:mais recente | Disco de dados (ver nota abaixo) |
| OpenLogic | CentOS 7.7 | 7.7 | OpenLogic:CentOS:7.7:mais recente | SO e disco de dados |
| OpenLogic | CentOS 7.7 | 7-LVM | OpenLogic:CentOS:7-LVM:mais recente | SO e disco de dados |
| OpenLogic | CentOS 7.6 | 7.6 | OpenLogic:CentOS:7.6:mais recente | SO e disco de dados |
| OpenLogic | CentOS 7.5 | 7,5 | OpenLogic:CentOS:7.5:mais recente | SO e disco de dados |
| OpenLogic | CentOS 7.4 | 7.4 | OpenLogic:CentOS:7.4:mais recente | SO e disco de dados |
| OpenLogic | CentOS 7.3 | 7.3 | OpenLogic:CentOS:7.3:mais recente | SO e disco de dados |
| OpenLogic | CentOS 7.2n | 7.2n | OpenLogic:CentOS:7.2n:mais recente | SO e disco de dados |
| OpenLogic | CentOS 7.1 | 7.1 | OpenLogic:CentOS:7.1:mais recente | Disco de dados apenas |
| OpenLogic | CentOS 7.0 | 7.0 | OpenLogic:CentOS:7.0:mais recente | Disco de dados apenas |
| OpenLogic | CentOS 6.8 | 6.8 | OpenLogic:CentOS:6.8:mais recente | Disco de dados apenas |
| SUSE | openSUSE 42.3 | 42.3 | SUSE:openSUSE-Leap:42.3:mais recente | Disco de dados apenas |
| SUSE | SLES 12-SP4 | 12-SP4 | SUSE:SLES:12-SP4:mais recente | Disco de dados apenas |
| SUSE | SLES HPC 12-SP3 | 12-SP3 | SUSE:SLES-HPC:12-SP3:mais recente | Disco de dados apenas |

> [!NOTE]
> A nova implementação de encriptação do disco Azure é suportada para RHEL OS e disco de dados para imagens RHEL7 Pay-As-You-Go.  
>
> O ADE também é suportado para imagens de ouro de bring-your-your-subscrição, mas apenas **após** a subscrição ter sido registada . Para mais informações, consulte [Red Hat Enterprise Linux Bring-Your-Own-Subscription Gold Images em Azure](../workloads/redhat/byos.md#encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images)

## <a name="additional-vm-requirements"></a>Requisitos adicionais de VM

A encriptação do disco Azure requer que os módulos dm-crypt e vfat estejam presentes no sistema. Remover ou desativar o vfat da imagem padrão impedirá o sistema de ler o volume da chave e obter a chave necessária para desbloquear os discos em reboots subsequentes. Os passos de endurecimento do sistema que removem o módulo vfat do sistema ou impõem a expansão dos pontos/pastas de oss nas unidades de dados não são compatíveis com a Encriptação do Disco Azure. 

Antes de permitir a encriptação, os discos de dados a encriptar devem ser devidamente listados em /etc/fstab. Utilize a opção "nofail" ao criar entradas e escolha um nome persistente do dispositivo de bloqueio (como os nomes do dispositivo no formato "/dev/sdX" podem não estar associados ao mesmo disco através de reboots, particularmente após a encriptação; para obter mais detalhes sobre este comportamento, consulte: Alterações no nome do [dispositivo Linux VM](../troubleshooting/troubleshoot-device-names-problems.md)de resolução de problemas).

Certifique-se de que as definições /etc/fstab estão configuradas corretamente para a montagem. Para configurar estas definições, executar o suporte -um comando ou reiniciar o VM e acionar a montagem dessa forma. Uma vez concluído, verifique a saída do comando de Lsblk para verificar se a unidade ainda está montada. 

- Se o ficheiro /etc/fstab não montar corretamente a unidade antes de ativar a encriptação, a Encriptação do Disco Azure não será capaz de montá-la corretamente.
- O processo de encriptação do disco Azure irá mover a informação de montagem de /etc/fstab para o seu próprio ficheiro de configuração como parte do processo de encriptação. Não se assuste ao ver a entrada em falta de /etc/fstab após a encriptação da unidade de dados estar concluída.
- Antes de iniciar a encriptação, certifique-se de parar todos os serviços e processos que possam estar a escrever para discos de dados montados e desativá-los, para que não reiniciem automaticamente após um reboot. Estes poderiam manter os ficheiros abertos nestas divisórias, impedindo o procedimento de encriptação de os voltar a montar, causando falhas na encriptação. 
- Após o reboot, levará tempo para o processo de encriptação do disco Azure para montar os discos recentemente encriptados. Não estarão imediatamente disponíveis depois de um reboot. O processo precisa de tempo para iniciar, desbloquear e, em seguida, montar as unidades encriptadas antes de estar disponível para outros processos de acesso. Este processo pode demorar mais de um minuto após o reboot, dependendo das características do sistema.

Aqui está um exemplo dos comandos utilizados para montar os discos de dados e criar as entradas necessárias /etc/fstab:

```bash
UUID0="$(blkid -s UUID -o value /dev/disk/azure/scsi1/lun0)"
UUID1="$(blkid -s UUID -o value /dev/disk/azure/scsi1/lun1)"
mkdir /data0
mkdir /data1
echo "UUID=$UUID0 /data0 ext4 defaults,nofail 0 0" >>/etc/fstab
echo "UUID=$UUID1 /data1 ext4 defaults,nofail 0 0" >>/etc/fstab
mount -a
```
## <a name="networking-requirements"></a>Requisitos de rede

Para ativar a funcionalidade de encriptação do disco Azure, os VMs Linux devem satisfazer os seguintes requisitos de configuração do ponto final da rede:
  - Para obter um token para ligar ao seu cofre chave, o Linux VM deve ser capaz de ligar a um ponto final do Azure Ative Directory, \[ login.microsoftonline.com \] .
  - Para escrever as chaves de encriptação do cofre, o Linux VM deve ser capaz de ligar ao ponto final do cofre.
  - O Linux VM deve ser capaz de se ligar a um ponto final de armazenamento Azure que acolhe o repositório de extensão Azure e uma conta de armazenamento Azure que acolhe os ficheiros VHD.
  -  Se a sua política de segurança limitar o acesso dos VMs Azure à Internet, pode resolver o URI anterior e configurar uma regra específica para permitir a conectividade de saída com os IPs. Para obter mais informações, consulte [o Azure Key Vault atrás de uma firewall](../../key-vault/general/access-behind-firewall.md).  

## <a name="encryption-key-storage-requirements"></a>Requisitos de armazenamento de chaves de encriptação  

A encriptação do disco Azure requer um Cofre de Chave Azure para controlar e gerir chaves e segredos de encriptação de discos. O seu cofre-chave e VMs devem residir na mesma região de Azure e subscrição.

Para mais detalhes, consulte [criar e configurar um cofre chave para encriptação do disco Azure](disk-encryption-key-vault.md).

## <a name="terminology"></a>Terminologia
A tabela a seguir define alguns dos termos comuns utilizados na documentação de encriptação do disco Azure:

| Terminologia | Definição |
| --- | --- |
| Azure Key Vault | Key Vault é um serviço de gestão criptográfico e chave que é baseado em módulos de segurança de hardware validados (FIPS) federais de processamento de informação. Estes padrões ajudam a salvaguardar as suas chaves criptográficas e segredos sensíveis. Para obter mais informações, consulte a documentação do [Cofre da Chave Azure](https://azure.microsoft.com/services/key-vault/) e [criando e configurando um cofre chave para encriptação do disco Azure](disk-encryption-key-vault.md). |
| CLI do Azure | [O CLI Azure](/cli/azure/install-azure-cli) está otimizado para gerir e administrar recursos Azure a partir da linha de comando.|
| DM-Crypt |[DM-Crypt](https://gitlab.com/cryptsetup/cryptsetup/wikis/DMCrypt) é o subsistema de encriptação de disco transparente baseado em Linux que é usado para permitir encriptação de discos em VMs Linux. |
| Chave de encriptação (KEK) | A chave assimétrica (RSA 2048) que pode usar para proteger ou embrulhar o segredo. Pode fornecer uma chave protegida por hardware (HSM) ou uma chave protegida por software. Para obter mais informações, consulte a documentação do [Cofre da Chave Azure](https://azure.microsoft.com/services/key-vault/) e [criando e configurando um cofre chave para encriptação do disco Azure](disk-encryption-key-vault.md). |
| Cmdlets do PowerShell | Para obter mais informações, consulte [os cmdlets Azure PowerShell](/powershell/azure/). |


## <a name="next-steps"></a>Passos seguintes

- [Quickstart - Criar e encriptar um Linux VM com Azure CLI ](disk-encryption-cli-quickstart.md)
- [Quickstart - Criar e encriptar um Linux VM com Azure Powershell](disk-encryption-powershell-quickstart.md)
- [Cenários do Azure Disk Encryption em VMs com Linux](disk-encryption-linux.md)
- [Encriptação de disco Azure pré-requisitos do script CLI](https://github.com/ejarvi/ade-cli-getting-started)
- [Encriptação de disco Azure pré-requisitos PowerShell script](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [Criar e configurar um cofre de chaves para o Azure Disk Encryption](disk-encryption-key-vault.md)
