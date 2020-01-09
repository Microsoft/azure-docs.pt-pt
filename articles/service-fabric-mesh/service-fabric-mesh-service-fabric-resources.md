---
title: Introdução ao modelo de recurso de Service Fabric do Azure
description: Saiba mais sobre o modelo de recurso Service Fabric, uma abordagem simplificada para definir Service Fabric aplicativos de malha.
author: vturecek
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: vturecek
ms.custom: mvc, devcenter
ms.openlocfilehash: 0ae2ed163560aee4c0c3525ab31910e37afaa5b9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75352465"
---
# <a name="introduction-to-service-fabric-resource-model"></a>Introdução ao modelo de recurso Service Fabric

O modelo de recurso Service Fabric descreve uma abordagem simples para definir recursos que compõem um aplicativo de malha Service Fabric. Recursos individuais podem ser implantados em qualquer ambiente de Service Fabric.  O modelo de recurso Service Fabric também é compatível com o modelo de Azure Resource Manager. Atualmente, há suporte para os seguintes tipos de recursos neste modelo:

- Aplicações e serviços
- Redes
- Gateways
- Segredos e segredos/valores
- Volumes

Cada recurso é descrito declarativamente em um arquivo de recurso, que é um documento simples YAML ou JSON que descreve o aplicativo de malha e é provisionado pela plataforma Service Fabric.

## <a name="applications-and-services"></a>Aplicativos e serviços

Um recurso de aplicativo é a unidade de implantação, controle de versão e tempo de vida de um aplicativo de malha. Ele é composto por um ou mais recursos de serviço que representam um microserviço. Cada recurso de serviço, por sua vez, é composto por um ou mais pacotes de código que descrevem tudo o que é necessário para executar a imagem de contêiner associada ao pacote de códigos.

![Aplicações e serviços][Image1]

Um recurso de serviço declara o seguinte:

- Nome do contêiner, versão e registro
- Recursos de CPU e memória necessários para cada contêiner
- Pontos de extremidade de rede
- Referências a outros recursos, como redes, volumes e segredos 

Todos os pacotes de código definidos como parte de um recurso de serviço são implantados e ativados juntos como um grupo. O recurso de serviço também descreve quantas instâncias do serviço executar e também faz referência a outros recursos (por exemplo, recurso de rede) dos quais depende.

Se um aplicativo de malha for composto por mais de um serviço, não haverá garantia de que eles sejam executados juntos no mesmo nó. Além disso, durante uma atualização do aplicativo, a falha de atualização de um único serviço fará com que todos os serviços sejam revertidos para a versão anterior.

Como alusão anteriormente, o ciclo de vida de cada instância do aplicativo pode ser gerenciado de forma independente. Por exemplo, uma instância de aplicativo pode ser atualizada independentemente das outras instâncias de aplicativo. Normalmente, você mantém o número de serviços em um aplicativo razoavelmente pequeno, pois quanto mais serviços você coloca em um aplicativo, mais difícil ele se torna gerenciar cada serviço de forma independente.

## <a name="networks"></a>Redes

O recurso de rede é um recurso implantável individualmente, independentemente de um recurso de aplicativo ou serviço que possa se referir a ele como sua dependência. Ele é usado para criar uma rede para seus aplicativos. Vários serviços de diferentes aplicativos podem fazer parte da mesma rede.  Para obter mais informações, leia sobre [rede em aplicativos de malha Service Fabric](service-fabric-mesh-networks-and-gateways.md).

> [!NOTE]
> A visualização atual dá suporte apenas a um mapeamento de um para um entre aplicativos e redes

![Rede e gateway][Image2]

## <a name="gateways"></a>Gateways
Um recurso de gateway conecta duas redes e roteia o tráfego.  Um gateway permite que seus serviços se comuniquem com clientes externos e fornece uma entrada em seus serviços.  Um gateway também pode ser usado para conectar seu aplicativo de malha com sua própria rede virtual existente. Para obter mais informações, leia sobre [rede em aplicativos de malha Service Fabric](service-fabric-mesh-networks-and-gateways.md).

![Rede e gateway][Image2]

## <a name="secrets"></a>Segredos

Os recursos de segredos são implantáveis independentemente de um recurso de aplicativo ou serviço que pode se referir a ele como sua dependência. Ele é usado para entregar segredos aos seus aplicativos com segurança. Vários serviços de diferentes aplicativos podem referenciar valores do mesmo segredo.

## <a name="volumes"></a>Volumes

Os contêineres geralmente tornam os discos temporários disponíveis. Os discos temporários são efêmeros, no entanto, você obtém um novo disco temporário e perde as informações quando um contêiner falha. Também é difícil compartilhar informações em discos temporários com outros contêineres. Volumes são diretórios que são montados dentro de suas instâncias de contêiner que você pode usar para manter o estado. Os volumes oferecem armazenamento de arquivos de uso geral e permitem que você leia/grave arquivos usando APIs normais de arquivo de e/s de disco. O recurso de volume é uma maneira declarativa de descrever como um diretório é montado e o armazenamento de backup para ele (volume de arquivos do Azure ou Service Fabric volume confiável).  Para obter mais informações, leia o [estado de armazenamento](service-fabric-mesh-storing-state.md#volumes).

![Volumes][Image3]

## <a name="programming-models"></a>Modelos de programação
O recurso de serviço requer apenas uma imagem de contêiner a ser executada, que é referenciada nos pacotes de código associados ao recurso. Você pode executar qualquer código, escrito em qualquer linguagem, usando qualquer estrutura dentro do contêiner sem a necessidade de conhecer, ou usar, Service Fabric APIs específicas de malha. 

O código do aplicativo permanece portátil mesmo fora da malha Service Fabric e suas implantações de aplicativo permanecem consistentes, independentemente da linguagem ou da estrutura usada para implementar seus serviços. Independentemente de seu aplicativo ser ASP.NET Core, ir ou apenas um conjunto de processos e scripts, o modelo de implantação de recursos de malha Service Fabric permanece o mesmo. 

## <a name="packaging-and-deployment"></a>Empacotamento e implantação

Service Fabric aplicativos de malha com base no modelo de recurso são empacotados como contêineres do Docker.  A malha Service Fabric é um ambiente compartilhado e multilocatário e os contêineres oferecem um alto nível de isolamento.  Esses aplicativos são descritos usando um formato JSON ou um formato YAML (que é então convertido em JSON). Ao implantar um aplicativo de malha na malha de Service Fabric do Azure, o JSON usado para descrever o aplicativo é um modelo de Azure Resource Manager. Os recursos são mapeados para os recursos do Azure.  Ao implantar um aplicativo de malha em um Cluster Service Fabric (autônomo ou hospedado pelo Azure), o JSON usado para descrever o aplicativo é um formato semelhante a um modelo de Azure Resource Manager.  Depois de implantados, os aplicativos de malha podem ser gerenciados por meio de interfaces HTTP ou do CLI do Azure. 


## <a name="next-steps"></a>Passos seguintes 
Para saber mais sobre a malha de Service Fabric, leia a visão geral:
- [Visão geral da malha de Service Fabric](service-fabric-mesh-overview.md)

[Image1]: media/service-fabric-mesh-service-fabric-resources/AppsAndServices.png
[Image2]: media/service-fabric-mesh-service-fabric-resources/NetworkAndGateway.png
[Image3]: media/service-fabric-mesh-service-fabric-resources/volumes.png
