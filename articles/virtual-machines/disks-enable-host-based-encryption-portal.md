---
title: Ativar encriptação de ponta a ponta utilizando encriptação no host - portal Azure - discos geridos
description: Utilize encriptação no anfitrião para ativar a encriptação de ponta a ponta nos discos geridos Azure - Portal Azure.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: cdb22805e2e68893d3883272b66c2cfac13c807e
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "104721873"
---
# <a name="use-the-azure-portal-to-enable-end-to-end-encryption-using-encryption-at-host"></a>Utilize o portal Azure para ativar a encriptação de ponta a ponta utilizando encriptação no anfitrião

Quando ativa a encriptação no anfitrião, os dados armazenados no anfitrião VM são encriptados em repouso e os fluxos encriptados para o serviço de Armazenamento. Para obter informações conceptuais sobre encriptação no anfitrião, bem como outros tipos de encriptação de discos geridos, consulte:

* Linux: [Encriptação no anfitrião - Encriptação de ponta a ponta para os seus dados VM](./disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data).

* Windows: [Encriptação no anfitrião - Encriptação de ponta a ponta para os seus dados VM](./disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data).

## <a name="restrictions"></a>Restrições

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]


### <a name="supported-vm-sizes"></a>Tamanhos de VM suportados

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

## <a name="prerequisites"></a>Pré-requisitos

Tem de ativar a funcionalidade da sua subscrição antes de utilizar a propriedade EncryptionAtHost para o seu VM/VMSS. Siga os passos abaixo para ativar a funcionalidade para a sua subscrição:

1. **Portal Azure**: Selecione o ícone Cloud Shell no [portal Azure](https://portal.azure.com):

    ![Ícone para lançar a Cloud Shell a partir do portal Azure](../Cloud-Shell/media/overview/portal-launch-icon.png)
    
2.  Execute o seguinte comando para registar a funcionalidade para a sua subscrição

    ```powershell
     Register-AzProviderFeature -FeatureName "EncryptionAtHost" -ProviderNamespace "Microsoft.Compute" 
    ```

3.  Verifique se o estado de registo está registado (demora alguns minutos) usando o comando abaixo antes de experimentar a funcionalidade.

    ```powershell
     Get-AzProviderFeature -FeatureName "EncryptionAtHost" -ProviderNamespace "Microsoft.Compute"  
    ```


Inscreva-se no portal Azure utilizando o [link fornecido](https://aka.ms/diskencryptionupdates).

> [!IMPORTANT]
> Deve utilizar o [link fornecido](https://aka.ms/diskencryptionupdates) para aceder ao portal Azure. A encriptação no anfitrião não é atualmente visível no portal público Azure sem utilizar o link.

### <a name="create-an-azure-key-vault-and-disk-encryption-set"></a>Crie um cofre de chave Azure e conjunto de encriptação de disco

Uma vez ativada a funcionalidade, terá de configurar um Cofre de Chaves Azure e um conjunto de encriptação de disco, se ainda não o fez.

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-portal](../../includes/virtual-machines-disks-encryption-create-key-vault-portal.md)]

## <a name="deploy-a-vm"></a>Implementar uma VM

Tem de implementar um novo VM para permitir a encriptação no hospedeiro, não podendo ser ativado em VM existentes.

1. Procure por **Máquinas Virtuais** e selecione **+ Adicionar** para criar um VM.
1. Crie uma nova máquina virtual, selecione uma região apropriada e um tamanho VM suportado.
1. Preencha os outros valores na lâmina **Básica** como quiser e, em seguida, dirija-se à lâmina **dos Discos.**

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-at-host-basic-blade.png" alt-text="A screenshot da máquina virtual criação de lâminas básicas, região e tamanho V M são destacados.":::

1. Na lâmina **Discos,** selecione **Sim** para **Encriptação no anfitrião**.
1. Faça as restantes seleções como quiser.

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-at-host-disk-blade.png" alt-text="A screenshot da lâmina de criação de discos de criação de máquinas virtuais, a encriptação no anfitrião é realçada.":::

1. Termine o processo de implantação de VM, faça seleções que se adaptem ao seu ambiente.

Agora implementou um VM com encriptação no anfitrião ativado, todos os seus discos associados serão encriptados usando encriptação no anfitrião.

## <a name="next-steps"></a>Passos seguintes

[Amostras de modelo do gestor de recursos Azure](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/EncryptionAtHost)
