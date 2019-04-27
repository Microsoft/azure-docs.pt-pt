---
title: Monitorização e diagnóstico em aplicativos de malha do Azure Service Fabric | Documentos da Microsoft
description: Saiba mais sobre monitorização e diagnóstico de aplicação no Service Fabric malha no Azure.
services: service-fabric-mesh
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/22/2018
ms.author: srrengar
ms.custom: mvc, devcenter
ms.openlocfilehash: 6166808c85bbee9465a8fa12332afe2163027982
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60810612"
---
# <a name="monitoring-and-diagnostics"></a>Monitorização e diagnóstico
O Azure Service Fabric Mesh é um serviço totalmente gerido que permite aos programadores implementar aplicações de microsserviços sem gerir máquinas virtuais, armazenamento ou redes. Monitorização e diagnóstico para a malha de recursos de infraestrutura do serviço é categorizado em três tipos principais de dados de diagnóstico:

- Registos de aplicações - estes são definidos como os registos das suas aplicações em contentores, com base na forma como tiver instrumentado a sua aplicação (por exemplo, registos do docker)
- Eventos de plataforma - eventos a partir da plataforma de malha relevante para a operação de contentor, incluindo atualmente ativação de contentor, a desativação e cessação.
- Métricas de contentor - métricas de desempenho e a utilização de recursos para os seus contentores (estatísticas de docker)

Este artigo discute as opções de monitorização e diagnóstico para a versão de pré-visualização mais recente disponível.

## <a name="application-logs"></a>Registos de aplicações

Pode ver os registos de docker dos contentores implementados, numa base por contentor. No modelo de aplicativo de malha do Service Fabric, cada contentor é um pacote de código em seu aplicativo. Para ver os registos associados com um pacote de código, utilize o seguinte comando:

```cli
az mesh code-package-log get --resource-group <nameOfRG> --app-name <nameOfApp> --service-name <nameOfService> --replica-name <nameOfReplica> --code-package-name <nameOfCodePackage>
```

> [!NOTE]
> Pode utilizar o comando "az mesh réplica service" para obter o nome de réplica. Nomes de réplica são incrementando números de 0.*

Eis o que isso é semelhante, para ver os registos do contêiner VotingWeb.Code da aplicação de voto:

```cli
az mesh code-package-log get --resource-group <nameOfRG> --application-name SbzVoting --service-name VotingWeb --replica-name 0 --code-package-name VotingWeb.Code
```

## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre a malha de recursos de infraestrutura do serviço, leia a visão geral:
- [Descrição geral de malha do Service Fabric](service-fabric-mesh-overview.md)
