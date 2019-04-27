---
title: Próximos passos de criação do Service Fabric project | Documentos da Microsoft
description: Saiba mais sobre o projeto de aplicação que acabou de criar no Visual Studio.  Saiba como criar serviços com tutoriais e saiba mais sobre o desenvolvimento de serviços para o Service Fabric.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: 299d1f97-1ca9-440d-9f81-d1d0dd2bf4df
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/07/2017
ms.author: atsenthi
ms.openlocfilehash: e5371cd3ea9de1993f0f824325f6cbf1e25343d4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60773048"
---
# <a name="your-service-fabric-application-and-next-steps"></a>A aplicação do Service Fabric e os passos seguintes
Foi criada a sua aplicação do Azure Service Fabric. Este artigo descreve alguns tutoriais para experimentar, como montar o seu projeto, algumas informações que possa estar interessadas e os passos seguintes potenciais.

## <a name="get-started-with-tutorials-walk-throughs-and-samples"></a>Comece com os tutoriais, instruções passo a passo e exemplos
Pronto para começar?  

Trabalhar com o tutorial de aplicação .NET. Saiba como [criar uma aplicação](service-fabric-tutorial-create-dotnet-app.md) com um front-end do ASP.NET Core e um back-end com monitorização de estado, [implementar a aplicação](service-fabric-tutorial-deploy-app-to-party-cluster.md) para um cluster [configurar CI/CD](service-fabric-tutorial-deploy-app-with-cicd-vsts.md), e [configurar monitorização e diagnóstico](service-fabric-tutorial-monitoring-aspnet.md).

Em alternativa, experimente um da seguinte instruções passo a passo e criar a sua primeira...
- [C#Serviço de serviços fiável no Windows](service-fabric-reliable-services-quick-start.md) 
- [C#Serviço de Atores fiável no Windows](service-fabric-reliable-actors-get-started.md) 
- [Serviço de executável de convidado no Windows](quickstart-guest-app.md) 
- [Aplicação de contentor do Windows](service-fabric-get-started-containers.md) 

Também poderá estar interessado em experimentar nosso [aplicações de exemplo](https://aka.ms/servicefabricsamples).

## <a name="have-questions-or-feedback--need-to-report-an-issue"></a>Tem perguntas ou comentários?  Tem de comunicar um problema?
Leia [perguntas comuns](service-fabric-common-questions.md) e encontre respostas sobre o Service Fabric pode fazer e como ele deve ser usado.

[Opções de suporte](service-fabric-support.md) apresenta uma lista de fóruns no StackOverflow e do MSDN para fazer perguntas, bem como opções para problemas de relatórios, obter suporte e enviar comentários sobre o produto.

## <a name="the-application-project"></a>O projeto de aplicativo
Cada nova aplicação inclui um projeto de aplicativo. Pode haver um ou dois projetos adicionais, dependendo do tipo de serviço escolhido.

O projeto de aplicativo consiste em:

* Um conjunto de referências para os serviços que tornam a sua aplicação.
* Publicar três perfis (1 nó Local, 5-nó Local e na Cloud) que pode utilizar para manter as preferências para trabalhar com ambientes diferentes, como preferências relacionados com um ponto de extremidade do cluster e se pretende efetuar implementações de atualização por predefinição.
* Os arquivos (tal como acima) que pode utilizar para manter as configurações de aplicativos específicos do ambiente, como o número de partições para criar um serviço de três parâmetros de aplicação. Saiba como [configurar a sua aplicação para vários ambientes](service-fabric-manage-multiple-environment-app-configuration.md).
* Um script de implementação que pode utilizar para implementar a sua aplicação a partir da linha de comando ou como parte de um pipeline de integração e implementação contínuo automatizado. Saiba mais sobre [implementar aplicações com o PowerShell](service-fabric-deploy-remove-applications.md).
* O manifesto do aplicativo, que descreve a aplicação. Pode encontrar o manifesto na pasta ApplicationPackageRoot. Saiba mais sobre [manifestos de aplicações e serviços](service-fabric-application-model.md).



## <a name="learn-more-about-the-programming-models"></a>Saiba mais sobre os modelos de programação
Service Fabric oferece várias maneiras de escrever e gerir os seus serviços.  Eis o descrição geral e informações conceituais sobre [Reliable Services com e sem estado](service-fabric-reliable-services-introduction.md), [Reliable Actors](service-fabric-reliable-actors-introduction.md), [contentores](service-fabric-containers-overview.md), [executáveis convidados ](service-fabric-guest-executables-introduction.md), e [serviços de ASP.NET Core com e sem estado](service-fabric-reliable-services-communication-aspnetcore.md).

## <a name="learn-about-service-communication"></a>Saiba mais sobre a comunicação de serviço
Uma aplicação do Service Fabric é composta por diferentes serviços, onde cada serviço executa uma tarefa de especializados. Estes serviços podem se comunicar entre si e podem existir aplicações de cliente fora do cluster que ligar ao e comunicam com os serviços. Saiba como [configurar a comunicação com e entre os seus serviços](service-fabric-connect-and-communicate-with-services.md) no Service Fabric. 

## <a name="learn-about-configuring-application-security"></a>Saiba mais sobre a configuração de segurança de aplicações
Pode proteger aplicações em execução no cluster sob diferentes contas de usuário. Service Fabric também ajuda a proteger os recursos utilizados por aplicações no momento da implantação sob as contas de utilizador – por exemplo, arquivos, diretórios e certificados. Isso faz com que aplicativos em execução, mesmo num ambiente de hospedagem compartilhado, mais seguro umas das outras.  Saiba como [configurar políticas de segurança para a sua aplicação](service-fabric-application-runas-security.md).

A aplicação pode conter informações confidenciais, como cadeias de ligação de armazenamento, as palavras-passe ou outros valores que não devem ser processados em texto simples. Saiba como [gerir segredos em seu aplicativo](service-fabric-application-secret-management.md).

## <a name="learn-about-the-application-lifecycle"></a>Saiba mais sobre o ciclo de vida do aplicativo
Como com outras plataformas, uma aplicação do Service Fabric normalmente vai nas seguintes fases: design, desenvolvimento, teste, implantação, atualizar, manutenção e remoção. [Este artigo](service-fabric-application-lifecycle.md) fornece uma descrição geral das APIs e como elas são usadas pelas funções de diferentes em todas as fases do ciclo de vida de aplicação do Service Fabric.

## <a name="next-steps"></a>Passos Seguintes
- [Criar um cluster do Windows no Azure](service-fabric-tutorial-create-vnet-and-windows-cluster.md).
- Visualizar o cluster, incluindo aplicações implementadas e o esquema físico, com [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
- [Versão e atualizar seus serviços](service-fabric-application-upgrade-tutorial.md)


