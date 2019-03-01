---
title: Saiba mais sobre as versões do cluster do Azure Service Fabric | Documentos da Microsoft
description: Versões suportadas do cluster do Azure Service Fabric
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chakdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 9/24/2018
ms.author: aljo
ms.openlocfilehash: de5522e68d1329ce2b80a4d3c7045d38c13169e5
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57191652"
---
# <a name="supported-service-fabric-versions"></a>Versões suportadas do Service Fabric

Certifique-se de que o cluster está sempre em execução uma versão suportada do Service Fabric. Como e quando podemos anunciar o lançamento de uma nova versão do Service Fabric, a versão anterior está marcada para o fim do suporte após um mínimo de 60 dias dessa data. As novas versões sejam anunciadas [no blog da equipe de recursos de infraestrutura do serviço](https://blogs.msdn.microsoft.com/azureservicefabric/).

Consulte os seguintes documentos em detalhes sobre como manter o seu cluster executar uma versão suportada do Service Fabric.

- [Atualizar a versão do Service Fabric num cluster do Azure](service-fabric-cluster-upgrade.md)
- [Atualizar a versão do Service Fabric num cluster de servidores windows autónomo](service-fabric-cluster-upgrade-windows-server.md)

Seguem-se a lista das versões de Service Fabric que são suportadas e respetivas datas de fim do suporte.

| **Runtime do Service Fabric no cluster** | **Pode atualizar diretamente a partir da versão do cluster** |**SDK compatível / versões de pacote de NuGet** | **Fim do suporte** |
| --- | --- |--- | --- |
| Todas as versões de cluster antes de 5.3.121 | 5.1.158* |Menor ou igual a versão 2.3 |20 de Janeiro de 2017 |
| 5.3.* | 5.1.158.* |Menor ou igual a versão 2.3 |24 de Fevereiro de 2017 |
| 5.4.* | 5.1.158.* |Menor ou igual a versão 2.4 |Pode 10,2017       |
| 5.5.* | 5.4.164.* |Menor ou igual a versão 2.5 |Agosto 10,2017    |
| 5.6.* | 5.4.164.* |Menor ou igual a versão 2.6 |Outubro 13,2017   |
| 5.7.* | 5.4.164.* |Menor ou igual a versão 2.7 |Dezembro 15,2017  |
| 6.0.* | 5.6.205.* |Menor ou igual a versão 2.8 |Março de 30,2018     |
| 6.1.* | 5.7.221.* |Menor ou igual a versão 3.0 |Julho 15,2018      |
| 6.2.* | 6.0.232.* |Menor ou igual à versão 3.1 |Outubro 26,2018   |
| 6.3.* | 6.1.480.* |Menor ou igual à versão 3.2 |Março de 31,2019  |
| 6.4.* | 6.2.301.* |Menor ou igual a versão 3.3 |Versão atual e por isso, sem data de fim |