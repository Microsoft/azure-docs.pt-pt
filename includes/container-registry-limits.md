---
title: incluir ficheiro
description: incluir ficheiro
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 11/05/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 6707e844948ac76d4cec29faf69d80b3c9cb3c0f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75392262"
---
| Recurso | Basic | Padrão | Premium |
|---|---|---|---|
| Armazenamento<sup>1</sup> | 10 GiB | 100 GiB| 500 GiB |
| Tamanho máximo da camada de imagem | 200 GiB | 200 GiB | 200 GiB |
| ReadOps por minuto<sup>2, 3</sup> | 1,000 | 3000 | 10,000 |
| WriteOps por minuto<sup>2, 4</sup> | 100 | 500 | 2\.000 |
| Baixar largura de banda<sup>2</sup> | 30 | 60 | 100 |
| Carregar largura de banda<sup>2</sup> | 10 | 20 | 50 |
| Webhooks | 2 | 10 | 500 |
| Georreplicação | N/A | N/A | [Suportado][geo-replication] |
| Confiança de conteúdo | N/A | N/A | [Suportado][content-trust] |
| Acesso à rede virtual | N/A | N/A | [Pré-visualização][vnet] |
| Permissões no escopo do repositório | N/A | N/A | [Pré-visualização][token]|
| Tokens de &bull; | N/A | N/A | 20,000 |
| &bull; mapas de escopo | N/A | N/A | 20,000 |
| &bull; repositórios por mapa de escopo | N/A | N/A | 500 |


<sup>1</sup> Os limites de armazenamento especificados são a quantidade de armazenamento *incluído* para cada camada. Você é cobrado por uma taxa diária adicional por GiB para o armazenamento de imagens acima desses limites. Para obter informações de taxa, consulte [preços do registro de contêiner do Azure][pricing].

<sup>2</sup>*ReadOps*, *WriteOps*e *largura de banda* são estimativas mínimas. O registro de contêiner do Azure se esforça para melhorar o desempenho conforme exigido pelo uso.

<sup>3</sup> Uma [Docker pull](https://docs.docker.com/registry/spec/api/#pulling-an-image) se traduz em várias operações de leitura com base no número de camadas na imagem, além da recuperação do manifesto.

<sup>4</sup> Um [push do Docker](https://docs.docker.com/registry/spec/api/#pushing-an-image) se traduz em várias operações de gravação, com base no número de camadas que devem ser enviadas por push. Uma `docker push` inclui *ReadOps* para recuperar um manifesto para uma imagem existente.

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/

<!-- LINKS - Internal -->
[geo-replication]: ../articles/container-registry/container-registry-geo-replication.md
[content-trust]: ../articles/container-registry/container-registry-content-trust.md
[vnet]: ../articles/container-registry/container-registry-vnet.md
[token]: ../articles/container-registry/container-registry-repository-scoped-permissions.md