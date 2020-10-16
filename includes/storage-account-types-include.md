---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/05/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 726fc9cc0e4eb5a8f9bed5cca8dd4d3df588ac7c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "79371562"
---
O Azure Storage oferece vários tipos de contas de armazenamento. Cada tipo suporta diferentes funcionalidades e tem o seu próprio modelo de preços. Considere estas diferenças antes de criar uma conta de armazenamento para determinar o tipo de conta que é melhor para as suas aplicações. Os tipos de contas de armazenamento são:

- **Contas v2 para fins gerais**: Tipo de conta de armazenamento básico para bolhas, ficheiros, filas e tabelas. Recomendado para a maioria dos cenários usando O Armazenamento Azure.
- **Contas v1 para fins gerais**: Tipo de conta legado para bolhas, ficheiros, filas e tabelas. Utilize, em vez disso, as contas v2 de uso geral, sempre que possível.
- **Contas BlockBlobStorage**: Contas de armazenamento com características de desempenho premium para bolhas de blocos e bolhas de apêndice. Recomendado para cenários com altas taxas de transações, ou cenários que usem objetos menores ou exijam uma latência de armazenamento consistentemente baixa.
- **Contas de arquivamento de ficheiros**: Contas de armazenamento apenas de ficheiros com características de desempenho premium. Recomendado para aplicações de escala de empresa ou de alto desempenho.
- **BlobStorage accounts**: Legacy Blob-only storage accounts. Utilize, em vez disso, as contas v2 de uso geral, sempre que possível.

A tabela a seguir descreve os tipos de contas de armazenamento e as suas capacidades:

| Tipo de conta de armazenamento | Serviços suportados                       | Níveis de desempenho suportados      | Níveis de acesso suportados         | Opções de replicação               | Modelo de implementação<div role="complementary" aria-labelledby="deployment-model"><sup>1</sup></div> | Encriptação<div role="complementary" aria-labelledby="encryption"><sup>2</sup></div> |
|----------------------|------------------------------------------|-----------------------------|--------------------------------|-----------------------------------|------------------------------|------------------------|
| V2 de uso geral   | Blob, Arquivo, Fila, Mesa, Disco e Data Lake Gen2<div role="complementary" aria-labelledby="data-lake-gen2"><sup>6</sup></div>      | Standard, Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | Quente, Fresco, Arquivo<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS, ZRS, GZRS (pré-visualização), RA-GZRS (pré-visualização)<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div> | Resource Manager             | Encriptados              |
| V1 de uso geral   | Blob, Arquivo, Fila, Tabela e Disco       | Standard, Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | N/D                            | LRS, GRS, RA-GRS                  | Gestor de Recursos, Clássico    | Encriptados              |
| BlockBlobStorage   | Blob (blobs de bloco e apenas bolhas de apêndice) | Premium                       | N/D                            | LRS, ZRS<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div>                               | Resource Manager             | Encriptados              |
| Arquitorage de arquivos   | Apenas arquivar | Premium                       | N/D                            | LRS, ZRS<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div>                               | Resource Manager             | Encriptados              |
| BlobStorage         | Blob (blobs de bloco e apenas bolhas de apêndice) | Standard                      | Quente, Fresco, Arquivo<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS                  | Resource Manager             | Encriptados              |

<div id="deployment-model"><sup>1</sup> Recomenda-se a utilização do modelo de implementação do Gestor de Recursos Azure. As contas de armazenamento que utilizam o modelo de implementação clássico ainda podem ser criadas em alguns locais, e as contas clássicas existentes continuam a ser suportadas. Para obter mais informações, consulte <a href="https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model">Azure Resource Manager vs. design clássico: Compreenda os modelos de implantação e o estado dos seus recursos.</a></div><br/>

<div id="encryption"><sup>2</sup> Todas as contas de armazenamento são encriptadas usando encriptação do serviço de armazenamento (SSE) para dados em repouso. Para obter mais informações, consulte <a href="https://docs.microsoft.com/azure/storage/common/storage-service-encryption">a Encriptação do Serviço de Armazenamento Azure para obter dados em repouso.</a></div><br/>

<div id="archive"><sup>3</sup> Arquivar o armazenamento e o nível de nível de bolhas apenas suportam bolhas de bloco. O nível Archive está disponível apenas ao nível de uma bolha individual, não ao nível da conta de armazenamento. Para obter mais informações, consulte <a href="https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers">o armazenamento Azure Blob: Níveis de armazenamento quente, fresco e archive</a>.</div><br/>

<div id="zone-redundant-storage"><sup>4</sup> O armazenamento redundante de zonas (ZRS) e o armazenamento redundante em zonas geo-zonas (GZRS/RA-GZRS) (pré-visualização) só estão disponíveis para as contas V2, BlockBlobStorage e FileStorage em determinadas regiões. Para obter mais informações sobre as opções de redundância do <a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy">Azure Storage, consulte a redundância do Azure Storage</a>.</div><br/>

<div id="premium-performance"><sup>5</sup> O desempenho premium para as contas v2 e v1 de uso geral está disponível apenas para o disco e para a página blob. O desempenho premium para blobs de bloco ou apêndice só está disponível nas contas BlockBlobStorage. O desempenho premium dos ficheiros só está disponível nas contas FileStorage.</div><br/>

<div id="data-lake-gen2"><sup>6</sup> Azure Data Lake Storage Gen2 é um conjunto de capacidades dedicadas à análise de big data, construídas no armazenamento de Azure Blob. Data Lake Storage Gen2 só é suportado em contas de armazenamento V2 de uso geral com espaço hierárquico habilitado. Para obter mais informações sobre data lake storage gen2, consulte <a href="https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction">Introdução ao Azure Data Lake Storage Gen2</a>.</div>
