---
title: Diferenças entre serviços em nuvem e o Service Fabric | Documentos da Microsoft
description: Uma descrição geral conceptual para migrar aplicações dos serviços Cloud para o Service Fabric.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 0b87b1d3-88ad-4658-a465-9f05a3376dee
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 8b486e617389e1611dfebf3d347d2d64df088593
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66258642"
---
# <a name="learn-about-the-differences-between-cloud-services-and-service-fabric-before-migrating-applications"></a>Saiba mais sobre as diferenças entre serviços em nuvem e o Service Fabric antes de migrar aplicações.
Microsoft Azure Service Fabric é a plataforma de aplicativos de nuvem de última geração para aplicações distribuídas altamente escalonáveis, altamente confiáveis. Ele introduz muitas funcionalidades novas para o empacotamento, implementar, atualizar e gerir aplicações na cloud distribuídas. 

Este é um guia introdutório para migrar aplicações dos serviços Cloud para o Service Fabric. Ele se concentra principalmente na arquitetura e diferenças de design entre serviços em nuvem e o Service Fabric.

## <a name="applications-and-infrastructure"></a>Aplicações e infraestrutura
Uma diferença fundamental entre serviços em nuvem e o Service Fabric é a relação entre as VMs, cargas de trabalho e aplicações. Uma carga de trabalho aqui é definida como o código de escrita para executar uma tarefa específica ou fornecer um serviço.

* **Serviços cloud é sobre a implementação de aplicações que as VMs.** O código de escrita é rigidamente acoplado a uma instância VM, como uma função de trabalho ou Web. Para implementar uma carga de trabalho nos serviços Cloud é implementar um ou mais instâncias VM que executam a carga de trabalho. Não existe nenhuma separação de aplicativos e as VMs e, portanto, não existe nenhuma definição formal de uma aplicação. Um aplicativo pode ser considerado como um conjunto de instâncias de função de trabalho ou Web dentro de uma implantação de serviços em nuvem ou como toda a implementação de serviços Cloud. Neste exemplo, um aplicativo é mostrado como um conjunto de instâncias de função.

![Aplicações de serviços na cloud e a topologia][1]

* **O Service Fabric é sobre a implementação de aplicações para as VMs existentes ou computadores que executam o Service Fabric no Windows ou Linux.** Os serviços que escreve são totalmente separados da infraestrutura subjacente, que é abstraída pela plataforma de aplicação do Service Fabric, para que um aplicativo pode ser implementado em vários ambientes. Uma carga de trabalho no Service Fabric é chamada de "serviço" e um ou mais serviços são agrupados num aplicativo definido formalmente, que é executada na plataforma de aplicação do Service Fabric. Vários aplicativos podem ser implementados para um único cluster de Service Fabric.

![Topologia e de aplicações do Service Fabric][2]

Service Fabric em si é uma camada de plataforma de aplicativo que é executado no Windows ou Linux, ao passo que os serviços em nuvem é um sistema para a implementação de VMs geridas do Azure com cargas de trabalho anexadas.
O modelo de aplicação do Service Fabric tem várias vantagens:

* Tempos de implementação rápida. Criação de instâncias VM pode ser demorado. No Service Fabric, os VMs são implementadas apenas uma vez para formar um cluster que aloja a plataforma de aplicações do Service Fabric. Daí em diante, pacotes de aplicações podem ser implementados no cluster muito rapidamente.
* Alojamento de alta densidade. Nos serviços Cloud, uma VM de função de trabalho aloja uma carga de trabalho. No Service Fabric, os aplicativos são separados das VMs que execução-los, que significa que pode implementar um grande número de aplicativos para um pequeno número de VMs, que pode reduzir o custo geral para Implantações maiores.
* Recursos de infraestrutura do serviço de plataforma pode ser executados em qualquer lugar que tem máquinas do Windows Server ou Linux, quer se trate de Azure ou no local. A plataforma fornece uma camada de abstração sobre a infraestrutura subjacente para que seu aplicativo pode ser executado em diferentes ambientes. 
* Gestão de aplicações distribuídas. O Service Fabric é uma plataforma que não apenas aplicações distribuídas de anfitriões, mas também ajuda a gerir o ciclo de vida independentemente da VM de alojamento ou o ciclo de vida da máquina.

