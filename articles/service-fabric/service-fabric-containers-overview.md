---
title: Visão geral do Tecido de Serviço e dos contentores
description: Uma visão geral do Tecido de Serviço e a utilização de contentores para implantar aplicações de microserviços. Este artigo fornece uma visão geral de como os recipientes podem ser usados e as capacidades disponíveis no Tecido de Serviço.
ms.topic: conceptual
ms.date: 7/9/2020
ms.openlocfilehash: cd0ec7dd2247fdd791df362fa34542178c17df4d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "87091662"
---
# <a name="service-fabric-and-containers"></a>Tecido de serviço e contentores

## <a name="introduction"></a>Introdução

O Azure Service Fabric é uma plataforma de sistemas distribuídos que facilita o empacotamento, a implementação e a gestão de microsserviços e contentores dimensionáveis e fiáveis.

Service Fabric é o orquestrador de [contentores](service-fabric-cluster-resource-manager-introduction.md) da Microsoft para a implantação de microserviços através de um conjunto de máquinas. O Service Fabric beneficia das lições aprendidas durante os seus anos de execução de serviços na Microsoft em grande escala.

Os microsserviços podem ser desenvolvidos de muitas formas, desde a utilização dos [modelos de programação do Service Fabric](service-fabric-choose-framework.md) e do [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md) até à implementação de [qualquer código à sua escolha](service-fabric-guest-executables-introduction.md). Ou, se quiser [apenas implantar e gerir contentores,](service-fabric-containers-overview.md)o Service Fabric também é uma ótima escolha.

Por predefinição, o Service Fabric implementa e ativa estes serviços como processos. Os processos proporcionam a ativação mais rápida e a maior densidade dos recursos num cluster. O Tecido de Serviço também pode implementar serviços em imagens de contentores. Também pode misturar serviços em processos, e serviços em contentores, na mesma aplicação.

Para entrar e experimentar recipientes em Service Fabric, experimente um arranque rápido, tutorial ou amostra:  

