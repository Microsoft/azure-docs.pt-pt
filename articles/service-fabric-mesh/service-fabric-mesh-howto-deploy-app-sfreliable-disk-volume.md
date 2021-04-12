---
title: Volume de disco fiável do tecido de serviço com malha de tecido de serviço
description: Aprenda a armazenar o estado numa aplicação de malha de tecido de serviço Azure, montando o volume baseado em disco fiável de tecido de serviço dentro do recipiente utilizando o Azure CLI.
author: ashishnegi
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: asnegi
ms.custom: mvc, devcenter, devx-track-azurecli
ms.openlocfilehash: ac65693f2513338695e07cd8a19acb13333e7281
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99625791"
---
# <a name="mount-highly-available-service-fabric-reliable-disk-based-volume-in-a-service-fabric-mesh-application"></a>Monte volume de disco fiável de tecido de serviço altamente disponível numa aplicação de malha de tecido de serviço 

> [!IMPORTANT]
> A pré-estreia da Malha de Tecido de Serviço Azure foi reformada. As novas implementações deixarão de ser permitidas através da API de malha de malha de tecido de serviço. O apoio às implementações existentes continuará até 28 de abril de 2021.
> 
> Para mais detalhes, consulte [a pré-reforma da malha de malha de malha de tecido de serviço Azure.](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)

O método comum de persistência do estado com aplicações de contentores é usar armazenamento remoto como O Azure File Storage ou base de dados como Azure Cosmos DB. Isto incorre em leitura significativa e escrever latência de rede para a loja remota.

Este artigo mostra como armazenar o estado em disco fiável de tecido de serviço altamente disponível, montando um volume dentro do recipiente de uma aplicação de malha de tecido de serviço.
O Disco Fiável do Tecido de Serviço fornece volumes para leituras locais com escritas replicadas dentro do Cluster de Tecido de Serviço para uma elevada disponibilidade. Isto remove pedidos de leitura de rede e reduz a latência da rede para escritas. Se o recipiente reiniciar ou se deslocar para outro nó, a nova instância do recipiente verá o mesmo volume que o mais antigo. Assim, é eficiente e altamente disponível.

Neste exemplo, a aplicação Counter tem um serviço Core ASP.NET com uma página web que mostra valor de contador num navegador.

O `counterService` periodicamente lê um valor de contra-valor de um ficheiro, incrementa-o e escreve-o de volta para o ficheiro. O ficheiro é armazenado numa pasta que é montada no volume apoiado pelo Disco Fiável do Tecido de Serviço.

## <a name="prerequisites"></a>Pré-requisitos

Pode utilizar o Azure Cloud Shell ou uma instalação local do CLI Azure para completar esta tarefa. Para utilizar o CLI Azure com este artigo, certifique-se de que `az --version` devolve pelo menos `azure-cli (2.0.43)` .  Instale (ou atualize) o módulo de extensão CLI da malha de malha de tecido de serviço Azure seguindo estas [instruções](service-fabric-mesh-howto-setup-cli.md).

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Azure e defina a sua subscrição.

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos no qual quer implementar a aplicação. O seguinte comando cria um grupo de recursos nomeado `myResourceGroup` numa localização no leste dos Estados Unidos. Se alterar o nome do grupo de recursos no comando abaixo, lembre-se de alterá-lo em todos os comandos que se seguem.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="deploy-the-template"></a>Implementar o modelo

>[!NOTE]
> A partir de 2 de novembro de 2020, [os limites de taxa de descarregamento aplicam-se](https://docs.docker.com/docker-hub/download-rate-limit/) a pedidos anónimos e autenticados para Docker Hub a partir de contas do plano Docker Free e são aplicados por endereço IP. 
> 
> Este modelo faz uso de imagens públicas de Docker Hub. Por favor, note que pode ser limitado. Para mais detalhes, consulte [Authenticate with Docker Hub](../container-registry/buffer-gate-public-content.md#authenticate-with-docker-hub).

O seguinte comando implementa uma aplicação Linux utilizando o [counter.sfreliablevolume.linux.jsno modelo](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/counter/counter.sfreliablevolume.linux.json). Para implementar uma aplicação Do Windows, utilize o [counter.sfreliablevolume.windows.jsno modelo](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/counter/counter.sfreliablevolume.windows.json). Tenha em atenção que imagens de contentores maiores podem demorar mais tempo a ser implantadas.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://raw.githubusercontent.com/Azure-Samples/service-fabric-mesh/master/templates/counter/counter.sfreliablevolume.linux.json
```

Também pode ver o estado do destacamento com o comando

```azurecli-interactive
az deployment group show --name counter.sfreliablevolume.linux --resource-group myResourceGroup
```

Note o nome do recurso gateway que tem o tipo de recurso como `Microsoft.ServiceFabricMesh/gateways` . Isto será usado para obter o endereço IP público da aplicação.

## <a name="open-the-application"></a>Abrir a aplicação

Assim que a aplicação for implementada com sucesso, obtenha o ipAddress do recurso gateway para a aplicação. Use o nome de porta de entrada que notou na secção acima.
```azurecli-interactive
az mesh gateway show --resource-group myResourceGroup --name counterGateway
```

A saída deve ter um `ipAddress` imóvel que é o endereço IP público para o ponto final de serviço. Abra-o a partir de um navegador. Apresentará uma página web com o valor do contador a ser atualizado a cada segundo.

## <a name="verify-that-the-application-is-able-to-use-the-volume"></a>Verifique se a aplicação é capaz de utilizar o volume

A aplicação cria um ficheiro nomeado `counter.txt` no volume interior da `counter/counterService` pasta. O conteúdo deste ficheiro é o valor de contador que está a ser apresentado na página web.

## <a name="delete-the-resources"></a>Eliminar os recursos

Elimine frequentemente os recursos que já não utiliza no Azure. Para eliminar os recursos relacionados com este exemplo, elimine o grupo de recursos em que foram implantados (que elimina tudo o que está associado ao grupo de recursos) com o seguinte comando:

```azurecli-interactive
az group delete --resource-group myResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

- Consulte a aplicação de amostra de volume fiável do tecido de serviço no [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter).
- Para saber mais sobre o Modelo de Recursos do Service Fabric, consulte [Modelo de Recursos do Service Fabric Mesh](service-fabric-mesh-service-fabric-resources.md).
- Para saber mais sobre o Service Fabric Mesh, consulte [Descrição geral do Service Fabric Mesh](service-fabric-mesh-overview.md).