---
title: Como funciona o Azure Dev Spaces
services: azure-dev-spaces
ms.date: 06/02/2020
ms.topic: conceptual
description: Descreve os processos que alimentam a Azure Dev Spaces
keywords: Espaços Azure Dev, Dev Spaces, Docker, Kubernetes, Azure, AKS, Serviço Azure Kubernetes, contentores
ms.openlocfilehash: 40125b07029c78b4e8858d54058aabe038109057
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87837419"
---
# <a name="how-azure-dev-spaces-works"></a>Como funciona o Azure Dev Spaces

Desenvolver uma aplicação Kubernetes pode ser um desafio. Precisas de ficheiros de configuração do Docker e do Kubernetes. Precisa de descobrir como testar a sua aplicação localmente e interagir com outros serviços dependentes. Poderá ter de lidar com o desenvolvimento e teste de múltiplos serviços ao mesmo tempo e com uma equipa de desenvolvedores.

O Azure Dev Spaces fornece-lhe múltiplas formas de iterar e depurar rapidamente aplicações kubernetes e colaborar com a sua equipa. Este artigo descreve o que a Azure Dev Spaces pode fazer e como funciona.

## <a name="rapidly-iterate-and-debug-your-kubernetes-application"></a>Iterar e depurar rapidamente a sua aplicação Kubernetes

A Azure Dev Spaces reduz o esforço de desenvolvimento, teste e iteração da sua aplicação Kubernetes no contexto do seu cluster AKS. Esta redução de esforço permite que os desenvolvedores se concentrem na lógica de negócio das suas aplicações e não configuram os seus serviços para funcionar em Kubernetes.

### <a name="local-process-with-kubernetes"></a>Processo Local com o Kubernetes

Com o Processo Local com Kubernetes, pode ligar o seu computador de desenvolvimento ao seu cluster Kubernetes, permitindo-lhe executar e depurar código no seu computador de desenvolvimento como se estivesse a funcionar no cluster. O Azure Dev Spaces redireciona o tráfego entre o seu cluster conectado, executando uma cápsula no seu cluster que funciona como um agente remoto para redirecionar o tráfego entre o seu computador de desenvolvimento e o cluster. Esta reorientação de tráfego permite que o código no seu computador de desenvolvimento e serviços em execução no seu cluster se comunique como se estivessem no mesmo cluster. Para obter mais informações sobre a ligação do seu computador de desenvolvimento a um cluster Kubernetes, consulte [como funciona o Processo Local com kubernetes.][how-it-works-local-process-kubernetes]

### <a name="run-your-code-in-aks"></a>Executar o seu código em AKS

Além de redirecionar o tráfego entre o seu computador de desenvolvimento e o seu cluster AKS, com a Azure Dev Spaces pode configurar e executar rapidamente o seu código diretamente em AKS. Com o Visual Studio, Visual Studio Code ou o Azure Dev Spaces CLI, os espaços Azure Dev irão carregar o seu código para cluster, e depois construí-lo e executá-lo. Os espaços Azure Dev também podem sincronizar alterações de código inteligentemente e reiniciar o seu serviço para refletir as alterações necessárias. Durante a execução do seu código, os registos de construção e os vestígios HTTP são transmitidos de volta para o seu cliente para que possa monitorizar o progresso e diagnosticar quaisquer problemas. Também pode utilizar os serviços Azure Dev Spaces, para anexar o depurador em Visual Studio e Visual Studio Code a Java, Node.js e serviços .NET Core. Para mais informações, veja [como funciona a preparação de um projeto para a Azure Dev Spaces][how-it-works-prep], Como funciona o seu código com a [Azure Dev Spaces][how-it-works-up], e como funciona a [depuração remota do seu código com a Azure Dev Spaces.][how-it-works-remote-debugging]

## <a name="team-development"></a>Desenvolvimento em equipa

A Azure Dev Spaces ajuda as equipas a trabalhar produtivamente na sua aplicação no mesmo cluster AKS sem serem disruptivas.

### <a name="intelligent-routing-between-dev-spaces"></a>Encaminhamento inteligente entre espaços dev

Com o Azure Dev Spaces, uma equipa pode partilhar um único cluster AKS executando uma aplicação nativa em nuvem e criar espaços dev isolados onde a equipa pode desenvolver, testar e depurar sem interferir com os outros espaços dev. Uma versão de base da aplicação corre num espaço de dev raiz. Os membros da equipa criam então espaços independentes de dev para crianças com base no espaço de raiz para desenvolvimento, teste e depuragem de alterações à aplicação. Através das capacidades de encaminhamento em Espaços Dev, os espaços de dev infantil podem encaminhar pedidos entre os serviços que executam no espaço dev infantil e o espaço dev dos pais. Este encaminhamento permite que os desenvolvedores executem a sua própria versão de um serviço enquanto reutilizam os serviços dependentes do espaço principal. Cada espaço infantil tem o seu próprio URL único, que pode ser partilhado e acedido por outros para colaboração. Para obter mais informações sobre como funciona o encaminhamento em Azure Dev Spaces, consulte [como funciona o encaminhamento com a Azure Dev Spaces][how-it-works-routing].

### <a name="live-testing-an-open-pull-request"></a>Teste ao vivo um pedido de puxar aberto

Também pode utilizar As Ações GitHub com Espaços Azure Dev para testar alterações à sua aplicação num pedido de puxar diretamente no seu cluster antes de se fundir. O Azure Dev Spaces pode implementar automaticamente uma versão de revisão da aplicação para o seu cluster, permitindo ao autor, bem como a outros membros da equipa, rever as alterações no contexto de toda a aplicação. Utilizando as capacidades de encaminhamento do Azure Dev Spaces, esta versão de revisão da aplicação também é implantada no seu cluster sem afetar outros espaços dev. Todas estas capacidades permitem-lhe aprovar e fundir com confiança pedidos de pull. Para ver um exemplo de GitHub Actions and Azure Dev Spaces, consulte [gitHub actions & Serviço Azure Kubernetes][pr-flow].

## <a name="next-steps"></a>Passos seguintes

Para começar a ligar o seu computador de desenvolvimento local ao seu cluster AKS, consulte [Ligue o seu computador de desenvolvimento a um cluster AKS.][connect]

Para começar a usar a Azure Dev Spaces para o desenvolvimento de equipas, consulte o desenvolvimento da [equipa em Azure Dev Spaces][quickstart-team] quickstart.

[connect]: https://code.visualstudio.com/docs/containers/local-process-kubernetes
[how-it-works-local-process-kubernetes]: /visualstudio/containers/overview-local-process-kubernetes
[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[how-it-works-up]: how-dev-spaces-works-up.md
[pr-flow]: how-to/github-actions.md
[quickstart-team]: quickstart-team-development.md
[routing]: #team-development