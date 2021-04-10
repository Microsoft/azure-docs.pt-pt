---
title: Armazenamento de imagem de recipiente
description: Detalhes sobre como as imagens do seu contentor e outros artefactos são armazenados no Registo de Contentores Azure, incluindo segurança, redundância e capacidade.
ms.topic: conceptual
ms.date: 03/24/2021
ms.custom: references_regions
ms.openlocfilehash: a9c8ec877ddb17603e82b763223278a2e5e36714
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105047750"
---
# <a name="container-image-storage-in-azure-container-registry"></a>Armazenamento de imagem de contentor no registo do contentor de Azure

Todos os registos [básicos, standard e premium](container-registry-skus.md) azure beneficiam de funcionalidades avançadas de armazenamento Azure, incluindo encriptação em repouso. As secções seguintes descrevem as características e limites de armazenamento de imagem no Registo do Contentor de Azure (ACR).

## <a name="encryption-at-rest"></a>Encriptação em repouso

Todas as imagens de contentores e outros artefactos do seu registo estão encriptadas em repouso. O Azure encripta automaticamente uma imagem antes de a armazenar e desencripta-a no voo quando você ou as suas aplicações e serviços retiram a imagem. Aplicar opcionalmente uma camada de encriptação extra com uma [chave gerida pelo cliente](container-registry-customer-managed-keys.md).

## <a name="regional-storage"></a>Armazenamento regional

O Registo de Contentores Azure armazena dados na região onde o registo é criado, para ajudar os clientes a cumprir os requisitos de residência e conformidade de dados. Em todas as regiões, com exceção do Brasil Sul e Sudeste Asiático, o Azure também pode armazenar dados de registo numa região emparelhada na mesma geografia. Nas regiões do Sul e do Sudeste Asiático, os dados de registo estão sempre confinados à região, para acomodar os requisitos de residência de dados para essas regiões.

Se ocorrer uma paragem regional, os dados do registo podem ficar indisponíveis e não são automaticamente recuperados. Os clientes que desejem ter os seus dados de registo armazenados em várias regiões para um melhor desempenho em diferentes geografias ou que desejem ter resiliência em caso de paralisação regional devem permitir [a geo-replicação.](container-registry-geo-replication.md)

## <a name="geo-replication"></a>Georreplicação

Para cenários que exijam garantias de alta disponibilidade, considere a utilização da funcionalidade [de geo-replicação](container-registry-geo-replication.md) dos registos Premium. A geo-replicação ajuda a prevenir a perda de acesso ao seu registo em caso de falha regional. A geo-replicação também proporciona outros benefícios, como o armazenamento de imagem de proximidade de rede para impulsos e puxas mais rápidos em cenários de desenvolvimento ou implementação distribuídos.

## <a name="zone-redundancy"></a>Redundância entre zonas

Para ajudar a criar um registo resiliente e de alta disponibilidade de contentores Azure, opcionalmente permitir [a redundância de zona](zone-redundancy.md) em regiões selecionadas de Azure. Uma característica do nível de serviço Premium, a redundância da zona utiliza [zonas de disponibilidade](../availability-zones/az-overview.md) de Azure para replicar o seu registo para um mínimo de três zonas separadas em cada região ativada. Combine a geo-replicação e a redundância da zona para aumentar a fiabilidade e o desempenho de um registo. 

## <a name="scalable-storage"></a>Armazenamento escalável

O Registo de Contentores Azure permite-lhe criar o maior número de repositórios, imagens, camadas ou tags que precisar, até ao limite de armazenamento do [registo.](container-registry-skus.md#service-tier-features-and-limits) 

Um elevado número de repositórios e tags pode afetar o desempenho do seu registo. Eliminar periodicamente repositórios, etiquetas e imagens não utenseios como parte da sua rotina de manutenção do registo, e definir opcionalmente uma política de [retenção](container-registry-retention-policy.md) para manifestos não marcados. Os recursos de registo eliminados, tais como repositórios, imagens e *etiquetas, não podem* ser recuperados após a eliminação. Para obter mais informações sobre a eliminação dos recursos de registo, consulte [apagar as imagens dos contentores no Registo do Contentor de Azure.](container-registry-delete.md)

## <a name="storage-cost"></a>Custo do armazenamento

Para obter todos os detalhes sobre os preços, consulte [os preços do registo do contentor Azure.][pricing]

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre os registos básicos, standard e premium dos contentores, consulte [os níveis de serviço de registo de contentores Azure.](container-registry-skus.md)

<!-- IMAGES -->

<!-- LINKS - External -->
[portal]: https://portal.azure.com
[pricing]: https://aka.ms/acr/pricing

<!-- LINKS - Internal -->
