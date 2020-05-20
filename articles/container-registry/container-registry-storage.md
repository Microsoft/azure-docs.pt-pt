---
title: Armazenamento de imagem de recipiente
description: Detalhes sobre como as imagens do seu contentor Docker são armazenadas no Registo de Contentores Azure, incluindo segurança, redundância e capacidade.
ms.topic: article
ms.date: 03/21/2018
ms.openlocfilehash: b738556e5a4f764cd47c72d964ee188d1344b336
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83683395"
---
# <a name="container-image-storage-in-azure-container-registry"></a>Armazenamento de imagem de contentor no Registo de Contentores de Azure

Todos os registos de contentores [Basic, Standard e Premium](container-registry-skus.md) Azure beneficiam de funcionalidades avançadas de armazenamento azure, como encriptação em repouso para segurança de dados de imagem e georedundância para proteção de dados de imagem. As seguintes secções descrevem tanto as características como os limites do armazenamento de imagem no Registo de Contentores Azure (ACR).

## <a name="encryption-at-rest"></a>Encriptação em repouso

Todas as imagens do contentor no seu registo estão encriptadas em repouso. O Azure encripta automaticamente uma imagem antes de a armazenar e desencripta-a no voo quando você ou as suas aplicações e serviços puxam a imagem.

## <a name="geo-redundant-storage"></a>Armazenamento georredundante

O Azure usa um esquema de armazenamento geo-redundante para se proteger da perda das imagens do seu contentor. O Registo de Contentores Azure replica automaticamente as imagens do seu contentor a múltiplos centros de dados geograficamente distantes, evitando a sua perda em caso de falha de armazenamento regional.

## <a name="geo-replication"></a>Georreplicação

Para cenários que requerem ainda mais garantia de alta disponibilidade, considere utilizar a característica de [geo-replicação](container-registry-geo-replication.md) dos registos Premium. A geo-replicação ajuda a evitar perder o acesso ao seu registo em caso de falha *total* regional, e não apenas uma falha de armazenamento. A geo-replicação também proporciona outros benefícios, como o armazenamento de imagem de proximidade da rede para impulsos mais rápidos e puxa em cenários de desenvolvimento ou implementação distribuídos.

## <a name="image-limits"></a>Limites de imagem

A tabela seguinte descreve os limites de imagem e armazenamento do contentor em vigor para os registos de contentores Azure.

| Recurso | Limite |
| -------- | :---- |
| Repositórios | Sem limite |
| Imagens | Sem limite |
| Camadas | Sem limite |
| Etiquetas | Sem limite|
| Armazenamento | 5 TB |

Um número muito elevado de repositórios e etiquetas pode ter impacto no desempenho do seu registo. Elimine periodicamente repositórios, etiquetas e imagens não utilizados como parte da sua rotina de manutenção do registo. Os recursos de registo apagados, como repositórios, imagens e *etiquetas, não podem* ser recuperados após a eliminação. Para obter mais informações sobre a eliminação de recursos de registo, consulte Apagar imagens de contentores no Registo de [Contentores de Azure](container-registry-delete.md).

## <a name="storage-cost"></a>Custo do armazenamento

Para mais detalhes sobre os preços, consulte o preço do Registo de [Contentores De Azure][pricing].

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre os registos de contentores Básicos, Standard e Premium, consulte os níveis de serviço do Registo de [Contentores De Azure](container-registry-skus.md).

<!-- IMAGES -->

<!-- LINKS - External -->
[portal]: https://portal.azure.com
[pricing]: https://aka.ms/acr/pricing

<!-- LINKS - Internal -->
