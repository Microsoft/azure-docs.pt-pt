---
title: Visão geral de Service Fabric e contêineres | Microsoft Docs
description: Uma visão geral de Service Fabric e o uso de contêineres para implantar aplicativos de microatendimento. Este artigo fornece uma visão geral de como os contêineres podem ser usados e os recursos disponíveis no Service Fabric.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: c98b3fcb-c992-4dd9-b67d-2598a9bf8aab
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/8/2018
ms.author: atsenthi
ms.openlocfilehash: 2ed3a9d4b1ec219d22a9e01e7acec5d7e950289b
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599757"
---
# <a name="service-fabric-and-containers"></a>Service Fabric e contêineres

## <a name="introduction"></a>Introdução

O Azure Service Fabric é uma plataforma de sistemas distribuídos que facilita o empacotamento, a implementação e a gestão de microsserviços e contentores dimensionáveis e fiáveis.

Service Fabric é o orquestrador de [contêiner](service-fabric-cluster-resource-manager-introduction.md) da Microsoft para implantar microserviços em um cluster de computadores. Service Fabric benefícios das lições aprendidas durante seus anos executando serviços na Microsoft em grande escala.

Os microsserviços podem ser desenvolvidos de muitas formas, desde a utilização dos [modelos de programação do Service Fabric](service-fabric-choose-framework.md) e do [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md) até à implementação de [qualquer código à sua escolha](service-fabric-guest-executables-introduction.md). Ou, se você apenas deseja [implantar e gerenciar contêineres](service-fabric-containers-overview.md), Service Fabric também é uma ótima opção.

Por padrão, o Service Fabric implanta e ativa esses serviços como processos. Os processos fornecem a ativação mais rápida e o uso de densidade mais alto dos recursos em um cluster. Service Fabric também pode implantar serviços em imagens de contêiner. Você também pode misturar serviços em processos e serviços em contêineres, no mesmo aplicativo.

Para ir direto e experimentar contêineres em Service Fabric, experimente um início rápido, tutorial ou exemplo:  

