---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/05/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 726fc9cc0e4eb5a8f9bed5cca8dd4d3df588ac7c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79371562"
---
O Azure Storage oferece vários tipos de contas de armazenamento. Cada tipo suporta diferentes funcionalidades e tem o seu próprio modelo de preços. Considere estas diferenças antes de criar uma conta de armazenamento para determinar o tipo de conta que é melhor para as suas aplicações. Os tipos de contas de armazenamento são:

- **Contas v2 de uso geral**: Tipo de conta de armazenamento básico para bolhas, ficheiros, filas e tabelas. Recomendado para a maioria dos cenários usando o Armazenamento Azure.
- **Contas v1 de uso geral**: Tipo de conta legado para bolhas, ficheiros, filas e tabelas. Utilize contas v2 de uso geral, quando possível.
- **Contas BlockBlobStorage**: Contas de armazenamento com características de desempenho premium para blocos blobs e blocos de apêndice. Recomendado para cenários com altas taxas de transações, ou cenários que utilizem objetos menores ou requerem latência de armazenamento consistentemente baixa.
- **Contas de FileStorage**: Contas de armazenamento apenas com ficheiros com características de desempenho premium. Recomendado para aplicações de escala empresarial ou de alta performance.
- **Contas BlobStorage**: Contas de armazenamento só para O Legado Blob. Utilize contas v2 de uso geral, quando possível.

O quadro seguinte descreve os tipos de contas de armazenamento e as suas capacidades:

| Tipo de conta de armazenamento | Serviços suportados                       | Níveis de desempenho suportados      | Níveis de acesso suportados         | Opções de replicação               | Modelo de implementação<div role="complementary" aria-labelledby="deployment-model"><sup>1</sup></div> | Encriptação<div role="complementary" aria-labelledby="encryption"><sup>2</sup></div> |
|----------------------|------------------------------------------|-----------------------------|--------------------------------|-----------------------------------|------------------------------|------------------------|
| V2 de uso geral   | Blob, File, Fila, Mesa, Disco e Data Lake Gen2<div role="complementary" aria-labelledby="data-lake-gen2"><sup>6</sup></div>      | Standard, Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | Hot, Cool, Archive<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS, ZRS, GZRs (pré-visualização), RA-GZRs (pré-visualização)<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div> | Resource Manager             | Encriptados              |
| V1 de uso geral   | Blob, File, Fila, Mesa e Disco       | Standard, Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | N/D                            | LRS, GRS, RA-GRS                  | Gestor de Recursos, Clássico    | Encriptados              |
| BlockBlobStorage   | Blob (bloco sinuoso e apenas bolhas de apêndice) | Premium                       | N/D                            | LRS, ZRS<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div>                               | Resource Manager             | Encriptados              |
| FileStorage   | Arquivar apenas | Premium                       | N/D                            | LRS, ZRS<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div>                               | Resource Manager             | Encriptados              |
| BlobStorage         | Blob (bloco sinuoso e apenas bolhas de apêndice) | Standard                      | Hot, Cool, Archive<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS                  | Resource Manager             | Encriptados              |

<div id="deployment-model"><sup>1</sup> Recomenda-se a utilização do modelo de implementação do Gestor de Recursos Azure. As contas de armazenamento que utilizam o modelo de implementação clássico ainda podem ser criadas em alguns locais, e as contas clássicas existentes continuam a ser suportadas. Para mais informações, consulte <a href="https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model">O Gestor de Recursos Azure vs. implantação clássica: Compreenda os modelos de implantação e o estado dos seus recursos</a>.</div><br/>

<div id="encryption"><sup>2</sup> Todas as contas de armazenamento são encriptadas utilizando encriptação do serviço de armazenamento (SSE) para dados em repouso. Para mais informações, consulte a Encriptação do Serviço de <a href="https://docs.microsoft.com/azure/storage/common/storage-service-encryption">Armazenamento Azure para dados em repouso</a>.</div><br/>

<div id="archive"><sup>3</sup> Armazenamento de arquivo e tiering de nível de bolha apenas bloqueia as bolhas de bloco. O nível De Arquivo está disponível apenas ao nível de uma bolha individual, e não ao nível da conta de armazenamento. Para mais informações, consulte o <a href="https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers">armazenamento Azure Blob: Hot, Cool e Archive.</a></div><br/>

<div id="zone-redundant-storage"><sup>4</sup> O armazenamento redundante em zonas (ZRS) e o armazenamento geo-zona-redundante (GZRS/RA-GZRS) (pré-visualização) estão disponíveis apenas para contas v2, BlockBlobStorage e FileStorage padrão em certas regiões. Para obter mais informações sobre as opções de redundância do Armazenamento Azure, consulte o despedimento do <a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy">Azure Storage.</a></div><br/>

<div id="premium-performance"><sup>5</sup> O desempenho premium para contas v2 de uso geral e v1 de uso geral está disponível apenas para o disco e para a página blob. O desempenho premium para bolhas de bloco ou apêndice só está disponível nas contas BlockBlobStorage. O desempenho premium dos ficheiros só está disponível nas contas fileStorage.</div><br/>

<div id="data-lake-gen2"><sup>6</sup> Azure Data Lake Storage Gen2 é um conjunto de capacidades dedicadas à análise de big data, construídas sobre o armazenamento azure blob. Data Lake Storage Gen2 é suportado apenas em contas de armazenamento V2 de uso geral com espaço de nome hierárquico habilitado. Para obter mais informações sobre data Lake Storage Gen2, consulte Introdução ao Armazenamento de <a href="https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction">Lagos De dados Azure Gen2</a>.</div>
