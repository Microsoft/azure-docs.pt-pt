---
title: Como funciona o Azure Dev Spaces
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Descreve os processos que alimentam os Espaços Azure Dev
keywords: Espaços Azure Dev, Espaços Dev, Docker, Kubernetes, Azure, AKS, Serviço Azure Kubernetes, contentores
ms.openlocfilehash: 99b0b3309d115b450bfca94871b6defd885349fe
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80234976"
---
# <a name="how-azure-dev-spaces-works"></a>Como funciona o Azure Dev Spaces

Desenvolver uma aplicação Kubernetes pode ser um desafio. Precisas de ficheiros de configuração do Docker e da Kubernetes. Você precisa descobrir como testar a sua aplicação localmente e interagir com outros serviços dependentes. Você pode precisar lidar com o desenvolvimento e testar vários serviços de uma vez e com uma equipe de desenvolvedores.

A Azure Dev Spaces oferece-lhe múltiplas formas de iterar e depurar rapidamente aplicações kubernetes e colaborar com a sua equipa. Este artigo descreve o que a Azure Dev Spaces pode fazer e como funciona.

## <a name="rapidly-iterate-and-debug-your-kubernetes-application"></a>Iterar rapidamente e desinbugiar a sua aplicação Kubernetes

A Azure Dev Spaces reduz o esforço de desenvolvimento, teste e iteração da sua aplicação Kubernetes no contexto do seu cluster AKS. Esta redução de esforço permite que os desenvolvedores se concentrem na lógica empresarial das suas aplicações e não configurar os seus serviços para funcionar em Kubernetes.

### <a name="connect-your-development-machine-to-aks"></a>Ligue a sua máquina de desenvolvimento ao AKS

Com a Azure Dev Spaces, pode ligar o seu computador de desenvolvimento ao seu cluster AKS, permitindo-lhe executar e depurar código no seu computador de desenvolvimento como se estivesse a funcionar no cluster. A Azure Dev Spaces redireciona o tráfego entre o seu cluster AKS conectado, executando uma cápsula no seu cluster que funciona como um agente remoto para redirecionar o tráfego entre a sua máquina de desenvolvimento e o cluster. Esta redirecionamento de tráfego permite que o código no seu computador de desenvolvimento e serviços em execução no seu cluster AKS se comuniquem como se estivessem no mesmo cluster AKS. Para obter mais informações sobre a ligação da sua máquina de desenvolvimento ao AKS, consulte como funciona o seu computador de [desenvolvimento ao seu cluster AKS][how-it-works-connect].

### <a name="run-your-code-in-aks"></a>Execute o seu código no AKS

Além de redirecionar o tráfego entre o seu computador de desenvolvimento e o seu cluster AKS, com o Azure Dev Spaces pode configurar e executar rapidamente o seu código diretamente no AKS. Com o Visual Studio, visual studio code, ou os espaços Azure Dev CLI, os espaços Azure Dev carregarão o seu código para cluster, em seguida, construí-lo e executá-lo. Os espaços Azure Dev também podem sincronizar inteligentemente as alterações de código e reiniciar o seu serviço para refletir alterações conforme necessário. Durante a execução do seu código, os registos de construção e os vestígios httpsão transmitidos de volta para o seu cliente para que possa monitorizar o progresso e diagnosticar quaisquer problemas. Também pode utilizar a Azure Dev Spaces, para anexar o debugger em Visual Studio e Visual Studio Code a Java, Node.js e serviços .NET Core. Para mais informações, consulte como funciona um projeto para a [Azure Dev Spaces][how-it-works-prep], como funciona o seu código com a [Azure Dev Spaces][how-it-works-up], e como funciona a [depuração remota do seu código com a Azure Dev Spaces.][how-it-works-remote-debugging]

## <a name="team-development"></a>Desenvolvimento em equipa

A Azure Dev Spaces ajuda as equipas a trabalhar em produtivo na sua aplicação no mesmo cluster AKS sem serem disruptivas.

### <a name="intelligent-routing-between-dev-spaces"></a>Encaminhamento inteligente entre espaços de v

Com a Azure Dev Spaces, uma equipa pode partilhar um único cluster AKS executando uma aplicação nativa de nuvem e criar espaços de dev isolados onde a equipa pode desenvolver, testar e depurar sem interferir com os outros espaços de dev. Uma versão de base da aplicação funciona num espaço root dev. Os membros da equipa criam então espaços de dev para crianças independentes com base no espaço de raiz para desenvolvimento, teste e depuração de alterações na aplicação. Através das capacidades de encaminhamento em Espaços Dev, os espaços de dev para crianças podem encaminhar pedidos entre serviços em funcionamento no espaço de dev infantil e o espaço de dev dos pais. Este encaminhamento permite que os desenvolvedores executem a sua própria versão de um serviço enquanto reutilizam os serviços dependentes do espaço dos pais. Cada espaço infantil tem o seu próprio URL único, que pode ser partilhado e acedido por outros para colaboração. Para obter mais informações sobre como funciona o encaminhamento em Espaços Azure Dev, veja como funciona o [encaminhamento com os Espaços Azure Dev.][how-it-works-routing]

### <a name="live-testing-an-open-pull-request"></a>Teste ao vivo um pedido de puxão aberto

Também pode utilizar ações GitHub com espaços Azure Dev para testar alterações na sua aplicação num pedido de pull diretamente no seu cluster antes de se fundir. A Azure Dev Spaces pode implementar automaticamente uma versão de revisão da aplicação para o seu cluster, permitindo ao autor e outros membros da equipa rever as alterações no contexto de toda a aplicação. Utilizando as capacidades de encaminhamento da Azure Dev Spaces, esta versão de revisão da aplicação também é implementada para o seu cluster sem afetar outros espaços de dev. Todas estas capacidades permitem-lhe aprovar e fundir com confiança os pedidos de pull. Para ver um exemplo de Ações GitHub e Espaços Azure Dev, consulte [GitHub Actions & Azure Kubernetes Service][pr-flow].

## <a name="next-steps"></a>Passos seguintes

Para começar a ligar a sua máquina de desenvolvimento local ao seu cluster AKS, consulte Connect a sua máquina de [desenvolvimento a um cluster AKS][connect].

Para começar a usar o Azure Dev Spaces para desenvolvimento de equipas, veja o desenvolvimento da [equipa em Azure Dev Spaces][quickstart-team] rapidamente.

[connect]: how-to/connect.md
[how-it-works-connect]: how-dev-spaces-works-connect.md
[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[how-it-works-up]: how-dev-spaces-works-up.md
[pr-flow]: how-to/github-actions.md
[quickstart-team]: quickstart-team-development.md
[routing]: #team-development