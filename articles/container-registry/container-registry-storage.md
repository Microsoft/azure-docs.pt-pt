---
title: Armazenamento de imagem de contêiner
description: Detalhes sobre como as imagens de contêiner do Docker são armazenadas no registro de contêiner do Azure, incluindo segurança, redundância e capacidade.
ms.topic: article
ms.date: 03/21/2018
ms.openlocfilehash: f66c3dd95edfe5035c46857cb6f9aa59d8a6a0e1
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456202"
---
# <a name="container-image-storage-in-azure-container-registry"></a>Armazenamento de imagem de contêiner no registro de contêiner do Azure

Cada registro de contêiner do Azure [básico, Standard e Premium](container-registry-skus.md) se beneficia dos recursos avançados de armazenamento do Azure, como criptografia em repouso para segurança de dados de imagem e redundância geográfica para proteção de dados de imagem. As seções a seguir descrevem os recursos e os limites do armazenamento de imagem no ACR (registro de contêiner do Azure).

## <a name="encryption-at-rest"></a>Criptografia em repouso

Todas as imagens de contêiner em seu registro são criptografadas em repouso. O Azure criptografa automaticamente uma imagem antes de armazená-la e a descriptografa imediatamente quando você ou seus aplicativos e serviços efetuam pull da imagem.

## <a name="geo-redundant-storage"></a>Armazenamento georredundante

O Azure usa um esquema de armazenamento com redundância geográfica para proteger contra perda de suas imagens de contêiner. O registro de contêiner do Azure replica automaticamente suas imagens de contêiner para vários data centers distantes geograficamente, evitando sua perda no caso de uma falha de armazenamento regional.

## <a name="geo-replication"></a>Georreplicação

Para cenários que exigem ainda mais garantia de alta disponibilidade, considere o uso do recurso de [replicação geográfica](container-registry-geo-replication.md) de registros Premium. A replicação geográfica ajuda a proteger contra a perda de acesso ao registro no caso de uma falha regional *total* , não apenas uma falha de armazenamento. A replicação geográfica também fornece outros benefícios, como o armazenamento de imagem de fechamento de rede para envios por push e pull mais rápidos em cenários de desenvolvimento ou implantação distribuídos.

## <a name="image-limits"></a>Limites de imagem

A tabela a seguir descreve a imagem de contêiner e os limites de armazenamento em vigor para registros de contêiner do Azure.

| Recurso | Limite |
| -------- | :---- |
| Repositórios | Sem limite |
| Imagens | Sem limite |
| Layer | Sem limite |
| Etiquetas | Sem limite|
| Storage | 5 TB |

Números muito altos de repositórios e marcas podem afetar o desempenho do registro. Exclua periodicamente repositórios, marcas e imagens não utilizados como parte da rotina de manutenção do registro. Os recursos de registro excluídos, como repositórios, imagens e marcas, *não podem* ser recuperados após a exclusão. Para obter mais informações sobre como excluir recursos do registro, consulte [Excluir imagens de contêiner no registro de contêiner do Azure](container-registry-delete.md).

## <a name="storage-cost"></a>Custo do armazenamento

Para obter detalhes completos sobre os preços, consulte [preços do registro de contêiner do Azure][pricing].

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre as diferentes SKUs do registro de contêiner do Azure (Basic, Standard, Premium), consulte [SKUs do registro de contêiner do Azure](container-registry-skus.md).

<!-- IMAGES -->

<!-- LINKS - External -->
[portal]: https://portal.azure.com
[pricing]: https://aka.ms/acr/pricing

<!-- LINKS - Internal -->
