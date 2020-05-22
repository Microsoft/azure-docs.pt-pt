---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 05/20/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 72afa8ebea4cb9bf07cb3ce56583cbdf1849be9e
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83778231"
---
A tabela seguinte descreve limites de predefinição para contas de armazenamento de v1, v2, armazenamento blob e bloco blob. O limite de *ingresso* refere-se a todos os dados enviados para uma conta de armazenamento. O limite de *saída* refere-se a todos os dados recebidos de uma conta de armazenamento.

| Recurso | Limite |
| --- | --- |
| Número de contas de armazenamento por região por subscrição, incluindo contas de armazenamento padrão e premium.| 250 |
| Capacidade máxima da conta de armazenamento | 5 PiB <sup>1</sup>|
| Número máximo de contentores blob, blobs, ações de ficheiros, mesas, filas, entidades ou mensagens por conta de armazenamento | Sem limite |
| Taxa máxima de pedido<sup>1</sup> por conta de armazenamento | 20.000 pedidos por segundo |
| Ingresso máximo<sup>1</sup> por conta de armazenamento (EUA, regiões europeias) | 25 Gbps |
| Ingresso máximo<sup>1</sup> por conta de armazenamento (regiões que não os EUA e a Europa) | 5 Gbps se RA-GRS/GRS estiver ativado, 10 Gbps para LRS/ZRS<sup>2</sup> |
| Saída máxima para contas de armazenamento v2 e blob (todas as regiões) | 50 Gbps |
| Maior esgress para contas de armazenamento v1 de uso geral (regiões dos EUA) | 20 Gbps se RA-GRS/GRS estiver ativado, 30 Gbps para LRS/ZRS<sup>2</sup> |
| Maiores saídas para contas de armazenamento v1 de uso geral (regiões não americanas) | 10 Gbps se RA-GRS/GRS estiver ativado, 15 Gbps para LRS/ZRS<sup>2</sup> |
| Número máximo de regras de rede virtual por conta de armazenamento | 200 |
| Número máximo de regras de endereço ip por conta de armazenamento | 200 |

<sup>1</sup> As contas padrão de armazenamento Azure suportam limites de capacidade mais elevados e limites mais elevados para a entrada por pedido. Para solicitar um aumento dos limites de conta, contacte o [Suporte Azure](https://azure.microsoft.com/support/faq/).

<sup>2</sup> Se a sua conta de armazenamento tiver acesso de leitura ativado com armazenamento geo-redundante (RA-GRS) ou armazenamento geo-zona redundante (RA-GZRS), então os alvos de saída para a localização secundária são idênticos aos da localização primária. As opções de replicação do [Armazenamento Azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy) incluem:

[!INCLUDE [azure-storage-redundancy](azure-storage-redundancy.md)]

<sup>3</sup> [Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-introduction.md) é um conjunto de capacidades dedicadas à análise de big data, construídas sobre o armazenamento azure Blob.

> [!NOTE]
> A Microsoft recomenda que utilize uma conta de armazenamento v2 para a maioria dos cenários. Pode facilmente atualizar uma conta de armazenamento de uso geral v1 ou uma Conta de armazenamento Azure Blob para uma conta V2 de uso geral sem tempo de inatividade e sem necessidade de copiar dados. Para mais informações, consulte [o Upgrade para uma conta de armazenamento v2 de uso geral](../articles/storage/common/storage-account-upgrade.md).

Se as necessidades da sua aplicação excederem os alvos de escalabilidade de uma única conta de armazenamento, pode construir a sua aplicação para utilizar várias contas de armazenamento. Em seguida, pode dividir os seus objetos de dados através dessas contas de armazenamento. Para obter informações sobre os preços de volume, consulte o preço do [Armazenamento De Azure](https://azure.microsoft.com/pricing/details/storage/).

Todas as contas de armazenamento funcionam numa topologia de rede plana, independentemente do momento em que foram criadas. Para obter mais informações sobre a arquitetura de rede plana azure storage e sobre escalabilidade, consulte [o Armazenamento Microsoft Azure: Um serviço de armazenamento em nuvem altamente disponível com forte consistência](https://docs.microsoft.com/archive/blogs/hanuk/windows-azures-flat-network-storage-to-enable-higher-scalability-targets). 
