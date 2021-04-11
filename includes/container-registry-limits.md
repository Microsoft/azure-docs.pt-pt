---
title: incluir ficheiro
description: incluir ficheiro
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 06/18/2020
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: b4701260a7d8da030f9f3019060aaa83e7a3a483
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104803224"
---
| Recurso | Básica | Standard | Premium |
|---|---|---|---|
| Armazenamento incluído<sup>1</sup> (GiB) | 10 | 100 | 500 |
| Limite de armazenamento (TiB) | 20| 20 | 20 |
| Tamanho máximo da camada de imagem (GiB) | 200 | 200 | 200 |
| Leituras por minuto<sup>2, 3</sup> | 1,000 | 3.000 | 10,000 |
| WriteOps por minuto<sup>2, 4</sup> | 100 | 500 | 2.000 |
| Baixar largura de banda<sup>2</sup> (Mbps) | 30 | 60 | 100 |
| Carregar largura de banda <sup>2</sup> (Mbps) | 10 | 20 | 50 |
| Webhooks | 2 | 10 | 500 |
| Georreplicação | N/D | N/D | [Suportado][geo-replication] |
| Zonas de disponibilidade | N/D | N/D | [Pré-visualizar][zones] |
| Confiança de conteúdo | N/D | N/D | [Suportado][content-trust] |
| Ligação privada com pontos finais privados | N/D | N/D | [Suportado][plink] |
| &bull; Pontos finais privados | N/D | N/D | 10 |
| Regras da rede IP pública | N/D | N/D | 100 |
| Acesso vnet de ponto final de serviço | N/D | N/D | [Pré-visualizar][vnet] |
| Chaves geridas pelo cliente | N/D | N/D | [Suportado][cmk] |
| Permissões de âmbito de repositório | N/D | N/D | [Pré-visualizar][token]|
| &bull; Fichas | N/D | N/D | 20 000 |
| &bull; Mapas de âmbito | N/D | N/D | 20 000 |
| &bull; Repositórios por mapa de âmbito | N/D | N/D | 500 |


<sup>1</sup> Armazenamento incluído na diária para cada nível. Pode ser utilizado um armazenamento adicional, até ao limite de armazenamento do registo, a uma taxa diária adicional por GiB. Para obter informações sobre tarifas, consulte [os preços do registo do contentor Azure][pricing]. Se necessitar de armazenamento para além do limite de armazenamento do registo, contacte o Suporte Azure.

<sup>2</sup>*ReadOps,* *WriteOps* e *Bandwidth* são estimativas mínimas. O Registo de Contentores Azure esforça-se para melhorar o desempenho conforme o uso requer.

<sup>3</sup> Um [puxão de estivar](https://docs.docker.com/registry/spec/api/#pulling-an-image) traduz-se em múltiplas operações de leitura com base no número de camadas na imagem, mais a recuperação do manifesto.

<sup>4</sup> Um [empurrão de estivar](https://docs.docker.com/registry/spec/api/#pushing-an-image) traduz-se em múltiplas operações de escrita, com base no número de camadas que devem ser empurradas. A `docker push` inclui *ReadOps* para recuperar um manifesto para uma imagem existente.

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/

<!-- LINKS - Internal -->
[geo-replication]: ../articles/container-registry/container-registry-geo-replication.md
[content-trust]: ../articles/container-registry/container-registry-content-trust.md
[vnet]: ../articles/container-registry/container-registry-vnet.md
[plink]: ../articles/container-registry/container-registry-private-link.md
[cmk]: ../articles/container-registry/container-registry-customer-managed-keys.md
[token]: ../articles/container-registry/container-registry-repository-scoped-permissions.md
[zones]: ../articles/container-registry/zone-redundancy.md
