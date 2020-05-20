---
title: Ativar o Azure Disk Encryption para VM do Linux
description: Este artigo fornece instruções sobre a ativação da encriptação do disco Microsoft Azure para VMs Linux.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 5c227c6ab24d6b71445354d1b17d238e80bf6313
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83655858"
---
# <a name="azure-disk-encryption-for-linux-vms"></a>Encriptação de disco azure para VMs Linux 

A Azure Disk Encryption ajuda a proteger e a salvaguardar os seus dados para cumprir as obrigações de conformidade e segurança da sua organização. Utiliza a funcionalidade [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) do Linux para fornecer encriptação de volume para o OS e discos de dados de máquinas virtuais Azure (VMs), e está integrado com o [Azure Key Vault](../../key-vault/index.yml) para o ajudar a controlar e gerir as chaves e segredos de encriptação do disco. 

Se utilizar o Centro de [Segurança Azure,](../../security-center/index.yml)é alertado se tiver VMs que não estejam encriptados. Os alertas mostram como Alta Severidade e a recomendação é encriptar estes VMs.

![Alerta de encriptação do disco do Azure Security Center](media/disk-encryption/security-center-disk-encryption-fig1.png)

> [!WARNING]
> - Se já utilizou anteriormente a Encriptação do Disco Azure com a AD Azure para encriptar um VM, tem de continuar a utilizar esta opção para encriptar o seu VM. Consulte a Encriptação do [Disco Azure com AD Azure (versão anterior)](disk-encryption-overview-aad.md) para obter mais detalhes. 
> - Certas recomendações podem aumentar a utilização de dados, rede ou computação de recursos, resultando em custos adicionais de licença ou subscrição. Deve ter uma subscrição azure ativa válida para criar recursos em Azure nas regiões apoiadas.
> - Atualmente, os VMs da Geração 2 não suportam a encriptação do disco Azure. Consulte [o Suporte para VMs da Geração 2 no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2) para obter mais detalhes.

Você pode aprender os fundamentos da Encriptação de Disco Azure para Linux em apenas alguns minutos com o [Create e encriptar um Linux VM com quickstart Azure CLI](disk-encryption-cli-quickstart.md) ou o [Create e encriptar um VM Linux com quickstart Azure PowerShell](disk-encryption-powershell-quickstart.md).

## <a name="supported-vms-and-operating-systems"></a>VMs e sistemas operativos suportados

### <a name="supported-vms"></a>VMs suportados

