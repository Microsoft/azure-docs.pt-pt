---
title: Ativar a dupla encriptação em repouso - portal Azure - discos geridos
description: Ativar a dupla encriptação em repouso para os dados do disco geridos utilizando o portal Azure.
author: roygara
ms.date: 07/23/2020
ms.topic: how-to
ms.author: rogarana
ms.service: virtual-machines-linux
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 7fefaa3b13b73db119da696738af66e84f8bdd63
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2020
ms.locfileid: "87136173"
---
# <a name="azure-portal---enable-double-encryption-at-rest-on-your-managed-disks"></a>Portal Azure - Ative a dupla encriptação em repouso nos seus discos geridos

O Azure Disk Storage suporta a dupla encriptação em repouso para discos geridos. Para obter informações conceptuais sobre a dupla encriptação em repouso, bem como outros tipos de encriptação de disco gerido, consulte a [encriptação dupla na](disk-encryption.md#double-encryption-at-rest) secção de repouso do nosso artigo de encriptação de disco.

[!INCLUDE [virtual-machines-double-encryption-at-rest-portal](../../../includes/virtual-machines-disks-double-encryption-at-rest-portal.md)]

## <a name="next-steps"></a>Passos seguintes

- [Amostras de modelo do gestor de recursos Azure](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/DoubleEncryption)
- [Ativar as chaves geridas pelo cliente com encriptação do lado do servidor - Exemplos](disks-enable-customer-managed-keys-cli.md#examples)