---
title: Diferenças entre os serviços de nuvem e Service Fabric
description: Uma visão geral conceitual para migrar aplicativos dos serviços de nuvem para Service Fabric.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 283ad2c63bb59771dab7881522e737f773ab1705
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75463379"
---
# <a name="learn-about-the-differences-between-cloud-services-and-service-fabric-before-migrating-applications"></a>Saiba mais sobre as diferenças entre os serviços de nuvem e Service Fabric antes de migrar aplicativos.
Microsoft Azure Service Fabric é a plataforma de aplicativos de nuvem de última geração para aplicativos distribuídos altamente escalonáveis e altamente confiáveis. Ele apresenta muitos recursos novos para empacotamento, implantação, atualização e gerenciamento de aplicativos de nuvem distribuídos. 

Este é um guia introdutório para migrar aplicativos dos serviços de nuvem para Service Fabric. Ele se concentra principalmente nas diferenças de arquitetura e design entre os serviços de nuvem e Service Fabric.

## <a name="applications-and-infrastructure"></a>Aplicativos e infraestrutura
Uma diferença fundamental entre os serviços de nuvem e a Service Fabric é a relação entre as VMs, as cargas de trabalho e os aplicativos. Uma carga de trabalho aqui é definida como o código que você escreve para executar uma tarefa específica ou fornecer um serviço.

* **Os serviços de nuvem são sobre a implantação de aplicativos como VMs.** O código que você escreve está rigidamente acoplado a uma instância de VM, como uma função Web ou de trabalho. Implantar uma carga de trabalho nos serviços de nuvem é implantar uma ou mais instâncias de VM que executam a carga de trabalho. Não há separação de aplicativos e VMs e, portanto, não há definição formal de um aplicativo. Um aplicativo pode ser considerado como um conjunto de instâncias de função de trabalho ou Web em uma implantação de serviços de nuvem ou como uma implantação de serviços de nuvem inteira. Neste exemplo, um aplicativo é mostrado como um conjunto de instâncias de função.

![Aplicativos e topologia dos serviços de nuvem][1]

* **Service Fabric é sobre a implantação de aplicativos em VMs existentes ou máquinas que executam o Service Fabric no Windows ou no Linux.** Os serviços que você escreve são completamente dissociados da infraestrutura subjacente, que é abstraida pela plataforma de aplicativo Service Fabric, de modo que um aplicativo pode ser implantado em vários ambientes. Uma carga de trabalho no Service Fabric é chamada de "serviço", e um ou mais serviços são agrupados em um aplicativo formalmente definido que é executado na plataforma de aplicativo Service Fabric. Vários aplicativos podem ser implantados em um único cluster Service Fabric.

![Service Fabric aplicativos e topologia][2]

Service Fabric em si é uma camada de plataforma de aplicativo que é executada no Windows ou no Linux, enquanto os serviços de nuvem são um sistema para implantar VMs gerenciadas pelo Azure com cargas de trabalho anexadas.
O modelo de aplicativo Service Fabric tem uma série de vantagens:

* Tempos de implantação rápidos. A criação de instâncias de VM pode ser demorada. No Service Fabric, as VMs são implantadas apenas uma vez para formar um cluster que hospeda a plataforma de aplicativo Service Fabric. A partir desse ponto, os pacotes de aplicativos podem ser implantados no cluster muito rapidamente.
* Hospedagem de alta densidade. Nos serviços de nuvem, a VM de uma função de trabalho hospeda uma carga. No Service Fabric, os aplicativos são separados das VMs que os executam, o que significa que você pode implantar um grande número de aplicativos em um pequeno número de VMs, o que pode reduzir o custo geral para implantações maiores.
* A plataforma Service Fabric pode ser executada em qualquer lugar que tenha computadores Windows Server ou Linux, seja o Azure ou local. A plataforma fornece uma camada de abstração sobre a infraestrutura subjacente para que seu aplicativo possa ser executado em ambientes diferentes. 
* Gerenciamento de aplicativos distribuídos. Service Fabric é uma plataforma que não só hospeda aplicativos distribuídos, mas também ajuda a gerenciar seu ciclo de vida independentemente da VM de hospedagem ou do ciclo de vida da máquina.

## <a name="application-architecture"></a>Arquitetura da aplicação
A arquitetura de um aplicativo de serviços de nuvem geralmente inclui várias dependências de serviço externo, como o barramento de serviço, o armazenamento de tabelas e BLOBs do Azure, SQL, Redis e outros para gerenciar o estado e os dados de um aplicativo e a comunicação entre a Web e Funções de trabalho em uma implantação de serviços de nuvem. Um exemplo de um aplicativo de serviços de nuvem completo pode ser assim:  

![Arquitetura de serviços de nuvem][9]

Service Fabric aplicativos também podem optar por usar os mesmos serviços externos em um aplicativo completo. Usando este exemplo de arquitetura de serviços de nuvem, o caminho de migração mais simples dos serviços de nuvem para Service Fabric é substituir apenas a implantação dos serviços de nuvem por um aplicativo Service Fabric, mantendo a arquitetura geral a mesma. As funções Web e de trabalho podem ser portadas para Service Fabric serviços sem estado com alterações mínimas de código.

![Arquitetura de Service Fabric após a migração simples][10]

Nesse estágio, o sistema deve continuar a funcionar da mesma forma que antes. Aproveitando os recursos com estado de Service Fabric, os armazenamentos de estado externos podem ser internalizados como serviços com estado quando aplicável. Isso é mais envolvido do que uma simples migração de funções Web e de trabalho para Service Fabric serviços sem monitoração de estado, pois requer a criação de serviços personalizados que fornecem funcionalidade equivalente ao seu aplicativo como os serviços externos faziam antes. Os benefícios de fazer isso incluem: 

