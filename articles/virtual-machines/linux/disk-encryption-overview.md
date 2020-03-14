---
title: Ativar encriptação de disco Azure para VMs Linux
description: Este artigo fornece instruções sobre a ativação da encriptação do disco Microsoft Azure para VMs Linux.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: ff1b37c3053ffa91dcb432cd97a7dd6fd71dad1c
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79250429"
---
# <a name="azure-disk-encryption-for-linux-vms"></a>Encriptação de disco azure para VMs Linux 

A Encriptação do Disco Azure ajuda a proteger e salvaguardar os seus dados para cumprir os seus compromissos de segurança organizacional e conformidade. Utiliza a funcionalidade [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) do Linux para fornecer encriptação de volume para o OS e discos de dados de máquinas virtuais Azure (VMs), e está integrado com o [Azure Key Vault](../../key-vault/index.yml) para o ajudar a controlar e gerir as chaves e segredos de encriptação do disco. 

Se utilizar o Centro de [Segurança Azure,](../../security-center/index.yml)é alertado se tiver VMs que não estejam encriptados. Os alertas mostram como de gravidade alta e a recomendação é de encriptar estas VMs.

![Alerta de encriptação de disco do Centro de segurança do Azure](media/disk-encryption/security-center-disk-encryption-fig1.png)

> [!WARNING]
> - Se já utilizou anteriormente a Encriptação do Disco Azure com a AD Azure para encriptar um VM, tem de continuar a utilizar esta opção para encriptar o seu VM. Consulte a Encriptação do [Disco Azure com AD Azure (versão anterior)](disk-encryption-overview-aad.md) para obter mais detalhes. 
> - Algumas recomendações podem aumentar de dados, a rede ou a utilização de recursos de computação, resultando em custos adicionais de licença ou subscrição. Tem de ter uma subscrição do Azure Active Directory válida para criar recursos no Azure nas regiões suportadas.
> - Atualmente, os VMs da Geração 2 não suportam a encriptação do disco Azure. Consulte [o Suporte para VMs da Geração 2 no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2) para obter mais detalhes.

Você pode aprender os fundamentos da Encriptação de Disco Azure para Linux em apenas alguns minutos com o [Create e encriptar um Linux VM com quickstart Azure CLI](disk-encryption-cli-quickstart.md) ou o [Create e encriptar um Linux VM com quickstart Azure Powershell](disk-encryption-powershell-quickstart.md).

## <a name="supported-vms-and-operating-systems"></a>VMs e sistemas operativos suportados

### <a name="supported-vm-sizes"></a>Tamanhos de VM suportados

