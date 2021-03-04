---
title: Armazenamento de imagem de recipiente
description: Detalhes sobre como as imagens do seu contentor e outros artefactos são armazenados no Registo de Contentores Azure, incluindo segurança, redundância e capacidade.
ms.topic: article
ms.date: 03/02/2021
ms.custom: references_regions
ms.openlocfilehash: 4bdffd111273e00b796e45f4e09bfac9ba6713e4
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102036015"
---
# <a name="container-image-storage-in-azure-container-registry"></a>Armazenamento de imagem de contentor no registo do contentor de Azure

Todos os registos [básicos, standard e premium](container-registry-skus.md) azure beneficiam de funcionalidades avançadas de armazenamento Azure, como encriptação em repouso para segurança de dados de imagem e geo-redundância para proteção de dados de imagem. As secções seguintes descrevem tanto as características como os limites do armazenamento de imagem no Registo do Contentor de Azure (ACR).

## <a name="encryption-at-rest"></a>Encriptação em repouso

Todas as imagens de contentores e outros artefactos do seu registo estão encriptadas em repouso. O Azure encripta automaticamente uma imagem antes de a armazenar e desencripta-a no voo quando você ou as suas aplicações e serviços retiram a imagem. Aplicar opcionalmente uma camada de encriptação extra com uma [chave gerida pelo cliente](container-registry-customer-managed-keys.md).

## <a name="geo-redundant-storage"></a>Armazenamento georredundante

Para os registos de contentores implantados na maioria das regiões, a Azure utiliza um sistema de armazenamento geodussuquente para ajudar a prevenir a perda das imagens dos seus contentores e outros artefactos. O Registo de Contentores Azure replica automaticamente as imagens do seu contentor em vários centros de dados geograficamente distantes, evitando a sua perda se ocorrer uma falha de armazenamento regional.

> [!IMPORTANT]
> * Se ocorrer uma falha de armazenamento regional, os dados do registo só podem ser recuperados contactando o Suporte Azure. 
> * Devido aos requisitos de residência de dados no Brasil Sul e Sudeste Asiático, os dados do Registo de Contentores Azure nessas regiões são armazenados apenas em [geo local.](https://azure.microsoft.com/global-infrastructure/geographies/) Para o Sudeste Asiático, todos os dados são armazenados em Singapura. Para o Brasil Sul, todos os dados são armazenados no Brasil. Quando a região se perder devido a um desastre significativo, a Microsoft não poderá recuperar os seus dados do Registo de Contentores Azure.

## <a name="geo-replication"></a>Georreplicação

Para cenários que exijam uma garantia de disponibilidade ainda mais elevada, considere a utilização da funcionalidade [de geo-replicação](container-registry-geo-replication.md) dos registos Premium. A geo-replicação ajuda a prevenir a perda de acesso ao seu registo em caso de falha regional *total,* e não apenas uma falha de armazenamento. A geo-replicação também proporciona outros benefícios, como o armazenamento de imagem de proximidade de rede para impulsos e puxas mais rápidos em cenários de desenvolvimento ou implementação distribuídos.

## <a name="zone-redundancy"></a>Redundância entre zonas

Para criar um registo resiliente e de alta disponibilidade de contentores Azure, opcionalmente permitir [a redundância de zona](zone-redundancy.md) em regiões selecionadas de Azure. Uma característica do nível de serviço Premium, a redundância da zona utiliza [zonas de disponibilidade](../availability-zones/az-overview.md) de Azure para replicar o seu registo para um mínimo de três zonas separadas em cada região ativada. Combine a geo-replicação e a redundância da zona para aumentar a fiabilidade e o desempenho de um registo. 

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
