---
title: Introdução ao Modelo de Recursos de Tecido de Serviço Azure
description: Conheça o Modelo de Recursos de Tecido de Serviço, uma abordagem simplificada para definir aplicações de malha de tecido de serviço.
author: vturecek
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: vturecek
ms.custom: mvc, devcenter
ms.openlocfilehash: 650755cf2692b613b2998e8ef7b706e09022b178
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99626914"
---
# <a name="introduction-to-service-fabric-resource-model"></a>Introdução ao Modelo de Recursos de Tecido de Serviço

> [!IMPORTANT]
> A pré-estreia da Malha de Tecido de Serviço Azure foi reformada. As novas implementações deixarão de ser permitidas através da API de malha de malha de tecido de serviço. O apoio às implementações existentes continuará até 28 de abril de 2021.
> 
> Para mais detalhes, consulte [a pré-reforma da malha de malha de malha de tecido de serviço Azure.](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)

O Modelo de Recursos de Tecido de Serviço descreve uma abordagem simples para definir recursos que compõem uma aplicação de malha de tecido de serviço. Os recursos individuais podem ser utilizados em qualquer ambiente de Tecido de Serviço.  O Modelo de Recursos de Tecido de Serviço também é compatível com o Modelo de Gestor de Recursos Azure. Neste modelo são suportados os seguintes tipos de recursos:

- Aplicações e serviços
- Redes
- Gateways
- Segredos e Segredos/Valores
- Volumes

Cada recurso é descrito declarativamente num ficheiro de recursos, que é um documento simples YAML ou JSON que descreve a Aplicação de Malha, e é aprovisionado pela plataforma Service Fabric.

## <a name="applications-and-services"></a>Aplicações e Serviços

Um recurso de aplicação é a unidade de implantação, versão e vida útil de uma aplicação de Malha. É composto por um, ou mais, recursos de serviço que representam um microserviço. Cada recurso de Serviço, por sua vez, é composto por um, ou mais, pacotes de código que descrevem tudo o que é necessário para executar a imagem do recipiente associada ao pacote de código.

![Aplicações e serviços][Image1]

Um recurso de serviço declara o seguinte:

- Nome do recipiente, versão e registo
- CPU e recursos de memória necessários para cada contentor
- Pontos finais da rede
- Referências a outros recursos, tais como redes, volumes e segredos 

Todos os pacotes de código definidos como parte de um recurso de serviço são implantados e ativados em conjunto como um grupo. O recurso de Serviço também descreve quantas instâncias do serviço a executar e também refere outros Recursos (por exemplo, recursos de rede) de que depende.

Se uma Aplicação de Malha for composta por mais de um Serviço, não é garantido que corram juntos no mesmo nó. Além disso, durante uma atualização da Aplicação, a falha na atualização de um único Serviço resultará na reversão de todos os Serviços para a versão anterior.

Como aludiu anteriormente, o ciclo de vida de cada instância de aplicação pode ser gerido de forma independente. Por exemplo, uma instância de aplicação pode ser atualizada independentemente das outras instâncias de Aplicação. Normalmente, mantém o número de serviços numa aplicação bastante pequena, à medida que quanto mais serviços coloca numa aplicação, mais difícil se torna gerir cada serviço de forma independente.

## <a name="networks"></a>Redes

O recurso de rede é individualmente implantável, independente de um recurso de Aplicação ou Serviço que pode referir-se a ele como a sua dependência. É usado para criar uma rede para as suas aplicações. Vários serviços de diferentes Aplicações podem fazer parte da mesma rede.  Para mais informações, leia sobre [networking em aplicações de malha de tecido de serviço.](service-fabric-mesh-networks-and-gateways.md)

> [!NOTE]
> A pré-visualização atual só suporta um mapeamento de um para um entre aplicações e redes

![Rede e gateway][Image2]

