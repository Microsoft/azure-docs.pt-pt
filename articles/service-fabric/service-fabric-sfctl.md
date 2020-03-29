---
title: Tecido de serviço Azure CLI-sfctl
description: Conheça o sfctl, a interface de linha de comando Azure Service Fabric. Inclui uma lista de comandos e subgrupos.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 56efa15a7de3414f9c535e66bd80c94594cd5038
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906218"
---
# <a name="sfctl"></a>sfctl
Comandos para gestão de clusters e entidades de Tecido de Serviço. Esta versão é compatível com o tempo de execução do Tecido de Serviço 7.0.

Os comandos seguem o padrão substantivo-verbo. Consulte os subgrupos para obter mais informações.

## <a name="subgroups"></a>Subgrupos
|Subgrupo|Descrição|
| --- | --- |
| [aplicação](service-fabric-sfctl-application.md) | Criar, eliminar e gerir aplicações e tipos de aplicações. |
| [caos](service-fabric-sfctl-chaos.md) | Comece, pare e informe sobre o serviço de teste do caos. |
| [aglomerado](service-fabric-sfctl-cluster.md) | Selecione, gere e opere os clusters de Tecido de Serviço. |
| [compor](service-fabric-sfctl-compose.md) | Crie, elimine e gereas aplicações do Docker Compose. |
| [recipiente](service-fabric-sfctl-container.md) | Executar comandos relacionados com contentores num nó de aglomerado. |
| [eventos](service-fabric-sfctl-events.md) | Recupere os eventos da loja de eventos (se o serviço EventStore já estiver instalado). |
| [é](service-fabric-sfctl-is.md) | Consulta e envie comandos para o serviço de infraestrutura. |
| [mesh](service-fabric-sfctl-mesh.md) | Eliminar e gerir aplicações de malha de tecido de serviço. |
| [nó](service-fabric-sfctl-node.md) | Gerencie os nós que formam um aglomerado. |
| [partição](service-fabric-sfctl-partition.md) | Consultar e gerir divisórias para qualquer serviço. |
| [propriedade](service-fabric-sfctl-property.md) | Armazenar e consultar propriedades sob nomes de Tecido de Serviço. |
| [réplica](service-fabric-sfctl-replica.md) | Gerencie as réplicas que pertencem às divisórias de serviço. |
| [rpm](service-fabric-sfctl-rpm.md) | Consulta e envie comandos para o serviço de reparação. |
| [sa-cluster](service-fabric-sfctl-sa-cluster.md) | Gerencie os clusters de tecido de serviço autónomos. |
| [serviço](service-fabric-sfctl-service.md) | Criar, eliminar e gerir os serviços, os tipos de serviço e os pacotes de serviço. |
| [definições](service-fabric-sfctl-settings.md) | Configure as configurações locais para esta instância de sfctl. |
| [loja](service-fabric-sfctl-store.md) | Efetue operações básicas de nível de ficheiro na loja de imagens do cluster. |

## <a name="next-steps"></a>Passos seguintes
- [Instale](service-fabric-cli.md) o CLI de tecido de serviço.
- Aprenda a utilizar o CLI de tecido de serviço utilizando as [scripts de amostra](/azure/service-fabric/scripts/sfctl-upgrade-application).