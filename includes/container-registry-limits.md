---
title: incluir ficheiro
description: incluir ficheiro
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 03/11/2020
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 0090f02382e024e5539383328b55d58798002d63
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2020
ms.locfileid: "79117136"
---
| Recurso | Básica | Standard | Premium |
|---|---|---|---|
| Armazenamento<sup>1</sup> | 10 GiB | 100 GiB| 500 GiB |
| Tamanho máximo da camada de imagem | 200 GiB | 200 GiB | 200 GiB |
| ReadOps por minuto<sup>2, 3</sup> | 1,000 | 3000 | 10,000 |
| WriteOps por minuto<sup>2, 4</sup> | 100 | 500 | 2000 |
| Transferir largura de banda em MBps<sup>2</sup> | 30 | 60 | 100 |
| Carregar largura de banda em MBps<sup>2</sup> | 10 | 20 | 50 |
| Webhooks | 2 | 10 | 500 |
| Georreplicação | N/D | N/D | [Suportado][geo-replication] |
| Confiança de conteúdo | N/D | N/D | [Suportado][content-trust] |
| Acesso à rede virtual | N/D | N/D | [Pré-visualização][vnet] |
| Integração de ligações privadas | N/D | N/D | [Pré-visualização][plink] |
| Chaves geridas pelo cliente | N/D | N/D | [Pré-visualização][cmk] |
| Permissões no âmbito do repositório | N/D | N/D | [Pré-visualização][token]|
| &bull; Tokens | N/D | N/D | 20 000 |
| &bull; Mapas de âmbito | N/D | N/D | 20 000 |
| &bull; Repositórios por mapa de âmbito | N/D | N/D | 500 |


<sup>1</sup>Os limites de armazenamento especificados são a quantidade de armazenamento *incluída* para cada escalão. É-lhe cobrada uma tarifa diária adicional por GiB relativamente ao armazenamento de imagens acima destes limites. Para obter informações sobre as tarifas, veja os [preços do Azure Container Registry][pricing].

<sup>2</sup>*ReadOps*, *WriteOps* e *Largura de banda* são estimativas mínimas. O Azure Container Registry visa melhorar o desempenho à medida das necessidades da utilização.

<sup>3</sup>O comando [docker pull](https://docs.docker.com/registry/spec/api/#pulling-an-image) traduz para várias operações de leitura com base no número de camadas existentes na imagem, para além de fazer a obtenção do manifesto.

<sup>4</sup>O comando [docker push](https://docs.docker.com/registry/spec/api/#pushing-an-image) traduz para várias operações de escrita com base no número de camadas que têm de ser emitidas. `docker push` inclui *ReadOps* para obter um manifesto de uma imagem existente.

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/

<!-- LINKS - Internal -->
[geo-replication]: ../articles/container-registry/container-registry-geo-replication.md
[content-trust]: ../articles/container-registry/container-registry-content-trust.md
[vnet]: ../articles/container-registry/container-registry-vnet.md
[plink]: ../articles/container-registry/container-registry-private-link.md
[cmk]: ../articles/container-registry/container-registry-customer-managed-keys.md
[token]: ../articles/container-registry/container-registry-repository-scoped-permissions.md