## <a name="application-architecture"></a>Arquitetura da aplicação
A arquitetura de um aplicativo de serviços Cloud normalmente inclui várias dependências de serviço externo, como do Service Bus, tabelas do Azure e armazenamento de BLOBs, SQL, Redis e outras pessoas para gerenciar o estado e os dados de um aplicativo e a comunicação entre Web e Funções de trabalho numa implantação de serviços Cloud. Um exemplo de um aplicativo completo de serviços em nuvem pode ser assim:  

![Arquitetura de serviços cloud][9]

Aplicações do Service Fabric também podem optar por utilizar os mesmos serviços externos num aplicativo completo. Com este exemplo de arquitetura de serviços Cloud, é o caminho de migração mais simples dos serviços Cloud para o Service Fabric substituir a implementação de serviços Cloud com uma aplicação do Service Fabric, manter a arquitetura geral da mesma. A Web e funções de trabalho podem ser PORTADAS para serviços sem monitoração de estado do Service Fabric com alterações mínimas ao código.

![Arquitetura do Service Fabric após a migração simple][10]

Nesta fase, o sistema deve continuar a trabalhar com as mesmas de antes. Pode ser internalizado e tirar partido das funcionalidades de com monitoração de estado do Service Fabric, arquivos de estado externo, como com monitoração de estado dos serviços onde for aplicável. Isso é mais envolvido que uma migração simple da Web e funções de trabalho para serviços sem monitoração de estado do Service Fabric, porque requer serviços personalizados que fornecem funcionalidade equivalente à sua aplicação, como os serviços externos fizeram antes de escrever. Os benefícios de se fazer isso: 

* Remover dependências externas 
* Unificando a implantação, gerenciamento e modelos de actualização. 

Uma arquitetura de exemplo resultante de internalizing estes serviços poderia ter esta aparência:

![Arquitetura do Service Fabric após a migração completa][11]

## <a name="communication-and-workflow"></a>Comunicação e o fluxo de trabalho
A maioria dos aplicativos de serviço em nuvem são compostos por mais de uma camada. Da mesma forma, uma aplicação do Service Fabric é constituído por mais de um serviço (normalmente, muitos serviços). Dois modelos comuns de comunicação são comunicação direta e por meio de um armazenamento durável externo.

### <a name="direct-communication"></a>Comunicação direta
Com comunicação direta, as camadas podem comunicar diretamente através de ponto final exposto por cada escalão. Em ambientes sem monitoração de estado, como serviços Cloud, este significa selecionar uma instância de uma função de VM, optar por aleatoriamente ou round robin para carga de saldo e a ligar ao seu ponto de extremidade diretamente.

![Comunicação direta de serviços da cloud][5]

 Comunicação direta é um modelo comum de comunicação no Service Fabric. A principal diferença entre o Service Fabric e serviços em nuvem é que os serviços Cloud em ligar a uma VM, ao passo que o Service Fabric vai ligar a um serviço. Esta é uma distinção importante por dois motivos:

* Serviços no Service Fabric não estão vinculados às VMs que alojam; serviços podem mover-se no cluster e, na verdade, espera-se para mover-se por vários motivos: Balanceamento de recurso, ativação pós-falha, as atualizações de aplicação e a infraestrutura e as restrições de posicionamento ou de carga. Isso significa que o endereço de uma instância de serviço pode alterar em qualquer altura. 
* Uma VM nos recursos de infraestrutura do serviço pode alojar vários serviços, cada um com pontos de extremidade exclusivos.

O Service Fabric fornece um mecanismo de deteção do serviço, chamado o serviço de nomenclatura, que pode ser utilizado para resolver os endereços de ponto de extremidade de serviços. 

![Comunicação direta do Service Fabric][6]

