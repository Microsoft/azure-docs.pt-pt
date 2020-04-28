---
title: Volume fiável de disco de tecido de serviço com malha de tecido de serviço
description: Aprenda a armazenar o estado numa aplicação de malha de tecido de serviço Azure, montando volume baseado em disco fidedigno de tecido de serviço dentro do recipiente utilizando o Azure CLI.
author: ashishnegi
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: asnegi
ms.custom: mvc, devcenter
ms.openlocfilehash: f26fe70afe7d9e2872f06ac6da7143556278b1b0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75497963"
---
# <a name="mount-highly-available-service-fabric-reliable-disk-based-volume-in-a-service-fabric-mesh-application"></a>Monte volume fiável de disco de tecido de serviço altamente disponível numa aplicação de malha de tecido de serviço 
O método comum de persistir com aplicações de contentores é usar armazenamento remoto como o Armazenamento de Ficheiros Azure ou base de dados como o Azure Cosmos DB. Isto incorre em significativa latência de leitura e escrita da rede para a loja remota.

Este artigo mostra como armazenar o estado em disco fiável de tecido de serviço altamente disponível, montando um volume dentro do recipiente de uma aplicação de malha de tecido de serviço.
O Disco Fiável de Tecido de Serviço fornece volumes para leituras locais com escritos replicados dentro do Cluster de Tecidode Serviço para alta disponibilidade. Isto remove as chamadas de rede para leituras e reduz a latência da rede para os escritos. Se o recipiente reiniciar ou se deslocar para outro nó, a nova instância do recipiente verá o mesmo volume que o mais antigo. Assim, é eficiente e altamente disponível.

Neste exemplo, a aplicação Counter tem um serviço core ASP.NET com uma página web que mostra contra-valor num navegador.

Lê `counterService` periodicamente um contravalor a partir de um ficheiro, aumenta-o e escreve-o de volta para o ficheiro. O ficheiro é armazenado numa pasta que é montada no volume apoiado pelo Disco Fiável de Tecido de Serviço.

## <a name="prerequisites"></a>Pré-requisitos

Pode utilizar a Casca de Nuvem Azure ou uma instalação local do Azure CLI para completar esta tarefa. Para utilizar o Azure CLI com `az --version` este `azure-cli (2.0.43)`artigo, certifique-se de que devolve pelo menos .  Instale (ou atualize) o módulo de extensão CLI de malha de tecido de tecido de serviço Azure seguindo estas [instruções](service-fabric-mesh-howto-setup-cli.md).

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Azure e defina a sua subscrição.

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos no qual quer implementar a aplicação. O seguinte comando cria `myResourceGroup` um grupo de recursos nomeado num local no leste dos Estados Unidos. Se alterar o nome do grupo de recursos no comando abaixo, lembre-se de alterá-lo em todos os comandos que se seguem.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="deploy-the-template"></a>Implementar o modelo

O comando seguinte implementa uma aplicação Linux utilizando o [modelo counter.sfreliablevolume.linux.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/counter/counter.sfreliablevolume.linux.json). Para implementar uma aplicação Windows, utilize o [modelo counter.sfreliablevolume.windows.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/counter/counter.sfreliablevolume.windows.json). Esteja ciente de que imagens de recipientes maiores podem demorar mais tempo a ser implantadas.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://raw.githubusercontent.com/Azure-Samples/service-fabric-mesh/master/templates/counter/counter.sfreliablevolume.linux.json
```

Você também pode ver o estado do destacamento com o comando

```azurecli-interactive
az group deployment show --name counter.sfreliablevolume.linux --resource-group myResourceGroup
```

Note o nome do recurso `Microsoft.ServiceFabricMesh/gateways`gateway que tem um tipo de recurso como . Isto será usado para obter endereço IP público da aplicação.

## <a name="open-the-application"></a>Abrir a aplicação

Assim que a aplicação for implementada com sucesso, obtenha o ipAddress do recurso gateway para a aplicação. Use o nome de porta de entrada que notou na secção acima.
```azurecli-interactive
az mesh gateway show --resource-group myResourceGroup --name counterGateway
```

A saída deve `ipAddress` ter um imóvel que seja o endereço IP público para o ponto final do serviço. Abra-o a partir de um navegador. Apresentará uma página web com o valor de contra-valor atualizado a cada segundo.

## <a name="verify-that-the-application-is-able-to-use-the-volume"></a>Verifique se a aplicação é capaz de utilizar o volume

A aplicação cria `counter.txt` um ficheiro `counter/counterService` nomeado no volume dentro da pasta. O conteúdo deste ficheiro é o contravalor que está a ser apresentado na página web.

## <a name="delete-the-resources"></a>Eliminar os recursos

Elimine frequentemente os recursos que já não utiliza em Azure. Para eliminar os recursos relacionados com este exemplo, elimine o grupo de recursos em que foram implantados (que elimina tudo o que está associado ao grupo de recursos) com o seguinte comando:

```azurecli-interactive
az group delete --resource-group myResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

- Ver a aplicação de amostra de disco de volume fiável do tecido de serviço no [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter).
- Para saber mais sobre o Modelo de Recursos do Service Fabric, consulte [Modelo de Recursos do Service Fabric Mesh](service-fabric-mesh-service-fabric-resources.md).
- Para saber mais sobre o Service Fabric Mesh, consulte [Descrição geral do Service Fabric Mesh](service-fabric-mesh-overview.md).