## <a name="gateways"></a>Gateways
Um recurso Gateway liga duas redes e encaminha o tráfego.  Um gateway permite que os seus serviços comuniquem com clientes externos e fornece uma entrada no seu(s) serviço(s).  Um gateway também pode ser usado para ligar a sua aplicação de Malha com a sua própria rede virtual existente. Para mais informações, leia sobre [networking em aplicações de malha de tecido de serviço.](service-fabric-mesh-networks-and-gateways.md)

![Rede e gateway][Image2]

## <a name="secrets"></a>Segredos

Os recursos secretos são implantáveis independentemente de uma aplicação ou recurso de serviço que pode se referir a ele como a sua dependência. É usado para entregar segredos de forma segura às suas aplicações. Vários serviços de diferentes aplicações podem referenciar valores do mesmo Segredo.

## <a name="volumes"></a>Volumes

Os contentores costumam disponibilizar discos temporários. No entanto, os discos temporários são efémeros, por isso obtém-se um novo disco temporário e perde-se a informação quando um contentor se despenha. Também é difícil partilhar informações sobre discos temporários com outros contentores. Os volumes são diretórios que são montados dentro dos casos do seu contentor que pode usar para persistir. Os volumes dão-lhe armazenamento de ficheiros de uso geral e permitem-lhe ler/escrever ficheiros utilizando apis de ficheiro sonoro normal. O recurso Volume é uma forma declarativa de descrever como um diretório é montado e o armazenamento de suporte para ele (ou volume de ficheiros Azure ou volume fiável de tecido de serviço).  Para mais informações, leia [o estado de armazenamento.](service-fabric-mesh-storing-state.md#volumes)

![O diagrama mostra um Serviço fluindo para um volume de disco, que flui para ambos os tecidos de serviço Volume fiável, em seguida, para um disco local replicado, e para O Volume de Ficheiros Azure, em seguida, para armazenamento de rede.][Image3]

## <a name="programming-models"></a>Modelos de programação
O recurso de serviço requer apenas uma imagem de contentor a ser executada, que é referenciada no(s) pacote de código(s) associado ao recurso. Pode executar qualquer código, escrito em qualquer idioma, utilizando qualquer estrutura dentro do recipiente sem necessidade de conhecer, ou utilizar, APIs específicos de malha de tecido de serviço. 

O seu código de aplicação permanece portátil mesmo fora da Malha de Tecido de Serviço e as suas implementações de aplicação permanecem consistentes independentemente do idioma ou enquadramento utilizado para implementar os seus serviços. Quer a sua aplicação seja ASP.NET Core, Go ou apenas um conjunto de processos e scripts, o modelo de implementação de Recursos de Malha de Tecido de Serviço permanece o mesmo. 

## <a name="packaging-and-deployment"></a>Embalagem e implantação

As aplicações de malha de tecido de serviço baseadas no modelo de recursos são embaladas como recipientes Docker.  A Malha de Tecido de Serviço é um ambiente partilhado e multi-inquilino e os contentores dão-lhe um alto nível de isolamento.  Estas aplicações são descritas utilizando um formato JSON ou um formato YAML (que é depois convertido em JSON). Ao implementar uma aplicação de malha para malha de tecido de serviço Azure, o JSON usado para descrever a aplicação é um modelo de Gestor de Recursos Azure. Os recursos estão mapeados para os recursos do Azure.  Ao implementar uma aplicação de Malha num cluster de Tecido de Serviço (autónomo ou alojado no Azure), o JSON utilizado para descrever a aplicação é um formato semelhante a um modelo de Gestor de Recursos Azure.  Uma vez implementadas, as aplicações de malha podem ser geridas através de interfaces HTTP ou do Azure CLI. 


## <a name="next-steps"></a>Passos seguintes 
Para saber mais sobre a Malha de Tecido de Serviço, leia a visão geral:
- [Visão geral da malha de tecido de serviço](service-fabric-mesh-overview.md)

[Image1]: media/service-fabric-mesh-service-fabric-resources/AppsAndServices.png
[Image2]: media/service-fabric-mesh-service-fabric-resources/NetworkAndGateway.png
[Image3]: media/service-fabric-mesh-service-fabric-resources/volumes.png
