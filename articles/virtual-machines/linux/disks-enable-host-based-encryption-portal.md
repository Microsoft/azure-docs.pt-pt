---
title: Ativar encriptação de ponta a ponta utilizando encriptação no host - portal Azure - discos geridos
description: Utilize encriptação no anfitrião para ativar a encriptação de ponta a ponta nos discos geridos Azure.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 07/23/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 96506e0ca09e9491135faa69d0b690b91ccc8177
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2020
ms.locfileid: "87136252"
---
# <a name="enable-end-to-end-encryption-using-encryption-at-host---azure-portal"></a>Ativar encriptação de ponta a ponta utilizando encriptação no host - Portal Azure

Quando ativa a encriptação no anfitrião, os dados armazenados no anfitrião VM são encriptados em repouso e os fluxos encriptados para o serviço de Armazenamento. Para obter informações conceptuais sobre encriptação no anfitrião, bem como outros tipos de encriptação de discos [geridos, consulte a Encriptação no anfitrião - Encriptação de ponta a ponta para os seus dados VM](disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data).

[!INCLUDE [virtual-machines-disks-encryption-at-host-portal](../../../includes/virtual-machines-disks-encryption-at-host-portal.md)]

## <a name="next-steps"></a>Passos seguintes

[Amostras de modelo do gestor de recursos Azure](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/EncryptionAtHost)