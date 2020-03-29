---
title: Diferenças entre serviços em nuvem e tecido de serviço
description: Uma visão geral conceptual para aplicações migratórias de Serviços cloud para tecido de serviço.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 283ad2c63bb59771dab7881522e737f773ab1705
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75463379"
---
# <a name="learn-about-the-differences-between-cloud-services-and-service-fabric-before-migrating-applications"></a>Conheça as diferenças entre serviços de nuvem e tecido de serviço antes de migrar aplicações.
O Microsoft Azure Service Fabric é a plataforma de aplicação cloud de próxima geração para aplicações distribuídas altamente escaláveis e altamente fiáveis. Introduz muitas novidades para embalagem, implantação, modernização e gestão de aplicações em nuvem distribuídas. 

Este é um guia introdutório para a migração de aplicações de Serviços cloud para tecido de serviço. Centra-se principalmente nas diferenças arquitetónicas e de design entre serviços de nuvem e tecido de serviço.

## <a name="applications-and-infrastructure"></a>Aplicações e infraestruturas
Uma diferença fundamental entre serviços de nuvem e tecido de serviço é a relação entre VMs, cargas de trabalho e aplicações. Uma carga de trabalho aqui é definida como o código que escreve para executar uma tarefa específica ou fornecer um serviço.

* **A Cloud Services trata-se de implementar aplicações como VMs.** O código que escreve está bem acoplado a uma instância vm, como uma Função Web ou Trabalhador. Implementar uma carga de trabalho nos Serviços cloud é implementar uma ou mais instâncias vm que executam a carga de trabalho. Não existe separação de pedidos e VMs, pelo que não existe uma definição formal de pedido. Uma aplicação pode ser considerada como um conjunto de instâncias web ou função de trabalhador dentro de uma implementação de Serviços cloud ou como uma implementação de serviços de nuvem inteira. Neste exemplo, uma aplicação é mostrada como um conjunto de exemplos.

![Aplicações e topologia de Serviços cloud][1]

* **O Service Fabric trata-se de implementar aplicações para VMs ou máquinas existentes que executam tecido de serviço no Windows ou Linux.** Os serviços que escreve estão completamente dissociados da infraestrutura subjacente, que é resumida pela plataforma de aplicação Service Fabric, para que uma aplicação possa ser implementada em vários ambientes. Uma carga de trabalho no Tecido de Serviço é chamada de "serviço", e um ou mais serviços são agrupados numa aplicação formalmente definida que funciona na plataforma de aplicação Service Fabric. Várias aplicações podem ser implantadas para um único cluster de Tecido de Serviço.

![Aplicações e topologia de tecido de serviço][2]

O próprio Service Fabric é uma camada de plataforma de aplicação que funciona no Windows ou linux, enquanto a Cloud Services é um sistema de implementação de VMs geridos pelo Azure com cargas de trabalho anexadas.
O modelo de aplicação Service Fabric tem uma série de vantagens:

* Tempos de implantação rápidos. Criar instâncias VM pode ser demorado. No Tecido de Serviço, os VMs só são implantados uma vez para formar um cluster que acolhe a plataforma de aplicação Service Fabric. A partir daí, os pacotes de aplicação podem ser implantados para o cluster muito rapidamente.
* Hospedagem de alta densidade. Nos Serviços cloud, um VM de função de trabalhador acolhe uma carga de trabalho. No Tecido de Serviço, as aplicações são separadas das VMs que as executam, o que significa que você pode implementar um grande número de aplicações para um pequeno número de VMs, o que pode reduzir o custo global para implementações maiores.
* A plataforma Service Fabric pode funcionar em qualquer lugar que tenha máquinas Windows Server ou Linux, seja azure ou no local. A plataforma fornece uma camada de abstração sobre a infraestrutura subjacente para que a sua aplicação possa funcionar em diferentes ambientes. 
* Gestão de aplicações distribuídas. O Service Fabric é uma plataforma que não só acolhe aplicações distribuídas, como também ajuda a gerir o seu ciclo de vida independentemente do vm de hospedagem ou ciclo de vida da máquina.

## <a name="application-architecture"></a>Arquitetura da aplicação
A arquitetura de uma aplicação cloud Services geralmente inclui numerosas dependências de serviços externos, tais como Service Bus, Azure Table e Blob Storage, SQL, Redis, e outros para gerir o estado e dados de uma aplicação e comunicação entre web e Funções de trabalhador numa implantação de Serviços cloud. Um exemplo de uma aplicação completa dos Serviços cloud pode parecer assim:  

![Arquitetura de Serviços cloud][9]

As aplicações service Fabric também podem optar por utilizar os mesmos serviços externos numa aplicação completa. Utilizando este exemplo a arquitetura Cloud Services, o caminho de migração mais simples dos Serviços cloud para o tecido de serviço é substituir apenas a implementação de Serviços cloud por uma aplicação De Serviço Tecido, mantendo a arquitetura geral igual. As Funções Web e Trabalhadoras podem ser transportadas para serviços apátridas service Fabric com alterações mínimas de código.

![Arquitetura de tecido de serviço após migração simples][10]

Nesta fase, o sistema deve continuar a funcionar da mesma forma que antes. Aproveitando as características imponentes da Service Fabric, as lojas estatais externas podem ser interiorizadas como serviços estatais quando aplicável. Isto está mais envolvido do que uma simples migração de Papéis Web e Trabalhadores para serviços apátridas de Tecido sacana, uma vez que requer a escrita de serviços personalizados que fornecem funcionalidadeequivalente à sua aplicação como os serviços externos fizeram anteriormente. Os benefícios de fazê-lo incluem: 

