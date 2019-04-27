---
title: Suporte a versões de cluster no Azure Service Fabric | Documentos da Microsoft
description: Saiba mais sobre as versões do cluster no Azure Service Fabric.
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
ms.openlocfilehash: 75e95737eecb9407a80103d1cad00d4987fe7091
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60716086"
---
# <a name="supported-service-fabric-versions"></a>Versões suportadas do Service Fabric

Certificar-se de que o cluster está sempre em execução uma versão suportada do Azure Service Fabric. Termina a um mínimo de 60 dias após a iremos anunciar o lançamento de uma nova versão do Service Fabric, suporte para a versão anterior. Encontrará anúncios de novas versões do [blogue da equipa do Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/).

Consulte os seguintes documentos para obter detalhes sobre como manter o seu cluster executar uma versão suportada do Service Fabric:

- [Atualizar um cluster do Azure Service Fabric](service-fabric-cluster-upgrade.md)
- [Atualizar a versão de Service Fabric que é executado no seu cluster do Windows Server autónomo](service-fabric-cluster-upgrade-windows-server.md)

## <a name="supported-versions"></a>Versões suportadas

A tabela seguinte lista as versões do Service Fabric e respetivas datas de fim do suporte.

| Runtime do Service Fabric no cluster | Pode atualizar diretamente a partir da versão do cluster |Versão de pacote SDK ou NuGet compatível | Fim do suporte |
| --- | --- |--- | --- |
| Todas as versões de cluster antes de 5.3.121 | 5.1.158.* |Menor ou igual a versão 2.3 |20 de Janeiro de 2017 |
| 5.3.* | 5.1.158.* |Menor ou igual a versão 2.3 |24 de Fevereiro de 2017 |
| 5.4.* | 5.1.158.* |Menor ou igual a versão 2.4 |10 de maio de 2017       |
| 5.5.* | 5.4.164.* |Menor ou igual a versão 2.5 |Agosto 10,2017    |
| 5.6.* | 5.4.164.* |Menor ou igual a versão 2.6 |Outubro 13,2017   |
| 5.7.* | 5.4.164.* |Menor ou igual a versão 2.7 |15 de Dezembro de 2017  |
| 6.0.* | 5.6.205.* |Menor ou igual a versão 2.8 |30 de Março de 2018     |
| 6.1.* | 5.7.221.* |Menor ou igual a versão 3.0 |15 de julho de 2018      |
| 6.2.* | 6.0.232.* |Menor ou igual à versão 3.1 |26 de Outubro de 2018   |
| 6.3.* | 6.1.480.* |Menor ou igual à versão 3.2 |31 de Março de 2019  |
| 6.4.* | 6.2.301.* |Menor ou igual a versão 3.3 |Versão atual, por isso, sem data de fim |

## <a name="supported-operating-systems"></a>Sistemas operativos suportados

A tabela seguinte lista os sistemas operativos suportados para as versões suportadas do Service Fabric.

| Sistema operativo | Mais antiga versão suportada do Service Fabric |
| --- | --- |
| Windows Server 2012 R2 | Todas as versões |
| Windows Server 2016 | Todas as versões |
| Windows Server 1709 | 6.0 |
| Windows Server versão 1803 | 6.4 |
| Windows Server 1809 | 6.4.654.9590 |
| Windows Server 2019 | 6.4.654.9590 |
| Linux Ubuntu 16.04 | 6.0 |

