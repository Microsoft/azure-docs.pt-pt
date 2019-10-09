---
title: Habilitar Azure Disk Encryption para VMs Linux
description: Este artigo fornece instruções sobre como habilitar a criptografia de disco Microsoft Azure para VMs do Linux.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 053c52d7d1a0282d72ad76408b77c96aa3b0e3e4
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72174695"
---
# <a name="azure-disk-encryption-for-linux-vms"></a>Azure Disk Encryption para VMs Linux 

Azure Disk Encryption ajuda a proteger e proteger seus dados para atender aos compromissos de segurança e conformidade da organização. Ele usa o recurso [DM-cript](https://en.wikipedia.org/wiki/Dm-crypt) do Linux para fornecer criptografia de volume para o sistema operacional e os discos de dados das VMs (máquinas virtuais) do Azure e é integrado com o [Azure Key Vault](../../key-vault/index.yml) para ajudá-lo a controlar e gerenciar as chaves e os segredos de criptografia de disco. 

Se você usar a [central de segurança do Azure](../../security-center/index.yml), você será alertado se tiver VMs que não estão criptografadas. Os alertas mostram como de gravidade alta e a recomendação é de encriptar estas VMs.

![Alerta de encriptação de disco do Centro de segurança do Azure](media/disk-encryption/security-center-disk-encryption-fig1.png)

> [!WARNING]
> - Se você tiver usado anteriormente Azure Disk Encryption com o Azure AD para criptografar uma VM, você deve continuar usando essa opção para criptografar sua VM. Consulte [Azure Disk Encryption com o Azure AD (versão anterior)](disk-encryption-overview-aad.md) para obter detalhes. 
> - Algumas recomendações podem aumentar de dados, a rede ou a utilização de recursos de computação, resultando em custos adicionais de licença ou subscrição. Tem de ter uma subscrição do Azure Active Directory válida para criar recursos no Azure nas regiões suportadas.

Você pode aprender os conceitos básicos do Azure Disk Encryption para Linux em apenas alguns minutos com a [criação e a criptografia de uma VM do Linux com CLI do Azure início rápido](disk-encryption-cli-quickstart.md) ou a [criação e a criptografia de uma VM do Linux com o início rápido do Azure PowerShell](disk-encryption-powershell-quickstart.md).

## <a name="supported-vms-and-operating-systems"></a>VMs e sistemas operacionais com suporte

### <a name="supported-vm-sizes"></a>Tamanhos de VM suportados

As VMs do Linux estão disponíveis em [vários tamanhos](sizes.md). O Azure Disk Encryption não está disponível em [VMs básicas da série A](https://azure.microsoft.com/pricing/details/virtual-machines/series/)ou em máquinas virtuais que não atendem a esses requisitos mínimos de memória:

| Máquina virtual | Requisito mínimo de memória |
|--|--|
| VMs do Linux ao criptografar apenas os volumes de dados| 2 GB |
| VMs do Linux ao criptografar volumes de dados e de so e onde o uso do sistema de arquivos raiz (/) é de 4 GB ou menos | 8 GB |
| VMs do Linux ao criptografar volumes de dados e de so e onde o uso do sistema de arquivos raiz (/) é maior que 4 GB | O uso do sistema de arquivos raiz * 2. Por exemplo, os 16 GB de uso do sistema de arquivos raiz exigem pelo menos 32 GB de RAM |

Depois que o processo de criptografia de disco do sistema operacional for concluído em máquinas virtuais Linux, a VM poderá ser configurada para ser executada com menos memória. 

Azure Disk Encryption também está disponível para VMs com armazenamento Premium. 

### <a name="supported-operating-systems"></a>Sistemas operativos suportados

Azure Disk Encryption tem suporte em um subconjunto de [distribuições Linux endossadas pelo Azure](endorsed-distros.md), que é, em si, um subconjunto de todas as distribuições possíveis do servidor Linux.

![Diagrama de Venn das distribuições do servidor Linux que dão suporte ao Azure Disk Encryption](./media/disk-encryption/ade-supported-distros.png)

As distribuições do servidor Linux que não são endossadas pelo Azure não dão suporte a Azure Disk Encryption; daqueles que são endossados, somente as seguintes distribuições e versões são compatíveis Azure Disk Encryption:

| Distribuição do Linux | Versão | Tipo de volume suportado para a encriptação|
| --- | --- |--- |
| Ubuntu | 18, 4| Disco de SO e dados |
| Ubuntu | 16.04| Disco de SO e dados |
| Ubuntu | 14.04.5</br>[com o kernel ajustado do Azure atualizado para 4,15 ou posterior](disk-encryption-troubleshooting.md) | Disco de SO e dados |
| RHEL | 7,7 | Sistema operacional e disco de dados (veja a observação abaixo) |
| RHEL | 7,6 | Sistema operacional e disco de dados (veja a observação abaixo) |
| RHEL | 7.5 | Sistema operacional e disco de dados (veja a observação abaixo) |
| RHEL | 7.4 | Sistema operacional e disco de dados (veja a observação abaixo) |
| RHEL | 7.3 | Sistema operacional e disco de dados (veja a observação abaixo) |
| RHEL | 7.2 | Sistema operacional e disco de dados (veja a observação abaixo) |
| RHEL | 6.8 | Disco de dados (veja a observação abaixo) |
| RHEL | 6.7 | Disco de dados (veja a observação abaixo) |
| CentOS | 7,7 | Disco de SO e dados |
| CentOS | 7,6 | Disco de SO e dados |
| CentOS | 7.5 | Disco de SO e dados |
| CentOS | 7.4 | Disco de SO e dados |
| CentOS | 7.3 | Disco de SO e dados |
| CentOS | 7.2N | Disco de SO e dados |
| CentOS | 6.8 | Disco de dados |
| openSUSE | 42,3 | Disco de dados |
| SLES | 12-SP4 | Disco de dados |
| SLES | 12-SP3 | Disco de dados |

> [!NOTE]
> A nova implementação de ADE tem suporte para o sistema operacional RHEL e o disco de dados para imagens pré-pagas do RHEL7. No momento, o ADE não tem suporte para imagens do RHEL BYOS (traga sua própria assinatura). 

## <a name="additional-vm-requirements"></a>Requisitos adicionais de VM

Azure Disk Encryption requer que os módulos DM-cript e vfat estejam presentes no sistema. Remover ou desabilitar o vfat da imagem padrão impedirá que o sistema Leia o volume de chave e obtenha a chave necessária para desbloquear os discos em reinicializações subsequentes. As etapas de proteção do sistema que removem o módulo vfat do sistema não são compatíveis com Azure Disk Encryption. 

Antes de habilitar a criptografia, os discos de dados a serem criptografados devem estar corretamente listados em/etc/fstab. Utilize um nome de dispositivo de bloco persistente para esta entrada, como nomes no formato "/ desenvolvimento/sdX" não podem ser confiados para ser associado com o mesmo disco em reinícios, particularmente depois de é aplicada a encriptação de dispositivo. Para obter mais detalhes sobre esse comportamento, consulte: [Solucionar problemas de alterações de nome do dispositivo VM Linux](troubleshoot-device-names-problems.md)

Certifique-se de que as definições de /etc/fstab. estão configuradas corretamente para a montagem. Para configurar estas definições, execute o comando mount - a ou reinicie a VM e acionar a remontagem dessa forma. Quando terminar, verifique a saída do comando lsblk para verificar que a unidade ainda está instalada. 
- Se o ficheiro de /etc/fstab. não montar a unidade corretamente antes de ativar a encriptação, Azure Disk Encryption não será capaz de montá-la corretamente.
- O processo de Azure Disk Encryption irá mover as informações de montagem fora /etc/fstab. e em seu próprio arquivo de configuração como parte do processo de encriptação. Não se assuste para ver a entrada em falta de /etc/fstab. depois de encriptação de unidade de dados é concluída.
- Antes de iniciar a criptografia, certifique-se de interromper todos os serviços e processos que podem estar gravando em discos de dados montados e desabilitá-los, para que eles não sejam reiniciados automaticamente após uma reinicialização. Eles poderiam manter os arquivos abertos nessas partições, impedindo o procedimento de criptografia para remontá-los, causando a falha da criptografia. 
- Após a reinicialização, levará tempo para o processo de encriptação de disco do Azure montar os discos encriptados recentemente. Eles não estarão imediatamente disponíveis após um reinício. O processo tem tempo para iniciar, desbloquear e, em seguida, montar os discos encriptados antes de estarem disponíveis para outros processos aceder. Este processo pode demorar mais de um minuto após a reinicialização, dependendo das características do sistema.

Um exemplo de comandos que podem ser usados para montar os discos de dados e criar as entradas de/etc/fstab necessárias pode ser encontrado no [script da CLI de Azure Disk Encryption pré-requisitos](https://github.com/ejarvi/ade-cli-getting-started) (linhas 244-248) e o [PowerShell de pré-requisitos de Azure Disk Encryption script](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts). 

## <a name="networking-requirements"></a>Requisitos de rede

Para habilitar o recurso Azure Disk Encryption, as VMs do Linux devem atender aos seguintes requisitos de configuração de ponto de extremidade de rede:
  - Para obter um token para se conectar ao cofre de chaves, a VM do Linux deve ser capaz de se conectar a um ponto de extremidade Azure Active Directory, @no__t -0login. microsoftonline. com @ no__t-1.
  - Para gravar as chaves de criptografia no cofre de chaves, a VM do Linux deve ser capaz de se conectar ao ponto de extremidade do cofre de chaves.
  - A VM do Linux deve ser capaz de se conectar a um ponto de extremidade de armazenamento do Azure que hospeda o repositório de extensões do Azure e uma conta de armazenamento do Azure que hospeda os arquivos VHD.
  -  Se a política de segurança limita o acesso a partir de VMs do Azure para a Internet, pode resolver o URI anterior e configurar uma regra específica para permitir a conectividade de saída para os IPs. Para obter mais informações, consulte [do Azure Key Vault protegido por uma firewall](../../key-vault/key-vault-access-behind-firewall.md).  

## <a name="encryption-key-storage-requirements"></a>Requisitos de armazenamento de chave de criptografia  

Azure Disk Encryption requer uma Azure Key Vault para controlar e gerenciar chaves e segredos de criptografia de disco. O cofre de chaves e as VMs devem residir na mesma região e assinatura do Azure.

Para obter detalhes, consulte [criando e configurando um cofre de chaves para Azure Disk Encryption](disk-encryption-key-vault.md).

## <a name="terminology"></a>Terminologia
A tabela a seguir define alguns dos termos comuns usados na documentação de criptografia de disco do Azure:

| Terminologia | Definição |
| --- | --- |
| Azure Key Vault | Key Vault é um serviço de gestão chave criptográfica que tem com base no Federal Information Processing Standards (FIPS) módulos de segurança de hardware validado. Estas normas ajudam a salvaguardar as chaves criptográficas e segredos sensíveis. Para obter mais informações, consulte a documentação do [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) e [criando e configurando um cofre de chaves para Azure Disk Encryption](disk-encryption-key-vault.md). |
| CLI do Azure | [A CLI do Azure](/cli/azure/install-azure-cli) está otimizada para gerir e administrar recursos do Azure a partir da linha de comandos.|
| DM-Crypt |[DM-cript](https://gitlab.com/cryptsetup/cryptsetup/wikis/DMCrypt) é o subsistema de criptografia de disco transparente, baseado em Linux, usado para habilitar a criptografia de disco em VMs do Linux. |
| Chave de criptografia de chave (KEK) | A chave assimétrica (RSA 2048) que você pode usar para proteger ou encapsular o segredo. Pode fornecer um módulo de segurança de hardware (HSM)-chave ou chave protegida por software protegidos. Para obter mais informações, consulte a documentação do [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) e [criando e configurando um cofre de chaves para Azure Disk Encryption](disk-encryption-key-vault.md). |
| Cmdlets do PowerShell | Para obter mais informações, consulte [cmdlets do Azure PowerShell](/powershell/azure/overview). |


## <a name="next-steps"></a>Passos seguintes

- [Início rápido-criar e criptografar uma VM Linux com CLI do Azure](disk-encryption-cli-quickstart.md)
- [Início rápido-criar e criptografar uma VM do Linux com o Azure PowerShell](disk-encryption-powershell-quickstart.md)
- [Cenários de Azure Disk Encryption em VMs Linux](disk-encryption-linux.md)
- [Script da CLI de pré-requisitos Azure Disk Encryption](https://github.com/ejarvi/ade-cli-getting-started)
- [Script do PowerShell de Azure Disk Encryption pré-requisitos](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [Criando e configurando um cofre de chaves para Azure Disk Encryption](disk-encryption-key-vault.md)


