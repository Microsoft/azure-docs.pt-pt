---
title: incluir ficheiro
description: incluir ficheiro
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 05/14/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: ee8ff3529524a63ca2e54a64327570197f363538
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "67184623"
---
| Resource | Básica | Standard | Premium |
|---|---|---|---|
| Armazenamento<sup>1</sup> | 10 GiB | 100 GiB| 500 GiB |
| Tamanho máximo da camada de imagem | 200 GiB | 200 GiB | 200 GiB |
| ReadOps por minuto<sup>2, 3</sup> | 1,000 | 3\.000 | 10,000 |
| WriteOps por minuto<sup>2, 4</sup> | 100 | 500 | 2\.000 |
| Baixar largura de banda<sup>2</sup> | 30 | 60 | 100 |
| Carregar largura de banda<sup>2</sup> | 10 | 20 | 50 |
| Webhooks | 2 | 10 | 100 |
| Georreplicação | N/A | N/A | [Suportado][geo-replication] |
| Confiança de conteúdo | N/A | N/A | [Suportado][content-trust] |

<sup>1</sup> Os limites de armazenamento especificados são a quantidade de armazenamento *incluído* para cada camada. Você é cobrado por uma taxa diária adicional por GiB para o armazenamento de imagens acima desses limites. Para obter informações de taxa, consulte [preços do registro de contêiner do Azure][pricing].

<sup>2</sup> *ReadOps*, *WriteOps*e *largura de banda* são estimativas mínimas. O registro de contêiner do Azure se esforça para melhorar o desempenho conforme exigido pelo uso.

<sup>3</sup> Uma [Docker pull](https://docs.docker.com/registry/spec/api/#pulling-an-image) se traduz em várias operações de leitura com base no número de camadas na imagem, além da recuperação do manifesto.

<sup>4</sup> Um [push do Docker](https://docs.docker.com/registry/spec/api/#pushing-an-image) se traduz em várias operações de gravação, com base no número de camadas que devem ser enviadas por push. Um `docker push` inclui *ReadOps* para recuperar um manifesto para uma imagem existente.

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/

<!-- LINKS - Internal -->
[geo-replication]: ../articles/container-registry/container-registry-geo-replication.md
[content-trust]: ../articles/container-registry/container-registry-content-trust.md
