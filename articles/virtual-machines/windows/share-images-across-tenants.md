---
title: Partilhar imagens da galeria em inquilinos no Azure | Documentos da Microsoft
description: Saiba como partilhar imagens de VM em inquilinos do Azure, galerias de imagem partilhada a utilizar.
services: virtual-machines-windows
author: cynthn
manager: jeconnoc
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 04/05/2019
ms.author: cynthn
ms.openlocfilehash: 5b3c4e5380c65b2ab6c736e7fabe1813fe32afc2
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67466488"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>Partilhar imagens de VM de galeria em inquilinos do Azure

[!INCLUDE [virtual-machines-share-images-across-tenants](../../../includes/virtual-machines-share-images-across-tenants.md)]


> [!IMPORTANT]
> Não é possível utilizar o portal para implementar uma VM a partir de uma imagem no outro inquilino do azure. Para criar uma VM a partir de uma imagem partilhada entre inquilinos, tem de utilizar o [CLI do Azure](../linux/share-images-across-tenants.md) ou o Powershell.

## <a name="create-a-vm-using-powershell"></a>Criar uma VM com o PowerShell


Inicie sessão nos dois inquilinos com o ID da aplicação, ID de inquilino e o segredo. 

```azurepowershell-interactive
$applicationId = '<App ID>'
$secret = <Secret> | ConvertTo-SecureString -AsPlainText -Force
$tenant1 = "<Tenant 1 ID>"
$tenant2 = "<Tenant 2 ID>"
$cred = New-Object -TypeName PSCredential -ArgumentList $applicationId, $secret
Clear-AzContext
Connect-AzAccount -ServicePrincipal -Credential $cred  -Tenant "<Tenant 1 ID>"
Connect-AzAccount -ServicePrincipal -Credential $cred -Tenant "<Tenant 2 ID>"
```

Crie a VM no grupo de recursos que tenha permissão sobre o registo de aplicações. Substitua as informações neste exemplo pelos seus próprios.

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$image = "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>"
New-AzVm `
   -ResourceGroupName "myResourceGroup" `
   -Name "myVMfromImage" `
   -Image $image `
   -Location "South Central US" `
   -VirtualNetworkName "myImageVnet" `
   -SubnetName "myImageSubnet" `
   -SecurityGroupName "myImageNSG" `
   -PublicIpAddressName "myImagePIP" `
   -OpenPorts 3389
```

## <a name="next-steps"></a>Passos Seguintes

Também pode criar imagem partilhada recursos da galeria com o [portal do Azure](shared-images-portal.md).