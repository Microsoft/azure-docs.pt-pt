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
ms.openlocfilehash: 16d05203e6c8e845b2354298880d132801e4b952
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72882570"
---
O armazenamento do Azure oferece vários tipos de contas de armazenamento. Cada tipo dá suporte a recursos diferentes e tem seu próprio modelo de preços. Considere essas diferenças antes de criar uma conta de armazenamento para determinar o tipo de conta que é melhor para seus aplicativos. Os tipos de contas de armazenamento são:

- **Contas de uso geral v2**: tipo de conta de armazenamento básico para BLOBs, arquivos, filas e tabelas. Recomendado para a maioria dos cenários usando o armazenamento do Azure.
- **Contas de uso geral v1**: tipo de conta herdada para BLOBs, arquivos, filas e tabelas. Use contas de uso geral V2 em vez disso, quando possível.
- **Contas do BlockBlobStorage**: contas de armazenamento somente blob com características de desempenho premium. Recomendado para cenários com altas taxas de transações, usando objetos menores ou exigindo latência de armazenamento consistentemente baixa.
- **Contas de armazenamento**de arquivo: contas de armazenamento somente de arquivos com características de desempenho premium. Recomendado para aplicativos de escala empresarial ou de alto desempenho.
- **Contas BlobStorage**: contas de armazenamento somente blob herdadas. Use contas de uso geral V2 em vez disso, quando possível.

A tabela a seguir descreve os tipos de contas de armazenamento e seus recursos:

| Tipo de conta de armazenamento | Serviços com suporte                       | Níveis de desempenho com suporte      | Níveis de acesso com suporte         | Opções de replicação               | Modelo de implementação<div role="complementary" aria-labelledby="deployment-model"><sup>1</sup></div> | Encriptação<div role="complementary" aria-labelledby="encryption"><sup>2</sup></div> |
|----------------------|------------------------------------------|-----------------------------|--------------------------------|-----------------------------------|------------------------------|------------------------|
| Uso geral v2   | BLOB, arquivo, fila, tabela e disco       | Standard, Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | Quente, frio e arquivo morto<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS, ZRS, GZRS (visualização), RA-GZRS (versão prévia)<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div> | Resource Manager             | Encriptada              |
| Uso geral v1   | BLOB, arquivo, fila, tabela e disco       | Standard, Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | N/A                            | LRS, GRS, RA-GRS                  | Gerenciador de recursos, clássico    | Encriptada              |
| BlockBlobStorage   | BLOB (BLOBs de blocos e blobs de acréscimo) | Premium                       | N/A                            | LRS                               | Resource Manager             | Encriptada              |
| Armazenamento de   | Somente arquivos | Premium                       | N/A                            | LRS                               | Resource Manager             | Encriptada              |
| BlobStorage         | BLOB (BLOBs de blocos e blobs de acréscimo) | Padrão                      | Quente, frio e arquivo morto<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS                  | Resource Manager             | Encriptada              |

<div id="deployment-model"><sup>1</sup> É recomendável usar o modelo de implantação Azure Resource Manager. As contas de armazenamento que usam o modelo de implantação clássico ainda podem ser criadas em alguns locais e contas clássicas existentes continuam com suporte. Para obter mais informações, consulte <a href="https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model">Azure Resource Manager vs. implantação clássica: entender os modelos de implantação e o estado de seus recursos</a>.</div>

<div id="encryption"><sup>2</sup> Todas as contas de armazenamento são criptografadas usando Criptografia do Serviço de Armazenamento (SSE) para dados em repouso. Para obter mais informações, consulte <a href="https://docs.microsoft.com/azure/storage/common/storage-service-encryption">criptografia do serviço de armazenamento do Azure para dados em repouso</a>.</div>

<div id="archive"><sup>3</sup> A camada de arquivo morto está disponível apenas no nível de um blob individual, não no nível da conta de armazenamento. Somente blobs de blocos e blobs de acréscimo podem ser arquivados. Para obter mais informações, consulte <a href="https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers">armazenamento de BLOBs do Azure: camadas de armazenamento quentes, frias e de arquivo</a>.</div>

<div id="zone-redundant-storage"><sup>4</sup> O ZRS (armazenamento com redundância de zona) e o GZRS (armazenamento com redundância de zona geográfica) (visualização) estão disponíveis somente para contas de armazenamento padrão de uso geral v2. Para obter mais informações sobre ZRS, consulte <a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs">armazenamento com redundância de zona (ZRS): aplicativos de armazenamento do Azure altamente disponíveis</a>. Para obter mais informações sobre o GZRS, consulte <a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy-gzrs">armazenamento com redundância de zona geográfica para alta disponibilidade e durabilidade máxima (versão prévia)</a>. Para obter mais informações sobre outras opções de replicação, consulte <a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy">replicação do armazenamento do Azure</a>.</div>

<div id="premium-performance"><sup>5</sup> O desempenho premium para contas de uso geral V2 e V1 de finalidade geral está disponível somente para blob de disco e de página.</div>
