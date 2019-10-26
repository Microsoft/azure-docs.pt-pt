---
title: CLI do Azure Service Fabric-sfctl | Microsoft Docs
description: Descreve os comandos de sfctl da CLI do Service Fabric.
services: service-fabric
documentationcenter: na
author: jeffj6123
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: 7e7fc7bbc65e92960d7839f6531ef1f7c1935ed3
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900887"
---
# <a name="sfctl"></a>sfctl
Comandos para gerenciar Service Fabric clusters e entidades. Esta versão é compatível com o tempo de execução do Service Fabric 6,5.

Os comandos seguem o padrão substantivo-Verb. Consulte subgrupos para obter mais informações.

## <a name="subgroups"></a>Subgrupos
|Subgrupo|Descrição|
| --- | --- |
| [aplicativo](service-fabric-sfctl-application.md) | Criar, excluir e gerenciar aplicativos e tipos de aplicativos. |
| [Chaos](service-fabric-sfctl-chaos.md) | Inicie, pare e relate o serviço de teste de caos. |
| [em](service-fabric-sfctl-cluster.md) | Selecione, gerencie e opere Service Fabric clusters. |
| [Compose](service-fabric-sfctl-compose.md) | Crie, exclua e gerencie aplicativos Docker Compose. |
| [Container](service-fabric-sfctl-container.md) | Execute comandos relacionados ao contêiner em um nó de cluster. |
| [LostFocus](service-fabric-sfctl-events.md) | Recupere eventos do repositório de eventos (se o serviço EventStore já estiver instalado). |
| [for](service-fabric-sfctl-is.md) | Consultar e enviar comandos para o serviço de infraestrutura. |
| [mesh](service-fabric-sfctl-mesh.md) | Exclua e gerencie aplicativos de malha Service Fabric. |
| [nó](service-fabric-sfctl-node.md) | Gerencie os nós que formam um cluster. |
| [particion](service-fabric-sfctl-partition.md) | Consultar e gerenciar partições para qualquer serviço. |
| [Propriedade](service-fabric-sfctl-property.md) | Armazenar e consultar Propriedades em nomes de Service Fabric. |
| [Replica](service-fabric-sfctl-replica.md) | Gerencie as réplicas que pertencem a partições de serviço. |
| [rpm](service-fabric-sfctl-rpm.md) | Consultar e enviar comandos para o serviço do Gerenciador de reparo. |
| [SA-cluster](service-fabric-sfctl-sa-cluster.md) | Gerenciar clusters Service Fabric autônomos. |
| [serviço](service-fabric-sfctl-service.md) | Criar, excluir e gerenciar serviço, tipos de serviço e pacotes de serviço. |
| [Configurações](service-fabric-sfctl-settings.md) | Defina as configurações local para essa instância do sfctl. |
| [armazenadas](service-fabric-sfctl-store.md) | Execute operações básicas de nível de arquivo no repositório de imagens do cluster. |

## <a name="next-steps"></a>Passos seguintes
- [Configure](service-fabric-cli.md) a CLI do Service Fabric.
- Saiba como usar a CLI do Service Fabric usando os [scripts de exemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).