[Quickstart: Implementar uma aplicação de contentor linux para o tecido de serviço](service-fabric-quickstart-containers-linux.md)  
[Quickstart: Implementar uma aplicação de contentores Windows para o Tecido de Serviço](service-fabric-quickstart-containers.md)  
[Colocar uma aplicação .NET existente num contentor](service-fabric-host-app-in-a-container.md)  
[Exemplos de Contentor do Service Fabric](https://azure.microsoft.com/resources/samples/service-fabric-containers/)  

## <a name="what-are-containers"></a>O que são contentores

Os contentores resolvem o problema da execução de aplicações de forma fiável em diferentes ambientes informáticos, proporcionando um ambiente imutável para a aplicação a funcionar. Os contentores envolvem uma aplicação e todas as suas dependências, como bibliotecas e ficheiros de configuração, na sua própria 'caixa' isolada que contém tudo o que é necessário para executar o software dentro do contentor. Onde quer que o contentor corra, a aplicação no seu interior tem sempre tudo o que precisa para ser executado, como as versões certas das suas bibliotecas dependentes, quaisquer ficheiros de configuração e tudo o mais que precisa de ser executado.

Os contentores funcionam diretamente em cima do núcleo e têm uma visão isolada do sistema de ficheiros e outros recursos. Uma aplicação num contentor não tem conhecimento de quaisquer outras aplicações ou processos fora do seu contentor. Cada aplicação e as suas bibliotecas de funcionamento, dependências e bibliotecas do sistema funcionam dentro de um contentor com acesso privado e completo à própria visão isolada do sistema operativo. Além de facilitar a prestação de todas as dependências da sua aplicação, precisa de funcionar em diferentes ambientes de computação, a segurança e o isolamento dos recursos são benefícios importantes da utilização de contentores com Tecido de Serviço -- que de outra forma gere serviços num processo.

Em comparação com as máquinas virtuais, os recipientes têm as seguintes vantagens:

* **Pequeno**: Os recipientes utilizam um espaço de armazenamento único e versões de camadas e atualizações para aumentar a eficiência.
* **Rápido**: Os contentores não têm de iniciar todo um sistema operativo, para que possam começar muito mais rápido -- normalmente em segundos.
* **Portabilidade**: Uma imagem de aplicação contentorizada pode ser canalizada para funcionar na nuvem, nas instalações, nas máquinas virtuais ou diretamente em máquinas físicas.
* **Governação dos recursos**: Um recipiente pode limitar os recursos físicos que pode consumir no seu hospedeiro.

### <a name="container-types-and-supported-environments"></a>Tipos de contentores e ambientes apoiados

O Service Fabric suporta recipientes tanto no Linux como no Windows e suporta o modo de isolamento Hyper-V no Windows.

#### <a name="docker-containers-on-linux"></a>Contentores de estivadores em Linux

O Docker fornece APIs para criar e gerir contentores em cima de recipientes de kernel Linux. O Docker Hub fornece um repositório central para armazenar e recuperar imagens de contentores.
Para um tutorial baseado em Linux, consulte Criar a sua primeira aplicação de [recipiente de tecido de serviço no Linux.](service-fabric-get-started-containers-linux.md)

#### <a name="windows-server-containers"></a>Contentores do Windows Server

O Windows Server 2016 e mais tarde fornece dois tipos diferentes de recipientes que diferem por nível de isolamento. Os contentores do Windows Server e dos contentores do Docker são semelhantes porque ambos têm espaço de nome e isolamento do sistema de ficheiros, enquanto partilham o núcleo com o hospedeiro em que estão a funcionar. No Linux, este isolamento tem sido tradicionalmente fornecido por grupos e espaços de nome, e os contentores do Windows Server comportam-se da mesma forma.

Os recipientes windows com suporte Hyper-V proporcionam mais isolamento e segurança porque nenhum recipiente partilha o núcleo do sistema operativo com qualquer outro recipiente, ou com o hospedeiro. Com este maior nível de isolamento de segurança, os contentores ativados pelo Hiper-V são direcionados para cenários potencialmente hostis e multi-inquilinos.
Para um tutorial baseado no Windows, consulte [criar a sua primeira aplicação de recipiente de tecido de serviço no Windows](service-fabric-get-started-containers.md).

O número a seguir mostra os diferentes tipos de níveis de virtualização e isolamento disponíveis.
![Plataforma do Service Fabric][Image1]

## <a name="scenarios-for-using-containers"></a>Cenários para a utilização de contentores

Aqui estão exemplos típicos onde um recipiente é uma boa escolha:

* **Elevação e mudança do IIS**: Pode colocar uma aplicação [MVC ASP.NET](https://www.asp.net/mvc) existente num recipiente em vez de a migrar para ASP.NET Core. Estas ASP.NET aplicações MVC dependem de Serviços de Informação da Internet (IIS). Pode embalar estas aplicações em imagens de contentores a partir da imagem IIS pré-crita e implantá-las com o Tecido de Serviço. Consulte [as imagens do contentor no Windows Server](/virtualization/windowscontainers/quick-start/quick-start-windows-server) para obter informações sobre os recipientes do Windows.

* **Misturar recipientes e microserviços de tecido de serviço**: Utilize uma imagem de recipiente existente para uma parte da sua aplicação. Por exemplo, pode utilizar o [recipiente NGINX](https://hub.docker.com/_/nginx/) para a parte frontal da sua aplicação e serviços estatais para a computação traseira mais intensiva.

* Reduzir o **impacto dos serviços de "vizinhos barulhentos"**: Pode utilizar a capacidade de governação de recursos dos contentores para restringir os recursos que um serviço utiliza num hospedeiro. Se os serviços podem consumir muitos recursos e afetar o desempenho de outros (como uma operação de longa duração, semelhante a consultas), considere colocar estes serviços em contentores que tenham governação de recursos.

## <a name="service-fabric-support-for-containers"></a>Suporte do Service Fabric para contentores

O Service Fabric suporta a colocação de contentores Docker em linux e servidor do Windows server no Windows Server 2016 e mais tarde, juntamente com o suporte para o modo de isolamento Hyper-V.

> [!NOTE]
> Os contentores não são suportados em clusters locais de tecido de serviço de nó único (nem clusters Linux na OneBox, nem agrupamentos Windows em instalações locais de Tecido de Serviço).

O Service Fabric fornece um [modelo de aplicação](service-fabric-application-model.md) no qual um recipiente representa um hospedeiro de aplicação no qual são colocadas várias réplicas de serviço. O Service Fabric também suporta um [cenário executável de hóspedes](service-fabric-guest-executables-introduction.md) no qual não utiliza os modelos de programação do Tecido de Serviço incorporado, mas em vez disso embala uma aplicação existente, escrita utilizando qualquer idioma ou estrutura, dentro de um recipiente. Este cenário é o caso comum de utilização dos contentores.

Também pode executar [serviços de Tecido de Serviço dentro de um recipiente.](service-fabric-services-inside-containers.md) O suporte para a execução dos serviços de tecido de serviço dentro de contentores é atualmente limitado.

O Service Fabric fornece várias capacidades de contentores que o ajudam a construir aplicações compostas por microserviços contentorizados, tais como:

* Implantação e ativação da imagem do contentor.
* Governação de recursos, incluindo a definição de valores de recursos por padrão nos clusters Azure.
* Autenticação do repositório.
* Porta de contentores para hospedar mapeamento portuário.
* Descoberta e comunicação contentor-a-contentor.
* Capacidade de configurar e definir variáveis ambientais.
* Capacidade de definir credenciais de segurança no contentor.
* Uma escolha de diferentes modos de rede para recipientes.

Para uma visão geral abrangente do suporte de contentores em Azure, como como criar um cluster Kubernetes com o Serviço Azure Kubernetes, como criar um registo privado de Docker no Registo de Contentores Azure, e muito mais, ver [Azure for Containers](../containers/index.yml).

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu sobre o suporte que o Service Fabric fornece para o funcionamento de contentores. Em seguida, vamos analisar exemplos de cada uma das funcionalidades para mostrar-lhe como usá-las.

[Criar a sua primeira aplicação de contentor do Service Fabric no Linux](service-fabric-get-started-containers-linux.md)  
[Criar a sua primeira aplicação de contentor do Service Fabric no Windows](service-fabric-get-started-containers.md)  
[Saiba mais sobre os Contentores do Windows](/virtualization/windowscontainers/about/)

[Image1]: media/service-fabric-containers/Service-Fabric-Types-of-Isolation.png
