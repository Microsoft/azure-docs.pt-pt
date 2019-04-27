---
title: O Azure Service Fabric CLI - sfctl | Documentos da Microsoft
description: Descreve os comandos do CLI do Service Fabric sfctl.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: c195d0c4250022102e735cf584370278e354bc41
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60545049"
---
# <a name="sfctl"></a>sfctl
Comandos para a gestão de clusters do Service Fabric e entidades. Esta versão é compatível com o tempo de execução do Service Fabric 6.4.

Os comandos seguem o padrão de verbo-substantivo. Ver subgrupos para obter mais informações.

## <a name="subgroups"></a>Subgrupos
|Subgrupo|Descrição|
| --- | --- |
| [application](service-fabric-sfctl-application.md) | Criar, eliminar e gerir aplicações e tipos de aplicativos. |
| [chaos](service-fabric-sfctl-chaos.md) | Iniciar, parar e gerar relatórios sobre o serviço de teste caos. |
| [cluster](service-fabric-sfctl-cluster.md) | Selecione, gerir e operar a clusters do Service Fabric. |
| [compose](service-fabric-sfctl-compose.md) | Criar, eliminar e gerir aplicações do Docker Compose. |
| [container](service-fabric-sfctl-container.md) | Contentor de execução relacionados com os comandos num nó de cluster. |
| [is](service-fabric-sfctl-is.md) | Consultar e enviar comandos para o serviço de infraestrutura. |
| [mesh](service-fabric-sfctl-mesh.md) | Eliminar e gerir aplicações de Mesh do Service Fabric. |
| [node](service-fabric-sfctl-node.md) | Gerir os nós que formam um cluster. |
| [partition](service-fabric-sfctl-partition.md) | Consultar e gerir partições de qualquer serviço. |
| [property](service-fabric-sfctl-property.md) | Propriedades de consulta e Store em nomes do Service Fabric. |
| [replica](service-fabric-sfctl-replica.md) | Gerir as réplicas que pertencem a partições de serviço. |
| [rpm](service-fabric-sfctl-rpm.md) | Consultar e enviar comandos para o serviço de Gestor de reparação. |
| [sa-cluster](service-fabric-sfctl-sa-cluster.md) | Gerir clusters autónomos do Service Fabric. |
| [service](service-fabric-sfctl-service.md) | Criar, eliminar e gerir o serviço, os tipos de serviço e pacotes de serviço. |
| [settings](service-fabric-sfctl-settings.md) | Configure as definições locais para esta instância de sfctl. |
| [store](service-fabric-sfctl-store.md) | Efetue operações de nível de arquivo básico no armazenamento de imagens do cluster. |

## <a name="next-steps"></a>Passos Seguintes
- [Configurar](service-fabric-cli.md) a CLI do Service Fabric.
- Saiba como utilizar a CLI do Service Fabric utilizando o [scripts de exemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).