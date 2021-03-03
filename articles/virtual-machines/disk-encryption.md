---
title: Encriptação do lado do servidor dos discos geridos pelo Azure
description: O Azure Storage protege os seus dados encriptando-os em repouso antes de os persistir nos clusters de armazenamento. Pode utilizar as chaves geridas pelo cliente para gerir a encriptação com as suas próprias chaves, ou pode confiar nas chaves geridas pela Microsoft para a encriptação dos seus discos geridos.
author: roygara
ms.date: 10/22/2020
ms.topic: conceptual
ms.author: rogarana
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: f9152e341ac04209754bbf5f008cd56373967b9f
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101677451"
---
# <a name="server-side-encryption-of-azure-disk-storage"></a>Encriptação do lado do servidor do armazenamento do disco Azure

A encriptação do lado do servidor (SSE) protege os seus dados e ajuda-o a cumprir os seus compromissos de segurança organizacional e de conformidade. A SSE encripta automaticamente os seus dados armazenados em discos geridos aZure (OS e discos de dados) em repouso por padrão quando os persiste na nuvem. 

Os dados em discos geridos azure são encriptados de forma transparente utilizando [encriptação AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)de 256 bits , uma das cifras de blocos mais fortes disponíveis, e é compatível com FIPS 140-2. Para obter mais informações sobre os módulos criptográficos subjacentes aos discos geridos pelo Azure, consulte [Cryptography API: Next Generation](/windows/desktop/seccng/cng-portal)

A encriptação do lado do servidor não afeta o desempenho dos discos geridos e não há custo adicional. 

> [!NOTE]
> Os discos temporários não são discos geridos e não são encriptados pela SSE, a menos que ative a encriptação no anfitrião.

## <a name="about-encryption-key-management"></a>Sobre a gestão de chaves de encriptação

Pode confiar em chaves geridas pela plataforma para a encriptação do seu disco gerido, ou pode gerir a encriptação usando as suas próprias chaves. Se optar por gerir a encriptação com as suas próprias chaves, pode especificar uma *chave gerida pelo cliente* para encriptar e desencriptar todos os dados em discos geridos. 

As secções seguintes descrevem cada uma das opções para a gestão das chaves com maior detalhe.

### <a name="platform-managed-keys"></a>Chaves geridas pela plataforma

Por predefinição, os discos geridos utilizam chaves de encriptação geridas pela plataforma. Todos os discos geridos, instantâneos, imagens e dados escritos para discos geridos existentes são automaticamente encriptados em repouso com teclas geridas pela plataforma.

### <a name="customer-managed-keys"></a>Chaves geridas pelo cliente

[!INCLUDE [virtual-machines-managed-disks-description-customer-managed-keys](../../includes/virtual-machines-managed-disks-description-customer-managed-keys.md)]

#### <a name="restrictions"></a>Restrições

Por enquanto, as chaves geridas pelo cliente têm as seguintes restrições:

- Se esta função estiver ativada para o seu disco, não poderá desativá-la.
    Se precisar de contornar isto, tem de copiar todos os dados utilizando o [módulo Azure PowerShell](windows/disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk) ou o [Azure CLI](linux/disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk), para um disco gerido inteiramente diferente que não esteja a utilizar chaves geridas pelo cliente.
[!INCLUDE [virtual-machines-managed-disks-customer-managed-keys-restrictions](../../includes/virtual-machines-managed-disks-customer-managed-keys-restrictions.md)]

#### <a name="supported-regions"></a>Regiões suportadas

As chaves geridas pelo cliente estão disponíveis em todas as regiões que os discos geridos estão disponíveis.

A rotação automática da chave está em pré-visualização e só está disponível nas seguintes regiões:

- E.U.A. Leste
- E.U.A. Leste 2
- E.U.A. Centro-Sul
- E.U.A. Oeste
- E.U.A. Oeste 2
- Europa do Norte
- Europa Ocidental
- França Central

