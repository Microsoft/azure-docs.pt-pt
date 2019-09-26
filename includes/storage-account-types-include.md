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
ms.openlocfilehash: 0e5c1974ecb1b3efb8df602c76700b7db04a88d7
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/26/2019
ms.locfileid: "71310531"
---
O armazenamento do Azure oferece vários tipos de contas de armazenamento. Cada tipo dá suporte a recursos diferentes e tem seu próprio modelo de preços. Considere essas diferenças antes de criar uma conta de armazenamento para determinar o tipo de conta que é melhor para seus aplicativos. Os tipos de contas de armazenamento são:

- **Contas de uso geral v2**: Tipo de conta de armazenamento básico para BLOBs, arquivos, filas e tabelas. Recomendado para a maioria dos cenários usando o armazenamento do Azure.
- **Contas de uso geral v1**: Tipo de conta herdada para BLOBs, arquivos, filas e tabelas. Use contas de uso geral V2 em vez disso, quando possível.
- **Bloquear contas de armazenamento de BLOBs**: Contas de armazenamento somente blob com características de desempenho premium. Recomendado para cenários com altas taxas de transações, usando objetos menores ou exigindo latência de armazenamento consistentemente baixa.
- **Contas de armazenamento de armazenamento**em File: Somente arquivos contas de armazenamento com características de desempenho premium. Recomendado para aplicativos de escala empresarial ou de alto desempenho.
- **Contas de armazenamento de BLOBs**: Contas de armazenamento somente BLOB. Use contas de uso geral V2 em vez disso, quando possível.

A tabela a seguir descreve os tipos de contas de armazenamento e seus recursos:

| Tipo de conta de armazenamento | Serviços suportados                       | Níveis de desempenho com suporte      | Níveis de acesso com suporte         | Opções de replicação               | Modelo de implementação<div role="complementary" aria-labelledby="deployment-model"><sup>1</sup></div> | Encriptação<div role="complementary" aria-labelledby="encryption"><sup>2</sup></div> |
|----------------------|------------------------------------------|-----------------------------|--------------------------------|-----------------------------------|------------------------------|------------------------|
| Uso geral v2   | BLOB, arquivo, fila, tabela e disco       | Standard, Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | Quente, frio e arquivo morto<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS, ZRS, GZRS (visualização), RA-GZRS (versão prévia)<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div> | Resource Manager             | Encriptado              |
| Uso geral v1   | BLOB, arquivo, fila, tabela e disco       | Standard, Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | N/A                            | LRS, GRS, RA-GRS                  | Gerenciador de recursos, clássico    | Encriptado              |
| Armazenamento de blobs de blocos   | BLOB (BLOBs de blocos e blobs de acréscimo) | Premium                       | N/A                            | LRS                               | Resource Manager             | Encriptado              |
| Armazenamento de   | Somente arquivos | Premium                       | N/A                            | LRS                               | Resource Manager             | Encriptado              |
| Armazenamento de blobs         | BLOB (BLOBs de blocos e blobs de acréscimo) | Standard                      | Quente, frio e arquivo morto<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS                  | Resource Manager             | Encriptado              |

<div id="deployment-model"><sup>1</sup> É recomendável usar o modelo de implantação Azure Resource Manager. As contas de armazenamento que usam o modelo de implantação clássico ainda podem ser criadas em alguns locais e contas clássicas existentes continuam com suporte. Para obter mais informações, <a href="https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model">consulte Azure Resource Manager vs. implantação clássica: Entenda os modelos de implantação e o estado de</a>seus recursos.</div>

<div id="encryption"><sup>2</sup> Todas as contas de armazenamento são criptografadas usando Criptografia do Serviço de Armazenamento (SSE) para dados em repouso. Para obter mais informações, consulte <a href="https://docs.microsoft.com/azure/storage/common/storage-service-encryption">criptografia do serviço de armazenamento do Azure para dados em repouso</a>.</div>

<div id="archive"><sup>3</sup> A camada de arquivo morto está disponível apenas no nível de um blob individual, não no nível da conta de armazenamento. Somente blobs de blocos e blobs de acréscimo podem ser arquivados. Para obter mais informações, <a href="https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers">consulte armazenamento de BLOBs do Azure: Camadas de armazenamento quentes, frias e de</a>arquivo morto.</div>

<div id="zone-redundant-storage"><sup>4</sup> O ZRS (armazenamento com redundância de zona) e o GZRS (armazenamento com redundância de zona geográfica) (visualização) estão disponíveis somente para contas de armazenamento padrão de uso geral v2. Para obter mais informações sobre ZRS, <a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs">consulte armazenamento com redundância de zona (ZRS): Aplicativos</a>de armazenamento do Azure altamente disponíveis. Para obter mais informações sobre o GZRS, consulte <a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy-gzrs">armazenamento com redundância de zona geográfica para alta disponibilidade e durabilidade máxima (versão prévia)</a>. Para obter mais informações sobre outras opções de replicação, consulte <a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy">replicação do armazenamento do Azure</a>.</div>

<div id="premium-performance"><sup>5</sup> O desempenho premium para contas de uso geral V2 e V1 de finalidade geral está disponível somente para blob de disco e de página.</div>
