---
title: Criação de projeto service Fabric próximas etapas
description: Conheça o projeto de candidatura que acabou de criar no Estúdio Visual.  Saiba como construir serviços usando tutoriais e saiba mais sobre o desenvolvimento de serviços para o Tecido de Serviço.
ms.topic: conceptual
ms.date: 12/07/2017
ms.openlocfilehash: e8fc105657bda6114851f4819be4658926ad621c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75349400"
---
# <a name="your-service-fabric-application-and-next-steps"></a>A sua aplicação De Tecido de Serviço e os próximos passos
A sua aplicação Azure Service Fabric foi criada. Este artigo descreve alguns tutoriais para experimentar, a composição do seu projeto, mais algumas informações que possa estar interessado, e potenciais próximos passos.

## <a name="get-started-with-tutorials-walk-throughs-and-samples"></a>Começar com tutoriais, walk-throughs e amostras
Pronto para começar?  

Trabalhe através do tutorial de aplicação .NET. Aprenda a [construir uma aplicação](service-fabric-tutorial-create-dotnet-app.md) com um ASP.NET Core front-end e um back-end imponente, [implemente a aplicação](service-fabric-tutorial-deploy-app-to-party-cluster.md) para um cluster, [configure CI/CD,](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)e [configure monitorização e diagnósticos.](service-fabric-tutorial-monitoring-aspnet.md)

Ou, experimente um dos seguintes passeios e crie o seu primeiro...
- [C# Serviços fiáveis no Windows](service-fabric-reliable-services-quick-start.md) 
- [C# Serviço de Atores Fiáveis no Windows](service-fabric-reliable-actors-get-started.md) 
- [Serviço executável de hóspedes no Windows](quickstart-guest-app.md) 
- [Aplicação de contentor do Windows](service-fabric-get-started-containers.md) 

Também pode estar interessado em experimentar as [nossas aplicações](https://aka.ms/servicefabricsamples)de amostra.

## <a name="have-questions-or-feedback--need-to-report-an-issue"></a>Tem perguntas ou comentários?  Precisa reportar um problema?
Leia através [de perguntas comuns](service-fabric-common-questions.md) e encontre respostas sobre o que o Tecido de Serviço pode fazer e como deve ser usado.

[As opções](service-fabric-support.md) de suporte listam fóruns em StackOverflow e MSDN para fazer perguntas, bem como opções para reportar problemas, obter apoio e submeter feedback do produto.

## <a name="the-application-project"></a>O projeto de candidatura
Cada nova aplicação inclui um projeto de candidatura. Pode haver um ou dois projetos adicionais, dependendo do tipo de serviço escolhido.

O projeto de candidatura consiste em:

* Um conjunto de referências aos serviços que compõem a sua aplicação.
* Três perfis de publicação (1-Nó Local, 5-Nó Local e Cloud) que pode usar para manter preferências para trabalhar com diferentes ambientes -- tais como preferências relacionadas com um ponto final de cluster e se realizar implementações de upgrade por padrão.
* Três ficheiros de parâmetros de aplicação (o mesmo que acima) que pode utilizar para manter configurações de aplicações específicas do ambiente, como o número de divisórias para criar para um serviço. Saiba como configurar a [sua aplicação para vários ambientes.](service-fabric-manage-multiple-environment-app-configuration.md)
* Um script de implantação que pode utilizar para implantar a sua aplicação a partir da linha de comando ou como parte de um pipeline automatizado de integração e implantação contínua. Saiba mais sobre a implementação de [aplicações utilizando o PowerShell](service-fabric-deploy-remove-applications.md).
* O manifesto de candidatura, que descreve a aplicação. Pode encontrar o manifesto sob a pasta ApplicationPackageRoot. Saiba mais sobre os manifestos de [aplicação e serviço.](service-fabric-application-model.md)



## <a name="learn-more-about-the-programming-models"></a>Saiba mais sobre os modelos de programação
O Service Fabric oferece várias formas de escrever e gerir os seus serviços.  Aqui está a visão geral e informação conceptual sobre [serviços apátridas e apátridas fiáveis,](service-fabric-reliable-services-introduction.md) [Atores Fiáveis,](service-fabric-reliable-actors-introduction.md) [contentores,](service-fabric-containers-overview.md) [executáveis de hóspedes,](service-fabric-guest-executables-introduction.md)e [apátridas e apátridas ASP.NET Serviços Core.](service-fabric-reliable-services-communication-aspnetcore.md)

## <a name="learn-about-service-communication"></a>Conheça a comunicação de serviço
Uma aplicação De Tecido de Serviço é composta por diferentes serviços, onde cada serviço realiza uma tarefa especializada. Estes serviços podem comunicar entre si e pode haver aplicações de clientes fora do cluster que se ligam e comunicam com serviços. Saiba como configurar a [comunicação com e entre os seus serviços](service-fabric-connect-and-communicate-with-services.md) em Tecido de Serviço. 

## <a name="learn-about-configuring-application-security"></a>Saiba mais sobre configurar a segurança da aplicação
Pode garantir aplicações que estejam a funcionar no cluster em diferentes contas de utilizador. O Service Fabric também ajuda a proteger os recursos utilizados pelas aplicações no momento da implementação ao abrigo das contas de utilizador -- por exemplo, ficheiros, diretórios e certificados. Isto torna as aplicações de execução, mesmo num ambiente partilhado, mais seguras umas das outras.  Saiba como configurar as políticas de [segurança para a sua aplicação.](service-fabric-application-runas-security.md)

A sua aplicação pode conter informações sensíveis, tais como cordas de ligação de armazenamento, palavras-passe ou outros valores que não devem ser tratados em texto simples. Saiba como [gerir segredos na sua aplicação.](service-fabric-application-secret-management.md)

## <a name="learn-about-the-application-lifecycle"></a>Conheça o ciclo de vida da aplicação
Tal como acontece com outras plataformas, uma aplicação Service Fabric passa geralmente pelas seguintes fases: design, desenvolvimento, teste, implementação, upgrade, manutenção e remoção. [Este artigo](service-fabric-application-lifecycle.md) fornece uma visão geral das APIs e como são utilizadas pelas diferentes funções ao longo das fases do ciclo de vida da aplicação do Tecido de Serviço.

## <a name="next-steps"></a>Passos seguintes
- [Crie um cluster Windows em Azure.](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
- Visualize o seu cluster, incluindo aplicações implementadas e layout físico, com [service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
- [Versão e atualizar os seus serviços](service-fabric-application-upgrade-tutorial.md)


