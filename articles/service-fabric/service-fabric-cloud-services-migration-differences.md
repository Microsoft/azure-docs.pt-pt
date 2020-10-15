---
title: Diferenças entre serviços em nuvem e tecido de serviço
description: Uma visão geral conceptual para aplicações migratórias dos Serviços cloud para o Tecido de Serviço.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 763b9977f756075789b826c2f1e01cd41ac60a73
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92091173"
---
# <a name="learn-about-the-differences-between-cloud-services-and-service-fabric-before-migrating-applications"></a>Saiba mais sobre as diferenças entre os Serviços cloud e o Tecido de Serviço antes de migrar as aplicações.
O Microsoft Azure Service Fabric é a plataforma de aplicações cloud de próxima geração para aplicações distribuídas altamente escaláveis e altamente fiáveis. Introduz muitas novidades para a embalagem, implantação, upgrade e gestão de aplicações em nuvem distribuídas. 

Este é um guia introdutório para a migração de aplicações dos Serviços cloud para o Tecido de Serviço. Centra-se principalmente nas diferenças arquitetónicas e de design entre os Serviços cloud e o Tecido de Serviço.

## <a name="applications-and-infrastructure"></a>Aplicações e infraestruturas
Uma diferença fundamental entre serviços em nuvem e tecido de serviço é a relação entre VMs, cargas de trabalho e aplicações. Uma carga de trabalho aqui é definida como o código que escreve para executar uma tarefa específica ou fornecer um serviço.

* **O Cloud Services tem a ver com a implementação de aplicações como VMs.** O código que escreve está muito acopdo a uma instância VM, como uma Web ou Papel de Trabalhador. Implementar uma carga de trabalho nos Serviços cloud é implementar uma ou mais instâncias VM que executam a carga de trabalho. Não existe separação de aplicações e VMs, pelo que não existe uma definição formal de aplicação. Uma aplicação pode ser considerada como um conjunto de instâncias web ou de função do trabalhador dentro de uma implementação de Cloud Services ou como uma implementação inteira de Cloud Services. Neste exemplo, uma aplicação é mostrada como um conjunto de instâncias de papel.

![Aplicações de Serviços cloud e topologia][1]

* **O Service Fabric trata-se de implementar aplicações para VMs ou máquinas existentes que executam o Tecido de Serviço no Windows ou Linux.** Os serviços que escreve estão completamente dissociados da infraestrutura subjacente, que é resumida pela plataforma de aplicação Service Fabric, para que uma aplicação possa ser implementada em vários ambientes. Uma carga de trabalho em Service Fabric é chamada de "serviço", e um ou mais serviços são agrupados numa aplicação formalmente definida que funciona na plataforma de aplicação Service Fabric. Várias aplicações podem ser implantadas num único cluster de Tecido de Serviço.

![Aplicações de tecido de serviço e topologia][2]

O Service Fabric em si é uma camada de plataforma de aplicação que funciona no Windows ou Linux, enquanto o Cloud Services é um sistema para implantar VMs geridos pelo Azure com cargas de trabalho anexadas.
O modelo de aplicação Service Fabric tem várias vantagens:

* Tempos de implantação rápidos. Criar instâncias VM pode ser demorado. No Tecido de Serviço, os VMs são implantados apenas uma vez para formar um cluster que acolhe a plataforma de aplicação Service Fabric. A partir daí, os pacotes de aplicação podem ser implantados para o cluster muito rapidamente.
* Hospedagem de alta densidade. Na Cloud Services, um VM de papel operário acolhe uma carga de trabalho. No Tecido de Serviço, as aplicações são separadas dos VMs que as executam, o que significa que você pode implementar um grande número de aplicações para um pequeno número de VMs, o que pode reduzir o custo global para implementações maiores.
* A plataforma Service Fabric pode funcionar em qualquer lugar que tenha máquinas Windows Server ou Linux, seja a Azure ou no local. A plataforma fornece uma camada de abstração sobre a infraestrutura subjacente para que a sua aplicação possa funcionar em diferentes ambientes. 
* Gestão de aplicações distribuídas. O Service Fabric é uma plataforma que não só acolhe aplicações distribuídas, como também ajuda a gerir o seu ciclo de vida independentemente do VM de hospedagem ou do ciclo de vida da máquina.

