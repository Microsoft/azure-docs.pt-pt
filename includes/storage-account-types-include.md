---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/17/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 5672334ab42ab474d862ae4c9649b94b58bb6af4
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77157279"
---
O armazenamento do Azure oferece vários tipos de contas de armazenamento. Cada tipo dá suporte a recursos diferentes e tem seu próprio modelo de preços. Considere essas diferenças antes de criar uma conta de armazenamento para determinar o tipo de conta que é melhor para seus aplicativos. Os tipos de contas de armazenamento são:

- **Contas v2 de uso geral**: Tipo de conta de armazenamento básico para bolhas, ficheiros, filas e tabelas. Recomendado para a maioria dos cenários usando o armazenamento do Azure.
- **Contas v1 de uso geral**: Tipo de conta legado para bolhas, ficheiros, filas e tabelas. Use contas de uso geral V2 em vez disso, quando possível.
- **Contas BlockBlobStorage**: Contas de armazenamento com características de desempenho premium para blocos blobs e blocos de apêndice. Recomendado para cenários com altas taxas de transações ou cenários que usam objetos menores ou exigem latência de armazenamento consistentemente baixa.
- **Contas de FileStorage**: Contas de armazenamento apenas com ficheiros com características de desempenho premium. Recomendado para aplicativos de escala empresarial ou de alto desempenho.
- **Contas BlobStorage**: Contas de armazenamento só para O Legado Blob. Use contas de uso geral V2 em vez disso, quando possível.

A tabela a seguir descreve os tipos de contas de armazenamento e seus recursos:

| Tipo de conta de armazenamento | Serviços suportados                       | Níveis de desempenho com suporte      | Níveis de acesso com suporte         | Opções de replicação               | Modelo de implementação<div role="complementary" aria-labelledby="deployment-model"><sup>1</sup></div> | Encriptação<div role="complementary" aria-labelledby="encryption"><sup>2</sup></div> |
|----------------------|------------------------------------------|-----------------------------|--------------------------------|-----------------------------------|------------------------------|------------------------|
| Uso geral v2   | BLOB, arquivo, fila, tabela, disco e Data Lake Gen2<div role="complementary" aria-labelledby="data-lake-gen2"><sup>6</sup></div>      | Standard, Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | Quente, frio e arquivo morto<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS, ZRS, GZRS (visualização), RA-GZRS (versão prévia)<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div> | Resource Manager             | Encriptados              |
| Uso geral v1   | BLOB, arquivo, fila, tabela e disco       | Standard, Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | N/D                            | LRS, GRS, RA-GRS                  | Gerenciador de recursos, clássico    | Encriptados              |
| BlockBlobStorage   | BLOB (BLOBs de blocos e blobs de acréscimo) | Premium                       | N/D                            | LRS, ZRS<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div>                               | Resource Manager             | Encriptados              |
| Armazenamento de   | Somente arquivo | Premium                       | N/D                            | LRS, ZRS<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div>                               | Resource Manager             | Encriptados              |
| BlobStorage         | BLOB (BLOBs de blocos e blobs de acréscimo) | Standard                      | Quente, frio e arquivo morto<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS                  | Resource Manager             | Encriptados              |

<div id="deployment-model"><sup>1</sup> Recomenda-se a utilização do modelo de implementação do Gestor de Recursos Azure. As contas de armazenamento que usam o modelo de implantação clássico ainda podem ser criadas em alguns locais e contas clássicas existentes continuam com suporte. Para mais informações, consulte <a href="https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model">O Gestor de Recursos Azure vs. implantação clássica: Compreenda os modelos de implantação e o estado dos seus recursos</a>.</div>

<div id="encryption"><sup>2</sup> Todas as contas de armazenamento são encriptadas utilizando encriptação do serviço de armazenamento (SSE) para dados em repouso. Para mais informações, consulte a Encriptação do Serviço de <a href="https://docs.microsoft.com/azure/storage/common/storage-service-encryption">Armazenamento Azure para dados em repouso</a>.</div>

<div id="archive"><sup>3</sup> O nível De Arquivo está disponível apenas ao nível de uma bolha individual, não ao nível da conta de armazenamento. Somente blobs de blocos e blobs de acréscimo podem ser arquivados. Para mais informações, consulte o <a href="https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers">armazenamento Azure Blob: Hot, Cool e Archive.</a></div>

<div id="zone-redundant-storage"><sup>4</sup> O armazenamento redundante em zonas (ZRS) e o armazenamento geo-zona-redundante (GZRS/RA-GZRS) (pré-visualização) estão disponíveis apenas para contas v2, BlockBlobStorage e FileStorage padrão em certas regiões. Para obter mais informações sobre as opções de redundância do Armazenamento Azure, consulte o despedimento do <a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy">Azure Storage.</a></div>

<div id="premium-performance"><sup>5</sup> O desempenho premium para contas v2 de uso geral e v1 de uso geral está disponível apenas para o disco e para a página blob. O desempenho premium para BLOBs de bloco ou de acréscimo só está disponível em contas BlockBlobStorage. O desempenho premium para arquivos só está disponível em contas de armazenamento de arquivo.</div>

<div id="data-lake-gen2"><sup>6</sup> Azure Data Lake Storage Gen2 é um conjunto de capacidades dedicadas à análise de big data, construídas sobre o armazenamento azure blob. Só há suporte para Data Lake Storage Gen2 em contas de armazenamento v2 de uso geral com namespace hierárquico habilitado. Para obter mais informações sobre data Lake Storage Gen2, consulte Introdução ao Armazenamento de <a href="https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction">Lagos De dados Azure Gen2</a>.</div>