Os VMs Linux estão disponíveis numa [gama de tamanhos.](sizes.md) A Encriptação do Disco Azure não está disponível em [VMs básicos, série A,](https://azure.microsoft.com/pricing/details/virtual-machines/series/)ou em máquinas virtuais que não cumprem estes requisitos mínimos de memória:

| Máquina virtual | Requisito mínimo de memória |
|--|--|
| VMs Linux quando apenas encriptam volumes de dados| 2 GB |
| VMs Linux ao encriptar tanto os volumes de dados como os volumes de OS, e onde a utilização do sistema de ficheiros raiz (/) é de 4GB ou menos | 8 GB |
| VMs Linux ao encriptar tanto os volumes de dados como os volumes de OS, e onde a utilização do sistema de ficheiros raiz (/) é superior a 4GB | O uso do sistema de ficheiros radicular * 2. Por exemplo, um uso de 16 GB de sistema de ficheiros radicular requer pelo menos 32GB de RAM |

Uma vez que o processo de encriptação do disco OS esteja concluído em máquinas virtuais Linux, o VM pode ser configurado para ser executado com menos memória. 

A Encriptação azure Disk também está disponível para VMs com armazenamento premium. 

### <a name="supported-operating-systems"></a>Sistemas operativos suportados

A encriptação do disco Azure é suportada num subconjunto das [distribuições Linux apoiadas pelo Azure,](endorsed-distros.md)que é em si um subconjunto de todas as possíveis distribuições do servidor Linux.

![Diagrama venn de distribuição de servidor Linux que suporta encriptação do disco Azure](./media/disk-encryption/ade-supported-distros.png)

As distribuições de servidores Linux que não são endossadas pelo Azure não suportam encriptação do disco Azure; dos que são endossados, apenas as seguintes distribuições e versões suportam encriptação do Disco Azure:

| Distribuição do Linux | Versão | Tipo de volume suportado para a encriptação|
| --- | --- |--- |
| Ubuntu | 18.04| Disco de SO e dados |
| Ubuntu | 16.04| Disco de SO e dados |
| Ubuntu | 14.04.5</br>[com kernel afinado Azure atualizado a 4.15 ou mais tarde](disk-encryption-troubleshooting.md) | Disco de SO e dados |
| RHEL | 7.7 | S e disco de dados (ver nota abaixo) |
| RHEL | 7.6 | S e disco de dados (ver nota abaixo) |
| RHEL | 7.5 | S e disco de dados (ver nota abaixo) |
| RHEL | 7.4 | S e disco de dados (ver nota abaixo) |
| RHEL | 7.3 | S e disco de dados (ver nota abaixo) |
| RHEL | 7.2 | S e disco de dados (ver nota abaixo) |
| RHEL | 6.8 | Disco de dados (ver nota abaixo) |
| RHEL | 6.7 | Disco de dados (ver nota abaixo) |
| CentOS | 7.7 | Disco de SO e dados |
| CentOS | 7.6 | Disco de SO e dados |
| CentOS | 7.5 | Disco de SO e dados |
| CentOS | 7.4 | Disco de SO e dados |
| CentOS | 7.3 | Disco de SO e dados |
| CentOS | 7.2N | Disco de SO e dados |
| CentOS | 6.8 | Disco de dados |
| openSUSE | 42.3 | Disco de dados |
| SLES | 12-SP4 | Disco de dados |
| SLES | 12-SP3 | Disco de dados |

> [!NOTE]
> A nova implementação de encriptação de disco azure é suportada para O RHEL OS e disco de dados para imagens RHEL7 Pay-As-You-Go.  
>
> O ADE também é suportado para Imagens Douradas RHEL Bring-Your-Your-Own-Subscription, mas apenas **depois** de a subscrição ter sido registada . Para mais informações, consulte [Red Hat Enterprise Linux Bring-Your-Your-Own-Subscription Gold Images in Azure](../workloads/redhat/byos.md#encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images)

## <a name="additional-vm-requirements"></a>Requisitos adicionais de VM

A encriptação do disco azure requer que os módulos dm-cripto e vfat estejam presentes no sistema. Remover ou desativar a vfat da imagem predefinida impedirá o sistema de ler o volume da chave e obter a chave necessária para desbloquear os discos em reboots subsequentes. Os passos de endurecimento do sistema que removem o módulo vfat do sistema não são compatíveis com a Encriptação do Disco Azure. 

Antes de permitir a encriptação, os discos de dados a encriptar devem ser corretamente listados em /etc/fstab. Utilize um nome de dispositivo de bloco persistente para esta entrada, como nomes no formato "/ desenvolvimento/sdX" não podem ser confiados para ser associado com o mesmo disco em reinícios, particularmente depois de é aplicada a encriptação de dispositivo. Para mais detalhes sobre este comportamento, consulte: [Troubleshoot Linux VM nome de nome do dispositivo](troubleshoot-device-names-problems.md)

Certifique-se de que as definições de /etc/fstab. estão configuradas corretamente para a montagem. Para configurar estas definições, execute o comando mount - a ou reinicie a VM e acionar a remontagem dessa forma. Quando terminar, verifique a saída do comando lsblk para verificar que a unidade ainda está instalada. 
- Se o ficheiro de /etc/fstab. não montar a unidade corretamente antes de ativar a encriptação, Azure Disk Encryption não será capaz de montá-la corretamente.
- O processo de Azure Disk Encryption irá mover as informações de montagem fora /etc/fstab. e em seu próprio arquivo de configuração como parte do processo de encriptação. Não se assuste para ver a entrada em falta de /etc/fstab. depois de encriptação de unidade de dados é concluída.
- Antes de iniciar a encriptação, certifique-se de parar todos os serviços e processos que possam estar a escrever para discos de dados montados e desativá-los, para que não reiniciem automaticamente após um reboot. Estes poderiam manter os ficheiros abertos nestas divisórias, impedindo que o procedimento de encriptação os remontasse, causando falhas na encriptação. 
- Após a reinicialização, levará tempo para o processo de encriptação de disco do Azure montar os discos encriptados recentemente. Eles não estarão imediatamente disponíveis após um reinício. O processo tem tempo para iniciar, desbloquear e, em seguida, montar os discos encriptados antes de estarem disponíveis para outros processos aceder. Este processo pode demorar mais de um minuto após a reinicialização, dependendo das características do sistema.

Um exemplo de comandos que podem ser usados para montar os discos de dados e criar as entradas necessárias /etc/fstab podem ser encontrados no script CLI de encriptação do [disco Azure](https://github.com/ejarvi/ade-cli-getting-started) (linhas 244-248) e na encriptação do disco [Azure pré-requisitos do script PowerShell](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts). 

## <a name="networking-requirements"></a>Requisitos de networking

Para ativar a funcionalidade de encriptação do disco Azure, os VMs Linux devem satisfazer os seguintes requisitos de configuração do ponto final da rede:
  - Para obter um símbolo para ligar ao seu cofre chave, o VM Linux deve ser capaz de ligar a um ponto final do Diretório Ativo Azure, \[login.microsoftonline.com\].
  - Para escrever as chaves de encriptação do seu cofre de chaves, o VM Linux deve ser capaz de ligar ao ponto final do cofre chave.
  - O Linux VM deve ser capaz de ligar a um ponto final de armazenamento Azure que acolhe o repositório de extensão Azure e uma conta de armazenamento Azure que acolhe os ficheiros VHD.
  -  Se a política de segurança limita o acesso a partir de VMs do Azure para a Internet, pode resolver o URI anterior e configurar uma regra específica para permitir a conectividade de saída para os IPs. Para mais informações, consulte [o Cofre chave Azure atrás de uma firewall](../../key-vault/key-vault-access-behind-firewall.md).  

## <a name="encryption-key-storage-requirements"></a>Requisitos de armazenamento de chaves de encriptação  

A encriptação do disco Azure requer um Cofre de Chave Azure para controlar e gerir chaves e segredos de encriptação de disco. O seu cofre chave e VMs devem residir na mesma região azure e subscrição.

Para mais detalhes, consulte [Criar e configurar um cofre chave para encriptação](disk-encryption-key-vault.md)de disco azure .

## <a name="terminology"></a>Terminologia
A tabela que se segue define alguns dos termos comuns utilizados na documentação de encriptação do disco Azure:

| Terminologia | Definição |
| --- | --- |
| Azure Key Vault | Key Vault é um serviço de gestão chave criptográfica que tem com base no Federal Information Processing Standards (FIPS) módulos de segurança de hardware validado. Estas normas ajudam a salvaguardar as chaves criptográficas e segredos sensíveis. Para mais informações, consulte a documentação do [Cofre de Chaves Azure](https://azure.microsoft.com/services/key-vault/) e [criar e configurar um cofre chave para encriptação](disk-encryption-key-vault.md)de disco azure . |
| CLI do Azure | [O Azure CLI](/cli/azure/install-azure-cli) está otimizado para gerir e administrar recursos Azure a partir da linha de comando.|
| DM-Crypt |[DM-Crypt](https://gitlab.com/cryptsetup/cryptsetup/wikis/DMCrypt) é o subsistema de encriptação de disco transparente baseado em Linux que é usado para ativar a encriptação do disco em VMs Linux. |
| Chave de encriptação (KEK) | A chave assimétrica (RSA 2048) que pode usar para proteger ou embrulhar o segredo. Pode fornecer um módulo de segurança de hardware (HSM)-chave ou chave protegida por software protegidos. Para mais informações, consulte a documentação do [Cofre de Chaves Azure](https://azure.microsoft.com/services/key-vault/) e [criar e configurar um cofre chave para encriptação](disk-encryption-key-vault.md)de disco azure . |
| Cmdlets do PowerShell | Para mais informações, consulte [os cmdlets Azure PowerShell](/powershell/azure/overview). |


## <a name="next-steps"></a>Passos seguintes

- [Quickstart - Crie e criptografe um Linux VM com O ClI Azure](disk-encryption-cli-quickstart.md)
- [Quickstart - Crie e criptografe um Linux VM com Azure Powershell](disk-encryption-powershell-quickstart.md)
- [Cenários do Azure Disk Encryption em VMs do Linux](disk-encryption-linux.md)
- [Encriptação do disco azure pré-requisitos script CLI](https://github.com/ejarvi/ade-cli-getting-started)
- [Encriptação de disco azure pré-requisitos PowerShell script](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [Criar e configurar um cofre de chaves para o Azure Disk Encryption](disk-encryption-key-vault.md)