## <a name="application-architecture"></a>Arquitetura da aplicação
A arquitetura de uma aplicação cloud Services geralmente inclui numerosas dependências de serviços externos, tais como Service Bus, Azure Table e Blob Storage, SQL, Redis, e outros para gerir o estado e dados de uma aplicação e comunicação entre funções Web e Worker roles em uma implementação de Cloud Services. Um exemplo de uma aplicação completa dos Serviços cloud pode ser assim:  

![Arquitetura de Serviços cloud][9]

As aplicações de Tecido de Serviço também podem optar por utilizar os mesmos serviços externos numa aplicação completa. Utilizando este exemplo da arquitetura Cloud Services, o caminho de migração mais simples dos Serviços cloud para o Tecido de Serviço é substituir apenas a implementação dos Serviços cloud por uma aplicação de Tecido de Serviço, mantendo a arquitetura geral da mesma forma. As funções web e trabalhadora podem ser transmitidas aos serviços apátridas do Service Fabric com alterações mínimas de código.

![Arquitetura de tecido de serviço após migração simples][10]

Nesta fase, o sistema deve continuar a funcionar da mesma forma que antes. Tirando partido das características imponentes da Service Fabric, as lojas externas do Estado podem ser internalizadas como serviços estatais, quando aplicável. Isto está mais envolvido do que uma simples migração de serviços apátridas web e worker roles para service fabric, uma vez que requer a escrita de serviços personalizados que fornecem funcionalidade equivalente à sua aplicação como os serviços externos fizeram anteriormente. Os benefícios de fazê-lo incluem: 

* Eliminação das dependências externas 
* Unificando os modelos de implementação, gestão e upgrade. 

Um exemplo da arquitetura resultante da internalização destes serviços poderia ser assim:

![Arquitetura de tecido de serviço após migração completa][11]

## <a name="communication-and-workflow"></a>Comunicação e fluxo de trabalho
A maioria das aplicações do Cloud Service consistem em mais de um nível. Da mesma forma, uma aplicação Service Fabric é composta por mais de um serviço (normalmente muitos serviços). Dois modelos de comunicação comuns são a comunicação direta e através de um armazenamento externo durável.

### <a name="direct-communication"></a>Comunicação direta
Com a comunicação direta, os níveis podem comunicar diretamente através do ponto final exposto por cada nível. Em ambientes apátridas como os Cloud Services, isto significa selecionar uma instância de um papel VM, aleatoriamente ou rodada para equilibrar a carga, e ligar-se diretamente ao seu ponto final.

![Comunicação direta dos Serviços cloud][5]

 A comunicação direta é um modelo comum de comunicação em Tecido de Serviço. A principal diferença entre o Service Fabric e o Cloud Services é que nos Serviços cloud liga-se a um VM, enquanto no Tecido de Serviço liga-se a um serviço. Esta é uma distinção importante por algumas razões:

* Os serviços em Tecido de Serviço não estão ligados aos VMs que os acolhem; os serviços podem mover-se no cluster e, na verdade, espera-se que se desloquem por várias razões: equilíbrio de recursos, failover, atualizações de aplicações e infraestruturas, e restrições de colocação ou carga. Isto significa que o endereço de uma instância de serviço pode ser alterado a qualquer momento. 
* Um VM in Service Fabric pode acolher vários serviços, cada um com pontos finais únicos.

A Service Fabric fornece um mecanismo de descoberta de serviços, chamado Serviço de Nomeação, que pode ser usado para resolver endereços de ponto final de serviços. 

