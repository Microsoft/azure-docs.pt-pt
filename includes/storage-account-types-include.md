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
ms.openlocfilehash: 0c0f1f4dfd873c8c9a18d300b249ace0295e450e
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74174030"
---
O armazenamento do Azure oferece vários tipos de contas de armazenamento. Cada tipo dá suporte a recursos diferentes e tem seu próprio modelo de preços. Considere essas diferenças antes de criar uma conta de armazenamento para determinar o tipo de conta que é melhor para seus aplicativos. Os tipos de contas de armazenamento são:

- **Contas de uso geral v2**: tipo de conta de armazenamento básico para BLOBs, arquivos, filas e tabelas. Recomendado para a maioria dos cenários usando o armazenamento do Azure.
- **Contas de uso geral v1**: tipo de conta herdada para BLOBs, arquivos, filas e tabelas. Use contas de uso geral V2 em vez disso, quando possível.
- **Contas do BlockBlobStorage**: contas de armazenamento somente blob com características de desempenho premium. Recomendado para cenários com altas taxas de transações, usando objetos menores ou exigindo latência de armazenamento consistentemente baixa.
- **Contas de armazenamento**de arquivo: contas de armazenamento somente de arquivos com características de desempenho premium. Recomendado para aplicativos de escala empresarial ou de alto desempenho.
- **Contas BlobStorage**: contas de armazenamento somente blob herdadas. Use contas de uso geral V2 em vez disso, quando possível.

A tabela a seguir descreve os tipos de contas de armazenamento e seus recursos:

| Tipo de conta de armazenamento | Serviços suportados                       | Níveis de desempenho com suporte      | Níveis de acesso com suporte         | Opções de replicação               | Modelo de implementação<div role="complementary" aria-labelledby="deployment-model"><sup>1</sup></div> | Encriptação<div role="complementary" aria-labelledby="encryption"><sup>2</sup></div> |
|----------------------|------------------------------------------|-----------------------------|--------------------------------|-----------------------------------|------------------------------|------------------------|
| Uso geral v2   | BLOB, arquivo, fila, tabela, disco e Data Lake Gen2<div role="complementary" aria-labelledby="data-lake-gen2"><sup>6</sup></div>      | Standard, Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | Quente, frio e arquivo morto<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS, ZRS, GZRS (visualização), RA-GZRS (versão prévia)<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div> | Resource Manager             | Encriptados              |
| Uso geral v1   | BLOB, arquivo, fila, tabela e disco       | Standard, Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | N/D                            | LRS, GRS, RA-GRS                  | Gerenciador de recursos, clássico    | Encriptados              |
| BlockBlobStorage   | BLOB (BLOBs de blocos e blobs de acréscimo) | Premium                       | N/D                            | LRS, ZRS<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div>                               | Resource Manager             | Encriptados              |
| Armazenamento de   | Somente arquivo | Premium                       | N/D                            | LRS, ZRS<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div>                               | Resource Manager             | Encriptados              |
| BlobStorage         | BLOB (BLOBs de blocos e blobs de acréscimo) | Standard                      | Quente, frio e arquivo morto<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS                  | Resource Manager             | Encriptados              |

<div id="deployment-model"><sup>1</sup> É recomendável usar o modelo de implantação Azure Resource Manager. As contas de armazenamento que usam o modelo de implantação clássico ainda podem ser criadas em alguns locais e contas clássicas existentes continuam com suporte. Para obter mais informações, consulte <a href="https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model">Azure Resource Manager vs. implantação clássica: entender os modelos de implantação e o estado de seus recursos</a>.</div>

<div id="encryption"><sup>2</sup> Todas as contas de armazenamento são criptografadas usando Criptografia do Serviço de Armazenamento (SSE) para dados em repouso. Para obter mais informações, consulte <a href="https://docs.microsoft.com/azure/storage/common/storage-service-encryption">criptografia do serviço de armazenamento do Azure para dados em repouso</a>.</div>

<div id="archive"><sup>3</sup> A camada de arquivo morto está disponível apenas no nível de um blob individual, não no nível da conta de armazenamento. Somente blobs de blocos e blobs de acréscimo podem ser arquivados. Para obter mais informações, consulte <a href="https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers">armazenamento de BLOBs do Azure: camadas de armazenamento quentes, frias e de arquivo</a>.</div>

<div id="zone-redundant-storage"><sup>4</sup> O ZRS (armazenamento com redundância de zona) e o armazenamento com redundância de zona geográfica (GZRS/RA-GZRS) (versão prévia) estão disponíveis somente para contas de armazenamento de, BlockBlobStorage e de uso geral padrão em determinadas regiões. Para obter mais informações sobre ZRS, consulte <a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs">armazenamento com redundância de zona (ZRS): aplicativos de armazenamento do Azure altamente disponíveis</a>. Para obter mais informações sobre GZRS/RA-GZRS, consulte <a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy-gzrs">armazenamento com redundância de zona geográfica para alta disponibilidade e durabilidade máxima (versão prévia)</a>. Para obter mais informações sobre outras opções de replicação, consulte <a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy">replicação do armazenamento do Azure</a>.</div>

<div id="premium-performance"><sup>5</sup> O desempenho premium para contas de uso geral V2 e V1 de finalidade geral está disponível somente para blob de disco e de página. O desempenho premium para BLOBs de bloco ou de acréscimo só está disponível em contas BlockBlobStorage. O desempenho premium para arquivos só está disponível em contas de armazenamento de arquivo.</div>

<div id="data-lake-gen2"><sup>6</sup> Azure Data Lake Storage Gen2 é um conjunto de recursos dedicados à análise de Big Data, criado no armazenamento de BLOBs do Azure. Só há suporte para Data Lake Storage Gen2 em contas de armazenamento v2 de uso geral com namespace hierárquico habilitado. Para obter mais informações sobre Data Lake Storage Gen2, consulte <a href="https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction">introdução ao Azure data Lake Storage Gen2</a>.</div>
