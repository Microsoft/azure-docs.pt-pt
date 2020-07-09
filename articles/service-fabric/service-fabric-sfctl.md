---
title: Azure Service Fabric CLI-sfctl
description: Saiba mais sobre o sfctl, a interface de linha de comando do Azure Service Fabric. Inclui uma lista de comandos e subgrupos.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 56efa15a7de3414f9c535e66bd80c94594cd5038
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "76906218"
---
# <a name="sfctl"></a>sfctl
Comandos para gerir clusters e entidades de Tecidos de Serviço. Esta versão é compatível com o tempo de execução do Service Fabric 7.0.

Os comandos seguem o padrão substantivo do verbo. Consulte os subgrupos para obter mais informações.

## <a name="subgroups"></a>Subgrupos
|Subgrupo|Descrição|
| --- | --- |
| [aplicação](service-fabric-sfctl-application.md) | Criar, excluir e gerir aplicações e tipos de aplicações. |
| [caos](service-fabric-sfctl-chaos.md) | Começa, para e reporta o serviço de testes do caos. |
| [cluster](service-fabric-sfctl-cluster.md) | Selecione, gerencie e opere os clusters de tecido de serviço. |
| [compor](service-fabric-sfctl-compose.md) | Criar, excluir e gerir aplicações Docker Compose. |
| [recipiente](service-fabric-sfctl-container.md) | Executar comandos relacionados com contentores num nó de cluster. |
| [eventos](service-fabric-sfctl-events.md) | Recupere os eventos da loja de eventos (se o serviço EventStore já estiver instalado). |
| [é](service-fabric-sfctl-is.md) | Consultar e enviar comandos para o serviço de infraestrutura. |
| [mesh](service-fabric-sfctl-mesh.md) | Eliminar e gerir aplicações de malha de tecido de serviço. |
| [nó](service-fabric-sfctl-node.md) | Gerir os nós que formam um aglomerado. |
| [partição](service-fabric-sfctl-partition.md) | Consultar e gerir divisórias para qualquer serviço. |
| [propriedade](service-fabric-sfctl-property.md) | Armazenar e consultar propriedades com nomes de Tecido de Serviço. |
| [réplica](service-fabric-sfctl-replica.md) | Gerir as réplicas que pertencem a divisórias de serviço. |
| [rpm](service-fabric-sfctl-rpm.md) | Consultar e enviar comandos para o serviço de gerente de reparação. |
| [sa-cluster](service-fabric-sfctl-sa-cluster.md) | Gerir clusters de tecido de serviço autónomos. |
| [serviço](service-fabric-sfctl-service.md) | Criar, eliminar e gerir serviços, tipos de serviço e pacotes de serviço. |
| [definições](service-fabric-sfctl-settings.md) | Configurar as definições locais para este caso de sfctl. |
| [loja](service-fabric-sfctl-store.md) | Efetue operações básicas de nível de ficheiro na loja de imagens de cluster. |

## <a name="next-steps"></a>Próximos passos
- [Configurar](service-fabric-cli.md) o CLI de Tecido de Serviço.
- Saiba como utilizar o CLI do tecido de serviço utilizando os [scripts](/azure/service-fabric/scripts/sfctl-upgrade-application)da amostra .