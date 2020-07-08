---
title: (PRECADO) Quickstart - Azure Docker CLUSTER CE para Linux
description: Aprenda rapidamente a criar um cluster Docker CE para contentores do Linux no Azure Container Service com a CLI do Azure.
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: iainfou
ms.custom: ''
ms.openlocfilehash: d4bbd5560681aa73709019e87c6c22470a64ad78
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "79481743"
---
# <a name="deprecated-deploy-docker-ce-cluster"></a>(PRECADO) Implementar o cluster CE do Docker

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Neste arranque rápido, um cluster CE Docker é implantado usando o Azure CLI. Depois, é implementada e executada no cluster uma aplicação de vários contentores que consiste num front-end da Web e numa instância de Redis. Depois de concluída, a aplicação está acessível através da Internet.

O Docker CE no Azure Container Service encontra-se em pré-visualização e **não deve ser utilizado em cargas de trabalho de produção**.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Se optar por instalar e usar a CLI localmente, este tópico requer a execução da versão 2.0.4 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#az-group-create). Um grupo de recursos do Azure é um grupo lógico, no qual os recursos do Azure são implementados e geridos.

O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *westus2.*

```azurecli-interactive
az group create --name myResourceGroup --location westus2
```

Saída:

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup",
  "location": "westus2",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-docker-swarm-cluster"></a>Criar um cluster do Docker Swarm

Crie um cluster Docker CE no Azure Container Service com o comando [az acs create](/cli/azure/acs#az-acs-create). Para obter informações sobre a disponibilidade da região de Docker CE, consulte [as regiões ACS para Docker CE](https://github.com/Azure/ACS/blob/master/announcements/2017-08-04_additional_regions.md)

O exemplo seguinte cria um cluster com o nome *mySwarmCluster* com um nó principal do Linux e três nós de agente do Linux.

```azurecli-interactive
az acs create --name mySwarmCluster --orchestrator-type dockerce --resource-group myResourceGroup --generate-ssh-keys
```

Em alguns casos, como numa versão de avaliação limitada, uma subscrição do Azure tem acesso limitado aos recursos do Azure. Caso a implementação falhe devido a um número limitado de núcleos disponíveis, reduza a contagem de agentes pré-definida ao adicionar `--agent-count 1` ao comando [az acs create](/cli/azure/acs#az-acs-create). 

Ao fim de vários minutos, o comando é concluído e devolve informações sobre o cluster no formato JSON.

## <a name="connect-to-the-cluster"></a>Ligar ao cluster

Durante todo este quickstart, você precisa do FQDN do mestre Docker Swarm e da piscina de agente Docker. Execute o seguinte comando para devolver os FQDNs principal e de agente.

```azurecli
az acs list --resource-group myResourceGroup --query '[*].{Master:masterProfile.fqdn,Agent:agentPoolProfiles[0].fqdn}' -o table
```

Saída:

```output
Master                                                               Agent
-------------------------------------------------------------------  --------------------------------------------------------------------
myswarmcluster-myresourcegroup-d5b9d4mgmt.ukwest.cloudapp.azure.com  myswarmcluster-myresourcegroup-d5b9d4agent.ukwest.cloudapp.azure.com
```

Crie um túnel SSH para o principal do Swarm. Substitua `MasterFQDN` pelo endereço FQDN do principal do Swarm.

```bash
ssh -p 2200 -fNL localhost:2374:/var/run/docker.sock azureuser@MasterFQDN
```

Defina a variável de ambiente `DOCKER_HOST`. Isto permite-lhe executar comandos do Docker relativamente ao Docker Swarm sem ter de especificar o nome do anfitrião.

```bash
export DOCKER_HOST=localhost:2374
```

Está pronto para executar os serviços do Docker no Docker Swarm.


## <a name="run-the-application"></a>Executar a aplicação

Crie um ficheiro com o nome `azure-vote.yaml` e copie o seguinte conteúdo para o mesmo.


```yaml
version: '3'
  azure-vote-back:
    image: redis
    ports:
        - "6379:6379"

  azure-vote-front:
    image: microsoft/azure-vote-front:v1
    environment:
      REDIS: azure-vote-back
    ports:
        - "80:80"
```

Execute o comando [docker stack deploy](https://docs.docker.com/engine/reference/commandline/stack_deploy/) para criar o serviço Azure Vote.

```bash
docker stack deploy azure-vote --compose-file azure-vote.yaml
```

Saída:

```output
Creating network azure-vote_default
Creating service azure-vote_azure-vote-back
Creating service azure-vote_azure-vote-front
```

Utilize o comando [docker stack ps](https://docs.docker.com/engine/reference/commandline/stack_ps/) para devolver o estado de implementação da aplicação.

```bash
docker stack ps azure-vote
```

Quando o `CURRENT STATE` de cada serviço for `Running`, a aplicação está pronta.

```output
ID                  NAME                            IMAGE                                 NODE                               DESIRED STATE       CURRENT STATE                ERROR               PORTS
tnklkv3ogu3i        azure-vote_azure-vote-front.1   microsoft/azure-vote-front:v1   swarmm-agentpool0-66066781000004   Running             Running 5 seconds ago                            
lg99i4hy68r9        azure-vote_azure-vote-back.1    redis:latest                          swarmm-agentpool0-66066781000002   Running             Running about a minute ago
```

## <a name="test-the-application"></a>Testar a aplicação

Navegue para o FQDN do conjunto de agentes do Swarm para testar a aplicação do Azure Vote.

![Imagem de navegação para o Azure Vote](media/container-service-docker-swarm-mode-walkthrough/azure-vote.png)

## <a name="delete-cluster"></a>Eliminar o cluster
Quando o cluster já não for necessário, pode utilizar o comando [az group delete](/cli/azure/group#az-group-delete) para remover o grupo de recursos, o serviço de contentores e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="get-the-code"></a>Obter o código

Neste arranque rápido, foram utilizadas imagens de contentores pré-criadas para criar um serviço Docker. O código da aplicação relacionado, o Dockerfile, e o ficheiro Compor estão disponíveis no GitHub.

[https://github.com/Azure-Samples/azure-voting-app-redis](https://github.com/Azure-Samples/azure-voting-app-redis.git)

## <a name="next-steps"></a>Próximos passos

Neste arranque rápido, lançou um aglomerado Docker Swarm e lançou uma aplicação multi-contentor.

Para aprender sobre a integração do enxame de Docker com Azure DevOps, continue para o CI/CD com Docker Swarm e Azure DevOps.

> [!div class="nextstepaction"]
> [CI/CD com Docker Swarm e DevOps do Azure](./container-service-docker-swarm-setup-ci-cd.md)
