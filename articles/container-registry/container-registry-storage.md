---
title: Armazenamento de imagem de recipiente
description: Detalhes sobre como as imagens do seu contentor Docker são armazenadas no Registo de Contentores Azure, incluindo segurança, redundância e capacidade.
ms.topic: article
ms.date: 06/18/2020
ms.openlocfilehash: d51014e9e0769091aba42682cce3a6a01cfa19de
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85214065"
---
# <a name="container-image-storage-in-azure-container-registry"></a>Armazenamento de imagem de contentor no registo do contentor de Azure

Todos os registos [básicos, standard e premium](container-registry-skus.md) azure beneficiam de funcionalidades avançadas de armazenamento Azure, como encriptação em repouso para segurança de dados de imagem e geo-redundância para proteção de dados de imagem. As secções seguintes descrevem tanto as características como os limites do armazenamento de imagem no Registo do Contentor de Azure (ACR).

## <a name="encryption-at-rest"></a>Encriptação em repouso

Todas as imagens do contentor no seu registo estão encriptadas em repouso. O Azure encripta automaticamente uma imagem antes de a armazenar e desencripta-a no voo quando você ou as suas aplicações e serviços retiram a imagem. Aplicar opcionalmente uma camada de encriptação adicional com uma [chave gerida pelo cliente](container-registry-customer-managed-keys.md).

## <a name="geo-redundant-storage"></a>Armazenamento georredundante

O Azure usa um sistema de armazenamento geo-redundante para evitar a perda das imagens do seu contentor. O Registo de Contentores Azure replica automaticamente as imagens do seu contentor em vários centros de dados geograficamente distantes, evitando a sua perda em caso de falha de armazenamento regional.

## <a name="geo-replication"></a>Georreplicação

Para cenários que exijam uma garantia de disponibilidade ainda mais elevada, considere a utilização da funcionalidade [de geo-replicação](container-registry-geo-replication.md) dos registos Premium. A geo-replicação ajuda a prevenir a perda de acesso ao seu registo em caso de falha regional *total,* e não apenas uma falha de armazenamento. A geo-replicação também proporciona outros benefícios, como o armazenamento de imagem de proximidade de rede para impulsos e puxas mais rápidos em cenários de desenvolvimento ou implementação distribuídos.

## <a name="scalable-storage"></a>Armazenamento escalável

O Registo de Contentores Azure permite-lhe criar o maior número de repositórios, imagens, camadas ou tags que precisar, até ao limite de armazenamento do [registo.](container-registry-skus.md#service-tier-features-and-limits) 

Um número muito elevado de repositórios e tags pode ter impacto no desempenho do seu registo. Eliminar periodicamente repositórios, etiquetas e imagens não utenseios como parte da sua rotina de manutenção do registo, e definir opcionalmente uma política de [retenção](container-registry-retention-policy.md) para manifestos não marcados. Os recursos de registo eliminados, tais como repositórios, imagens e *etiquetas, não podem* ser recuperados após a eliminação. Para obter mais informações sobre a eliminação dos recursos de registo, consulte [apagar as imagens dos contentores no Registo do Contentor de Azure.](container-registry-delete.md)

## <a name="storage-cost"></a>Custo do armazenamento

Para obter todos os detalhes sobre os preços, consulte [os preços do registo do contentor Azure.][pricing]

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre os registos básicos, standard e premium dos contentores, consulte [os níveis de serviço de registo de contentores Azure.](container-registry-skus.md)

<!-- IMAGES -->

<!-- LINKS - External -->
[portal]: https://portal.azure.com
[pricing]: https://aka.ms/acr/pricing

<!-- LINKS - Internal -->
