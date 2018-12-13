---
title: Opções de armazenamento no Azure Service Fabric em malha de estado | Documentos da Microsoft
description: Saiba mais sobre armazenamento fiável do Estado em aplicativos de malha de recursos de infraestrutura do serviço de mensagens em fila em execução no Azure Service Fabric em malha.
services: service-fabric-mesh
keywords: ''
author: rwike77
ms.author: ryanwi
ms.date: 11/27/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: jeconnoc
ms.openlocfilehash: ecdb36af786d96a5b343d11cd689642d59528445
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2018
ms.locfileid: "52888540"
---
# <a name="state-management-with-service-fabric"></a>Gerenciamento de estado com o Service Fabric

Service Fabric suporta muitas opções diferentes para o armazenamento de estado. Para uma descrição geral conceptual dos padrões de gestão de estado e o Service Fabric, veja [conceitos de recursos de infraestrutura do serviço: estado](/azure/service-fabric/service-fabric-concepts-state). Estes mesmos conceitos aplicam-se se os serviços são executados dentro ou fora da malha de recursos de infraestrutura do serviço. 

Com a malha do Service Fabric, pode facilmente implementar uma nova aplicação e ligá-la a um arquivo de dados existente, alojado no Azure. Além de usar qualquer base de dados remoto, existem várias opções para armazenar dados, dependendo se o serviço deseja armazenamento local ou remoto. 

## <a name="volumes"></a>Volumes

Contentores, muitas vezes, fazer uso de discos temporários. Discos temporários são efêmeros, no entanto, para que obtenha um novo disco temporário e perde as informações quando a falha de um contentor. Também é difícil de partilhar informações sobre os discos temporários com outros contentores. Volumes são diretórios que ser montados dentro as instâncias de contentor que pode utilizar para persistir o estado. Volumes dão-lhe o armazenamento de ficheiros para fins gerais e permitem-lhe ler/escrever ficheiros através de APIs de ficheiros de e/s de disco normal. O recurso de Volume descreve como montar um diretório e o armazenamento de backup para utilizar. Pode escolher o armazenamento de ficheiros do Azure ou o disco do Volume de recursos de infraestrutura do serviço para armazenar dados.

![Volumes][image3]

### <a name="service-fabric-reliable-volume"></a>Volume fiáveis do Service Fabric

Volume fiáveis do Service Fabric é um driver de volume do Docker usado para montar um volume local para um contentor. Leituras e gravações são operações locais e rápida. Dados são replicados horizontalmente para nós secundários, tornando a elevada disponibilidade. Ativação pós-falha também é rápida. Quando um contentor falha, ele executa failover para um nó que já tenha uma cópia dos seus dados. Por exemplo, veja [como implementar uma aplicação com Volume fiáveis do Service Fabric.](https://github.com/Azure-Samples/service-fabric-mesh/tree/2018-09-01-preview/templates/counter)

### <a name="azure-files-volume"></a>Volume de ficheiros do Azure

Volume de ficheiros do Azure é um driver de volume do Docker usado para montar uma partilha de ficheiros do Azure para um contentor. Ficheiros do Azure, armazenamento utiliza o armazenamento de rede, portanto, lê e escreve ocorrem através da rede. Em comparação com Volume fiáveis do Service Fabric, armazenamento de ficheiros do Azure é menos eficiente, mas fornece uma opção de dados mais barato e totalmente confiável. Por exemplo, veja [como implementar uma aplicação com o Volume de ficheiros do Azure](service-fabric-mesh-howto-deploy-app-azurefiles-volume.md).

## <a name="next-steps"></a>Passos Seguintes

Para obter informações sobre o modelo de aplicativo, consulte [recursos de infraestrutura do serviço](service-fabric-mesh-service-fabric-resources.md)

[image3]: ./media/service-fabric-mesh-storing-state/volumes.png
