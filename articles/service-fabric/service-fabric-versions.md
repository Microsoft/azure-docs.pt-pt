---
title: Versões de cluster suportadas em Tecido de Serviço Azure
description: Conheça as versões de cluster no Azure Service Fabric, incluindo um link para os mais recentes lançamentos do blog da equipa do Service Fabric.
ms.topic: troubleshooting
ms.date: 06/15/2020
ms.openlocfilehash: 4569b31f990687fb9658d2bf251045e27ecf91a2
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92480961"
---
# <a name="supported-service-fabric-versions"></a>Versões de tecido de serviço suportado

Certifique-se de que o seu cluster está sempre a executar uma versão suportada do Azure Service Fabric. No mínimo 60 dias após anunciarmos o lançamento de uma nova versão do Service Fabric, o suporte para a versão anterior termina. Encontrará anúncios de novos lançamentos no blog da [equipa de Service Fabric.](https://azure.microsoft.com/updates/?product=service-fabric)

Para uma determinada versão do tempo de funcionação do Tecido de Serviço, pode utilizar as versões especificadas ou mais antigas dos pacotes SDK/NuGet. As versões mais recentes dos pacotes não são suportadas e podem ter problemas direcionados para clusters mais antigos, uma vez que podem ter alterações de funcionalidades ou protocolos não suportados por esses ambientes.

Consulte os seguintes documentos para obter mais informações sobre como manter o seu cluster a executar uma versão suportada do Service Fabric:

- [Atualize um cluster de tecido de serviço Azure](service-fabric-cluster-upgrade.md)
- [Atualize a versão 'Service Fabric' que funciona no seu cluster de servidores do Windows autónomo](service-fabric-cluster-upgrade-windows-server.md)

## <a name="supported-versions"></a>Versões suportadas

A tabela que se segue lista as versões do Service Fabric e as datas finais de suporte.

| Tempo de execução do tecido de serviço no cluster | Pode atualizar diretamente a partir da versão cluster |Versão compatível do pacote SDK ou NuGet | Fim do apoio |
| --- | --- |--- | --- |
| Todas as versões do cluster antes de 5.3.121 | 5.1.158.* |Menos ou igual à versão 2.3 |20 de janeiro de 2017 |
| 5.3.* | 5.1.158.* |Menos ou igual à versão 2.3 |24 de fevereiro de 2017 |
| 5.4.* | 5.1.158.* |Menos ou igual à versão 2.4 |10 de maio de 2017       |
| 5.5.* | 5.4.164.* |Menos ou igual à versão 2.5 |10 de agosto de 2017    |
| 5.6.* | 5.4.164.* |Menos ou igual à versão 2.6 |13 de outubro de 2017   |
| 5.7.* | 5.4.164.* |Menos ou igual à versão 2.7 |15 de dezembro de 2017  |
| 6.0.* | 5.6.205.* |Menos ou igual à versão 2.8 |30 de março de 2018     |
| 6.1.* | 5.7.221.* |Menos ou igual à versão 3.0 |15 de julho de 2018      |
| 6.2.* | 6.0.232.* |Menos ou igual à versão 3.1 |26 de outubro de 2018   |
| 6.3.* | 6.1.480.* |Menos ou igual à versão 3.2 |31 de março de 2019  |
| 6.4.* | 6.2.301.* |Menos ou igual à versão 3.3 |15 de setembro de 2019 |
| 6.5.* | 6.4.617.* |Menos ou igual à versão 3.4 |1 de agosto de 2020 |
| 7.0.466.* | 6.4.664.* |Menos ou igual à versão 4.0|31 de janeiro de 2021  |
| 7.0.466.* | 6.5.* |Menos ou igual à versão 4.0|31 de janeiro de 2021 |
| 7.0.470.* | 7.0.466.* |Menos ou igual à versão 4.0 |31 de janeiro de 2021  |
| 7.0.472.* | 7.0.466.* |Menos ou igual à versão 4.0 |31 de janeiro de 2021  |
| 7.0.478.* | 7.0.466.* |Menos ou igual à versão 4.0 |31 de janeiro de 2021  |
| 7.1.409.* | 7.0.466.* |Menos ou igual à versão 4.1 |31 de março de 2021 |
| 7.1.417.* | 7.0.466.* |Menos ou igual à versão 4.1 |31 de março de 2021 |
| 7.1.428.* | 7.0.466.* |Menos ou igual à versão 4.1 |31 de março de 2021 |
| 7.1.456.* | 7.0.466.* |Menos ou igual à versão 4.1 |31 de março de 2021 |
| 7.1.458.* | 7.0.466.* |Menos ou igual à versão 4.1 |31 de março de 2021 |
| 7.1.459.* | 7.0.466.* |Menos ou igual à versão 4.1 |31 de março de 2021 |
| 7.2.413.* | 7.0.470.* |Menos ou igual à versão 4.2 |Versão atual, então sem data de fim |

## <a name="supported-operating-systems"></a>Sistemas operativos suportados

A tabela que se segue lista os sistemas operativos suportados para as versões suportadas do Service Fabric.

| Sistema operativo | Primeira versão suportada do Tecido de Serviço |
| --- | --- |
| Windows Server 2012 R2 | Todas as versões |
| Windows Server 2016 | Todas as versões |
| Servidor Windows 1709 | 6,0 |
| Servidor Windows 1803 | 6.4 |
| Windows Server 1809 | 6.4.654.9590 |
| Windows Server 2019 | 6.4.654.9590 |
| Linux Ubuntu 16.04 | 6,0 |
| Linux Ubuntu 18.04 | 7.1 |

## <a name="supported-version-names"></a>Nomes de versão suportados

A tabela que se segue lista os nomes da versão do Service Fabric e os respetivos números de versão.

| Nome da versão | Número da versão do Windows | Número da versão Linux |
| --- | --- | --- |
| 5.3 RTO | 5.3.121.9494 | ND |
| 5.3 CU1 | 5.3.204.9494 | ND |
| 5.3 CU2 | 5.3.301.9590 | ND |
| 5.3 CU3 | 5.3.311.9590 | ND |
| 5.4 CU2 | 5.4.164.9494 | ND |
| 5.5 CU1 | 5.5.216.0    | ND |
| 5.5 CU2 | 5.5.219.0    | ND |
| 5.5 CU3 | 5.5.227.0    | ND |
| 5.5 CU4 | 5.5.232.0    | ND |
| 5.6 RTO | 5.6.204.9494 | ND |
| 5.6 CU2 | 5.6.210.9494 | ND |
| 5.6 CU3 | 5.6.220.9494 | ND |
| 5.7 RTO | 5.7.198.9494 | ND |
| 5.7 CU4 | 5.7.221.9494 | ND |
| 6.0 RTO | 6.0.211.9494 | 6.0.120.1 |
| 6.0 CU1 | 6.0.219.9494 | 6.0.127.1 |
| 6.0 CU2 | 6.0.232.9494 | 6.0.133.1 |
| 6.1 CU1 | 6.1.456.9494 | 6.1.183.1 |
| 6.1 CU2 | 6.1.467.9494 | 6.1.185.1 |
| 6.1 CU3 | 6.1.472.9494 | ND |
| 6.1 CU4 | 6.1.480.9494 | 6.1.187.1 |
| 6.2 RTO | 6.2.269.9494 | 6.2.184.1 | 
| 6.2 CU1 | 6.2.274.9494 | 6.2.191.1 |
| 6.2 CU2 | 6.2.283.9494 | 6.2.194.1 |
| 6.2 CU3 | 6.2.301.9494 | 6.2.199.1 |
| 6.3 RTO | 6.3.162.9494 | 6.3.119.1 |
| 6.3 CU1 | 6.3.176.9494 | 6.3.124.1 |
| 6.3 CU1 | 6.3.187.9494 | 6.3.129.1 |
| 6.4 RTO | 6.4.617.9590 | 6.4.625.1 |
| 6.4 CU2 | 6.4.622.9590 | ND |
| 6.4 CU3 | 6.4.637.9590 | 6.4.634.1 |
| 6.4 CU4 | 6.4.644.9590 | 6.4.639.1 |
| 6.4 CU5 | 6.4.654.9590 | 6.4.649.1 |
| 6.4 CU6 | 6.4.658.9590 | ND |
| 6.4 CU7 | 6.4.664.9590 | 6.4.661.1 |
| 6.4 CU8 | 6.4.670.9590 | ND |
| 6.5 RTO | 6.5.639.9590 | 6.5.435.1 |
| 6.5 CU1 | 6.5.641.9590 | 6.5.454.1 |
| 6,5 CU2 | 6.5.658.9590 | 6.5.460.1 |
| 6.5 CU3 | 6.5.664.9590 | 6.5.466.1 |
| 6,5 CU5 | 6.5.676.9590 | 6.5.467.1 |
| 7.0 RTO | 7.0.457.9590 | 7.0.457.1 |
| 7.0 CU2 | 7.0.464.9590 | 7.0.464.1 |
| 7.0 CU3 | 7.0.466.9590 | 7.0.465.1 |
| 7.0 CU4 | 7.0.470.9590 | 7.0.469.1 |
| 7.0 CU6 | 7.0.472.9590 | 7.0.471.1 |
| 7.0 CU9 | 7.0.478.9590 | 7.0.472.1 |
| 7.1 RTO | 7.1.409.9590 | 7.1.410.1 |
| 7.1 CU1 | 7.1.417.9590 | 7.1.418.1 |
| 7.1 CU2 | 7.1.428.9590 | 7.1.428.1 |
| 7.1 CU3 | 7.1.456.9590 | 7.1.452.1 |
| 7.1 CU5 | 7.1.458.9590 | 7.1.454.1 |
| 7.1 CU6 | 7.1.459.9590 | 7.1.455.1 |
| 7.2 RTO | 7.2.413.9590 | ND |

