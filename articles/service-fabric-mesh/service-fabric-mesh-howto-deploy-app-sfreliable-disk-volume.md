---
title: Utilize a elevada disponibilidade Service Fabric Reliable Volume de disco num aplicativo de malha do Azure Service Fabric | Documentos da Microsoft
description: Saiba como armazenar o estado num aplicativo de malha do Azure Service Fabric ao montar o volume de disco de fiável de recursos de infraestrutura de serviço com base no interior do contentor com a CLI do Azure.
services: service-fabric-mesh
documentationcenter: .net
author: ashishnegi
manager: raunakpandya
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/03/2018
ms.author: asnegi
ms.custom: mvc, devcenter
ms.openlocfilehash: b5e4ad30a65b25140cfb2c80dd15d8cd28fb827b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57850858"
---
# <a name="mount-highly-available-service-fabric-reliable-disk-based-volume-in-a-service-fabric-mesh-application"></a>Montar o volume elevada disponibilidade do disco de fiável de recursos de infraestrutura de serviço com base num aplicativo de malha do Service Fabric 
O método comum de estado persistente com aplicações de contentor é usar o armazenamento remoto, como o armazenamento de ficheiros do Azure ou a base de dados, como o Azure Cosmos DB. Isto leva a leitura significativa e latência de rede de escrita para o armazenamento remoto.

Este artigo mostra como armazenar o estado na elevada disponibilidade disco fiável para o serviço de recursos de infraestrutura ao montar um volume no interior do contentor de um aplicativo de malha do Service Fabric.
Disco fiáveis do Service Fabric fornece volumes para leituras locais com gravações replicadas dentro do Cluster do Service Fabric para elevada disponibilidade. Esta ação remove as chamadas de rede para leituras e reduz a latência de rede para escritas. Se o contentor é reiniciado ou se move para outro nó, nova instância do contentor irá ver o mesmo volume que mais antigos. Portanto, é eficiente e de elevada disponibilidade.

Neste exemplo, o aplicativo de contador tem um serviço ASP.NET Core com uma página da web que mostra o valor do contador num browser.

O `counterService` periodicamente lê um valor de contador de um arquivo, incrementa-lo e a escrita de volta para o ficheiro. O ficheiro é armazenado numa pasta que está montada no volume apoiado por disco fiáveis do Service Fabric.

## <a name="prerequisites"></a>Pré-requisitos

Pode utilizar o Azure Cloud Shell ou uma instalação local da CLI do Azure para concluir esta tarefa. Para utilizar a CLI do Azure com este artigo, certifique-se de que `az --version` retorna, pelo menos, `azure-cli (2.0.43)`.  Instalar (ou atualizar) o módulo de extensão de CLI de malha do Azure Service Fabric através destas [instruções](service-fabric-mesh-howto-setup-cli.md).

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Azure e defina a sua subscrição.

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos no qual quer implementar a aplicação. O comando seguinte cria um grupo de recursos com o nome `myResourceGroup` numa localização nos EUA Leste. Se alterar o nome do grupo de recursos no comando abaixo, lembre-se de alterá-la em todos os comandos que se seguem.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="deploy-the-template"></a>Implementar o modelo

O seguinte comando implementa uma aplicação do Linux utilizar o [counter.sfreliablevolume.linux.json modelo](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/counter/counter.sfreliablevolume.linux.json). Para implementar uma aplicação do Windows, utilize o [counter.sfreliablevolume.windows.json modelo](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/counter/counter.sfreliablevolume.windows.json). Lembre-se de que a maior imagens de contentor podem demorar mais tempo a implementar.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://raw.githubusercontent.com/Azure-Samples/service-fabric-mesh/master/templates/counter/counter.sfreliablevolume.linux.json
```

Também pode ver o estado da implementação com o comando

```azurecli-interactive
az group deployment show --name counter.sfreliablevolume.linux --resource-group myResourceGroup
```

Tenha em atenção o nome do recurso de gateway que tem o recurso de tipo como `Microsoft.ServiceFabricMesh/gateways`. Isso será usado na obtenção de endereço IP público da aplicação.

## <a name="open-the-application"></a>Abrir a aplicação

Assim que a aplicação é implementada com êxito, obtenha o ipAddress do recurso de gateway para a aplicação. Utilize o nome do gateway que é reparado no acima secção.
```azurecli-interactive
az mesh gateway show --resource-group myResourceGroup --gateway-name counterGateway
```

A saída deve ter uma propriedade `ipAddress` que é o endereço IP público para o ponto final de serviço. Abri-lo a partir de um browser. Esta será apresentada uma página da web com o valor do contador que está a ser atualizado a cada segundo.

## <a name="verify-that-the-application-is-able-to-use-the-volume"></a>Certifique-se de que o aplicativo é capaz de usar o volume

O aplicativo cria um ficheiro denominado `counter.txt` no volume no interior `counter/counterService` pasta. O conteúdo deste ficheiro é o valor de contador que está a ser apresentado na página da web.

## <a name="delete-the-resources"></a>Eliminar os recursos

Com frequência, elimine os recursos que já não estiver a utilizar no Azure. Para eliminar os recursos relacionados a este exemplo, elimine o grupo de recursos no qual eles foram implantados (o que elimina tudo associado o grupo de recursos) com o seguinte comando:

```azurecli-interactive
az group delete --resource-group myResourceGroup
```

## <a name="next-steps"></a>Passos Seguintes

- Ver a aplicação de exemplo do disco de Volume fiáveis do Service Fabric num [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter).
- Para saber mais sobre o Modelo de Recursos do Service Fabric, consulte [Modelo de Recursos do Service Fabric Mesh](service-fabric-mesh-service-fabric-resources.md).
- Para saber mais sobre o Service Fabric Mesh, consulte [Descrição geral do Service Fabric Mesh](service-fabric-mesh-overview.md).
