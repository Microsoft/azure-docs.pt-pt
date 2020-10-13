---
title: Versões suportadas Postgres com Arco Azure habilitado Hiperescala PósgreSQL
description: Versões suportadas Postgres com Arco Azure habilitado Hiperescala PósgreSQL
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 0f3f9e7f4566800bdea6871cae1c5bafb3b37b95
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90939187"
---
# <a name="supported-versions-of-postgres-with-azure-arc-enabled-postgresql-hyperscale"></a>Versões suportadas do Postgres com o PostgreSQL Hyperscale preparado para o Azure Arc

Este artigo explica quais as versões de Postgres disponíveis com a Hiperescala PostgreSQL ativada.
A lista de versões suportadas evolui ao longo do tempo. Hoje, as principais versões que são suportadas são:
- Postgres 12 (padrão)
- Postgres 11

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="how-to-chose-between-versions"></a>Como escolher entre versões?
Recomenda-se que veja para que versões as suas aplicações foram desenhadas e quais são as capacidades de cada uma das versões. Para saber mais, leia sobre cada versão no site oficial dos Postgres:
- [Postgres 12 (padrão)](https://www.postgresql.org/docs/12/index.html)
- [Postgres 11](https://www.postgresql.org/docs/11/index.html)

## <a name="how-to-create-a-particular-version-in-azure-arc-enabled-postgresql-hyperscale"></a>Como criar uma versão específica em Azure Arc ativada em Hiperescala PósgreSQL?
No momento da criação, tem a possibilidade de indicar que versão criar ao passar o parâmetro _da versão --motor._ Se não indicar uma informação de versão, por padrão, será criado um grupo de servidores da versão 12 do Postgres.

## <a name="how-do-be-notified-when-other-versions-are-available"></a>Como ser notificado quando outras versões estão disponíveis?
Volta e lê este artigo. Será atualizado conforme apropriado. Também pode listar os tipos de definições de recursos personalizados (CRD) no Controlador de Dados do Arco no seu cluster Kubernetes.
Execute o seguinte comando:
```console
kubectl get crds
```

Devolverá uma saída como:
```console
NAME                                            CREATED AT
datacontrollers.arcdata.microsoft.com           2020-08-31T20:15:16Z
postgresql-11s.arcdata.microsoft.com            2020-08-31T20:15:16Z
postgresql-12s.arcdata.microsoft.com            2020-08-31T20:15:16Z
sqlmanagedinstances.sql.arcdata.microsoft.com   2020-08-31T20:15:16Z
```

Neste exemplo, esta saída indica que existem dois tipos de CRD relacionados com Postgres:
- postgresql-12s.arcdata.microsoft.com é o CRD para Postgres 12
- postgresql-11s.arcdata.microsoft.com é o CRD para Postgres 11

Estes são CRDs, não grupos de servidores. A presença de um CRD não é uma indicação de que tenha - ou não - criado um grupo de servidores dessa versão.
O CRD é uma indicação de que tipo de recursos podem ser criados.

## <a name="next-steps"></a>Passos seguintes:
- [Leia sobre a criação de Azure Arc ativado em hiperescala pósgreSQL](create-postgresql-hyperscale-server-group.md)
- [Leia sobre a obtenção de uma lista dos grupos de servidores de hiperescala pós-escala ativados do Arco](list-server-groups-postgres-hyperscale.md)
