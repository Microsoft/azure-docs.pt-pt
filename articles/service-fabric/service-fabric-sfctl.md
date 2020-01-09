---
title: CLI do Azure Service Fabric-sfctl
description: Saiba mais sobre o sfctl, a interface de linha de comando Service Fabric do Azure. Inclui uma lista de comandos e subgrupos.
author: jeffj6123
ms.topic: reference
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: 518e60ee92a637533fdf5ab44053d1a1c8757bbe
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75645247"
---
# <a name="sfctl"></a>sfctl
Comandos para gerenciar Service Fabric clusters e entidades. Esta versão é compatível com o tempo de execução do Service Fabric 6,5.

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
| [property](service-fabric-sfctl-property.md) | Armazenar e consultar Propriedades em nomes de Service Fabric. |
| [replica](service-fabric-sfctl-replica.md) | Gerencie as réplicas que pertencem a partições de serviço. |
| [rpm](service-fabric-sfctl-rpm.md) | Consultar e enviar comandos para o serviço do Gerenciador de reparo. |
| [sa-cluster](service-fabric-sfctl-sa-cluster.md) | Gerenciar clusters Service Fabric autônomos. |
| [service](service-fabric-sfctl-service.md) | Criar, excluir e gerenciar serviço, tipos de serviço e pacotes de serviço. |
| [settings](service-fabric-sfctl-settings.md) | Defina as configurações local para essa instância do sfctl. |
| [store](service-fabric-sfctl-store.md) | Execute operações básicas de nível de arquivo no repositório de imagens do cluster. |

## <a name="next-steps"></a>Passos seguintes
- [Configure](service-fabric-cli.md) a CLI do Service Fabric.
- Saiba como usar a CLI do Service Fabric usando os [scripts de exemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).