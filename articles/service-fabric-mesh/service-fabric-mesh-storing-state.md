---
title: Opções de armazenamento do estado na malha de tecido de serviço Azure
description: Saiba mais sobre o estado de armazenamento fiável nas aplicações de malha de tecido de serviço em execução na Malha de Tecido de Serviço Azure.
author: georgewallace
ms.author: gwallace
ms.date: 11/27/2018
ms.topic: conceptual
ms.openlocfilehash: 651329b1b061f2cf5a06dbdc9d60f73cec64e7d3
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/06/2021
ms.locfileid: "99625554"
---
# <a name="state-management-with-service-fabric"></a>Gestão do Estado com Tecido de Serviço

> [!IMPORTANT]
> A pré-estreia da Malha de Tecido de Serviço Azure foi reformada. As novas implementações deixarão de ser permitidas através da API de malha de malha de tecido de serviço. O apoio às implementações existentes continuará até 28 de abril de 2021.
> 
> Para mais detalhes, consulte [a pré-reforma da malha de malha de malha de tecido de serviço Azure.](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)

O Service Fabric suporta muitas opções diferentes para armazenamento do estado. Para uma visão geral conceptual dos padrões de gestão do Estado e do Tecido de Serviço, consulte [Conceitos de Tecido de Serviço: Estado](../service-fabric/service-fabric-concepts-state.md). Todos estes mesmos conceitos aplicam-se quer os seus serviços sejam executados dentro ou fora da Rede de Tecido de Serviço. 

Com a Malha de Tecido de Serviço, pode facilmente implementar uma nova aplicação e conectá-la a uma loja de dados existente hospedada no Azure. Além de utilizar qualquer base de dados remota, existem várias opções para armazenar dados, dependendo se o serviço deseja armazenamento local ou remoto. 

## <a name="volumes"></a>Volumes

Os recipientes geralmente utilizam discos temporários. No entanto, os discos temporários são efémeros, por isso obtém-se um novo disco temporário e perde-se a informação quando um contentor se despenha. Também é difícil partilhar informações sobre discos temporários com outros contentores. Os volumes são diretórios que são montados dentro dos casos do seu contentor que pode usar para persistir. Os volumes dão-lhe armazenamento de ficheiros de uso geral e permitem-lhe ler/escrever ficheiros utilizando apis de ficheiro sonoro normal. O recurso Volume descreve como montar um diretório e que armazenamento de suporte para usar. Pode escolher o armazenamento do ficheiro Azure ou o disco de volume de tecido de serviço para armazenar dados.

![O diagrama mostra o Serviço, que flui para volume, que flui tanto para o Volume Fiável do Tecido de Serviço num disco local replicado como para o Volume de Ficheiros Azure no armazenamento da rede.][image3]

### <a name="service-fabric-reliable-volume"></a>Volume fiável do tecido de serviço

Tecido de serviço Volume fiável é um controlador de volume Docker usado para montar um volume local num recipiente. Leituras e escritos são operações locais e rápidas. Os dados são replicados para nós secundários, tornando-os altamente disponíveis. A falha também é rápida. Quando um contentor se despenha, falha num nó que já tem uma cópia dos seus dados. Por exemplo, consulte [como implementar uma aplicação com o Service Fabric Reliable Volume](service-fabric-mesh-howto-deploy-app-sfreliable-disk-volume.md).

### <a name="azure-files-volume"></a>Volume de ficheiros Azure

O Volume de Ficheiros Azure é um controlador de volume Docker usado para montar uma partilha de Ficheiros Azure num contentor. O armazenamento do Azure Files utiliza o armazenamento da rede, pelo que as leituras e as gravações ocorrem ao longo da rede. Em comparação com o volume fiável do tecido de serviço, o armazenamento de Ficheiros Azure é menos performante, mas fornece uma opção de dados mais barata e totalmente fiável. Por exemplo, consulte [Como implementar uma aplicação com volume de ficheiros Azure](service-fabric-mesh-howto-deploy-app-azurefiles-volume.md).

## <a name="next-steps"></a>Passos seguintes

Para obter informações sobre o modelo de aplicação, consulte [os recursos do Service Fabric](service-fabric-mesh-service-fabric-resources.md)

[image3]: ./media/service-fabric-mesh-storing-state/volumes.png
