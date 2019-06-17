---
title: Reconfiguração nos recursos de infraestrutura do serviço do Azure | Documentos da Microsoft
description: Compreender a reconfiguração das partições no Service Fabric
services: service-fabric
documentationcenter: .net
author: appi101
manager: anuragg
editor: ''
ms.assetid: d5ab75ff-98b9-4573-a2e5-7f5ab288157a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/10/2018
ms.author: aprameyr
ms.openlocfilehash: a24aa6aa1695a3d1166816b7960bdd7b551e1a37
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60882202"
---
# <a name="reconfiguration-in-azure-service-fabric"></a>Reconfiguração nos recursos de infraestrutura do serviço do Azure
R *configuração* é definido como as réplicas e as respetivas funções para uma partição de serviço com estado.

R *reconfiguração* é o processo de mover uma configuração para outra configuração. Faz uma alteração para a réplica definido para uma partição de serviço com estado. Denomina-se a antiga configuração do *configuração anterior (PC)* , e a nova configuração é chamada o *configuração atual (CC)* . O protocolo de reconfiguração no Azure Service Fabric preserva a consistência e mantém a disponibilidade durante todas as alterações ao conjunto de réplicas.

Gestor de ativação pós-falha inicia Reconfigurações em resposta a eventos diferentes no sistema. Por exemplo, se o principal falhar, em seguida, uma reconfiguração é iniciada para promover um secundário ativo para um site primário. Outro exemplo é em resposta às atualizações de aplicações quando poderá ser necessário mover o primário para outro nó para atualizar o nó.

## <a name="reconfiguration-types"></a>Tipos de reconfiguração
Reconfigurações podem ser classificadas em dois tipos:

- Reconfigurações onde está a mudar o principal:
    - **Ativação pós-falha**: As ativações pós-falha são Reconfigurações em resposta a falha de uma principal de execução.
    - **SwapPrimary**: Trocas são Reconfigurações onde o Service Fabric tem de mover uma execução principal de um nó para outro, normalmente em resposta ao balanceamento de carga ou uma atualização.

- Reconfigurações em que o principal não está a mudar.

## <a name="reconfiguration-phases"></a>Fases de reconfiguração
Uma reconfiguração procede de várias fases:

- **Phase0**: Esta fase ocorre no Reconfigurações de comutação primário em que o primário atual transfere o seu estado para a nova principal e transições para o secundário ativo.

- **Phase1**: Esta fase ocorre durante a Reconfigurações em que o principal está mudando. Durante esta fase, o Service Fabric identifica o primário correto entre as réplicas atuais. Nesta fase não é necessário durante a troca primário Reconfigurações porque a nova principal já foi escolhida. 

- **Phase2**: Durante esta fase, o Service Fabric garante que todos os dados estão disponíveis na maioria das réplicas da configuração atual.

Há várias outras fases que são apenas para utilização interna.

## <a name="stuck-reconfigurations"></a>Reconfigurações bloqueadas
Podem obter Reconfigurações *preso* por uma série de motivos. Algumas das razões comuns incluem:

- **Diminuir réplicas**: Alguns fases de reconfiguração requerem a maioria das réplicas na configuração ficar operacional.
- **Problemas de rede ou a comunicação**: Reconfigurações requerem conectividade de rede entre nós diferentes.
- **Falhas de API**: O protocolo de reconfiguração exige que as implementações de serviço concluir determinados APIs. Por exemplo, não respeitar o token de cancelamento num serviço fiável faz com que SwapPrimary Reconfigurações fique preso.

Utilizar relatórios de estado de funcionamento dos componentes de sistema, como System.FM, System.RA e System diagnosticar onde uma reconfiguração está bloqueada. O [página de relatório de estado de funcionamento do sistema](service-fabric-understand-and-troubleshoot-with-system-health-reports.md) descreve estes relatórios de estado de funcionamento.

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre conceitos do Service Fabric, consulte os artigos seguintes:

- [Ciclo de vida do Reliable Services - C#](service-fabric-reliable-services-lifecycle.md)
- [Relatórios de estado de funcionamento do sistema](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
- [Réplicas e instâncias](service-fabric-concepts-replica-lifecycle.md)
