---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/13/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: b5de45086b324006a3dafdef5c80689923650759
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/14/2020
ms.locfileid: "79382132"
---
A tabela seguinte descreve os limites predefinidos para as contas de armazenamento para fins gerais v1, v2, armazenamento de Blobs, armazenamento de blobs de blocos e Data Lake Storage Gen2 do Azure. O limite de *entrada* refere-se a todos os dados enviados para uma conta de armazenamento. O limite de *saída* refere-se a todos os dados recebidos de uma conta de armazenamento.

| Recurso | Limite predefinido |
| --- | --- |
| O número de contas de armazenamento por região e subscrição, incluindo contas de armazenamento standard, premium e Data Lake Storage Gen2.<sup>3</sup> | 250 |
| Capacidade máxima da conta de armazenamento | 5 PiB <sup>1</sup>|
| Número máximo de contentores de blobs, blobs, partilhas de ficheiros, tabelas, filas, entidades ou mensagens por conta de armazenamento | Sem limite |
| Taxa máxima de pedidos<sup>1</sup> por conta de armazenamento | 20 000 pedidos por segundo |
| Entrada máxima<sup>1</sup> por conta de armazenamento (regiões dos EUA e Europa) | 25 Gbps |
| Entrada máxima<sup>1</sup> por conta de armazenamento (regiões fora dos EUA e da Europa) | 5 Gbps se estiver ativado RA-GRS/GRS, 10 Gbps para LRS/ZRS<sup>2</sup> |
| Saída máxima para contas de armazenamento para fins gerais v2 e de Blobs (todas as regiões) | 50 Gbps |
| Saída máxima para contas de armazenamento para fins gerais v1 (regiões dos EUA) | 20 Gbps se RA-GRS/GRS estiver ativado, 30 Gbps para LRS/ZRS<sup>2</sup> |
| Saída máxima para contas de armazenamento para fins gerais v1 (regiões fora dos EUA) | 10 Gbps se RA-GRS/GRS estiver ativado, 15 Gbps para LRS/ZRS<sup>2</sup> |
| Número máximo de regras de rede virtual por conta de armazenamento | 200 |
| Número máximo de regras de endereço IP por conta de armazenamento | 200 |

<sup>1</sup> As contas standard de Armazenamento do Azure suportam limites de capacidade superiores e limites mais elevados para entrada por pedido. Para solicitar um aumento dos limites de conta, contacte o [Suporte do Azure](https://azure.microsoft.com/support/faq/).

<sup>2</sup> Se a sua conta de armazenamento tiver o acesso de leitura ativado com armazenamento georredundante (RA-GRS) ou armazenamento com redundância entre zonas (RA-GZRS), os destinos de saída para a localização secundária são idênticos aos da localização primária. As opções de [replicação de Armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy) incluem:

[!INCLUDE [azure-storage-redundancy](azure-storage-redundancy.md)]

<sup>3</sup> O [Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-introduction.md) é um conjunto de capacidades dedicadas à análise de macrodados, criado com base no armazenamento de Blobs do Azure. As limitações de Armazenamento do Azure e do armazenamento de blobs aplicam-se ao Data Lake Storage Gen2.

> [!NOTE]
> A Microsoft recomenda que utilize uma conta de armazenamento para fins gerais v2 para a maioria dos cenários. Pode atualizar facilmente uma conta de armazenamento para fins gerais v1 ou de Blobs do Azure para uma conta para fins gerais v2 sem tempo de inatividade e sem necessidade de copiar dados. Para obter mais informações, veja [Upgrade to a general-purpose v2 storage account](../articles/storage/common/storage-account-upgrade.md) (Atualizar para uma conta de armazenamento para fins gerais v2).

Se as necessidades da sua aplicação excederem os alvos de escalabilidade de uma única conta de armazenamento, pode criar a sua aplicação para utilizar várias contas de armazenamento. Em seguida, pode dividir os objetos de dados por essas contas de armazenamento. Para obter informações sobre os preços de volumes, veja [Preços do Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/).

Todas as contas de armazenamento funcionam numa topologia de rede simples, independentemente do momento em que foram criadas. Para obter mais informações sobre a arquitetura de rede simples do Armazenamento do Azure e escalabilidade, veja [Microsoft Azure Storage: A Highly Available Cloud Storage Service with Strong Consistency](https://docs.microsoft.com/archive/blogs/hanuk/windows-azures-flat-network-storage-to-enable-higher-scalability-targets) (Armazenamento do Microsoft Azure: Serviço de Armazenamento na Cloud de Elevada Disponibilidade com Forte Consistência). 