![Diagrama que mostra como o Service Fabric fornece um mecanismo de descoberta de serviço, chamado Serviço de Nomeação, que pode ser usado para resolver endereços de ponto final de serviços.][6]

### <a name="queues"></a>Filas
Um mecanismo comum de comunicação entre níveis em ambientes apátridas, como os Serviços Cloud, é usar uma fila de armazenamento externo para armazenar tarefas de trabalho duradouras de um nível para outro. Um cenário comum é um nível web que envia empregos para uma Fila Azure ou autocarro de serviço onde os casos de papel dos trabalhadores podem descaendirá-lo e processar os empregos.

![Comunicação de fila de serviços de nuvem][7]

O mesmo modelo de comunicação pode ser utilizado no Tecido de Serviço. Isto pode ser útil ao migrar uma aplicação de Serviços Cloud existente para o Tecido de Serviço. 

![Comunicação direta do Tecido de Serviço][8]

## <a name="parity"></a>Parity
[Os Serviços cloud são semelhantes ao Service Fabric em grau de controlo versus facilidade de utilização, mas agora é um serviço antigo e o Tecido de Serviço é recomendado para novos desenvolvimentos;](/azure/architecture/guide/technology-choices/compute-decision-tree) trata-se de uma comparação com a API:


| **API de Serviço de Nuvem** | **API de Tecido de Serviço** | **Notas** |
| --- | --- | --- |
| RoleInstance.GetID | FabricRuntime.GetNodeContext.NodeId ou . Nome de nó | ID é uma propriedade do Nome Node |
| RoleInstance.GetFaultDomain | FabricClient.QueryManager.GetNodeList | Filtrar no Nome no Node e utilizar propriedade FD |
| RoleInstance.GetUpgradeDomain | FabricClient.QueryManager.GetNodeList | Filtrar no Nome Node e usar a propriedade Upgrade |
| RoleInstance.GetInstanceEndpoints | FabricRuntime.GetActivationContext ou Naming (ResolveService) | CodePackageActivationContext fornecido tanto pelo FabricRuntime.GetActivationContext como dentro das réplicas via ServiceInitializationParameters.CodePackageActivationContext fornecido durante . Inicializar |
| RoleEnvironment.GetRoles | FabricClient.QueryManager.GetNodeList | Se quiser fazer o mesmo tipo de filtragem por tipo, pode obter a lista de tipos de nós do manifesto do cluster via FabricClient.ClusterManager.GetClusterManifest e agarrar os tipos de papéis/nódissalhões a partir daí. |
| RoleEnvironment.GetIsAvailable | Connect-WindowsFabricCluster ou criar um FabricRuntime apontado para um nó particular | * |
| RoleEnvironment.GetLocalResource | CodePackageActivationContext.Log/Temp/Work | * |
| RoleEnvironment.GetCurrentRoleInstance | CodePackageActivationContext.Log/Temp/Work | * |
| LocalResource.GetRootPath | CodePackageActivationContext.Log/Temp/Work | * |
| Role.GetInstances | FabricClient.QueryManager.GetNodeList ou ResolveService | * |
| RoleInstanceEndpoint.GetIPEndpoint | FabricRuntime.GetActivationContext ou Naming (ResolveService) | * |

## <a name="next-steps"></a>Passos Seguintes
A rota de migração mais simples dos Serviços cloud para o Tecido de Serviços é substituir apenas a implementação dos Serviços Cloud por uma aplicação de Tecido de Serviço, mantendo a arquitetura geral da sua aplicação aproximadamente igual. O seguinte artigo fornece um guia para ajudar a converter uma Função Web ou Trabalhador para um serviço apátrida de Tecido de Serviço.

* [Migração simples: converter uma função web ou trabalhadora para um serviço apátrida de tecido de serviço](service-fabric-cloud-services-migration-worker-role-stateless-service.md)

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