[Quickstart: Implantar um aplicativo de contêiner do Linux para Service Fabric](service-fabric-quickstart-containers-linux.md)  
[Quickstart: Implantar um aplicativo de contêiner do Windows para Service Fabric](service-fabric-quickstart-containers.md)  
[Colocar um aplicativo .NET existente em um contêiner](service-fabric-host-app-in-a-container.md)  
[Exemplos de Contentor do Service Fabric](https://azure.microsoft.com/resources/samples/service-fabric-containers/)  

## <a name="what-are-containers"></a>O que são contêineres

Os contêineres resolvem o problema da execução de aplicativos de forma confiável em ambientes de computação diferentes, fornecendo um ambiente imutável para que o aplicativo seja executado no. Os contêineres encapsulam um aplicativo e todas as suas dependências, como bibliotecas e arquivos de configuração, em sua própria ' caixa ' isolada que contém tudo o que é necessário para executar o software dentro do contêiner. Onde quer que o contêiner seja executado, o aplicativo dentro dele sempre tem tudo o que precisa para ser executado, como as versões certas de suas bibliotecas dependentes, quaisquer arquivos de configuração e qualquer outra coisa que precise executar.

Os contêineres são executados diretamente na parte superior do kernel e têm uma exibição isolada do sistema de arquivos e de outros recursos. Um aplicativo em um contêiner não tem nenhum conhecimento de outros aplicativos ou processos fora de seu contêiner. Cada aplicativo e seu tempo de execução, suas dependências e suas bibliotecas de sistema são executados dentro de um contêiner com acesso privado e completo à exibição isolada própria do contêiner do sistema operacional. Além de facilitar o fornecimento de todas as dependências do seu aplicativo, ele precisa ser executado em diferentes ambientes de computação, segurança e isolamento de recursos são benefícios importantes do uso de contêineres com Service Fabric – que, de outra forma, executa serviços em um Process.

Em comparação com as máquinas virtuais, os contêineres têm as seguintes vantagens:

* **Pequeno**: Os contêineres usam um único espaço de armazenamento e versões de camada e atualizações para aumentar a eficiência.
* **Rápido**: Os contêineres não precisam inicializar um sistema operacional inteiro, para que possam começar muito mais rapidamente – normalmente em segundos.
* **Portabilidade**: Uma imagem de aplicativo em contêiner pode ser transportada para ser executada na nuvem, local, dentro de máquinas virtuais ou diretamente em máquinas físicas.
* **Governança de recursos**: Um contêiner pode limitar os recursos físicos que ele pode consumir em seu host.

### <a name="container-types-and-supported-environments"></a>Tipos de contêiner e ambientes com suporte

O Service Fabric dá suporte a contêineres no Linux e no Windows e dá suporte ao modo de isolamento do Hyper-V no Windows.

#### <a name="docker-containers-on-linux"></a>Contêineres do Docker no Linux

O Docker fornece APIs para criar e gerenciar contêineres sobre contêineres de kernel do Linux. O Hub do Docker fornece um repositório central para armazenar e recuperar imagens de contêiner.
Para um tutorial baseado em Linux, consulte [criar seu primeiro aplicativo de contêiner de Service Fabric no Linux](service-fabric-get-started-containers-linux.md).

#### <a name="windows-server-containers"></a>Containers Windows Server

O Windows Server 2016 fornece dois tipos diferentes de contêineres que diferem por nível de isolamento. Os contêineres do Windows Server e os contêineres do Docker são semelhantes porque têm o isolamento de namespace e sistema de arquivos, enquanto compartilham o kernel com o host no qual estão sendo executados. No Linux, esse isolamento tradicionalmente foi fornecido por cgroups e namespaces, e os contêineres do Windows Server se comportam da mesma forma.

Os contêineres do Windows com suporte a Hyper-V fornecem mais isolamento e segurança porque nenhum contêiner compartilha o kernel do sistema operacional com qualquer outro contêiner ou com o host. Com esse nível mais alto de isolamento de segurança, os contêineres habilitados para Hyper-V são direcionados a cenários potencialmente hosts de vários locatários.
Para um tutorial baseado no Windows, consulte [criar seu primeiro aplicativo de contêiner de Service Fabric no Windows](service-fabric-get-started-containers.md).

A figura a seguir mostra os diferentes tipos de virtualização e níveis de isolamento disponíveis.
![Plataforma Service Fabric][Image1]

## <a name="scenarios-for-using-containers"></a>Cenários para usar contêineres

Aqui estão exemplos típicos em que um contêiner é uma boa opção:

* **Deslocamento do IIS**: Você pode colocar um aplicativo [MVC ASP.net](https://www.asp.net/mvc) existente em um contêiner em vez de migrá-lo para ASP.NET Core. Esses aplicativos ASP.NET MVC dependem do Serviços de Informações da Internet (IIS). Você pode empacotar esses aplicativos em imagens de contêiner da imagem do IIS precriada e implantá-los com Service Fabric. Consulte [imagens de contêiner no Windows Server](https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-server) para obter informações sobre contêineres do Windows.

* **Misturar contêineres e Service Fabric microservices**: Use uma imagem de contêiner existente para parte do seu aplicativo. Por exemplo, você pode usar o [contêiner Nginx](https://hub.docker.com/_/nginx/) para o front-end da Web do seu aplicativo e serviços com estado para a computação de back-end mais intensa.

* **Reduza o impacto dos serviços "vizinhos com ruído"** : Você pode usar a capacidade de governança de recursos de contêineres para restringir os recursos que um serviço usa em um host. Se os serviços puderem consumir muitos recursos e afetarem o desempenho de outros (como uma operação de longa execução, semelhante a consulta), considere colocar esses serviços em contêineres que têm governança de recursos.

## <a name="service-fabric-support-for-containers"></a>Suporte Service Fabric para contêineres

O Service Fabric dá suporte à implantação de contêineres do Docker no Linux e contêineres do Windows Server no Windows Server 2016, juntamente com o suporte para o modo de isolamento do Hyper-V. 

Service Fabric fornece um [modelo de aplicativo](service-fabric-application-model.md) no qual um contêiner representa um host de aplicativo no qual várias réplicas de serviço são colocadas. O Service Fabric também dá suporte a um [cenário de executável convidado](service-fabric-guest-executables-introduction.md) no qual você não usa os modelos de programação de Service Fabric internos, mas, em vez disso, empacota um aplicativo existente, escrito usando qualquer linguagem ou estrutura, dentro de um contêiner. Esse cenário é o caso de uso comum para contêineres.

Você também pode executar [Service Fabric serviços dentro de um contêiner](service-fabric-services-inside-containers.md). O suporte para a execução de serviços de Service Fabric dentro de contêineres é limitado no momento.

O Service Fabric fornece vários recursos de contêiner que ajudam você a criar aplicativos compostos por microservices em contêineres, como:

* Implantação e ativação de imagem de contêiner.
* Governança de recursos, incluindo a definição de valores de recurso por padrão em clusters do Azure.
* Autenticação de repositório.
* Porta do contêiner para o mapeamento de porta do host.
* Descoberta e comunicação de contêiner para contêiner.
* Capacidade de configurar e definir variáveis de ambiente.
* Capacidade de definir credenciais de segurança no contêiner.
* Uma opção de modos de rede diferentes para contêineres.

Para obter uma visão geral abrangente do suporte a contêiner no Azure, por exemplo, como criar um cluster kubernetes com o serviço kubernetes do Azure, como criar um registro do Docker privado no registro de contêiner do Azure e muito mais, consulte [Azure para contêineres](https://docs.microsoft.com/azure/containers/).

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, você aprendeu sobre o suporte Service Fabric fornece para contêineres em execução. Em seguida, veremos exemplos de cada um dos recursos para mostrar como usá-los.

[Criar seu primeiro aplicativo de contêiner de Service Fabric no Linux](service-fabric-get-started-containers-linux.md)  
[Criar seu primeiro aplicativo de contêiner de Service Fabric no Windows](service-fabric-get-started-containers.md)  
[Saiba mais sobre contêineres do Windows](https://docs.microsoft.com/virtualization/windowscontainers/about/)

[Image1]: media/service-fabric-containers/Service-Fabric-Types-of-Isolation.png