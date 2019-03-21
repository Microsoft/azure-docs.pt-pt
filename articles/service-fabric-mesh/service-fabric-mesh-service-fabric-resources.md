---
title: Introdução ao modelo de recursos de recursos de infraestrutura do serviço do Azure | Documentos da Microsoft
description: Saiba mais sobre o modelo de recursos de recursos de infraestrutura de serviço, uma abordagem simplificada para definir a malha de recursos de infraestrutura do serviço de aplicações.
services: service-fabric-mesh
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/23/2018
ms.author: vturecek
ms.custom: mvc, devcenter
ms.openlocfilehash: 3cee0ada75c4ea265c7e9c598408eb6b01477d6c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57903679"
---
# <a name="introduction-to-service-fabric-resource-model"></a>Introdução ao modelo de recursos do Service Fabric

O modelo de recursos do Service Fabric descreve uma abordagem simples para definir os recursos que compõem uma aplicação de malha do Service Fabric. Recursos individuais podem ser implantados em qualquer ambiente do Service Fabric.  O modelo de recursos do Service Fabric também é compatível com o modelo do Azure Resource Manager. Os seguintes tipos de recursos atualmente são suportados neste modelo:

- Aplicações e serviços
- Redes
- Gateways
- Segredos e os segredos/valores
- Volumes

Cada recurso é descrito de forma declarativa num arquivo de recurso, o que é um YAML simple ou um documento JSON que descreve a aplicação de malha e é aprovisionado pela plataforma do Service Fabric.

## <a name="applications-and-services"></a>Aplicações e serviços

Um recurso de aplicação é a unidade de implementação, o controle de versão e o tempo de vida de um aplicativo de malha. Ele é composto por um ou mais, recursos de serviço que representam um microsserviço. Cada recurso de serviço, por sua vez, é composta por um ou mais pacotes de código que descrevem tudo necessários para executar a imagem de contentor associada ao pacote de código.

![Aplicações e serviços][Image1]

Um recurso de serviço declara o seguinte:

- Nome do contentor, versão e do Registro
- Recursos de CPU e memória necessários para cada contentor
- Pontos finais de rede
- Referências a outros recursos, tais como redes, volumes e segredos 

Todos os pacotes de código definidos como parte de um recurso de serviço são implementados e ativados em conjunto como um grupo. O recurso de serviço também descreve o número de instâncias de serviço para ser executado e também faz referência a outros recursos (por exemplo, o recurso de rede), que depende.

Se um aplicativo de malha é composto por mais de um serviço, não é garantido que seja executado em conjunto no mesmo nó. Além disso, durante uma atualização do aplicativo, falha de atualização de um único serviço resultará em todos os serviços que está a ser revertidos para a versão anterior.

Conforme mencionamos anteriormente, o ciclo de vida de cada instância da aplicação pode ser gerido de forma independente. Por exemplo, uma instância da aplicação pode ser atualizada independentemente de outras instâncias do aplicativo. Normalmente, mantenha o número de serviços num aplicativo bem pequeno, como os serviços mais que coloca num aplicativo, o mais difícil torna-se gerir cada serviço independente.

## <a name="networks"></a>Redes

Recurso de rede é um recurso implementável individualmente, independente de um recurso de aplicação ou serviço que pode fazer referência a ele como sua dependência. Ele é usado para criar uma rede para as suas aplicações. Vários serviços de aplicativos diferentes podem fazer parte da mesma rede.  Para obter mais informações, leia sobre [redes em malha de recursos de infraestrutura do serviço de aplicações](service-fabric-mesh-networks-and-gateways.md).

> [!NOTE]
> A pré-visualização atual só suporta um mapeamento um-para-um entre os aplicativos e de redes

![Rede e de gateway][Image2]

