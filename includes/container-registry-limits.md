---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 06/18/2020
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 643cf8fd400adf06bf61f070947bd78ba7be50eb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85242166"
---
| Recurso | Básica | Standard | Premium |
|---|---|---|---|
| Armazenamento incluído<sup>1</sup> (GiB) | 10 | 100 | 500 |
| Limite de armazenamento (TiB) | 20| 20 | 20 |
| Tamanho máximo da camada de imagem (GiB) | 200 | 200 | 200 |
| Leituras por minuto<sup>2, 3</sup> | 1,000 | 3.000 | 10,000 |
| WriteOps por minuto<sup>2, 4</sup> | 100 | 500 | 2.000 |
| Baixar largura de banda MBps<sup>2</sup> | 30 | 60 | 100 |
| Carregar largura de banda MBps<sup>2</sup> | 10 | 20 | 50 |
| Webhooks | 2 | 10 | 500 |
| Georreplicação | N/D | N/D | [Suportado][geo-replication] |
| Confiança de conteúdo | N/D | N/D | [Suportado][content-trust] |
| Ligação privada com pontos finais privados | N/D | N/D | [Suportado][plink] |
| Acesso vnet de ponto final de serviço | N/D | N/D | [Pré-visualizar][vnet] |
| Chaves geridas pelo cliente | N/D | N/D | [Suportado][cmk] |
| Permissões de âmbito de repositório | N/D | N/D | [Pré-visualizar][token]|
| &bull;Fichas | N/D | N/D | 20 000 |
| &bull;Mapas de âmbito | N/D | N/D | 20 000 |
| &bull;Repositórios por mapa de âmbito | N/D | N/D | 500 |


<sup>1</sup> Armazenamento incluído na diária para cada nível. Para armazenamento adicional, é-lhe cobrada uma taxa diária adicional por GiB, até ao limite de armazenamento. Para obter informações sobre tarifas, consulte [os preços do registo do contentor Azure][pricing].

<sup>2</sup>*ReadOps,* *WriteOps*e *Bandwidth* são estimativas mínimas. O Registo de Contentores Azure esforça-se para melhorar o desempenho conforme o uso requer.

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