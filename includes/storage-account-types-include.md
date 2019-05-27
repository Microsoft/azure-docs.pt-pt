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
ms.openlocfilehash: d4f57eca89cbb68d61546c6d5ce5bcd04f9256e7
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66114942"
---
O armazenamento do Azure oferece vários tipos de contas de armazenamento. Cada tipo oferece suporte a recursos diferentes e tem o seu próprio modelo de preços. Considere estas diferenças antes de criar uma conta de armazenamento para determinar o tipo de conta que é melhor para as suas aplicações. Os tipos de contas de armazenamento são:

- **Contas para fins gerais v2**: Tipo de conta de armazenamento básico para blobs, ficheiros, filas e tabelas. Recomendada para a maioria dos cenários que utilizam o armazenamento do Azure.
- **Contas de fins gerais v1**: Tipo de conta legada para blobs, ficheiros, filas e tabelas. Utilize contas de fins gerais v2 em vez disso, sempre que possível.
- **Bloquear contas do blob storage**: Contas de armazenamento apenas de Blobs com características de desempenho premium. Recomendada para cenários com taxas de transações elevada, usando objetos menores ou sem ter de latência de armazenamento consistentemente baixo.
- **Contas de armazenamento (pré-visualização) de FileStorage**: Contas de armazenamento apenas de arquivos com características de desempenho premium. Recomendado para o enterprise ou aplicações de alto desempenho.
- **Contas do blob storage**: Contas de armazenamento apenas de Blobs. Utilize contas de fins gerais v2 em vez disso, sempre que possível.

A tabela seguinte descreve os tipos de contas de armazenamento e as respetivas funcionalidades:

| Tipo de conta de armazenamento | Serviços suportados                       | Escalões de desempenho suportados      | Suporte de acesso         | Opções de replicação               | Modelo de implementação<sup>1</sup> | Encriptação<sup>2</sup> |
|----------------------|------------------------------------------|-----------------------------|--------------------------------|-----------------------------------|------------------------------|------------------------|
| Para fins gerais V2   | BLOB, ficheiro, fila, tabela e disco       | Standard, Premium<sup>5</sup> | Frequente, esporádico, arquivo<sup>3</sup> | LRS, ZRS<sup>4</sup>, GRS, RA-GRS | Resource Manager             | Encriptado              |
| Para fins gerais V1   | BLOB, ficheiro, fila, tabela e disco       | Standard, Premium<sup>5</sup> | N/A                            | LRS, GRS, RA-GRS                  | Gestor de recursos, clássico    | Encriptado              |
| Armazenamento de BLOBs de blocos   | BLOB (blobs de blocos e de acréscimo apenas) | Premium                       | N/A                            | LRS                               | Resource Manager             | Encriptado              |
| FileStorage (pré-visualização)   | Apenas os ficheiros | Premium                       | N/A                            | LRS                               | Resource Manager             | Encriptado              |
| Armazenamento de blobs         | BLOB (blobs de blocos e de acréscimo apenas) | Standard                      | Frequente, esporádico, arquivo<sup>3</sup> | LRS, GRS, RA-GRS                  | Resource Manager             | Encriptado              |

<sup>1</sup>é recomendado utilizar o modelo de implementação Azure Resource Manager. Contas de armazenamento com o modelo de implementação clássica ainda podem ser criadas em alguns locais e contas clássicas existentes continuarão a ter suporte. Para obter mais informações, consulte [vs. de implementação clássica do Azure Resource Manager: Compreender os modelos de implementação e o estado dos seus recursos](../articles/azure-resource-manager/resource-manager-deployment-model.md).

<sup>2</sup>todas as contas de armazenamento são encriptadas utilizando encriptação de serviço de armazenamento (SSE) para dados inativos. Para obter mais informações, consulte [encriptação do serviço de armazenamento do Azure para dados Inativos](../articles/storage/common/storage-service-encryption.md).

<sup>3</sup>camada o arquivo está disponível no nível de um blob individual só, não ao nível da conta de armazenamento. Apenas blobs de blocos e de acréscimo blobs podem ser arquivados. Para obter mais informações, consulte [armazenamento de Blobs do Azure: Acesso frequente, esporádico e de camadas de armazenamento de arquivo](../articles/storage/blobs/storage-blob-storage-tiers.md).

<sup>4</sup>armazenamentoredundância de zona (ZRS) só está disponível para contas de armazenamento standard para fins gerais v2. Para obter mais informações sobre ZRS, veja [armazenamentoredundância de zona (ZRS): Aplicações de armazenamento do Azure de elevada disponibilidade](../articles/storage/common/storage-redundancy-zrs.md). Para obter mais informações sobre outras opções de replicação, consulte [replicação de armazenamento do Azure](../articles/storage/common/storage-redundancy.md).

<sup>5</sup> desempenho premium para fins gerais v2 e contas de fins gerais v1 está disponível para o blob disco e de página.