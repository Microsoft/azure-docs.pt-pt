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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79117136"
---
| Recurso | Básico | Standard | Premium |
|---|---|---|---|
| Armazenamento<sup>1</sup> | 10 GiB | 100 GiB| 500 GiB |
| Tamanho máximo da camada de imagem | 200 GiB | 200 GiB | 200 GiB |
| ReadOps por minuto<sup>2, 3</sup> | 1,000 | 3.000 | 10,000 |
| WriteOps por minuto<sup>2, 4</sup> | 100 | 500 | 2.000 |
| Baixar largura de banda MBps<sup>2</sup> | 30 | 60 | 100 |
| Carregar largura de banda MBps<sup>2</sup> | 10 | 20 | 50 |
| Webhooks | 2 | 10 | 500 |
| Georreplicação | N/D | N/D | [Apoiado][geo-replication] |
| Confiança de conteúdo | N/D | N/D | [Apoiado][content-trust] |
| Acesso à rede virtual | N/D | N/D | [previsualizar][vnet] |
| Integração de ligações privadas | N/D | N/D | [previsualizar][plink] |
| Chaves geridas pelo cliente | N/D | N/D | [previsualizar][cmk] |
| Permissões com âmbito de repositório | N/D | N/D | [previsualizar][token]|
| &bull;Fichas | N/D | N/D | 20 000 |
| &bull;Mapas de âmbito | N/D | N/D | 20 000 |
| &bull;Repositórios por mapa de âmbito | N/D | N/D | 500 |


<sup>1</sup> Os limites de armazenamento especificados são a quantidade de armazenamento *incluído* para cada nível. É-lhe cobrada uma taxa diária adicional por GiB para armazenamento de imagem acima destes limites. Para obter informações sobre tarifas, consulte os preços do Registo de [Contentores de Azure][pricing].

<sup>2</sup>*ReadOps*, *WriteOps*e *Largura de Banda* são estimativas mínimas. O Registo de Contentores Azure esforça-se por melhorar o desempenho conforme o uso requer.

<sup>3</sup> Um [puxão de estiva traduz-se](https://docs.docker.com/registry/spec/api/#pulling-an-image) em múltiplas operações de leitura baseadas no número de camadas na imagem, mais a recuperação manifesta.

<sup>4</sup> Um [impulso de estivador](https://docs.docker.com/registry/spec/api/#pushing-an-image) traduz-se em múltiplas operações de escrita, com base no número de camadas que devem ser empurradas. A `docker push` inclui *ReadOps* para recuperar um manifesto para uma imagem existente.

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/

<!-- LINKS - Internal -->
[geo-replication]: ../articles/container-registry/container-registry-geo-replication.md
[content-trust]: ../articles/container-registry/container-registry-content-trust.md
[vnet]: ../articles/container-registry/container-registry-vnet.md
[plink]: ../articles/container-registry/container-registry-private-link.md
[cmk]: ../articles/container-registry/container-registry-customer-managed-keys.md
[token]: ../articles/container-registry/container-registry-repository-scoped-permissions.md