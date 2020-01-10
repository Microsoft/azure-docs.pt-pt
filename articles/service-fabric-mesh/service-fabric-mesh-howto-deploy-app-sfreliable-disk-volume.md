---
title: Service Fabric volume de disco confiável com malha de Service Fabric
description: Saiba como armazenar o estado em um aplicativo de malha de Service Fabric do Azure montando Service Fabric volume baseado em disco confiável dentro do contêiner usando o CLI do Azure.
author: ashishnegi
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: asnegi
ms.custom: mvc, devcenter
ms.openlocfilehash: f26fe70afe7d9e2872f06ac6da7143556278b1b0
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/26/2019
ms.locfileid: "75497963"
---
# <a name="mount-highly-available-service-fabric-reliable-disk-based-volume-in-a-service-fabric-mesh-application"></a>Montagem altamente disponível Service Fabric volume baseado em disco confiável em um aplicativo de malha Service Fabric 
O método comum de persistência de estado com aplicativos de contêiner é usar o armazenamento remoto como armazenamento de arquivos do Azure ou banco de dados como Azure Cosmos DB. Isso incorre em uma latência de rede de leitura e gravação significativa para o armazenamento remoto.

Este artigo mostra como armazenar o estado em alta disponibilidade Service Fabric disco confiável montando um volume dentro do contêiner de um aplicativo de malha Service Fabric.
Service Fabric disco confiável fornece volumes para leituras locais com gravações replicadas no Cluster Service Fabric para alta disponibilidade. Isso remove as chamadas de rede para leituras e reduz a latência de rede para gravações. Se o contêiner for reiniciado ou for movido para outro nó, a nova instância de contêiner verá o mesmo volume que o mais antigo. Portanto, ele é eficiente e altamente disponível.

Neste exemplo, o aplicativo do contador tem um serviço de ASP.NET Core com uma página da Web que mostra o valor do contador em um navegador.

O `counterService` lê periodicamente um valor de contador de um arquivo, incrementa-o e grava-o de volta no arquivo. O arquivo é armazenado em uma pasta que é montada no volume com suporte de Service Fabric disco confiável.

## <a name="prerequisites"></a>Pré-requisitos

Você pode usar o Azure Cloud Shell ou uma instalação local do CLI do Azure para concluir esta tarefa. Para usar o CLI do Azure com este artigo, verifique se `az --version` retorna pelo menos `azure-cli (2.0.43)`.  Instale (ou atualize) o módulo de extensão da CLI da malha de Service Fabric do Azure seguindo estas [instruções](service-fabric-mesh-howto-setup-cli.md).

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Azure e defina a sua subscrição.

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos no qual quer implementar a aplicação. O comando a seguir cria um grupo de recursos chamado `myResourceGroup` em um local no Estados Unidos Oriental. Se você alterar o nome do grupo de recursos no comando abaixo, lembre-se de alterá-lo em todos os comandos a seguir.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="deploy-the-template"></a>Implementar o modelo

O comando a seguir implanta um aplicativo do Linux usando o [modelo Counter. sfreliablevolume. Linux. JSON](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/counter/counter.sfreliablevolume.linux.json). Para implantar um aplicativo do Windows, use o [modelo Counter. sfreliablevolume. Windows. JSON](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/counter/counter.sfreliablevolume.windows.json). Lembre-se de que imagens de contêiner maiores podem levar mais tempo para serem implantadas.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://raw.githubusercontent.com/Azure-Samples/service-fabric-mesh/master/templates/counter/counter.sfreliablevolume.linux.json
```

Você também pode ver o estado da implantação com o comando

```azurecli-interactive
az group deployment show --name counter.sfreliablevolume.linux --resource-group myResourceGroup
```

Observe o nome do recurso de gateway que tem o tipo de recurso como `Microsoft.ServiceFabricMesh/gateways`. Isso será usado para obter o endereço IP público do aplicativo.

## <a name="open-the-application"></a>Abrir a aplicação

Depois que o aplicativo for implantado com êxito, obtenha o ipAddress do recurso de gateway para o aplicativo. Use o nome do gateway que você observou na seção acima.
```azurecli-interactive
az mesh gateway show --resource-group myResourceGroup --name counterGateway
```

A saída deve ter uma propriedade `ipAddress` que é o endereço IP público para o ponto de extremidade de serviço. Abra-o em um navegador. Ele exibirá uma página da Web com o valor do contador sendo atualizado a cada segundo.

## <a name="verify-that-the-application-is-able-to-use-the-volume"></a>Verificar se o aplicativo é capaz de usar o volume

O aplicativo cria um arquivo chamado `counter.txt` no volume dentro da pasta `counter/counterService`. O conteúdo desse arquivo é o valor do contador que está sendo exibido na página da Web.

## <a name="delete-the-resources"></a>Eliminar os recursos

Exclua com frequência os recursos que você não está mais usando no Azure. Para excluir os recursos relacionados a este exemplo, exclua o grupo de recursos no qual eles foram implantados (o que exclui tudo associado ao grupo de recursos) com o seguinte comando:

```azurecli-interactive
az group delete --resource-group myResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

- Exiba o Service Fabric aplicativo de exemplo de disco de volume confiável no [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter).
- Para saber mais sobre o Modelo de Recursos do Service Fabric, consulte [Modelo de Recursos do Service Fabric Mesh](service-fabric-mesh-service-fabric-resources.md).
- Para saber mais sobre o Service Fabric Mesh, consulte [Descrição geral do Service Fabric Mesh](service-fabric-mesh-overview.md).
