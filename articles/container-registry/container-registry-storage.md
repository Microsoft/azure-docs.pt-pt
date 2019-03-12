---
title: Armazenamento de imagens no Azure Container Registry
description: Detalhes sobre como as imagens de contentor do Docker são armazenadas no Azure Container Registry, incluindo segurança, redundância e a capacidade.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 03/21/2018
ms.author: danlep
ms.openlocfilehash: 55c84907ab41f6da9d7a0989c68a1c1f90c5e424
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57551029"
---
# <a name="container-image-storage-in-azure-container-registry"></a>Armazenamento de imagens de contentor no Azure Container Registry

Cada [básico, Standard e Premium](container-registry-skus.md) benefícios de registo de contentor do Azure com recursos avançados de armazenamento do Azure, como a encriptação em repouso para segurança de dados de imagem e pela georredundância para proteção de dados de imagem. As secções seguintes descrevem os recursos e os limites de armazenamento de imagem no Azure Container Registry (ACR).

## <a name="encryption-at-rest"></a>Encriptação em repouso

Todas as imagens de contentor no seu registo são encriptadas em inatividade. O Azure automaticamente criptografa uma imagem antes de os armazenar e desencripta-lo no momento, quando ou seus aplicativos e serviços, extrair a imagem.

## <a name="geo-redundant-storage"></a>Armazenamento georredundante

Para proteger contra a perda de imagens de contentor, o Azure utiliza um esquema de armazenamento georredundante. O registo de contentor do Azure replica automaticamente as imagens de contentor para vários centros de dados, geograficamente distante, impedir a perda de seus em caso de falha de armazenamento regional.

## <a name="geo-replication"></a>Georreplicação

Para cenários que requerem a garantia de alta disponibilidade ainda mais, considere a utilização a [georreplicação](container-registry-geo-replication.md) funcionalidade de registos Premium. Replicação geográfica ajuda na proteção contra a perda do acesso ao seu registo na eventualidade de uma *total* falha regional, não apenas uma falha de armazenamento. Replicação geográfica fornece outras vantagens, também, como imagem de perto da rede armazenamento para mais rapidamente envia por push e efetua pull em cenários de desenvolvimento ou implementação distribuídos.

## <a name="image-limits"></a>Limites de imagem

A tabela seguinte descreve os limites de armazenamento e de imagem de contentor num local de registos de contentores do Azure.

| Recurso | Limite |
| -------- | :---- |
| Repositórios | Sem limite |
| Imagens | Sem limite |
| Camadas | Sem limite |
| Etiquetas | Sem limite|
| Armazenamento | 5 TB |

Muito elevado número de repositórios e as etiquetas pode afetar o desempenho do seu registo. Elimine repositórios não utilizados, marcas e imagens periodicamente como parte da sua rotina de manutenção do registo. Eliminar recursos de registo como repositórios, imagens e etiquetas *não é possível* ser recuperado após a eliminação. Para obter mais informações sobre como eliminar os recursos de registo, consulte [eliminar imagens de contentor no Azure Container Registry](container-registry-delete.md).

## <a name="storage-cost"></a>Custo de armazenamento

Para obter detalhes completos sobre os preços, consulte [preços do Azure Container Registry][pricing].

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre os diferentes SKUs do Azure Container Registry (Basic, Standard, Premium), consulte [SKUs de registo de contentor do Azure](container-registry-skus.md).

<!-- IMAGES -->

<!-- LINKS - External -->
[portal]: https://portal.azure.com
[pricing]: https://aka.ms/acr/pricing

<!-- LINKS - Internal -->
