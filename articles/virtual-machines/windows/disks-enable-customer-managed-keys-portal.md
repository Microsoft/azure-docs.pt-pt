---
title: Utilize o portal Azure para permitir chaves geridas pelo cliente com SSE - discos geridos
description: Utilize o portal Azure para ativar a encriptação do lado do servidor utilizando teclas geridas pelo cliente nos discos geridos pelo cliente.
author: roygara
ms.date: 07/10/2020
ms.topic: how-to
ms.author: rogarana
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 4e3699e1fab928db5a466073c411b701ffc1a51d
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2020
ms.locfileid: "86236033"
---
# <a name="enable-customer-managed-keys-with-server-side-encryption---managed-disks---portal"></a>Ativar as teclas geridas pelo cliente com encriptação do lado do servidor - discos geridos - portal

O Armazenamento de Discos Azure permite-lhe gerir as suas próprias chaves ao utilizar a encriptação do lado do servidor (SSE) para discos geridos, se escolher. Para obter informações conceptuais sobre a SSE com as teclas geridas pelo cliente, bem como outros tipos de encriptação de discos geridos, consulte a secção de [chaves gerida pelo Cliente](disk-encryption.md#customer-managed-keys) do nosso artigo de encriptação de disco.

## <a name="restrictions"></a>Restrições

Por enquanto, as chaves geridas pelo cliente têm as seguintes restrições:

- Se esta função estiver ativada para o seu disco, não poderá desativá-la.
    Se precisar de contornar isto, tem de [copiar todos os dados](disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk) para um disco gerido totalmente diferente que não esteja a utilizar chaves geridas pelo cliente.
[!INCLUDE [virtual-machines-managed-disks-customer-managed-keys-restrictions](../../../includes/virtual-machines-managed-disks-customer-managed-keys-restrictions.md)]

As seguintes secções abrangem como ativar e utilizar chaves geridas pelo cliente para discos geridos:

[!INCLUDE [virtual-machines-disks-encryption-portal](../../../includes/virtual-machines-disks-encryption-portal.md)]

> [!IMPORTANT]
> As chaves geridas pelo cliente dependem de identidades geridas para recursos Azure, uma característica do Azure Ative Directory (Azure AD). Ao configurar as chaves geridas pelo cliente, uma identidade gerida é automaticamente atribuída aos seus recursos sob as capas. Se posteriormente mover a subscrição, o grupo de recursos ou o disco gerido de um diretório AD Azure para outro, a identidade gerida associada aos discos geridos não é transferida para o novo inquilino, pelo que as chaves geridas pelo cliente podem deixar de funcionar. Para obter mais informações, consulte [a transferência de uma subscrição entre os diretórios AD da Azure](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

## <a name="next-steps"></a>Passos seguintes

- [Explore os modelos do Gestor de Recursos Azure para criar discos encriptados com chaves geridas pelo cliente](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [O que é o Azure Key Vault?](../../key-vault/general/overview.md)
- [Replicar máquinas com chaves geridas pelo cliente](../../site-recovery/azure-to-azure-how-to-enable-replication-cmk-disks.md)
- [Configurar a recuperação de VMware VMs para Azure com a PowerShell](../../site-recovery/vmware-azure-disaster-recovery-powershell.md#replicate-vmware-vms)
- [Confiem da recuperação de desastres para a Azure para VMs hiper-V usando PowerShell e Azure Resource Manager](../../site-recovery/hyper-v-azure-powershell-resource-manager.md#step-7-enable-vm-protection)