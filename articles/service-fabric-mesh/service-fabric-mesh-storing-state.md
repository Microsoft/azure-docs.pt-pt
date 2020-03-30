---
title: Opções de armazenamento do Estado na malha de tecido de serviço Azure
description: Saiba mais sobre o estado de armazenamento fiável em aplicações de malha de tecido de serviço em execução em malha de tecido de serviço Azure.
author: dkkapur
ms.author: dekapur
ms.date: 11/27/2018
ms.topic: conceptual
ms.openlocfilehash: 0b0bd611fa86d155bb5bf2e07808e7365e28871c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259100"
---
# <a name="state-management-with-service-fabric"></a>Gestão do Estado com Tecido de Serviço

O Service Fabric suporta muitas opções diferentes para armazenamento estatal. Para uma visão geral conceptual dos padrões de gestão do Estado e do Tecido de Serviço, consulte [Service Fabric Concepts: State](/azure/service-fabric/service-fabric-concepts-state). Todos estes mesmos conceitos se aplicam quer os seus serviços sejam executados dentro ou fora da Malha de Tecido de Serviço. 

Com malha de tecido de serviço, pode facilmente implementar uma nova aplicação e ligá-la a uma loja de dados existente hospedada no Azure. Além de utilizar qualquer base de dados remota, existem várias opções para armazenar dados, dependendo se o serviço deseja armazenamento local ou remoto. 

## <a name="volumes"></a>Volumes

Os recipientes utilizam frequentemente discos temporários. Os discos temporários são efémeros, no entanto, por isso obtém-se um novo disco temporário e perde-se a informação quando um contentor se despenha. Também é difícil partilhar informações sobre discos temporários com outros contentores. Os volumes são diretórios que são montados dentro dos seus casos de contentores que pode usar para persistir no estado. Os volumes dão-lhe armazenamento de ficheiros de uso geral e permitem-lhe ler/escrever ficheiros utilizando APIs de ficheiro sinuoso normal. O recurso Volume descreve como montar um diretório e qual o armazenamento de apoio a utilizar. Pode escolher o armazenamento de ficheiros Azure ou o disco de volume de tecido de serviço para armazenar dados.

![Volumes][image3]

### <a name="service-fabric-reliable-volume"></a>Volume fiável de tecido de serviço

Volume fiável de tecido de serviço é um controlador de volume Docker usado para montar um volume local em um recipiente. As leituras e os escritos são operações locais e rápidos. Os dados são replicados para nós secundários, tornando-os altamente disponíveis. Failover também é rápido. Quando um contentor se despenha, falha num nó que já tem uma cópia dos seus dados. Por exemplo, consulte [como implementar uma aplicação com volume fiável](service-fabric-mesh-howto-deploy-app-sfreliable-disk-volume.md)de tecido de serviço .

### <a name="azure-files-volume"></a>Volume de Ficheiros Azure

O Volume de Ficheiros Azure é um controlador de volume Docker usado para montar uma partilha de Ficheiros Azure num contentor. O armazenamento do Azure Files utiliza o armazenamento da rede, pelo que as leituras e os escritos têm lugar sobre a rede. Em comparação com o Volume Fiável do Tecido de Serviço, o armazenamento de Ficheiros Azure é menos performante, mas fornece uma opção de dados mais barata e totalmente fiável. Por exemplo, consulte como implementar uma aplicação com volume de [ficheiros Azure](service-fabric-mesh-howto-deploy-app-azurefiles-volume.md).

## <a name="next-steps"></a>Passos seguintes

Para obter informações sobre o modelo de aplicação, consulte [os recursos do Service Fabric](service-fabric-mesh-service-fabric-resources.md)

[image3]: ./media/service-fabric-mesh-storing-state/volumes.png