> [!IMPORTANT]
> As chaves geridas pelo cliente dependem de identidades geridas para recursos Azure, uma característica do Azure Ative Directory (Azure AD). Ao configurar as chaves geridas pelo cliente, uma identidade gerida é automaticamente atribuída aos seus recursos sob as capas. Se posteriormente mover a subscrição, o grupo de recursos ou o disco gerido de um diretório AD Azure para outro, a identidade gerida associada aos discos geridos não é transferida para o novo inquilino, pelo que as chaves geridas pelo cliente podem deixar de funcionar. Para obter mais informações, consulte [a transferência de uma subscrição entre os diretórios AD da Azure](../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

## <a name="encryption-at-host---end-to-end-encryption-for-your-vm-data"></a>Encriptação no anfitrião - Encriptação de ponta a ponta para os seus dados VM

Quando ativa a encriptação no anfitrião, essa encriptação começa no próprio anfitrião VM, o servidor Azure a que o seu VM está atribuído. Os dados relativos ao disco temporário e às caches de disco de oss/dados são armazenados no anfitrião VM. Depois de permitir a encriptação no anfitrião, todos estes dados são encriptados em repouso e fluem encriptados para o serviço de Armazenamento, onde é persistido. Essencialmente, a encriptação no anfitrião encripta os seus dados de ponta a ponta. A encriptação no anfitrião não utiliza o CPU do seu VM e não afeta o desempenho do seu VM. 

Os discos temporários e os discos de OS efémeros são encriptados em repouso com chaves geridas pela plataforma quando ativam a encriptação de ponta a ponta. Os caches de so e de disco de dados são encriptados em repouso com as teclas geridas pelo cliente ou geridas pela plataforma, dependendo do tipo de encriptação de disco selecionado. Por exemplo, se um disco for encriptado com teclas geridas pelo cliente, então o cache do disco é encriptado com teclas geridas pelo cliente, e se um disco é encriptado com teclas geridas pela plataforma, então o cache para o disco é encriptado com teclas geridas pela plataforma.

### <a name="restrictions"></a>Restrições

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]

#### <a name="supported-regions"></a>Regiões suportadas

[!INCLUDE [virtual-machines-disks-encryption-at-host-regions](../../includes/virtual-machines-disks-encryption-at-host-regions.md)]

#### <a name="supported-vm-sizes"></a>Tamanhos de VM suportados

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

## <a name="double-encryption-at-rest"></a>Dupla encriptação em repouso

Clientes sensíveis de alta segurança que estejam preocupados com o risco associado a qualquer algoritmo de encriptação, implementação ou chave que está a ser comprometido podem agora optar por camadas adicionais de encriptação usando um algoritmo/modo de encriptação diferente na camada de infraestrutura usando chaves de encriptação geridas pela plataforma. Esta nova camada pode ser aplicada a sos e discos de dados, instantâneos e imagens, que serão encriptados em repouso com dupla encriptação.

### <a name="supported-regions"></a>Regiões suportadas

A dupla encriptação está disponível em todas as regiões que os discos geridos estão disponíveis.

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>Encriptação do lado do servidor contra encriptação do disco Azure

[A Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md) aproveita a funcionalidade [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) do Linux ou a funcionalidade [BitLocker](/windows/security/information-protection/bitlocker/bitlocker-overview) do Windows para encriptar discos geridos com chaves geridas pelo cliente dentro do VM do hóspede.  A encriptação do lado do servidor com as teclas geridas pelo cliente melhora no ADE, permitindo-lhe utilizar quaisquer tipos e imagens de OS para os seus VMs encriptando dados no serviço de Armazenamento.
> [!IMPORTANT]
> As chaves geridas pelo cliente dependem de identidades geridas para recursos Azure, uma característica do Azure Ative Directory (Azure AD). Ao configurar as chaves geridas pelo cliente, uma identidade gerida é automaticamente atribuída aos seus recursos sob as capas. Se posteriormente mover a subscrição, o grupo de recursos ou o disco gerido de um diretório AD Azure para outro, a identidade gerida associada aos discos geridos não é transferida para o novo inquilino, pelo que as chaves geridas pelo cliente podem deixar de funcionar. Para obter mais informações, consulte [a transferência de uma subscrição entre os diretórios AD da Azure](../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

## <a name="next-steps"></a>Passos seguintes

- Ativar a encriptação de ponta a ponta utilizando a encriptação no hospedeiro com o [módulo Azure PowerShell](windows/disks-enable-host-based-encryption-powershell.md), o [Azure CLI](linux/disks-enable-host-based-encryption-cli.md)ou o [portal Azure](disks-enable-host-based-encryption-portal.md).
- Ativar a dupla encriptação em repouso para discos geridos com o [módulo Azure PowerShell,](windows/disks-enable-double-encryption-at-rest-powershell.md)o [Azure CLI](linux/disks-enable-double-encryption-at-rest-cli.md) ou o [portal Azure](disks-enable-double-encryption-at-rest-portal.md).
- Ativar as chaves geridas pelo cliente para discos geridos com o [módulo Azure PowerShell,](windows/disks-enable-customer-managed-keys-powershell.md)o [Azure CLI](linux/disks-enable-customer-managed-keys-cli.md) ou o [portal Azure](disks-enable-customer-managed-keys-portal.md).
- [Explore os modelos do Gestor de Recursos Azure para criar discos encriptados com chaves geridas pelo cliente](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [O que é o Azure Key Vault?](../key-vault/general/overview.md)