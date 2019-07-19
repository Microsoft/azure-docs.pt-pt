---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/11/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 54050116f9894f9a7903dad53caf6b01228313b7
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326976"
---
A tabela a seguir descreve os limites padrão para contas de armazenamento de blob v1, V2 e de uso geral do Azure. O limite de *entrada* refere-se a todos os dados de solicitações enviadas para uma conta de armazenamento. O limite de *saída* refere-se a todos os dados de respostas recebidas de uma conta de armazenamento.

| Resource | Limite predefinido |
| --- | --- |
| Número de contas de armazenamento por região por assinatura, incluindo contas padrão e Premium | 250 |
| Capacidade máxima da conta de armazenamento | 2 PB para nós e Europa e 500 TB para todas as outras regiões (incluindo o Reino Unido) <sup>1</sup>|
| Número máximo de contêineres de BLOB, BLOBs, compartilhamentos de arquivos, tabelas, filas, entidades ou mensagens por conta de armazenamento | Sem limite |
| Taxa de solicitação máxima<sup>1</sup> por conta de armazenamento | 20.000 solicitações por segundo |
| Entrada máxima<sup>1</sup> por conta de armazenamento (EUA, regiões da Europa) | 25 Gbps |
| Entrada máxima<sup>1</sup> por conta de armazenamento (regiões diferentes de EUA e Europa) | 5 Gbps se RA-GRS/GRS estiver habilitado, 10 Gbps para LRS/ZRS<sup>2</sup> |
| Egresso máxima para contas de armazenamento de BLOBs V2 e de uso geral (todas as regiões) | 50 Gbps |
| Egresso máxima para contas de armazenamento v1 de uso geral (regiões dos EUA) | 20 Gbps se RA-GRS/GRS estiver habilitado, 30 Gbps para LRS/ZRS<sup>2</sup> |
| Egresso máxima para contas de armazenamento v1 de uso geral (regiões fora dos EUA) | 10 Gbps se RA-GRS/GRS estiver habilitado, 15 Gbps para LRS/ZRS<sup>2</sup> |

<sup>1</sup> As contas de armazenamento standard do Azure dão suporte a limites de capacidade mais altos e limites mais altos para entrada por solicitação. Para solicitar um aumento nos limites de conta para entrada, entre em contato com o [suporte do Azure](https://azure.microsoft.com/support/faq/). Para obter mais informações, consulte [anunciando contas de armazenamento maiores e de maior escala](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/).

<sup>2</sup> as opções de [replicação de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy) incluem:

- **RA-GRS**: Armazenamento com redundância geográfica com acesso de leitura. Se o RA-GRS estiver habilitado, os destinos de saída para o local secundário serão idênticos aos do local primário.
- **GRS**: Armazenamento com redundância geográfica.
- **ZRS**: Armazenamento com redundância de zona.
- **LRS**: Armazenamento com redundância local.

> [!NOTE]
> Recomendamos que você use uma conta de armazenamento de uso geral v2 para a maioria dos cenários. Você pode atualizar facilmente uma conta de uso geral v1 ou de armazenamento de BLOBs do Azure para uma conta v2 de finalidade geral sem tempo de inatividade e sem a necessidade de copiar dados.
>
> Para obter mais informações sobre contas de armazenamento do Azure, consulte [visão geral da conta de armazenamento](../articles/storage/common/storage-account-overview.md).

Se as necessidades do seu aplicativo excederem as metas de escalabilidade de uma única conta de armazenamento, você poderá criar seu aplicativo para usar várias contas de armazenamento. Em seguida, você pode particionar seus objetos de dados entre essas contas de armazenamento. Para obter informações sobre o preço do volume, consulte [preços do armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/).

Todas as contas de armazenamento são executadas em uma topologia de rede simples e dão suporte a escalabilidade e metas de desempenho descritas neste artigo, independentemente de quando elas foram criadas. Para obter mais informações sobre a arquitetura de rede simples do armazenamento do Azure e [sobre escalabilidade, consulte armazenamento do Microsoft Azure: Um serviço de armazenamento em nuvem altamente disponível com](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)consistência forte.

