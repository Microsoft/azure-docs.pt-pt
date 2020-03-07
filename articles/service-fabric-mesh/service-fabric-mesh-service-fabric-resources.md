---
title: Introdução ao modelo de recurso de tecido de serviço Azure
description: Conheça o Modelo de Recursos de Tecido de Serviço, uma abordagem simplificada para definir aplicações de malha de tecido de serviço.
author: vturecek
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: vturecek
ms.custom: mvc, devcenter
ms.openlocfilehash: 0ae2ed163560aee4c0c3525ab31910e37afaa5b9
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78397263"
---
# <a name="introduction-to-service-fabric-resource-model"></a>Introdução ao modelo de recurso de tecido de serviço

O Modelo de Recursos de Tecido de Serviço descreve uma abordagem simples para definir recursos que compõem uma aplicação de malha de tecido de serviço. Os recursos individuais podem ser implantados em qualquer ambiente de Tecido de Serviço.  O Modelo de Recursos de Tecido de Serviço também é compatível com o Modelo de Gestor de Recursos Azure. Os seguintes tipos de recursos são atualmente suportados neste modelo:

- Aplicações e serviços
- Redes
- Gateways
- Segredos e Segredos/Valores
- Volumes

Cada recurso é descrito declarativamente num ficheiro de recursos, que é um simples documento YAML ou JSON que descreve a Aplicação de Malha, e é provisionado pela plataforma Service Fabric.

## <a name="applications-and-services"></a>Aplicações e Serviços

Um recurso de aplicação é a unidade de implantação, versão e vida útil de uma aplicação de Malha. É composto por um, ou mais, recursos de serviço que representam um microserviço. Cada recurso de Serviço, por sua vez, é composto por um, ou mais, pacotes de código que descrevem tudo o que é necessário para executar a imagem do recipiente associada ao pacote de código.

![Aplicativos e serviços][Image1]

Um recurso de serviço declara o seguinte:

- Nome, versão e registo do contentor
- CPU e recursos de memória necessários para cada recipiente
- Pontos finais da rede
- Referências a outros recursos, tais como redes, volumes e segredos 

Todos os pacotes de código definidos como parte de um recurso de serviço são implantados e ativados em conjunto como um grupo. O recurso de Serviço também descreve quantas instâncias do serviço a executar e também faz referência a outros Recursos (por exemplo, recursos de rede) de que depende.

Se uma Aplicação de Malha for composta por mais de um Serviço, não é garantido que corram juntas no mesmo nó. Além disso, durante uma atualização da Aplicação, a falha na modernização de um único Serviço resultará na reversão de todos os Serviços para a sua versão anterior.

Tal como aludido anteriormente, o ciclo de vida de cada instância de Aplicação pode ser gerido de forma independente. Por exemplo, uma instância de aplicação pode ser atualizada independentemente das outras instâncias de Aplicação. Normalmente, mantém-se o número de serviços numa aplicação relativamente pequena, à medida que quanto mais serviços se colocam numa aplicação, mais difícil se torna gerir cada serviço de forma independente.

## <a name="networks"></a>Redes

O recurso de rede é um recurso individualmente implantável, independentemente de um recurso de Aplicação ou Serviço que pode referir-se a ele como sua dependência. É usado para criar uma rede para as suas aplicações. Vários Serviços de diferentes Aplicações podem fazer parte da mesma rede.  Para mais informações, leia sobre [a rede em aplicações](service-fabric-mesh-networks-and-gateways.md)de malha de tecido de serviço .

> [!NOTE]
> A pré-visualização atual apenas suporta um mapeamento de um a um entre aplicações e redes

![Rede e porta de entrada][Image2]

## <a name="gateways"></a>Gateways
Um recurso Gateway liga duas redes e encaminha o tráfego.  Um portal permite que os seus serviços se comuniquem com clientes externos e fornece uma entrada no seu serviço(s).  Um gateway também pode ser usado para ligar a sua aplicação Mesh com a sua própria rede virtual existente. Para mais informações, leia sobre [a rede em aplicações](service-fabric-mesh-networks-and-gateways.md)de malha de tecido de serviço .

![Rede e porta de entrada][Image2]

## <a name="secrets"></a>Segredos

Os recursos secretos são implantáveis independentemente de uma aplicação ou recurso de serviço que pode referir-se a ele como sua dependência. É usado para entregar segredos de forma segura às suas aplicações. Vários serviços de diferentes aplicações podem referenciar valores do mesmo Segredo.

## <a name="volumes"></a>Volumes

Os recipientes muitas vezes disponibilizam discos temporários. Os discos temporários são efémeros, no entanto, por isso obtém-se um novo disco temporário e perde-se a informação quando um contentor se despenha. Também é difícil partilhar informações sobre discos temporários com outros contentores. Os volumes são diretórios que são montados dentro dos seus casos de contentores que pode usar para persistir no estado. Os volumes dão-lhe armazenamento de ficheiros de uso geral e permitem-lhe ler/escrever ficheiros utilizando APIs de ficheiro sinuoso normal. O recurso Volume é uma forma declarativa de descrever como um diretório é montado e o armazenamento de apoio para ele (volume de ficheiros Azure ou volume fiável de tecido de serviço).  Para mais informações, leia o [estado de armazenamento.](service-fabric-mesh-storing-state.md#volumes)

![Volumes][Image3]

## <a name="programming-models"></a>Modelos de programação
O recurso de serviço requer apenas uma imagem de contentor a ser executada, que é referenciada no ou no pacote de código s associado ao recurso. Pode executar qualquer código, escrito em qualquer idioma, utilizando qualquer estrutura dentro do recipiente sem exigir saber, ou utilizar, APIs específicos de malha de tecido de serviço. 

O seu código de aplicação permanece portátil mesmo fora da Malha de Tecido de Serviço e as suas implementações de aplicações permanecem consistentes independentemente da linguagem ou enquadramento utilizado para implementar os seus serviços. Quer a sua aplicação seja ASP.NET Core, Go, ou apenas um conjunto de processos e scripts, o modelo de implementação de recursos de malha de tecido de serviço permanece o mesmo. 

## <a name="packaging-and-deployment"></a>Embalagem e implantação

Aplicações de malha de tecido de serviço baseadas no modelo de recursos são embaladas como recipientes Docker.  Service Fabric Mesh é um ambiente partilhado, multi-inquilino e os recipientes dão-lhe um alto nível de isolamento.  Estas aplicações são descritas utilizando um formato JSON ou um formato YAML (que é depois convertido para JSON). Ao implementar uma aplicação de malha de malha para a malha de tecido de serviço Azure, o JSON usado para descrever a aplicação é um modelo de Gestor de Recursos Azure. Os recursos estão mapeados para os recursos do Azure.  Ao implementar uma aplicação de malha para um cluster de tecido de serviço (autónomo ou azure-hosted), o JSON usado para descrever a aplicação é um formato semelhante a um modelo de Gestor de Recursos Azure.  Uma vez implementadas, as aplicações de malha podem ser geridas através das interfaces HTTP ou do Azure CLI. 


## <a name="next-steps"></a>Passos seguintes 
Para saber mais sobre a Malha de Tecido de Serviço, leia a visão geral:
- [Visão geral da malha de tecido de serviço](service-fabric-mesh-overview.md)

[Image1]: media/service-fabric-mesh-service-fabric-resources/AppsAndServices.png
[Image2]: media/service-fabric-mesh-service-fabric-resources/NetworkAndGateway.png
[Image3]: media/service-fabric-mesh-service-fabric-resources/volumes.png
