---
title: incluir ficheiro
description: incluir ficheiro
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 04/29/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: d20e266d1331fc15e65b2d119468483ff53a4c06
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64951558"
---
| Recurso | Básica | Standard | Premium |
|---|---|---|---|
| Armazenamento<sup>1</sup> | 10 GiB | 100 GiB| 500 GiB |
| Tamanho da camada de imagem máximo | 200 GiB | 200 GiB | 200 GiB |
| ReadOps por minuto<sup>2, 3</sup> | 1,000 | 3000 | 10,000 |
| WriteOps por minuto<sup>2, 4</sup> | 100 | 500 | 2.000 |
| Transferir MBps da largura de banda<sup>2</sup> | 30 | 60 | 100 |
| Carregar MBps da largura de banda<sup>2</sup> | 10 | 20 | 50 |
| Webhooks | 2 | 10 | 100 |
| Georreplicação | N/A | N/A | [Suportado][geo-replication] |
| Fidedignidade do conteúdo (pré-visualização) | N/A | N/A | [Suportado][content-trust] |

<sup>1</sup>os limites de armazenamento especificadas são a quantidade de *incluído* armazenamento para cada camada. Que lhe é cobrada uma taxa diária adicional por GiB para o armazenamento de imagem acima estes limites. Para informações de taxa, consulte [preços do Azure Container Registry][pricing].

<sup>2</sup>*ReadOps*, *WriteOps*, e *largura de banda* são estimativas mínimo. O Azure Container Registry está empenhada em melhorar o desempenho à medida que necessita de utilização.

<sup>3</sup>uma [pull do docker](https://docs.docker.com/registry/spec/api/#pulling-an-image) se traduz em várias operações de leitura, com base no número de camadas na imagem, bem como a obtenção de manifesto.

<sup>4</sup>uma [push do docker](https://docs.docker.com/registry/spec/api/#pushing-an-image) se traduz em várias operações de escrita, com base no número de camadas que devem ser colocadas. R `docker push` inclui *ReadOps* para recuperar um manifesto para uma imagem existente.

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/

<!-- LINKS - Internal -->
[geo-replication]: ../articles/container-registry/container-registry-geo-replication.md
[content-trust]: ../articles/container-registry/container-registry-content-trust.md
