---
title: Descrição geral do Service Fabric e contentores | Documentos da Microsoft
description: Uma visão geral do Service Fabric e o uso de contentores para implementar aplicações de microsserviços. Este artigo fornece uma visão geral de como os contentores podem ser usados e as capacidades disponíveis no Service Fabric.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: c98b3fcb-c992-4dd9-b67d-2598a9bf8aab
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/8/2018
ms.author: aljo, msfussell
ms.openlocfilehash: 5344f34e0e35d4d47b032b660726a4d70a4f1987
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2019
ms.locfileid: "56807019"
---
# <a name="service-fabric-and-containers"></a>Service Fabric e contentores

## <a name="introduction"></a>Introdução

O Azure Service Fabric é uma plataforma de sistemas distribuídos que facilita o empacotamento, a implementação e a gestão de microsserviços e contentores dimensionáveis e fiáveis.

O Service Fabric é da Microsoft [orquestrador de contentores](service-fabric-cluster-resource-manager-introduction.md) para implementar microsserviços em clusters de máquinas. Benefícios do Service Fabric das lições aprendidas durante sua anos a executar os serviços da Microsoft em grande escala.

Os microsserviços podem ser desenvolvidos de muitas formas, desde a utilização dos [modelos de programação do Service Fabric](service-fabric-choose-framework.md) e do [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md) até à implementação de [qualquer código à sua escolha](service-fabric-guest-executables-introduction.md). Ou, se só quer [implementar e gerir contentores](service-fabric-containers-overview.md), Service Fabric também é uma ótima opção.

Por predefinição, o Service Fabric implementa e ativa estes serviços como processos. Processos fornecem a ativação mais rápida e a mais alta utilização de densidade dos recursos num cluster. Service Fabric também pode implementar serviços nas imagens de contentor. Também pode combinar serviços em processos e serviços em contentores, no mesmo aplicativo.

Para iniciar de imediato e experimentar os contentores no Service Fabric, tente um início rápido, tutorial ou exemplo:  