* Remoção de dependências externas 
* Unificar os modelos de implantação, gestão e atualização. 

Um exemplo de arquitetura resultante da internalização destes serviços poderia ser assim:

![Arquitetura de tecido de serviço após migração completa][11]

## <a name="communication-and-workflow"></a>Comunicação e fluxo de trabalho
A maioria das aplicações do Serviço cloud são compostas por mais de um nível. Da mesma forma, uma aplicação service Fabric consiste em mais de um serviço (normalmente muitos serviços). Dois modelos comuns de comunicação são a comunicação direta e através de um armazenamento duradouro externo.

### <a name="direct-communication"></a>Comunicação direta
Com a comunicação direta, os níveis podem comunicar diretamente através do ponto final exposto por cada nível. Em ambientes apátridas como os Serviços cloud, isto significa selecionar uma instância de uma função VM, aleatória ou redonda para equilibrar a carga, e ligar-se diretamente ao seu ponto final.

![Comunicação direta de Serviços cloud][5]

 A comunicação direta é um modelo comum de comunicação no Tecido de Serviço. A principal diferença entre o Serviço de Tecidos e Serviços de Cloud é que nos Serviços de Cloud liga-se a um VM, enquanto no Tecido de Serviço liga-se a um serviço. Esta é uma distinção importante por algumas razões:

* Os serviços em Tecido de Serviço não estão ligados aos VMs que os acolhem; os serviços podem circular no cluster e, de facto, devem mover-se por várias razões: equilíbrio de recursos, failover, atualizações de aplicações e infraestruturas, e restrições de colocação ou carga. Isto significa que o endereço de uma instância de serviço pode mudar a qualquer momento. 
* Um VM em Tecido de Serviço pode acolher vários serviços, cada um com pontos finais únicos.

O Service Fabric fornece um mecanismo de descoberta de serviços, chamado Serviço de Nomeação, que pode ser usado para resolver endereços finais de serviços. 

![Comunicação direta de tecido de serviço][6]

### <a name="queues"></a>Filas
Um mecanismo comum de comunicação entre níveis em ambientes apátridas, como os Serviços cloud, é usar uma fila de armazenamento externo para armazenar tarefas de trabalho de um nível para outro. Um cenário comum é um nível web que envia empregos para uma Fila Azure ou autocarro de serviço onde as instâncias do Papel do Trabalhador podem desfilar e processar os trabalhos.

![Comunicação de fila de serviços de nuvem][7]

O mesmo modelo de comunicação pode ser utilizado no Tecido de Serviço. Isto pode ser útil quando se migra uma aplicação de Serviços cloud existente para o Tecido de Serviço. 

![Comunicação direta de tecido de serviço][8]

## <a name="parity"></a>Parity
[Os Serviços cloud são semelhantes ao Tecido de Serviço em grau de controlo versus facilidade de utilização, mas agora é um serviço legado e o Tecido](https://docs.microsoft.com/azure/app-service/overview-compare)de Serviço é recomendado para novo desenvolvimento; segue-se uma comparação da API:


| **Cloud Service API** | **API de tecido de serviço** | **Notas** |
| --- | --- | --- |
| RoleInstance.GetID | FabricRuntime.GetNodeContext.NodeId ou . Nome de nó | ID é uma propriedade do Nome do Nó |
| RoleInstance.GetFaultDomain | FabricClient.QueryManager.GetNodeList | Filtrar no Nome do Nó e utilizar propriedade FD |
| RoleInstance.GetUpgradeDomain | FabricClient.QueryManager.GetNodeList | Filtrar no NodeName e usar a propriedade Upgrade |
| RoleInstance.GetInstanceEndpoints | FabricRuntime.GetActivationContext ou Naming (ResolveService) | CodePackageActivationContext que é fornecido tanto por FabricRuntime.GetActivationContext e dentro das réplicas através do ServiceInitializationParameters.CodePackageActivationContext fornecido durante . Inicializar |
| RoleEnvironment.GetRoles | FabricClient.QueryManager.GetNodeList | Se quiser fazer o mesmo tipo de filtragem por tipo, pode obter a lista de tipos de nós do manifesto de cluster via FabricClient.ClusterManager.GetClusterManifest e agarrar os tipos de role/nó a partir daí. |
| RoleEnvironment.GetisAvailable | Connect-WindowsFabricCluster ou criar um FabricRuntime apontado para um nó particular | * |
| RoleEnvironment.GetLocalResource | CodePackageActivationContext.Log/Temp/Work | * |
| RoleEnvironment.GetCurrentRoleInstance | CodePackageActivationContext.Log/Temp/Work | * |
| LocalResource.GetRootPath | CodePackageActivationContext.Log/Temp/Work | * |
| Role.GetInstances | FabricClient.QueryManager.GetNodeList ou ResolveService | * |
| RoleInstanceEndpoint.GetIPEndpoint | FabricRuntime.GetActivationContext ou Naming (ResolveService) | * |

## <a name="next-steps"></a>Passos Seguintes
A via de migração mais simples dos Serviços Cloud para o Tecido de Serviço é substituir apenas a implementação de Serviços cloud por uma aplicação De Tecido de Serviço, mantendo a arquitetura geral da sua aplicação aproximadamente igual. O seguinte artigo fornece um guia para ajudar a converter uma Função Web ou Trabalhador para um serviço apátrida de Tecido de Serviço.

* [Migração simples: converter uma Função Web ou Trabalhador num serviço apátrida de Tecido de Serviço](service-fabric-cloud-services-migration-worker-role-stateless-service.md)

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
