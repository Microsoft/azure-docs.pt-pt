---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/23/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: bffe948eec81b480e51d0cf5a25f6091f397dd15
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58372855"
---
O armazenamento do Azure oferece vários tipos de contas de armazenamento. Cada tipo oferece suporte a recursos diferentes e tem o seu próprio modelo de preços. Considere estas diferenças antes de criar uma conta de armazenamento para determinar o tipo de conta que é melhor para as suas aplicações. Os tipos de contas de armazenamento são:

- **Contas para fins gerais v2**: Tipo de conta de armazenamento básico para blobs, ficheiros, filas e tabelas. Recomendada para a maioria dos cenários que utilizam o armazenamento do Azure.
- **Contas de fins gerais v1**: Tipo de conta legada para blobs, ficheiros, filas e tabelas. Utilize contas de fins gerais v2 em vez disso, sempre que possível.
- **Bloquear contas do blob storage**: Contas de armazenamento apenas de Blobs com características de desempenho premium. Recomendada para cenários com taxas de transações elevada, usando objetos menores ou sem ter de latência de armazenamento consistentemente baixo.
- **Contas do blob storage**: Contas de armazenamento apenas de Blobs. Utilize contas de fins gerais v2 em vez disso, sempre que possível.

A tabela seguinte descreve os tipos de contas de armazenamento e as respetivas funcionalidades:

| Tipo de conta de armazenamento | Serviços suportados                       | Escalões de desempenho suportados      | Suporte de acesso         | Opções de replicação               | Modelo de implementação<sup>1</sup> | Encriptação<sup>2</sup> |
|----------------------|------------------------------------------|-----------------------------|--------------------------------|-----------------------------------|------------------------------|------------------------|
| Para fins gerais V2   | BLOB, ficheiro, fila, tabela e disco       | Standard, Premium<sup>5</sup> | Frequente, esporádico, arquivo<sup>3</sup> | LRS, ZRS<sup>4</sup>, GRS, RA-GRS | Resource Manager             | Encriptados              |
| Para fins gerais V1   | BLOB, ficheiro, fila, tabela e disco       | Standard, Premium<sup>5</sup> | N/A                            | LRS, GRS, RA-GRS                  | Gestor de recursos, clássico    | Encriptados              |
| Armazenamento de BLOBs de blocos   | BLOB (blobs de blocos e de acréscimo apenas) | Premium                       | N/A                            | LRS                               | Resource Manager             | Encriptados              |
| Armazenamento de blobs         | BLOB (blobs de blocos e de acréscimo apenas) | Standard                      | Frequente, esporádico, arquivo<sup>3</sup> | LRS, GRS, RA-GRS                  | Resource Manager             | Encriptados              |

<sup>1</sup>é recomendado utilizar o modelo de implementação Azure Resource Manager. Contas de armazenamento com o modelo de implementação clássica ainda podem ser criadas em alguns locais e contas clássicas existentes continuarão a ter suporte. Para obter mais informações, consulte [vs. de implementação clássica do Azure Resource Manager: Compreender os modelos de implementação e o estado dos seus recursos](../articles/azure-resource-manager/resource-manager-deployment-model.md).

<sup>2</sup>todas as contas de armazenamento são encriptadas utilizando encriptação de serviço de armazenamento (SSE) para dados inativos. Para obter mais informações, consulte [encriptação do serviço de armazenamento do Azure para dados Inativos](../articles/storage/common/storage-service-encryption.md).

<sup>3</sup>camada o arquivo está disponível no nível de um blob individual só, não ao nível da conta de armazenamento. Apenas blobs de blocos e de acréscimo blobs podem ser arquivados. Para obter mais informações, consulte [armazenamento de Blobs do Azure: Acesso frequente, esporádico e de camadas de armazenamento de arquivo](../articles/storage/blobs/storage-blob-storage-tiers.md).

<sup>4</sup>armazenamentoredundância de zona (ZRS) só está disponível para contas de armazenamento standard para fins gerais v2. Para obter mais informações sobre ZRS, veja [armazenamentoredundância de zona (ZRS): Aplicações de armazenamento do Azure de elevada disponibilidade](../articles/storage/common/storage-redundancy-zrs.md). Para obter mais informações sobre outras opções de replicação, consulte [replicação de armazenamento do Azure](../articles/storage/common/storage-redundancy.md).

<sup>5</sup> desempenho premium para fins gerais v2 e contas de fins gerais v1 está disponível para o blob disco e de página.