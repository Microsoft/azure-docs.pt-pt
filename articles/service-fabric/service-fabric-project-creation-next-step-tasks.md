---
title: Criação de projeto de tecido de serviço nos próximos passos
description: Conheça o projeto de candidatura que acabou de criar no Visual Studio.  Saiba como construir serviços usando tutoriais e saiba mais sobre o desenvolvimento de serviços para o Service Fabric.
ms.topic: conceptual
ms.date: 12/07/2017
ms.openlocfilehash: 01a69016e0c299fba0365fab5332b572fd2ca87a
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92314478"
---
# <a name="your-service-fabric-application-and-next-steps"></a>A sua aplicação de Tecido de Serviço e os próximos passos
A sua aplicação Azure Service Fabric foi criada. Este artigo descreve alguns tutoriais para experimentar, a composição do seu projeto, mais informações que possa estar interessado, e potenciais próximos passos.

## <a name="get-started-with-tutorials-walk-throughs-and-samples"></a>Começar com tutoriais, walk-throughs e amostras
Pronto para começar?  

Trabalhe através do tutorial de aplicação .NET. Aprenda a [construir uma app](service-fabric-tutorial-create-dotnet-app.md) com uma ASP.NET Core front-end e uma extremidade traseira imponente, implementar a [aplicação](service-fabric-tutorial-deploy-app-to-party-cluster.md) num cluster, [configurar CI/CD,](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)e [configurar monitorização e diagnósticos](service-fabric-tutorial-monitoring-aspnet.md).

Ou, experimente um dos seguintes passeios e crie o seu primeiro...
- [C# Serviços fiáveis no Windows](service-fabric-reliable-services-quick-start.md) 
- [C# Serviço de atores fiáveis no Windows](service-fabric-reliable-actors-get-started.md) 
- [Serviço executável de hóspedes no Windows](quickstart-guest-app.md) 
- [Aplicação de contentor do Windows](service-fabric-get-started-containers.md) 

Você também pode estar interessado em experimentar as [nossas aplicações de amostra.](/samples/browse/?products=azure)

## <a name="have-questions-or-feedback--need-to-report-an-issue"></a>Tem perguntas ou comentários?  Precisa reportar um problema?
Leia [as perguntas comuns](service-fabric-common-questions.md) e encontre respostas sobre o que o Service Fabric pode fazer e como deve ser usado.

[As opções de suporte](service-fabric-support.md) listam fóruns no StackOverflow e MSDN para fazer perguntas, bem como opções para reportar problemas, obter suporte e submeter feedback do produto.

## <a name="the-application-project"></a>O projeto de candidatura
Cada nova aplicação inclui um projeto de candidatura. Pode haver um ou dois projetos adicionais, dependendo do tipo de serviço escolhido.

O projeto de candidatura consiste em:

* Um conjunto de referências aos serviços que compõem a sua aplicação.
* Três perfis de publicação (1-Nó Local, 5-Node Local e Cloud) que pode usar para manter preferências por trabalhar com diferentes ambientes - tais como preferências relacionadas com um ponto final do cluster e se deve realizar implementações de upgrade por padrão.
* Três ficheiros de parâmetros de aplicação (os mesmos que acima) que pode utilizar para manter configurações de aplicações específicas do ambiente, como o número de divisórias para criar para um serviço. Saiba como configurar a [sua aplicação para vários ambientes.](service-fabric-manage-multiple-environment-app-configuration.md)
* Um script de implementação que pode utilizar para implementar a sua aplicação a partir da linha de comando ou como parte de um pipeline de integração e implantação contínua automatizado. Saiba mais sobre [a implementação de aplicações utilizando o PowerShell](service-fabric-deploy-remove-applications.md).
* O manifesto de aplicação, que descreve a aplicação. Pode encontrar o manifesto na pasta ApplicationPackageRoot. Saiba mais sobre [os manifestos de aplicação e serviço.](service-fabric-application-model.md)



## <a name="learn-more-about-the-programming-models"></a>Saiba mais sobre os modelos de programação
A Service Fabric oferece várias formas de escrever e gerir os seus serviços.  Aqui está uma visão geral e informação conceptual sobre [os serviços atatriados e imponentes, os Reliable](service-fabric-reliable-services-introduction.md) [Actors,](service-fabric-reliable-actors-introduction.md) [os contentores,](service-fabric-containers-overview.md) [os serviços de execução de hóspedes,](service-fabric-guest-executables-introduction.md)e [os serviços de ASP.NET Core apátridas e imponentes.](service-fabric-reliable-services-communication-aspnetcore.md)

## <a name="learn-about-service-communication"></a>Saiba mais sobre comunicação de serviços
Uma aplicação De Tecido de Serviço é composta por diferentes serviços, onde cada serviço realiza uma tarefa especializada. Estes serviços podem comunicar entre si e pode haver aplicações de clientes fora do cluster que se conectam e comunicam com os serviços. Saiba como configurar a [comunicação com e entre os seus serviços](service-fabric-connect-and-communicate-with-services.md) em Service Fabric. 

## <a name="learn-about-configuring-application-security"></a>Saiba como configurar a segurança da aplicação
Pode proteger as aplicações que estão a ser executando no cluster sob diferentes contas de utilizador. O Service Fabric também ajuda a garantir os recursos que são utilizados pelas aplicações no momento da implantação nas contas do utilizador -- por exemplo, ficheiros, diretórios e certificados. Isto torna as aplicações de execução, mesmo num ambiente partilhado, mais seguras umas das outras.  Saiba como [configurar políticas de segurança para a sua aplicação.](service-fabric-application-runas-security.md)

A sua aplicação pode conter informações sensíveis, tais como cadeias de ligação de armazenamento, palavras-passe ou outros valores que não devem ser manuseados em texto simples. Saiba como [gerir segredos na sua aplicação.](service-fabric-application-secret-management.md)

## <a name="learn-about-the-application-lifecycle"></a>Conheça o ciclo de vida da aplicação
Tal como acontece com outras plataformas, uma aplicação de Tecido de Serviço geralmente passa pelas seguintes fases: design, desenvolvimento, teste, implementação, upgrade, manutenção e remoção. [Este artigo](service-fabric-application-lifecycle.md) fornece uma visão geral das APIs e como são usadas pelas diferentes funções ao longo das fases do ciclo de vida da aplicação Service Fabric.

## <a name="next-steps"></a>Passos seguintes
- [Criar um cluster Windows em Azure](service-fabric-tutorial-create-vnet-and-windows-cluster.md).
- Visualize o seu cluster, incluindo aplicações implementadas e layout físico, com [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
- [Versão e atualização dos seus serviços](service-fabric-application-upgrade-tutorial.md)