Os VMs Linux estão disponíveis numa [gama de tamanhos.](sizes.md) A Encriptação do Disco Azure não está disponível em [VMs básicos, série A,](https://azure.microsoft.com/pricing/details/virtual-machines/series/)ou em máquinas virtuais que não cumprem estes requisitos mínimos de memória:

| Máquina virtual | Requisito mínimo de memória |
|--|--|
| VMs Linux quando apenas encriptam volumes de dados| 2 GB |
| VMs Linux ao encriptar tanto os volumes de dados como os volumes de OS, e onde a utilização do sistema de ficheiros raiz (/) é de 4GB ou menos | 8 GB |
| VMs Linux ao encriptar tanto os volumes de dados como os volumes de OS, e onde a utilização do sistema de ficheiros raiz (/) é superior a 4GB | O uso do sistema de ficheiros radicular * 2. Por exemplo, um uso de 16 GB de sistema de ficheiros radicular requer pelo menos 32GB de RAM |

Uma vez que o processo de encriptação do disco OS esteja concluído em máquinas virtuais Linux, o VM pode ser configurado para ser executado com menos memória. 

A Encriptação azure Disk também está disponível para VMs com armazenamento premium.

A encriptação do disco Azure não está disponível nas [VMs da Geração 2)](generation-2.md#generation-1-vs-generation-2-capabilities)e nas [séries Lsv2.](../lsv2-series.md) Para mais exceções, consulte [encriptação do disco Azure: cenários não suportados](disk-encryption-linux.md#unsupported-scenarios).

### <a name="supported-operating-systems"></a>Sistemas operativos suportados

A encriptação do disco Azure é suportada num subconjunto das [distribuições Linux apoiadas pelo Azure,](endorsed-distros.md)que é em si um subconjunto de todas as possíveis distribuições do servidor Linux.

![Diagrama venn de distribuição de servidor Linux que suporta encriptação do disco Azure](./media/disk-encryption/ade-supported-distros.png)

As distribuições de servidores Linux que não são endossadas pelo Azure não suportam encriptação do disco Azure; dos que são endossados, apenas as seguintes distribuições e versões suportam encriptação do Disco Azure:

| Publisher | Oferta | SKU | URN | Tipo de volume suportado para encriptação |
| --- | --- |--- | --- |
| Canónico | Ubuntu | 18.04-LTS | Canonical:UbuntuServer:18.04-LTS:mais recente | SEm e disco de dados |
| Canónico | Ubuntu 18.04 | 18.04-DIÁRIO-LTS | Canonical:UbuntuServer:18.04-DAILY-LTS:mais recente | SEm e disco de dados |
| Canónico | Ubuntu 16.04 | 16.04-DIÁRIO-LTS | Canonical:UbuntuServer:16.04-DAILY-LTS:mais recente | SEm e disco de dados |
| Canónico | Ubuntu 14.04.5</br>[com kernel afinado Azure atualizado a 4.15 ou mais tarde](disk-encryption-troubleshooting.md) | 14.04.5-LTS | Canonical:UbuntuServer:14.04.5-LTS:mais recente | SEm e disco de dados |
| Canónico | Ubuntu 14.04.5</br>[com kernel afinado Azure atualizado a 4.15 ou mais tarde](disk-encryption-troubleshooting.md) | 14.04.5-DIÁRIO-LTS | Canonical:UbuntuServer:14.04.5-DAILY-LTS:mais recente | SEm e disco de dados |
| RedHat | RHEL 7.7 | 7.7 | RedHat:RHEL:7.7:mais recente | S e disco de dados (ver nota abaixo) |
| RedHat | RHEL 7.7 | 7-RAW | RedHat:RHEL:7-RAW:mais recente | S e disco de dados (ver nota abaixo) |
| RedHat | RHEL 7.7 | 7-LVM | RedHat:RHEL:7-LVM:mais recente | S e disco de dados (ver nota abaixo) |
| RedHat | RHEL 7.6 | 7.6 | RedHat:RHEL:7.6:mais recente | S e disco de dados (ver nota abaixo) |
| RedHat | RHEL 7.5 | 7,5 | RedHat:RHEL:7.5:mais recente | S e disco de dados (ver nota abaixo) |
| RedHat | RHEL 7.4 | 7.4 | RedHat:RHEL:7.4:mais recente | S e disco de dados (ver nota abaixo) |
| RedHat | RHEL 7.3 | 7.3 | RedHat:RHEL:7.3:mais recente | S e disco de dados (ver nota abaixo) |
| RedHat | RHEL 7.2 | 7.2 | RedHat:RHEL:7.2:mais recente | S e disco de dados (ver nota abaixo) |
| RedHat | RHEL 6.8 | 6.8 | RedHat:RHEL:6.8:mais recente | Disco de dados (ver nota abaixo) |
| RedHat | RHEL 6.7 | 6.7 | RedHat:RHEL:6.7:mais recente | Disco de dados (ver nota abaixo) |
| OpenLogic | Centos 7.7 | 7.7 | OpenLogic:CentOS:7.7:mais recente | SEm e disco de dados |
| OpenLogic | Centos 7.7 | 7-LVM | OpenLogic:CentOS:7-LVM:mais recente | SEm e disco de dados |
| OpenLogic | Centos 7.6 | 7.6 | OpenLogic:CentOS:7.6:mais recente | SEm e disco de dados |
| OpenLogic | CentOS 7.5 | 7,5 | OpenLogic:CentOS:7.5:mais recente | SEm e disco de dados |
| OpenLogic | CentOS 7.4 | 7.4 | OpenLogic:CentOS:7.4:mais recente | SEm e disco de dados |
| OpenLogic | Centos 7.3 | 7.3 | OpenLogic:CentOS:7.3:mais recente | SEm e disco de dados |
| OpenLogic | CentOS 7.2n | 7.2n | OpenLogic:CentOS:7.2n:mais recente | SEm e disco de dados |
| OpenLogic | Centos 7.1 | 7.1 | OpenLogic:CentOS:7.1:mais recente | Disco de dados apenas |
| OpenLogic | Centos 7.0 | 7.0 | OpenLogic:CentOS:7.0:mais recente | Disco de dados apenas |
| OpenLogic | Centos 6.8 | 6.8 | OpenLogic:CentOS:6.8:mais recente | Disco de dados apenas |
| SUSE | aberturaSUSE 42.3 | 42.3 | SUSE:openSUSE-Leap:42.3:mais recente | Disco de dados apenas |
| SUSE | SLES 12-SP4 | 12-SP4 | SUSE:SLES:12-SP4:mais recente | Disco de dados apenas |
| SUSE | SLES HPC 12-SP3 | 12-SP3 | SUSE:SLES-HPC:12-SP3:mais recente | Disco de dados apenas |

> [!NOTE]
> A nova implementação de encriptação de disco azure é suportada para O RHEL OS e disco de dados para imagens RHEL7 Pay-As-You-Go.  
>
> O ADE também é suportado para Imagens Douradas RHEL Bring-Your-Your-Own-Subscription, mas apenas **depois** de a subscrição ter sido registada . Para mais informações, consulte [Red Hat Enterprise Linux Bring-Your-Your-Own-Subscription Gold Images in Azure](../workloads/redhat/byos.md#encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images)

## <a name="additional-vm-requirements"></a>Requisitos adicionais de VM

A encriptação do disco azure requer que os módulos dm-cripto e vfat estejam presentes no sistema. Remover ou desativar a vfat da imagem predefinida impedirá o sistema de ler o volume da chave e obter a chave necessária para desbloquear os discos em reboots subsequentes. Os passos de endurecimento do sistema que removem o módulo vfat do sistema não são compatíveis com a Encriptação do Disco Azure. 

Antes de permitir a encriptação, os discos de dados a encriptar devem ser corretamente listados em /etc/fstab. Utilize a opção "nofail" ao criar entradas e escolha um nome de dispositivo de bloco persistente (como os nomes do dispositivo no formato "/dev/sdX" não podem estar associados ao mesmo disco através de reboots, particularmente após encriptação; para mais detalhes sobre este comportamento, consulte: Alterações no nome do [dispositivo Desmancha de Linux).](troubleshoot-device-names-problems.md)

Certifique-se de que as definições /etc/fstab estão corretamente configuradas para montagem. Para configurar estas definições, executar o suporte - um comando ou reiniciar o VM e desencadear a montagem dessa forma. Uma vez concluído, verifique a saída do comando de lsblk para verificar se a unidade ainda está montada. 

- Se o ficheiro /etc/fstab não montar corretamente a unidade antes de ativar a encriptação, a Encriptação do Disco Azure não será capaz de montá-la corretamente.
- O processo de encriptação do disco Azure irá mover a informação de montagem para fora de /etc/fstab e para o seu próprio ficheiro de configuração como parte do processo de encriptação. Não se assuste ao ver a entrada em falta de /etc/fstab após a encriptação da unidade de dados estar concluída.
- Antes de iniciar a encriptação, certifique-se de parar todos os serviços e processos que possam estar a escrever para discos de dados montados e desativá-los, para que não reiniciem automaticamente após um reboot. Estes poderiam manter os ficheiros abertos nestas divisórias, impedindo que o procedimento de encriptação os remontasse, causando falhas na encriptação. 
- Após o reboot, levará tempo para que o processo de encriptação do disco Azure monte os discos recentemente encriptados. Não estarão imediatamente disponíveis depois de um reboot. O processo precisa de tempo para iniciar, desbloquear e, em seguida, montar as unidades encriptadas antes de estar disponível para outros processos de acesso. Este processo pode demorar mais de um minuto após o reboot dependendo das características do sistema.

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
## <a name="networking-requirements"></a>Requisitos de networking

Para ativar a funcionalidade de encriptação do disco Azure, os VMs Linux devem satisfazer os seguintes requisitos de configuração do ponto final da rede:
  - Para obter um símbolo para ligar ao seu cofre chave, o VM Linux deve ser capaz de ligar a um ponto final do Diretório Ativo Azure, \[ login.microsoftonline.com \] .
  - Para escrever as chaves de encriptação do seu cofre de chaves, o VM Linux deve ser capaz de ligar ao ponto final do cofre chave.
  - O Linux VM deve ser capaz de ligar a um ponto final de armazenamento Azure que acolhe o repositório de extensão Azure e uma conta de armazenamento Azure que acolhe os ficheiros VHD.
  -  Se a sua política de segurança limitar o acesso dos VMs Azure à Internet, pode resolver o URI anterior e configurar uma regra específica para permitir a conectividade de saída aos IPs. Para mais informações, consulte [o Cofre chave Azure atrás de uma firewall](../../key-vault/general/access-behind-firewall.md).  

## <a name="encryption-key-storage-requirements"></a>Requisitos de armazenamento de chaves de encriptação  

A encriptação do disco Azure requer um Cofre de Chave Azure para controlar e gerir chaves e segredos de encriptação de disco. O seu cofre chave e VMs devem residir na mesma região azure e subscrição.

Para mais detalhes, consulte [Criar e configurar um cofre chave para encriptação](disk-encryption-key-vault.md)de disco azure .

## <a name="terminology"></a>Terminologia
A tabela que se segue define alguns dos termos comuns utilizados na documentação de encriptação do disco Azure:

| Terminologia | Definição |
| --- | --- |
| Azure Key Vault | Key Vault é um serviço de gestão criptográfico e chave que é baseado em módulos de segurança de hardware validados (FIPS) federal Information Processing Standards (FIPS). Estes padrões ajudam a salvaguardar as suas chaves criptográficas e segredos sensíveis. Para mais informações, consulte a documentação do [Cofre de Chaves Azure](https://azure.microsoft.com/services/key-vault/) e [criar e configurar um cofre chave para encriptação](disk-encryption-key-vault.md)de disco azure . |
| CLI do Azure | [O Azure CLI](/cli/azure/install-azure-cli) está otimizado para gerir e administrar recursos Azure a partir da linha de comando.|
| DM-Cripta |[DM-Crypt](https://gitlab.com/cryptsetup/cryptsetup/wikis/DMCrypt) é o subsistema de encriptação de disco transparente baseado em Linux que é usado para ativar a encriptação do disco em VMs Linux. |
| Chave de encriptação (KEK) | A chave assimétrica (RSA 2048) que pode usar para proteger ou embrulhar o segredo. Pode fornecer uma chave protegida por hardware (HSM) ou uma chave protegida por software. Para mais informações, consulte a documentação do [Cofre de Chaves Azure](https://azure.microsoft.com/services/key-vault/) e [criar e configurar um cofre chave para encriptação](disk-encryption-key-vault.md)de disco azure . |
| Cmdlets do PowerShell | Para mais informações, consulte [os cmdlets Azure PowerShell](/powershell/azure/overview). |


## <a name="next-steps"></a>Passos seguintes

- [Quickstart - Crie e criptografe um Linux VM com O ClI Azure](disk-encryption-cli-quickstart.md)
- [Quickstart - Crie e criptografe um Linux VM com Azure Powershell](disk-encryption-powershell-quickstart.md)
- [Cenários do Azure Disk Encryption em VMs do Linux](disk-encryption-linux.md)
- [Encriptação do disco azure pré-requisitos script CLI](https://github.com/ejarvi/ade-cli-getting-started)
- [Encriptação de disco azure pré-requisitos PowerShell script](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [Criar e configurar um cofre de chaves para o Azure Disk Encryption](disk-encryption-key-vault.md)