[Quickstart: Implementar uma aplicação de contentor do Linux no Service Fabric](service-fabric-quickstart-containers-linux.md)  
[Quickstart: Implementar uma aplicação de contentores do Windows no Service Fabric](service-fabric-quickstart-containers.md)  
[Crie contentores de uma aplicação .NET existente](service-fabric-host-app-in-a-container.md)  
[Exemplos de Contentor do Service Fabric](https://azure.microsoft.com/resources/samples/service-fabric-containers/)  

## <a name="what-are-containers"></a>Quais são os contentores

Contentores de resolvem o problema das aplicações em execução com confiança em ambientes de computação diferentes, fornecendo um imutável ambiente para o aplicativo seja executado. Contentores de encapsular um aplicativo e todas as respetivas dependências, como bibliotecas e ficheiros de configuração, na sua própria caixa isolada que contém tudo o que for necessário para executar o software dentro do contentor. Onde quer que o contentor é executado, o aplicativo em seu interior sempre tem tudo o que ele precisa ser executado, como as versões corretas do seu bibliotecas dependentes, quaisquer ficheiros de configuração e qualquer outra coisa, precisa ser executado.

Os contentores executados diretamente sobre o kernel e tem um modo de exibição isolado do sistema de ficheiros e outros recursos. Uma aplicação num contentor não tem conhecimento de quaisquer outros aplicativos ou processos estejam fora do contentor. Cada aplicativo e suas bibliotecas de tempo de execução, dependências e do sistema são executados dentro de um contentor com acesso completo e privado para a vista de contentor próprio isolado do sistema operativo. Para além de tornar fácil fornecer todas as dependências do seu aplicativo, precisa ser executado em diferentes ambientes de computação, segurança e isolamento de recursos são vantagens importantes da utilização de contentores com o Service Fabric, qual é executado; caso contrário, serviços num processo.

Em comparação com as máquinas virtuais, contentores têm as seguintes vantagens:

* **Pequenas**: Contentores de utilizarem um espaço de armazenamento única e versões de camada e as atualizações para aumentar a eficiência.
* **Fast**: Contentores não é preciso inicializar um sistema operativo inteiro, para que podem começar a muito mais rapidamente, normalmente em segundos.
* **Portabilidade**: Uma imagem de aplicação contentorizada pode ser convertida para serem executados na cloud, no local, dentro de máquinas virtuais, ou diretamente em máquinas físicas.
* **Governação de recursos**: Um contentor pode limitar os recursos físicos que ele possa consumir no respetivo anfitrião.

### <a name="container-types-and-supported-environments"></a>Tipos de contentor e Ambientes suportados

Service Fabric suporta contentores no Linux e Windows e suporta o modo de isolamento de Hyper-V no Windows.

#### <a name="docker-containers-on-linux"></a>Contentores do docker no Linux

Docker fornece APIs para criar e gerir contentores por cima de contentores de kernel do Linux. Hub do docker fornece repositório central para armazenar e recuperar imagens de contentor.
Para obter um tutorial baseado em Linux, veja [criar a sua primeira aplicação de contentor do Service Fabric no Linux](service-fabric-get-started-containers-linux.md).

#### <a name="windows-server-containers"></a>Containers Windows Server

Windows Server 2016 fornece dois tipos diferentes de contentores que diferem por nível de isolamento. Contentores do Windows Server e os contentores do Docker são semelhantes porque têm espaço de nomes e o ficheiro de isolamento de sistema, enquanto o kernel de partilha com o anfitrião em que são executadas. No Linux, esse isolamento tradicionalmente fornecido pela unidade e espaços de nomes e contentores do Windows Server se comportam da mesma forma.

Os contentores do Windows com suporte de Hyper-V oferecem mais segurança e isolamento, porque não existir um contentor compartilha o kernel do sistema operacional com qualquer outro contentor ou com o anfitrião. Com este nível de isolamento de segurança mais elevado, contentores de Hyper-V ativada destinam-se cenários potencialmente hostis, multi-inquilinos.
Para obter um tutorial baseados em Windows, consulte [criar a sua primeira aplicação de contentor do Service Fabric no Windows](service-fabric-get-started-containers.md).

A figura seguinte mostra os diferentes tipos de níveis de Virtualização e isolamento disponíveis.
![Plataforma do Service Fabric][Image1]

## <a name="scenarios-for-using-containers"></a>Cenários de utilização de contentores

Seguem-se exemplos típicos onde um contentor é uma boa opção:

* **IIS lift- and -shift**: Pode colocar um existente [ASP.NET MVC](https://www.asp.net/mvc) aplicação num contentor, em vez de migrar para ASP.NET Core. Estas aplicações de ASP.NET MVC dependem de serviços de informação Internet (IIS). Pode empacotar esses aplicativos nas imagens de contentor a partir da imagem pré-criada do IIS e implementá-las com o Service Fabric. Ver [imagens de contentor no Windows Server](https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-server) para obter informações sobre contentores do Windows.

* **Misturar contentores e microsserviços de Service Fabric**: Utilize uma imagem de contentor existente para a parte da sua aplicação. Por exemplo, poderá utilizar o [contentor NGINX](https://hub.docker.com/_/nginx/) para web front-end da aplicação e serviços com estado para a computação de back-end mais intensiva.

* **Reduzir o impacto dos serviços de "vizinhos ruidosos"**: Pode utilizar a capacidade de governação de recursos de contentores para restringir os recursos utilizados por um serviço num anfitrião. Se os serviços podem consumir muitos recursos e afetar o desempenho de outras pessoas (como uma operação de longa execução, tipo de consulta), considere colocar esses serviços em contentores que tenham a governação de recursos.

## <a name="service-fabric-support-for-containers"></a>Suporte do Service Fabric para contentores

O Service Fabric suporta a implementação de contentores do Docker no Linux e contentores do Windows Server no Windows Server 2016, juntamente com suporte para o modo de isolamento de Hyper-V. 

O Service Fabric fornece um [modelo de aplicativo](service-fabric-application-model.md) em que um contentor representa um host de aplicativo no qual serviço várias réplicas são colocadas. O Service Fabric também suporta um [cenário executáveis de convidado](service-fabric-guest-executables-introduction.md) em que não use o modelos de programação do Service Fabric incorporadas, mas em vez disso, a empacotar um aplicativo existente, escrito usando qualquer linguagem ou arquitetura, dentro um contentor. Este cenário é o caso de utilização comuns para contentores.

Também pode executar [serviços do Service Fabric dentro de um contentor](service-fabric-services-inside-containers.md). Suporte para serviços do Service Fabric em execução no interior de contentores está limitado.

O Service Fabric fornece várias capacidades de contentores que o ajudam a criarem aplicações que são compostas por microsserviços em contentores, tais como:

* Implantação de imagem de contentor e a ativação.
* Governação de recursos, incluindo a definição de valores de recursos por predefinição em clusters do Azure.
* Autenticação do repositório.
* Porta do contentor para mapeamento de portas do anfitrião.
* Deteção de contentor para contentor e a comunicação.
* Capacidade de configurar e definir variáveis de ambiente.
* Capacidade de definir as credenciais de segurança no contentor.
* Uma opção de diferentes modos de funcionamento em rede para contentores.

Para uma visão geral abrangente do contentor de suporte no Azure, tais como criar um cluster de Kubernetes com o serviço de Kubernetes do Azure, como para criar um registo privado do Docker no Azure Container Registry e muito mais, consulte [do Azure para contentores](https://docs.microsoft.com/azure/containers/).

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, aprendeu sobre o suporte do que Service Fabric fornece para contentores em execução. Em seguida, podemos abordará exemplos de cada um dos recursos para lhe mostrar como usá-los.

[Criar a sua primeira aplicação de contentor do Service Fabric no Linux](service-fabric-get-started-containers-linux.md)  
[Criar a sua primeira aplicação de contentor do Service Fabric no Windows](service-fabric-get-started-containers.md)  
[Saiba mais sobre os contentores do Windows](https://docs.microsoft.com/virtualization/windowscontainers/about/)

[Image1]: media/service-fabric-containers/Service-Fabric-Types-of-Isolation.png