* Removendo dependências externas 
* Unificar os modelos de implantação, gerenciamento e atualização. 

Um exemplo de arquitetura resultante de internalizar esses serviços poderia ser assim:

![Arquitetura de Service Fabric após a migração completa][11]

## <a name="communication-and-workflow"></a>Comunicação e fluxo de trabalho
A maioria dos aplicativos de serviço de nuvem consistem em mais de uma camada. Da mesma forma, um aplicativo Service Fabric consiste em mais de um serviço (geralmente muitos serviços). Dois modelos de comunicação comuns são a comunicação direta e por meio de um armazenamento durável externo.

### <a name="direct-communication"></a>Comunicação direta
Com a comunicação direta, as camadas podem se comunicar diretamente por meio do ponto de extremidade exposto por cada camada. Em ambientes sem estado, como serviços de nuvem, isso significa selecionar uma instância de uma função VM, seja aleatoriamente ou Round Robin para balancear a carga e conectar-se diretamente ao ponto de extremidade.

![Comunicação direta dos serviços de nuvem][5]

 A comunicação direta é um modelo de comunicação comum no Service Fabric. A principal diferença entre Service Fabric e serviços de nuvem é que nos serviços de nuvem você se conecta a uma VM, enquanto em Service Fabric você se conecta a um serviço. Essa é uma distinção importante por alguns motivos:

* Os serviços no Service Fabric não estão associados às VMs que os hospedam; os serviços podem se movimentar no cluster e, na verdade, devem ser movidos por vários motivos: balanceamento de recursos, failover, atualizações de aplicativos e infraestrutura e restrições de posicionamento ou carregamento. Isso significa que o endereço de uma instância de serviço pode ser alterado a qualquer momento. 
* Uma VM no Service Fabric pode hospedar vários serviços, cada um com pontos de extremidade exclusivos.

Service Fabric fornece um mecanismo de descoberta de serviço, chamado de Serviço de Nomenclatura, que pode ser usado para resolver endereços de ponto de extremidade de serviços. 

![Service Fabric comunicação direta][6]

### <a name="queues"></a>Filas
Um mecanismo de comunicação comum entre camadas em ambientes sem estado, como serviços de nuvem, é usar uma fila de armazenamento externo para permanentemente armazenar tarefas de trabalho de uma camada para outra. Um cenário comum é uma camada da Web que envia trabalhos para uma fila do Azure ou barramento de serviço em que as instâncias de função de trabalho podem remover da fila e processar os trabalhos.

![Comunicação de fila de serviços de nuvem][7]

O mesmo modelo de comunicação pode ser usado em Service Fabric. Isso pode ser útil ao migrar um aplicativo de serviços de nuvem existente para Service Fabric. 

![Service Fabric comunicação direta][8]

## <a name="parity"></a>Parity
Os [serviços de nuvem são semelhantes a Service Fabric no grau de controle versus facilidade de uso, mas agora é um serviço herdado e Service Fabric é recomendado para novo desenvolvimento](https://docs.microsoft.com/azure/app-service/overview-compare); a seguir está uma comparação de API:


| **API do serviço de nuvem** | **API de Service Fabric** | **Notas** |
| --- | --- | --- |
| RoleInstance.GetID | FabricRuntime.GetNodeContext.NodeId or .NodeName | ID é uma propriedade de NodeName |
| RoleInstance.GetFaultDomain | FabricClient.QueryManager.GetNodeList | Filtrar por NodeName e usar a propriedade FD |
| RoleInstance.GetUpgradeDomain | FabricClient.QueryManager.GetNodeList | Filtrar por NodeName e usar a propriedade upgrade |
| RoleInstance.GetInstanceEndpoints | FabricRuntime. GetActivationContext ou nomenclatura (ResolveService) | CodePackageActivationContext, que é fornecido por FabricRuntime. GetActivationContext e dentro das réplicas por meio de iminitializationparameters. CodePackageActivationContext fornecido durante. Inicializar |
| RoleEnvironment.GetRoles | FabricClient.QueryManager.GetNodeList | Se você quiser fazer o mesmo tipo de filtragem por Type, poderá obter a lista de tipos de nó do manifesto do cluster por meio de FabricClient. Clustermanager. GetClusterManifest e pegar os tipos de função/nó a partir daí. |
| RoleEnvironment.GetIsAvailable | Connect-WindowsFabricCluster ou criar um FabricRuntime apontado para um nó específico | * |
| RoleEnvironment.GetLocalResource | CodePackageActivationContext.Log/Temp/Work | * |
| RoleEnvironment.GetCurrentRoleInstance | CodePackageActivationContext.Log/Temp/Work | * |
| LocalResource.GetRootPath | CodePackageActivationContext.Log/Temp/Work | * |
| Role.GetInstances | FabricClient. Querymanager. getnodelist ou ResolveService | * |
| RoleInstanceEndpoint.GetIPEndpoint | FabricRuntime. GetActivationContext ou nomenclatura (ResolveService) | * |

## <a name="next-steps"></a>Próximos Passos
O caminho de migração mais simples dos serviços de nuvem para Service Fabric é substituir apenas a implantação dos serviços de nuvem por um aplicativo Service Fabric, mantendo a arquitetura geral do seu aplicativo, aproximadamente. O artigo a seguir fornece um guia para ajudar a converter uma função Web ou de trabalho em um Service Fabric serviço sem estado.

* [Migração simples: converter uma função Web ou de trabalho em um Service Fabric serviço sem estado](service-fabric-cloud-services-migration-worker-role-stateless-service.md)

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
