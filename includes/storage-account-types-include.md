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
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/14/2020
ms.locfileid: "79371562"
---
O Armazenamento do Azure oferece vários tipos de contas de armazenamento. Cada tipo suporta diferentes funcionalidades e tem o seu próprio modelo de preços. Considere estas diferenças antes de criar uma conta de armazenamento para determinar o tipo de conta mais adequada às suas aplicações. Os tipos de contas de armazenamento são:

- **Contas de armazenamento para fins gerais v2**: tipo de conta de armazenamento básica para blobs, ficheiros, filas e tabelas. Recomendado para a maioria dos cenários que utilizam o Armazenamento do Azure.
- **Contas de armazenamento para fins gerais v1**: tipo de conta legada para blobs, ficheiros, filas e tabelas. Utilize antes contas de armazenamento para fins gerais v2 sempre que possível.
- **Contas BlockBlobStorage**: contas de armazenamento com características de desempenho premium para blobs de blocos e blobs de acréscimo. Recomendado para cenários com elevadas taxas de transações ou cenários que utilizem objetos menores ou requerem latência de armazenamento consistentemente baixa.
- **Contas FileStorage**: contas de armazenamento apenas para ficheiros com características de desempenho premium. Recomendado para aplicações de dimensionamento empresariais ou de elevado desempenho.
- **Contas BlobStorage**: contas de armazenamento legadas apenas para Blobs. Utilize antes contas de armazenamento para fins gerais v2 sempre que possível.

A tabela seguinte descreve os tipos de contas de armazenamento e as respetivas capacidades:

| Tipo de conta de armazenamento | Serviços suportados                       | Escalões de desempenho suportados      | Escalões de acesso suportados         | Opções de replicação               | Modelo de implementação<div role="complementary" aria-labelledby="deployment-model"><sup>1</sup></div> | Encriptação<div role="complementary" aria-labelledby="encryption"><sup>2</sup></div> |
|----------------------|------------------------------------------|-----------------------------|--------------------------------|-----------------------------------|------------------------------|------------------------|
| Fins gerais v2   | Blob, Ficheiro, Fila, Tabela, Disco e Data Lake Gen2<div role="complementary" aria-labelledby="data-lake-gen2"><sup>6</sup></div>      | Standard, Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | Frequente, Esporádico, Arquivo<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS, ZRS, GZRS (pré-visualização), RA-GZRS (pré-visualização)<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div> | Resource Manager             | Encriptados              |
| Fins gerais v1   | Blob, Ficheiro, Fila, Tabela e Disco       | Standard, Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | N/D                            | LRS, GRS, RA-GRS                  | Resource Manager, Clássico    | Encriptados              |
| BlockBlobStorage   | Blob (apenas blobs de blocos e blobs de acréscimo) | Premium                       | N/D                            | LRS, ZRS<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div>                               | Resource Manager             | Encriptados              |
| FileStorage   | Apenas ficheiros | Premium                       | N/D                            | LRS, ZRS<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div>                               | Resource Manager             | Encriptados              |
| BlobStorage         | Blob (apenas blobs de blocos e blobs de acréscimo) | Standard                      | Frequente, Esporádico, Arquivo<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS                  | Resource Manager             | Encriptados              |

<div id="deployment-model"><sup>1</sup>É recomendada a utilização do modelo de implementação Azure Resource Manager. As contas de armazenamento que utilizam o modelo de implementação clássico ainda podem ser criadas em algumas localizações e as contas clássicas existentes continuam a ser suportadas. Para obter mais informações, veja <a href="https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model">Azure Resource Manager vs. classic deployment: Understand deployment models and the state of your resources</a> (Azure Resource Manager vs. implementação clássica: compreender os modelos de implementação e o estado dos seus recursos).</div><br/>

<div id="encryption"><sup>2</sup>Todas as contas de armazenamento são encriptadas com a Encriptação do Serviço de Armazenamento (SSE) para dados inativos. Para obter mais informações, veja <a href="https://docs.microsoft.com/azure/storage/common/storage-service-encryption">Azure Storage Service Encryption for Data at Rest</a> (Encriptação do Serviço de Armazenamento do Azure para Dados Inativos).</div><br/>

<div id="archive"><sup>3</sup> O armazenamento de arquivo e a criação de camadas ao nível de blobs suportam apenas blobs de blocos. A camada de Arquivo só está disponível ao nível de um blob individual e não ao nível da conta de armazenamento. Para obter mais informações, veja <a href="https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers">Azure Blob storage: Hot, Cool, and Archive storage tiers</a> (Armazenamento de blobs do Azure: camadas de acesso frequente, esporádico e de arquivo).</div><br/>

<div id="zone-redundant-storage"><sup>4</sup>O armazenamento com redundância entre zonas (ZRS) e o armazenamento com georredundância entre zonas (GZRS/RA-GZRS) (pré-visualização) estão disponíveis apenas para contas para fins gerais v2, BlockBlobStorage e FileStorage em certas regiões. Para obter mais informações sobre as opções de redundância do Armazenamento do Azure, veja <a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy">Azure Storage redundancy</a> (Redundância do Armazenamento do Azure).</div><br/>

<div id="premium-performance"><sup>5</sup>O desempenho premium para contas para fins gerais v1 e v2 está disponível apenas para blobs de disco e de páginas. O desempenho premium para blobs de blocos ou de acréscimo só está disponível nas contas BlockBlobStorage. O desempenho premium para ficheiros só está disponível nas contas FileStorage.</div><br/>

<div id="data-lake-gen2"><sup>6</sup>O Azure Data Lake Storage Gen2 é um conjunto de capacidades dedicadas à análise de macrodados, criado com base no armazenamento de Blobs do Azure. O Data Lake Storage Gen2 é suportado apenas em contas de armazenamento para fins gerais v2 com o espaço de nomes hierárquico ativado. Para obter mais informações sobre o Data Lake Storage Gen2, veja <a href="https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction">Introduction to Azure Data Lake Storage Gen2</a> (Introdução ao Azure Data Lake Storage Gen2).</div>
