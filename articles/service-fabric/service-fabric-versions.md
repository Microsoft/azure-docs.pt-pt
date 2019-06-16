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
ms.date: 06/10/2019
ms.author: aljo
ms.openlocfilehash: 8a548bce6da8b3bae004b0be4f6e8f09c9894025
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67060354"
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
| 6.4.* | 6.2.301.* |Menor ou igual a versão 3.3 |15 de Setembro de 2019 |
| 6.5.* | 6.4.617.* |Menor ou igual a versão 3.4 |Versão atual, por isso, sem data de fim |

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

## <a name="supported-version-names"></a>Nomes de versão suportada

A tabela seguinte lista os nomes de versão de Service Fabric e seus números de versão correspondente.

| Nome da versão | Número de versão do Windows | Número de versão do Linux |
| --- | --- | --- |
| 5.3 RTO | 5.3.121.9494 | N/D |
| 5.3 CU1 | 5.3.204.9494 | N/D |
| 5.3 CU2 | 5.3.301.9590 | N/D |
| 5.3 CU3 | 5.3.311.9590 | N/D |
| 5.4 CU2 | 5.4.164.9494 | N/D |
| 5.5 CU1 | 5.5.216.0    | N/D |
| 5.5 CU2 | 5.5.219.0    | N/D |
| 5.5 CU3 | 5.5.227.0    | N/D |
| 5.5 CU4 | 5.5.232.0    | N/D |
| 5.6 RTO | 5.6.204.9494 | N/D |
| 5.6 CU2 | 5.6.210.9494 | N/D |
| 5.6 CU3 | 5.6.220.9494 | N/D |
| 5.7 RTO | 5.7.198.9494 | N/D |
| 5.7 CU4 | 5.7.221.9494 | N/D |
| 6.0 RTO | 6.0.211.9494 | 6.0.120.1 |
| 6.0 CU1 | 6.0.219.9494 | 6.0.127.1 |
| 6.0 CU2 | 6.0.232.9494 | 6.0.133.1 |
| 6.1 CU1 | 6.1.456.9494 | 6.1.183.1 |
| 6.1 CU2 | 6.1.467.9494 | 6.1.185.1 |
| 6.1 CU3 | 6.1.472.9494 | N/D |
| 6.1 CU4 | 6.1.480.9494 | 6.1.187.1 |
| 6.2 RTO | 6.2.269.9494 | 6.2.184.1 | 
| 6.2 CU1 | 6.2.274.9494 | 6.2.191.1 |
| 6.2 CU2 | 6.2.283.9494 | 6.2.194.1 |
| 6.2 CU3 | 6.2.301.9494 | 6.2.199.1 |
| 6.3 RTO | 6.3.162.9494 | 6.3.119.1 |
| 6.3 CU1 | 6.3.176.9494 | 6.3.124.1 |
| 6.3 CU1 | 6.3.187.9494 | 6.3.129.1 |
| 6.4 RTO | 6.4.617.9590 | 6.4.625.1 |
| 6.4 CU2 | 6.4.622.9590 | N/D |
| 6.4 CU3 | 6.4.637.9590 | 6.4.634.1 |
| 6.4 CU4 | 6.4.644.9590 | 6.4.639.1 |
| 6.4 CU5 | 6.4.654.9590 | 6.4.649.1 |
| 6.4 CU6 | 6.4.658.9590 | N/D |
| 6.4 CU7 | 6.4.664.9590 | 6.4.661.1 |
| 6.5 RTO | 6.5.639.9590 | 6.5.435.1 |