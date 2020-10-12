---
title: Portal Azure - Ativar chaves geridas pelo cliente com SSE - discos geridos
description: Ativar as teclas geridas pelo cliente nos discos geridos através do portal Azure.
author: roygara
ms.date: 08/24/2020
ms.topic: how-to
ms.author: rogarana
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 1b40eef0a4cae8fa68631426ff72003db43d7530
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88817875"
---
# <a name="use-the-azure-portal-to-enable-server-side-encryption-with-customer-managed-keys-for-managed-disks"></a>Utilize o portal Azure para ativar a encriptação do lado do servidor com chaves geridas pelo cliente para discos geridos

O Armazenamento de Discos Azure permite-lhe gerir as suas próprias chaves ao utilizar a encriptação do lado do servidor (SSE) para discos geridos, se escolher. Para obter informações conceptuais sobre a SSE com as teclas geridas pelo cliente, bem como outros tipos de encriptação de discos geridos, consulte a secção de **chaves gerida pelo Cliente** do nosso artigo de encriptação de disco:

- Para o Linux: [Chaves geridas pelo cliente](./linux/disk-encryption.md#customer-managed-keys)
- Para Windows: [Chaves geridas pelo cliente](./windows/disk-encryption.md#customer-managed-keys)

## <a name="restrictions"></a>Restrições

Por enquanto, as chaves geridas pelo cliente têm as seguintes restrições:

- Se esta função estiver ativada para o seu disco, não poderá desativá-la.
    Se precisar de contornar isto, tem de copiar todos os dados para um disco gerido totalmente diferente que não utilize chaves geridas pelo cliente:

    - Para Linux: [Copiar um disco gerido](./linux/disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk)

    - Para windows: [Copiar um disco gerido](./windows/disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk)

- Apenas [o software e as teclas HSM RSA](../key-vault/keys/about-keys.md) dos tamanhos de 2.048 bits, 3.072 bits e 4.096 bits são suportadas, sem outras chaves ou tamanhos.
    - As chaves [HSM](../key-vault/keys/hsm-protected-keys.md) requerem o nível **premium** dos cofres da Chave Azure.
[!INCLUDE [virtual-machines-managed-disks-customer-managed-keys-restrictions](../../includes/virtual-machines-managed-disks-customer-managed-keys-restrictions.md)]

As seguintes secções abrangem como ativar e utilizar chaves geridas pelo cliente para discos geridos:

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-portal](../../includes/virtual-machines-disks-encryption-create-key-vault-portal.md)]

## <a name="deploy-a-vm"></a>Implementar uma VM

Agora que criou e montou o cofre de chaves e o conjunto de encriptação do disco, pode implementar um VM usando a encriptação.
O processo de implementação de VM é semelhante ao processo de implementação padrão, as únicas diferenças são que você precisa implementar o VM na mesma região que os seus outros recursos e você opta por usar uma chave gerida pelo cliente.

1. Procure por **Máquinas Virtuais** e selecione **+ Adicionar** para criar um VM.
1. Na lâmina **Basic,** selecione a mesma região que o seu conjunto de encriptação de disco e a Azure Key Vault.
1. Preencha os outros valores na lâmina **Básica** como quiser.

    ![Screenshot da experiência de criação de VM, com o valor da região em destaque.](media/virtual-machines-disk-encryption-portal/server-side-encryption-create-a-vm-region.png)

1. Na lâmina **Discos,** selecione **Encriptação em repouso com uma chave gerida pelo cliente**.
1. Selecione o conjunto de encriptação do disco no conjunto de encriptação do **disco.**
1. Faça as restantes seleções como quiser.

    ![Screenshot da experiência de criação de VM, a lâmina dos discos. Com o conjunto de encriptação do disco em destaque.](media/virtual-machines-disk-encryption-portal/server-side-encryption-create-vm-select-customer-managed-key-disk-encryption-set.png)

## <a name="enable-on-an-existing-disk"></a>Ativar num disco existente

> [!CAUTION]
> Ativar a encriptação do disco em quaisquer discos ligados a um VM exigirá que pare o VM.
    
1. Navegue para um VM que está na mesma região que um dos seus conjuntos de encriptação de disco.
1. Abra o VM e **selecione Stop**.

    ![Screenshot da sobreposição principal para o seu exemplo VM, com o botão Stop realçado.](media/virtual-machines-disk-encryption-portal/server-side-encryption-stop-vm-to-encrypt-disk-fix.png)

1. Depois de o VM ter terminado de parar, selecione **Discos** e, em seguida, selecione o disco que pretende encriptar.

    ![Screenshot do seu exemplo VM, com a lâmina dos Discos aberta. O disco SO é realçado, como um disco de exemplo para você selecionar.](media/virtual-machines-disk-encryption-portal/server-side-encryption-existing-disk-select.png)

1. Selecione **Encriptação** e selecione **Encriptação em repouso com uma chave gerida pelo cliente** e, em seguida, selecione o conjunto de encriptação do disco na lista de drop-down.
1. Selecione **Guardar**.

    ![Screenshot do seu disco DE EXEMPLO. A lâmina de encriptação está aberta, a encriptação em repouso com uma chave gerida pelo cliente é selecionada, bem como o seu exemplo Azure Key Vault. Depois de estois, o botão de poupança é selecionado.](media/virtual-machines-disk-encryption-portal/server-side-encryption-encrypt-existing-disk-customer-managed-key.png)

1. Repita este processo para quaisquer outros discos ligados ao VM que pretende encriptar.
1. Quando os discos terminarem de mudar para teclas geridas pelo cliente, se não houver outros discos anexos que queira encriptar, poderá iniciar o seu VM.

> [!IMPORTANT]
> As chaves geridas pelo cliente dependem de identidades geridas para recursos Azure, uma característica do Azure Ative Directory (Azure AD). Ao configurar as chaves geridas pelo cliente, uma identidade gerida é automaticamente atribuída aos seus recursos sob as capas. Se posteriormente mover a subscrição, o grupo de recursos ou o disco gerido de um diretório AD Azure para outro, a identidade gerida associada aos discos geridos não é transferida para o novo inquilino, pelo que as chaves geridas pelo cliente podem deixar de funcionar. Para obter mais informações, consulte [a transferência de uma subscrição entre os diretórios AD da Azure](../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

## <a name="next-steps"></a>Passos seguintes

- [Explore os modelos do Gestor de Recursos Azure para criar discos encriptados com chaves geridas pelo cliente](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [O que é o Azure Key Vault?](../key-vault/general/overview.md)
- [Replicar máquinas com chaves geridas pelo cliente](../site-recovery/azure-to-azure-how-to-enable-replication-cmk-disks.md)
- [Configurar a recuperação após desastre de VMs do VMware para o Azure com o PowerShell](../site-recovery/vmware-azure-disaster-recovery-powershell.md#replicate-vmware-vms)
- [Configurar a recuperação após desastre para o Azure para VMs Hyper-V com o PowerShell e com o Azure Resource Manager](../site-recovery/hyper-v-azure-powershell-resource-manager.md#step-7-enable-vm-protection)
