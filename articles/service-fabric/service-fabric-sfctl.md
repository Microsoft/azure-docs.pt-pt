---
title: CLI do Azure Service Fabric-sfctl | Microsoft Docs
description: Descreve os comandos de sfctl da CLI do Service Fabric.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: 35b881268ca21a840836c96388a4562a54d17d3b
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035807"
---
# <a name="sfctl"></a>sfctl
Comandos para gerenciar Service Fabric clusters e entidades. Esta versão é compatível com o tempo de execução do Service Fabric 6,4.

Os comandos seguem o padrão substantivo-Verb. Consulte subgrupos para obter mais informações.

## <a name="subgroups"></a>Subgrupos
|Subgrupo|Descrição|
| --- | --- |
| [application](service-fabric-sfctl-application.md) | Criar, excluir e gerenciar aplicativos e tipos de aplicativos. |
| [chaos](service-fabric-sfctl-chaos.md) | Inicie, pare e relate o serviço de teste de caos. |
| [cluster](service-fabric-sfctl-cluster.md) | Selecione, gerencie e opere Service Fabric clusters. |
| [compose](service-fabric-sfctl-compose.md) | Crie, exclua e gerencie aplicativos Docker Compose. |
| [container](service-fabric-sfctl-container.md) | Execute comandos relacionados ao contêiner em um nó de cluster. |
| [is](service-fabric-sfctl-is.md) | Consultar e enviar comandos para o serviço de infraestrutura. |
| [mesh](service-fabric-sfctl-mesh.md) | Exclua e gerencie aplicativos de malha Service Fabric. |
| [nó](service-fabric-sfctl-node.md) | Gerencie os nós que formam um cluster. |
| [partition](service-fabric-sfctl-partition.md) | Consultar e gerenciar partições para qualquer serviço. |
| [Propriedade](service-fabric-sfctl-property.md) | Armazenar e consultar Propriedades em nomes de Service Fabric. |
| [replica](service-fabric-sfctl-replica.md) | Gerencie as réplicas que pertencem a partições de serviço. |
| [rpm](service-fabric-sfctl-rpm.md) | Consultar e enviar comandos para o serviço do Gerenciador de reparo. |
| [sa-cluster](service-fabric-sfctl-sa-cluster.md) | Gerenciar clusters Service Fabric autônomos. |
| [service](service-fabric-sfctl-service.md) | Criar, excluir e gerenciar serviço, tipos de serviço e pacotes de serviço. |
| [settings](service-fabric-sfctl-settings.md) | Defina as configurações local para essa instância do sfctl. |
| [store](service-fabric-sfctl-store.md) | Execute operações básicas de nível de arquivo no repositório de imagens do cluster. |

## <a name="next-steps"></a>Passos seguintes
- [Configure](service-fabric-cli.md) a CLI do Service Fabric.
- Saiba como usar a CLI do Service Fabric usando os [scripts de exemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).