## <a name="gateways"></a>Gateways
Um recurso de Gateway liga duas redes e encaminha o tráfego.  Um gateway permite que os seus serviços comunicar com clientes externos e fornece uma entrada para o seu serviço ou serviços.  Um gateway também pode ser utilizado para ligar a aplicação de malha com seu próprios, rede virtual existente. Para obter mais informações, leia sobre [redes em malha de recursos de infraestrutura do serviço de aplicações](service-fabric-mesh-networks-and-gateways.md).

![Rede e de gateway][Image2]

## <a name="secrets"></a>Segredos

Recursos de segredos estão implementável independentemente de uma aplicação ou recurso de serviço que pode fazer referência a ele como sua dependência. É utilizado para fornecer com segurança segredos às suas aplicações. Vários serviços de aplicativos diferentes podem referenciar valores do segredo do mesmo.

## <a name="volumes"></a>Volumes

Contentores, muitas vezes, disponibilizam os discos temporários. Discos temporários são efêmeros, no entanto, para que obtenha um novo disco temporário e perde as informações quando a falha de um contentor. Também é difícil de partilhar informações sobre os discos temporários com outros contentores. Volumes são diretórios que ser montados dentro as instâncias de contentor que pode utilizar para persistir o estado. Volumes dão-lhe o armazenamento de ficheiros para fins gerais e permitem-lhe ler/escrever ficheiros através de APIs de ficheiros de e/s de disco normal. O recurso de Volume é uma forma declarativa para descrever como um diretório está montado e o armazenamento de backup para o mesmo (Volume de ficheiros do Azure ou Volume fiáveis do Service Fabric).  Para obter mais informações, leia [armazenando o estado](service-fabric-mesh-storing-state.md#volumes).

![Volumes][Image3]

## <a name="programming-models"></a>Modelos de programação
Recurso de serviço requer apenas uma imagem de contentor para executar, o que é referenciada nos pacotes de código associado ao recurso. Pode executar qualquer código, escrito em qualquer linguagem, através de qualquer arquitetura dentro do contêiner sem a necessidade de saber ou utilizar o Service Fabric em malha APIs específicas. 

O código da aplicação permanece portátil fora de malha do Service Fabric e suas implementações de aplicações permanecem consistentes, independentemente da linguagem ou estrutura usada para implementar os seus serviços. Se seu aplicativo for ASP.NET Core, Go ou apenas um conjunto de processos e scripts, o modelo de implementação de recursos de malha de recursos de infraestrutura de serviço permanece igual. 

## <a name="packaging-and-deployment"></a>Empacotamento e implantação

Aplicações de Mesh do Service Fabric com base no modelo de recurso são empacotadas como contentores do Docker.  Malha de recursos de infraestrutura do serviço é um ambiente multi-inquilino partilhado e contentores oferecem um elevado nível de isolamento.  Esses aplicativos são descritos usando o formato JSON ou um formato YAML (que, em seguida, é convertido em JSON). Ao implantar um aplicativo de malha para o modo de malha do Azure Service Fabric, o JSON utilizado para descrever o aplicativo é um modelo Azure Resource Manager. Recursos são mapeados para recursos do Azure.  Ao implantar um aplicativo de malha para um cluster do Service Fabric (autónomo ou alojado no Azure), o JSON utilizado para descrever o aplicativo é um formato semelhante a um modelo Azure Resource Manager.  Depois de implementada, a malha de aplicativos podem ser geridos por meio de interfaces HTTP ou a CLI do Azure. 


## <a name="next-steps"></a>Passos Seguintes 
Para saber mais sobre a malha de recursos de infraestrutura do serviço, leia a visão geral:
- [Descrição geral de malha do Service Fabric](service-fabric-mesh-overview.md)

[Image1]: media/service-fabric-mesh-service-fabric-resources/AppsAndServices.png
[Image2]: media/service-fabric-mesh-service-fabric-resources/NetworkAndGateway.png
[Image3]: media/service-fabric-mesh-service-fabric-resources/volumes.png
