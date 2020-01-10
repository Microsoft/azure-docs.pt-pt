---
title: Próximos passos de criação do projeto do Service Fabric
description: Saiba mais sobre o projeto de aplicativo que você acabou de criar no Visual Studio.  Saiba como criar serviços usando tutoriais e saiba mais sobre como desenvolver serviços para Service Fabric.
ms.topic: conceptual
ms.date: 12/07/2017
ms.openlocfilehash: e8fc105657bda6114851f4819be4658926ad621c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75349400"
---
# <a name="your-service-fabric-application-and-next-steps"></a>O aplicativo Service Fabric e as próximas etapas
Seu aplicativo de Service Fabric do Azure foi criado. Este artigo descreve alguns tutoriais para experimentar, a composição de seu projeto, algumas informações sobre as quais você pode estar interessado e as próximas etapas possíveis.

## <a name="get-started-with-tutorials-walk-throughs-and-samples"></a>Introdução aos tutoriais, orientações e exemplos
Pronto para começar?  

Trabalhe no tutorial do aplicativo .NET. Saiba como [criar um aplicativo](service-fabric-tutorial-create-dotnet-app.md) com um ASP.NET Core front-end e um back-end com estado, [implantar o aplicativo](service-fabric-tutorial-deploy-app-to-party-cluster.md) em um cluster, [Configurar o CI/CD](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)e [Configurar o monitoramento e o diagnóstico](service-fabric-tutorial-monitoring-aspnet.md).

Ou então, experimente um dos seguintes passo a passo e crie seu primeiro...
- [C#Serviço de Reliable Services no Windows](service-fabric-reliable-services-quick-start.md) 
- [C#Serviço de Reliable Actors no Windows](service-fabric-reliable-actors-get-started.md) 
- [Serviço executável convidado no Windows](quickstart-guest-app.md) 
- [Aplicação de contentor do Windows](service-fabric-get-started-containers.md) 

Você também pode estar interessado em experimentar nossos [aplicativos de exemplo](https://aka.ms/servicefabricsamples).

## <a name="have-questions-or-feedback--need-to-report-an-issue"></a>Tem perguntas ou comentários?  Precisa relatar um problema?
Leia as [perguntas comuns](service-fabric-common-questions.md) e encontre respostas sobre o que Service Fabric pode fazer e como ela deve ser usada.

[Opções de suporte](service-fabric-support.md) lista fóruns no stackoverflow e no MSDN para fazer perguntas, bem como opções para relatar problemas, obter suporte e enviar comentários sobre o produto.

## <a name="the-application-project"></a>O projeto de aplicativo
Cada novo aplicativo inclui um projeto de aplicativo. Pode haver um ou dois projetos adicionais, dependendo do tipo de serviço escolhido.

O projeto de aplicativo consiste em:

* Um conjunto de referências para os serviços que compõem seu aplicativo.
* Três perfis de publicação (local de 1 nó, local de 5 nós e nuvem) que você pode usar para manter as preferências para trabalhar com ambientes diferentes, como preferências relacionadas a um ponto de extremidade de cluster e se as implantações de atualização devem ser executadas por padrão.
* Três arquivos de parâmetro de aplicativo (o mesmo que acima) que você pode usar para manter configurações de aplicativo específicas do ambiente, como o número de partições a serem criadas para um serviço. Saiba como [configurar seu aplicativo para vários ambientes](service-fabric-manage-multiple-environment-app-configuration.md).
* Um script de implantação que você pode usar para implantar seu aplicativo da linha de comando ou como parte de um pipeline de implantação e integração contínua automatizado. Saiba mais sobre como [implantar aplicativos usando o PowerShell](service-fabric-deploy-remove-applications.md).
* O manifesto do aplicativo, que descreve o aplicativo. Você pode encontrar o manifesto na pasta ApplicationPackageRoot Saiba mais sobre [manifestos de aplicativo e serviço](service-fabric-application-model.md).



## <a name="learn-more-about-the-programming-models"></a>Saiba mais sobre os modelos de programação
O Service Fabric oferece várias maneiras de escrever e gerenciar seus serviços.  Aqui está uma visão geral e informações conceituais sobre [Reliable Services com e sem](service-fabric-reliable-services-introduction.md)estado, [Reliable Actors](service-fabric-reliable-actors-introduction.md), [contêineres](service-fabric-containers-overview.md), [executáveis de convidado](service-fabric-guest-executables-introduction.md)e [serviços de ASP.NET Core com e sem](service-fabric-reliable-services-communication-aspnetcore.md)estado.

## <a name="learn-about-service-communication"></a>Saiba mais sobre a comunicação do serviço
Um aplicativo Service Fabric é composto por diferentes serviços, onde cada serviço executa uma tarefa especializada. Esses serviços podem se comunicar entre si e pode haver aplicativos cliente fora do cluster que se conectam e se comunicam com serviços. Saiba como [Configurar a comunicação com e entre seus serviços](service-fabric-connect-and-communicate-with-services.md) no Service Fabric. 

## <a name="learn-about-configuring-application-security"></a>Saiba mais sobre como configurar a segurança do aplicativo
Você pode proteger os aplicativos que estão em execução no cluster em contas de usuário diferentes. Service Fabric também ajuda a proteger os recursos que são usados por aplicativos no momento da implantação nas contas de usuário, por exemplo, arquivos, diretórios e certificados. Isso torna os aplicativos em execução, mesmo em um ambiente hospedado compartilhado, mais protegidos uns dos outros.  Saiba como [Configurar políticas de segurança para seu aplicativo](service-fabric-application-runas-security.md).

Seu aplicativo pode conter informações confidenciais, como cadeias de conexão de armazenamento, senhas ou outros valores que não devem ser tratados em texto sem formatação. Saiba como [gerenciar segredos em seu aplicativo](service-fabric-application-secret-management.md).

## <a name="learn-about-the-application-lifecycle"></a>Saiba mais sobre o ciclo de vida do aplicativo
Assim como em outras plataformas, um aplicativo Service Fabric geralmente passa pelas seguintes fases: design, desenvolvimento, teste, implantação, atualização, manutenção e remoção. [Este artigo](service-fabric-application-lifecycle.md) fornece uma visão geral das APIs e como elas são usadas pelas diferentes funções em todas as fases do ciclo de vida de Service Fabric aplicativo.

## <a name="next-steps"></a>Passos seguintes
- [Crie um cluster do Windows no Azure](service-fabric-tutorial-create-vnet-and-windows-cluster.md).
- Visualize o cluster, incluindo os aplicativos implantados e o layout físico, com [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
- [Versão e atualizar seus serviços](service-fabric-application-upgrade-tutorial.md)