### <a name="queues"></a>Filas
Um mecanismo de comunicação comum entre camadas em ambientes sem monitoração de estado, como os serviços Cloud é usar uma fila de armazenamento externo de maneira duradoura armazenar tarefas de trabalho de um escalão para outro. Um cenário comum é uma camada web que envia tarefas a uma fila do Azure ou do Service Bus em que as instâncias de função de trabalho podem remover da fila e processar as tarefas.

![Comunicação de fila de serviços da cloud][7]

O mesmo modelo de comunicação pode ser utilizado no Service Fabric. Isso pode ser útil ao migrar um aplicativo de serviços Cloud existente para o Service Fabric. 

![Comunicação direta do Service Fabric][8]

## <a name="parity"></a>Paridade
[Serviços cloud são semelhantes ao Service Fabric grau de controlo versus facilidade de uso, mas agora é um serviço legado e o Service Fabric é recomendado para um novo desenvolvimento](https://docs.microsoft.com/azure/app-service/overview-compare); o seguinte é uma comparação de API:


| **API de serviço em nuvem** | **API do Service Fabric** | **Notas** |
| --- | --- | --- |
| RoleInstance.GetID | FabricRuntime.GetNodeContext.NodeId or .NodeName | ID é uma propriedade do NodeName |
| RoleInstance.GetFaultDomain | FabricClient.QueryManager.GetNodeList | Filtrar em NodeName e utilizar a propriedade FD |
| RoleInstance.GetUpgradeDomain | FabricClient.QueryManager.GetNodeList | Filtrar em NodeName e utilize a propriedade de atualização |
| RoleInstance.GetInstanceEndpoints | FabricRuntime.GetActivationContext ou nomenclatura (ResolveService) | CodePackageActivationContext que é fornecido pelo FabricRuntime.GetActivationContext e dentro das réplicas por meio de ServiceInitializationParameters.CodePackageActivationContext fornecido durante o. Inicializar |
| RoleEnvironment.GetRoles | FabricClient.QueryManager.GetNodeList | Se quiser fazer o mesmo tipo de filtragem por tipo, que pode obter a lista de tipos de nós do cluster do manifesto via FabricClient.ClusterManager.GetClusterManifest e obter os tipos de função/nó a partir daí. |
| RoleEnvironment.GetIsAvailable | WindowsFabricCluster ligar ou criar um FabricRuntime apontada para um determinado nó | * |
| RoleEnvironment.GetLocalResource | CodePackageActivationContext.Log/Temp/Work | * |
| RoleEnvironment.GetCurrentRoleInstance | CodePackageActivationContext.Log/Temp/Work | * |
| LocalResource.GetRootPath | CodePackageActivationContext.Log/Temp/Work | * |
| Role.GetInstances | FabricClient.QueryManager.GetNodeList ou ResolveService | * |
| RoleInstanceEndpoint.GetIPEndpoint | FabricRuntime.GetActivationContext ou nomenclatura (ResolveService) | * |

## <a name="next-steps"></a>Próximos Passos
O caminho de migração mais simples dos serviços Cloud para o Service Fabric é substituir a implementação de serviços Cloud com uma aplicação do Service Fabric, manter a arquitetura geral do seu aplicativo aproximadamente o mesmo. O artigo seguinte disponibiliza um guia para ajudar a converter um Web ou a função de trabalho para um serviço sem estado do Service Fabric.

* [Migração Simple: converter um Web ou a função de trabalho para um serviço sem estado do Service Fabric](service-fabric-cloud-services-migration-worker-role-stateless-service.md)

<!--Image references-->
[1]: ./media/service-fabric-cloud-services-migration-differences/topology-cloud-services.png
[2]: ./media/service-fabric-cloud-services-migration-differences/topology-service-fabric.png
[5]: ./media/service-fabric-cloud-services-migration-differences/cloud-service-communication-direct.png
[6]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-communication-direct.png
[7]: ./media/service-fabric-cloud-services-migration-differences/cloud-service-communication-queues.png
[8]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-communication-queues.png
[9]: ./media/service-fabric-cloud-services-migration-differences/cloud-services-architecture.png
[10]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-architecture-simple.png
[11]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-architecture-full.png
