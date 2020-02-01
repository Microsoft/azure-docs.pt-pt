---
title: CLI do Azure Service Fabric-sfctl
description: Saiba mais sobre o sfctl, a interface de linha de comando Service Fabric do Azure. Inclui uma lista de comandos e subgrupos.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 56efa15a7de3414f9c535e66bd80c94594cd5038
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906218"
---
# <a name="sfctl"></a>sfctl
Comandos para gerenciar Service Fabric clusters e entidades. Esta versão é compatível com o tempo de execução do Tecido de Serviço 7.0.

Os comandos seguem o padrão substantivo-Verb. Consulte subgrupos para obter mais informações.

## <a name="subgroups"></a>Subgrupos
|Subgrupo|Descrição|
| --- | --- |
| [application](service-fabric-sfctl-application.md) | Criar, excluir e gerenciar aplicativos e tipos de aplicativos. |
| [chaos](service-fabric-sfctl-chaos.md) | Inicie, pare e relate o serviço de teste de caos. |
| [cluster](service-fabric-sfctl-cluster.md) | Selecione, gerencie e opere Service Fabric clusters. |
| [compose](service-fabric-sfctl-compose.md) | Crie, exclua e gerencie aplicativos Docker Compose. |
| [container](service-fabric-sfctl-container.md) | Execute comandos relacionados ao contêiner em um nó de cluster. |
| [LostFocus](service-fabric-sfctl-events.md) | Recupere eventos do repositório de eventos (se o serviço EventStore já estiver instalado). |
| [is](service-fabric-sfctl-is.md) | Consultar e enviar comandos para o serviço de infraestrutura. |
| [mesh](service-fabric-sfctl-mesh.md) | Exclua e gerencie aplicativos de malha Service Fabric. |
| [nó](service-fabric-sfctl-node.md) | Gerencie os nós que formam um cluster. |
| [partition](service-fabric-sfctl-partition.md) | Consultar e gerenciar partições para qualquer serviço. |
| [propriedade](service-fabric-sfctl-property.md) | Armazenar e consultar Propriedades em nomes de Service Fabric. |
| [replica](service-fabric-sfctl-replica.md) | Gerencie as réplicas que pertencem a partições de serviço. |
| [rpm](service-fabric-sfctl-rpm.md) | Consultar e enviar comandos para o serviço do Gerenciador de reparo. |
| [sa-cluster](service-fabric-sfctl-sa-cluster.md) | Gerenciar clusters Service Fabric autônomos. |
| [service](service-fabric-sfctl-service.md) | Criar, excluir e gerenciar serviço, tipos de serviço e pacotes de serviço. |
| [settings](service-fabric-sfctl-settings.md) | Defina as configurações local para essa instância do sfctl. |
| [store](service-fabric-sfctl-store.md) | Execute operações básicas de nível de arquivo no repositório de imagens do cluster. |

## <a name="next-steps"></a>Passos seguintes
- [Configure](service-fabric-cli.md) a CLI do Service Fabric.
- Saiba como usar a CLI do Service Fabric usando os [scripts de exemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).