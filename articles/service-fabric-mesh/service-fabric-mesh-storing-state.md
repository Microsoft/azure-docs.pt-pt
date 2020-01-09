---
title: Opções de armazenamento de estado na malha de Service Fabric do Azure
description: Saiba mais sobre o estado de armazenamento confiável em aplicativos de malha Service Fabric em execução na malha de Service Fabric do Azure.
author: dkkapur
ms.author: dekapur
ms.date: 11/27/2018
ms.topic: conceptual
ms.openlocfilehash: 0b0bd611fa86d155bb5bf2e07808e7365e28871c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75352335"
---
# <a name="state-management-with-service-fabric"></a>Gerenciamento de estado com Service Fabric

O Service Fabric dá suporte a muitas opções diferentes para o armazenamento de estado. Para obter uma visão geral conceitual dos padrões de gerenciamento de estado e Service Fabric, consulte [Service Fabric Concepts: State](/azure/service-fabric/service-fabric-concepts-state). Todos esses mesmos conceitos se aplicam independentemente de seus serviços serem executados dentro ou fora da malha Service Fabric. 

Com a malha Service Fabric, você pode facilmente implantar um novo aplicativo e conectá-lo a um repositório de dados existente hospedado no Azure. Além de usar qualquer banco de dados remoto, há várias opções de armazenamento de dado, dependendo se o serviço deseja o armazenamento local ou remoto. 

## <a name="volumes"></a>Volumes

Os contêineres geralmente fazem uso de discos temporários. Os discos temporários são efêmeros, no entanto, você obtém um novo disco temporário e perde as informações quando um contêiner falha. Também é difícil compartilhar informações em discos temporários com outros contêineres. Volumes são diretórios que são montados dentro de suas instâncias de contêiner que você pode usar para manter o estado. Os volumes oferecem armazenamento de arquivos de uso geral e permitem que você leia/grave arquivos usando APIs normais de arquivo de e/s de disco. O recurso de volume descreve como montar um diretório e o armazenamento de backup a ser usado. Você pode escolher o armazenamento de arquivos do Azure ou Service Fabric disco de volume para armazenar dados.

![Volumes][image3]

### <a name="service-fabric-reliable-volume"></a>Service Fabric volume confiável

Service Fabric volume confiável é um driver de volume do Docker usado para montar um volume local em um contêiner. Leituras e gravações são operações locais e rápidas. Os dados são replicados para nós secundários, tornando-os altamente disponíveis. O failover também é rápido. Quando um contêiner falha, ele faz failover para um nó que já tem uma cópia dos dados. Para obter um exemplo, consulte [como implantar um aplicativo com Service Fabric volume confiável](service-fabric-mesh-howto-deploy-app-sfreliable-disk-volume.md).

### <a name="azure-files-volume"></a>Volume de arquivos do Azure

O volume de arquivos do Azure é um driver de volume do Docker usado para montar um compartilhamento de arquivos do Azure para um contêiner. O armazenamento de arquivos do Azure usa o armazenamento de rede, assim, leituras e gravações ocorrem pela rede. Em comparação com o volume confiável Service Fabric, o armazenamento de arquivos do Azure é menos eficaz, mas fornece uma opção de dados mais barata e totalmente confiável. Para obter um exemplo, consulte [como implantar um aplicativo com o volume de arquivos do Azure](service-fabric-mesh-howto-deploy-app-azurefiles-volume.md).

## <a name="next-steps"></a>Passos seguintes

Para obter informações sobre o modelo de aplicativo, consulte [recursos de Service Fabric](service-fabric-mesh-service-fabric-resources.md)

[image3]: ./media/service-fabric-mesh-storing-state/volumes.png
